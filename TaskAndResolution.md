•	Identify the running container named ubuntu_latest
•	Commit the container’s current state into a new Docker image
•	The new image must be named demo:natural

2. Verify the Running Container
```
docker ps
```

-Output
-CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS              PORTS     NAMES
-79870846fc7c   ubuntu    "/bin/bash"   About a minute ago   Up About a minute             ubuntu_latest

-Explanation
-Ensures that the ubuntu_latest container exists and is running before committing it to an image.

