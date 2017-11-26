---
title: "Multi-User&#58 Deploy to OpenShift"
keywords: openshift, installation, ocp, multi-user, multi user, keycloak, postgres, s2i, deployment
tags: [installation, openshift]
sidebar: user_sidebar
permalink: multi-user-openshift.html
folder: setup
---

## Deployment diagram

Deployment of a multi-user Che is a bit more complicated as Keycloak and Postgres images need to be provisioned with the right configuration. On top of that, pods need to start in a particular order: **Postgres-Keycloak-Che**. To accomplish this task, the deployment script makes use of OpenShift S2I build strategy - BuildConfigs and ImageStreams are created first. S2I builds grab official Keycloak and Postgres images, copy necessary configuration files that come with `eclipse/che-init` image and save resulting images as ImageStreams that are then referenced in corresponding DeploymentConfigs. When builds succeed, the script continues with deployment.

{% include image.html file="ocp_multi_user.png" %}

## How to Get Scripts

There are two ways to get deployment scripts: either clone Che or run a Docker image:

```shell
git clone https://github.com/eclipse/che
cd che/dockerfiles/init/modules/openshift/files/scripts
```
or

```shell
docker run -ti -v /var/run/docker.sock:/var/run/docker.sock -v ${LOCAL_PATH}:/data eclipse/che init
cd ${LOCAL_PATH}/instance/config/openshift/scripts
```

## MiniShift

```
export CHE_MULTIUSER=true
./deploy_che.sh && ./wait_until_che_is_available.sh

```
## OpenShift Container platform

```
export CHE_MULTIUSER=true
export OPENSHIFT_ENDPOINT=<OCP_ENDPOINT_URL> # e.g. https://opnshmdnsy3t7twsh.centralus.cloudapp.azure.com:8443
export OPENSHIFT_TOKEN=<OCP_TOKEN> # it depends on authentication scheme for your OCP cluster - it can also be OPENSHIFT_USERNAME and OPENSHIFT_PASSWORD instead
export OPENSHIFT_NAMESPACE_URL=<CHE_HOSTNAME> # e.g. che-eclipse-che.52.173.199.80.xip.io
export OPENSHIFT_FLAVOR=ocp
./deploy_che.sh && ./wait_until_che_is_available.sh
```
In both cases, you can additionally run `replace_stacks.sh` that will replace Che upstream stacks with the ones currently used in OSIO flavored Che.


{% include links.html %}
