# Buildable Mautic application stack for Kubernetes on Wodby

Deploy Mautic built from your Git repository on Kubernetes with Wodby: your
repository is a Mautic Composer project with your own plugins and themes.

This repository defines the Wodby stack manifests and default service
composition for buildable Mautic.

<!-- wodby:generated:start -->

## Stack contract

- [Browse Wodby application stacks](https://wodby.com/stacks)
- [Wodby stack documentation](https://wodby.com/docs/2.0/stacks/)
- [Stack manifest reference](https://wodby.com/docs/2.0/stacks/template/)

## Service definitions

- [Buildable Mautic service](https://github.com/wodby/service-mautic-buildable)
- [MariaDB service](https://github.com/wodby/service-mariadb)
- [NFS provisioner service](https://github.com/wodby/service-nfs-provisioner)
- [OpenSMTPD service](https://github.com/wodby/service-opensmtpd)

## What's included

| Component / service | Default configuration |
| --- | --- |
| Mautic<br>`mautic` | required; enabled by default; derivatives: `cron`, `worker`; volumes: `files` 20 GB; links: `db` → `mariadb`, `files` → `files-nfs`, `sendmail` → `opensmtpd` |
| MariaDB<br>`mariadb` | optional; enabled by default; versions: `12.3`, `11.8` (default), `11.4`; volumes: `data` 10 GB |
| Files NFS storage<br>`files-nfs` | optional; enabled by default; volumes: `data` 25 GB |
| OpenSMTPD<br>`opensmtpd` | optional; enabled by default |

Enabled optional services are selected by default but can be excluded when an
app is created. Disabled optional services are available but not selected by
default. Required services cannot be excluded.

## Validate the stack manifest

```bash
wodby stack validate-manifest stack.yml --org <org-id>
```

<!-- wodby:generated:end -->

## Deploy this stack

Connect a Git repository started from the
[Mautic boilerplate](https://github.com/wodby/mautic-boilerplate), Mautic's
recommended Composer project with a `composer.lock`. Add plugins and themes
with Composer, or commit them under `docroot/plugins/` and `docroot/themes/`.
Wodby runs `composer install`, which also builds Mautic's assets, and adds its
settings on top of [wodby/mautic-php](https://github.com/wodby/mautic-php).

Updating Mautic means updating `composer.lock` and deploying; the deployment
runs the database migrations and installs or updates your plugins. You decide
when to update, so watch Mautic's releases for security fixes. To have Wodby
keep Mautic up to date instead, use the
[Mautic stack](https://github.com/wodby/stack-mautic).

Enter the administrator's password and email, and optionally a username, in the
Mautic service's settings when you create the app. The first deployment installs
Mautic with that account; Mautic's web installer is not used. Email is sent
through OpenSMTPD from the administrator's address until you change it in
Mautic's settings.

Mautic's cron jobs run in the `cron` worker, and queued email and page hits are
processed by the `worker` worker. Mautic's settings file, uploads, language
packs, imports and logs are kept on the shared `files` volume: back it up
together with the database.

Mautic supports MariaDB and MySQL only and can't connect to a database server
over TLS.

## Maintain a custom version

1. Fork this repository.
2. Edit the stack manifest.
3. Import the repository as a [Git-backed stack](https://wodby.com/docs/2.0/stacks/create/#create-a-git-backed-stack).

When replacing or renaming a stack service, update every related link target
and derivative reference. Stack-local names and referenced service names are
distinct identifiers.
