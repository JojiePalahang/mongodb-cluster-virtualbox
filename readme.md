# Mongo DB Cluster on VirtualBox using Docker
 [Source](http://www.sohamkamani.com/blog/2016/06/30/docker-mongo-replica-set)

# Requirements 
 - Vagrant 
 - Docker
 - Internet Connection

# To Use
 - Git clone the project `git clone git@github.com:JojiePalahang/mongodb-cluster-virtualbox.git`
 - Goto the project directory `cd mongodb-cluster-virtualbox`
 - run the command `vagrant up`
 - Copy your public ssh key for passwordless login (`brew install ssh-copy-id` if you need it on OSX)
 - `ssh-copy-id vagrant@192.168.88.100` (password is 'vagrant')
    - **Important**: Test you are able to connect *without password* to your VM
 - `ssh -v vagrant@192.168.88.100`
    - If you are able to log in without password you're good to proceed.
 - Install docker into the VM
 - `docker-machine create --driver generic --generic-ssh-user vagrant --generic-ip-address 192.168.88.100 mongodb-cluster`
 - Point your workstation docker client to the VM
 - `eval "$(docker-machine env mongodb-cluster)"`
 - Pull the latest mongodb image from docker hub `docker pull mongo`
 - Set up the network where mongodb instance communicate `docker network create mongo-cluster-network`
 - Set up our containers for our replica set 
    - **Important**: By default 1 replica set is composed of 3 running mongodb instance.
    - `docker run -p 30001:27017 --name mongo1 --net mongo-cluster-network mongo mongod --replSet mongo-local-set`
    - `docker run -p 30002:27017 --name mongo2 --net mongo-cluster-network mongo mongod --replSet mongo-local-set`
    - `docker run -p 30003:27017 --name mongo3 --net mongo-cluster-network mongo mongod --replSet mongo-local-set`  

        - *** Legend ***
        - `docker run` : Start a container from an image
        - `-p 30001:27017` : Expose port 27017 in our container, as port 30001 on the localhost
        - `--name mongo1` : name this container “mongo1”
        - `--net mongo-cluster-network` : Add this container to the “mongo-cluster-network” network.
        - `mongo` : the name of the image we are using to spawn this container
        - `mongod --replSet mongo-local-set` : Run mongod while adding this mongod instance to the replica set named “mongo-local-set” 
 - Set up replication that will make 3 mongodb instance servers will start communicate to each instance.
 - Connect to the mongo shell in any of the containers `docker exec -it mongo1 mongo`
 - Use test schema for executing commands `use test`
 - we first create our configuration run the command   
 - `config = {"_id" : "mongo-local-set","members" : [{"_id" : 0,"host" : "mongo1:27017"},{"_id" : 1,"host" : "mongo2:27017"	},{"_id" : 2,"host" : "mongo3:27017"}]}`
 - run this command to execute our configuration `rs.initiate(config)`
 - wait until `mongo-local-set:PRIMARY>` display
 - <b>Congratulations were done</b>.

# To Test
 - Insert record on PRIMARY instance, then check SECONDARY instance if the record exist.


# Architecture Diagram

 ![Architecture](docs/architecture-diagram.png?raw=true)


 # License
 MIT