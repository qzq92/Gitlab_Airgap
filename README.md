# Gitlab Container Image
## Getting Started
 - OS: Ubuntu 16.04/18.04
 - Binaries: docker, docker-compose
 - Required docker image: gitlab/gitlab-ce:13.0.6-ce.0
 - Main doc for basic using docker-compose for gitlab
 - Ref: https://docs.gitlab.com/omnibus/docker/#install-gitlab-using-docker-engine

## Document for parameters configuration for Gitlab
 - https://docs.gitlab.com/omnibus/settings/README.html

## Document for Package defaults
 - https://docs.gitlab.com/omnibus/package-information/defaults.html

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

### Note: Based on https://docs.gitlab.com/omnibus/package-information/defaults.html, the list ports for the exporters(Gitlab,Node,Redis,Psql and Sidekiq are enabled).

### Configure environment settings by GITLAB_OMNIBUS_CONFIG in docker-compose.yaml. Put 0.0.0.0 as IP placeholder for convenience, which also means all IPv4 addresses on the local machine. Refer to GITLAB omnibus webpage ref: https://docs.gitlab.com/omnibus/docker/README.html on the configuration needed for installing gitlab. The configuration defined in GITLAB_OMNIBUS_CONFIG affects the file /etc/gitlab/gitlab.rb in gitlab container.

