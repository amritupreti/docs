# Replication

> Replication in MongoDB is a process of synchronizing data across multiple servers to ensure high availability and redundancy. It allows for automatic failover and data redundancy, which is crucial for maintaining the integrity and availability of data in distributed systems.

## Benefits of Replication
- **High Availability**: If the primary server fails, one of the secondaries can be elected as the new primary.
- **Data Redundancy**: Data is stored on multiple servers, reducing the risk of data loss.
- **Read Scalability**: Read operations can be distributed across secondary servers, improving performance.


## Replica Set
A replica set is a group of MongoDB servers that maintain the same data set. It consists of:

- **Primary**: The server that receives all write operations.
- **Secondary**: The servers that replicate the primary's data and can serve read operations.
- **Arbiter**: An optional member that participates in elections but does not hold data.


## Setting Up a Replica Set using Docker
Source code: [https://github.com/amritupreti/mongo/tree/replication](https://github.com/amritupreti/mongo/tree/replication)

### Project Structure
```plaintext title="replication"
.
├── docker-compose.yml
├── replicaSet.js
```

### replicaSet.js
Create a `replicaSet.js` file to initialize the replica set:
```javascript title="replicaSet.js" hl_lines="4-6"
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo1:27017" },
    { _id: 1, host: "mongo2:27017" },
    { _id: 2, host: "mongo3:27017" }
  ]
})
```

> **Note**: You can either use hostnames or IP addresses in the `host` field. Ensure that the hostnames are resolvable within your Docker network.

### Docker Compose Configuration
Create a `docker-compose.yml` file with the following content:
```yaml title="docker-compose.yml" hl_lines="5 7 9 38 41"
services:
  # MongoDB replica set services
  mongo1:
    image: mongo:latest
    command: mongod --replSet rs0     # rs0 is the name of the replica set
    volumes:
      - mongo1-data:/data/db          # Volume for data persistence
    networks:
      - mongoCluster                  # Network for communication
    container_name: mongo1
    restart: always

  mongo2:
    image: mongo:latest
    command: mongod --replSet rs0
    volumes:
      - mongo2-data:/data/db
    networks:
      - mongoCluster
    container_name: mongo2
    restart: always

  mongo3:
    image: mongo:latest
    command: mongod --replSet rs0
    volumes:
      - mongo3-data:/data/db 
    networks:
      - mongoCluster
    container_name: mongo3
    restart: always

  # Temporary service to initialize the replica set
  # Exits after running the replicaSet.js script
  mongo-init:
    image: mongo:latest
    volumes:
      - ./replicaSet.js:/replicaSet.js:ro       # Read-only mount of the initialization script
    networks:
      - mongoCluster
    command: ["sh", "-c", "sleep 10 && mongosh --host mongo1:27017 /replicaSet.js"]
    depends_on:
      - mongo1
      - mongo2
      - mongo3
    container_name: mongo-init

volumes:
  mongo1-data:
    name: mongo1-data
    driver: local
  mongo2-data:
    name: mongo2-data
    driver: local
  mongo3-data:
    name: mongo3-data
    driver: local
networks:
  mongoCluster:
    name: mongoCluster
    driver: bridge
```

### Running the Replica Set
1. Start the services:
```bash
docker compose up -d
```

> **Note**: The `mongo-init` service will run the `replicaSet.js` script to initialize the replica set and then exit.

2. Check the status of the replica set:
    - Using `docker exec`:
    ```bash
    docker exec -it mongo1 mongosh --eval "rs.status()"
    ```
    - Using `mongosh` directly in one of the MongoDB containers:
    ```javascript
    rs.status()
    ```

3. After initialization, add new members from the primary server:
    - Ensure the new member is running and accessible.
    - Using `docker exec` to add a new member:
    ```bash
    docker exec -it mongo1 mongosh --eval "rs.add('mongo4:27017')"
    ```
    - Using `mongosh` directly from the primary server:
    ```javascript
    rs.add("mongo4:27017")
    ```



## Monitoring Replica Set
Monitor your replica set with `rs.status()`, which shows each member's state, health, and replication lag.

## Conclusion
MongoDB replication improves data availability and reliability. Setting up a replica set ensures your data remains accessible and protected against server failures—crucial for production environments.
