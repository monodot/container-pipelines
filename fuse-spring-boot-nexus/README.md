# Sample pipeline for a Fuse application on OpenShift

Features of this pipeline:

- **Deploys the Fuse REST HTTP Booster** - this is a sample application for Red Hat Fuse
- **Pipeline is located separately from application code** - this means that the pipeline can be used with a number of applications of a similar type.
- **Uses Jenkins slave images:** Expects Jenkins agent images labelled `maven` and `ansible`.
  - i.e. an image stream with the label `role` set to `jenkins-slave`, or image stream tags which have the annotation `role` set to `jenkins-slave`.
- **Everything starts with the pipeline:** OpenShift Applier will first create a `BuildConfig` of type `JenkinsPipeline`. [If a Jenkins instance does not already exist in the given namespace, OpenShift will create one.][1] The [OpenShift Jenkins Sync Plug-in][syncplugin] will then automatically create a Build Job in Jenkins, using the details in the BuildConfig.
- **The pipeline creates the build and deployment objects:** The Jenkins pipeline uses [OpenShift Applier][applier] to create the BuildConfigs, DeploymentConfigs and other objects in OpenShift.
- The **[OpenShift Jenkins Pipeline (DSL) Plugin][clientplugin]** is used to create and manage objects inside OpenShift (this plugin should be used with OpenShift 3.7+)
- Does not use a Pipeline Library
- Does use Nexus as an artifact repository
- Does a binary build to produce a container image, and tags the image with the build number from Jenkins

## Getting started

Create the initial pipeline:

    oc process -f .openshift/templates/pipeline.yml \
      -p APPLICATION_NAME=basic-fuse-app \
      -p SOURCE_REPOSITORY_URL=https://github.com/monodot/container-pipelines \
      -p SOURCE_REPOSITORY_REF=nexus-spring-boot \
      | oc create -f -
    

[applier]: https://github.com/redhat-cop/openshift-applier
[1]: https://docs.openshift.com/container-platform/3.9/architecture/core_concepts/builds_and_image_streams.html#pipeline-build
[syncplugin]: https://github.com/openshift/jenkins-sync-plugin
[clientplugin]: https://github.com/openshift/jenkins-client-plugin
[resthttpbooster]: https://github.com/jboss-fuse/fuse-rest-http-booster
