Task: Deploy nginx Container Using nginx:alpine on Application Server.

The container must be named nginx_1 and remain in a running state.

1. Pull the nginx:alpine Docker Image
```
sudo docker pull nginx:alpine

:Output
alpine: Pulling from library/nginx
014e56e61396: Pull complete
dfad290a5c25: Pull complete
5d2cc344426d: Pull complete
abdece946203: Pull complete
51c30493937c: Pull complete
ad5b65da02cf: Pull complete
fc13532503d7: Pull complete
136bc6976c20: Pull complete
Digest: sha256:289decab414250121a93c3f1b8316b9c69906de3a4993757c424cb964169ad42
Status: Downloaded newer image for nginx:alpine
docker.io/library/nginx:alpine
```
Explanation:

Pulls the lightweight Alpine-based nginx image from Docker Hub so it can be used to create a container.

________________________________________
2. Create and Run Container nginx_1
```
sudo docker run -d --name nginx_1 nginx:alpine

Output:
84818fb428e5b54d98a42a7213c00d13e4420d0568d6791aa7cc3f277ee0b49b
(Container ID confirms successful creation.)
```
Explanation:

-d: Run in background

--name nginx_1: Assigns container name

nginx:alpine: Specifies the image

This starts an nginx web server inside the container.

________________________________________
3. Verify Container is Running
```
sudo docker ps

Output:
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS     NAMES
84818fb428e5   nginx:alpine   "/docker-entrypoint.…"   18 seconds ago   Up 17 seconds   80/tcp    nginx_1
```
Explanation:

Lists all running containers and confirms whether nginx_1 is active.

This confirms:

✔ Container is running

✔ Image is correct

✔ Port 80 exposed internally

✔ Name is correct (nginx_1)
________________________________________
Final Outcome

✔ nginx:alpine image downloaded

✔ Container nginx_1 created successfully

✔ Container is in running state
✔ Task fully completed as required

