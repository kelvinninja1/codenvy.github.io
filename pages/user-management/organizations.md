---
title: "Organizations"
keywords: organizations, user management, permissions
tags: [organizations]
sidebar: user_sidebar
permalink: organizations.html
folder: user-management
---
{% include links.html %}

## Organizations in Eclipse Che

Organizations allow admins to group Eclipse Che users and allocate resources. Resources and permissions are controlled and allocated within the admin dashboard by a system administrator.

## Roles

There are two different roles in an organization:  

- **Admin**: Organization admins are able to manage the organization. Admins can edit settings, manage members, resources and sub-organization.  
- **Members**: Organization members are able create workspaces, manage their own workspaces and use any workspaces they have permissions for.  

There is also a **System Admin** role which sits above the organization construct. System Admins are able to create root organizations, as well as manage all resources, members and sub-organizations.

## Organization Structure

Top level organizations are called "root organizations." Any organization can 0..n sub-organizations.

Multiple root organizations are allowed and can be created only be the System Admin. Management of root organizations' resources can only be done by the System Admin.

## Creating an Organization

Only the System Admin is able to create root organizations. Organization Admins can create sub-organizations.

To create an organization, use the menu in the left sidebar, which leads to the list of organizations:  

{% include image.html file="organizations/organization-menu.png" %}

A new page is displayed with all organizations in your system. Click on the top-left button to create a new organization.


{% include image.html file="organizations/organization-list.png" %}

A new page is displayed in which an organization name should set and organization members may be added.

{% include image.html file="organizations/organization-create.png" %}

## Organization List

The list of all organizations are displayed on organizations page:

{% include image.html file="organizations/organization-list2.png" %}

The list contains the general information for each organization: number of members, total and available RAM and number of sub-organizations.

## Adding Organization Members

Adding organization members by clicking the "Add" button will display a new popup. You can add multiple users at the same time by separating emails with a comma but note that all users added at the same time will be given the same [role]][TODO]:

{% include image.html file="organizations/organization-multiple-invites.png" %}

You can change an organization member's role or remove them from the organization at any time.

{% include image.html file="organizations/organization-create-invite-members.png" %}

Note: Users with the green checkmark beside their name already have an account on your Eclipse Chesystem and will be added to the organization. Users without a checkmark do not have an account and will not be added into the organization.

## Workspaces in Organization

Workspace is created inside of an organization and uses the resources of this very organization. Workspace creator has to choose the organization on workspace creation page:

{% include image.html file="organizations/organization-create-workspace.png" %}

## Email Notifications

When a user joins or leaves an organization, Che server can send a notification in case SMTP is properly configured in `che.env`. By default, Che does not offer any built-in SMTP server. You may use any mail server you want. Below is an example on how to use own Gmail to send notification emails:

```
CHE_MAIL_PORT=465
CHE_MAIL_HOST=smtp.gmail.com
CHE_MAIL_SMTP_STARTTLS_ENABLE=true
CHE_MAIL_SMTP_AUTH=true
CHE_MAIL_SMTP_AUTH_USERNAME=no-reply@gmail.com
CHE_MAIL_SMTP_AUTH_PASSWORD=password
```

## Create Sub-Organization
The creation of sub-organization can be done from organization details page by selecting Sub-Organizations tab and clicking "Add Sub-Organization" button.


The flow of sub-organization creation is the same as for organization.

## Add members to Sub-Organization

The sub-organization members can be added only from the list of parent organization's members:

{% include image.html file="organizations/sub-organization-add-members.png" %}

## Organization and Sub-Organization Administration

{% include image.html file="organizations/organization-settings.png" %}

Organization settings are visible to all members of the organization, but only the Eclipse Che system administrator is able to modify the settings.

## Rename an Organization or Sub-Organization

**Action restricted to**: Eclipse Che system administrator and admins of the organization.

To rename an Organization, click in the "Name" textfield and start editing the name of the organization. Once edited, the save mode will appear - click on "Save" button to update the name.

The name of the organization is restricted to the following rules:  
- Only alphanumeric characters or a single "-" can be used  
- Spaces cannot be used in organization names  
- Each organization name must be unique within the Eclipse Che install
- Each sub-organization name must be unique within an organization

## Leave an Organization or Sub-Organization

This action is not possible for members of an organization. Users have to contact organization's admin or Eclipse Che system admin.

## Delete an Organization or Sub-Organization

**Action restricted to**: Eclipse Che system administrator and admins of the organization.

{% include image.html file="organizations/organization-delete.png" %}

To delete an organization or a sub-organization, click on "Delete" button.
This action can't be reverted and all workspaces created under the organization will be deleted.

All members of the organization will receive an email notification to inform about organization deletion.

## Organization Resources

Workspaces created in an organization use the resources allocated by the system administrator to that organization. Resources for sub-organizations are taken from the parent organization. Admins can control whether all, or a portion of the parent's resources are available to the sub-organization.  

## Managing Limits

**Action restricted to**: Eclipse Che system administrator and admins of the organization.
The organization default caps are taken from the system configuration. The admin of the organization can manage only the limits of it's sub-organizations.
By default, there are no resource limits applied to the organization so all members can benefit from all the allocated resources. If an organization admin wishes to set limits they have three options:  

- **Workspace Cap**: The maximum number of workspaces that can exist in the organization.  
- **Running Workspace Cap**: The maximum number of workspaces which can run simultaneously in the organization.  
- **Workspace RAM Cap**: The maximum total RAM organization workspaces can use in GB.  

## Update Organization and Sub-Organization Member Roles

**Action restricted to**: Eclipse Che system administrator and admins of the organization.

To edit the role of a organization member click on the "Edit" button in the "Actions" column:

{% include image.html file="organizations/organization-edit-role-action.png" %}

You'll get a pop-up where you can update the role of the selected member:

{% include image.html file="organizations/organization-edit-role.png" %}

Click "Save" to confirm the update.

## Remove Organization and Sub-Organization Members

**Action restricted to**: Eclipse Che system administrator and admins of the organization.

To remove a member from the organization, you can click on the "Delete" button in the "Actions" column:

{% include image.html file="organizations/organization-remove-single-member.png" %}

You'll get a confirmation pop-up, where you can confirm or cancel your action.

You can also select multiple members from the organization, using the checkboxes. A delete button will appear in the header of the table:

{% include image.html file="organizations/organization-remove-members.png" %}

The members that are removed from the organization will receive an email notification.
