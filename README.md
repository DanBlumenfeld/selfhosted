# My self-hosted Docker stack
**This is a work in progress...**

I know little about Docker, and little about self-hosting. 

I know that I prefer environments that are easy to recreate whenever necessary, and that I really hate having to go through a lot of manual steps to do so.

## Why public?
Because I'm trying to force myself to be better about clean organization and separation of confidential/configuration data from infrastructural definition. 

And because I'll be less likely to do things that embarrass myself, or at least more likely to clean them up when they become obvious.

## Inspiration
[https://github.com/BaptisteBdn/docker-selfhosted-apps](https://github.com/BaptisteBdn/docker-selfhosted-apps)

[https://github.com/DoTheEvo/selfhosted-apps-docker](https://github.com/DoTheEvo/selfhosted-apps-docker)

## Priorities
 - Backup and Disaster Recovery (automatic data backups, automatic redeployment, minimal manual steps to set up a new self-hosted server)
 - Security (use of HTTPS internally and externally, automatic certificate acquisition and renewal, minimal attack surface)
 - Clarity (well documented, easy to understand, easy to monitor and diagnose problems)
 - Fun (I want to learn new things and play with new-to-me toys)

# The nitty-gritty

## External Dependencies

### Cloud Backup Storage
 - Does what it says on the tin. An off-premises place to which my on-network backups are synced.
 - Currently, [Backblaze](https://www.backblaze.com/), but I'm sure Amazon S3 or Azure or another cloud storage provider would be fine

### NAS 
A RAID box that sits quietly and stores files. I've got a QNAP NAS with all sorts of bells and whistles, but I frankly don't want to use it to run containers or serve media or much else other than save my data. It does a nice job of syncing and mirroring data to and from places like Google, Dropbox, Backblaze, etc., though.
 - Stores media
 - Stores user-specific files for myself and family/friends
 - Stores container backups
 - Stores backups of various other third-party services (Google Drive, Photos, Dropbox, mail, etc)
 - Synchronizes in-house backups with offsite Cloud Backup Storage

### Public DNS/DDNS
I needed a public domain name, hosted by someone who can both provide DDNS (so that I can keep my IP up-to-date), and API access to the nameserver records (so that I can set up automatic Let's Encrypt certificate issuance and renewal [without needing to punch a hole in my firewall for an HTTP challenge](https://letsencrypt.org/docs/challenge-types/#dns-01-challenge)).

I was using [NoIp](https://www.noip.com/), until I realized that while their DDNS offering is great, their Managed DNS doesn't have the API access I'd need. So, I am currently using [Namecheap](https://www.namecheap.com/), and am happy thus far. I am less happy about throwing a year's subscription fee to a provider who didn't meet my needs...entirely my fault for not considering things more carefully first.

There are [lots of providers out there](https://community.letsencrypt.org/t/dns-providers-who-easily-integrate-with-lets-encrypt-dns-validation/86438) who fit the bill, however.

[Here's more details on how I did it in my environment.](howto/namecheap_ddns.md)

## Key Network Devices

### Gateway/firewall
 - Currently a hardware solution composed of Ubiquiti devices. Might go software someday, but I really like the convenience of a simple UniFi network, and it's flexible enough (so far) to meet my needs.
 - Also provides me a place to set up local DNS records, so that I can bind internal IPs to a public domain and secure them with proper certificates.

### Home Automation Server
 - I'm currently playing with [Home Assistant](https://www.home-assistant.io/). While I like the idea of running it in a VM (can't containerize, as ~it needs~ I want add-ons that are themselves containers), it's a key part of my system, and I prefer separate physical hardware into which I can plug a couple of USB dongles for Z-Wave/Zigbee/Matter control.
 - I do require that it be accessed only from inside my network, and I have set up the [Let's Encrypt add-on](https://letsencrypt.org/](https://github.com/home-assistant/addons/blob/master/letsencrypt/DOCS.md)) to secure communications inside the network too. (Other tools I used: the [Certificate Expiry](https://www.home-assistant.io/integrations/cert_expiry/) sensor, and a [nifty blueprint](https://gist.github.com/TJ-developer/b216ee49b1f784ec8a699b5929416261) which uses said sensor to renew the certificate when needed. (Here's [the initial post](https://www.derekseaman.com/2023/06/home-assistant-auto-renewing-lets-encrypt-ssl.html) that showed me the way, as well as notes on how I [resolved a name resolution problem](/howto/ha_networking.md#failure-to-resolve-dns-defined-by-your-local-router).)

### Docker Host
For my first server: any Debian system, basically minimum features + ssh (e.g. Raspberry Pi 4, since I have a spare). Easy to spin up another on any hardware I have lying around.
 - [Docker Engine](https://docs.docker.com/engine/install/debian/)
 - [Docker Compose](https://docs.docker.com/compose/install/#scenario-two-install-the-compose-plugin)
 - Git

## Core Services
 - Reverse proxy/traffic manager/SSL chokepoint. [trafik](https://github.com/traefik/traefik/) 
 - Container management GUI. [Portainer](https://www.portainer.io/)
 - Backup manager (to get container data backed up to the NAS). [borg](https://github.com/borgbackup/borg)
 - Performance monitoring. [Prometheus/Loki/Grafana?](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/prometheus_grafana_loki)
 - **TBD** IDS/IPS/Vulnerability scanning
 - Notifications [ntf](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/gotify-ntfy-signal)
 - IP banning tool [fail2ban](https://github.com/BaptisteBdn/docker-selfhosted-apps/tree/main/fail2ban)
  - SSO/LDAP [authelia](https://www.authelia.com/)/[LLDAP](https://github.com/lldap/lldap)

## Utility Services
 - **TBD** RSS aggregator. Probably TinyTinyRSS
 - **TBD** File sharing system

## Home Automation
 - Node-red. Mostly for fun and education, as I've not yet come close to finding things that Home Assistant can't do.
 - **TBD** MQTT Broker. Mosquitto seems like a good place to start.
 - **TBD** NVR and video analysis. Frigate?

## Media Management
 - Movies/music/tv shows, probably [Jellyfin](https://jellyfin.org/)
 - **TBD** Books and comics. [Calibre-Web](https://github.com/janeczku/calibre-web?ref=selfh.st)? [Kavita](http://www.kavitareader.com/?ref=selfh.st)? [Komga](https://komga.org/?ref=selfh.st)?
 - Photo Management, probably [Immich](https://immich.app/)
 - Document Management, probably [Paperless](https://docs.paperless-ngx.com/)
