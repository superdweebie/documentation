The Manifest
============

Each extension has an ``ExtensionConfig`` class that defines the configuration
options for that extension. Each extension also has an ``Extension`` class that
will return all the resources that the extension requires/provides, including:

* Doctrine event subscribers
* Annotations
* Filters
* Documents
* CliCommands
* CliHelpers
* Other extensions it is dependent on

The ``Manifest`` allows multiple extensions to be configured, and provide a list
of all resources that all extensions require/provide with dependencies resolved.
A ``Manifest`` can be created from a ``ManifestConfig``.

``ManifestConfig`` has the following properties:

* *annotationReader*: used to read annoations.
* *identity*: some extensions consume an instance of ``Sds\Common\Identity\IdentityInterface`` which may be set with this property. See :doc:`/common/identity`.
* *extensionConfigs* an array of arrays that can be used to construct a ``ExtensionConfig`` for each requried extension.

For example::

    use Doctrine\Common\Annotations\AnnotationReader;
    use Sds\DoctrineExtensions\Manifest;
    use Sds\DoctrineExtensions\ManifestConfig;

    function createManifest(){
        return new Manifest(new ManifestConfig([
            'annotationReader' => new AnnotationReader,
            'extensionConfigs' => [
                'Sds\DoctrineExtensions\Readonly' => null,
                'Sds\DoctrineExtensions\Validator' ['validateOnFlush' => false]
            ]
        ]);
    }

Calling ``createManifest`` will return a manifest which configures the ``Readonly`` extension
and the ``Validator`` extension with it's ``validateOnFlush`` config option set to ``false``.

A manifest can then be used to configure doctrine and retrieve a ``documentManager``.
If you are useing ZendFramework2, this configuration is handled by :doc:`/doctrine-extensions-module/index`.
In other cases, a code snippet such as this will set get a ``documentManager`` for you::

    use Doctrine\Common\Annotations\AnnotationRegistry;
    use Doctrine\Common\EventManager;
    use Doctrine\Common\Persistence\Mapping\Driver\MappingDriverChain;
    use Doctrine\MongoDB\Connection;
    use Doctrine\ODM\MongoDB\Configuration;
    use Doctrine\ODM\MongoDB\Mapping\Driver\AnnotationDriver;

    function configDoctrine($manifest){
        $config = new Configuration();

        $config->setProxyDir(__DIR__ . 'MyProxiesDir');
        $config->setProxyNamespace('Proxies');

        $config->setHydratorDir(__DIR__ . 'MyHydratorsDir');
        $config->setHydratorNamespace('Hydrators');

        $config->setDefaultDB('myDefaultDb');

        //create driver chain
        $chain  = new MappingDriverChain;

        foreach ($manifest->getDocuments() as $namespace => $path){
            $driver = new AnnotationDriver($this->annotationReader, $path);
            $chain->addDriver($driver, $namespace);
        }
        $config->setMetadataDriverImpl($chain);

        //register filters
        foreach ($manifest->getFilters() as $name => $class){
            $config->addFilter($name, $class);
        }

        //create event manager
        $eventManager = new EventManager();
        foreach($manifest->getSubscribers() as $subscriber){
            $eventManager->addEventSubscriber($subscriber);
        }

        //register annotations
        AnnotationRegistry::registerLoader(function($className) {
            return class_exists($className);
        });

        $conn = new Connection(null, array(), $config);

        return DocumentManager::create($conn, $config, $eventManager);
    }
