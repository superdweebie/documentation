Readonly
========

Makes a field 'read only'. A read only field can be populated once, but never updated.

Config Options
^^^^^^^^^^^^^^

* **annotationReader** Used to read annotations

Basic Use
^^^^^^^^^

To make a field readonly annotate it with ``@Sds\Readonly``. For example::

    /**
     * @ODM\Field(type="string")
     * @Sds\Readonly
     */
    protected $myReadonlyField;

If an update to a readonly is attempted, the following two events are triggered during ``$documentManager->flush()``:

* **preReadonlyRollback** occurs before the field value is changed back to it' original value.
* **postReadonlyRollback** occurs after the field value is changed back to it' original value.
