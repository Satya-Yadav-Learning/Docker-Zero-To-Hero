
## Task Overview

Dockerfile located under `/opt/docker` contained invalid instructions causing the image build to fail.

### Requirements

* Dockerfile location: `/opt/docker`
* Do **not** change the base image
* Do **not** modify existing data (e.g., `index.html`, certificates)
* Fix only Dockerfile issues so that the image builds successfully

---

## Initial Issue

When attempting to build the Docker image, the build failed with a Dockerfile parse error.

### Command Executed

```bash
docker build -t test-image .
```

### Error Output

```text
dockerfile parse error on line 1: unknown instruction: IMAGE
```

### Analysis

* `IMAGE` is **not a valid Dockerfile instruction**
* Dockerfiles must start with the `FROM` instruction
* `ADD` was incorrectly used to execute a command (`sed`), which should be executed using `RUN`

---

## Investigation Steps

### Step 1: Navigate to Dockerfile Directory

```bash
cd /opt/docker
ls -la
```

### Output

```text
Dockerfile
certs/
html/
```

This confirmed:

* Correct working directory
* Only one Dockerfile exists

---

### Step 2: Inspect the Dockerfile

```bash
cat Dockerfile
```

### Observed Issues

* Invalid instruction: `IMAGE httpd:2.4.43`
* Incorrect usage of `ADD` for running shell commands

---

## Resolution

The Dockerfile was corrected **without changing the base image or application data**.

### Fixed Dockerfile

```dockerfile
FROM httpd:2.4.43

RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf

RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf
RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf
RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf

COPY certs/server.crt /usr/local/apache2/conf/server.crt
COPY certs/server.key /usr/local/apache2/conf/server.key
COPY html/index.html /usr/local/apache2/htdocs/
```

### Explanation of Fixes

* `IMAGE` replaced with `FROM` (mandatory Dockerfile syntax)
* `ADD` replaced with `RUN` for executing `sed` commands
* Base image (`httpd:2.4.43`) unchanged
* Application files and certificates untouched

---

## Build Verification

### Build Command

```bash
docker build -t test.image .
```

### Build Output (Full)

```text
[+] Building 194.9s (13/13) FINISHED                                               docker:default
 => [internal] load build definition from Dockerfile                                         0.0s
 => => transferring dockerfile: 557B                                                         0.0s
 => [internal] load metadata for docker.io/library/httpd:2.4.43                            120.9s
 => [internal] load .dockerignore                                                            0.0s
 => => transferring context: 2B                                                              0.0s
 => [internal] load build context                                                            0.0s
 => => transferring context: 3.19kB                                                          0.0s
 => [1/8] FROM docker.io/library/httpd:2.4.43@sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c  63.9s
 => => resolve docker.io/library/httpd:2.4.43@sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c2  0.0s
 => => sha256:f1455599cc2e008a4555f14451e590f071371d371a3b87790651a367357d2 7.35kB / 7.35kB  0.0s
 => => sha256:bf59529304463f62efa7179fa1a32718a611528cc4ce9f30c0d1bbc672 27.09MB / 27.09MB  30.5s
 => => sha256:3d3fecf6569b94e406086a2b68a7c8930254490b45c0de4911f497ea9cf0876c 146B / 146B  30.6s
 => => sha256:b5fc3125d9129e4cdd43f496195cc8f39d43e9bad171044ecb5b8f82b2 10.37MB / 10.37MB  30.9s
 => => sha256:cd88fee4eab37f0d8cd04b06ef97285ca981c27b4d685f0321e65c5d4fd49 1.86kB / 1.86kB  0.0s
 => => sha256:53729354a74c9c146aa8726a8906e833755066ada1a478782f4dfb2ea6994 1.37kB / 1.37kB  0.0s
 => => extracting sha256:bf59529304463f62efa7179fa1a32718a611528cc4ce9f30c0d1bbc6724ec3fb    2.4s
 => => sha256:3c61041685c0f65e0b375bae6ae6bdeab9b6c20960dbef5e30201db18a 24.47MB / 24.47MB  61.2s
 => => sha256:34b7e9053f76ca3c9dc574c5034679769256a596008efbfbff1d1b1546600841 298B / 298B  61.3s
 => => extracting sha256:3d3fecf6569b94e406086a2b68a7c8930254490b45c0de4911f497ea9cf0876c    0.4s
 => => extracting sha256:b5fc3125d9129e4cdd43f496195cc8f39d43e9bad171044ecb5b8f82b2f6e30d    1.0s
 => => extracting sha256:3c61041685c0f65e0b375bae6ae6bdeab9b6c20960dbef5e30201db18a4e6d4a    1.3s
 => => extracting sha256:34b7e9053f76ca3c9dc574c5034679769256a596008efbfbff1d1b1546600841    0.8s
 => [2/8] RUN sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf          1.4s
 => [3/8] RUN sed -i '/LoadModule\ ssl_module modules\/mod_ssl.so/s/^#//g' conf/httpd.conf   1.3s
 => [4/8] RUN sed -i '/LoadModule\ socache_shmcb_module modules\/mod_socache_shmcb.so/s/^#//g' conf/httpd.conf  1.3s
 => [5/8] RUN sed -i '/Include\ conf\/extra\/httpd-ssl.conf/s/^#//g' conf/httpd.conf         1.4s
 => [6/8] COPY certs/server.crt /usr/local/apache2/conf/server.crt                           0.8s
 => [7/8] COPY certs/server.key /usr/local/apache2/conf/server.key                           0.8s
 => [8/8] COPY html/index.html /usr/local/apache2/htdocs/                                    0.9s
 => exporting to image                                                                       2.2s
 => => exporting layers                                                                      2.2s
 => => writing image sha256:b8e8fe53ccd5e945c2116b9e2266935cadcbb01e6f19b076ccd29c3493d244b  0.0s
 => => naming to docker.io/library/test.image
```

text
[+] Building FINISHED
=> naming to docker.io/library/test.image

````

### Verify Image Creation
```bash
docker images
````

### Output

```text
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
test.image   latest    b8e8fe53ccd5   12 minutes ago   166MB
```

---

## Final Result

* Docker image built successfully
* All task constraints satisfied
* No base image or data changes performed

---

