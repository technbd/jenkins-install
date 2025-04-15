
## Jenkins Tools Configure: 

Configuring tools in Jenkins allows you to manage versions of build tools like JDK, Maven, Gradle, Node.js, etc., so you don’t have to hardcode installation paths in your pipelines.

Here’s a quick guide on how to configure tools in Jenkins, and how to use them in a Jenkinsfile.



### Set Time Zone:

Go ot `Manage Jenkins` - `Manage Users` or `Security` section - `Users` - `admin` click [Setting Gear] icon: 
- Select `Account`: 
- Time zone: Asia/Dhaka
- Save




### Folder create:

Go to `Dashboard` - click `+ New Item` - Enter an item name: `demo-project` - select `Folder` click `Ok`
- General: 
    - Display Name: demo-project
    - Description: demo-project

- Apply - Save



####  Create Job:

Go to `Dashboard` - click `+ New Item` : 
- Enter an item name: `job1-jenkinsfile `- click `Pipeline` - OK.

- `Pipeline` Section:
    - Definition: `Pipeline script`

```
pipeline {
    agent any

    stages {

        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }

    }
}
```

- click `Save`




### Jenkins User Management:

1. Go to `Manage Jenkins` - `Security` Section -  `Manage Users` or `Users` - click `+ Create User` : 
- Username: user1
- Password: password


2. Go to `Manage Jenkins` - `Security` Section - `Configure Global Security` or `Security` : 
- Authentication:
- [ ] Disable remember me		[-> By Default unchecked]

- Security Realm: [`Jenkins own user database`]

- Authorization: [`Project-based Matrix Authorization Strategy`]

- click `Add user`: user1
- Overall: `Read`
- Jobs: `Build`, `Configure`, `Create`.
- Apply - Save.





### Set Credentials: 

1. Go to `Manage Jenkins` - `Manage Credentials` or `Security` Section - click `Credentials` - click `(global)` - `+ Add Credentials`:

- Kind: `Username with password`
- Scope: Global (Jenkins, nodes, items, all clild items, etc)

- User: root
- Password: ****
- ID: host-191-ssh-pw
- Description: host-191-ssh-pw
- click `Create`


Or,


#### On Jenkins server: 192.168.10.193:

_From Jenkins server (192.168.10.193) to Remote host (192.168.10.191):_

```
ssh-keygen

ssh-copy-id root@192.168.10.191

cat ~/.ssh/id_rsa          [-->> Copy this private key:]
```


2. Go to `Manage Jenkins` - `Manage Credentials` or `Security` Section - click `Credentials` - click `(global)` - `+ Add Credentials`:

- Kind: `SSH Username with private key`
- Scope: Global (Jenkins, nodes, items, all clild items, etc)

- ID: host-191-ssh-key
- Description: host-191-ssh-key

- User: root
- Private Key: [ ✓ ] Enter directly
- Key: **On Jenkins server private key paste here**
- click `Create`





### Configure Master-Slave: (Same Node)

- Here, configure Master-Slave is same jenkins node


#### Step-1:

```
mkdir /jenkins-slave

chown -R jenkins:jenkins /jenkins-slave
```


#### Step-2: 
Go to `Manage Jenkins` - `Manage Nodes and Clouds` or `System Configuration` section - `Nodes` - click `+ New Node`: 
- Node Name: slave-1
- Type:	[✓] Permanent Agent
- click 'Create'

- Name: slave-1
- Description: slave-1

- Number of executors: [ `1` ] [-> By Default]
- Remote root directory: `/jenkins-slave`
- Labels: slave-1

- Usage: `use this node as much as possible` [-> By Default]
- Launch method: `Launch agent via execution of command on the controller` [-> By Default]
- Availability: `Keep this agnet online as much as possible`    [-> By Default]

- click `Save`


#### Step-3: Run the Agent: 

_Run from agent command line: (Unix):_

```
curl -sO http://192.168.10.193:8080/jnlpJars/agent.jar

java -jar agent.jar -url http://192.168.10.193:8080/ -secret <secret_text> -name "slave-1" -webSocket -workDir "/jenkins-slave" & 
```


Or,



_Download `agent.jar`:_

- click on `?` mark:  then download `agent.jar` [`agent.jar` can be downloaded from here]

- Launch command ?: `java -jar /jenkins-slave/agent.jar`		[-> upload the 'agent.jar' on this directory]




### Configure Master-Slave: (Remote Node)

- Here, configure Master-Slave is Remote node
- Jenkins server and remote node must be same java version 


### Method-1: (Using SSH Password)


#### Step-1:

_On Node 192.168.10.191:_

```
mkdir -p /jenkins-remote1
```


#### Step-2: 
Go to `Manage Jenkins` - `Manage Nodes and Clouds` or `System Configuration` section - `Nodes` - click `+ New Node`: 
- Node Name: remote-slave1
- Type:	[✓] Permanent Agent
- click 'Create'

- Name: remote-slave1
- Description: remote-slave1
- Number of executors: [ `1` ] [-> By Default]
- Remote root directory: `/jenkins-remote1`
- Labels: remote-slave1

- Usage: `use this node as much as possible` [-> By Default]
- Launch method: `Launch agent via SSH`
    - Host: 192.168.10.191
    - Credentials:  `+ Add` - `Jenkins` - Or: Select `host-191-ssh-pw`

