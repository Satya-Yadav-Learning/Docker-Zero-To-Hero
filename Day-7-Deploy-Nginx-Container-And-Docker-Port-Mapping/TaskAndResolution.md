Nginx-based container must run using the `nginx:alpine` image and expose the application via **host port 3001**.

- Container Runtime: Docker

- Image: nginx:alpine

- Container Name: media

- Port Mapping: Host 3001 → Container 80

# 1: Verify Docker Installation
```
docker --version

Output
    
Docker version 26.1.3, build b72abbb

Explanation

Confirms that Docker is installed and accessible for the current user.
```
# 2: Verify Docker Service Status
```
sudo systemctl status docker

Output

● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled)
     Active: active (running)

Explanation

Ensures the Docker daemon is running before creating containers.
```
# 3: Pull the Nginx Alpine Image
```
docker pull nginx:alpine

Output

Digest: sha256:289decab414250121a93c3f1b8316b9c69906de3a4993757c424cb964169ad42
Status: Downloaded newer image for nginx:alpine
docker.io/library/nginx:alpine

Explanation

Downloads the lightweight Alpine-based Nginx image from Docker Hub.
```
# 4:Verify Image Availability
```
Output

nginx   alpine   a236f84b9d5d   2 days ago   53.7MB

Explanation

Confirms that the required image is available locally.
```
# 5: Create and Run the Container
```
docker run -d --name media -p 3001:80 nginx:alpine

Output

8880915e4fa4d450085b7de6517101855c5c75a8e1d5308c41873f06a13e1c58

Explanation

-d runs the container in detached mode

--name media assigns the container name

-p 3001:80 maps host port 3001 to container port 80

nginx:alpine specifies the image to use
```
# 6: Verify Container Status
```
docker ps | grep media

Output

8880915e4fa4   nginx:alpine   "/docker-entrypoint.…"   Up   0.0.0.0:3001->80/tcp   med

Explanation

Confirms that the container is running and the port mapping is correct.
```
# 7: Final Result
```
nginx:alpine image successfully pulled

Container media created and running

Host port 3001 mapped to container port 80

Container is accessible and running as expected
```
