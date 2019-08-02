# CassandraOnDockerDemo
Implementing Cassandra in Docker Containers

## 1. Create Docker Account
As you may have assumed by the title, you will need Docker installed with access to the Docker Hub repository to pull the Cassandra images. Go to [Docker](https://hub.docker.com/) to create an account.

## 2. Download Docker
Containers are platform independent, so choose your OS for download. 
After installing, you will have access to the docker terminal from the command line terminal.
- Windows:
- [Windows Download](https://docs.docker.com/docker-for-windows/install/)
- [Mac Download](https://docs.docker.com/docker-for-mac/install/)


## 3. Pull Docker and Cassandra Images
First, login to Docker with the following command (all subsequent requests will be using these credentials):
```
docker login
```
Next, pull the The DataStax Server Image which is DataStax distribution of Apache Cassandra with additional capabilities of Search Engine, Spark Analytics and Graph Components.
```
docker pull datastax/dse-server:latest
```

## 4. Start your first Cassandra node on Docker
The `docker run` command will create containers from the pulled images.
Create *node1* using the following command:
```
docker run --name node1 -p 9042:9042 -e CASSANDRA_CLUSTER_NAME=MyCluster -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=datacenter1 -d cassandra
```




### Docker settings
Ensure that the CPUs=2 and memory= ~4GB 
Each node will require about 1.5 GB RAM to work properly and we will have 2 nodes.

### Pull official Cassandra image
Run: docker pull cassandra in terminal

Datacenters:
dc1
dc2

Cluster name:
LoClusters

Nodes:
node1 - dc1
node2 - dc1
node3 - dc2

### Start First Node

```
docker run --name node1 -p 9042:9042 -e CASSANDRA_CLUSTER_NAME=LoClusters -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=dc1 -d cassandra
```


### Inspect node1 to make sure that node2 & node3 can use node1 as seed node and join cluster

docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1

Produces: 172.17.0.2

docker run --name node2 -e CASSANDRA_SEEDS="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1)" -e CASSANDRA_CLUSTER_NAME=LoClusters -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=dc1 -d cassandra

Produces: 10b6eb0a3f250e478bc1212074ce8eab95c4a9d8b1f23e2f44e85aadfeb77a66
 
docker run --name node3 -e CASSANDRA_SEEDS="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1)" -e CASSANDRA_CLUSTER_NAME=LoClusters -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=dc2 -d cassandra

Produce: e419c038e04578b6c84444adda0136bb2483f112eb2cf4e147b0b18a240c31dd


### Connect node1 and runs "nodetool status" command. You will then see ...

docker exec -ti node1 nodetool status
