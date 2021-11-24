---
title: Dropserver Documentation -- Basics
layout: ../../layouts/DocsPageLayout.astro
---

## How Does it Work?

Dropserver bakes in common features of most web services plus application management capabilities. This means all Dropserver applications have single-sign-on, user management, public and private routes, etc... In addition, each appspace has automatic data backups and migrations, application updates, resource monitoring, and more.

Dropserver provides a point-and-click interface where users can manage their appspaces, including choosing a subdomain or domain, deciding when apps should automatically upgrade (or not), how often to run backups, and adding or removing appspace users. The goal is to make it possible for non-technical users to install and run their own apps.

A Dropserver application behaves much like a regular web service. It can serve static files or run code in response to requests, and each route can either be public or require authorization. Developers are in familiar territory, however they do not need to write any user authentication code. Read more in the [application model page](/application-model/).

From the user's point of view, a Dropserver appspace is similar to any website they might interact with. The main difference is that they authenticate ("log in") from within their own Dropserver account.

## What Dropserver provides

A Dropserver application consists of backend code written in JavaScript, and runs in [Deno](https://deno.land).

The frontend is entirely up to you. Use what you're familiar with because Dropserver does not mandate any particular framework or dependency. You can even write a native app that communicates with a Dropserver app. Any client that can make HTTP requests can potentially communicate with a Dropserver app.

The Dropserver platform provides the following to all applications:

*   Single-Sign-On and user management
*   Private routes behind user authentication (API keys, secret links, etc... are roadmap features)
*   File storage and Sqlite database
*   Export and import and automatic backups of all application data
*   Data migration hooks
*   Subdomain or custom domain
*   Email sending (on the roadmap)
*   CDN serving of static assets (roadmap)
*   Quotas for resource usage (roadmap)
*   And much more as development progresses...

Writing a server-side service can be daunting. With so many of the hard bits provided by Dropserver, a secure and useful backend for a personal app can be written in a few dozen lines of code.