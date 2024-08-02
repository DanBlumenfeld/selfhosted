# Thursday August 1st 2024
- Set up [public repo for my stack](https://github.com/DanBlumenfeld/selfhosted)
- Documented basic setup and plans

# Friday August 2nd 2024
- Added howtos for [Namecheap DDNS](../howto/namecheap_ddns.md) and [Home Assistant networking](../howto/ha_networking.md)
- Added [Raspberry Pi host info](../hosts/raspberry-pi-4.md)
- Added this journal
- Set up [Raspberry Pi as basic docker host](../hosts/raspberry-pi-4.md)

# Next Steps
- Set up a few core containers, probably Traefik (including Let's Encrypt integration), Portainer, and something for backup that goes all the way to my cloud backup storage.

# Side Quests
- Figure out SSH keys (management, backup, etc) to avoid needing password entry for SSH
- Figure out an initial setup script that I can use on a fresh Debian instance to install docker &tc. and pull down the containers I need
- Figure out how to set ENV vars in the freshly set up containers
- Figure out how to restore container volumes from backup via script