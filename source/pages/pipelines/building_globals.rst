.. _globals-desc:

Globals
*******

Overview
++++++++

Some of the settings that can be specified for :attr:`steps<Step>` can also be specified in the :attr:`globals<Globals>` section - this means that they will be applied to all :attr:`steps<Step>` in the pipeline. Thanks to this, you don't have to redeclare settings (like environment variables) in each :attr:`step<Step>`, but set it globally for the whole pipeline instead.


.. note::
  Objects from the global section will be passed to steps in the pipeline only when it makes sense. See the :attr:`globals` reference for more details.



Examples
++++++++

Environment variables
_____________________

Below is an example of setting up a :attr:`global<globals>` environment variables and overriding them on :attr:`step<Step>` level.

.. code-block:: yaml

  globals:
    environment:
    - name: GLOBAL_ENV
      value: global-value

  steps:
  - name: step1
    containerRun:
      image: busybox
      script: "printenv GLOBAL_ENV"

  - name: step2
    containerRun:
      image: busybox
      script: "printenv GLOBAL_ENV"
      environment:
      - name: GLOBAL_ENV
        value: local-value

Files
_____

Here's an example of setting up a :attr:`global<globals>` file and overriding it on :attr:`step<Step>` level.

.. code-block:: yaml

  globals:
    files:
    - path: /mnt/file
      fromSecret: global-secret

  steps:
  - name: step1
    containerRun:
      image: busybox
      script: "cat /mnt/file"

  - name: step2
    containerRun:
      image: busybox
      script: "cat /mnt/file"
      files:
      - path: /mnt/file
        fromSecret: local-secret
