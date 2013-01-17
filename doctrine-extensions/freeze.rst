Freeze
======

Makes a document 'freezable'. A frozen document cannot be changed until it is thawed.

Config Options
^^^^^^^^^^^^^^

* **enableFreezeStamps** Boolean. If true, documents will get a :doc:`stamp` when the document is Frozen or Thawed.
* **enableAccessControl** Boolean. If true, :doc:`accessControl` will be enabled for Freeze and Thaw actions.
* **identityName** The identityName to use if ``enableFreezeStamps`` is ``true`` and an identity object is not provided to the config.
* **annotationReader** Used to read annotations
* **identity** If the identity provided implements ``Sds\Common\Identity\RoleAwareIdentityInterface`` then the Identity's roles will be passed to Access Control to use.
* **roles** If identity is not provided, or does not implement ``Sds\Common\Identity\RoleAwareIdentityInterface`` then ``roles`` may be an array of strings which are the roles passed to Access Control to use.

Basic Use
^^^^^^^^^

To make a document freezable, it must:

* Implement ``Sds\Common\Freeze\FreezeableInterface``.
* Have a boolean property annotated with ``@Sds\Freeze``. This property will store the flag indicating if the document is frozen or not. The ``Sds\DoctrineExtensions\Freeze\DataModel\FreezeableTrait`` can be used to create this property.

The simplest freezable document would look like this::

    use Sds\DoctrineExtensions\Freeze\DataModel\FreezeableTrait;
    use Sds\Common\Freeze\FreezeableInterface;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /** @ODM\Document */
    class Simple implements FreezeableInterface {

        use FreezeableTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

To freeze a document, set the property annotated with ``@Sds\Freeze`` to true. Alternately, if using the
``FreezableTrait`` you can just use the ``freeze`` method. eg::

    $myDoc->freeze();

During ``$documentManager->flush()`` this will raise two events:

* **preFreeze** occurs before the document is frozen.
* **postFreeze** occurs after the document is frozen.

While a document is frozen, no updates are permitted. If updates are attempted on a frozen
document, the **frozenUpdateDenied** event will be raised, and the updates will not be applied.

While a document is frozen, it cannot be deleted. If delete is attempted on a frozen
document, the **frozenDeleteDenied** event will be raised, and the delete will not occur.

To thaw a frozen document, set the property annotated with ``@Sds\Freeze`` to false. Alternately, if using the
``FreezableTrait`` you can just use the ``thaw`` method. eg::

    $myDoc->thaw();

During ``$documentManager->flush()`` this will raise two events:

* **preThaw** occurs before the document is thawed.
* **postThaw** occurs after the document is thawed.

Stamping
^^^^^^^^

Documents can be stamped with an identityName and timestamp each time they are frozen or thawed.
To enable stamping, set ``enableFreezeStamps`` to true in the extension config and set up documents
to be stamped. A stampable document should implement the interfaces corresponding to the stamps you want. The relevant interfaces are:

* Sds\Common\Freeze\FrozenByInterface
* Sds\Common\Freeze\FrozenOnInterface
* Sds\Common\Freeze\ThawedByInterface
* Sds\Common\Freeze\ThawedOnInterface

Each interface can be implemented with a corresponding trait:

* Sds\DoctrineExtensions\Freeze\DataModel\FrozenOnTrait
* Sds\DoctrineExtensions\Freeze\DataModel\FrozenByTrait
* Sds\DoctrineExtensions\Freeze\DataModel\ThawedOnTrait
* Sds\DoctrineExtensions\Freeze\DataModel\ThawedByTrait

If you want use all four interfaces traits, it can easily be done with ``Sds\DoctrineExtensions\Freeze\DataModel\FreezeStampTrait``.

A simple freezable document, with all stamps enabled would look like this::

    use Sds\Common\Freeze\FreezeableInterface;
    use Sds\Common\Freeze\FrozenByInterface;
    use Sds\Common\Freeze\FrozenOnInterface;
    use Sds\Common\Freeze\ThawedByInterface;
    use Sds\Common\Freeze\ThawedOnInterface;
    use Sds\DoctrineExtensions\Freeze\DataModel\FreezeableTrait;
    use Sds\DoctrineExtensions\Freeze\DataModel\FreezeStampTrait;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /** @ODM\Document */
    class Stamped implements
        FreezeableInterface,
        FrozenByInterface,
        FrozenOnInterface,
        ThawedByInterface,
        ThawedOnInterface
    {
        use FreezeableTrait;
        use FreezeStampTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

The stamps on this document could be interogated with these methods:

* $myDoc->getFrozenBy()
* $myDoc->getFrozenOn()
* $myDoc->getThawedBy()
* $myDoc->getThawedOn()

Access Control
^^^^^^^^^^^^^^

The Freeze extension integrates with the :doc:`accessControl` extension. It allows the definition
of two new actions ``freeze`` and ``thaw``. To enable access control, set ``enableAccessControl`` to
true in the extension config, and set up any access controlled documents. An access controlled document should
be implement the ``AccessControlledInterface`` and annotated with ``@Sds\AccessControl``.

For example::

    use Sds\Common\AccessControl\AccessControlledInterface;
    use Sds\Common\Freeze\FreezeableInterface;
    use Sds\DoctrineExtensions\AccessControl\DataModel\AccessControlledTrait;
    use Sds\DoctrineExtensions\Freeze\DataModel\FreezeableTrait;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /**
     * @ODM\Document
     * @Sds\AccessControl(
     *     @Sds\AccessControl\DefaultValue(false),
     *     @Sds\AccessControl\Freeze(true),
     *     @Sds\AccessControl\Thaw(true)
     * )
     */
    class AccessControlled implements FreezeableInterface, AccessControlledInterface {

        use FreezeableTrait;
        use AccessControlledTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

This document is access controlled for the ``freeze`` and ``thaw`` actions only. It is not access controlled
for ``create``, ``read``, ``update`` or ``delete``.

Using the Filter
^^^^^^^^^^^^^^^^

The Freeze extension includes a Freeze filter. This can be used to filter out frozen or thawed
documents from database result sets.

To return only frozen documents use::

    $filter = $documentManager->getFilterCollection()->getFilter('freeze');
    $filter->onlyFrozen();
    $documentManager->getFilterCollection()->enable('freeze');

To return only not frozen documents (all thawed documents, and those that have never been frozen) use::

    $filter = $documentManager->getFilterCollection()->getFilter('freeze');
    $filter->onlyNotFrozen();
    $documentManager->getFilterCollection()->enable('freeze');
