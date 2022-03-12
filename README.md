# Creating and Using Containers Like a Boss

Image - the application, includes the configs etc
Container - A running instance of an Image

## Check Our Docker Install and Config

```
docker version

docker info

docker

docker container run

docker run
```

## Starting a Nginx Web Server

```
docker container run --publish 80:80 nginx

docker container run --publish 80:80 --detach nginx

docker container ls

docker container stop 690

docker container ls

docker container ls -a

docker container run --publish 80:80 --detach --name webhost nginx

docker container ls -a

docker container logs webhost

docker container top

docker container top webhost

docker container --help

docker container ls -a

docker container rm 63f 690 ode

docker container ls

docker container rm -f 63f

docker container ls -a
```

## Container VS. VM: It's Just a Process

```
docker run --name mongo -d mongo

docker container ps

docker top mongo

docker stop mongo

docker container ps

docker top mongo

docker start mongo

docker container ps

docker top mongo
```
## Assignment Answers: Manage Multiple Containers
```
docker container run -d -p 3306:3306 --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes MYSQL_RANDOM_ROOT_PASSWORD

docker container logs db

docker container run -d --name webserver -p 8080:80 httpd

docker container ps

docker container run -d --name proxy -p 80:80 nginx

docker container ps

docker container ls

docker container stop TAB COMPLETION

docker container ps -a

docker container ls -a

docker container rm

docker container ps -a

docker image ls
```

## What's Going On In Containers: CLI Process Monitoring

```
docker container run -d --name nginx nginx

docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql

docker container ls

docker container top mysql

docker container top nginx

docker container inspect mysql

docker container stats --help

docker container stats

docker container ls

## Getting a Shell Inside Containers: No Need for SSH

docker container run -help

docker container run -it --name proxy nginx bash

docker container ls

docker container ls -a

docker container run -it --name ubuntu ubuntu

docker container ls

docker container ls -a

docker container start --help

docker container start -ai ubuntu

docker container exec --help

docker container exec -it mysql bash

docker container ls

docker pull alpine

docker image ls

docker container run -it alpine bash

docker container run -it alpine sh
```

docker container exec -> on an existing container
docker container run -> start a new container

## Docker Networks: Concepts for Private and Public Comms in Containers

The --network flag enables us to place multiple containers in the same virtual network

```
docker container run -p 80:80 --name webhost -d nginx

docker container port webhost

docker container inspect --format '{{ .NetworkSettings.IPAddress }}' webhost
```

## Docker Networks: CLI Management of Virtual Networks

```
docker network ls

docker network inspect bridge

docker network ls

docker network create my_app_net

docker network ls

docker network create --help

docker container run -d --name new_nginx --network my_app_net nginx

docker network inspect my_app_net

docker network --help

docker network connect

docker container inspect TAB COMPLETION

docker container disconnect TAB COMPLETION

docker container inspect
```

## Docker Networks: DNS and How Containers Find Each Other

```
docker container ls

docker network inspect TAB COMPLETION

docker container run -d --name my_nginx --network my_app_net nginx:alpine

docker container inspect TAB COMPLETION

docker container exec -it my_nginx ping new_nginx

docker container exec -it new_nginx ping my_nginx

docker network ls

docker container create --help
```

## Assignment Answers: Using Containers for CLI Testing

```
docker container run --rm -it centos:7 bash

docker container ps -a

docker container run --rm -it ubuntu:14.04 bash

docker container ps -a
```

## Assignment Answers: DNS Round Robin Testing

```
docker network create dude

docker container run -d --net dude --net-alias search elasticsearch:2

docker container ls

docker container run --rm --net dude alpine nslookup search

docker container run --rm --net dude centos curl -s search:9200

docker container ls

docker container rm -f TAB COMPLETION
```

# Container Images, Where To Find Them and How To Build Them

## The Mighty Hub: Using Docker Hub Registry Images

http://hub.docker.com

