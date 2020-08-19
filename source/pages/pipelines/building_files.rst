.. _files-desc:

Files
*****

Overview
++++++++

Each of the step containers can have a number of files mounted - those files can be accessed from within the step script. Files are strictly connected with secrets, as their content is taken from the content of the secrets. The mount path provided in the secret has to be an absolute path.

.. note::
  File content can't be provided inline - it has to reference an already existing secret. The secret must be of ``generic`` type.

Example
-------

Here's an example of mounting a file into a pipeline step. Keep in mind that the generic ``content`` secret has been created using the IceCI UI and is available in the cluster.

.. code-block:: yaml

  steps:
  - name: step1
    containerRun:
      image: busybox
      files:
      - path: /var/myfile
        fromSecret: content
      script: |
        cat /var/myfile
