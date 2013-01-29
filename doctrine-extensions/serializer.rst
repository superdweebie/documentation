Serializer
==========

Can serialize and unserialize documents to php arrays and json strings.

Config Options
^^^^^^^^^^^^^^

* **annotationReader** Used to read annotations
* **classNameProperty** Property name used to store the class name, if the class name is serialized. Defaults to ``_className``
* **typeSerializers** An array of custom serialziers for MongoDB datatypes.
* **maxNestingDepth** Integer. Maximum depth to serialize reference if an eager style reference serializer is used.

Basic Use
^^^^^^^^^

The serializer has five methods:

**toArray**
Will serialize a document to a php array::

    $array = Serializer::toArray($document, $documentManager)

**toJson**
Will serialize a document to a json string::

    $json = Serializer::toJson($document, $documentManager)

**applySerializeMetadataToArray**
Will apply serialization rules to a document represented as an array. This is useful when a document is just
being pulled from the database and then imediately serialized. Doctrine can be told to skip the
normal hydration of documents, and save some processing time.

    $docArray = $documentManager
        ->createQueryBuilder()
        ->find('MyDocument')
        ->field('id')->equals($id)
        ->hydrate(false)
        ->getQuery()
        ->getSingleResult();

    $array = Serializer::applySerializeMetadataToArray($docArray, 'MyDocument', $documentManager)

**fromArray**
Will unserialize an array into a document. To do this the serializer needs to know which
document class to create. This can be done in two ways.

Unserialization can be done by explicitly passing the document class to create as the fourth argument. Eg::

    $myDocumentInstance = Serializer::fromArray($array, $documentManager, null, 'MyDocument');

Unserialization can be done passing the document class as a property of the array to be unserialized. The
default property is ``_className``. Eg::

    $array = [
        '_className' => 'MyDocument',
        'property1' => 1,
        'property2' => 2
    ];

    $myDocumentInstance = Serializer::fromArray($array, $documentManager);

If you want to use a non-standard class name property, you can pass it to the unserializer as the third argument. Eg::

    $array = [
        'documentType' => 'MyDocument',
        'property1' => 1,
        'property2' => 2
    ];

    $myDocumentInstance = Serializer::fromArray($array, $documentManager, 'documentName');

You can set a non-standard class name property through the extension config, which then allows the implicit use of
a non-standard class name property. Eg, you could use this ``ExtensionConfig`` array::

   ['Sds\DoctrineExtensions\Serializer' => ['classNameProperty' => 'documentType']]

Then you could unserialize like this (without the third argument)::

    $array = [
        'documentType' => 'MyDocument',
        'property1' => 1,
        'property2' => 2
    ];

    $myDocumentInstance = Serializer::fromArray($array, $documentManager);

The unserializer will check the supplied array for the id property defined in the document's
metadata.

If no id is found, then a new document is created, but not persisted, and returned.

If an id is found, the underializer will attempt to load that id from the database and apply the data in
the supplied array to the managed document. The altered managed document will be returned, but not flushed.

If an id is found in the array, but not in the database, then a new document is created, but not persisted, and returned.

**fromJson**
Will unserialize a json string into a document. Has all the same options as ``fromArray``.

Fine Grained Control
^^^^^^^^^^^^^^^^^^^^

The behaviour of the serializer can be altered with the use of annotations.

To skip a property when serializing and unserializing, add the following annotation::

    @Sds\Serializer(@Sds\Ignore)

This is shorthand for::

    @Sds\Serializer(@Sds\Ignore('ignore_always'))

To skip a property only wne serializing or unserializing, use the following annotations::

    @Sds\Serializer(@Sds\Ignore('ignore_when_serializing'))
    @Sds\Serializer(@Sds\Ignore('ignore_when_unserializing'))

To restore serializer behaviour in inherited documents, the following annotation is also provided::

    @Sds\Serializer(@Sds\Ignore('ignore_never'))

To include the class name in a serialized array, use the following as a class annotation::

    @Sds\Serializer(@Sds\ClassName)

For documents that have a discriminator property, to include the discriminator in a serialized array, use the following as a class annotation::

    @Sds\Serializer(@Sds\Discriminator)

Reference Serializers
^^^^^^^^^^^^^^^^^^^^^

Document references can be serialized in several differnet ways.

By default references will be serialized to an array like this::

    [$ref: 'CollectionName/DocumentId']

The $ref style of referencing is what Mongo uses internally. The format of the reference is given with the expectation it could be used as a URL to a REST API.

The default behaviour uses the RefLazy serializer. However this can be overridden by defineing an alternative ``ReferenceSerializer`` as a property annotation::

    /**
     * @ODM\ReferenceMany(targetDocument="MyTargetDocument")
     * @Sds\Serializer(@Sds\ReferenceSerializer('MyAlternativeSerializer'))
     */
    protected $myDocumentProperty;

Two alternate ReferenceSerializers are already included with Doctrine Extensions:

* **SimpleLazy** will serialize a reference as the mongo id. It can be used like this::

    /**
     * @ODM\ReferenceMany(targetDocument="MyTargetDocument")
     * @Sds\Serializer(@Sds\ReferenceSerializer('Sds\DoctrineExtensions\Serializer\Reference\Eager'))
     */
    protected $myDocumentProperty;

* **Eager** will serialize references as if they were embedded documents. It can be used like this::

    /**
     * @ODM\ReferenceMany(targetDocument="MyTargetDocument")
     * @Sds\Serializer(@Sds\ReferenceSerializer('Sds\DoctrineExtensions\Serializer\Reference\Eager'))
     */
    protected $myDocumentProperty;

When using the Eager serializer, the `maxNestingDepth` configuration option will control how deep the Eager serializer
will go into a tree of references.

Provided seralizers can be involked with the shorthand annotations::

    @Sds\Serializer(@Sds\RefLazy))
    @Sds\Serializer(@Sds\SimpleLazy))
    @Sds\Serializer(@Sds\Eager))

Alternate ReferenceSerializers must implement Sds\DoctrineExtensions\Serializer\Reference\ReferenceSerializerInterface

Date Serializer
^^^^^^^^^^^^^^^

Fields of type ``date`` are serialized by default with ``Sds\DoctrineExtensions\Serializer\Type\DateSerializer``.
 
Dates will be serialized into this format::

    TODO add format

Likewise, to unserialize dates correctly, they must provided in this format.

To override this default date serialization, see the Custom Type Serizlizers section below.

Custom Type Serializers
^^^^^^^^^^^^^^^^^^^^^^^

Each document field has an associated type, such as ``string`` or ``date``. Serialization may be customized by type.

First create a class which implements the ``Sds\DoctrineExtensions\Serializer\Type\TypeSerializerInterface``.
You will need to define ``serialize`` and ``unserialize`` methods.

For example, this class will uppercase the first letter of every string when serializing, and
lower case the first letter when unserializing::

    use Sds\DoctrineExtensions\Serializer\Type\TypeSerializerInterface;

    class MyStringSerializer implements TypeSerializerInterface {

        public static function serialize($value) {
            return ucfirst($value);
        }

        public static function unserialize($value) {
            return lcfirst($value);
        }
    }

Then the class needs to be registered in the extension config::

    'Sds\DoctrineExtensions\Serializer' => [
        'typeSerializers' => [
            'string' => 'MyStringSerializer'
        ]
    ]

The default Date serializer is an example of a Type Serializer which is regisered by default.
To over ride it, simply register your own in the extension config::

    'Sds\DoctrineExtensions\Serializer' => [
        'typeSerializers' => [
            'date' => 'MyDateSerializer'
        ]
    ]