# CassandraOnDockerDemo
Implementing Cassandra in Docker Containers

## 1. Create Docker Account
As you may have assumed by the title, you will need Docker installed with access to the Docker Hub repository to pull the Cassandra images. Go to [Docker](https://hub.docker.com/) to create an account.

## 2. Download Docker
Containers are platform independent, so choose your OS for download. 
After installing, you will have access to the docker terminal from the command line terminal.
- [Windows Download](https://docs.docker.com/docker-for-windows/install/)
- [Mac Download](https://docs.docker.com/docker-for-mac/install/)

#### Docker settings
Go to Docker > Preferences > Advanced to adjust computing settings.
Ensure that the CPUs=2 and memory= ~5GB 
Each node will require about 1.5 GB RAM to work properly and we will have 3 nodes.


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
## 5. Add two more nodes to the cluster
Add *node2*:
```
docker run --name node2 -e CASSANDRA_SEEDS="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1)" -e CASSANDRA_CLUSTER_NAME=MyCluster -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=datacenter1 -d cassandra
```
Add *node3*:
```
docker run --name node3 -e CASSANDRA_SEEDS="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1)" -e CASSANDRA_CLUSTER_NAME=MyCluster -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=datacenter2 -d cassandra
```
#### Now, we have 3 nodes between 2 datacenters.
Run `docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1` for each node to retrieve the IP address.

Run `docker exec -ti node1 nodetool status` for each node to see the status of the node.


