Task: Install Docker CE and Docker Compose on App Server & Start Docker Service

________________________________________
1. Install Required Dependencies
```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
Explanation:

These packages ensure the OS can manage extra repositories (yum-utils) and support storage drivers (device-mapper, lvm2) required by Docker.
________________________________________
2. Add the Official Docker CE Repository
```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
Explanation:

This enables access to the official Docker repository, ensuring the installation of the latest stable Docker packages.
________________________________________
3. Install Docker CE & Docker Compose Plugin
```
sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Explanation:

This command installs:

•	docker-ce → Docker Community Edition engine

•	docker-ce-cli → Command-line client

•	containerd.io → Container runtime used by Docker

•	docker-buildx-plugin → Advanced BuildKit features

•	docker-compose-plugin → Docker Compose v2 (docker compose)
________________________________________
4. Enable Docker Service on System Boot
```
sudo systemctl enable docker
```
Explanation:

Ensures Docker starts automatically whenever the server reboots.
________________________________________
5. Start the Docker Service
```
sudo systemctl start docker
```
Explanation:

Starts the Docker daemon (dockerd) so containers can run.
________________________________________
6. Verify Docker Engine Installation
```
docker --version
```
Explanation:

Confirms the Docker Engine is installed and working.

Output example:

Docker version 29.1.2, build 890dcca
________________________________________
7. Verify Docker Compose Installation
```
docker compose version
```
Explanation:

Confirms Docker Compose v2 is available as a Docker plugin.

Compose v2 uses the syntax:

docker compose up

instead of the older docker-compose.
________________________________________
8. Verify Docker Service Status
```
sudo systemctl status docker
```
Explanation:

Confirms Docker service is active and running.

Your output showed:

Active: active (running)

Which verifies successful installation and startup.
________________________________________
Final Outcome

✔ Docker CE successfully installed

✔ Docker Compose v2 successfully installed

✔ Docker service enabled and started

✔ Verified Docker daemon is running without issues

✔ App Server is now ready for container-based workloads

