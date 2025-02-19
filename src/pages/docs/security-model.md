---
title: Dropserver Security Model
layout: ../../layouts/DocsPageLayout.astro
---

# Dropserver Security Model

Dropserver is designed to give users of applications peace of mind. It should not be worrisome to install something from an unknown and therefore not trustable source.

Furthermore if you run a Dropserver for your friends you should not have to worry about what they install and run on your instance.

To achieve this peace of mind we must recognize the harm that untrusted code can do to a user of that application or to the instance and guard against these threats as best we can.

This is in addition to the "standard" threats that affect all web services.

## Standard Threats

As applications accessible to the internet Dropserver Appspaces are subject to the full breadth of known and understood threats.

These are dealt with using well established mitigations:

*   safe by default and minimal configuration options for the user
*   https only
*   tight same origin policies and no CORS
*   Dropserver is solely responsible for setting and validating user authentication cookies, and applies strict policies like secure, http only, same-site, etc... as much as possible

More generally, Dropserver handles as much of the stuff that can impact security (TLS, cookies, CORS, etc..) as it can. With some time and many eyeballs on the open source code Dropserver ought to be acceptably secure and any application that runs within it will benefit from that security.

## Threats Specific to Dropserver

In addition to standard well-known threats Dropserver users may be subject to a number of additional threats because:

*   Dropserver runs untrusted code
*   An administrator of a Dropserver instance may allow other users to operate on their instance

A Dropserver might be under attack from the inside. A user may try to run code that will deliberately attack the instance, or an evil application unwittingly installed by a well-meaning user could be trying to break into the system.

Here are some improper things an evil application might try to do:

*   Data exfiltration
*   Data destruction
*   Unwanted tracking
*   Abuse of computing resources
*   Serving as relay to attack other servers

## Mitigations

Dropserver assumes every Application is potentially hostile and anything the application is allowed to do will be used in a hostile manner.

To mitigate the threats a Dropserver application will be unable to do anything except create and manipulate data in a dedicated Appspace data directory. Anything else must be explicitly allowed by the Appspace's owner.

Here are the specific ways in which Applications are limited:

*   No filesystem access, except for a data directory dedicated to that Appspace, and read-only access to application files. It is not possible to grant further filesystem access.
*   No outgoing network requests unless explicitly whitelisted by the user. This holds for the application on the server, and browser-side code.
*   No network listeners (aka servers). All incoming requests go through Dropserver host.
*   Computing resources are monitored and capped.
*   User login and session security ...?

## Implementation

Sandboxing the application code is of course the key to achieving these security goals.

Dropserver currently uses [Deno](https://deno.land) as its application code sandbox to run untrusted code while controlling its interactions with the outside world.

Deno is a "Secure Runtime for JavaScript and Typescript". Unlike node which allows JS scripts access to a very large swath of calls to the underlying operating system, Deno stands in the way of each and every call to the OS. A Deno user can run untrusted code while limiting what that code can do.

If available, Dropserver can also leverage the operating system's sandbox to prevent unwanted activity or resource consumption. Read about [Dropserver's use of Bubblewrap on Linux](https://olivierforget.net/blog/2023/dropserver-sandbox/) for example.

Regardless of how the code is sandboxed, the running application will be restricted in the following ways:

### Filesystem Access and Protection

For each Appspace file system access is strictly restricted to a few directories: read-only access to the application files and some metadata files, and read-write access to a dedicated data directory. No further filesystem permissions can be granted.

An Appspace can not gain access to other Appspace data directories. An Appspace can only communicate with another Appspace via an http API, as any user would.

While an Application can technically destroy its own data, the consequences of this are limited. Each Appspace's data directory is regularly backed up and stored in a directory that the application can not access.

### Outgoing Network Connections

Appspaces must be granted the right to make network requests from the server. The calls are only allowed if the destination matches a whitelist. (This is a roadmap feature, read about the [challenges here](https://olivierforget.net/blog/2024/dropserver-net-requests/).)

Dropserver also attempts to protect users against unwanted outgoing network connections from the browser side. It does so by adding restrictive headers to HTTP responses (in particular a very strict CSP and no CORS). Most modern browsers respect these headers.

### Network Listeners

Applications are unable to create any kind of network listener. By the same token, it prevents applications from probing for open and used ports on the host.

Dropserver is the server that listens for incoming network requests. These are then forwarded to the right application as needed.

### Computing resources

Each Appspace runs its own Deno Process, which is attached to a cgroup that measures and limits its resource usage. This is a roadmap feature.

## Threats Mitigated

Let's look at our list of basic threats and see how the mitigations neutralize them:

#### Data exfiltration

*   The app has zero access to data except for the data in the appspace directory and the data that comes in via requests. This limits the data it can exfiltrate.
*   The app could steal the appspace data that it does have access to, but it would have to send it somewhere. Here, the restrictions on making requests to other domains can help.

#### Data destruction

*   As above, access to data outside of appspaces controlled by the app is forbidden, so no risk there.
*   Should the app attempt to destroy its own data, or encrypt it in an attempt to extract a ransom, the automated regular backups that are performed entirely outside the control of the app can make such an attack a non-issue.

#### Unwanted tracking

*   Tracking depends on being able to send data to a remote host, so here again limiting the app's ability to make requests to other domains is helpful.
*   In particular a strong default Content Security Policy for all appspace pages neutralizes any attempt to ship data to another server from the frontend.

#### Abuse of computing resources

*   Appspaces are limited in how much disk space they can take up, and CPU and memory is limited by a cgroup (roadmap feature).
*   In addition, the Dropserver user panel reveals resource usage for each of a user's appspaces, which should help identify apps that are not gentle with the user's resources. Roadmap features.

#### Serving as relay to attack other servers

*   The restrictions on outbound requests should prevent this.

## Status of Implementation

Only some of the ideas outlined here are implemented, and in many cases could be implemented better. It's mostly a matter of developer time, and eyeballs.