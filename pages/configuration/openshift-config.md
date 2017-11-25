---
title: "Configuration: OpenShift"
keywords: openshift, configuration
tags: [installation, openshift]
sidebar: user_sidebar
permalink: openshift-config.html
folder: configuration
---

You can configure deployment of Che on OpenShift with env variables that are then saved into a [ConfigMap](https://github.com/eclipse/che/blob/che6/dockerfiles/init/modules/openshift/files/scripts/che-openshift.yml#L50). Export envs before running the script.

## OpenShift Flavor

`OPENSHIFT_FLAVOR` defaults to `minishift`. Allowed values: `ocp`, `osio`

## Username, Password, Token

Deployment script needs access to a running OpenShift instance either via username/password or a token:

```
OPENSHIFT_USERNAME
OPENSHIFT_PASSWORD
OPENSHIFT_TOKEN
```
If token is set, it is used by `oc` client to log in, else default credentials are used (`developer/developer`) for MiniShift and OCP.


## Namespace for Workspaces

You can instruct Che server in what namespace(s) to create workspace pods. There are two scenarios. By default, every new workspace pod is created in a new namespace which is dictated by the following env:

`CHE_INFRA_OPENSHIFT_PROJECT=""`

You may have one common namespace for all workspace pods:

`CHE_INFRA_OPENSHIFT_PROJECT="che-workspaces"`

## Volumes

`CHE_INFRA_OPENSHIFT_PVC_STRATEGY="unique"`

There are two [Persistent Volume Claim](https://docs.openshift.com/container-platform/3.6/dev_guide/persistent_volumes.html) strategies:

* **unique** - each workspace gets own PVC. When a workspace is deleted, associated PVC is deleted as well. This is the default strategy.
* **common** - there is one PVC for all workaspaces. When a PVC is shared with all workspaces, there's a special service pod that starts before workspace is created to create a subpath in the PV for this particular ws. When a workspace is deleted an associated subpath is deleted as well.

If a common strategy is set, it is impossible to run multiple workspaces simultaneously because of a default `ReadWriteOnce` [access mode](https://docs.openshift.com/container-platform/3.6/architecture/additional_concepts/storage.html#pv-access-modes) for workspace PVCs.

## HTTPS Mode

To enable https for server and workspace routes, export the following environment variable:

`OC_SKIP_TLS=false`

When deploying to OSIO, HTTPS is enabled by default.

## Debug Mode

If you want Che server to run in a debug mode export the following env before running the script (false by default):

`CHE_DEBUG_SERVER=true`

## Image Pull Policy

If you want to always have the latest nightly image or use own latest tags when developing and testing Che

`IMAGE_PULL_POLICY`

## Enable ssh and sudo

By default, pods are run with an arbitrary user that has a randomly generated UID (the range is defined in OpenShift config file). This security constrain has several consequences for Eclipse Che users:

* installers for language servers will fails since most of them require `sudo`
* no way to run any sudo commands in a running workspace

It is possible to allow root access which in its turn allows running system services such as `sshd`. You can change this behavior. See [OpenShift Documentation for details](https://docs.openshift.com/container-platform/3.6/admin_guide/manage_scc.html#enable-images-to-run-with-user-in-the-dockerfile).

## Multi-User Configuration

See: [Multi User Che on OpenShift][multi-user-openshift]

{% include links.html %}
