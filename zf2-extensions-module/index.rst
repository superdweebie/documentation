Sds Zend Framework 2 Extensions Module
=====================================

.. toctree::
   :hidden:

   installation

A range of small extensions to zf2 classes used by other Sds zf2 modules.

Using Zf2 Extensions Module
---------------------------

* :doc:`installation`



#Rpc Use

The Json Rpc controller is much like the action controller that ships with
zf2. Here is a simple example:

    use Sds\JsonController\AbstractJsonRpcController;

    class JsonRpcTestController extends AbstractJsonRpcController
    {

        public function registerRpcMethods(){
            return array(
                'add',
                'concat'
            );
        }

        /**
        * @param int $a
        * @param int $b
        * @return int
        */
        public function add($a, $b)
        {
            return (int) $a + (int) $b;
        }

        /**
        * @param string $a
        * @param string $b
        * @return string
        */
        public function concat($a, $b)
        {
            return (string) $a . (string) $b;
        }
    }

The `registerRpcMethods` must return a list of methods the can be called via json
rpc. Each of those methods must use type hinting and docblock to describe them.

When the controller is called with `GET`, it will return a service map of the
api.

When the controller is called with `POST` it will execute the requested method.

For more information on the json rpc standard see json-rpc.org.

#Rest use

The rest controller is used in extacly the same manner as Zend AbstractRestfulController.
The only change is that PUT and POST parameters are expected as json.