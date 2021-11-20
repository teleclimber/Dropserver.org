---
title: Dropserver Documentation -- Basics
layout: ../../layouts/PageLayout.astro
---

## How Does it Work?

Dropserver bakes in common features of most web services plus application management capabilities. This means all Dropserver applications have single-sign-on, user management, public and private routes, etc... In addition, each appspace has automatic data backups and migrations, application updates, resource monitoring, and more.

Dropserver provides a point-and-click interface where users can manage their appspaces, including choosing a subdomain or domain, deciding when apps should automatically upgrade (or not), how often to run backups, and adding or removing appspace users. The goal is to make it possible for non-technical users to install and run their own apps.

A Dropserver application behaves much like a regular web service. It can serve static files or run code in response to requests, and each route can either be public or require authorization. Developers are in familiar territory, however they do not need to write any user authentication code. Read more in the [application model page](/application-model/).

From the user's point of view, a Dropserver appspace is similar to any website they might interact with. The main difference is that they authenticate ("log in") from within their own Dropserver account.