- Host Key Verification Strategy?
    - `Non verifying Vefification Strategy`      [-> Worked]

- Availability: `Keep this agnet online as much as possible`    [-> By Default]
- click `Save`

- Then click `Launch Agent`





### Method-2: (SSH Private Key)

#### Step-1:


_On Node 192.168.10.191:_

```
mkdir -p /jenkins-remote2
```


_From Jenkins server (192.168.10.193) to Remote host (192.168.10.191):_

```
ssh-keygen

ssh-copy-id root@192.168.10.191

ssh root@192.168.10.191
```

```
cat ~/.ssh/id_rsa          [-->> Copy this private key:]
```



#### Step-2: 
Go to `Manage Jenkins` - `Manage Nodes and Clouds` or `System Configuration` section - `Nodes` - click `+ New Node`: 
- Node Name: remote-slave2
- Type:	[✓] Permanent Agent
- click 'Create'

- Name: remote-slave2
- Description: remote-slave2
- Number of executors: [ `1` ] [-> By Default]
- Remote root directory: `/jenkins-remote2`
- Labels: remote-slave2

- Usage: `use this node as much as possible` [-> By Default]

- Launch method: `Launch agent via SSH`
    - Host: 192.168.10.191
    - Credentials:  `+ Add` - `Jenkins` - Or: Select `host-191-ssh-key`


- Host Key Verification Strategy?
    - `Known hosts file Vefification Strategy`      [-> Worked]

- Availability: `Keep this agnet online as much as possible`    [-> By Default]
- click `Save`

- Then click `Launch Agent`





### Set JAVA HOME:


```
readlink -f $(which java)

/usr/lib/jvm/java-17-openjdk-17.0.14.0.7-3.el8.x86_64/bin/java
```


Go to `Manage Jenkins` - `Global Tool Configuration` or, `System Configuration` section - `Tools`:
- `JDK installations` Section  - `Add JDK` click: 
- Name: JAVA_HOME
- JAVA_HOME: /usr/lib/jvm/java-17-openjdk-17.0.14.0.7-3.el8.x86_64

- [ ] Install automatically	[-> unchecked]
- Apply - Save. 



### Set MAVEN HOME:


```
which mvn

/opt/maven/bin/mvn
```

Go to `Manage Jenkins` - `Global Tool Configuration` or, `System Configuration` section - `Tools`:
- `Maven installations` Section  - `Add Maven` click: 
- Name: MAVEN_HOME
- MAVEN_HOME: /opt/maven

- [ ] Install automatically	[-> unchecked]
- Apply - Save. 





### Set Git:

```
which git

/usr/bin/git
```


1. Go to `Manage Jenkins` - `Global Tool Configuration` or, `System Configuration` section - `Tools`:
- `Git installations` Section: 
- Name: GIT
- Path to Git executable: `/usr/bin/git`
- Apply - Save. 


2. Go to `Manage Jenkins` - `Manage Credentials` or `Security` Section - click `Credentials` - click `(global)` - `+ Add Credentials`:

- Kind: `Username with password`
- Scope: Global (Jenkins, nodes, items, all child items, etc)

- User: git_username
- Password: Github password or token here

- ID: git_auth
- Description: git_auth

- click `Create`




#### Set Git Auth: 

Click `Pipeline Syntax` under the Pipeline code box:


1. `Steps` Section: 
- Sample Step: [git: Git]  [-> select your need]

- Git ? :
    - Repository URL : https://github.com/page-cloud/java-docker-jenkinsfile.git
    - Branch : main 
    - Credentials: click `+ Add` - `Jenkins` - Or: select `git_auth`


- `Generate Pipeline Script` Click :

```
git branch: 'main', credentialsId: 'git_auth', url: 'https://github.com/page-cloud/java-docker-jenkinsfile.git'
```


### Set Docker:
- Install Plugins: Docker

```
which docker

/usr/bin/docker
```


1. Go to `Manage Jenkins` - `Global Tool Configuration` or, `System Configuration` section - `Tools`:
- `Docker installations` Section - click `Add Docker` : 
- Name: DOCKER
- Installation root: `/usr/bin`
- Apply - Save. 


2. Go to `Manage Jenkins` - `Manage Credentials` or `Security` Section - click `Credentials` - click `(global)` - `+ Add Credentials`:

- Kind: `Username with password`
- Scope: Global (Jenkins, nodes, items, all child items, etc)

- User: docker_username
- Password: Docker password or token here

- ID: docker_auth
- Description: docker_auth

- click `Create`



#### Set Docker Auth:


Click `Pipeline Syntax` under the Pipeline code box:


1. `Steps` Section: 
- Sample Step: `withCredentials: Bind credentials to variables`

- Bindings: `Username and password (separated)`
    - Username Variable: docker_user
    - Password Variable: docker_pw
    - Credentials: click `+ Add` - `Jenkins` - Or: select `docker_auth`


- `Generate Pipeline Script` Click :

```
withCredentials([usernamePassword(credentialsId: 'docker_auth', passwordVariable: 'docker_pw', usernameVariable: 'docker_user')]) {
    // some block
}
```

_Example:_
```
withCredentials([usernamePassword(credentialsId: 'docker_auth', passwordVariable: 'docker_pw', usernameVariable: 'docker_user')]) {
          sh "docker login -u ${env.docker_user} -p ${env.docker_pw}"
          sh 'docker push technbd/tomcat:v1'
}
```