```
docker image ls

docker pull nginx

docker pull nginx:1.11.9

docker pull nginx:1.11

docker pull nginx:1.11.9-alpine

docker image ls
```
## Images and Their Layers: Discover the Image Cache
```
docker image ls

docker history nginx:latest

docker history mysql

docker image inspect nginx
```
## Image Tagging and Pushing to Docker Hub
```
docker image tag -- help

docker image ls

docker pull mysql/mysql-server

docker image ls

docker pull nginx:mainline

docker image ls

docker image tag nginx bretfisher/nginx

docker image tag --help

docker image ls

docker image push bretfisher/nginx

docker --help

docker login

cat .docker/config.json

docker image push bretfisher/nginx

docker image push bretfisher/nginx bretfisher/nginx:testing

docker image ls

docker image push bretfisher/nginx:testing

docker image ls
```
## Building Images: The Dockerfile Basics
```
cd dockerfile-sample-1

vim Dockerfile
```
## Building Images: Running Docker Builds

WHen a line change in the dockerfile - every line after that will be re-ran so we want to make sure we put the least changing lines at the top and most changing at the bottom

```
docker image build -t customnginx .

docker image ls

docker image build -t customnginx .
```
## Building Images: Extending Official Images
```
cd dockerfile-sample-2

vim Dockerfile

docker container run -p 80:80 --rm nginx

docker image build -t nginx-with-html .

docker container run -p 80:80 --rm nginx-with-html

docker image ls

docker image tag --help

docker image tag nginx-with-html:latest bretfisher/nginx-with-html:latest

docker image ls

docker push
```
## Assignment Answers: Build Your Own Dockerfile and Run Containers From It
```
cd dockerfile-assignment-1

vim Dockerfile

docker build cmd

docker build -t testnode .

docker container run --rm -p 80:3000 testnode

docker images

docker tag --help

docker tag testnode bretfisher/testing-node

docker push --help

docker push bretfisher/testing-node

docker image ls

docker image rm bretfisher/testing-node

docker container run --rm -p 80:3000 bretfisher/testing-node
```

# Container Lifetime & Persistent Data: Volumes, Volumes, Volumes

Containers should be immutable and if something changes in the app we should be able to just create a new container instead.

What about persistent data? want to preserve data even when the container dies.

Docker solution:
1. Volumes: make special location outside of the container
2. Bind mounts: link container fs to the host

## Persistent Data: Data Volumes

```
docker pull mysql

docker image inspect mysql

docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql

docker container ls

docker container inspect mysql

docker volume ls

docker volume inspect TAB COMPLETION

docker container run -d --name2 mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True mysql

docker volume ls

docker container stop mysql

docker container stop mysql2

docker container ls

docker container ls -a

docker volume ls

docker container rm mysql mysql2

docker volume ls

docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql

docker volume ls

docker volume inspect mysql-db

docker container rm -f mysql

docker container run -d --name mysql3 -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
```

This creates a named volume with a specific location.
every other container will use the same volume if the name matches.

```
docker volume ls

docker container inspect mysql3

docker volume create --help
```
Shouldn't really need to use docker volume create.

## Persistent Data: Bind Mounting

```
cd dockerfile-sample-2

pcat Dockerfile

docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx

docker container run -d --name nginx2 -p 8080:80 nginx

docker container exec -it nginx bash
```

## Assignment Answers: Edit Code Running In Containers With Bind Mounts

```
docker run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve
```
# Making It Easier with Docker Compose: The Multi-Container Tool

1. configure relationships between containers
2. save our docker container run settings in easy-to-read file
3. create one liner developer environment startups
4. Docker compose automatically creates a shared network

Comprised of 2 separate but related things:
1. YAML-formatted file that descrives our solution options
   1. containers
   2. networks
   3. volumes
2. A CLI tool docker-compose used for local dev/ test automation with those YAML files

## Docker Compose and The Docker-compose.yml File

docker-compose.yml

https://docs.docker.com

## Trying Out Basic Compose Commands
```
pcat docker-compose.yml

docker-compose up

docker-compose up -d

docker-compose logs

docker-compose --help

docker-compose ps

docker-compose top

docker-compose down
```
## Assignment Answers: Build a Compose File for a Multi-Container Service
```
docker-compose.yml

docker pull drupal

docker image inspect drupal

docker-compose up

https://hub.docker.com

docker-compose down --help

docker-compose down -v
```
## Adding Image Building to Compose Files
```
docker-compose.yml

docker-compose up

docker-compose up --build

docker-compose down

docker image ls

docker-compose down --help

docker image rm nginx-custom

docker image ls

docker-compose up -d

docker image ls

docker-compose down --help

docker-compose down --rmi local
```
## Assignment Answers: Compose for Run-Time Image Building and Multi-Container Dev

