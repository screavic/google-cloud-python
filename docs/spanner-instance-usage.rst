Instance Admin API
==================

After creating a :class:`~google.cloud.spanner.client.Client`, you can
interact with individual instances for a project.

Instance Configurations
-----------------------

Each instance within a project maps to a named "instance configuration",
specifying the location and other parameters for a set of instances.  These
configurations are defined by the server, and cannot be changed.

To list of all instance configurations available to your project, use the
:meth:`~google.cloud.spanner.client.Client.list_instance_configs`
method of the client:

.. code:: python

    configs, token = client.list_instance_configs()


To fetch a single instance configuration, use the
:meth:`~google.cloud.spanner.client.Client.get_instance_configuration`
method of the client:

.. code:: python

    config = client.get_instance_configuration('config-name')


List Instances
--------------

If you want a comprehensive list of all existing instances, use the
:meth:`~google.cloud.spanner.client.Client.list_instances` method of
the client:

.. code:: python

    instances, token = client.list_instances()


Instance Factory
----------------

To create a :class:`~google.cloud.spanner.instance.Instance` object:

.. code:: python

    config = configs[0]
    instance = client.instance(instance_id,
                               configuration_name=config.name,
                               node_count=10,
                               display_name='My Instance')

- ``configuration_name`` is the name of the instance configuration to which the
  instance will be bound.  It must be one of the names configured for your
  project, discoverable via
  :meth:`google.cloud.spanner.client.Client.list_instance_configs`.

- ``node_count`` is a postitive integral count of the number of nodes used
  by the instance.  More nodes allows for higher performance, but at a higher
  billing cost.

- ``display_name`` is optional. When not provided, ``display_name`` defaults
  to the ``instance_id`` value.

You can also use :meth:`Client.instance` to create a local wrapper for
an instance that has already been created:

.. code:: python

    instance = client.instance(existing_instance_id)
    instance.reload()


Create a new Instance
---------------------

After creating the instance object, use its
:meth:`~google.cloud.spanner.instance.Instance.create` method to
trigger its creation on the server:

.. code:: python

    instance.display_name = 'My very own instance'
    operation = instance.create()

.. note::

    Creating an instance triggers a "long-running operation" and
    returns an :class:`google.cloud.spanner.instance.Operation`
    object.  See :ref:`check-on-current-instance-operation` for polling
    to find out if the operation is completed.


Refresh metadata for an existing Instance
-----------------------------------------

After creating the instance object, reload its server-side configuration
using its :meth:`~google.cloud.spanner.instance.Instance.reload` method:

.. code:: python

    instance.reload()

This will load ``display_name``, ``config_name``, and ``node_count``
for the existing ``instance`` object from the back-end.


Update an existing Instance
---------------------------

After creating the instance object, you can update its metadata via
its :meth:`~google.cloud.spanner.instance.Instance.update` method:

.. code:: python

    client.display_name = 'New display_name'
    operation = instance.update()

.. note::

    Update an instance triggers a "long-running operation" and
    returns a :class:`google.cloud.spanner.instance.Operation`
    object.  See :ref:`check-on-current-instance-operation` for polling
    to find out if the operation is completed.


Delete an existing Instance
---------------------------

Delete an instance using its
:meth:`~google.cloud.spanner.instance.Instance.delete` method:

.. code:: python

    instance.delete()


.. _check-on-current-instance-operation:

Check on Current Instance Operation
-----------------------------------

The :meth:`~google.cloud.spanner.instance.Instance.create` and
:meth:`~google.cloud.spanner.instance.Instance.update` methods of instance
object trigger long-running operations on the server, and return instances
of the :class:`~google.cloud.spanner.instance.Operation` class.

You can check if a long-running operation has finished
by using its :meth:`~google.cloud.spanner.instance.Operation.finished`
method:

.. code:: python

    >>> operation = instance.create()
    >>> operation.finished()
    True

.. note::

    Once an :class:`~google.cloud.spanner.instance.Operation` object
    has returned :data:`True` from its
    :meth:`~google.cloud.spanner.instance.Operation.finished` method, the
    object should not be re-used. Subsequent calls to
    :meth:`~google.cloud.spanner.instance.Operation.finished`
    will result in an :exc`ValueError` being raised.

Next Step
---------

Now we go down the hierarchy from
:class:`~google.cloud.spanner.instance.Instance` to a
:class:`~google.cloud.spanner.database.Database`.

Next, learn about the :doc:`spanner-database-usage`.


.. _Instance Admin API: https://cloud.google.com/spanner/reference/rpc/google.spanner.admin.instance.v1
