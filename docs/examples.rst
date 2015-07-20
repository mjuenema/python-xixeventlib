Examples
========

SNMPv1 TRAP
-----------

SNMPv1 is selected by setting ``version=1``.

.. code-block:: python
   
   from xixeventlib import event
   
   # xixEventSet
   event('my_event_id', 'Something bad happened', 
         target='nms.example.com', community='public',
         version=1)
   
   # xixEventClear
   event('my_event_id', None, 
         target='nms.example.com', community='public',
         version=1)


SNMPv2c NOTIFICATION
--------------------

SNMPv2 is selected by setting ``version=2``. This is also the default
value so it can be omitted.

.. code-block:: python
   
   from xixeventlib import event
   
   # xixEventSet
   event('my_event_id', 'Something bad happened', 
         target='nms.example.com', community='public')
   
   # xixEventClear
   event('my_event_id', None, 
         target='nms.example.com', community='public')


SNMPv2c INFORM
--------------

Setting ``inform=True`` will send an SNMPv2 INFORM instead of a 
NOTIFICATION. Set ``timeout`` and ``retries`` as needed or leave
them at their defaults.

.. code-block:: python
   
   from xixeventlib import event
   
   # xixEventSet
   event('my_event_id', 'Something bad happened', 
         target='nms.example.com', community='public',
         inform=True, timeout=1, retries=3)
   
   # xixEventClear
   event('my_event_id', None, 
         target='nms.example.com', community='public')
         inform=True, timeout=1, retries=3)


SNMPv3 NOTIFICATION
-------------------

SNMPv3 requires several more arguments. It is not necessary to set
``version=3`` as SNMPv3 is selected automatically if ``user`` is not
``None``. The ``authkey`` and ``privkey`` arguments must be set as
required. The ``authproto`` and ``privproto`` can be set to any
of the ``pysnmp.entity.rfc3413.oneliner.cmdgen.usm*AuthProtocol`` 
and ``pysnmp.entity.rfc3413.oneliner.cmdgen.usm*PrivProtocol`` symbols
respectively. 

A complete example could look like this.

.. code-block:: python
   
   from xixeventlib import event
   from pysnmp.entity.rfc3413.oneliner.cmdgen import usmHMACMD5AuthProtocol 
   from pysnmp.entity.rfc3413.oneliner.cmdgen import usmDESPrivProtocol
   
   # xixEventSet
   event('my_event_id', 'Something bad happened', 
         target='nms.example.com', 
         user='myuser', authkey='myauthkey', privkey='myprivkey',
         authproto=usmHMACMD5AuthProtocol,
         privproto=usmDESPrivProtocol)
   
   # xixEventClear
   event('my_event_id', None,
         target='nms.example.com', 
         user='myuser', authkey='myauthkey', privkey='myprivkey',
         authproto=usmHMACMD5AuthProtocol,
         privproto=usmDESPrivProtocol)

Adding ``inform=True`` will generate an SNMPv3 INFORM.


Asynchronous calls and callbacks
--------------------------------

So far all examples used synchronous calls. Setting ``callback`` to point
to a function will make calling `xixeventlib.event()` asynchronous. The 
``context`` argument can be used to pass additional information
to the callback function.

.. code-block:: python
   
   from xixeventlib import event

   def my_callback(sendRequestHandle, errorIndication, errorStatus, 
                   errorIndex, varBinds, context):
       # Do something here...

   # xixEventSet
   event('my_event_id', 'Something bad happened', 
         target='nms.example.com', community='public',
         callback=my_callback, context='Hello world')
   
   # xixEventClear
   event('my_event_id', None, 
         target='nms.example.com', community='public',
         callback=my_callback, context='Hello other world')

This works with SNMPv1 TRAPS as well as SNMPv2c and SNMPv3 
NOTIFICATIONS and INFORMS.
