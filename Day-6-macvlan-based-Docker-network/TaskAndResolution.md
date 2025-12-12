In this task, we need to:

Create a Docker network named beta

Use the macvlan driver

Configure subnet as 192.168.0.0/24

Configure ip-range as 192.168.0.0/24

Use appropriate parent interface on the host

# 1:Verify Docker Installation
```
docker version

Output:

Client: Docker Engine - Community
 Version:           26.1.3
...
Server: Docker Engine - Community
 Version:          26.1.3
...

Purpose: Ensures Docker client and server are installed and running
```
# 2: Verify Docker Service Status
```
sudo systemctl status docker --no-pager

Active: active (running)
Main PID: 1647 (dockerd)

Purpose: Confirms the Docker daemon is active and running.
```
# 3: Identify Host Network Interface (Determine which physical interface will act as the macvlan parent).
```
ip -br addr

lo               UNKNOWN        127.0.0.1/8
docker0          DOWN           172.12.0.1/24
eth0@if23        UP             172.16.238.12/24
eth1@if29        UP             172.16.239.5/24
eth2@if39        UP             172.17.0.8/16

eth0 is the correct physical interface for macvlan.
```
# 4. Create the macvlan Network (Creates the required Docker network using macvlan driver).
```
docker network create -d macvlan \  --subnet=192.168.0.0/24 \  --ip-range=192.168.0.0/24 \  --gateway=192.168.0.1 \  --opt parent=eth0 \  beta

Output:
f8bbc095c534e394b8feac26fb584f28dc7407aa0202cde77407d00a37121e11
```
# 5: Verify the macvlan network (To check network was created with correct configuration).
```
docker network inspect beta

Output (important portion):

"Driver": "macvlan",
"Subnet": "192.168.0.0/24",
"IPRange": "192.168.0.0/24",
"Gateway": "192.168.0.1",
"parent": "eth0"
```
# 6: Run Test Container on macvlan Network (Verifies container can join macvlan network and receive static IP).
```
docker run -d --name test-macvlan-static --network beta --ip 192.168.0.50 --rm nginx:alpine

Output:

cb3d106b19b754d20bbb78c3999da4b4fab96b0aa9a4783afe13a0a59cc7dcbd
```
# 7: Confirm Container Networking (To Confirm the container is correctly attached to beta).
```
docker inspect -f '{{json .NetworkSettings.Networks}}' test-macvlan-static

Output:

{"beta":{"IPAddress":"192.168.0.50","Gateway":"192.168.0.1",...}}
```
# 8 : Create Host-Side macvlan Interface
```
By default, macvlan prevents host ↔ container communication.

We need a host macvlan interface manually.

a. Create host macvlan interface
sudo ip link add macvlan_host link eth0 type macvlan mode bridge

b. Assign an IP to host macvlan interface
sudo ip addr add 192.168.0.250/24 dev macvlan_host

c. Bring the interface UP
sudo ip link set macvlan_host up

Verify by using below command

ip -br addr

Output:

macvlan_host@eth0 UP 192.168.0.250/24
```
# 9: Install ping utility (iputils)
```
sudo yum install -y iputils
```
# 10: Test Host → Container Connectivity
```
sudo ping -c 3 192.168.0.50

Output:

64 bytes from 192.168.0.50: icmp_seq=1 ttl=64 time=0.134 ms
...
0% packet loss

Connectivity successful.
```
# 11: Test HTTP Access (Because the container runs NGINX, we confirm service access).
```
curl http://192.168.0.50

Output:

<h1>Welcome to nginx!</h1>

Web server is accessible—network functioning perfectly.
```
# 12: Test Container → Host Connectivity 
```
docker exec -it test-macvlan-static sh   #(login inside container)

ping -c 3 192.168.0.250

Output:

3 packets transmitted, 3 packets received, 0% packet loss

Bidirectional communication is working.
```
# ✅ Final Summary
```
What was successfully completed:

Created macvlan network beta

Configured subnet, ip-range, and gateway exactly as required

Attached macvlan to correct parent interface eth0

Started a container with static IP

Enabled host ↔ container communication

Verified connectivity using ping and curl
```


# Macvlan Network Architecture Diagram

                         ┌──────────────────────────────────────────┐
                         │        App Server                        │
                         │        Host OS: CentOS Stream 9          │
                         └──────────────────────────────────────────┘
                                             │
                                             │  Physical Network
                                             ▼
                 ┌─────────────────────────────────────────────────────────────┐
                 │                         eth0                                │
                 │                IP: 172.16.238.12/24                         │
                 │         (Primary host interface used as macvlan parent)     │
                 └─────────────────────────────────────────────────────────────┘
                                             │
                                   macvlan (parent)
                                             │
               ┌──────────────────────────────────────────────────────────────┐
               │                  Host-side macvlan interface                 │
               │                        macvlan_host                          │
               │                  IP: 192.168.0.250/24                        │
               │        (Allows host ↔ container communication)               │
               └──────────────────────────────────────────────────────────────┘
                                             │
                              ┌────────────────────────────────┐
                              │   Docker macvlan network:      │
                              │             beta               │
                              │ Subnet:     192.168.0.0/24     │
                              │ IP Range:   192.168.0.0/24     │
                              │ Gateway:    192.168.0.1        │
                              └────────────────────────────────┘
                                             │
                                             │  L2 switching (MAC-level)
                                             ▼
                     ┌────────────────────────────────────────────┐
                     │ Docker Container (macvlan endpoint)        │
                     │   Name: test-macvlan-static                │
                     │   Image: nginx:alpine                      │
                     │   IP: 192.168.0.50/24                      │
                     │   MAC: 02:42:c0:a8:00:32                   │
                     └────────────────────────────────────────────┘
                                             │
                                             │  HTTP Test
                                             ▼
                         curl http://192.168.0.50 → NGINX Welcome Page

