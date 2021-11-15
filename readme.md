# Forge Backup

This is a shell script for establishing encrypted, efficient backups of your [Laravel Forge](https://forge.laravel.com) server on [AWS S3](https://aws.amazon.com/s3/) storage.

**This is a free script with no support or warranty. You're responsible for whatever joy or sadness it brings you!**

## Requirements

- Server provisioned with Laravel Forge running Ubuntu 18+.
- Amazon Web Services account.

## What It Does

- Installs [restic](https://restic.net/), a free, secure, and efficient backup program.
- Creates a read-only `backup` MySQL user for generating dumps.
- Adds shell scripts:
    - `mysql-backup.sh` for generating and pruning dumps in `//mysqldump`
    - `restic-backup.sh` for running database dumps and backing up `/home/forge` to S3
    - `restic-mount.sh` for locally mounting and browsing+verifying your backups
- Stores configuration and shell scripts in `/root/restic`.

## Setup

Create a AWS S3 bucket for your server, and an application key limited to that new bucket.

Run the installer **as root (not sudo!)** On its initial run, it will ask for S3 credentials and your `forge` database password to create the `backup` database user.

```
sudo bash
curl -O https://raw.githubusercontent.com/stijnvanouplines/forge-backup/master/restic-setup.sh && chmod +x restic-setup.sh && ./restic-setup.sh
```

## Scheduling

Once the restic repository is established, try running a few backups and mounting them with `restic-mount.sh` to be sure everything looks good. If it does, automate backups by adding a scheduled job that runs as `root`:

```
/root/restic/restic-backup.sh >/dev/null 2>&1
```

A custom value of `0 3 * * *` will run every day at 3am.

Optionally run the MySQL backup on its own interval:

```
/root/restic/mysql-backup.sh >/dev/null 2>&1
```
