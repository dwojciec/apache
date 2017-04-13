## create a Jenkins server using plugins on Apache server
create a new openshift project
## Apache server
Create a Apache Server to play the role of Update Center for Jenkins with this structure :

http://**httpd-server**/download/plugins/.....

```
oc new-project apache-jenkins

oc new-build getwarped/s2i-httpd-server~https://github.com/dwojciec/apache.git --name=httpd-server

--> Found Docker image 2ab92d6 (6 days old) from Docker Hub for "getwarped/s2i-httpd-server"

    Apache HTTPD Server 
    ------------------- 
    S2I builder for hosting files with Apache HTTPD server

    Tags: builder, httpd

    * An image stream will be created as "s2i-httpd-server:latest" that will track the source image
    * A source build using source code from https://github.com/dwojciec/apache.git will be created
      * The resulting image will be pushed to image stream "httpd-server:latest"
      * Every time "s2i-httpd-server:latest" changes a new build will be triggered

--> Creating resources with label build=httpd-server ...
    imagestream "s2i-httpd-server" created
    imagestream "httpd-server" created
    buildconfig "httpd-server" created
--> Success
    Build configuration "httpd-server" created and build triggered.
    Run 'oc logs -f bc/httpd-server' to stream the build progress.
    
   

oc new-app httpd-server

--> Found image 83d6591 (2 minutes old) in image stream "apache-jenkins/httpd-server" under tag "latest" for "httpd-server"

    apache-jenkins/httpd-server-1:58d22633 
    -------------------------------------- 
    S2I builder for hosting files with Apache HTTPD server

    Tags: builder, httpd

    * This image will be deployed in deployment config "httpd-server"
    * Port 8080/tcp will be load balanced by service "httpd-server"
      * Other containers can access this service through the hostname "httpd-server"

--> Creating resources ...
    deploymentconfig "httpd-server" created
    service "httpd-server" created
--> Success
    Run 'oc status' to view your app.


oc expose service httpd-server

```

**Apache server created**  
![Openshift](https://raw.githubusercontent.com/dwojciec/apache/master/images/httpd-server-png)


**our Jenkins Update Center**

![Openshift](https://raw.githubusercontent.com/dwojciec/apache/master/images/apache.png)



using the variable **JENKINS_UC**


```

oc new-build jenkins:2~https://github.com/dwojciec/jenkins.git --context-dir=/1/test/s2i   --name=jenkins-plugin -e JENKINS_UC=httpd-server:8080

oc new-app jenkins-ephemeral -p NAMESPACE=<new-project>  -p JENKINS_IMAGE_STREAM_TAG=jenkins-plugin:latest

```

