Access Control
==============

Permissions based access to documents. Supports permissions for Create, Read, Update and Delete.

Config Options
^^^^^^^^^^^^^^

* **permissionClass** The embedded document class used to store permissions. Defaults to ``DataModel\Permission``
* **annotationReader** Used to read annotations
* **identity** If the identity provided implements ``Sds\Common\Identity\RoleAwareIdentityInterface`` then the Identity's roles will be passed to Access Control to use.
* **roles** If identity is not provided, or does not implement ``Sds\Common\Identity\RoleAwareIdentityInterface`` then ``roles`` may be an array of strings which are the roles passed to Access Control to use.

Basic Use
^^^^^^^^^

A document may have several permission documents embedded. These permission documents define
who can do what and when. A permission document has two required properties:

* **Role**: defines who, this is compared to the array of roles pulled from the Config Options.
* **Action**: defines what, either create, read, update, or delete.

A permission document also has an optional **state** which can be used in conjunction
with :doc:`state` to control when an action is permitted.

So, a permission defines which roles can do what action when a document is in a particular state.

To enable access control on a document and embed permissions, the document must implement
``Sds\Common\AccessControl\AccessControlledInterface``. Permission documents must
implement ``Sds\Common\AccessControl\PermissionInterface``. To make this simpler, the
following are provided:

* **DataModel\PermissionTrait** provides code to satisfy ``PermissionInterface`` to make the creation of a permission document class simple.
* **DataModel\Permission** a basic permission document that uses ``DataModel\PermissionTrait``
* **DataModel\AccessControlledTrait** provides code to implement the ``AccessControlledInterface`` using ``DataModel\Permission``

Using all these pieces, an access controlled document can be defined simply like this::

    /**
     * @ODM\Document
     */
    class Simple implements AccessControlledInterface {

        use AccessControlledTrait;

        /**
         * @ODM\Id(strategy="UUID")
         */
        protected $id;

        public function getId() {
            return $this->id;
        }
    }

Permissions can be added to the document like this::

    use Sds\Common\AccessControl\Constant\Action;
    use Sds\Common\AccessControl\Constant\Role;
    use Sds\DoctrineExtensions\AccessControl\DataModel\Permission;

    $testDoc = new Simple();

    $testDoc->setPermissions([
        new Permission(Role::user, Action::create),
        new Permission(Role::guest, Action::read),
        new Permission(Role::guest, Action::update)
    ]);

The roles to test permissions against can be defined by passing a ``RoleAwareIdentity`` object to ExtensionConfig,
or by explicitly defineing a list of roles::

    $identity->addRole('guest');
    $manifestConfig = new ManifestConfig([
        'identity' => $identity,
        'extensionConfigs' => ['Sds\DoctrineExtensions\AccessControl' => null]
    ]);

or::

    $manifestConfig = new ManifestConfig([
        'extensionConfigs' => ['Sds\DoctrineExtensions\AccessControl' => ['roles' => ['guest']]
    ]);

Note: if you are using ZendFamework2, :doc:`\authentication-module\index` integrates with the
Zend AuthenticationService, so the Identity can be automatically configured at each request and
passed by :doc:`\doctrine-extensions-module\index` to the ``ManifestConfig``.

Finally, turn on the readAccessControl filter::

    $documentManager->getFilterCollection()->enable('readAccessControl');

Once these document, permissions, roles, and read filter have been set up, just use doctrine as normal.
Access Control will not raise exceptions if a disallowed action is attempted, so that other
operations during a flush are not stopped. Instead Access Controll will raise these doctrine events
which may be listened to.

* **createDenied**
* **udpateDenied**
* **deleteDenied**

These events will pass a standard ``Doctrine\ODM\MongoDB\Event\LifecycleEventArgs``.

There is no event for readDenied. This is because read access control is enforced through a filter, so read
access denied documents are never fetched from the database.

If you want test different actions and documents, use the ``AccessController``. It has three
useful static methods.

**getAllowedActions** returns a list of actions that can be executed on a document by a list of roles::

    getAllowedActions($document, array $roles = [], $state = null)

**isActionAllowed** returns a boolean to indicate if the given list of roles may perform the given action on the given document::

    isActionAllowed($document, $action, array $roles = [], $state = null)

**isAccessControlEnabled** returns a boolean to indicate if access control is enabled for a certain action on a given class of documents (the isTransition argument is present to support :doc:`workflow`)::

    isAccessControlEnabled(ClassMetadata $metadata, $action, $isTransition = false)

Finer grained Access Control
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

By default Access Control is enabled for all actions (create, read, update, delete) on all properties of
a document. However, this default is not always ideal. Document annotations can be used to give
finer control over which actions and properties should be subject to access control checks.

The default behaviour is equivalent to placing this as a class annotation::

    @Sds\AccessControl(@Sds\Accesscontrol\DefaultValue(true))

To enable/disable access control checks for specific actions on a whole document use the
following annotations::

    @Sds\AccessControl\Create
    @Sds\AccessControl\Read
    @Sds\AccessControl\Update
    @Sds\AccessControl\Delete

For example, this class annotation this would enable access control checks for Read and Update only::

    @Sds\AccessControl(
        @Sds\AccessControl\Create(false)
        @Sds\AccessControl\Delete(false)
    )

Alternately, this class annotations would have the same effect::

    @Sds\AccessControl(
        @Sds\Accesscontrol\DefaultValue(false)
        @Sds\AccessControl\Read(true)
        @Sds\AccessControl\Update(true)
    )

Annotations may also be placed on properties to enable/disable access control checks. For example,
this would disable the access control update check for only the state property when it
is changed::

    /**
     * @ODM\String
     * @Sds\AccessControl(@Sds\AccessControl\Update(false))
     */
    protected $state;

Note: The :doc:`annotation` extension supports annotation inheritance, so access control
enable/disable can vary up and down the inheritance tree.

Extending Access Control
^^^^^^^^^^^^^^^^^^^^^^^^

Access Control can be extended beyond the basic Create, Read, Update and Delete permissions.

For example, the :doc:`softDelete` and :doc:`freeze` extensions add SoftDelete, Restore, Freeze and Thaw permissions.

You may add your own custom permissions also.

Defineing your own roles is easy, just start using them. The roles defined in ``Sds\Common\AccessControl\Constant\Role`` are just for convenience.

Defineing your own actions is almost as easy. As with roles, actions are just a string, and you
can just start using them. However, if you want a particular action to be checked at some
point in a document's life cycle, you will want to write and register a Doctrine Event Subscriber.
The subscriber can use the helper methods from ``AccessController`` to determine if an action
is permitted, and then do stuff depending on the result.