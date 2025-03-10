---
title: Dropserver Application Model
layout: ../../layouts/DocsPageLayout.astro
---

# Dropserver Application Model

Dropserver is a fresh take on hosting applications for non-technical users. As such it is worth considering each aspect anew.

## Goals

Dropserver's goal is to allow individuals to host their own personal web-based applications and services in a safe and convenient way. This means:

*   **Security and Privacy:** Minimize the damage a malicious app can do by using the principle of least privilege. See [security model](security-model) for details.
*   **Convenience:** Users should be able to "install" apps in a few clicks, and not be burdened with maintenance tasks.
*   **Simplicity:** Assume users are not experts. Minimize configuration options and favor making safe decisions for them.
*   **Portability:** Should they choose to move to a different host, or want to host under a different domain, an existing service should be transplantable with minimal fuss.
*   **Stability:** While Dropserver will evolve over time and its APIs will change, old applications should continue to work on the latest Dropserver.
*   **Performance:** Users may install many apps, but performance should be as good as possible on low power hardware. It also means responding quickly to requests to an appspace even if it is rarely used.

## What is a Dropserver "Appspace"?

We are all familiar with apps on our phones, and applications on our desktop based systems.

In these systems, we typically use the OS's user interface to "launch" an application, or to bring it to the foreground, and then interact with the app using its UI.

We are also familiar with typical web-based services, which work differently: the user navigates to the app's domain and logs in to interact with their data.

So how is a _personal_ web-based application accessed?

Since it is web-based, the implication is that has its own unique address on the web: a URL the user can enter into an address bar.

On the desktop a user typically chooses which files to edit in the application using the OS's file picker. This does not map well with the way web services typically work. Therefore an instance of an application is tied to a data directory.

An application instance therefore specifies:

*   An address, like myservice.example.com
*   Application code
*   A data directory

In Dropserver this trio is called an **Appspace**.

Let's go over these concepts and more:

### Appspace Address

An appspace's address is a domain, optionally with subdomains: "myapp.mypersonalsite.name" for example.

The address must be a unique domain and subdomain combination to ensure cookies for one appspace can't be used for an other.

### Application Code

Each appspace is run by a single application at a given version.

Application code consists of one or more files, including JavaScript code that runs in response to inbound requests (see sandbox in [security model](security-model)), frontend code files, assets, data files, and anything else that is needed.

One application can be used to run multiple appspaces, however each appspace is completely independent.

Applications can be uploaded from the users computer, or acquired via a URL (roadmap feature).

### Appspace Data Directory

Each appspace gets exclusive read and write access to a directory on the system. No other appspace has read or write access there.

This directory is created when the appspace is initialized and is used to store data in files.

Files in the appspace's data directory can be served statically via specially crafted routes (see below).

The size of the directory can be monitored and restricted (roadmap feature).

### Appspace Schema And Data Migrations

The application is responsible for the appspace's data directory structure, as well as the schema of the databases within it.

An application specifies the version of the schema that it works with. Each new version of the application can specify the same schema until it needs to make a change to the structure of the files and/or databases within it (such as to accommodate a new feature).

When a migration is needed the application specifies a new schema, and it also includes code that can migrate a data directory from the previous schema to this new one, as well as perform the reverse operation.

Dropserver runs migrations as needed when a new version of the application is used by an appspace, and upon initialization of an appspace.

### Appspace Data Backup and Import/Export

Dropserver can stop an appspace temporarily and take a "snapshot" of an appspace (basically a copy of the data directory plus some metadata). This can be used to backup an appspace.

The exported data contains enough data to recreate the appspace on a different Dropserver host. If proper app coding practices are followed (tbd) it should be possible to move an appspace to a new host and a new domain with no issues.

Finally it's also possible to run an exported appspace locally on a desktop. This can be useful for debugging an app (see ds-dev), or simply because it's a convenient way of accessing an appspace that is no longer online.

### Appspace Availability

To ensure consistent backups, Dropserver stops the appspace before performing a backup. "Stopping" an appspace means waiting for all http requests to finish, and for all connections to databases to close cleanly. New HTTP requests get a 503 response during this period. Once the backup is finished the appspace resumes normal operation.

An appspace owner can also manually cause the appspace to become unavailable by hitting the "Pause" button in the UI.

Furthermore, Dropserver will make an appspace unavailable if it detects an anomaly in the appspace (such as the wrong data schema). These measures are intended to forestall data corruption.

### Application Code is Stoppable

Application code is written so that the sandbox can be stopped. This frees up resources for other applications.

Long running code like server listeners are taken care of by Dropserver as much as possible. An App's design is similar to a "serverless" app: functions that respond to certain requests.

Dropserver can also handle some routes itself, without waking the sandbox: in particular it can serve static assets. (See route types below.)

### Appspace Users

When a Dropserver user creates an appspace, they are automatically added as a user of that appspace. For single-user apps, that's the end of the story. But cloud apps are more fun and useful when family, friends, and collaborators are there too.

Some apps can benefit from being more open to outsiders. Consider for example an appspace that provides commenting capabilities for a blog. In those cases, the appspace owner can make registration "open". This means that someone can add themselves to the appspace by submitting their Dropserver URL and username and authenticating. (Roadmap feature, details TBD.)

The appspace owner can add a friend to the appspace using their email (roadmap), or using something called a DropID.

A DropID is essentially a link to the friend's own Dropserver instance. With a DropID, the friend can log in to the user's appspace simply by logging in to their own Dropserver.

To log in with an email, the user would enter their email to receive a special link that would log them in. (Roadmap feature.)

### Application Routes

Routes are created in app code, but unlike most JS server apps, these routes are not stored in the running application's memory. Instead they are written to a database on the Dropserver host. This allows Dropserver to clear an appspace's runtime stack and shut down its sandbox when it is not used.

Routes are declared by specifying the relevant http methods and the path, an authorization scheme (see below), and a route handler.

Route handlers can be:

*   Call function: start a sandbox if necessary and call this function which will handle the reply.
*   Static serve: a static file server that can map to an appspace directory or an app directory.

Dropserver can respond to static routes without starting the sandbox. This helps appspaces feel responsive and conserve resources.

There are two ways to create routes: by the app when it is installed, and dynamically during regular appspace use (the latter is a roadmap feature).

### Route Authorization

Routes can be:

*   Authorized: a visitor must be authorized to access the route by authenticating as an authorized user or with an API key (API key is a roadmap feature.)
*   Public: no authentication or authorization needed.

Users can authenticate using their own Dropserver account, regardless of where it is hosted.

In addition, there is a concept of permissions: apps can specify permissions that can be required to access certain routes. It works like this:

1.  App code specifies a list of permissions (like "view", "create").
2.  Routes with the "Authorized" scheme can require that a permission be held (accessing /create requires the "create" permission).
3.  Users and API keys can be associated with permission in the user management interface (see below).

### Timed Function Calls

In addition to responding to http requests app functions can be called in a [cron](https://en.wikipedia.org/wiki/Cron)-like fashion. (Roadmap feature.)

### Resource Utilization

Resources used by each appspace are tracked, including CPU time, disk usage, memory used when running, etc...

This is all a big roadmap meta-feature.