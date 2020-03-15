.. _services-desc:

Services
********

Overview
++++++++

A service in IceCI is a container that is running during the whole pipeline execution. Services let you run backing containers for your pipeline - for example a PostgreSQL database for your integration tests.

Every service has a name - in the pipeline it can be resolved using this name. If you create a **website** service, running ``curl http://website`` command in your pipeline :py:attr:`step<Step>` will hit the **website** service assuming that it's a container listening on port **80** (like **nginx**, for example).

.. note::
  The pipeline will stop all services after finishing - regardless of the pipeline result.

.. warning::
  If a service stops during the pipeline execution, the pipeline will fail no matter what was the exit code of the service main process.



Examples
++++++++

Hitting an nginx service
________________________

Below is an example of a working pipeline running a **website** :py:attr:`service<Service>` and a **step1** :py:attr:`step<Step>` that will curl the nginx container running as the **website** :py:attr:`service<Service>`.

.. code-block:: yaml

  services:
  - name: website
    image: nginx

  steps:
  - name: step1
    dockerRun:
      image: iceci/utils
      script: "curl http://website/"


Further reading
+++++++++++++++

Service reference can be found in the :ref:`service <service-reference>` section.
