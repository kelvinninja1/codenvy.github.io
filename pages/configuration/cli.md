---
title: "CLI Reference"
keywords: docker, configuration, CLI, cli
tags: [installation, docker]
sidebar: user_sidebar
permalink: cli-reference.html
folder: configuration
---

## CLI Syntax and Commands

The CLI is a Docker image that comes with a collection of commands to configure, interact and start Che. The CLI also contains commands for end users to interact with workspaces such as sync and ssh.

```
USAGE:
  docker run -it --rm <DOCKER_PARAMETERS> eclipse/che-cli:<version> [COMMAND]

MANDATORY DOCKER PARAMETERS:
  -v <LOCAL_PATH>:/data                Where user, instance, and log data saved

OPTIONAL DOCKER PARAMETERS:
  -e CHE_HOST=<YOUR_HOST>              IP address or hostname where che will serve its users
  -e CHE_PORT=<YOUR_PORT>              Port where che will bind itself to
  -e CHE_CONTAINER=<YOUR_NAME>         Prefix name for the che container
  -v <LOCAL_PATH>:/data/instance       Where instance, user, log data will be saved
  -v <LOCAL_PATH>:/data/backup         Where backup files will be saved
  -v <LOCAL_PATH>:/repo                che git repo - uses local binaries and manifests
  -v <LOCAL_PATH>:/assembly            che assembly - uses local binaries
  -v <LOCAL_PATH>:/sync                Where remote ws files will be copied with sync command
  -v <LOCAL_PATH>:/unison              Where unison profile for optimizing sync command resides
  -v <LOCAL_PATH>:/chedir              Soure repository to convert into workspace with Chedir utility

COMMANDS:
  action <action-name>                 Start action on che instance
  backup                               Backups che configuration and data to /data/backup volume mount
  config                               Generates a che config from vars; run on any start / restart
  destroy                              Stops services, and deletes che instance data
  dir <command>                        Use Chedir and Chefile in the directory mounted to :/chedir
  download                             Pulls Docker images for the current che version
  help                                 This message
  info                                 Displays info about che and the CLI
  init                                 Initializes a directory with a che install
  offline                              Saves che Docker images into TAR files for offline install
  restart                              Restart che services
  restore                              Restores che configuration and data from /data/backup mount
  rmi                                  Removes the Docker images for <version>, forcing a repull
  ssh <wksp-name> [machine-name]       SSH to a workspace if SSH agent enabled
  start                                Starts che services
  stop                                 Stops che services
  sync <wksp-name>                     Synchronize workspace with local directory mounted to :/sync
  test <test-name>                     Start test on che instance
  upgrade                              Upgrades che from one version to another with migrations and backups
  version                              Installed version and upgrade paths

GLOBAL COMMAND OPTIONS:
  --fast                               Skips networking, version, nightly and preflight checks
  --offline                            Runs CLI in offline mode, loading images from disk
  --debug                              Enable debugging of che server
  --trace                              Activates trace output for debugging CLI
```

The CLI has three primary phases: initialization, configuration, and start. The initialization phase is executed by `init` and will install version-specific files into the folder mounted to `/data`. This includes the universal configuration file named `che.env`, a version identifier, and a location where configuration files will be saved. The configuration is executed by the `config` command and takes as input your `che.env` configuration file, the OS of your host, and then generates an OS-specific set of configuration files in the `/data/instance` folder that can be used to run an instance of Che. The configuration phase will run an initialization if a folder is not found. Every execution of the `config` command will overwrite the files in `/data/instance` with the latest configuration. This way if an admin modifies any configuration file, the instance's configuration files will be updated to be guaranteed consistent. The CLI generates a large number of configuration files specific to running Che. The configuration files are sourced from Puppet templates that are stored in our GitHub repository under `/dockerfiles/init`. The start phase is executed by `start` and will use a configuration-generated `docker-compose-container.yml` file to launch Eclipse Che. The start phase always executes a `config` command, so any files that were edited in `/data/instance` will be overwritten with the generated configuration from the CLI.

The CLI will hide most error conditions from standard out. Internal stack traces and error output is redirected to `cli.log`, which is saved in the host folder where `:/data` is mounted.

You can override any value in `che.env` for a single execution by passing in `-e NAME=VALUE` on the command line. The CLI will detect the values on the command line and ignore those imported from `che.env`.

-----

*action*

Executes some actions on the Eclipse Che instance or on a workspace running inside Che.
For example to list all workspaces on Che, the following command can be used `action list-workspaces`.
To execute a command on a workspace `action execute-command <workspace-name> <action>` where action can be any bash command.

-----

*backup*

TARS your `/instance` into files and places them into `/backup`. These files are restoration-ready.

-----

*config*

