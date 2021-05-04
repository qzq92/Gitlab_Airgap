# Deploying Gitlab on local machine
## Getting Started
 - OS: Ubuntu 16.04/18.04
 - Binaries: docker, docker-compose
 - Required docker image: gitlab/gitlab-ce:13.0.6-ce.0
 - Main doc for basic using docker-compose for gitlab
 - Ref: https://docs.gitlab.com/omnibus/docker/#install-gitlab-using-docker-engine

## Reference for parameters configuration for Gitlab
 - https://docs.gitlab.com/omnibus/settings/README.html

## Reference for Package defaults
 - https://docs.gitlab.com/omnibus/package-information/defaults.html
 
## Certificate Generation
 - You may refer to the steps that I did by using your client as Certificate Authority. There are more detailed explainations online for topics related to certificates, especially when you want to use it to enable HTTPs protocol for other applications.
``` bash
# Generate private key. This would prompt you to enter a pass phrase and reconfirm again.**
 - $ openssl genrsa -des3 -out myCA.key 4096 # Enter pass phrase for myCA.key

# Generate root certificate (.pem) 
 - $ openssl req -x509 -new -nodes -key myCA.key -sha256 -days 3650 -out myCA.pem

# Generate a secure server key for GoHarbor."
 - $ sudo openssl genrsa -out gitlab.io.key 4096

# Generate the certificate signing request file taking reference from my configuration file req.conf
 - $ sudo openssl req -new -key gitlab.io.key -out gitlab.io.csr -config req.conf

# Create the signed certificate for GoHarbor using the our Cert Authority certificate and keys with the GoHarbor signing request"
 - $ sudo openssl x509 -req -in gitlab.io.csr -CA myCA.pem -CAkey myCA.key -CAcreateserial -out gitlab.io.crt -days 3650 -sha256
```
## Starting and stopping Gitlab from the Gitlab folder
### To bring up:
 - $ sudo docker-compose up
### To bring down:
 - $ sudo docker-compose down

### Volumes required:
  - '<Gitlab_folder_path>/config:/etc/gitlab' **Directory containing configurations related to gitlab**
  - '<Gitlab_folder_path>/logs:/var/log/gitlab' **Directory storing gitlab logs**
  - '<Gitlab_folder_path>/data:/var/opt/gitlab' **Directory storing gitlab related data**
  - '<Gitlab_folder_path>/volume_data/ssl:/etc/ssl/certs/gitlab' **Directory containing certs required for https**

### Ports required and related information
  - '80:80' **Http endpoint**
  - '443:443' **Https endpoint**
  - '22:22' **ssh endpoint**
  - '9090:9090' **Prometheus UI endpoint**
  - '9168:9168' **Gitlab exporter (endpoint for various GitLab metrics pulled from Redis and the   database) Requires env: gitlab_exporter['enable']=true**
  - '9236:9236' **Gitaly (service that provides high-level RPC access to Git repositories. Without it, no GitLab components can read or write Git data. Also a service that provides storage for Git) repositories**
  - '9100:9100' **Node exporter (measure various machine resources such as memory, disk and CPU utilization.)**
  - '9121:9121' **Redis exporter(export scraped redis(in-memory data structure store metrics) to be collected by Prometheus. Requires env: redis_exporter['enable'] = true**
  - '9187:9187' **Psql exporter(export various PostgreSQL metrics) Requires env: postgres_exporter['enable'] = true**
  - '8060:8060' **NGINX health-check endpoint**
  - '8082:8082' **Sidekiq exporter (background job processor GitLab uses to asynchronously run tasks). Sidekiq requires connection to the Redis, PostgreSQL and Gitaly instances**

### Configure environment settings by GITLAB_OMNIBUS_CONFIG in docker-compose.yaml. Put 0.0.0.0 as IP placeholder for convenience, which also means all IPv4 addresses on the local machine. Refer to GITLAB omnibus webpage ref: https://docs.gitlab.com/omnibus/docker/README.html on the configuration needed for installing gitlab. The configuration defined in GITLAB_OMNIBUS_CONFIG affects the file /etc/gitlab/gitlab.rb in gitlab container.


### Note: From the reference online: https://docs.gitlab.com/omnibus/package-information/defaults.html, the list ports for the exporters (Gitlab,Node,Redis,Psql and Sidekiq are enabled).The "Create_CA" folder provides my self-signed certificates(expires until 2030) to enable the https protocol for Gitlab. By default, if https is enabled for GoHarbor, it would automatically route http request to https instead, assuming you are using port 80 and 443 for http and https port respectively. If you are not intending to enable https for Gitlab, no certificate is required and please rename the external url to http and remove nginx related entries under GITLAB_OMNIBUS_CONFIG
