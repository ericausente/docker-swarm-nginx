# docker-swarm-nginx

This guide will walk you through the steps of setting up your first Docker Swarm cluster. Docker Swarm is Docker's native container orchestration tool that allows you to manage multiple containers deployed across a cluster of machines.

## Prerequisites

- Docker installed on all nodes. See the [Docker installation guide for Ubuntu](https://docs.docker.com/engine/install/ubuntu/).

## Cluster Nodes

- **Manager Node**: `10.201.10.168` (Ubuntu)
- **Worker Node**: `10.201.10.149` (Ubuntu 20)

## Initialization

On the manager node, initialize the swarm:

```
sudo docker swarm init
```

You will receive an output similar to the following:
```
$ ericausente@ericausente-virtual-machine:/etc/apt/apt.conf.d$ sudo docker swarm init
Swarm initialized: current node (74ydy5uslm5u2ryb3d5sdqake) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token <TOKEN> 10.201.10.149:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

```


The output includes commands to add worker nodes to the swarm.

## Adding Nodes

To add a worker node to your swarm, use the command provided in the initialization output:
```
ericausente@ericausente-virtual-machine:~$ sudo docker swarm join --token <TOKEN>  10.201.10.149:2377
This node joined a swarm as a worker.
```

Computers in a Swarm cluster are called nodes. Nodes can play two roles in a Swarm:

-  The role of manager nodes is to manage the cluster; you can execute Swarm management commands on manager nodes
-  The role of worker nodes is to run your containers that do the actual job (like running a web server).

    
## Node Management

### List all nodes in the swarm:
```
$ sudo docker node ls
ID                            HOSTNAME                      STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
74ydy5uslm5u2ryb3d5sdqake *   ericausente-virtual-machine   Ready     Active         Leader           24.0.7
b918cg3ntovvhfjl2wx1g1z9w     ericausente-virtual-machine   Ready     Active                          19.03.4
```

## Deploying Services

### Deploy an NGINX service:

```
docker service create \
  --name my-service \
  --replicas 3 \
  nginx:alpine
```

Sample output: 
```
$ sudo docker service create   --name my-service   --replicas 3   nginx:alpine
image nginx:alpine could not be accessed on a registry to record
its digest. Each node will access nginx:alpine independently,
possibly leading to different nodes running different
versions of the image.
ognnw5nj4nwr09yyipqtuwvm7
overall progress: 3 out of 3 tasks
1/3: running   [==================================================>]
2/3: running   [==================================================>]
3/3: running   [==================================================>]
verify: Service converged
```

## Managing Services

### List all services:
```
docker service ls
```


### Scale a service:
```
docker service scale my-nginx=5
```

Sample output:
```
$ sudo docker service scale my-service=5
my-service scaled to 5
overall progress: 5 out of 5 tasks
1/5: running   [==================================================>]
2/5: running   [==================================================>]
3/5: running   [==================================================>]
4/5: running   [==================================================>]
5/5: running   [==================================================>]
verify: Service converged
```


### Update a service:
```
sudo docker service update --image nginx:latest my-service
```

Sample output:
```
$ sudo docker service update --image=nginx:latest my-service
my-service
overall progress: 5 out of 5 tasks
1/5: running   [==================================================>]
2/5: running   [==================================================>]
3/5: running   [==================================================>]
4/5: running   [==================================================>]
5/5: running   [==================================================>]
verify: Service converged
```


### Inspect a service:

```
sudo docker service inspect my-service
```

Sample output:
```

~$ sudo docker service inspect my-service
[
    {
        "ID": "ognnw5nj4nwr09yyipqtuwvm7",
        "Version": {
            "Index": 90
        },
        "CreatedAt": "2023-11-07T08:43:42.627013182Z",
        "UpdatedAt": "2023-11-07T08:47:45.367441496Z",
        "Spec": {
            "Name": "my-service",
            "Labels": {},
            "TaskTemplate": {
                "ContainerSpec": {
                    "Image": "nginx:latest@sha256:86e53c4c16a6a276b204b0fd3a8143d86547c967dc8258b3d47c3a21bb68d3c6",
                    "Init": false,
                    "StopGracePeriod": 10000000000,
                    "DNSConfig": {},
                    "Isolation": "default"
                },
                "Resources": {
                    "Limits": {},
                    "Reservations": {}
                },
                "RestartPolicy": {
                    "Condition": "any",
                    "Delay": 5000000000,
                    "MaxAttempts": 0
                },
                "Placement": {
                    "Platforms": [
                        {
                            "Architecture": "amd64",
                            "OS": "linux"
                        },
                        {
                            "OS": "linux"
                        },
                        {
                            "OS": "linux"
                        },
                        {
                            "Architecture": "arm64",
                            "OS": "linux"
                        },
                        {
  ...
```

##  Removing Services

### Remove a service from your swarm:
```
docker service rm my-service
```
