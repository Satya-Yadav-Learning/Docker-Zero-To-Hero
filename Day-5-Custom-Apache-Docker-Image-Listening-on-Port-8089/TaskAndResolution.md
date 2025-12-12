Task: Create Custom Apache Docker Image Listening on Port 8089.

# Create a Dockerfile on **App Server ** under `/opt/docker/Dockerfile` 

1. Base image must be **ubuntu:24.04**.

2. Install **apache2** and reconfigure it to listen on **port 8089** instead of the default port 80.

3. No other Apache configuration (document root, server name, modules, etc.) should be modified.


# sudo vi /opt/docker/Dockerfile
```
FROM ubuntu:24.04

# Install Apache
RUN apt update && \
    apt install -y apache2 && \
    apt clean

# Change Apache listening port to 8089
RUN sed -i 's/Listen 80/Listen 8089/' /etc/apache2/ports.conf && \
    sed -i 's/<VirtualHost \*:80>/<VirtualHost \*:8089>/' /etc/apache2/sites-enabled/000-default.conf

# Expose the new port
EXPOSE 8089

# Ensure Apache runs in the foreground (required in Docker)
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```
# Build the Docker Image
```
cd /opt/docker/

sudo docker build -t custom-apache:latest .


========================================
Output Snapshot

Image built successfully

Named as custom-apache:latest

Uses base image ubuntu:24.04
```
# Run Container Exposing Port 8089
```
sudo docker run -d --name app_test -p 8089:8089 custom-apache:latest

sudo docker ps            #Verify container is running
```

# Test Apache inside container
```
sudo docker exec -it app_test bash     

#Exec into the container

apt update && apt install -y curl

curl http://localhost:8089

curl http://127.0.0.1:8089
```







    
