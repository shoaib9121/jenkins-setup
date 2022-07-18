# Jenkins setup guide
Setup Jenkins locally in virtual machine

## Up Jenkins using Vagrant

- Make sure vagrant is installed on host machine.
- cd vagrant directory where Vagrantfile is located.
- Change VM network to public. `nano Vagrantfile` and look for line `config.vm.network "public_network"` and uncomment it.
- `vagrant up`
- Select Wifi (wireless) network. Enter a corresponding digit against Wifi connected.
- `vagrant ssh` (to enter into vagrant shell)
- `mkdir jenkins && cd jenkins`
- create `docker-compose.yml` file and it should contain code snippet similar as below

```
# docker-compose.yaml

version: '3.8'

services:
  jenkins:
    image: jenkins/jenkins:lts
    user: root
    restart: always
    container_name: jenkins
    privileged: true
    ports:
     - 2376:8080
     - 50000:50000
    volumes:
      - ./jenkins_configuration:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - /usr/bin/docker:/usr/bin/docker
  agent:
    image: jenkins/ssh-agent:jdk11
    privileged: true
    user: root
    container_name: agent
    expose:
      - 22
    environment:
      - JENKINS_AGENT_SSH_PUBKEY=ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCf0V8aWSP/WXDwVtBg/VXF3GqwGWYPDi0TOPKaU3fzicLH+LFGBDSytSeNJPvEc6Xw2P9TdzlACo/xOXF2P+gSyrsDR+mdd2PZD4HTVSox8KGyDQAy1nY0AzO3M6g5IrplgMPE1DbFpD9awmTrTcmRjAH0mXcGdA38M1ox0PCSVLkC8SclkTxYTHNbLBx2Fi5BSjGTDwmB7afcc7LxWcKBDtVtt2FvhM/s/bY2i+XfBkecemIgJfjN8NMTLkHKHR1KxP0VbLDUv0/wAJV6NlCmesJeujj5DCyRdyYye13J/bhgK4NQPLvxvnlVAsQUPg1WG1kav5ZBmseRKj50AhzPajilQHMBUigF+lLBVxHObKydS5hjomHv+BLBsIRtwzzdeWbbgHlMchc/lZ5SJLvTEksm3Uye21S1cevEYoWjN/FCBDWN4E9kyxafVQL9zoWnMPmTP+0O/+w+tRhK8TVteqKEoCO0YeF5fpEiqiFVM9nZ2SqcIIOTNISGpUili+U= vagrant@vagrant
```
- Execute command `docker-compose run` (it will pull jenkins image if not pulled already).
- Notice we have `JENKINS_AGENT_SSH_PUBKEY` in `docker-compose.yml`. This is `ssh-rsa` public key generated from `ssh-keygen` in the same directory where `docker-compose.yml` is located.

