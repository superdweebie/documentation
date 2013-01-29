Rest
====

Defines a rest endpoint to access the document.

Config Options
^^^^^^^^^^^^^^

* **basePath** The base url for all rest endpoints
* **annotationReader** Used to read annotations

Basic Use
^^^^^^^^^

The extension can be configured with a ``basePath`` option. eg::

    [
        'Sds\DoctrineExtensions\Rest' => [
            'basePath' => 'http://myserver.com/'
        ]
    ]

A ``@Sds\Rest`` annotation can be placed on a document to indicate the REST endpoint that 
can be used to access that document. eg::

    /**
     * @ODM\Document
     * @Sds\Rest
     */
    class MyDocument {

        ...
    }

If no options are set in the ``@Sds\Rest`` annotation, the rest endpoint defaults to the basePath defined 
in the extension config combined with the document's collection. In this example, the rest endpoint 
would be::

    http://myserver.com/mydocument

If a value is passed to the ``@Sds\Rest`` annotation, it will used instead of the document's collection. eg::

    @Sds\Rest("doc")

Would give the endpoint::

    http://myserver.com/doc

A basePath can also be passed to the ``@Sds\Rest`` annotation to override the extension config. eg:

    @Sds\Rest("basePath"="http://otherserver.com")

Would give the endpoint::

    http://otherserver.com/mydocument

The rest extension does not actually create a rest service. It only extends document metadata so that 
a document's rest endpoint can be inferred.

Sds code that consumes @Sds/Rest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The metadata producted by the @Sds/Rest annotation is consumed by other Sds code:

* **Sds\DoctrineExtensionsModule** Uses this metadata to create rest services using zend framework 2. See :doc:`/doctrine-extensions-module/rest`
* **Dojo extension** Uses this metadata to auto generate code for `dojo/store/jsonrest` modules that can talk to the rest endpoint. See :doc:`dojo`

These two pieces of code are designed to work together. The dojo extension can generate the client side 
javascript which will talk to a server side rest endpoint created in zend framework 2. 