In the compose file we have two purposes for the key-value:

1. If we only have image tag without build - it means to get from the docker.hub the latest image by that name and run it
2. If we have both "build" and "image" tags then it means to build an image from a dockerfile in the folder "build" and name it "image"

```
docker-compose up

docker-compose down

docker-compose up
```

# Swarm Basic Features and How to Use Them In Your Workflow

## Scaling Out with Overlay Networking

docker network create --driver overlay mydrupal

docker network ls

docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=mypass postgres

docker service ls

docker service ps psql

docker container logs psql TAB COMPLETION

docker service create --name drupal --network mydrupal -p 80:80 drupal

docker service ls

watch docker service ls

docker service ps drupal

docker service inspect drupal

## Scaling Out with Routing Mesh
```
docker service create --name search --replicas 3 -p 9200:9200 elasticsearch:2

docker service ps search
```
## Assignment Answers: Create a Multi-Service Multi-Node Web App
```
docker node ls

docker service ls

docker network create -d overlay backend

docker network create -d overlay frontend

docker service create --name vote -p 80:80 --network frontend -- replica 2 COPY IMAGE

docker service create --name redis --network frontend --replica 1 redis:3.2

docker service create --name worker --network frontend --network backend COPY IMAGE

docker service create --name db --network backend COPY MOUNT INFO

docker service create --name result --network backend -p 5001:80 COPY INFO

docker service ls

docker service ps result

docker service ps redis

docker service ps db

docker service ps vote

docker service ps worker

cat /etc/docker/

docker service logs worker

docker service ps worker
```
## Swarm Stacks and Production Grade Compose
```
docker stack deploy -c example-voting-app-stack.yml voteapp

docker stack

docker stack ls

docker stack ps voteapp

docker container ls

docker stack services voteapp

docker stack ps voteapp

docker network ls

docker stack deploy -c example-voting-app-stack.yml voteapp
```
## Using Secrets in Swarm Services
```
docker secret create psql_usr psql_usr.txt

echo "myDBpassWORD" | docker secret create psql_pass - TAB COMPLETION

docker secret ls

docker secret inspect psql_usr

docker service create --name psql --secret psql_user --secret psql_pass -e POSTGRES_PASSWORD_FILE=/run/secrets/psql_pass -e POSTGRES_USER_FILE=/run/secrets/psql_user postgres

docker service ps psql

docker exec -it psql.1.CONTAINER NAME bash

docker logs TAB COMPLETION

docker service ps psql

docker service update --secret-rm
```
## Using Secrets with Swarm Stacks
```
vim docker-compose.yml

docker stack deploy -c docker-compose.yml mydb

docker secret ls

docker stack rm mydb
```
## Assignment Answers: Create A Stack with Secrets and Deploy
```
vim docker-compose.yml

docker stack deploy - c docker-compose.yml drupal

echo STRING |docker secret create psql-ps - VALUE

docker stack deploy -c docker-compose.yml drupal

docker stack ps drupal
```
# Swarm App Lifecycle

## Using Secrets With Local Docker Compose
```
docker node ls

docker-compose up -d

docker-compose exec psql cat /run/secrets/psql_user

docker-compose 11

pcat docker-compose.yml
```
## Full App Lifecycle: Dev, Build and Deploy With a Single Compose Design
```
docker-compose up -d

docker inspect TAB COMPLETION

docker-compose down

docker-compose -f docker-compose.yml -f docker-compose.test.yml up -d

docker inspect TAB COMPLETION

docker-compose -f docker-compose.yml -f docker-compose.prod.yml config --help

docker-compose -f docker-compose.yml -f docker-compose.prod.yml config

docker-compose -f docker-compose.yml -f docker-compose.prod.yml config > output.yml
```
## Service Updates: Changing Things In Flight
```
docker service create -p 8088:80 --name web nginx:1.13.7

docker service ls

docker service scale web=5

docker service update --image nginx:1.13.6 web

docker service update --publish-rm 8088 --publish-add 9090:80

docker service update --force web
```
## Healthchecks in Dockerfiles
```
docker container run --name p1 -d postgres

docker container ls

docker container run --name p2 -d --health-cmd="pg_isready -U postgres || exit 1" postgres

docker container ls

docker container inspect p2

docker service create --name p1 postgres

docker service create --name p2 --health-cmd="pg_isready -U postgres || exit 1" postgres
```
# Container Registries: Image Storage and Distribution

