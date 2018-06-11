# AndroidCI

## Setup Environment

### Docker

* Download [Docker Community Edition](https://store.docker.com/search?offering=community&type=edition)

### Jenkins

* Pull the latest [Jenkins Docker image](https://hub.docker.com/_/jenkins/)
  ```bash
  docker pull jenkins:2.60.3-alpine
  ```
* Get the initial admin password
   ```bash
   docker cp $(docker ps -aqf "ancestor=jenkins:2.60.3-alpine"):/var/jenkins_home/secrets/initialAdminPassword initialAdminPassword && cat initialAdminPassword && rm initialAdminPassword
   ```

### Android SDK
