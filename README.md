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
LoCluster

Nodes:
node1 - dc1
node2 - dc1
node3 - dc2

### Start First Node

'''
docker run --name cas1 -p 9042:9042 -e CASSANDRA_CLUSTER_NAME=MyCluster -e CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch -e CASSANDRA_DC=datacenter1 -d cassandra
'''
