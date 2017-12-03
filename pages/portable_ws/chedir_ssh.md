---
title: "SSH"
keywords: chedir, factories
tags: [chedir, factories]
sidebar: user_sidebar
permalink: chedir_ssh.html
folder: portable_ws
---

{% include links.html %}

You can use Chedir to SSH into the newly created workspace, whether it is local or remote. It does not matter what operating system that you are using, this technique also supports Microsoft Windows without having to install putty!

```shell
docker run -it --rm -v /var/run/docker.sock:/var/run/docker.sock
                    -v <path>:/data
                    -v <path-to-workspace>:/chedir
                       eclipse/che:<version> dir ssh
  ```

The command has local context of the Che server and workspace that is associated with the Chefile in the current directory. Chedir looks up the appropriate context and then initiates an SSH connection.