## Docker Hub: Digging Deeper

https://hub.docker.com

## Docker Store: What Is It For?

https://store.docker.com

## Docker Cloud: CI/CD and Server Ops

https://cloud.docker.com

https://hub.docker.com

## Understanding Docker Registry

https://github.com/docker/distribution

https://hub.docker.com/registry

## Run a Private Docker Registry

docker container run -d -p 5000:5000 --name registry registry

docker container ls

docker image ls

docker pull hello-world

docker run hello-world

docker tag hello-world 127.0.0.1:5000/hello-world

docker image ls

docker push 127.0.0.1:5000/hello-world

docker image remove hello-world

docker image remove 127.0.0.1:5000/hello-world

docker container rm admiring_stallman

docker image remove 127.0.0.1:5000/hello-world

docker image ls

docker pull 127.0.0.1:5000/hello-world:latest

docker container kill registry

docker container rm registry

docker container run -d -p 5000:5000 --name registry -v $(pwd)/registry-data:/var/lib/registry registry TAB COMPLETION

docker image ls

docker push 127.0.0.1:5000/hello-world

## Using Docker Registry With Swarm

http://play-with-docker.com

docker node ls

docker service create --name registry --publish 5000:5000 registry

docker service ps registry

docker pull hello-world

docker tag hello-world 127.0.0.1:5000/hello-world

docker push 127.0.0.1:5000/hello-world

docker pull nginx

docker tag nginx 127.0.0.1:5000/nginx

docker push 127.0.0.1:5000/nginx

docker service create --name nginx -p 80:80 --replicas 5 --detach=false 127.0.0.1:5000/nginx

docker service ps nginx

# Kubernetes (k8s or Kube)

Kubectl: (pronounce Kube-control) CLI to configure and manages apps
Node: single server in the Kubernetes cluster
Kubelet: Kubernetes agent running on nodes
Control Plane: set of containers that manage the cluster

Masters in the control plane, and Nodes

## in each master we run docker:
- etcd container
  - API
  - Scheduler - schedule containers to pods
  - Control manager
  - Core DNS

## in Node run with Docker:
- Kubelet
- Kube-proxy

## Kubernetes Container Abstractions

- Pods: one ore mote containers running together on one Node
  - Basic unit of deployment. Containers are always in pods
- Controller: for creating updating pods and other objects
  - Many types: Deployment, ReplicaSet, Cronjob, DaemonSet, etc.
- Service: network endpoint to connect to a pod
- Namespace: filtered group of objects in cluster

## Kubectl commands
- run: changing to be only for pod creation
- create: create some resources via CLI or YAML
- apply: create/update anything via YAML

### Kubectl - creating Pods with kubectl

```
kubectl run my-nginx --image nginx
--output--
pod/my-nginx created
```
Can see the output with:
```
kubectl get pods
--output--
NAME       READY   STATUS    RESTARTS   AGE
my-nginx   1/1     Running   0          33s
```
To see everything we can get all:
```
kubectl get all 
--output--
NAME           READY   STATUS    RESTARTS   AGE
pod/my-nginx   1/1     Running   0          45s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   14m
```
### Kubectl - delete pods

```
kubectl delete pod my-nginx
```

### Kubectl - create deployment
This creates alot of backend stuff for us.
lets look at:
```
kubectl create deployment my-nginx --image nginx
--output--
deployment.apps/my-nginx created
```

Now:
```
kubectl get all
--output--
NAME                            READY   STATUS    RESTARTS   AGE
pod/my-nginx-6b74b79f57-8bxcj   1/1     Running   0          27s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   23m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx   1/1     1            1           27s

NAME                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-6b74b79f57   1         1         1       27s
```

To delete:
```
kubectl delete deployment my-nginx
```

## Scaling with ReplicaSets

