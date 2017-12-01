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

There are two [Persistent Volume Claim](https://docs.openshift.com/container-platform/3.7/dev_guide/persistent_volumes.html) strategies:

* **unique** - each workspace gets own PVC.  This is the default strategy. When a workspace is deleted, associated PVC is deleted as well. However, an associated PV may not be recycled and tghus cannot be re-used for a new claim. See [k8s docs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#recycling). If you have started your cluster with `oc cluster up`, you need to create a service account for a special recycler pod: `oc create serviceaccount pv-recycler-controller -n openshift-infra` (requires admin login)
* **common** - there is one PVC for all workaspaces. When a PVC is shared with all workspaces, there's a special service pod that starts before workspace is created to create a subpath in the PV for this particular ws. When a workspace is deleted an associated subpath is deleted as well.

If a common strategy is set, it is impossible to run multiple workspaces simultaneously because of a default `ReadWriteOnce` [access mode](https://docs.openshift.com/container-platform/3.7/architecture/additional_concepts/storage.html#pv-access-modes) for workspace PVCs.

OpenShift cluster admins may want to study docs on [distributed volumes](https://docs.openshift.com/container-platform/3.7/install_config/persistent_storage/index.html) and [applying resource limits](https://docs.openshift.com/container-platform/3.7/admin_guide/quota.html).


## HTTPS Mode

To enable https for server and workspace routes, export the following environment variable:

`ENABLE_SSL=true`

When deploying to OSIO, HTTPS is enabled by default.

## Scalability

To be able to run more workspaces, [add more nodes to your OpenShift cluster](https://docs.openshift.com/container-platform/3.7/admin_guide/manage_nodes.html). If the system is out of resources, workspace start will fail with an error message returned from OpenShift (usually it's `no available nodes` kind of error).

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

## Multi-User: Using Own Keycloak and PSQL

Out of the box Che is deployed together with Keycloak and Postgres pods, and all three services are properly configured to be able to communicate. However, it does not matter for Che what Keycloak server and Postgres DB to use, as long as those have compatible versions and meet certain requirements.


***Che Server and Keycloak***

KeyCloak server URL is retrieved from the `CHE_KEYCLOAK_AUTH-SERVER-URL` environment variable. A new installation of Che will use its own Keycloak server running in a Docker container pre-configured to communicate with Che server. Realm and client are mandatory environment variables. By default Keycloak environment variables are:

```
CHE_KEYCLOAK_AUTH__SERVER__URL=http://${KC_ROUTE}:5050/auth
CHE_KEYCLOAK_REALM=che
CHE_KEYCLOAK_CLIENT__ID=che-public
```

You can use your own Keycloak server. Create a new realm and a public client. A few things to keep in mind:

* It must be a public client
* `redirectUris` should be `${CHE_SERVER_ROUTE/*`. If no or incorrect `redirectUris` are provided or the one used is not in the list of `redirectUris`, Keycloak will display an error saying that redirect_uri param is invalid.
* `webOrigins` should be  either`${CHE_SERVER_ROUTE}` or `*`. If no or incorrect `webOrigins` are provided, Keycloak script won't be injected into a page because of CORS error.


***Che Server and PostgreSQL***

Che server uses the below defaults to connect to PostgreSQL to store info related to users, user preferences and workspaces:

```
CHE_JDBC_USERNAME=pgche
CHE_JDBC_PASSWORD=pgchepassword
CHE_JDBC_DATABASE=dbche
CHE_JDBC_URL=jdbc:postgresql://postgres:5432/dbche
CHE_JDBC_DRIVER__CLASS__NAME=org.postgresql.Driver
CHE_JDBC_MAX__TOTAL=20
CHE_JDBC_MAX__IDLE=10
CHE_JDBC_MAX__WAIT__MILLIS=-1
```

Che currently uses version 9.6.


***Keycloak and PostgreSQL***

Database URL, port, database name, user and password are defined as environment variables in Keycloak pod. Defaults are:

```
POSTGRES_PORT_5432_TCP_ADDR=postgres
POSTGRES_PORT_5432_TCP_PORT=5432
POSTGRES_DATABASE=keycloak
POSTGRES_USER=keycloak
POSTGRES_PASSWORD=keycloak
```
{% include links.html %}
