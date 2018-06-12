# AndroidCI

## Setup Environment

### Docker

* Download [Docker Community Edition](https://store.docker.com/search?offering=community&type=edition)

### Jenkins

* Pull the latest [Jenkins Docker image](https://hub.docker.com/_/jenkins/)

  ```console
  docker pull jenkins:2.60.3-alpine
  ```

* Get the initial admin password

   ```console
   docker cp $(docker ps -aq -f "ancestor=jenkins:2.60.3-alpine" -f "status=running"):/var/jenkins_home/secrets/initialAdminPassword initialAdminPassword && cat initialAdminPassword && rm initialAdminPassword
   ```

* Run Jenkins

   Run Jenkins container with a mounted `jenkins_home` volume, all plugins and settings will be persisted.
   
   ```console
   docker run -p 8080:8080 -p 50000:50000 -v $(pwd)/jenkins_home:/var/jenkins_home jenkins:2.60.3-alpine
   ```

* Install Plugins (no need if you use persistent volume)

   * [Docker Plugin](https://plugins.jenkins.io/docker-plugin)

* Configure Docker Plugin

   Jenkins -> Manage Jenkins -> Configure System -> Cloud -> Add a new cloud -> Docker

### Android SDK
