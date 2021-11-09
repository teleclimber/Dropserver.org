---
title: Dropserver
layout: ../layouts/HomeLayout.astro
---

## An Application Platform for Your Personal Web Services

Dropserver is an open source server for hosting a multitude of small web-based applications.

With Dropserver the goal is not to scale your application to many users, but to allow each user to run their own applications at a small scale. Dropserver is designed to host multiple different applications just for you, your family, your club, or your small business.

You can run your own Dropserver instance for you and your friends, or (hopefully in the future) you will be able to sign up for a commercial host for a few dollars per month.

Once you have a Dropserver, installing an application takes a few clicks and "just works".

## How Does it Work?

Dropserver bakes in common features of most web services plus application management capabilities. This means all Dropserver applications have single-sign-on, user management, public and private routes, etc... In addition, each appspace has automatic data backups and migrations, application updates, resource monitoring, and more.

Dropserver provides a point-and-click interface where users can manage their appspaces, including choosing a subdomain or domain, deciding when apps should automatically upgrade (or not), how often to run backups, and adding or removing appspace users. The goal is to make it possible for non-technical users to install and run their own apps.

A Dropserver application behaves much like a regular web service. It can serve static files or run code in response to requests, and each route can either be public or require authorization. Developers are in familiar territory, however they do not need to write any user authentication code. Read more in the [application model page](/application-model/).

From the user's point of view, a Dropserver appspace is similar to any website they might interact with. The main difference is that they authenticate ("log in") from within their own Dropserver account.

## What Kind of App Would You Build For Dropserver?

Here are some ideas:

*   Completely private single-user service, like a personal journal. This would have zero public routes and nobody but yourself would have access. However, unlike purely on-device native apps, your journal would be available on any device you're logged in to.
*   A family-oriented service, like a refrigerator minder for tracking leftovers. Give other members of the household access. There would be no public routes.
*   A static site editor and publishing service. The routes for creating content would be private, while the routes that serve the site itself would be public (or you could give access to the site to your friends only.)
*   Any application where there is one or a handful of users that interact with the same data may be a good fit.

At first there won't be many applications for Dropserver, but a goal of the project is to make building one easy.

## Build For Dropserver

If you are able to write some basic JS for webpages then you should be able to write a Dropserver application.

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

## Why?

Dropserver is a kindred spirit of the "indie web" and "small web" movements, which aims to give internet users the means to control their presence on the web. Dropserver's goal is to give users control over the code they interact with in the cloud.

### Your Own Cloud Applications

Instead of signing up for services that eventually shutdown, degrade, or pivot away, set up a Dropserver and install your own applications.

With Dropserver, installing an application takes a few clicks, and you can tell it to upgrade itself automatically or manually control when to upgrade.

### A Convenient Cloud

There is no need to create a new login every time you use a new application. Dropserver gives you single-sign-on to all your installed applications.

Dropserver includes a "contact list" feature that makes it trivial to invite your friends and family to a multi-user app. They too will be able to log in to your application using their existing Dropserver account, regardless of where it's hosted.

### Sandboxed Cloud Applications

Each application runs in a sandbox that prevents it from reading files from disk or contacting any server on the internet by default. If it needs to do any such thing that permission must be explicitly granted by the user.

Read more about Dropserver's [security model](/security-model/).

### No Lock-In

Dropserver is open source so you will always be able to run it on your own hardware if you choose to.

In addition applications and associated data can be exported and moved to a new home, giving you the freedom to relocate to a new server whenever you feel like it.

### Efficient Cloud

How many apps do you have on your phone? How many do you use regularly? Dropserver is designed with that usage pattern in mind: users should be able to install many applications yet only the actively used ones should consume resources like RAM or CPU cycles.

Read more about the [application model](/application-model/).