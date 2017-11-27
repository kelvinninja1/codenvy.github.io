---
title: "User Management"
keywords: organizations, user management, permissions
tags: [ldap]
sidebar: user_sidebar
permalink: user-management.html
folder: user-management
---
{% include links.html %}

## Auth and User Management

Eclipse Che relies on [Keycloak](http://www.Keycloak.org) to create, import, manage, delete and authenticate users. Keycloak uses its own authentication mechanisms and user storage. Eclipse Che requires a Keycloak token when access to Che resources is requested.

Keycloak can create and authenticate users by itself or rely on 3rd party identity management systems and providers.

## User Federation

Keycloak provides a user friendly page to [connect LDAP/Active Directory](http://www.Keycloak.org/docs/latest/server_admin/topics/user-federation.html). There are a number of [fields to be populated](http://www.Keycloak.org/docs/latest/server_admin/topics/user-federation/ldap.html) on the config page, and those are specific to your particular LDAP instance, user filters, preferable mode etc. It is possible to test connection and authentication even before saving any particular storage provider.

## Social Login

Keycloak offers social login buttons such as GitHub, Facebook, Twitter, OpenShift etc. See: Instructions to [enable Login with GitHub](http://www.Keycloak.org/docs/latest/server_admin/topics/identity-broker/social/github.html).

## Protocol Based Providers

Keycloak provides support for [SAML v2.0](http://www.Keycloak.org/docs/latest/server_admin/topics/identity-broker/saml.html) and [OpenID Connect v1.0](http://www.Keycloak.org/docs/latest/server_admin/topics/identity-broker/oidc.html) protocols so you can connect your identity provider systems if they support these protocols.

## Managing Users

There's UI to add, delete and edit users. See: [Keycloak User Management](http://www.Keycloak.org/docs/latest/server_admin/topics/users.html).

## SMTP Configuration/Email Notifications

Eclipse Che does not provide any out-of-the-box SMTP servers. This functionality is enabled in Keycloak itself, `che realm settings > Email`. You will need to provide host, port, username and password, if necessary. Eclipse Che is shipped with the default theme that is used for email templates (registration, email confirmation, password recovery, failed login etc).
