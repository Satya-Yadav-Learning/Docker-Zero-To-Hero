
## Task Overview

The Dockerfile located under `/opt/docker` contained invalid instructions causing the image build to fail.

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

### Build Output (Summary)

```text
[+] Building FINISHED
=> naming to docker.io/library/test.image
```

### Verify Image Creation

```bash
docker images
```

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

## Task Closure Statement

**The Dockerfile issues were resolved and the Docker image was built successfully as per the requirements.**

