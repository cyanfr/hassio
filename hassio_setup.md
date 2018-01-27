## Obtain Software
Get etcher and install: https://etcher.io/

Get rasbian image: https://www.raspberrypi.org/downloads/raspbian/

Unzip to get to reveal .img file.

Run etcher and write .img to microsd card.

Add an empty file called ssh (no extention!) to boot drive on sdcard before inserting in Raspberry for first boot.

## First Powerup/Shell Session
Put sdcard into Raspberry, plug in ethernet cable and power.

Wait 5 minutes for forst boot to complete.

Connect on port 22 via puTTY or other terminal program to the local IP address assigned to the Raspberry.

username: pi

password: raspberry

## First Updates
On first connection, perform a first update/upgrade of base install.
```
sudo apt-get update
sudo apt-get upgrade
sudo reboot
sudo apt-get install vim
```
Now set some convenient alias commands in the ~/.profile of user pi.
```
cd ~
vi .profile
````
At bottom of .profile add the following:
```
alias ll="ls -l"
alias vi="vim"
alias view="vim -R"
```
Reboot with `sudo reboot` and reconnect.
Not strictly necessary but just to be sure all is clean in the environment.

## Security Measures Related to Shell
Now we will do several things.
1. Create a key-pair for the Raspi.
2. Add the user key-pair to a new file called authorized_keys so that we can connect without a password (safer).
3. Disable password authentication and require key-pair for login.
4. Disable direct root user login and require connecting as pi and sudo su-ing to root when needed.

### Generate strong key-pair for the Raspi.
```
cd ~
ssh-keygen -t rsa -b 4096
cd ~/.ssh
```

### Add the public key we will use when connecting to shell.
```
cd ~/.ssh
vi authorized_keys
```
In this new file, authorized_keys, we add the PUBLIC key we will use when connecting to shell.

This is NOT the key you just created for the Raspi.

But rather a public key from a key-pair you previously generate or generated elsewhere.

When connecting to the shell you will provide the PRIVATE key which will work

in conjunction with the public key stored in authorized_keys to authenticate you and allow to into the shell.

After creating and saving authorized_keys as above, test connecting to the shell with your terminal program with the key-pair.

Do NOT use the password.

Once you are certain logging in with the key-pair works, lock down the sshd_config to deny

direct rootconnect or password connect.

```
sudo vi /etc/ssh/sshd_config
```
Make the following changes:
```
# uncomment and change from default port of 22 to something else
# this is just a bit of obfuscation to disuade casual trolling of ports.
Port ?????
```
```
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
PermitRootLogin no
```
Save the changes and restart the ssh service.
```
sudo service ssh restart
```
Now disonnect and reconnect.

Remember that you will need to use the private key and the new port you just specified.

Install lastest docker.
```
curl -sSL https://get.docker.com | sh
```
Install other dependencies as per:  https://github.com/home-assistant/hassio-build/blob/master/install/README.md
```
sudo apt-get install socat jq
sudo reboot
sudo su
curl -sL https://raw.githubusercontent.com/home-assistant/hassio-build/master/install/hassio_install | bash -s -- -m raspberrypi3
sudo reboot
```
#### NOTE! On this first reboot with hassio installed it can take 20 minutes or so to fully start.  Be patient!

## Security Measures Related to Website
Try to connect via browser: http://<192 local IP>:8123

Note that no password is required.

You will need to set the password from the shell.

### Update hassio configuration.yaml and secrets.yaml files.
Reconnect to raspbian shell as user pi.

Then change directories to where hassio is installed and update the configuration.yaml file.
```
cd /usr/share/hassio/homeassistant
sudo vi configuration.yaml
```
Uncomment this line by removing the #.  Line should still be indented.
```
api_password: !secret http_password
```
Now edit secrets.yaml file in the same directory and change the password "welcome" to something better.
This will be your password to log into your hassio website.
```
sudo vi secrets.yaml
# replace welcome with a real one# restart hassio
```

