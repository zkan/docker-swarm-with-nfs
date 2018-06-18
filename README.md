# docker-swarm-with-nfs

## Start up and running

### Reproduce using vagrant

In vagrant environment we simulate production environment by using 1 nfs server, 1 master node and 2 worker node

* Run `vagrant up` to spin up virtual machine
* After vagrant up successful you can ssh into 4 virtual machines
  * Run `vagrant ssh nfs` to ssh to nfs server
  * Run `vagrant ssh master` to ssh to master
  * Run `vagrant ssh a1` to ssh to worker a1
  * Run `vagrant ssh a2` to ssh to worker a2

### Create docker-swarm environment to deploy stack

First we need to initial docker swarm environment for our setup

* Run `vagrant ssh master` to initial docker swarm setup
* Run `docker swarm init` we expect to get worker join-token we will use in a1, a2 (if doesn't work execute as a root)
* Run `vagrant ssh a1` and use token from master to let a1 join as a worker node
* Run `vagrant ssh a2` and use token from master to let a2 join as a worker node

All done setup for docker swarm environment!

### Deploy docker-compose stack

* Run `vagrant ssh master` after that we copy docker-compose.yaml from this reposity put it in master node
* Run `docker stack deploy -c <docker-compose-file> <name-of-stack>` after deployment docker will create 2 replicas of ubuntu and 2 replicas of centos using volume from nfs server

After that we can ssh into our service
* Run `vagrant ssh a1` or `vagrant ssh a2` and do `docker ps` if there's a container we can `docker exec -it <container-id> /bin/sh` to inspect into our container and do `ls / | grep 'myvol'` to see if there's myvol folder
* Run `cd /myvol` and then create file `touch hello.py`

After we created some files we can ssh to another service to check if file we created is there

## Configure docker-compose.yaml for nfs volume

### Configure nfs volumes

```yaml
volumes:
  <volume-name>:
    driver: local
    driver_opts:
      type: nfs
      o: "addr=<nfs-server-ip>,rw,soft,nolock"
      device: ":/home"
```
