# AndroidCI

## CI Architecture

<img src="https://github.com/mytaxi/AndroidCI/blob/master/screenshots/jenkins_architecture.png?raw=true">

## Setup Environment

### Docker

* Download [Docker Community Edition](https://store.docker.com/search?offering=community&type=edition)

### Jenkins

* Pull the latest [Jenkins Docker image](https://hub.docker.com/_/jenkins/)

  ```console
  docker pull jenkins:2.60.3-alpine
  ```

* Run Jenkins

   Run Jenkins container with a mounted `jenkins_home` volume, all plugins and settings will be persisted.
   
   ```console
   docker run -p 8080:8080 -p 50000:50000 -v $(pwd)/jenkins_home:/var/jenkins_home jenkins:2.60.3-alpine
   ```

* Get the initial admin password

   ```console
   docker exec -it `docker ps -aq -f "ancestor=jenkins:2.60.3-alpine" -f "status=running"` bash -c 'cat /var/jenkins_home/secrets/initialAdminPassword'
   ```

* Install Plugins (no need if you use persistent volume)

   * [Docker Plugin](https://plugins.jenkins.io/docker-plugin)
   * [Android Lint](https://plugins.jenkins.io/android-lint)

* Configure Docker Plugin

   Setup guide (from the official plugin web page) is [here](https://wiki.jenkins.io/display/JENKINS/Docker+Plugin).

   **Jenkins** -> **Manage Jenkins** -> **Configure System** -> **Cloud** -> **Add a new cloud** -> **Docker**
   
   <img src="https://github.com/mytaxi/AndroidCI/blob/master/screenshots/jenkins_config_cloud_docker.png?raw=true">
   
   Regarding the **Docker Host URI**, it should be `tcp://<YOUR_DOCKER_HOST_IP_ADDRESS>:2376`
   
   For the sake of workshop, we will use the Docker host on the same machine where the Jenkins container is running.  Thus you can use the IP address of your host machine.  Or simply use a DNS name `host.docker.internal` if you're using the latest version of *docker-for-mac* or *docker-for-windows* ([not yet implemented for Linux](https://github.com/docker/for-linux/issues/264)).
   
   And then you'll have to listen to the port:
   
   ```bash
   # on macOS
   brew install socat
   socat TCP-LISTEN:2376,reuseaddr,fork UNIX-CONNECT:/var/run/docker.sock
   
   # on Linux (Debian / Ubuntu)
   # changing DOCKER_OPTS is optional
   # Use DOCKER_OPTS to modify the daemon startup options
   # echo -e '\nDOCKER_OPTS="-H tcp://0.0.0.0:2376 -H unix:///var/run/docker.sock"\n' | sudo tee --append /etc/default/docker > /dev/null
   # find the location of systemd unit file
   systemctl status docker
   #=> docker.service - Docker Application Container Engine
   #=> Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   sudo sed -i.bak 's?ExecStart.*?ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2376 -H unix:///var/run/docker.sock?g' /lib/systemd/system/docker.service
   sudo systemctl daemon-reload
   sudo systemctl restart docker.service
   # check if the port is listened or not
   sudo netstat -tulpn | grep LISTEN
   ```
   
   Finally, you can click **Docker Agent templates...** -> **Add Docker Template**
   
   <img src="https://github.com/mytaxi/AndroidCI/blob/master/screenshots/jenkins_config_docker_agent_template.png?raw=true">

* SSH authentication

   Create a new bash session to the Jenkins container
   
   ```console
   docker exec -it $(docker ps -aq -f "ancestor=jenkins:2.60.3-alpine" -f "status=running") bash
   ```
   
   Generate a new SSH key
   
   ```console
   yes y | ssh-keygen -t rsa -N "" -b 4096 -C "your_email@example.com"
   ```
   
   The keys will be generated here by default:
   
   ```console
   /var/jenkins_home/.ssh/id_rsa.pub
   /var/jenkins_home/.ssh/id_rsa
   ```
   
   Copy the content of your `id_rsa.pub` file and put into the mounted `authorized_keys` file.
   
   Add and set a new SSH credential in Jenkins.  Go to Jenkins -> Credentials - System (<YOUR_JENKINS_URL>/credentials/store/system/)
   
   <img src="https://github.com/mytaxi/AndroidCI/blob/master/screenshots/jenkins_config_credentials.png?raw=true">

### Android SDK

[Project and source code](https://github.com/thyrlian/AndroidSDK)

[Docker Image](https://hub.docker.com/r/thyrlian/android-sdk/)
