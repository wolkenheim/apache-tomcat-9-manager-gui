# Enable Manager Gui in Apache Tomcat 9 Docker Image

The official Apache Tomcat Docker Images don´t come with the Manager Gui enabled by default. To enable them you can use the following steps. You could either clone this repository and go straight to step 6. Or you follow the steps, which I recommend. This should hopefully work in future versions

0. Make directory for local files, set absolute path for docker commands
```
mkdir config
ABS_PATH=`pwd`
```
1. Run image initally
```
docker run -p8888:8080 --name tomkat \
tomcat:9.0.30-jdk11-openjdk
```
2. Get User File
```
docker cp tomkat:/usr/local/tomcat/conf/tomcat-users.xml $ABS_PATH/config/tomcat-users.xml
```
3. Edit user File
edit file, add user 
```vi config/tomcat-users.xml```
```
  <role rolename="manager-gui"/>
  <user username="tomkat" password="change-me" roles="manager-gui"/>
```
4. Get original webapps to replace the currently empty webapps directory in docker container
```
docker cp tomkat:/usr/local/tomcat/webapps.dist $ABS_PATH/config/webapps
```
5. Edit access valve for manager gui
```
vi config/webapps/manager/META-INF/context.xml
```
Place comments around the valve directive like this
```
<Context antiResourceLocking="false" privileged="true" >
	<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
-->
</Context>
```

6. For restarting the container you might want to use:
```
docker stop tomkat && docker rm tomkat
```

7. Use local files in your docker image

```
docker run -p8888:8080 --name tomkat \
-v $ABS_PATH/config/tomcat-users.xml:/usr/local/tomcat/conf/tomcat-users.xml \
-v $ABS_PATH/config/webapps:/usr/local/tomcat/webapps \
tomcat:9.0.30-jdk11-openjdk
```

8. The Manager should work now with a password prompt at 
http://localhost:8888/manager 
