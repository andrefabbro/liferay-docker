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
mkdir -p jboss-liferay/liferay-dependencies/
cp liferay-portal-dependencies-6.2-ee-sp14.zip jboss-liferay/liferay-dependencies/

mkdir -p jboss-liferay/liferay-license/
cp license.xml jboss-liferay/liferay-license/

mkdir -p jboss-liferay/liferay-package/
cp liferay-portal-6.2-ee-sp14.zip jboss-liferay/liferay-package/

mkdir -p jboss-liferay/liferay-patching-tool
cp patching-tool-22.zip jboss-liferay/liferay-patching-tool
```

Then run:

```
cd jboss-liferay/

docker build --build-arg LFR_DEPS_FOLDER=liferay-portal-dependencies-6.2-ee-sp14 -t andrefabbro/liferay-jboss:latest .
```

> Notice that you should send the argument *LFR_DEPS_FOLDER* that should be the name of the folder inside the zip that contains the liferay dependencies (this could change for each package). 

## Run the Machine

In order to create a new instance, you should run:

```
docker run -d -p 9990:9990 -p 8080:8080 --name liferay andrefabbro/liferay-jboss:latest
```

Then, see the log:

```
docker logs --tail=100 -f liferay
```

And open you browser to the new Liferay EE instance: ```http://localhost:8080/```.
