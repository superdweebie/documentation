State
=====

Adds a ``state`` property to a document. Used by :doc:`accessControl` and :doc:`workflow`.

Config Options
^^^^^^^^^^^^^^

* **enableAccessControl** Boolean. If true, :doc:`accessControl` will be enabled for transition actions.
* **annotationReader** Used to read annotations
* **identity** If the identity provided implements ``Sds\Common\Identity\RoleAwareIdentityInterface`` then the Identity's roles will be passed to Access Control to use.
* **roles** If identity is not provided, or does not implement ``Sds\Common\Identity\RoleAwareIdentityInterface`` then ``roles`` may be an array of strings which are the roles passed to Access Control to use.

Basic Use
^^^^^^^^^

To give a document state, it must:

* Implement ``Sds\Common\State\StateAwareInterface``.
* Have a String property annotated with ``@Sds\State``. This property will store the a string indicating the document's state.
* If using access control, the property must also be annotated with ``@Sds\AccessControl(@Sds\AccessControl\Update(false))``.
* The ``Sds\DoctrineExtensions\State\DataModel\StateAwareTrait`` can be used to create the required property with the correct annotations.

The state enabled document would look like this::

    use Sds\Common\State\StateAwareInterface;
    use Sds\DoctrineExtensions\State\DataModel\StateAwareTrait;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /** @ODM\Document */
    class Simple implements StateAwareInterface {

        use StateAwareTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

A document's state can be accessed with::

    $state = $myDoc->getState();
    $myDoc->setState('new_state');

When a document's state is changed it is known as a Transition. A transition is often
represented by a Transition object, with these useful methods::

* ``$transition->getFromState()`` returns the old state name.
* ``$transition->getToState()`` returns the new state name.
* ``$transition->getAction()`` returns a string with the format ``oldStateName-newStateName`` which is used by :doc:`accessControl`

When a document's state is changed, during ``$documentManager->flush()`` three events will be raised:

* **preTransition** Called before state change. Can be used to roll the state change back.
* **onTransition** Called during state change. Can be used to update the workflow.
* **postTransition** Called after state change complete.

Each of these events will pass a ``Sds\DoctrineExtensions\State\EventArgs`` object. Which includes the ``Transition``
object which can be accessed by::

    $transition = $eventArgs->getTransition();

Access Control
^^^^^^^^^^^^^^

The State extension integrates with the :doc:`accessControl` extension in two ways:

* Permissions can be defined with a ``state`` parameter.
* Transitions from one state to another can be access controlled.

Permissions with ``state`` parameter
------------------------------------
When a Permission is defined it can take three parameters:

* **Role**: defines who, this is compared to the array of roles pulled from the Config Options.
* **Action**: defines what, either create, read, update, or delete.
* **State**: optional paramter which can control when an action can be performed.

For example::

    $myDoc->addPermission(new Permission('user', 'delete', 'expired'));

This permission would grant identities with the `user` role permission to do the `delete` action when, and only when,
the document's state is `expired`. If the document's state is anything else, permission would be denied.

See :doc:`accessControl` for further discussion of Permissions.

Access Controlled Transitions
-----------------------------

An access control action can be named after any transition. To enable transition access control, set ``enableAccessControl`` to
true in the extension config, and set up any access controlled documents. An access controlled document should
be implement the ``AccessControlledInterface`` and annotated with ``@Sds\AccessControl``.

For example::

    use Sds\Common\AccessControl\AccessControlledInterface;
    use Sds\Common\State\StateAwareInterface;
    use Sds\DoctrineExtensions\AccessControl\DataModel\AccessControlledTrait;
    use Sds\DoctrineExtensions\State\DataModel\StateAwareTrait;

    //Annotation imports
    use Doctrine\ODM\MongoDB\Mapping\Annotations as ODM;
    use Sds\DoctrineExtensions\Annotation\Annotations as Sds;

    /**
     * @ODM\Document
     * @Sds\AccessControl(
     *     @Sds\AccessControl\DefaultValue(false),
     *     @Sds\AccessControl\DefaultTransition(true)
     * )
     */
    class AccessControlled implements StateAwareInterface, AccessControlledInterface {

        use StateAwareTrait;
        use AccessControlledTrait;

        /**
         * @ODM\Id
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

This document is access controlled for every transition. It is not access controlled
for ``create``, ``read``, ``update`` or ``delete``.

For example, imagine this document was allowed to have three states: `draft`, `embargoed` and `published`.
Imagine you wanted to allow identities with a `writer` or `editor` role to to this transition:

* draft -> embargoed

And you wanted to allow only identites with the `editor` role to do these tranitions:

* draft -> published
* embargoed -> published

Then add the following permissions to a document::

   $myDoc->addPermission(new Permission('writer', 'draft-embargoed'));
   $myDoc->addPermission(new Permission('editor', 'draft-embargoed'));
   $myDoc->addPermission(new Permission('editor', 'draft-published'));
   $myDoc->addPermission(new Permission('editor', 'embargoed-published'));

The :doc:`workflow` extension further leverages States and Transitions to create document workflows.

Using the Filter
^^^^^^^^^^^^^^^^

The State extension includes a State filter. This can be used to filter documents with defined states from
database result sets.

First set a list of states::

    $filter = $documentManager->getFilterCollection()->getFilter('state');
    $filter->setStates(['state1', 'state2']);

Then set if only those states should be inclueded::

    $filter->includeStateList();

or exclueded::

    $filter->excludeStateList();

Then enable the filter::

    $documentManager->getFilterCollection()->enable('state');
