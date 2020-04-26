Environment
###########

IceCI generates additional metadata for every build and passes this data to the relevant objects in the pipeline.

.. note::
  In the UI those environment variables won't be visible in neither :attr:`step<Step>` nor :attr:`failure handler<FailureHandler>` details. They are injected during execution - the UI only shows variables defined in the pipeline.



Steps
-----

Environment variables specified here are injected into every :attr:`step<Step>` and :attr:`failure handler<FailureHandler>` in the pipeline.


.. envvar:: ICE_BUILD_NUMBER

  Subsequent number of a build in a repository. This value is unique only in the context of a given repository.

  Example value: ``12``


.. envvar:: ICE_STEP_NAME

  Name of the step that's currently executing

  Example value: ``run-tests``


.. envvar:: ICE_SERVICE_XXX

  IP address of the **XXX** service. This variable is created for every service defined in the pipeline spec.

  Example value: ``10.0.0.1``


.. envvar:: ICE_GIT_EVENT_TYPE

  Git event type. Currently only ``commit`` is supported.

  Example value: ``commit``


.. envvar:: ICE_GIT_COMMIT_SHA

  SHA of Git commit.

  Example value: ``93126518fa6eec3447d1d57c503aeebfd84f23ec``


.. envvar:: ICE_GIT_BRANCH_NAME

  Name of the branch on which the event happened.

  Example value: ``master``


.. envvar:: ICE_GIT_TAG

  Git tag name. This environment value is set only if ``ICE_GIT_EVENT_TYPE`` is set to ``tag``.

  Example value: ``0.1.0``


.. envvar:: ICE_GIT_LOG_HEADER

  Git log header encoded in *base64*.

  Example value: ``VXBkYXRlICdSRUFETUUubWQnCg==``


.. envvar:: ICE_GIT_LOG_MESSAGE

  Git log body (without the header) encoded in *base64*.

  Example value: ``VXBkYXRlICdSRUFETUUubWQnCg==``


.. envvar:: ICE_GIT_AUTHOR_NAME

  Name of the event author.

  Example value: ``iceci``


.. envvar:: ICE_GIT_AUTHOR_EMAIL

  Email of the event author.

  Example value: ``iceci@iceci.io``


.. envvar:: ICE_GIT_AUTHOR_DATE

  Date of the event.

  Example value: ``Wed, 5 Feb 2020 01:24:15 +0100``



Failure handler
---------------

Environment variables specified here are injected into every :attr:`failure handler<FailureHandler>` in the pipeline.

.. envvar:: ICE_FAILED_STEP_NAME

  Name of the failed step.

  Example Value: ``run-tests``

.. important::
  Failure handlers also have all of the failed step environment variables injected - this includes secrets.

