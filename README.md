# Liferay EE Docker in JBoss EAP 6.4

## Create the JBoss base image

Copy your Jboss EAP 6.4 inside the folder jboss-base, then run:

```
cd jboss-base/
docker build -t andrefabbro/jboss-base:latest .
```

## Create the Liferay EE base image

Copy the Liferay EE package, dependencies, license and patching tool inside the folders inside jboss-liferay:

```
cp liferay-portal-dependencies-6.2-ee-sp14.zip jboss-liferay/liferay-dependencies/

cp license.xml jboss-liferay/liferay-license/

cp liferay-portal-6.2-ee-sp14.zip jboss-liferay/liferay-package/

cp patching-tool-22.zip jboss-liferay/liferay-patching-tool
```

The run:

```
cd jboss-liferay/

docker build --build-arg LFR_DEPS_FOLDER=liferay-portal-dependencies-6.2-ee-sp14 -t andrefabbro/liferay-jboss:latest .
```

> Notice that you should send the argument *LFR_DEPS_FOLDER* that should be the same as the folder inside the zip that contains the liferay dependencies. 