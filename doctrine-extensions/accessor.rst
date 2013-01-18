Accessor
========

Allows the use of non-standard getter and setter methods for document properties.

Config Options
^^^^^^^^^^^^^^

* **annotationReader** Used to read annotations.

Basic Use
^^^^^^^^^

Many extensions require access to document properties. This is normally done with
``getProperty`` and ``setProperty($value)`` methods. However, there may occasionally be good
reason to deviate from this pattern. The Accessor extension has two annotations that
can be used to define a non-standard getter or setter for property.

For example::

    /**
     * @ODM\String
     * @Sds\Getter('myGetter')
     * @Sds\Setter('mySetter')
     */
    protected $property

    public function myGetter(){
        return $this->property;
    }

    public function mySetter($value){
        $this->property = $value;
    }

To retireve the correct getter or setter for a property use the ``Accessor``. Eg::

    use Sds\DoctrineExtensions\Accessor\Accessor;

    $getMethod = Accessor::getGetter($metadata, 'property', $document);
    $value = $document->$getMethod();

and::

    use Sds\DoctrineExtensions\Accessor\Accessor;

    $getSetter = Accessor::getSetter($metadata, 'property', $document);
    $document->$setMethod('value');

**note:** this extension is not normally configured by itself. It is a dependency of several other extensions.

getId
^^^^^

`getId` can be used to return the id of a document no matter what the `id` property is, and
if there is a custom getter defined. Given this document snippet::

    /**
     * @ODM\Id
     * @Sds\Getter('myGetter')
     * @Sds\Setter('mySetter')
     */
    protected $property

    public function myGetter(){
        return $this->property;
    }

    public function mySetter($value){
        $this->property = $value;
    }

The id can be retrieved with::

    $id = Accessor::getId($metadata, $document);
