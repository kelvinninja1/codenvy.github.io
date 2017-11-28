---
title: Developer Documentation Overview
sidebar: dev_sidebar
keywords: dev docs
permalink: developer.html
toc: false
folder: developer
---

Welcome to Eclipse Che developer documentation. If you are here, you are probably interested in customizing Che, and you are certainly welcome to do so. We try to keep out docs action-driven, i.e. structure information and guides according what a developer wants to achieve. This info however is preceded by an overview of a high level Che architecture and brief description of Che (sub)modules. Before you dive into it, let's install and debug your first Che plugin using Che itself.

## Run Eclipse Che

`docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v <path>:/data eclipse/che start`

See: [Install: Docker][docker]

## Create Workspace and Sample Extension

Search for a stack by keyword `che` and choose `che-ide-server-extension` as a sample project. This extension is both IDE and server side extension. It will bring a new menu item, an action to the IDE and a notification with the string retrieved from a simple REST service.   

{% include image.html file="devel/create_extension.png" %}

## Build a Deploy Your Extension

When the project shows up in project explorer, you will find 2 main Maven modules: `assembly` (See: [Assemblies][assemblies]) and plugins (containing plugins themselves). Make sure the parent module is selected in project explorer and hit `Build` command:

{% include image.html file="devel/build_command.png" %}

This command will build plugins and package them into corresponding `war` artifacts that we will then deploy with a Tomcat server.

Once the build is a success, execute `Traefik Start`, `Tomcat8-IDE Start`, `Deploy IDE` and `Deploy Workspace Agent` commands:

* Tomcat8-IDE Start - the name speaks for itself. The command starts a Tomcat 8 server
* Traefik Start - we need some smart redirects inside a workspace. By design, the IDE tries to reach workspace master at the same host:port it is running. Since we will access the 2nd IDE instance on the same host, but a different port (it's a random port from the ephemeral port range that Docker uses to publish exposed ports)
* Deploy IDE - copies ide.war from `assembly-ide-war` target dir over to Tomcat's webapp
* Deploy Workspace Agent does the same with the agent war artifact




{% include image.html file="devel/sample_action.png" %}
{% include links.html %}