```
kubectl create deployment my-apache --image httpd
```
To scale that up we use the command scale:
```
kubectl scale deploy/my-apache --replicas 2
```
Now when we look at the kubectl output we can see we have two pods (one is a replica)
```
kubectl get all
--output--
NAME                             READY   STATUS    RESTARTS   AGE
pod/my-apache-7b68fdd849-plm8r   1/1     Running   0          78s
pod/my-apache-7b68fdd849-r2zmw   1/1     Running   0          72s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   28m

NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-apache   2/2     2            2           78s

NAME                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/my-apache-7b68fdd849   2         2         2       78s
```
Here the control plane understand the need for two replicas and then the control plane assigns node to pod and Kubelet sees pod is needed and starts another container

## Get containers log

```
kubectl logs deploy/my-apache
--output--
Found 2 pods, using pod/my-apache-7b68fdd849-plm8r
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.0.9. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 10.1.0.9. Set the 'ServerName' directive globally to suppress this message
[Tue Feb 01 08:25:42.250407 2022] [mpm_event:notice] [pid 1:tid 139690800831808] AH00489: Apache/2.4.52 (Unix) configured -- resuming normal operations
[Tue Feb 01 08:25:42.250666 2022] [core:notice] [pid 1:tid 139690800831808] AH00094: Command line: 'httpd -D FOREGROUND'
```

To go into the logs simply write:
```
kubectl logs deploy/my-apache --follow --tail 1
```

## Labeling

We can use selector to select multiple containers to handle.
For example in logs:
```
kubectl logs -l run=my-apache
```

## Describe pods
For extended information regarding the pod simply:
```
kubectl get pods
```

Then use the pod name in the command:
```
kubectl describe pod my-apache-7b68fdd849-plm8r
--output--
Name:         my-apache-7b68fdd849-plm8r
Namespace:    default
Priority:     0
Node:         docker-desktop/192.168.65.4
Start Time:   Tue, 01 Feb 2022 10:25:29 +0200
Labels:       app=my-apache
              pod-template-hash=7b68fdd849
Annotations:  <none>
Status:       Running
IP:           10.1.0.9
IPs:
  IP:           10.1.0.9
Controlled By:  ReplicaSet/my-apache-7b68fdd849
Containers:
  httpd:
    Container ID:   docker://b316f7a90355edee609e3e4567fb1c3af98c3e3c9c443f675a60fdd002f4166a 
    Image:          httpd
    Image ID:       docker-pullable://httpd@sha256:5cc947a200524a822883dc6ce6456d852d7c5629ab177dfbf7e38c1b4a647705
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 01 Feb 2022 10:25:42 +0200
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bl2sz (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  kube-api-access-bl2sz:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s        
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s      
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  12m   default-scheduler  Successfully assigned default/my-apache-7b68fdd849-plm8r to docker-desktop
  Normal  Pulling    12m   kubelet            Pulling image "httpd"
  Normal  Pulled     12m   kubelet            Successfully pulled image "httpd" in 11.502142743s
  Normal  Created    12m   kubelet            Created container httpd
  Normal  Started    12m   kubelet            Started container httpd
```

## Kill a pod and automatically resurect it

Open a seconds terminal

on terminal 2:
```
kubectl get pods -w
```
To watch for changes in the pods

on main terminal (terminal 1) delete the pod:
```
kubectl delete pod/my-apache-7b68fdd849-plm8r
--output--
pod "my-apache-7b68fdd849-plm8r" deleted
```
Now you can see that if we look at terminal 2:
```
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-7b68fdd849-plm8r   1/1     Running   0          15m
my-apache-7b68fdd849-r2zmw   1/1     Running   0          15m
my-apache-7b68fdd849-plm8r   1/1     Terminating   0          17m
my-apache-7b68fdd849-cswhj   0/1     Pending       0          0s
my-apache-7b68fdd849-cswhj   0/1     Pending       0          0s
my-apache-7b68fdd849-cswhj   0/1     ContainerCreating   0          0s     
my-apache-7b68fdd849-plm8r   0/1     Terminating         0          17m    
my-apache-7b68fdd849-plm8r   0/1     Terminating         0          17m    
my-apache-7b68fdd849-plm8r   0/1     Terminating         0          17m    
my-apache-7b68fdd849-cswhj   1/1     Running             0          4s
```
Actually ran a new pod again (because we required the replicas to be 2 earlier)
We can validate by running:
```
kubectl get pods
--output--
NAME                         READY   STATUS    RESTARTS   AGE
my-apache-7b68fdd849-cswhj   1/1     Running   0          2m7s
my-apache-7b68fdd849-r2zmw   1/1     Running   0          19m
```
# Exposing Kubernetes Ports

