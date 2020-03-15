Overview
########

IceCI pipelines are defined in ``.iceci.yaml`` file stored in root of your Git repository.

Keep in mind - after adding a repository containing a pipeline definition to IceCI, the system  **won't** build anything until a Git event occurs.

The repository is scanned for new events every minute - when a new event is recorded, a build is triggered. The build is performed based on the contents of the pipeline definition file.

.. attention::
  Currently the only supported Git events are commits. Support for tags and pull requests will be added in future versions.
