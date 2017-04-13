create a new openshift project

oc new-project <new-project>
oc new-build jenkins:2~https://github.com/dwojciec/jenkins.git --context-dir=/1/test/s2i   --name=jenkins-plugin
oc new-app jenkins-ephemeral -p NAMESPACE=<new-project>  -p JENKINS_IMAGE_STREAM_TAG=jenkins-plugin:latest

create a Apache Server to play the role of Update Center for Jenkins with this structure :
http://<httpd-server>/download/plugins/.....

oc new-build getwarped/s2i-httpd-server~https://github.com/dwojciec/apache.git --name=httpd-server
oc new-app httpd-server

expose the service as route and use the value : httpd-server:8080 for JENKINS_UC variable 
