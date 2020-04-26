.. default-domain::py

Pipeline structure
##################


.. _pipeline-root-reference:

Pipeline root object
--------------------

The root of pipeline yaml file consists of the following fields.

.. py:attribute:: steps
  :type: list(Step)

  List of :attr:`Step` objects.

  For more information and examples on how steps work see :ref:`steps section<steps-desc>`.

.. py:attribute:: services
  :type: list(Service)

  List of :attr:`Service` objects. Services will be created all at once, at the beginning of pipeline.

  For more information and examples on how services work see :ref:`services section<services-desc>`.

.. py:attribute:: failureHandlers
  :type: list(FailureHandler)

  List of :attr:`FailureHandler` objects.

  List of all failure handler objects available in the whole pipeline. They can be referenced by both :attr:`Step` or other global failure handlers.

  For more information and examples on how failure handlers work see :ref:`failure handlers<failure-handlers-desc>` section.

.. py:attribute:: globals
  :type: object

  Globals object contains settings that will be passed down to the relevant objects in the pipeline.

  For more information and examples see :ref:`globals section<globals-desc>`.

  .. py:data:: dockerSecret
    :type: string

    Name of the docker secret used for communicating with docker registry.

    This value will be passed to the following objects:

    * :attr:`container run step<ContainerRun>`
    * :attr:`container build step<ContainerBuild>`
    * :attr:`failure handler<FailureHandler>`
    * :attr:`service<Service>`


  .. py:attribute:: onFailure
    :type: list(FailureHandlerReference)

    List of global failure handlers. Those failure handlers will be run after every failed :attr:`step<Step>` in the pipeline, no matter what type it was.

  .. py:attribute:: environment
    :type: list(EnvironmentVariable)

    List of :attr:`environment variables<EnvironmentVariable>`.

    Those environment variables will be passed to every :attr:`Container run step<ContainerRun>` in the pipeline.

    .. important::
      ``Failure handlers`` have access to all the environment variables of a given ``step`` injected into their spec, so they're available in the failure handler as well.

  .. py:attribute:: files
    :type: list(File)

    List of files that will be mounted in every :attr:`Container run step<ContainerRun>` in the pipeline.

    .. important::
      Like environment variables, files from a given ``step`` are also mounted into ``Failure handlers``.



Objects and types
-----------------

Definitions of all objects and types used in the pipeline definition.



.. _step-reference:

.. py:attribute:: Step
  :type: Object

  Step is an object in the pipeline representing a single execution unit.

  For more information and examples of how steps work see :ref:`steps section<steps-desc>`.

  .. py:data:: name

    Name of the step. This name will be displayed in the UI.

  .. py:attribute:: onFailure
    :type: list(FailureHandlerReference)

    List of :attr:`FailureHandlerReference` objects. All of failure handlers will be executed in the order declared in this list. If global failure handlers are also defined, they will be run after those specified here.

  .. py:attribute:: containerRun
    :type: ContainerRun

    Container run step is used for running various commands in a *container*.

    For full reference see :attr:`ContainerRun`.

  .. py:attribute:: containerBuild
    :type: ContainerBuild

    Container build step is used to build a Docker image and publish it to a specified Docker registry.

    For full reference see :attr:`ContainerBuild`.

  .. important::
    A step can be either a ``containerRun`` or ``containerBuild`` type - never both. If more than one field will be set, the pipeline will fail during validation.



.. _docker-run-reference:

.. py:attribute:: ContainerRun
  :type: Object

  Container run executes the :data:`script` inside a *Docker container* running a Docker :data:`image`. If any of the commands exit with code other than ``0``, the step will fail.

  .. py:data:: image
    :type: string

    Docker image used to run your commands.

  .. py:data:: dockerSecret
    :type: string
    :value: ""

    Name of the Docker secret used for communicating with Docker registry. Used for pulling image from private registries.

  .. py:data:: script
    :type: string

    A string containing the script that will be executed. The shell is run with ``set -e`` so this script will fail if any of the commands exits with a code other than ``0``.  If empty, the default ``command`` from the *Docker image* will be executed.

  .. py:data:: environment
    :type: list(EnvironmentVariable)

    List of environment variables passed to the container.

  .. py:data:: files
    :type: list(File)

    List of files that will be mounted in the container.



.. container-build-reference:

