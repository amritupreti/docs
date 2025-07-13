# HAProxy

> HAProxy is a open-source project written in C that provides high availability, `layer 4` and `layer 7` load balancing and proxying.

## Architecture
- **Frontend**: Anything in front of HAProxy that sends requests to it.
    - Timeout client
    - Bind to IP and port
    - Access Control List (ACL)
- **Backend**: Anything behind HAProxy that receives requests from it.
    - Timeout connect
    - Timeout server
    - Balance algorithm (roundrobin, leastconn, source, etc.)

## HAProxy Modes (`tcp` and `http`)
- **TCP Mode**:
    - Operates at the transport layer `(Layer 4)`.
    - Can handle raw TCP connections.
    - **Example**: Load balancing MySQL or Redis databases.
- **HTTP Mode**:
    - Operates at the application layer `(Layer 7)`.
    - Can inspect and manipulate HTTP headers.
    - **Example**: Load balancing web servers, caching, SSL termination.

**Note**: `http` mode is often slower than `tcp` mode due to the additional overhead of parsing HTTP headers, but it provides more features like content switching and header manipulation.

## Access Control Lists (ACLs)
ACLs are used to define rules for routing requests based on specific criteria.

- Applies to both frontend and backend configurations.
- Can be used to match based on:
    - Source IP address
    - HTTP headers
    - URL paths
- Re-route traffic based on ACLs to different backends or apply specific actions.

## TLS Termination vs TLS Passthrough
- **TLS Termination**:
    - HAProxy decrypts incoming TLS traffic.
    - This allows it to inspect and modify requests before forwarding them to the backend in plain text.
    - Enables features such as header manipulation and `Layer 7 ACLs`.
    - Requires a TLS certificate to be configured on HAProxy.

- **TLS Passthrough**:
    - HAProxy forwards the encrypted TLS traffic directly to the backend without decrypting it.
    - Backend servers must handle TLS termination.
    - Proxy the packets as-is, without inspecting or modifying them.
    - No caching, `L4 ACLs only`, but more secure, it doesn't need a TLS certificate on HAProxy.

## Load Balancing Algorithms
Load balancing algorithms determine how requests are distributed among backend servers. Some common algorithms include:

- **Round Robin**: `balance roundrobin` 
    - Default algorithm in HAProxy.
    - Distributes requests sequentially across all available servers.
    - **Example**: Let's say, we have 3 servers `(S1, S2, S3)`, then the request sequence will be `S1 -> S2 -> S3 -> S1 -> S2`, and so on.

- **Least Connections**: `balance leastconn`
    - Directs traffic to the server with the fewest active connections.
    - Useful for servers with varying processing capabilities.

- **Source IP Hash**: `balance source`
    - Routes requests from the same client IP to the same backend server.
    - Useful for session persistence `(sticky sessions)`.


