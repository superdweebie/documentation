Annotations Reference
=====================

**@AccessControl** Used to specify which Access Control permission checks should be applied to a document or property.

**@AccessControl\Create(true | false)** Used to turn on|off all access control checks for the 'create' action.

**@AccessControl\DefaultValue(true | false)** Used to turn on|off all access control checks.

**@AccessControl\DefaultTransition(true | false)**

**@AccessControl\Delete(true | false)** Used to turn on|off all access control checks for the 'delete' action.

**@AccessControl\Freeze(true | false)** Used to turn on|off all access control checks for the 'freeze' action.

**@AccessControl\Read(true | false)** Used to turn on|off all access control checks for the 'read' action.

**@AccessControl\Restore(true | false)** Used to turn on|off all access control checks for the 'restore' action.

**@AccessControl\SoftDelete(true | false)** Used to turn on|off all access control checks for the 'softdelete' action.

**@AccessControl\Thaw(true | false)** Used to turn on|off all access control checks for the 'thaw' action.

**@AccessControl\Transition(value = true | false, fromState = stateName, toState = stateName)**

**@AccessControl\Update(true | false)** Used to turn on|off all access control checks for the 'update' action.

**@ClassName** Add the class name property when serializing.

**@Discriminator** Add the discriminator property when serializing.

**@Eager** Use the eager reference serializer.

**@Freeze** Annotates a property to bed used to flag if a document is frozen.

**@Getter(methodName)** Defines a non-standard getter.

**@Ignore(null | 'ignore_always' | 'ignore_none' | 'ignore_when_serializing' | 'ignore_when_unserializing')** Tell the serializer to selectively ignore properties.

**@Lazy** Use the lazy reference serializer.

**@Readonly** Marks a field as read only.

**@ReferenceSerializer(className)** Defines the serializer use use for a reference.

**@Setter(methodName)** Defines a non-standard setter.

**@Serializer(array)** Can contain a list of other annotations to control the behaviour of the Serializer.

**@SoftDelete** Annotates a property to bed used to flag if a document is soft deleted.