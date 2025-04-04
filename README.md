
# [GUIDE] Get the best performance out of Docker on Windows with WSL2

This guide shows how to get the best performance out of Docker on Windows with WSL2 

If you care about speed, you should avoid "Docker Desktop for Windows" altogether.

## Prerequisites
- You must have at least one WSL distro installed. This has been tested on both Debian and Ubuntu.

## Step 1 : Install and configure Docker Engine on WSL 
 - Uninstall "Docker Desktop" completely
 - Make sure the distros "docker-desktop" and "docker-desktop-data" no longer exist by using the command "wsl --list", and that you have a distribution set as default.
 - Follow the instructions to install Docker Engine on Debian/Ubuntu
**[https://docs.docker.com/engine/install/debian/](https://docs.docker.com/engine/install/debian/)**
 - Try to run `dockerd`, if you get this error `failed to start daemon: [...] (iptables failed: iptables --wait -A DOCKER-ISOLATION-STAGE-1 -j [...]`, follow these steps :
	 - Run `update-alternatives --config iptables`
	 - Choose option `iptables-legacy`
	
## Step 2 : Start docker daemon on WSL startup
WSL is not a virtual machine and it only starts when a WSL command is entered into a terminal, or a WSL window is opened. Since it's not a complete installation of Linux, it does not have a service manager such as **systemd**. 
Follow these steps to start the docker daemon as soon as you log in : 
- Log in as usual and edit the configuration for sudo
`sudo visudo`
- Allow members of the **docker** group to start the daemon as sudo without a password, by adding these lines after the definition for the **sudo** group
````
# Allow members of group "docker" to execute "dockerd" as root without password
%docker ALL=(ALL) NOPASSWD: /usr/bin/dockerd
````
- Don't forget to add your user to group **docker**
- Then, edit your current user bash configuration with
`sudo nano ~/.bashrc`
- Add the lines :
````
# Start Docker Daemon on login
sudo dockerd &>/dev/null &
disown
````
- Close all opened WSL windows and terminals, and reopen WSL. The docker daemon should be running.

## Optional : configure Visual Studio Code to display containers
To configure the Docker extension for Visual Studio Code so that it shows containers running inside WSL, go to Settings > Docker > Host, and change it for `unix:///var/run/docker.sock`

## Optional : Get Lando to use Docker on WSL2 instead of Docker Desktop for Windows
Courtesy of [https://creativelogic.biz/blog/lando-on-wsl2](https://creativelogic.biz/blog/lando-on-wsl2)

- Create `~/.lando/config.yml` with the following lines :
````
setup:
	buildEngine: false
````
- Install Lando for Linux
- Restart WSL