## Service Types
```
kubectl expose
```
## Creating a ClusterIP Service
```
kubectl get pods -w

kubectl create deployment httpenv --image=bretfisher/httpenv

kubectl scale deployment/httpenv --replicas=5

kubectl expose deployment/httpenv --port 8888

kubectl get service

kubectl run --generator run-pod/v1 tmp-shell --rm -it --image bretfisher/netshoot -- bash

curl httpenv:8888

curl [ip of service]:8888

kubectl get service
```
## Creating a NodePort and LoadBalancer Service
```
kubectl get all

kubectl expose deployment/httpenv --port 8888 --name httpenv-np --type NodePort

kubectl get services

curl localhost:32334

kubectl expose deployment/httpenv --port 8888 --name httpenv-lb --type LoadBalancer

kubectl get services

curl localhost:8888

kubectl delete service/httpenv service/httpenv-np

kubectl delete service/httpenv-lb deployment/httpenv
```
## Kubernetes Services DNS
```
curl <hostname>

kubectl get namespaces

curl <hostname>.<namespace>.svc.cluster.local
```
# Kubernetes Management Techniques

## Run, Expose and Create Generators
```
kubectl create deployment sample --image nginx --dry-run -o yaml

kubectl create deployment test --image nginx --dry-run

kubectl create deployment test --image nginx --dry-run -o yaml

kubectl create job test --image nginx -dry-run -o yaml

kubectl expose deployment/test --port 80 --dry-run -o -yaml

kubectl create deployment test --image nginx

kubectl expose deployment/test --port 80 --dry-run -o -yaml

kubectl delete deployment test
```
## The Future of Kubectl Run
```
kubectl run test --image nginx --dry-run

kubectl run test --image nginx --port 80 --expose --dry-run

kubectl run test --image nginx --restart OnFailure --dry-run

kubectl run test --image nginx --restart Never --dry-run

kubectl run test --image nginx --scheduled "*/1 * * * *" --dry-run
```
## Imperative vs. Declarative
```
kubectl apply -f my-resources.yaml

kubectl run
```
# Moving to Declarative Kubernetes YAML

## Kubectl Apply
Can be applied to a whole directory of yamls
```
kubectl apply -f filename.yml

kubectl apply -f myfile.yaml

kubectl apply -f myyaml/

kubectl apply -f https://bret.run/pod.yml

curl -L https://bret.run/pod

start https://bret.run/pod.yml
```
## Building Your YAML Files
list every resource cluster support
```
kubectl api-resources
```
list different verions of APIs
```
kubectl api-versions
```

kind + apiversion will be the final determining factor on which actual resources you will get

spec: everything we need!

## Dry Runs and Diffs

To figure out what to put in each field in the YAML:
```
kubectl explain services --recursive -> every keys with kind support
kubectl explain services.spec -> sub-keys of the specs
kubectl explain services.spec.type -> description of field "type"
kubectl explain services.spec.template.spec.volumes.nfs.server
```

To see changes in the YAMLs the currently deployed YAMLs:
```
kubectl apply -f app.yml --dry-run

kubectl apply -f app.yml --server-dry-run

kubectl diff -f app.yml
```
## Labels and Label Selectors

To select specific pods for nodes we use the:
```
selector:
    matchLabels:
      app: app-nginx
```
And in order to mark pods with this label for example, we can use:
```
template:
    metadata:
      labels:
        app: app-nginx
```
The "template" is where we describe the pods.

CMD use of labels
```
kubectl get pods -l app=nginx

kubectl apply -f myfile.yaml -l app=nginx
```

## Cleanup

```
kubectl get all

kubectl delete <resource type>/<resource name>
```

# Your Next Steps, and The Future of Kubernetes

## Storage

