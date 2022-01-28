---
title: ds-dev - Dropserver Documentation
layout: ../../layouts/DocsLayout.astro
setup: |
  import Note from '../../layouts/Note.astro'
---

# Using ds-dev to Develop Dropserver Applications

`ds-dev` is a command line tool that runs Dropserver applications locally for the purpose of developing apps.

Some features and capabilities:

- Serves apps at `localhost` over plain `HTTP` (no need to set up TLS) so you can just fire it up and get going.
- It can emulate any user of the appspace or a non-authenticated user, making it easy to test your app as a logged in user or as a member of the public.
- You can point it to an existing appspace data directory (like a backup you downloaded from `ds-host`) to test your app changes on actual "production" data.
- `ds-dev` watches your application files and re-reads your app's metadata after each save.
- The UI shows your app's metadata like routes and schemas.
- The UI has live app and appspace logs, route hits, migrations etc...
- Manually run migrations to test your migration code
- Set "Inspect" mode to debug your app code as it runs in the sandbox.

## Installing `ds-dev`

Get the `ds-dev` executable from the [Dropserver releases](https://github.com/teleclimber/Dropserver/releases) page. The packaged executable is available for MacOS or Linux. Windows users should use the Linux version under WSL2.

`ds-dev` is self-contained **except** for its dependence on `Deno` which is not packaged with it. You must install [Deno](https://deno.land) and make sure that the deno executable is on the path.

## Running `ds-dev`

To run using an empty appspace directory:

```
$ ds-dev -app=/path/to/app/dir/
```

To provide an existing appspace as working data:

```
$ ds-dev -app=/path/to/app/dir/ -appspace=/path/to/appspace/files/
```

If you downloaded appspace data from `ds-host` you must unzip it before pointing `ds-dev` to it.

<Note>The appspace data is never modified. `ds-dev` copies all the files from the provided directory into a temporary directory, and uses that as the working data.</Note>

## `ds-dev` UI Basics

Once running `ds-dev` should indicate that its UI is available at `http://localhost:3003/dropserver-dev/`. Make sure this port is available prior to running `ds-dev`.

Open `http://localhost:3003/dropserver-dev/` in a browser window. The UI should show you basic data about your app, and appspace if there is any.

If the app was loaded without errors and no migration is needed for the appspace data, then you should be able to visit `http://localhost:3003/` to access your app's "/" page.