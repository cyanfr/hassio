## Obtain Software
Get etcher and install:

https://etcher.io/

Get rasbian image:

https://www.raspberrypi.org/downloads/raspbian/

Unzip to get to reveal .img file.

Run etcher and write .img to microsd card.

#### add an empty file called ssh to boot on sdcard before first boot

```
sudo apt-get update
sudo apt-get upgrade
sudo reboot
sudo apt-get install vim
vi .profile
````

#### add the following
```
alias ll="ls -l"
alias vi="vim"
alias view="vim -R"
```

#### generate strong key-pair
```
cd ~
ssh-keygen -t rsa -b 4096
cd ~/.ssh
vi authorized_keys
```
#### add your public key
#### this is NOT the key you just created.
#### but rather a public key from a key-pair you previously generate
#### you'll use the to connect to shell with putty without a password
#### test that you can connect to shell with key and not password
#### once you are sure that works, lock down the sshd_config to not allow direct root connect or password connect

```
sudo vi /etc/ssh/sshd_config
```

#### make the following changes
#### uncomment and change from default port of 22 to something else
```
Port ?????
```
#### uncomment (if needed) and update as follows
```
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
PermitRootLogin no
```

#### restart ssh service
```
sudo service ssh restart
```
#### now disconnect and reconnect

#### install lastest docker
```
curl -sSL https://get.docker.com | sh
```

#### install other dependencies as per
#### https://github.com/home-assistant/hassio-build/blob/master/install/README.md
```
sudo apt-get install socat jq
sudo reboot
sudo su
curl -sL https://raw.githubusercontent.com/home-assistant/hassio-build/master/install/hassio_install | bash -s -- -m raspberrypi3
sudo reboot
```
#### hassio takes about 20 mins to start first time

#### now try to connect via browser
http://<192 local IP>:8123

#### reconnect to raspbian shell
```
cd /usr/share/hassio/homeassistant
sudo vi configuration.yaml
```
#### uncomment
```
api_password: !secret http_password
```
```
sudo vi secrets.yaml
```
#### replace welcome with a real one# restart hassio