## Simple Example Using Docker
Source code: [https://github.com/amritupreti/haproxy_docker](https://github.com/amritupreti/haproxy_docker)

> Please refer to the source code for a complete example. Feel free to leave a star if you find it useful.

### Project Structure

```plantext title="haproxy_docker"
├── docker-compose.yml
├── haproxy/
│   └── haproxy.cfg
└── server/
    ├── app.py
    ├── Dockerfile
    ├── .dockerignore
    └── requirements.txt
```

### Create FastAPI Server
I'm using fastapi as a simple web server to demonstrate the load balancing.

1. Create a directory for the project:
```bash
mkdir haproxy_docker
cd haproxy_docker
```

2. Create server:
```bash
# Make directory for server
mkdir server
cd server

# Make isolated python environment
virtualenv venv
source venv/bin/activate

# Install fastapi and save requirements
pip install "fastapi[standard]"
pip freeze > requirements.txt

# Create app.py and files for docker
touch app.py
touch Dockerfile
touch .dockerignore
```

3. Add the following content to `app.py`:
```python
import os
from fastapi import FastAPI, APIRouter

server_name = os.getenv("SERVER_NAME", "default_server")

router = APIRouter()
@router.get("/")
async def home():
    return f"{server_name}: Home page!"

@router.get("/admin")
async def admin():
    return f"{server_name}: Admin page!"

@router.get("/lightwork")
async def admin():
    return f"{server_name}: Doing light work!"

@router.get("/heavywork")
async def heavywork():
    return f"{server_name}: Doing heavy work!"

def create_app() -> FastAPI:
    app = FastAPI()
    app.include_router(router)
    return app

app = create_app()
```

4. Add the following content to `Dockerfile`:
```dockerfile
FROM python:3.12.11-alpine3.22

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

CMD ["fastapi", "run", "--host", "0.0.0.0", "--port", "8000"]
```

5. Add the following content to `.dockerignore`:
```plaintext
venv/
Dockerfile
__pycache__/
```


### Create HAProxy Configuration
1. Create a `haproxy.cfg` file in the `haproxy` directory:
```bash
mkdir haproxy
cd haproxy
touch haproxy.cfg
```

2. Add the following content to `haproxy.cfg`:
```cfg title="haproxy.cfg"
frontend clients
    bind *:80
    timeout client 60s

    # Frontend mode should match the mode of the backend
    mode http

    # Use ACLs to route traffic based on the URL path
    acl lightwork path_end -i /lightwork
    acl heavywork path_end -i /heavywork

    # Block traffic to /admin
    http-request deny if { path -i -m beg /admin }

    use_backend lightServers if lightwork
    use_backend heavyServers if heavywork

    default_backend servers

backend lightServers
    timeout connect 10s
    timeout server 10s

    # Use same mode as frontend
    mode http

    server server1 server1:8000
    server server2 server2:8000

backend heavyServers
    # Longer timeouts for heavy work as it may take more time to process
    timeout connect 10s
    timeout server 120s

    # Use same mode as frontend
    mode http

    # balance algorithm -> default is roundrobin
    # Suppose our heavy work servers are stateful
    # We want to keep the session on the same server
    balance source

    server server3 server3:8000
    server server4 server4:8000

backend servers
    timeout connect 10s
    timeout server 60s

    # http operates at layer 7 (application layer)
    mode http

    server server1 server1:8000
    server server2 server2:8000 
    server server3 server3:8000
    server server4 server4:8000
```

### Docker Compose Setup

6. Create a `docker-compose.yml` file in the root directory `haproxy_docker`:
```yaml title="docker-compose.yml"
services:
  server1:
    image: server:latest
    build:
      context: ./server
      dockerfile: Dockerfile
    ports:
      - "8001:8000"
    environment:
      - SERVER_NAME=server1
    container_name: server1

  server2:
    image: server:latest
    build:
      context: ./server
      dockerfile: Dockerfile
    ports:
      - "8002:8000"
    environment:
      - SERVER_NAME=server2
    container_name: server2

  server3:
    image: server:latest
    build:
      context: ./server
      dockerfile: Dockerfile
    ports:
      - "8003:8000"
    environment:
      - SERVER_NAME=server3
    container_name: server3

  server4:
    image: server:latest
    build:
      context: ./server
      dockerfile: Dockerfile
    ports:
      - "8004:8000"
    environment:
      - SERVER_NAME=server4
    container_name: server4

  haproxy:
    image: haproxy:latest
    ports:
      - "8000:80"
    volumes:
      - ./haproxy/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro
    depends_on:
      - server1
      - server2
      - server3
      - server4
    container_name: haproxy
```

### Running the Setup
1. Start the services:
```bash
docker compose up -d
```

2. Test the setup:
    - `server1`: [http://localhost:8001/](http://localhost:8001/)
    - `server2`: [http://localhost:8002/](http://localhost:8002/)
    - `server3`: [http://localhost:8003/](http://localhost:8003/)
    - `server4`: [http://localhost:8004/](http://localhost:8004/)
    - `HAProxy`: [http://localhost:8000/](http://localhost:8000/)

3. Test the load balancing:
    - Access the home page: [http://localhost:8000/](http://localhost:8000/)
    - Access the light work page: [http://localhost:8000/lightwork](http://localhost:8000/lightwork)
    - Access the heavy work page: [http://localhost:8000/heavywork](http://localhost:8000/heavywork)
    - Access the admin page from HAProxy frontend client: [http://localhost:8000/admin](http://localhost:8000/admin) (should be blocked)
    - Try admin page on each server: 
        - [http://localhost:8001/admin](http://localhost:8001/admin)
        - [http://localhost:8002/admin](http://localhost:8002/admin), etc. (should be accessible)

4. Stop the services:
```bash
docker compose down
```

## Conclusion
HAProxy is extremely powerful for load balancing and proxying traffic. It supports various modes, ACLs, and load balancing algorithms, making it suitable for a wide range of applications. The example provided demonstrates how to set up HAProxy with multiple backend servers using Docker, showcasing its capabilities in a practical scenario.