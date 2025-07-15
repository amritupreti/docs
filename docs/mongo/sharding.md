# Sharding

> MongoDB sharding is a method of distributing data across multiple servers or nodes, known as "shards" to handle large datasets and high user concurrency effectively.

---

## Benefits of Sharding
- **Cost-effective scaling:** Use standard, affordable hardware instead of investing in specialized, high-cost systems.
- **Performance optimization:** Tailor hardware and resources to the needs of individual collections for better efficiency.
- **Increased resilience:** Limit the impact of failures by containing them within individual shards.
- **Operational simplicity:** Adopt sharding gradually, minimizing disruption and complexity during scaling.

> For high-throughput workloads, rapidly growing datasets, multi-tenant applications, and global deployments, MongoDB sharding delivers sustainable scaling without the complexity traditionally associated with distributed databases. - [MongoDB Sharding](https://www.mongodb.com/resources/products/capabilities/sharding)

---

## When to choose Sharded Clusters

- **Large datasets**: When your data exceeds the capacity of a single server.
- **High throughput**: When you need to handle a large number of read and write operations.
- **Geographically distributed data**: When you want to distribute data across multiple locations for better performance and availability.
- **Scalability**: When you anticipate significant growth in data volume or user load.
- **Multi-tenant applications**: When you need to isolate data for different tenants while maintaining a single database instance.
- **High availability**: When you require a system that can continue to operate even if some nodes fail.

---

## How to distribute workload
There are two main approaches to distribute workload in MongoDB sharding:

1. **Dedicated Shards**: Move entire **collections** to specific shards based on their purpose or usage patterns.

2. **Collection Partitioning**: Split a single **collection** across multiple shards using a **shard key** to distribute documents.

---

## Sharding Architecture
MongoDB sharding architecture consists of the following components:

- **Shards**: The individual databases that store the data.
- **Config Servers**: Store metadata and configuration settings for the sharded cluster.
- **Mongos**: The query router that directs client requests to the appropriate shard based on the sharding key.

---

## How to choose a Shard Key
Choosing an effective shard key is crucial for optimal performance and scalability. Here are some guidelines:

- **Cardinality**: The shard key should have high cardinality, meaning it should have many unique values to ensure even distribution of data across shards.
- **Query Patterns**: Consider the most common query patterns. The shard key should align with these patterns to minimize cross-shard queries.
- **Write Distribution**: Ensure that the shard key allows for balanced write operations across shards to avoid hotspots.
- **Avoid Monotonic Keys**: Avoid using monotonically increasing keys (like timestamps) as shard keys, as they can lead to uneven data distribution.
- **Experiment**: Pick a shard key based on your data and workload characteristics, and test its performance.

---

## Sharding Example Using Docker
### Project Structure
Source code: [https://github.com/amritupreti/mongo/tree/sharding](https://github.com/amritupreti/mongo/tree/sharding)

Here's the project structure for setting up a MongoDB sharded cluster using Docker:
```plaintext title="sharding"
.
├── cfgsvr
│   ├── docker-compose.yml
├── shard1
│   ├── docker-compose.yml
├── shard2
│   ├── docker-compose.yml
├── shard3
│   ├── docker-compose.yml
└── mongos
    └── docker-compose.yml
```

I'll be creating a network named `mongoCluster`, `config server` with `3 replicas`, `3 shards` with `3 replicas` each, and a `mongos router`.

---

### Create a Docker Network
```bash
docker network create mongoCluster 
```

---

### Config Server
#### Create Config Server
Create a `docker-compose.yml` file in the `cfgsvr` directory:
```bash
mkdir cfgsvr
cd cfgsvr
touch docker-compose.yml
```

Define the `docker-compose.yml` file as follows:
```yaml title="cfgsvr/docker-compose.yml"
services:
  cfgsvr1:
      image: mongo:latest
      command: mongod --configsvr --replSet cfgrs --port 27017 --dbpath /data/db
      volumes:
        - cfgsvr1:/data/configdb
      networks:
        - mongoCluster
      container_name: cfgsvr1
      healthcheck:
        test: ["CMD", "mongosh", "--host", "cfgsvr1:27017", "--eval", "db.adminCommand('ping')"]
        interval: 30s
        timeout: 10s
        retries: 5
  cfgsvr2:
    image: mongo:latest
    command: mongod --configsvr --replSet cfgrs --port 27017 --dbpath /data/db
    volumes:
      - cfgsvr2:/data/configdb
    networks:
      - mongoCluster
    container_name: cfgsvr2
    healthcheck:
      test: ["CMD", "mongosh", "--host", "cfgsvr2:27017", "--eval", "db.adminCommand('ping')"]
      interval: 30s
      timeout: 10s
      retries: 5
  cfgsvr3:
    image: mongo:latest
    command: mongod --configsvr --replSet cfgrs --port 27017 --dbpath /data/db
    volumes:
      - cfgsvr3:/data/configdb
    networks:
      - mongoCluster  
    container_name: cfgsvr3
    healthcheck:
      test: ["CMD", "mongosh", "--host", "cfgsvr3:27017", "--eval", "db.adminCommand('ping')"]
      interval: 30s
      timeout: 10s
      retries: 5

volumes:
  cfgsvr1:
  cfgsvr2:
  cfgsvr3:  
networks:
  mongoCluster:
    driver: bridge
    name: mongoCluster
    external: true
```

#### Start Config Server
Run the following command in the `cfgsvr` directory to start the config server:
```bash
docker compose up -d
```

#### Initialize Replica Set
1. Login to one of the config server containers:
```bash
docker exec -it cfgsvr1 mongosh
```

2. Run the following command to initialize the replica set:
```javascript title="cfgsvr/replicaSet.js"
rs.initiate({
  _id: "cfgrs",
  members: [
    { _id: 0, host: "cfgsvr1:27017" },
    { _id: 1, host: "cfgsvr2:27017" },
    { _id: 2, host: "cfgsvr3:27017" }
  ]
})
```

3. Check the status of the replica set, it takes a few seconds for the replica set to elect a primary node.
    ```javascript
    rs.status()
    ```


> **NOTE**: Notice, we are using hostname `cfgsvr1`, `cfgsvr2`, and `cfgsvr3`. It will be resolved by Docker's internal DNS since all the services are in the same network `mongoCluster`.

---

### Shards
#### Create Shards
Here's how to create each shard with 3 replicas. The process is similar for `shard2` and `shard3`, just change the names accordingly.

Change to `project directory` and create a `docker-compose.yml` file in the `shard1` directory:
```bash
mkdir shard1
cd shard1
touch docker-compose.yml
```

Define the `docker-compose.yml` file as follows:
```yaml title="shard1/docker-compose.yml"
services:
  shard1svr1:
    image: mongo:latest
    command: mongod --shardsvr --replSet shard1rs --port 27017 --dbpath /data/db 
    volumes:
      - shard1svr1:/data/db
    networks:
      - mongoCluster
    container_name: shard1svr1
    healthcheck:
      test: ["CMD", "mongosh", "--host", "shard1svr1:27017", "--eval", "db.adminCommand('ping')"]
      interval: 30s
      timeout: 10s
      retries: 5
  
  shard1svr2:
    image: mongo:latest
    command: mongod --shardsvr --replSet shard1rs --port 27017 --dbpath /data/db
    volumes:
      - shard1svr2:/data/db
    networks:
      - mongoCluster
    container_name: shard1svr2
    healthcheck:
      test: ["CMD", "mongosh", "--host", "shard1svr2:27017", "--eval", "db.adminCommand('ping')"]
      interval: 30s
      timeout: 10s
      retries: 5

  shard1svr3:
    image: mongo:latest
    command: mongod --shardsvr --replSet shard1rs --port 27017 --dbpath /data/db
    volumes:
      - shard1svr3:/data/db 
    networks:
      - mongoCluster
    container_name: shard1svr3
    healthcheck:
      test: ["CMD", "mongosh", "--host", "shard1svr3:27017", "--eval", "db.adminCommand('ping')"]
      interval: 30s
      timeout: 10s
      retries: 5

volumes:
  shard1svr1:
  shard1svr2:
  shard1svr3:
networks:
  mongoCluster:
    driver: bridge
    name: mongoCluster
    external: true
```

#### Start Shards
Run the following command in the `shard1` directory to start the first shard:
```bash
docker compose up -d
```

> Repeat the same for `shard2` and `shard3` by changing the shard name and container names accordingly.

#### Initialize Replica Set for Shard
1. Login to one of the shard containers:
```bash
docker exec -it shard1svr1 mongosh
```

2. Run the following command to initialize the replica set:
```javascript title="shard1/replicaSet.js"
rs.initiate({
  _id: "shard1rs",
  members: [
    { _id: 0, host: "shard1svr1:27017" },
    { _id: 1, host: "shard1svr2:27017" },
    { _id: 2, host: "shard1svr3:27017" }
  ]
})
```

3. Check the status of the replica set:
```javascript
rs.status()
```

> **NOTE**: For `shard2` and `shard3`, change the replica set name and hostnames accordingly.

---

### Mongos Router
#### Create Mongos Router
Change to `project directory` and create a `docker-compose.yml` file in the `mongos` directory:
```bash
mkdir mongos
cd mongos
touch docker-compose.yml
```

Define the `docker-compose.yml` file as follows:
```yaml title="mongos/docker-compose.yml"
services:
  mongos:
    image: mongo:latest
    command: mongos --configdb cfgrs/cfgsvr1:27017,cfgsvr2:27017,cfgsvr3:27017 --bind_ip 0.0.0.0 --port 27017
    ports:
      - "27017:27017"
    networks:
      - mongoCluster
    container_name: mongos
    restart: always  
networks:
  mongoCluster:
    driver: bridge
    name: mongoCluster
    external: true
```

#### Start Mongos Router
Run the following command in the `mongos` directory to start the mongos router:
```bash
docker compose up -d
```

#### Check Shard Status
You can check the status of the sharded cluster using the following command:
```bash
docker exec -it mongos mongosh --eval "sh.listShards()"
```

---

### Add Shards to the Cluster
You need to add each shard to the sharded cluster. To add `shard1`, run the following command:
```bash
docker exec -it mongos mongosh --eval "sh.addShard('shard1rs/shard1svr1:27017,shard1svr2:27017,shard1svr3:27017')"
```

> Repeat the same for `shard2` and `shard3` by changing the shard name and container names accordingly.

---

### Sharding a Collection
Let's create a db named `imdb` and a collection named `movies`. We'll use the `title` field as the shard key.

1. Connect to the `mongos` router:
   ```bash
   docker exec -it mongos mongosh
   ```

2. Create the `imdb` database and switch to it:
   ```javascript
   use imdb
   ```

3. Create the `movies` collection:
   ```javascript
    db.createCollection("movies")
    ```

4. Enable sharding for the `imdb` database:
   ```javascript
   sh.enableSharding("imdb")
   ```

5. Shard the `movies` collection using the `title` field as the shard key:
   ```javascript
   sh.shardCollection("imdb.movies", { "title": "hashed" })
   ```

6. Insert some sample data into the `movies` collection:
   ```javascript
   db.movies.insertMany([
     { title: "Inception", year: 2010, genre: "Sci-Fi" },
     { title: "The Dark Knight", year: 2008, genre: "Action" },
     { title: "Interstellar", year: 2014, genre: "Sci-Fi" },
     { title: "The Matrix", year: 1999, genre: "Sci-Fi" },
     { title: "Pulp Fiction", year: 1994, genre: "Crime" },
     { title: "The Shawshank Redemption", year: 1994, genre: "Drama" },
     { title: "The Godfather", year: 1972, genre: "Crime" },
     { title: "Forrest Gump", year: 1994, genre: "Drama" },
     { title: "The Lord of the Rings: The Return of the King", year: 2003, genre: "Fantasy" },
     { title: "Fight Club", year: 1999, genre: "Drama" }
   ])
   ```

7. Verify the sharding status of the `movies` collection:
   ```javascript
   db.movies.getShardDistribution()
   ```

8. Check the data distribution across shards:
   ```javascript
   sh.status()
   ```

---

## Conclusion
MongoDB sharding enables horizontal scaling by distributing data across multiple servers. With the right shard key and setup, you can efficiently manage large datasets and high concurrency. This guide covers a basic Docker-based sharded cluster, which you can adapt to your needs.