.. py:attribute:: ContainerBuild
  :type: Object

  Build the *Docker image* and pushes it to registry.

  .. py:data:: registry
    :type: string
    :value: docker.io

    Address of the Docker registry used to store images.

  .. py:data:: user
    :type: string

    User used to login to the Docker registry.

  .. py:data:: imageName
    :type: string

    Name of the image that will be built. This name is not the full image name, but only the name of a given image without a :data:`tag`, :data:`user` and :data:`registry`.

  .. py:data:: tag
    :type: string

    Tag of a Docker image that will be build.

  .. py:data:: contextPath
    :type: string
    :value: .

    Path that will be used as **context** in the Docker build process. See **docker** help command for more information.

    .. important::
      The context will be set to **.**, but the *current working directory* is set to the *workspace* folder, so it will behave like the Docker command is run inside the source directory.

  .. py:data:: dockerfilePath
    :type: string
    :value: Dockerfile

    Path to the **Dockerfile** file.

  .. py:data:: dockerSecret
    :type: string

    Name of the Docker secret used for communicating with Docker registry. Used for pulling image from private registries.



.. _service-reference:

.. py:attribute:: Service
  :type: Object

  Service runs an application specified by :data:`script` inside a Docker :data:`image`.

  .. py:data:: name
    :type: string

    Name of the service that will be used in the UI and also for communicating with the service. This name will be added to /etc/hosts of every :attr:`step<Step>` and :attr:`failure handler<FailureHandler>` in the pipeline, so the service can be resolved using the name.

  .. py:data:: image
    :type: string

    Docker image used to run the service.

  .. py:data:: dockerSecret
    :type: string

    Name of the Docker secret used for communicating with Docker registry. Used for pulling image from private registries.

  .. py:data:: script
    :type: string
    :value: ""

    A script or command that will be executed. If empty, the default ``command`` from the *Docker image* will be executed.

  .. py:data:: environment
    :type: list(EnvironmentVariable)

    List of environment variables passed to the container.


.. _failure-handler-reference:

.. py:attribute:: FailureHandler
  :type: Object

  Failure handler is an object representing a special kind of pipeline execution unit for handling errors in pipeline **steps**. Its definition is very similar to the **container run** **step**.

  .. py:data:: name
    :type: string

    Name of the failure handler. This name will be used by **FailureHandlerReference**

  .. py:data:: image
    :type: string

    Docker image used to run the failure handler.

  .. py:data:: dockerSecret
    :type: string

    Name of the Docker secret used for communicating with Docker registry. Used for pulling image from private registries.

  .. py:data:: script
    :type: string

    A script or command that will be executed. If empty, the default ``command`` from the *Docker image* will be executed.

  .. py:data:: environment
    :type: list(EnvironmentVariable)

    List of environment variables passed to the container.

  .. py:data:: files
    :type: list(File)

    List of files that will be mounted in the container.


.. _failure-handler-reference-reference:

.. py:attribute:: FailureHandlerReference
  :type: Object

  Failure handler reference is an object representing reference to a defined failure handler.

  .. py:attribute:: handlerName

    Name of the :attr:`FailureHandler` object to run. The failure handler must be defined in the :attr:`failureHandlers` list - else the pipeline will fail during validation.



.. _environment-variable-reference:

.. py:attribute:: EnvironmentVariable
  :type: Object

  The environment variable object represents an environment variable within a container. You can provide a value inline through :attr:`value` or by referencing a secret by its name using :attr:`fromSecret` field.

  .. py:attribute:: name

    The name of the environment variable that will be passed to the container.

  .. py:attribute:: value

    Value for a given environment variable.

  .. py:attribute:: fromSecret

    Name of a secret from which the value should be retrieved to inject to the container as an environment variable.

  .. note::
    Currently ``IceCI`` supports creating environment variables by explicitly entering their values in the pipeline yaml or by providing a secret name from which the value should be taken. Those options are exclusive for a given variable - you can't have both :attr:`value` and :attr:`fromSecret` set at the same time - the pipeline validation will fail.



.. _file-reference:

.. py:attribute:: File
  :type: Object

  The file object represents a file that'll be mounted in a container from a secret. Unlike environment variables, file values cannot be provided inline - they have to reference a secret.

  .. py:attribute:: path

    The absolute path that the file will be mounted in.

  .. py:attribute:: fromSecret

    Name of a secret from which the value should be retrieved to mount into the container as a file.
