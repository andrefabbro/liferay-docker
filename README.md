# Docker Liferay DXP + JBoss EAP 7.0.x

These are Dockerfiles for build images of Liferay DXP for JBoss EAP 7.0.

#### Instructions

First, you need a repository where you have to put the required binaries for install Liferay DXP, and these files can be found on customer page in liferay.com. The URL for these binaries must be sent as parameters of the docker build command. Which are the following:

* **LIFERAY_PACKAGE_URL**: The WAR file of Liferay DXP;
* **LIFERAY_DEPENDENCIES_URL**: Dependencies of the DXP;
* **LIFERAY_LICENSE_URL**: A valid license for DXP;
* **LIFERAY_PATCHING_TOOL_URL**: The last version of Liferay Patching Tool;
* **LIFERAY_OSGI_URL**: The OSGi package for DXP;
* **LIFERAY_TOOLS_URL**: The 'tools' folder (zipped) that could be found in any Liferay bundle package;
* **LIFERAY_WORK_URL**: The 'work' folder (zipped) that could be found in any Liferay bundle package;
* **LIFERAY_FIX_PACK**: The last fix pack for Liferay DXP.

Additionally, you have to get your JBoss EAP and put in a remote location for download too, with the following parameter:

* **JBOSS7_PACKAGE_URL**: The JBoss 7 package, that you can download from the Red Hat site.

Upload these binaries in some repo, for instance: http://**<<YOUR_PACKAGE_REPOSITORY>>**/, then you should build the images like the following example:

First, build the jboss base image:

```
cd jboss-base/
docker build \
 --build-arg JBOSS7_PACKAGE_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/jboss-eap-7.0.0.zip"
 -t andrefabbro/jboss-7-base:latest .
```

Then, build the DXP:

```
cd dxp/
docker build \
 --build-arg LIFERAY_PACKAGE_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/liferay-dxp-digital-enterprise-7.0-sp6-20171010144253003.war" \
 --build-arg LIFERAY_DEPENDENCIES_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/liferay-dxp-digital-enterprise-dependencies-7.0-sp6-20171010144253003.zip" \
 --build-arg LIFERAY_LICENSE_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/activation-key-development.xml" \
 --build-arg LIFERAY_PATCHING_TOOL_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/patching-tool-2.0.7.zip" \
 --build-arg LIFERAY_OSGI_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/liferay-dxp-digital-enterprise-osgi-7.0-sp6-20171010144253003.zip" \
 --build-arg LIFERAY_TOOLS_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/liferay-dxp-digital-enterprise-tools-7.0-sp6-20171010144253003.zip" \
 --build-arg LIFERAY_WORK_URL="http://<<YOUR_PACKAGE_REPOSITORY>>/liferay-dxp-digital-enterprise-work-7.0-sp6-20171010144253003.zip" \
 --build-arg LIFERAY_FIX_PACK="http://<<YOUR_PACKAGE_REPOSITORY>>/liferay-fix-pack-de-39-7010.zip" \
 -t andrefabbro/dxp-jboss7:latest. 
```

##### Run

```
docker run -d -p 9990:9990 -p 8080:8080 --name dxp andrefabbro/dxp-jboss7:latest
```

Then, see the log:

```
docker logs --tail=100 -f dxp
```

Open you browser and point to the new Liferay DXP instance: ```http://localhost:8080/```.
