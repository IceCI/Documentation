.. _interpolation-desc:

Interpolation
*************

Overview
++++++++

Interpolation syntax was introduced in ``IceCI`` to allow pipelines to use the pipeline context for some of the operations. The interpolation works only on selected fields in the pipeline. We do not support any templating of the yaml file itself.

Currently the fields supporting interpolations are:

  - :attr:`ContainerBuild<ContainerBuild>`:

    - ``tags``

  - :attr:`ServiceRun<ServiceRun>`:

    - ``image``

In fields supported by interpolation syntax, the part inside ``{{`` and ``}}`` is interpreted and treated as a name of the field. During the pipeline run it'll be replaced with the value of the specified field. The rest of the string will not change. Thanks to that you can embed pipeline context into part of the string.

Here are examples of using interpolation to create a Docker tag containing the name of the build and a service created from a Docker image with that build. Notice that you can use the interpolation variable along with a plain string. For example, in the container build step, if the build number was ``42``, the first pushed tag would be ``build-42`` - the other one will be ``latest``.


.. code-block:: yaml

  steps:
  - name: pipeline-context
    containerBuild:
      dockerSecret: dockerhub
      user: iceci
      imageName: my-app
      tags:
      - "build-{{ ICE_BUILD_NUMBER }}"
      - latest
  - name: pipeline-service
    serviceRun:
      name: example-service
      image: "myapp:build-{{ ICE_BUILD_NUMBER }}"
      dockerSecret: dockerhub



Available variables
+++++++++++++++++++

.. note::

  Although this list is similar to the list of :doc:`environment variables <environment>`, these are actually two independent sets of variables - they just happen to share some values.

.. envvar:: ICE_BUILD_NUMBER

  Subsequent number of a build in a repository. This value is unique only in the context of a given repository.

  Example value: ``12``

.. envvar:: ICE_GIT_EVENT_TYPE

  Git event type. Currently only ``commit`` is supported.

  Example value: ``commit``

.. envvar:: ICE_GIT_BRANCH_NAME

  Name of the branch on which the event happened.

  Example value: ``master``

.. envvar:: ICE_GIT_TAG

  Git tag name. This environment value is set only if ``ICE_GIT_EVENT_TYPE`` is set to ``tag``.

  Example value: ``0.1.0``

.. envvar:: ICE_GIT_COMMIT_SHA

  SHA of Git commit.

  Example value: ``93126518fa6eec3447d1d57c503aeebfd84f23ec``

.. envvar:: ICE_GIT_AUTHOR_NAME

  Name of the event author.

  Example value: ``iceci``

.. envvar:: ICE_GIT_AUTHOR_EMAIL

  Email of the event author.

  Example value: ``iceci@iceci.io``

.. envvar:: ICE_GIT_AUTHOR_DATE

  Date of the event.

  Example value: ``Wed, 5 Feb 2020 01:24:15 +0100``

.. envvar:: ICE_GIT_LOG_HEADER

  Git log header encoded in *base64*.

  Example value: ``VXBkYXRlICdSRUFETUUubWQnCg==``

.. envvar:: ICE_GIT_LOG_MESSAGE

  Git log body (without the header) encoded in *base64*.

  Example value: ``VXBkYXRlICdSRUFETUUubWQnCg==``

.. envvar:: ICE_GIT_TAG_OR_BRANCH

  The name of the current git branch or tag - the value depends on which type of event triggered the build.

  Example value: ``master``
