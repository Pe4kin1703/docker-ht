
# Docker Setup for Client and Server with Netcat

## 1. How to Build Images

We need to create two Docker images: one for the client and one for the server. Ensure you have Dockerfiles (`Dockerfile`) ready with the appropriate instructions.

### Dockerfile for the Client (client/Dockerfile)
```dockerfile
FROM alpine:latest
RUN apk add --no-cache netcat-openbsd
COPY client.sh /scripts/client.sh
RUN chmod +x /scripts/client.sh
WORKDIR /scripts
ENTRYPOINT ["sh", "/app/client.sh"]
CMD ["tail", "-f", "/dev/null"]
```

### Dockerfile for the Server (server/Dockerfile)
```dockerfile
FROM alpine:latest
RUN apk add --no-cache netcat-openbsd
COPY server.sh /scripts/server.sh
RUN chmod +x /scripts/server.sh
WORKDIR /scripts
CMD ["sh", "/scripts/server.sh"]
```

### Build the Docker Images

Run the following commands to build the Docker images:

```
docker build -t client/client-image .
docker build -t server/server-image .
```

## 2. How to Create a Network

Create a non-default Docker bridge network to allow containers to communicate with each other:

```
docker network create docker-ht
```

## 3. How to Run Containers

Run the containers in the created network:

### Run the Server Container

```
docker run -d --name server-container --network docker-ht server-image
```

### Run the Client Container

```
docker run -d --name client-container --network docker-ht client-image
```

## 4. Commands to Verify Containers Connectivity

To verify that the containers are running and can see each other on the network, use the following commands:

### List Running Containers

```
docker ps
```

### Check Network Connectivity

You can use `ping` to check if containers can communicate. For example, exec into the `client-container` and ping the `server-container`:

```
docker exec -it client-container ping server-container
```

After containers have been run you can check output of the command:

```
docker network inspect docker-ht
```
In the output you can see something like this:

```
"Containers": {
    "0359bc5b41930b122c6a118ef0f6cb517dce42f59d6bc51bd58fbfb20a1d97fb": {
        "Name": "client-docker",
        "EndpointID": "2a8a48de7e310aa57d991d271b9e1004b8e952adf0b2d21fdaaed44f1d286dcd",
        "MacAddress": "02:42:ac:12:00:03",
        "IPv4Address": "172.18.0.3/16",
        "IPv6Address": ""
    },
    "8f1e65688ffb475676d97257c00cc3f29c7b7a6d30f3fe944b368c2f3c6f75aa": {
        "Name": "server-docker",
        "EndpointID": "6ce1b297bc33dd84cdf45fbb2208408220c64af02462554a29bba9f9a58c9074",
        "MacAddress": "02:42:ac:12:00:02",
        "IPv4Address": "172.18.0.2/16",
        "IPv6Address": ""
    }
}
```
It means that containers share the same network.

## 5. Commands to Verify Netcat Scripts

### Check Server Logs

To see if the server is receiving data, view its logs:

```
docker logs server-container
```



