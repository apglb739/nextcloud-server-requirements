# Nextcloud Server Requirements and Install Options: How Much RAM You Need, Which Database and PHP Version to Pick, and Which Install Path Survives an Upgrade

Nextcloud's administration manual reduces the sizing question to two numbers: 128MB of RAM per process as a floor, 512MB per process as the recommendation. The current supported stack is PHP 8.5, MariaDB 11.8 or PostgreSQL 18, and Apache 2.4 running php-fpm. The built-in Updater wants 256MB to itself on top of that.

Three details decide whether an install survives contact with real files. Memory is counted per process rather than per machine, the official Docker image boots on SQLite, and plenty of tutorials still in circulation install PHP 8.2 on Ubuntu 22.04, neither of which appears on the current support list. Everything below is checked against the Nextcloud 35 manual released on November 5, 2026.

> **Quick pick:** run the official `apache` image, connect it to an external MariaDB 11.8 or PostgreSQL 18 database from the first boot, and keep SQLite for a throwaway test instance. For the machine itself, SpinServers sells unmanaged KVM VPS and bare metal in Dallas and San Jose. The VPS page lists a starting price of **$62.00/mo** for 12 vCPU, 12 GB RAM and a 160 GB RAID-10 SSD; the entry dedicated server is **$79.00/mo** for a 4-core Xeon E3-1280 v5 with 32 GB RAM and a 1 TB NVMe drive. 👉 [Check SpinServers' current configurations and prices](https://bit.ly/Spinservers)

## Nextcloud 35 requirements at a glance

| Component | Supported by Nextcloud 35 |
| --- | --- |
| Operating system (64-bit) | Ubuntu 26.04 LTS (recommended), Ubuntu 24.04 LTS, RHEL 10 (recommended), RHEL 9, Debian 13 (Trixie), SLES 16, SLES 15 SP7 or later, openSUSE Leap 16, CentOS Stream, Alpine Linux |
| Database | MySQL 8.4 / 9.7; MariaDB 10.11 / 11.4 / 11.8 (recommended) / 12.3; PostgreSQL 14 / 15 / 16 / 17 / 18 (recommended); Oracle 19c, 21c, 23ai only with an enterprise subscription; SQLite 3.24+ for testing and minimal instances |
| Web server | Apache 2.4 with `mod_php` or `php-fpm` (recommended); nginx with `php-fpm` |
| PHP | 8.3, 8.4, 8.5 (recommended) |

The 64-bit recommendation is a firm one in the manual, though 32-bit systems are still supported with three stated limits: dates before January 1, 1970 are unsupported, dates after 2038 are unsupported, and some third-party apps will not run. A 64-bit OS is the easy call.

If you are following a guide from a year or two ago, check its platform versions before you copy a single command. The 33 branch of the same document listed Ubuntu 22.04 LTS, Debian 12 and SLES 15 SP6 as supported and marked PHP 8.2 as deprecated; all four are gone from the 35 list. MySQL moved from 8.0 to 8.4 / 9.7, and MariaDB 12.3 was added.

Client requirements moved too: Files for Android needs 9.0 or newer, and the Talk app now requires Nextcloud Server 22.0+ with Talk 12.0+. Desktop clients cover Windows 10+, macOS 12.0+ (64-bit only, and your server may need a certificate that satisfies Apple's App Transport Security), and Linux via the official AppImage on any distribution newer than Ubuntu 18.04.

## The hardware side: SpinServers' current plans

SpinServers runs two lines, both unmanaged with root access, both in US data centers (Dallas, Texas and San Jose, California). Prices are monthly and quoted in USD as starting prices.

**KVM VPS line**

| Plan | vCPU / RAM | Storage | Transfer | Starting price | Order |
| --- | --- | --- | --- | --- | --- |
| Simple Application Server | 6 vCPU / 8 GB DDR4 | 120 GB RAID-10 SSD | 8 TB/mo | Not published | [ View plan](https://bit.ly/Spinservers) |
| Simple Application Server | 8 vCPU / 8 GB DDR4 | 160 GB RAID-10 SSD | 10 TB/mo | Not published | [ View plan](https://bit.ly/Spinservers) |
| Simple Application Server | 12 vCPU / 12 GB DDR4 | 160 GB RAID-10 SSD | 10 TB/mo | **$62.00/mo** | [ View plan](https://bit.ly/Spinservers) |
| Simple Application Server (KVM 16vCPU 16GB) | 16 vCPU / 16 GB DDR4 | 320 GB RAID-10 SSD | 15 TB/mo | **$74.00/mo** | [ View plan](https://bit.ly/Spinservers) |
| Advanced Application Server | 32 vCPU / 32 GB | 500 GB SSD | 20 TB/mo | Not published | [ View plan](https://bit.ly/Spinservers) |

All VPS plans include a 1 Gbps port plus one IPv4 and one IPv6 address.

**Dedicated servers**

| Server | CPU | RAM | Storage | Price | Order |
| --- | --- | --- | --- | --- | --- |
| Intel Xeon E3-1280 v5 (Dallas) | 4C / 8T, 3.7 GHz | 32 GB DDR4 ECC | 1 TB M.2 NVMe | **$79.00/mo** | [ View server](https://bit.ly/Spinservers) |
| Intel Xeon E3-1280 v5 (San Jose) | 4C / 8T, 3.7 GHz | 32 GB DDR4 ECC | 1 TB M.2 NVMe | **$79.00/mo** | [ View server](https://bit.ly/Spinservers) |
| 12-core Xeon (San Jose) | 12C / 24T, 2.6 GHz | 64 GB DDR3 ECC | 240 GB SSD | Not published | [ View server](https://bit.ly/Spinservers) |
| Dual Xeon E5-2630L v3, SJC-202 | 16C / 32T, 1.8 GHz | 64 GB DDR4 ECC | 1.6 TB SSD | Not published | [ View server](https://bit.ly/Spinservers) |
| Dual Xeon E5-2630L v3, SJC-206 | 16C / 32T, 1.8 GHz | 64 GB DDR4 ECC | 1.6 TB NVMe | Not published | [ View server](https://bit.ly/Spinservers) |
| Dual Xeon E5-2650L v3, SJC-203 | 24C / 48T, 1.8 GHz | 64 GB DDR4 ECC | 1.6 TB SSD | Not published | [ View server](https://bit.ly/Spinservers) |
| Dual Xeon E5-2630L v3, SJC-201 | 16C / 32T, 1.8 GHz | 256 GB DDR4 ECC | 2 × 1.6 TB SSD, HW RAID | Not published | [ View server](https://bit.ly/Spinservers) |
| Dual Xeon E5-2683 v4, SJC-204 | 32C / 64T, 2.1 GHz | 512 GB DDR4 ECC | 2 × 3.84 TB SSD, HW RAID | Not published | [ View server](https://bit.ly/Spinservers) |
| Dual Xeon Gold 6148 (Dallas) | 40 vCPU | 256 GB | 3.84 TB SSD | **$235.00/mo** | [ View server](https://bit.ly/Spinservers) |
| Dual Xeon Platinum 8173M (Dallas) | 56 vCPU | 512 GB | 7.68 TB SSD | **$390.00/mo** | [ View server](https://bit.ly/Spinservers) |

Where a price reads "not published", that configuration is listed on the SpinServers site without a starting price attached to it. I have not filled the gaps with estimates. The same pages publish no renewal price, no money-back window and no trial, so confirm those with sales before you commit to anything longer than a month.

For a Nextcloud install, the useful starting point is the 6 vCPU / 8 GB tier, not the featured 12 vCPU plan. A file-sync server for a handful of people is bottlenecked by disk and by PHP worker count, not by core count, and 8 GB covers a MariaDB instance, Redis and a modest php-fpm pool without much room to spare. Step up to 12 or 16 vCPU when the same box also runs Talk, an office suite container or object storage. The dual-socket machines with 256 GB and 512 GB are for multi-tenant deployments, not for a department share.

One detail matters more than the spec sheet if you plan to send mail from the server:

> "In an effort to safeguard our clients and prevent IP abuse, we close port 25 (SMTP mailing port) by default on all of our virtual private servers. If you require this port to be opened, please submit a ticket with a justification."

Nextcloud sends password resets, share notifications and activity digests over SMTP. Point it at an authenticated relay on port 587 or 465 and the block never comes into play. Run a local mail transfer agent that delivers directly to remote MX records and outbound port 25 is what you need, so either open a ticket or skip the local MTA entirely. 👉 [Compare SpinServers' VPS and dedicated server options](https://bit.ly/Spinservers)

## Memory: count PHP processes, not user accounts

The manual is explicit that memory requirements "are greatly variable, depending on the numbers of users, apps, files and volume of server activity", which is why forum answers range from 512MB to 16GB without anyone being wrong. The per-process figure is the one you can actually calculate with.

Take your php-fpm pool size (`pm.max_children`). Ten workers at the recommended 512MB each is roughly 5GB reserved for PHP before MariaDB allocates a buffer pool. Add the database, Redis if you enable memory caching, the web server and the OS, and an 8 GB machine with ten PHP workers is already tight. Drop to four workers on a small team and 4 GB is workable. The 128MB floor exists for constrained environments, where the manual warns that some features and apps need their defaults lowered or switched off.

Two numbers are easy to forget. The built-in Updater needs at least 256MB when it runs, so a box sitting permanently at 95% memory will fail upgrades rather than installations. And the recommendation applies to every process, not to the sum, so "512MB of RAM" advice you see quoted for a whole server is describing a single-user test box.

## Database prerequisites that block the install

MySQL and MariaDB have four hard requirements, and a stock installation satisfies maybe two of them:

- **InnoDB storage engine.** MyISAM is not supported.
- **READ COMMITTED transaction isolation level.** Set it explicitly in the server config. It is not what a default install hands you.
- **Binary logging disabled, or `BINLOG_FORMAT = ROW`.** Replication-friendly setups on managed database services often default to `STATEMENT`, which breaks things later rather than at install time.
- **UTF8 4-byte support** if you want emoji in file names and comments, which needs a separate configuration step on MySQL and MariaDB.

Get these wrong and you find out during setup or during the first sync of a file with an emoji in its name. PostgreSQL 18 avoids the isolation-level question entirely, which is one reason it carries the recommendation alongside MariaDB 11.8. SQLite needs none of this configuration, and that convenience is exactly why it becomes a trap.

## Three install paths and what each costs you later

**Official `apache` image.** The `nextcloud/docker` repository has been running since July 2016 and ships over 1,200 tags, so the image is well-trodden. It bundles Apache and PHP, persists everything outside the install directory in a volume mounted at `/var/www/html`, and accepts environment variables for the database connection, the initial admin account, `datadirectory`, `trusted_domains`, Redis, SMTP, object storage and reverse-proxy settings. It is the fastest route to a login page. The README is candid that its examples suit limited testing, and the default database is SQLite.

**Official `fpm` image plus nginx.** Same image, no bundled web server. The container exposes port 9000, and you proxy HTTP requests to it from nginx on the host or in another container on the same Docker network. Do not publish 9000 to the host. FastCGI cannot serve static files, so nginx needs access to the Nextcloud document root, typically by sharing the volume (`volumes-from` or an equivalent mount).

**The linuxserver image.** `lscr.io/linuxserver/nextcloud:latest` picks the right architecture automatically and rebased to Alpine 3.24 with PHP 8.5 in its October 2026 release. Two limits decide the choice for you. Web-UI upgrades are not supported on this image; the update notification app was re-added in August 2023 to warn you about new versions, but you update by pulling a new image. And the collaboration packages that ship with Nextcloud, Collabora/CODE and OnlyOffice, run only on x86_64 with glibc, so they are incompatible with this image and have to live in separate containers reached through their connector apps.

## Four things that go wrong after the install

**SQLite as the default.** The official image starts on SQLite, and the same manual that documents it recommends SQLite only "for testing and minimal-instances". Every guide that says "just run the container" is describing a test box. Point it at MariaDB or PostgreSQL before the first real upload, because migrating afterwards is a project of its own.

**Reading `config.php` and believing it.** The official image injects configuration through Nextcloud's multiple-config support, so the file on disk is not the running configuration. Use `occ config:list system`, which merges everything and hides passwords and secrets by default.

**Configuring SMTP twice.** Environment variables cover `SMTP_HOST`, `SMTP_SECURE` (`ssl` or `tls`), `SMTP_PORT` (465 for SSL, 25 unencrypted, 587 the common STARTTLS port), `SMTP_AUTHTYPE` (default `LOGIN`, use `PLAIN` where no auth is required), `SMTP_NAME`, `SMTP_PASSWORD`, `MAIL_FROM_ADDRESS` and `MAIL_DOMAIN`. At minimum you need `SMTP_HOST`, `MAIL_FROM_ADDRESS` and `MAIL_DOMAIN`. Once these are set, do not touch the mail settings in the web UI: the image's documentation warns that the two override each other. Sensitive values can be read from files with a `_FILE` suffix if you use Docker secrets.

**Reverse proxy host and protocol.** Behind nginx, Caddy or a load balancer, Nextcloud needs to know the public hostname and scheme or it will generate redirects to an internal address. Set `trusted_domains` and the proxy-related host and protocol variables together, and check the result by logging in from outside rather than from localhost.

## Who should not self-host this

If you want the full Nextcloud Hub experience with Talk, collaborative editing and the AI features documented in the 35 manual (local translation, speech-to-text, OCR, context chat) without maintaining a container stack, hand-installing is the wrong use of your week. Nextcloud's own manual points organizations that depend on reliable scaling and professional support toward enterprise support, and a premium subscription adds five years of long-term support covering high and critical security fixes, data-loss fixes and regressions within a version. That is a maintenance guarantee no community image can give you.

Self-hosting makes sense when you have someone who will patch the OS, watch the logs, run backups and handle the occasional upgrade. The software is not the hard part.

## How to choose

- **Evaluating Nextcloud, one user:** official `apache` image, SQLite, delete it afterwards.
- **A team that will keep data on it:** external MariaDB 11.8 or PostgreSQL 18, `fpm` image behind nginx, Redis enabled, backups tested before the first share link goes out.
- **Collabora or OnlyOffice in the mix:** x86_64 with glibc, collaboration server in its own container, connected through the matching Nextcloud app.
- **Machine size:** count php-fpm workers, multiply by 512MB, add the database, Redis and OS headroom, then add a margin for the Updater.

Two things to confirm before you order anything: the OS image you pick appears on the Nextcloud 35 list (Ubuntu 26.04, Ubuntu 24.04, Debian 13, RHEL 9/10 and the others above, not Ubuntu 22.04 or Debian 12), and you have the time to administer an unmanaged server, since SpinServers hands you root and nothing else. If both check out, the 8 GB tier or the $79 dedicated box will run a small Nextcloud comfortably. 👉 [Pick a SpinServers plan for your Nextcloud server](https://bit.ly/Spinservers)
