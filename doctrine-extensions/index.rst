Doctrine Extensions
===================

.. toctree::
   :hidden:

   installation
   manifest
   zf2integration
   custom
   annotationsReference
   testing
   issues
   accessControl
   accessor
   annotation
   crypt
   dojo
   freeze
   readonly
   rest
   serializer
   softDelete
   stamp
   state
   validator
   workflow
   zone

A collection of php 5.4 traits, Documents, Annotations, Subscribers and Filters
that can be used to extend DoctrineMongoODM.

Each extension is packaged separately. Multiple extensions can be loaded with the Manifest, which will automatically
resolve dependencies between extensions.

Using Doctrine Extensions
-------------------------

* :doc:`installation`
* :doc:`manifest`
* :doc:`zf2integration`
* :doc:`custom`
* :doc:`annotationsReference`
* :doc:`testing`
* :doc:`issues`

Extensions Reference
--------------------

:doc:`accessControl`
^^^^^^^^^^^^^^^^^^^^
Permissions based access to documents. Supports permissions for Create, Read, Update and Delete.

:doc:`accessor`
^^^^^^^^^^^^^^^
Allows the use of non-standard getter and setter methods for document properties.

:doc:`annotation`
^^^^^^^^^^^^^^^^^
Raises annotation events and supports annotation inheritance.

:doc:`crypt`
^^^^^^^^^^^^
Supports encryption and hashing of document properties.

:doc:`dojo`
^^^^^^^^^^^
Generates javascript so doctrine documents can be used with the dojo javascript framework

:doc:`freeze`
^^^^^^^^^^^^^
Makes a document 'freezable'. A frozen document cannot be changed until it is thawed.

:doc:`readonly`
^^^^^^^^^^^^^^^
Makes a field 'read only'. A read only field can be populated once, but never updated or deleted.

:doc:`rest`
^^^^^^^^^^^
Defines a rest endpoint to access the document.

:doc:`serializer`
^^^^^^^^^^^^^^^^^
Can serialize and unserialize documents to php arrays and json strings.

:doc:`softDelete`
^^^^^^^^^^^^^^^^^
Makes a document 'soft deleteable'.

:doc:`stamp`
^^^^^^^^^^^^
Stamps a document indicating who and when it was created or last changed.

:doc:`state`
^^^^^^^^^^^^

:doc:`validator`
^^^^^^^^^^^^^^^^

:doc:`workflow`
^^^^^^^^^^^^^^^

:doc:`zone`
^^^^^^^^^^^