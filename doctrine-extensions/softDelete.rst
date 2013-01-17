Soft Delete
===========

Makes a document 'soft deleteable'. When a soft deleteable document is soft deleted it is not deleted from the database permanently. Rather a ``softDeleted`` flag is set. The document can then be restored at a later time.

Config Options
^^^^^^^^^^^^^^

* **$enableSoftDeleteStamps** Boolean. If true, documents will get a :doc:`stamp` when the document is SoftDeleted or Restored.
* **enableAccessControl** Boolean. If true, :doc:`accessControl` will be enabled for SoftDelete and Restore actions.
* **identityName** The identityName to use if ``enableSoftDeleteStamps`` is ``true`` and an identity object is not provided to the config.
* **annotationReader** Used to read annotations
* **identity** If the identity provided implements ``Sds\Common\Identity\RoleAwareIdentityInterface`` then the Identity's roles will be passed to Access Control to use.
* **roles** If identity is not provided, or does not implement ``Sds\Common\Identity\RoleAwareIdentityInterface`` then ``roles`` may be an array of strings which are the roles passed to Access Control to use.

Basic Use
^^^^^^^^^

To make a document soft deleteable, it must:

* Implement ``Sds\Common\SoftDelete\SoftDeleteableInterface``.
* Have a boolean property annotated with ``@Sds\SoftDelete``. This property will store the flag indicating if the document is frozen or not. The ``Sds\DoctrineExtensions\SoftDelete\DataModel\SoftDeleteableTrait`` can be used to create this property.

The simplest soft deleteable document would look like this::

    use Sds\Common\SoftDelete\SoftDeleteableInterface;
    use Sds\DoctrineExtensions\SoftDelete\DataModel\SoftDeleteableTrait;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /** @ODM\Document */
    class Simple implements SoftDeleteableInterface {

        use SoftDeleteableTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

To soft delete a document, set the property annotated with ``@Sds\SoftDelete`` to true. Alternately, if using the
``SoftDeleteableTrait`` you can just use the ``softDelete`` method. eg::

    $myDoc->softDelete();

During ``$documentManager->flush()`` this will raise two events:

* **preSoftDelete** occurs before the document is soft deleted.
* **postSoftDelete** occurs after the document is soft deleted.

While a document is soft delted, no updates are permitted. If updates are attempted on a soft deleted
document, the **softDeletedUpdateDenied** event will be raised, and the updates will not be applied.

**Note:** a soft deletable document can still be permanently and completely deleted with ``$documentManager->remove($myDoc)``.
If you want to prevent this possibility, then use the :doc:`doNotHardDelete` extension.

To restore a soft deleted document, set the property annotated with ``@Sds\SoftDelete`` to false. Alternately, if using the
``SoftDeleteableTrait`` you can just use the ``restore`` method. eg::

    $myDoc->restore();

During ``$documentManager->flush()`` this will raise two events:

* **preRestore** occurs before the document is restored.
* **postRestore** occurs after the document is restored.

Stamping
^^^^^^^^

Documents can be stamped with an identityName and timestamp each time they are soft deleted or restored.
To enable stamping, set ``enableSoftDeleteStamps`` to true in the extension config and set up documents
to be stamped. A stampable document should implement the interfaces corresponding to the stamps you want. The relevant interfaces are:

* Sds\Common\SoftDelete\SoftDeletedByInterface
* Sds\Common\SoftDelete\SoftDeletedOnInterface
* Sds\Common\SoftDelete\RestoredByInterface
* Sds\Common\SoftDelete\RestoredOnInterface

Each interface can be implemented with a corresponding trait:

* Sds\DoctrineExtensions\SoftDelete\DataModel\SoftDeletedOnTrait
* Sds\DoctrineExtensions\SoftDelete\DataModel\SoftDeletedByTrait
* Sds\DoctrineExtensions\SoftDelete\DataModel\RestoredOnTrait
* Sds\DoctrineExtensions\SoftDelete\DataModel\RestoredByTrait

If you want use all four interfaces traits, it can easily be done with ``Sds\DoctrineExtensions\SoftDelete\DataModel\SoftStampTrait``.

A simple soft deletable document, with all stamps enabled would look like this::

    use Sds\DoctrineExtensions\SoftDelete\DataModel\SoftStampTrait;
    use Sds\Common\SoftDelete\SoftDeletedByInterface;
    use Sds\Common\SoftDelete\SoftDeletedOnInterface;
    use Sds\Common\SoftDelete\RestoredByInterface;
    use Sds\Common\SoftDelete\RestoredOnInterface;
    use Sds\DoctrineExtensions\SoftDelete\DataModel\SoftDeleteableTrait;
    use Sds\Common\SoftDelete\SoftDeleteableInterface;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /** @ODM\Document */
    class Stamped implements
        SoftDeleteableInterface,
        SoftDeletedByInterface,
        SoftDeletedOnInterface,
        RestoredByInterface,
        RestoredOnInterface
    {
        use SoftDeleteableTrait;
        use SoftStampTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

The stamps on this document could be interogated with these methods:

* $myDoc->getSoftDeletedBy()
* $myDoc->getSoftDeletedOn()
* $myDoc->getRestoredBy()
* $myDoc->getRestoredOn()

Access Control
^^^^^^^^^^^^^^

The SoftDelte extension integrates with the :doc:`accessControl` extension. It allows the definition
of two new actions ``softDelete`` and ``restore``. To enable access control, set ``enableAccessControl`` to
true in the extension config, and set up any access controlled documents. An access controlled document should
be implement the ``AccessControlledInterface`` and annotated with ``@Sds\AccessControl``.

For example::

    use Sds\Common\AccessControl\AccessControlledInterface;
    use Sds\Common\SoftDelete\SoftDeleteableInterface;
    use Sds\DoctrineExtensions\AccessControl\DataModel\AccessControlledTrait;
    use Sds\DoctrineExtensions\SoftDelete\DataModel\SoftDeleteableTrait;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /**
     * @ODM\Document
     * @Sds\AccessControl(
     *     @Sds\AccessControl\DefaultValue(false),
     *     @Sds\AccessControl\SoftDelete(true),
     *     @Sds\AccessControl\Restore(true)
     * )
     */
    class AccessControlled implements SoftDeleteableInterface, AccessControlledInterface {

        use SoftDeleteableTrait;
        use AccessControlledTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

This document is access controlled for the ``softDelete`` and ``restore`` actions only. It is not access controlled
for ``create``, ``read``, ``update`` or ``delete``.

Using the Filter
^^^^^^^^^^^^^^^^

The SoftDelete extension includes a SoftDelete filter. This can be used to filter out soft deleted or restored
documents from database result sets.

To return only soft delted documents use::

    $filter = $documentManager->getFilterCollection()->getFilter('softDelete');
    $filter->onlySoftDeleted();
    $documentManager->getFilterCollection()->enable('softDelete');

To return only not soft deleted documents (all restored documents, and those that have never been soft deleted) use::

    $filter = $documentManager->getFilterCollection()->getFilter('softDelete');
    $filter->onlyNotSoftDeleted();
    $documentManager->getFilterCollection()->enable('softDelete');
