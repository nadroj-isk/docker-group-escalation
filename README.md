# docker-group-escalation

Simple idea that I had one day when looking up how to run `docker`  commands without sudo.

Heavily based on code by [chrisfosterelli](https://github.com/chrisfosterelli/dockerrootplease) and [KrustyHack](https://github.com/KrustyHack/docker-privilege-escalation)

# Usage
`chmod +x ./exploit.sh`

`./exploit`

# When would this happen?

One of the most searched things about Docker is "how to run docker commands without sudo." Since docker binds to a Unix socket instead of a TCP port it needs to run as a root user. 
Therefore to run docker commands its important to run it with sudo.

To get around this you can do the following steps
1. Add the docker group if its not in existence

` sudo groupadd docker`

2. Add yourself to said group

` sudo gpasswd -a $USER docker`

3. Activate the changes

`newgrp docker`

4. Run docker commands without sudo

Unfortunatley now your account is now running a process that has root access, without first escalating your account. This can lead to many different attacks.

For instance the attack discussed in this repo.

# What the attack does

First the shell script will check if you are running as root. If you are there is no need to continue. After that it will check
if you are in the docker group, if you're not this script cannot achieve its goal. If you are in the docker group it will then
build the docker image based on the Dockerfile and will tag it with the name rootesc. The dockerfile is one line: FROM Alpine. This image will pull the Alpine distro (a extremly small linux distro) and then exit.

However you can stop it from exiting when you run the container.  Next in the script it does just that. It runs the image rootsec in interactive mode with a pseudo terminal (-it) and will give it a volume
of '/' on the host computer and bind it to '/home' on the docker container.

After running that command you will be dropped into the docker container. If you change directories to /home you will now have a shell with root access of the host machine!

