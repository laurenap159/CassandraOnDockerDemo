# CassandraOnDockerDemo
Implementing a Cassandra in Docker Containers

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


docker run --name node1 -p 9042:9042 -e CASSANDRA_CLUSTER_NAME=LoClusters -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=dc1 -d cassandra

Produces: 5631b7af91567ac6304cbc5e6059dac0dba6f37ba64fc56f23f0fe2a80d10175

### Inspect node1 to make sure that node2 & node3 can use node1 as seed node and join cluster

docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1

Produces: 172.17.0.2

docker run --name node2 -e CASSANDRA_SEEDS="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1)" -e CASSANDRA_CLUSTER_NAME=LoClusters -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=dc1 -d cassandra

Produces: 10b6eb0a3f250e478bc1212074ce8eab95c4a9d8b1f23e2f44e85aadfeb77a66
 
docker run --name node3 -e CASSANDRA_SEEDS="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' node1)" -e CASSANDRA_CLUSTER_NAME=LoClusters -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=dc2 -d cassandra

Produce: e419c038e04578b6c84444adda0136bb2483f112eb2cf4e147b0b18a240c31dd


### Connect node1 and runs "nodetool status" command. You will then see ...

docker exec -ti node1 nodetool status
