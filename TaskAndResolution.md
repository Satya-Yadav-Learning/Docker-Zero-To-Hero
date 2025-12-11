Identify the running container named ubuntu_latest.

Commit the container’s current state into a new Docker image

The new image must be named demo:natural

1. Verify the Running Container
```
docker ps

Output
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS              PORTS     NAMES
79870846fc7c   ubuntu    "/bin/bash"   About a minute ago   Up About a minute             ubuntu_latest
```
Ensures that the ubuntu_latest container exists and is running before committing it to an image.

2. Create a New Image From the Container
```
sudo docker commit ubuntu_latest demo:natural
Output
sha256:1b540b9b30f5d981d5216a798ba6a9162b9b2c84dfdee35b51c131018b3b4647
This is the new image ID generated during commit.
```
Explanation
docker commit captures the current state of the container as a new image.

ubuntu_latest → source container.

demo:natural → new image name:tag created from it.

3. Verify the Newly Created Image
```
docker image ls
Output
REPOSITORY   TAG        IMAGE ID       CREATED          SIZE
demo         natural   1b540b9b30f5   14 seconds ago   135MB
ubuntu       latest     c3a134f2ace4   7 weeks ago      78.1MB
```
4 . 
```
sudo docker images | grep demo
Output
demo         natural   1b540b9b30f5   43 seconds ago   135MB
```

✔ Container ubuntu_latest identified and running

✔ New image demo:nautilus successfully created

✔ Image size and ID verified





