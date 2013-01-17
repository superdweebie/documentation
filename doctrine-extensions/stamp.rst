Stamp
=====

Stamps a document indicating who and when it was created or last changed. Other extensions such as
:doc:`freeze` and :doc:`softDelete` can add stamps for other actions.

Config Options
^^^^^^^^^^^^^^

* **identityName** The identityName to use if an identity object is not provided to the config.
* **annotationReader** Used to read annotations
* **identity**

Basic Use
^^^^^^^^^

Documents can be stamped with an identityName and timestamp when they are created and each time they are udpated.
A stampable document should implement the interfaces corresponding to the stamps you want. The relevant interfaces are:

* Sds\Common\Stamp\CreatedByInterface
* Sds\Common\Stamp\CreatedOnInterface
* Sds\Common\Stamp\UpdatedByInterface
* Sds\Common\Stamp\UpdatedOnInterface

Each interface can be implemented with a corresponding trait:

* Sds\DoctrineExtensions\Stamp\DataModel\CreatedOnTrait;
* Sds\DoctrineExtensions\Stamp\DataModel\CreatedByTrait;
* Sds\DoctrineExtensions\Stamp\DataModel\UpdatedOnTrait;
* Sds\DoctrineExtensions\Stamp\DataModel\UpdatedByTrait;

If you want to use all for traits, it can easily be done with ``Sds\DoctrineExtensions\Stamp\DataModel\StampTrait``.

A simple stampable document, with all stamps enabled would look like this::

    use Sds\DoctrineExtensions\Stamp\DataModel\StampTrait;
    use Sds\Common\Stamp;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /** @ODM\Document */
    class Simple implements
        Stamp\CreatedByInterface,
        Stamp\CreatedOnInterface,
        Stamp\UpdatedByInterface,
        Stamp\UpdatedOnInterface
    {
        use StampTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

The stamps on this document could be interogated with these methods:

* $myDoc->getCreatedBy()
* $myDoc->getCreatedOn()
* $myDoc->getUpdatedBy()
* $myDoc->getUpdatedOn()
