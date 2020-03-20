Configuration
#############

This guide will show you how can you customize and configure IceCI's installation for your cluster.

.. note::
  We'll focus on customizing the deploy by modifying the YAML manifests provided in IceCI's repository.

Before we start, clone IceCI's repository so you can make the modifications locally.

.. code-block:: bash

  git clone https://github.com/IceCI/IceCI.git



Storage config
--------------

The storage configuration manifests can be found in ``manifests/operator/pvc-storage.yaml``. As you can see, two types of storage are defined - **static** and **dynamic storage**. In the provided installation, the volumes are configured as 1Gi host paths. As mentioned in the :doc:`installation <installation>` page - it will work fine for single node clusters. You might want to change the type and the requested disk space though, depending on the kind of setup you'll be deploying the application in.

The names of the storage PVCs are defined in the environment variables in ``manifests/operator/operator.yaml`` - you can create the PVCs with different names, but remember to update them in the operator configuration.

.. important::
  The operator isn't the only application making use of those PVCs - keep in mind that the sync app uses them as well! You can find it's manifest in ``manifests/app/sync.yaml``.



Database config
---------------

The default IceCI manifests deploy a PostgreSQL instance for persisting pipeline and build data - it can be found in ``manifests/app/postgres.yaml``. If you already have a database up and running that you'd like to use with IceCI, you can simply skip the creation of that DB and configure the applications to use your own. All you have to do is configure the following environment variables in the API and sync apps. Their manifests can be found in ``manifests/app/api.yaml`` and ``manifests/app/sync.yaml``, respectively.

.. code-block:: yaml

  - name: ICECI_DB_USER
    value: <db_user>
  - name: ICECI_DB_HOST
    value: <db_host>
  - name: ICECI_DB_NAME
    value: <db_name>
  - name: ICECI_DB_PASS
    value: <db_pass>
  - name: ICECI_DB_PORT
    value: <db_port>
  - name: ICECI_DB_DIALECT
    value: <db_dialect>



Resource requests and limits
----------------------------

By default, the applications deployed in the cluster don't have any resources requests nor limits set. You can add them easily by editing the deployment manifests for each of the applications that you decide do deploy in the cluster. You can read more about resource requests and limits in the `Kubernetes documentation <https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/>`_.



Further reading
---------------

How that you've got IceCI installed and configured in your cluster, it's time to build some pipelines. If you need a quick refresher on how a basic pipeline is built, you can go back to the :doc:`Quickstart <quickstart>` page or visit one of example repositories, like the `Quickstart example <https://github.com/IceCI/example-quickstart>`_ or the `Python Flask API example <https://github.com/IceCI/example-python-flask-api>`_. For a more comprehensive guide on how the pipeline configuration files are structured - check out the :doc:`Pipelines <pipelines/overview>` page.