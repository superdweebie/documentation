Writing your own Extension
==========================

Doctrine Extensions provides you with a framework to easily write and register
your own extensions.

This tutorial will work through writing a simple extension that will create a
filter so that only Jellybeans of a particular colour will be returned.

A minimal extension must have the following two files:

* ExtensionConfig
* Extension

To be useful, any extension will also have some other files that actually do the
work. In this case, there will be two additional files:

* A Jellybean document
* A colour filter

Let's imagine that these four files are all in the ``MyExtension`` namespace.

The Jellybean document is a simple doctrine document::

    <?php
    namespace MyExtension;

    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;

    /**
     * @ODM\Document
     */
    class Jellybean
    {
        /**
         * @ODM\Id(strategy="UUID")
         */
        protected $id;

        /**
         * @ODM\String
         */
        protected $colour;

        public function getId() {
            return $this->id;
        }

        public function getColour() {
            return $this->colour;
        }

        public function setColour($colour) {
            $this->colour = $colour;
        }
    }

Then, write a filter that do the filtering::

    <?php
    namespace MyExtension;

    use Doctrine\ODM\MongoDB\Mapping\ClassMetadata;
    use Doctrine\ODM\MongoDB\Query\Filter\BsonFilter;

    class ColourFilter extends BsonFilter
    {
        public function setColour($colour){
            $this->parameters['colour'] = $colour;
        }

        public function addFilterCriteria(ClassMetadata $targetDocument)
        {
            if ($targetDocument->rootDocumentName == 'MyExtension\Jellybean'){
                return ['colour' => $this->parameters['colour']];
            }
            return [];
        }
    }

Now we have a document and a filter, we need to package them into an extension.

ExtensionConfig
^^^^^^^^^^^^^^^

``ExtensionConfig`` must extend ``Sds\DoctrineExtensions\AbstractExtensionConfig``. This class
defines any configuration options for the extension. You may define your own
configuration options, as well as the ones already defined:

* identity
* annotationReader
* dependencies

Our example extension doesn't have any extra config options, so ``ExtensionConfig`` would look like this::

    <?php
    namespace MyExtension;

    use Sds\DoctrineExtensions\AbstractExtensionConfig;

    class ExtensionConfig extends AbstractExtensionConfig
    {
    }

Extension
^^^^^^^^^

``Extension`` must extend ``Sds\DoctrineExtensions\AbstractExtension``. This class is
responsible for consuming ``ExtensionConfig`` to create any required resources, and
make those resources available. In particular, the :doc:`manifest` will call these methods
on ``Extension``::

    public function getConfig()
    public function getFilters()
    public function getSubscribers()
    public function getDocuments()
    public function getCliCommands()
    public function getCliHelpers()

Our example ``Extension`` needs to report the existance of the Jellybean document,
the colour filter, so ``Extension`` would look like this::

    <?php
    namespace MyExtension;

    use Sds\DoctrineExtensions\AbstractExtension;

    class Extension extends AbstractExtension {

        public function __construct($config){

            $this->configClass = __NAMESPACE__ . '\ExtensionConfig';
            parent::__construct($config);
            $config = $this->getConfig();

            $reflection = new \ReflectionClass('MyExtension\Jellybean');
            $this->documents = array($reflection->getNamespaceName() => dirname($reflection->getFileName()));

            $this->filters = array('colour' => 'MyExtension\ColourFilter');
        }
    }

Using MyExtension
^^^^^^^^^^^^^^^^^

``MyExtension`` can be passed to the ``ManifestConfig`` with::

    new ManifestConfig(['extensionConfigs' => ['MyExtension' => null]]);

The colour filter can be activated with::

        $documentManager->getFilterCollection()->getFilter('colour')->setColour('black');
        $documentManager->getFilterCollection()->enable('colour');

With the filter enabled, whenever ``Jellybean`` documents are returned, any black ones
will be absent from the result set.