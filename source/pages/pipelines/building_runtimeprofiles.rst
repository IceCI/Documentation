.. _runtime-profiles-desc:

Runtime profiles
****************

Overview
++++++++

Runtime profiles are configuration sets which can be assigned to specific pipeline steps. They allow for fine-tuning the pod configuration by setting resource requirements or node selectors, as well adding additional information via labels and annotations, among other things. The runtime profiles are Kubernetes custom resources that can be created in the cluster by the administrator. After creation they can be used in the pipelines by all the users. If a pipeline step uses a runtime profile, the specification details of the profile will be copied to the pipeline step pod.


Example
+++++++

Below is an example of a runtime profile YAML manifest, using all of the possible configuration fields.

.. code-block:: yaml

  apiVersion: iceci.io/v1alpha1
  kind: RuntimeProfile
  metadata:
    name: example
  spec:
    serviceAccountName: myuser
    imageBuilder: buildkit
    nodeSelector:
      kubernetes.io/hostname: iceci-builder
    annotations:
      example/annotation: myvalue
    labels:
      example/label: myvalue
    resources:
      requests:
        cpu: "1"
        memory: 128Mi
      limits:
        cpu: "1"
        memory: 128Mi


Creating and using runtime profiles
+++++++++++++++++++++++++++++++++++

Runtime profiles can only be created directly inside the cluster by an administrator. To create a profile, simply prepare the YAML manifest and run ``kubectl apply``.

To apply a runtime profile inside your step, add the ``runtimeProfile`` parameter to your step definition in ``.iceci.yaml``

.. code-block:: yaml

  steps:
  - name: step1
    runtimeProfile: myprofile
    containerRun:
      image: busybox
      script: "echo 'Hello world!'"


Runtime profile schema
++++++++++++++++++++++

As mentioned, runtime profiles are Kubernetes custom resources and as such, at the top level, they follow the standard CRD schema.

.. py:attribute:: apiVersion
  :type: string

  The API version of the CRD. The value that should be used is ``iceci.io/v1alpha1``

.. py:attribute:: kind
  :type: string

  The kind of the CRD. The value that should be used is ``RuntimeProfile``

.. py:attribute:: metadata
  :type: map[string]string

  The metadata of the CRD. Should contain the ``name`` field, but additional values can be added if needed.

.. py:attribute:: spec
  :type: Object

  The specification of the runtime profile. It may contain any of the following.

  .. py:attribute:: serviceAccountName
    :type: string

    The name of the service account that will be used to run the pipeline step pod.

  .. py:attribute:: imageBuilder
    :type: string

    Name of the library that will be used to build Docker images in ``ContainerBuild`` steps. The possible values are ``kaniko`` and ``buildkit``. If this parameter isn't provided, all builds are done using ``kaniko``.

  .. py:attribute:: nodeSelector
    :type: map[string]string

    A map of node labels that will be used to determine which node pipeline step pod will be scheduled onto. This field follows the structure of the Kubernetes native node selector. For more information on node selectors, see the `node selector <https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector>`_ section in the Kubernetes documentation.

  .. py:attribute:: annotations
    :type: map[string]string

    A map of annotations that will be added to the pipeline step pod. For more information on annotations, see the `annotations section <https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/>`_ in the Kubernetes documentation.

  .. py:attribute:: labels
    :type: map[string]string

    A map of labels that will be added to the pipeline step pod. For more information on labels, see the `labels section <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`_ in the Kubernetes documentation.

    .. note::

      The ``iceci/*`` prefix is used internally by IceCI for both annotations and labels and shouldn't be used in runtime profiles. Any annotations or labels in the runtime profile spec starting with this prefix will be ignored and won't be added to the pod.

  .. py:attribute:: resources
    :type: Object

    A set of resource requirements that will be added to the pipeline step pod. This field follows the structure of the Kubernetes native resource requirements. For more information on resource requirements, see the `managing resources for containers section <https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/>`_ in the Kubernetes documentation.



