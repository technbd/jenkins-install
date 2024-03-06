## Jenkins install:

Jenkins is an open-source automation server widely used for continuous integration and continuous delivery (CI/CD) pipelines. It helps automate the process of building, testing, and deploying software.

To install Jenkins, you can follow these general steps. Note that the exact commands and procedures might vary slightly based on your operating system and environment, but these are the typical steps:


### Prerequisites:
Recommended hardware configurations:

- 4 GB+ of RAM
- 50 GB+ of drive space
- Java 


### Install Java:
The following Java versions are required to run Jenkins:

| Supported Java versions   |      LTS release      |  Weekly release |
|---------------------------|:---------------------:|----------------:|
| Java 11, Java 17, or Java 21 | 2.426.1 (November 2023)  | 2.419 (August 2023)      |
| Java 11 or Java 17           | 2.361.1 (September 2022) |   2.357 (June 2022)      |
| Java 8, Java 11, or Java 17  | 2.346.1 (June 2022)      |    2.340 (March 2022)    |
| Java 8 or Java 11            | 2.164.1 (March 2019)     |    2.164 (February 2019) |


```
### Ubuntu:

sudo apt update
sudo apt-cache search openjdk-11
sudo apt install openjdk-11-jdk -y 
```


```
### Centos:

yum install java-11-openjdk-devel -y 
```


```
java -version

openjdk version "11.0.21" 2023-10-17
OpenJDK Runtime Environment (build 11.0.21+9-post-Ubuntu-0ubuntu120.04)
OpenJDK 64-Bit Server VM (build 11.0.21+9-post-Ubuntu-0ubuntu120.04, mixed mode, sharing)
```


**Note:** If you install an unsupported Java version, your Jenkins controller will not run.


### Install Jenkins:
On Debian and Debian-based distributions like Ubuntu you can install Jenkins through apt.

A LTS (Long-Term Support) release is chosen every 12 weeks from the stream of regular releases as the stable release for that time period. It can be installed from the debian-stable apt repository. Run bash:


```
sudo chmod +x install-jenkins.sh
sudo bash install-jenkins.sh
```


```
sudo systemctl start jenkins
sudo systemctl status jenkins
```


```
netstat -tlpn | grep 8080

tcp6       0      0 :::8080     :::*     LISTEN      120340/java
```


### Jenkins Port Change: (Optional)
Here, "4040" was chosen but you can put another port available.

```
ll /usr/lib/systemd/system/jenkins.service
```


```
vim /usr/lib/systemd/system/jenkins.service

[Service]
Environment="JENKINS_PORT=4040"
```


```
systemctl daemon-reload
systemctl restart jenkins
systemctl status jenkins
```


```
netstat -tlpn | grep 4040

tcp6       0      0 :::4040      :::*    LISTEN      128289/java
```


### Unlocking Jenkins:
Browse to http://your-server-ip:8080 (or whichever port you configured for Jenkins when installing it) and wait until the Unlock Jenkins page appears.


```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

On the Unlock Jenkins page, paste this password into the Administrator password field and click Continue.


### Important Plugins:
Log in to your Jenkins instance and access the Jenkins server Dashboard navigate -> Manage Jenkins > Plugins -> select Available plugins -> search required plugins and install:

- Role-based Authorization Strategy
- Build Authorization Token Root
- Pipeline: Deprecated Groovy Libraries
- pipeline-groovy-lib
- Build Pipeline
- Blue ocean
- ThinBackup
- Git
- Credentials Binding
- SSH Agent
- Publish Over SSH
- Ansible
- Maven Integration
- Maven Invoker
- Deploy to container
- Copy Artifact
- Docker
- Docker Pipeline
- docker-build-step
- Docker Compose Build Step
- CloudBees Docker Build and Publish
- Ant
- JUnit Realtime Test Reporter
- openJDK-native-plugin
- Eclipse Temurin installer


### Links:

- [Jenkins install](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu)



Remember to refer to the official Jenkins documentation for detailed instructions specific to your environment.