Generates a Che instance configuration thta is placed in `/instance`. This command uses puppet to generate Docker Compose configuration files to run Che and its associated server. Che's server configuration is generated as a che.properties file that is volume mounted into the Che server when it boots. This command is executed on every `start` or `restart`.

If you are using a `eclipse/che:<version>` image and it does not match the version that is in `/instance/che.ver`, then the configuration will abort to prevent you from running a configuration for a different version than what is currently installed.

This command respects `--no-force`, `--pull`, `--force`, and `--offline`.

-----

*destroy*

Deletes `/docs`, `che.env` and `/instance`, including destroying all user workspaces, projects, data, and user database. If you pass `--quiet` then the confirmation warning will be skipped. Passing `--cli` will also destroy the `cli.log`. By default this is left behind for traceability.

-----

*dir*

Boots a new Eclipse Che instance with a workspace for the folder `:/chedir` defined as volume mount in parameter.

If for example `$HOME/my-project` is given as parameter, a new Che instance will be created, using `$HOME/my-project` as project in the IDE.

So inside the IDE, `/projects` folder will contain a `my-project` folder with your host folder.
Then, any changes inside the IDE will be reflected in your host folder. And the opposite is also true, updating a file on your local computer will update the content of the file inside the IDE.

Other commands are `init`,`up`, `down`, `ssh` and `status`

  - `init`    : Initialize the directory specified and add a default `Chefile` file if there is none
  - `up`      : Boot Eclipse Che with workspace on folder
  - `down`    : Stop Eclipse Che and any workspaces
  - `ssh`     : Connect to the running workspace by using ssh
  - `status`  : Display if an instance of Eclipse Che is running or not for the specified folder.

-----

*download*

Used to download Docker images that will be stored in your Docker images repository. This command downloads images that are used by the CLI as utilities, for Che to do initialization and configuration, and for the runtime images that Che needs when it starts.  This command respects `--offline`, `--pull`, `--force`, and `--no-force` (default).  This command is invoked by `che init`, `che config`, and `che start`.

`download` is invoked by `che init` before initialization to download images for the version specified by `eclipse/che:<version>`.

It is possible to override the docker images used by the CLI by setting the following
environment variables:

- `IMAGE_INIT` to override the default `eclipse/che-init:<version>` docker image,
- `IMAGE_CHE` to override the default `eclipse/che-server:<version>` docker image.

For example if, for both images, you need to use to use a given tag in you own docker account, you can add the following parameters to the docker command:

```
-e IMAGE_INIT=myDockerAccount/che-init:givenTag -e IMAGE_CHE=myDockerAccount/che-server:givenTag
```

-----

*info*

Displays system state and debugging information. `--network` runs a test to take your `CHE_HOST` value to test for networking connectivity simulating browser > Che and Che > workspace connectivity. `--bundle` will generate a support diagnostic bundle in a TAR file which includes the output of certain commands and your execution logs.

-----

*init*

Initializes an empty directory with a Che configuration and instance folder where user data and runtime configuration will be stored. You must provide a `<path>:/data` volume mount, then Che creates a `instance` and `backup` subfolder of `<path>`. You can optionally override the location of `instance` by volume mounting an additional local folder to `/data/instance`. You can optionally override the location of where backups are stored by volume mounting an additional local folder to `/data/backup`.  After initialization, a `che.env` file is placed into the root of the path that you mounted to `/data`.

These variables can be set in your local environment shell before running and they will be respected during initialization:

| Variable | Description |
|----------|-------------|
| `CHE_HOST` | The IP address or DNS name of the Che service. We use `eclipse/che-ip` to attempt discovery if not set. |
| `CHE_PORT` | The port the Che server will run on and expose in its container for your clients to connect to. |

Che depends upon Docker images. We use Docker images to:

1. Provide cross-platform utilities within the CLI. For example, in scenarios where we need to perform a `curl` operation, we use a small Docker image to perform this function. We do this as a precaution as many operating systems (like Windows) do not have curl installed.
2. Look up the master version and upgrade manifest, which is saved within the CLI docker image in the /version subfolder.
3. Perform initialization and configuration of Che such as with `eclipse/che-init`. This image contains templates to be installed onto your computer used by the CLI to configure Che for your specific OS.

You can control how Che downloads these images with command line options. All image downloads are performed with `docker pull`.

