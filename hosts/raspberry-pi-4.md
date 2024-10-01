# Setting up the host

*This was heavily inspired by [this set of directions](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/beginners-speedrun-selfhosting)*

OS: Raspberry Pi Lite (headless Debian)

Using [Raspberry Pi Imager](https://www.raspberrypi.com/software/) with customized OS Settings:
- `General` 
    - `Set hostname`: dockerserv.local (you do you, of course)
    - `Set username and password`: duh (also, now's a good time to add these to a password manager)
- `Services`
    - `Enable SSH`: true
        - `Use password authentication`: true

1. Fired up the Pi
1. Configured it with a static DHCP lease in the router
1. Connected via SSH successfully.
1. Had a celebratory drink of Lady Grey tea.

# Setting up Docker
1. Executed `sudo wget -qO- https://get.docker.com | bash` to get Docker
1. Executed `sudo gpasswd -a {username} docker` and exited/re-logged in to avoid lots of Docker sudoing
1. installed [ctop](https://github.com/bcicen/ctop) by executing
>sudo apt-get install ca-certificates curl gnupg lsb-release
curl -fsSL https://azlux.fr/repo.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/azlux-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/azlux-archive-keyring.gpg] http://packages.azlux.fr/debian \
  $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/azlux.list >/dev/null
sudo apt-get update
sudo apt-get install docker-ctop




