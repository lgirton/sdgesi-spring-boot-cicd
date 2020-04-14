# SDGESI Continuous Integration and Delivery

This repository contains templates and scripts for the creation and management of SDGESI CI/CD pipeline
and deployments.

## Pipeline Template

The [sdgesi-boot-pipeline-template.yaml](templates/sdgesi-boot-pipeline-template.yaml) is a template that will
help bootstrap the creation of a Jenkins Pipeline in Openshift.

The template will create three resources in Openshift:

1. Pipeline BuildConfig
1. Image BuildConfig
1. ImageStream

The Pipeline BuildConfig will instantiate a Jenkins Pipeline build that will reference a provided Jenkins which will ultimately build an ImageStream using the Image BuildConfig.

The reference [Jenkinsfile](Jenkinsfile) is part fo this repository and can be view [here](Jenkinsfile).

The template favors `Convention over Configuration` and so in most cases you will only need to override two of the parameters: NAME (required) and SOURCE_REPO_URL.

E.g.
```
oc process -f sdgesi-boot-pipeline-template.yaml -p NAME=sdgesi-spring-boot-fuse -p SOURCE_REPO_URL=https://aplsiscmp001.sempra.com/lgirton/sdgesi-spring-boot-fuse.git | oc create -f - -n build
```
Output:
```
imagestream.image.openshift.io/sdgesi-spring-boot-fuse created
buildconfig.build.openshift.io/sdgesi-spring-boot-fuse created
buildconfig.build.openshift.io/sdgesi-spring-boot-fuse-pipeline created
```

The template can also be installed in the Openshift namespace where the pipelines will be managed so that it's visible in the OpenShift Service Catalog:

```
oc create -f templates/sdgesi-boot-pipeline-template.yaml -n build
```

### Parameters
As mentioned earlier, the template favors `Convention` over `Configuration` and so there are sensible defaults for most of the parameters and in most cases you will only need to provide the NAME and SOURCE_REPO_URL parameters to the template.

To view all of the parameters of the template:

```sh
oc process -f templates/sdgesi-boot-pipeline-template.yaml --parameters
```

## Deployment Template


