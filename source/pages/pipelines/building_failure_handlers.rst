.. _failure-handlers-desc:

Failure handlers
****************

Overview
++++++++

Failure handlers are very similar to :py:attr:`docker run<DockerRun>` :py:attr:`steps<Step>`. They will execute a set of commands in a *container*. The main difference is that they are used to execute code after a pipeline step fails. This allows you to add cleanup and notification actions to the pipeline.

Failure handlers are defined globally, and can be referenced on both global pipeline level and on :py:attr:`step<Step>` level. Thanks to that, you can create a global error notification, but also add certain cleanup logic to specific steps.

Failure handlers are executed in order of appearance of the ``onFailure`` section of the pipeline. Failure handlers for :py:attr:`steps<Step>` are executed first, followed by  failure handlers defined in the :py:attr:`global<globals>` section.

.. important::
  When multiple failure handlers are specified, and one of them fails, the other ones will continue to execute. The pipeline tries to run every failure handler ignoring statuses of the previous ones.

..  important::
  Failure handlers don't affect the status of the pipeline. If a step fails, but all failure handlers finish correctly, the pipeline will still be in failed status!



Examples
++++++++

Single failure handler
______________________

Below is an example of running a failure handler after a failed :attr:`step<Step>`. It also shows how environment variables are injected to every :attr:`step<Step>` and failure handler in the pipeline.

.. code-block:: yaml

  steps:
  - name: step-that-fails
    dockerRun:
      image: busybox
      script: "noSuchCommand"
    onFailure:
    - handlerName: failure-handler-1

  failureHandlers:
  - name: failure-handler-1
    image: busybox
    script: 'echo "step $ICE_FAILED_STEP_NAME has failed"'


Global failure handlers
_______________________

Below is an example of running a :attr:`failure handlers<FailureHandler>` from both :attr:`step<Step>` and pipeline level.

.. code-block:: yaml

  steps:
  - name: step-that-fails
    dockerRun:
      image: busybox
      script: "noSuchCommand"
    onFailure:
    - handlerName: failure-handler-1
    - handlerName: failure-handler-2

  globals:
    onFailure:
    - handlerName: failure-handler-1
    - handlerName: failure-handler-3

  failureHandlers:
  - name: failure-handler-1
    image: busybox
    script: 'echo "failure handler $ICE_STEP_NAME"'

  - name: failure-handler-2
    image: busybox
    script: 'echo "failure handler $ICE_STEP_NAME"'

  - name: failure-handler-3
    image: busybox
    script: 'echo "failure handler $ICE_STEP_NAME"'

.. note::
  Notice that ``failure-handler-1`` will run twice because it's declared in both the global section and in the step. Currently IceCI does not implement any deduplication mechanism for failure handlers.


Environment variables and files
_______________________________

Here's an example of defining environment variables and files on :attr:`failure handler<FailureHandler>` level.

.. code-block:: yaml

  steps:
  - name: step-that-fails
    dockerRun:
      image: busybox
      script: "noSuchCommand"
    onFailure:
    - handlerName: failure-handler-1

  failureHandlers:
  - name: failure-handler-1
    image: busybox
    script: |
      echo $ICE_FH
      cat /mnt/file
    environment:
    - name: ICE_FH
      value: failure-handler-env
    files:
    - path: /mnt/file
      fromSecret: failure-secret