| Mode >>>>>>>>>> | Description |
|------|-------------|
| `--no-force` | Default behavior. Will download an image if not found locally. A local check of the image will see if an image of a matching name is in your local registry and then skip the pull if it is found. This mode does not check DockerHub for a newer version of the same image. |
| `--pull` | Will always perform a `docker pull` when an image is requested. If there is a newer version of the same tagged image at DockerHub, it will pull it, or use the one in local cache. This keeps your images up to date, but execution is slower. |
| `--force` | Performs a forced removal of the local image using `docker rmi` and then pulls it again (anew) from DockerHub. You can use this as a way to clean your local cache and ensure that all images are new. |
| `--offline` | Loads Docker images from `backup/*.tar` folder during a pre-boot mode of the CLI. Used if you are performing an installation or start while disconnected from the Internet. |

You can reinstall Che on a folder that is already initialized and preserve your `che.env` values by passing the `--reinit` flag.

-----

*offline*

Saves all of the Docker images that Che requires into `/backup/*.tar` files. Each image is saved as its own file. If the `backup` folder is available on a machine that is disconnected from the Internet and you start Che with `--offline`, the CLI pre-boot sequence will load all of the Docker images in the `/backup/` folder.

`--list` option will list all of the core images and optional stack images that can be downloaded. The core system images and the CLI will always be saved, if an existing TAR file is not found. `--image:<image-name>` will download a single stack image and can be used multiple times on the command line. You can use `--all-stacks` or `--no-stacks` to download all or none of the optional stack images.

-----

*restart*

Performs a `stop` followed by a `start`, respecting `--pull`, `--force`, `--offline`, `--skip:config`, `--skip:preflight`, and `--skip:postflight`.

-----

*restore*

Restores `/instance` to its previous state. You do not need to worry about having the right Docker images. The normal start / stop / restart cycle ensures that the proper Docker images are available or downloaded, if not found.

This command will destroy your existing `/instance` folder, so use with caution, or set these values to different folders when performing a restore.

-----

*rmi*

Deletes the Docker images from the local registry that Che has downloaded for this version.

-----

*ssh*

Connects the current terminal where the command is started to the terminal of a machine of the workspace. If no machine is specified in the command, it will connect to the default machine which is the dev machine.
The syntax is `ssh <workspace-name> [machine-name]`
The ssh connection will work only if there is a workspace ssh key setup. A default ssh key is automatically generated when a workspace is created.

-----

*sync*

Synchronizes a workspace's contents with a local folder mounted to :/sync
The syntax is `-v <path-on-your-machine>:/sync eclipse/che sync <workspace-name>`

To get extra information, the flag `--unison-verbose` can be used to display log of the underlying unison tool.

-----

*start*

Starts Che and its services using `docker-compose`. If the system cannot find a valid configuration it will perform an `init`. Every `start` and `restart` will run a `config` to generate a new configuration set using the latest configuration. The starting sequence will perform pre-flight testing to see if any ports required by Che are currently used by other services and post-flight checks to verify access to key APIs.

You can skip pre-flight and post-flight checks with `--skip:preflight` and `--skip:postflight` respectively. The typical Che start sequence includes an invocation of the `config` method, which regenerates configuration files placed into the `/instance` folder. You can skip this generation with `--skip:config`.  You can automatically print out the server logs to the console during the booting of the server by appending `--follow`. This flag is blocking and requires you to CTRL-C to interrupt the output.

-----

*stop*

The default stop is a graceful stop where each workspace is stopped and confirmed shutdown before stopping system services. If workspaces are configured to snap on stop, then all snaps will be completed before system service shutdown begins. You can ignore workspace stop behavior and shut down only system services with --force flag.

-----

*test*

Performs some tests on your local instance of Che. It can for example check the ability to create a workspace, start the workspace by using a custom Workspace runtime and then use it.
The list of all the tests available can be obtained by providing only `test` command.

-----

*upgrade*

Manages the sequence of upgrading Che from one version to another. Run `che version` to get a list of available versions that you can upgrade to.

Upgrading Che is done by using a `eclipse/che:<version>` that is newer than the version you currently have installed. For example, if you have 5.0.0-M2 installed and want to upgrade to 5.0.0-M7, then:

```
# Get the new version of Che
docker pull eclipse/che:5.0.0-M7

# You now have two eclipse/che images (one for each version)
# Perform an upgrade - use the new image to upgrade old installation
docker run <volume-mounts> eclipse/che:5.0.0-M7 upgrade
```

The upgrade command has numerous checks to prevent you from upgrading Che if the new image and the old version are not compatiable. In order for the upgrade procedure to proceed, the CLI image must be newer than the value of '/instance/che.ver'.

The upgrade process: a) performs a version compatibility check, b) downloads new Docker images that are needed to run the new version of Che, c) stops Che if it is currently running triggering a maintenance window, d) backs up your installation, e) initializes the new version, and f) starts Che.

You can run `che version` to see the list of available versions that you can upgrade to.

