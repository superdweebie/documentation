Annotation
==========

Raises annotation events and supports annotation inheritance.

This extension is a dependency of almost every other extension.

Config Options
^^^^^^^^^^^^^^

* **annotationReader** Used to read annotations.

Annotation Events
^^^^^^^^^^^^^^^^^

The annotation extension subscribes to Doctrine's ``loadClassMetadata`` event. The extension
will read all the annotations of a document, and raise annotation event for any
annotation that defines an ``event`` constant.  Annotations that do not define an
``event`` constant are ignored.

For example, an annotation may be defined as::

    use Doctrine\Common\Annotations\Annotation;

    /**
     * @Annotation
     * @Target({"PROPERTY"})
     */
    final class MyAnnotation extends Annotation
    {
        const event = 'myAnnotation';

        public $value;
    }

This annotation could be used on a document property like this::

    /**
     * @ODM\String
     * @MyAnnotation('myValue')
     */
    protected $name;

When ``@MyAnnotation`` is read by the Annotaion Extension, it will raise a `myAnnotation` event, and
pass that event an instance of `AnnotationEventArgs`. `AnnotationEventArgs` has the following useful methods::

    getMetadata()
    getAnnotation()
    getEventType() //will return 'class' | 'property' | 'method'
    getReflection()

Annotation Inheritance
^^^^^^^^^^^^^^^^^^^^^^

Annotation Extension will raise annotation events for annotations in the document
being read, and also any parent documents. If the same annotation appears on a parent and
a child, only the child annotation will raise an event. Ie: child annotation can be used
to override parent annotations.