#Raspberry pi

## OS

You can install either the raspberry OS or a linux OS.  
For my case, I went for the raspberry OS light (no GUI) 32 bits. (because the 64bits is in beta)

## Static IP

I set a static ip for both my ethernet and wi--fi connexion
```bash

# /etc/dhcpcd.conf

# ethernet
interface eth0
static ip_address=192.168.0.25/24
static ip6_address=fd51:42f8:caae:d92e::ff/64
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 8.8.8.8 fd51:42f8:caae:d92e::1

# wifi
interface wlan0
static ip_address=192.168.0.26/24
static routers=192.168.0.1
static domain_name_servers=192.168.0.1 8.8.8.8
```


## Docker

Install and add the current user in docker group so you don't have to sudo docker-compose each time ...
```
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker pi
``` 

Let's install docker compose since you will need it.
```
sudo apt install -y libffi-dev libssl-dev python3 python3-pip
sudo pip3 install docker-compose
```

## SSH & GITHUB

Generate your ssh key
`ssh-keygen -t ed25519 -C "email@email.com`

Create your `~/.ssh/config` file to allow the connexion to github
```bash
Host github.com
    Hostname github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
```

On github, you will need to add your public key.

You can check if everything is in order by doing a quick checkup `ssh -T git@github.com`