`--skip-backup` option allow to skip [backup](https://github.com/codenvy/che-docs/blob/master/src/main/_docs/setup/setup-cli.md#backup) during update, that could be useful to speed up upgrade because [backup](https://github.com/codenvy/che-docs/blob/master/src/main/_docs/setup/setup-cli.md#backup) can be very expensive operation if `/instace` folder is really big due to many user worksapces and projects.

-----

*version*

Provides information on the current version and the available versions that are hosted in Che's repositories. `che upgrade` enforces upgrade sequences and will prevent you from upgrading one version to another version where data migrations cannot be guaranteed.

-----

## CLI Development
You can customize the CLI using a variety of techniques. This section discusses how engineers develop and test the CLI on their local machines.

## Structure
The Che CLI is constructed of multiple Docker images within the Che source repository.

```
/dockerfiles/base  # Common functions and commands
/dockerfiles/cli   # CLI entrypoint, overrides, and version information
/dockerfiles/init  # Manifests used to configure Che on a host installation
```

The Che CLI is authored in `bash`. The `cli` image depends upon both the `base` image and the `init` image. In the source repository, we have `build.sh` commands which will build these Docker images for you either one at a time or collectively as a group.

It can become tedious rebuilding images every time you want to test a small change to a bash script. You can avoid having to rebuild images each time for every change to a bash script by volume mounting the contents during the image execution. You cannot volume mount the `entrypoint.sh` file which is where each container has a launch point, but you can volume mount others:

```
# Volume mount the contents of the base image
-v <path-to-che-repo>/dockerfiles/scripts/base/scripts:/base/scripts

# Volume mount the contents of the init image
-v <path-to-che-repo>:/repo
```

If you run the Che CLI in this configuration, then any changes made to the bash files or templates in those repositories will be used without having to first rebuild the CLI image.

## Custom CLI

The Che CLI was designed to be overridden to allow different CLIs to be created from the same base structure. This is how Codenvy and ARTIK has an identical CLIs to Che. The CLI is created with a few minimal assets:

```
/dockerfiles/cli/build.sh               # Local file to build the image
/dockerfiles/cli/Dockerfile             # Image definition, must FROM eclipse/che-base:nightly
/dockerfiles/cli/scripts                # Contains additional commands in form of cmd_<name>.sh
/dockerfiles/cli/scripts/entrypoint.sh  # The entrypoint of the CLI container, with usage() method
/dockerfiles/cli/scripts/cli.sh         # Defines CLI-specific product names & variables
/dockerfiles/cli/version                # Contains version-specific data the CLI requires
```

You can add additional commands to the Che CLI beyond the base set of commands that are provided by adding a file of the name `cmd_<name>.sh` into the `scripts` folder. Codenvy is an [example that adds additional commands](https://github.com/codenvy/codenvy/tree/master/dockerfiles/cli/scripts).

The `version` folder has information that details the latest version and a sub-folder for each version that is available for installation. Each version subfolder has version-specific data that the CLI depends upon to create a manifest of Docker images that must be downloaded to support the product that is going to be run. When we generate a release of the Che CLI, we have our CI systems automatically update the `/version` folder with the version-specific information contained in a release.

## Puppet Templates

The Che CLI uses Puppet to generate OS-specific configuration files based upon environment variables set by the user either with `-e <VALUE>` options on the command line, or by modifying their `che.env` file. We pass all of these values into Puppet and then run a puppet configuration utility across the files contained in the `/dockerfiles/init/modules` and `/dockerfiles/init/manifests` folder to take the templates contained within the `/init` module, marry them with user-specific variables, and then generate an instance-specific configuration in `/instance`. Puppet has logic constructs that allow us to generate different kinds of constructs with logic based upon the values provided by end users.

This puppet-based approach allow us to simplify the outputs for end users and limit the locations where end users need to configure various parts of the system. One powerful example of this is that we generate two `docker-compose.yml` files from a single Puppet template. In the user's `/instance` folder is `docker-compose.yml` and `docker-compose-container.yml`. The first one is a configuration file that allows a user to run Docker compose for Che on their host. They can just `docker-compose up` in that folder. The second file is for running Docker compose from within a container, which is what the CLI does. The syntax of Docker compose changes in each of these scenarios as the files being referenced from within the compose syntax are different. In the `init` image, we have a single template for Docker Compose and then apply it in two configurations using Puppet.

## CLI Tests

There are existing [bats](https://github.com/sstephenson/bats) tests for the Che CLI, which runs automatically with each execution of a `build.sh` script located in the `dockerfiles/cli` folder. To skip them, pass `--skip-tests` argument when running the build script. If you want to just run tests you can achieve it by running script `test.sh` located in the same folder. Tests utilizes docker image `eclipse/che-bats` which is build from `Dockerfile` placed in `dockerfiles/bats`.

{% include links.html %}
