---
title: "Single-User&#58 Deploy to OpenShift"
keywords: openshift, installation
tags: [installation, openshift]
sidebar: user_sidebar
permalink: openshift.html
folder: setup
---
## Supported OpenShift Flavors and Versions

Che can be deployed to MiniShift, OCP, OSD and OSO v3.5+.

## Pre-Reqs

* [jq](https://stedolan.github.io/jq/) unility
* `bash`

Scripts will use default settings for things like Che project name, http/https protocol, log level etc. See: [OpenShift config][openshift-config]

## MiniShift

Download and run deployment scripts:

```shell
DEPLOY_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/deploy_che.sh
WAIT_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/wait_until_che_is_available.sh
STACKS_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/replace_stacks.sh
curl -fsSL ${DEPLOY_SCRIPT_URL} -o ./get-che.sh
curl -fsSL ${WAIT_SCRIPT_URL} -o ./wait-che.sh
curl -fsSL ${STACKS_SCRIPT_URL} -o ./stacks-che.sh
bash ./get-che.sh && bash ./wait-che.sh && bash ./stacks-che.sh
```


## OpenShift Container Platform

* Use environment variables to set [deployment options][openshift-config]:

```shell
export OPENSHIFT_ENDPOINT=<OCP_ENDPOINT_URL> # e.g. https://opnshmdnsy3t7twsh.centralus.cloudapp.azure.com:8443
export OPENSHIFT_TOKEN=<OCP_TOKEN> # it depends on authentication scheme for your OCP cluster - it can also be OPENSHIFT_USERNAME and OPENSHIFT_PASSWORD instead
export OPENSHIFT_NAMESPACE_URL=<CHE_HOSTNAME> # e.g. che-eclipse-che.52.173.199.80.xip.io
export OPENSHIFT_FLAVOR=ocp
```

* Download and run deployment scripts:

```shell
DEPLOY_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/deploy_che.sh
WAIT_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/wait_until_che_is_available.sh
STACKS_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/replace_stacks.sh
curl -fsSL ${DEPLOY_SCRIPT_URL} -o ./get-che.sh
curl -fsSL ${WAIT_SCRIPT_URL} -o ./wait-che.sh
curl -fsSL ${STACKS_SCRIPT_URL} -o ./stacks-che.sh
bash ./get-che.sh && bash ./wait-che.sh && bash ./stacks-che.sh
```

## Openshift.io

Please note that deploying Che on [OpenShift.io](openshift.io) (OSIO) is only intended for Che developers and OSIO contributors. This is useful when a developer wants to test how changes will work on [OpenShift.io](openshift.io). If you are interested in using OpenShift.io you can [sign up](https://openshift.io/) for the service.

Use environment variables to set [deployment options][openshift-config]:

```shell
export OPENSHIFT_TOKEN=<OSO_TOKEN> # Retrieve the OSO_TOKEN from https://console.starter-us-east-2.openshift.com/console/command-line
export OPENSHIFT_FLAVOR=osio
```

Download and run deployment scripts:

```shell
DEPLOY_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/deploy_che.sh
WAIT_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/wait_until_che_is_available.sh
STACKS_SCRIPT_URL=https://raw.githubusercontent.com/eclipse/che/master/dockerfiles/init/modules/openshift/files/scripts/replace_stacks.sh
curl -fsSL ${DEPLOY_SCRIPT_URL} -o ./get-che.sh
curl -fsSL ${WAIT_SCRIPT_URL} -o ./wait-che.sh
curl -fsSL ${STACKS_SCRIPT_URL} -o ./stacks-che.sh
bash ./get-che.sh && bash ./wait-che.sh && bash ./stacks-che.sh
```

## Deployment Options and Configuration

See: [OpenShift Deployment Config][openshift-config]

{% include links.html %}