StatefulSets resource type making pods sticky,
but you should use db-as-a-service whenever you can.
Most pods should be stateless.
Can create volumes in pods with the "Volumes" resource.
"Volumes" is tied to a pod, all containers in a single pod can share them.
"PersistentVolumes" is a volume created on a cluster level - outlives a pod

https://kubernetes.io/docs/concepts/storage/volumes/

## Ingress

No services work at the OSI layer 7 (i.e. HTTP)
TO support this we can use the "Ingress" to route traffic based on hostname, url etc.
More info:

https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/


## CRD's and The Operator Pattern

can add 3rd party Resources and controllers
Operator: automate deployment and management of complex apps, can learn more:

https://kubernetes.io/docs/concepts/extend-kubernetes/operator/


## Higher Deployment Abstractions

How to deploy apps with kubernetes?
many many tools!

Helm is the most popular

Full list of kubernetes application management tools:

https://docs.google.com/spreadsheets/d/1FCgqz1Ci7_VCz_wdh8vBitZ3giBtac_H8SBw4uxnrsE/edit#gid=0


Compose with Kubernetes

https://github.com/docker/compose-on-kubernetes/tree/master/docs


In order to customize YAML you can use:

https://kubernetes.io/blog/2018/05/29/introducing-kustomize-template-free-configuration-customization-for-kubernetes/


## Kubernetes Dashboard
Should use passwords and proxies in order to save the privacy and secure you dashboard

https://github.com/kubernetes/dashboard

## Namespaces and Context

Namespaces limit scope, aka "virtual clusters" - does not relate to docker and linux namespaces

https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/

```
kubectl get namespaces

kubectl get all -all-namespaces

~/.kube/config file

kubectl config get-contexts

kubectl config set*
```

# Security

https://github.com/BretFisher/ama/issues/17

## Docker Cgroups and Namespaces

linux's cgroup and namespaces

cgroup - limiting container's resources on the VM - like RAM, CPU etc.
namespaces - isolate containers from other processes in the VM

## Docker Engine's out-of-the-box security features

Docker enables by the default:
1. apparmor
2. seccomp
3. small whitelist capabalities
4. SElinux

## Docker Bench - The host configuration Scanner

Free tool at:

https://github.com/docker/docker-bench-security

That will scan the configuration and find security problems based on CSI docker banchmark

## Using USER in Dockerfiles to Avoid running as Root

do not run containers as root

https://docs.docker.com/engine/reference/builder/#user

Sample use of USER:
```
7 lines (9 sloc)  252 Bytes
   
FROM node:12-slim

EXPOSE 3000

RUN mkdir /app && chown -R node:node /app

WORKDIR /app

USER node

COPY --chown=node:node package.json package-lock*.json ./

RUN npm install && npm cache clean --force

COPY --chown=node:node . .

CMD ["npm", "start"]
```

Notice the chown we now need to do

## Docker user namespaces for extra host security

Enable user namespaces is a guard that will map the user inside the container to a non-priviledges user in the host.
This means that if a hacker was able to escape the container it will still be an unpriviledges user in the host

## Code Repo and Image Scanning for CVE's

https://snyk.io/

Scan you repo for CVE's

https://github.com/aquasecurity/microscanner

https://github.com/aquasecurity/trivy

Scan images for CVE's vulnerability

## Custom security profiling

https://docs.docker.com/engine/security/trust/

https://docs.docker.com/engine/security/seccomp/

https://docs.docker.com/engine/security/apparmor/

## Docker Rootless Mode

Feature that can run docker deamon without root

https://github.com/docker/engine/blob/v19.03.0-rc3/docs/rootless.md

# Advance Docker features

## ENTRYPOINT vs CMD

Entrypoint will run when using docker run
CMD will be added to the ENTRYPOINT and run with it *only when* the docker run command line did not specify anything after the image!

```
ENTRYPOINT ["ping"]

CMD ["google.com"]
```

if we run the image with:
```
docker run my-img
```
it will ping google

but if we will run
```
docker run my-img amazon.com
```

the container will ping amazon instead - it will replace the CMD with what we will write in the docker run command.

Mostly you should use an entrypoint.sh script to set everything up (ssh keys, env variable, etc.) then put the last line:
```
exec "$@"
```
so that the CMD will now run after the environment was set
