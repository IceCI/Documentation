Installation
############

IceCI's installation consists of multiple components. This guide will show you how can you customize your installation to best suit your needs

.. important::
  The provided yaml files are suitable only for single node installations. If you want to deploy IceCI in a multi node cluster, the storage will need to be updated.

Ice operator
------------

Custom resources
****************

IceCI uses a range of custom Kubernetes resources. They are required by the operator to properly handle repositories and run builds.

.. code-block:: bash

  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/iceci.io_gitpipelines_crd.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/iceci.io_gitwatchers_crd.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/icekube.io_backingservices_crd.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/icekube.io_dockerbuilds_crd.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/icekube.io_gitclones_crd.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/icekube.io_pipelines_crd.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/icekube.io_tasks_crd.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/crds/icekube.io_workspaces_crd.yaml

.. note::
  You can find out more about Kubernetes custom resources in the `documentation <https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/>`_



Service accounts and roles
**************************

The operator uses two separate service accounts - one is used by the operator itself, the other one is used by the pipeline steps. Those service accounts are required for the operator to work properly.

.. code-block:: bash

  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/operator/service_account.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/operator/role.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/operator/role_binding.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/operator/step_service_account.yaml



Storage
*******

The operator uses two types of storage - *dynamic* and *shared* storage. By default, both of the persistent volumes are of *hostPath* type. The storage is required by the operator.

.. code-block:: bash

  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/operator/storage-pvc.yaml


.. important::
  As mentioned, the *hostPath* storage type will be suitable only for single node clusters and will have to be changed for multi node installations.



The operator
************

The operator is the heart of IceCI, responsible for handling the creation and lifecycle of all the objects related to running the pipelines.

.. code-block:: bash

  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/operator/operator.yaml



DB Sync
-------

PostgreSQL
**********

A PostgreSQL database is used to store all the data extracted by the sync application.

.. code-block:: bash

  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/app/postgres.yaml



Sync
****

The sync application monitors the cluster for object changes (like new object creation or status updates) - as well as logs - and stores them in the database for persistence. Installing the application is optional - unless the API and UI are used.

.. code-block:: bash

  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/app/sync.yaml



API and UI
--------------

The API and UI provide a web interface for interacting with the operator. The applications allow for creating and monitoring pipelines, providing access to information about the pipelines themselves, as well as specifics regarding builds. Installing the applications is optional.

.. code-block:: bash

  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/app/api.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/app/ui.yaml
  kubectl apply -f https://raw.githubusercontent.com/IceCI/IceCI/master/manifests/app/ingress.yaml


.. important::
  The API pulls information from the database, hence the sync application is required for the web applications to function correctly.



Further reading
---------------

For more information about customizing the configuration of particular apps, please check out the :doc:`Configuration page <configuration>`.