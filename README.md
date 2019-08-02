# CassandraOnDockerDemo
Implementing Cassandra in Docker Containers

## 1. Create Docker Account
You will need Docker installed with access to the Docker Hub repository to pull the Cassandra images. Go to [Docker](https://hub.docker.com/) to create an account. Save these credentials for step 3.

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

## 6. Execute CQL commands in the terminal
Run `docker exec -ti node1 cqlsh` to run CQL shell.

## 7. Create a keyspace which will be replicated to datacenter1 & datacenter2
In CQL shell, run the following the create a keyspace:
```
CREATE KEYSPACE mykeyspace
WITH replication = {
	'class' : 'NetworkTopologyStrategy',
	'datacenter1' : 1,
	'datacenter2' : 1
};
```
You know have a keyspace in both datacenters. Run `DESCRIBE SCHEMA;` to view your keyspace and replication strategy.

## 8. Create a table: *animals*
In CQL shell, run the following command to create a table called *animals*:
```
CREATE TABLE animals
(animalId uui, 
name varchar, 
description varchar, 
create_date timestamp, 
PRIMARY KEY (animalid) );
```
To view table in keyspace, run `DESCRIBE KEYSPACE mykeyspace;`

## 9. Insert data into your tables
Find *insert_animal_queries.txt* in my repository for this project. 
You can copy all the insert queries and paste them into CQL shell to add data to the table. 
Run `DESCRIBE KEYSPACE mykeyspace;`
Run `SELECT COUNT(animalId) FROM mykeyspace.animals;` to view the number of records inserted into your database.

# 10. Schema
