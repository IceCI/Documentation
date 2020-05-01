.. _steps-desc:

Steps
*****

Overview
++++++++

Steps are the bread and butter of every pipeline. They are operations that will be executed during the pipeline execution. Steps are run in the order defined in the :attr:`steps` field in pipeline :ref:`spec<pipeline-root-reference>`.

When one of the :attr:`steps<Step>` fail, :ref:`failure handlers<failure-handlers-desc>` are executed and the pipeline finishes. No other :attr:`steps<Step>` in the pipeline will run.

.. note::
  Currently IceCI does not support parallel pipeline execution. This feature is considered for future versions.

Every :attr:`step<Step>` is executed in a *container*, running as a *pod* in *Kubernetes cluster*. Every :attr:`step<Step>` has the same *volume* mounted in the ``/workspace`` directory inside the *container* running the :attr:`step<Step>`. This *volume* contains  source code related to the Git event that occurred in the git repository. The *volume* is persistent across the whole pipeline and is isolated from other pipelines.

.. note::
  If you create files in the ``/workspace`` directory in any of your steps during pipeline execution, all the subsequent steps will  have access those files.



Examples
++++++++

Simple steps
____________

Here's an example of a working pipeline having 2 simple :attr:`container run<ContainerRun>` :attr:`steps<Step>`.

.. code-block:: yaml

  steps:
  - name: step1
    containerRun:
      image: busybox
      script: "echo 'Hello world!'"

  - name: step2
    containerRun:
      image: busybox
      script: |
        echo "step 2"
        env



Persistent workspace
____________________

An example of generating a file and then accessing it in next :attr:`step<Step>`.

.. code-block:: yaml

  steps:
  - name: generate-date
    containerRun:
      image: busybox
      script: "date > date.log"

  - name: print-date
    containerRun:
      image: busybox
      script: "cat date.log"


Environment variables
_____________________

Here's an example of passing environment variables to a container.

.. code-block:: yaml

  steps:
  - name: env-test
    containerRun:
      image: busybox
      script: "printenv ENV_VAR_1"
      environment:
      - name: ENV_VAR_1
        value: test-value

.. note::

  As you can see, the environment variable value is hardcoded into the pipeline. This is fine if your build configuration doesn't contain passwords or other sensitive data. For more information on how to manage sensitive data in ``IceCI`` see :ref:`secrets section<secrets-desc>`.

Files
_____

Here's an example of mounting files from a secret in a container.

.. code-block:: yaml

  steps:
  - name: file-test
    containerRun:
      image: busybox
      script: "cat /mnt/file"
      files:
      - path: /mnt/file
        fromSecret: file-secret

.. note::

  The content of a file can't be defined inline. Every file has to have a reference to a secret, from which the content is pulled.

Conditional execution
---------------------

You can control which Git events trigger the execution of your step, here's an example.

.. code-block:: yaml

  steps:
  - name: step1
    when:
      event: ["commit"]
      branch: ["master"]
      skipBranch: ["development", "feature-*"]
    containerRun:
      image: busybox
      script: "echo 'Hello world!'"

Further reading
+++++++++++++++

For more information about steps and their specification see :py:attr:`Step`

