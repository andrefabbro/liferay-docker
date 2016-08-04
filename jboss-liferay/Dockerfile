#
# e.g. 
# docker build --build-arg LFR_DEPS_FOLDER=liferay-portal-dependencies-6.2-ee-sp14
#
FROM andrefabbro/jboss-base
MAINTAINER Andre Fabbro <andre.fabbro@liferay.com>

ENV INSTALLDIR /opt/liferay

# Name of the Folder that is extracted from the dependencies ZIP
ARG LFR_DEPS_FOLDER

USER root

RUN yum -y install net-tools

RUN mkdir -p $INSTALLDIR \
    && chmod -R 755 $INSTALLDIR

WORKDIR $INSTALLDIR

COPY liferay-package/*.war $INSTALLDIR

# Get the Liferay WAR and place in JBoss deployment folder
RUN cd $INSTALLDIR \
    && ln -s /opt/jboss/jboss-eap-6.4 jboss-eap-6.4 \
    && unzip -q *.war -d ROOT.war \
    && chmod -R 775 ROOT.war/ \
    && mv ROOT.war jboss-eap-6.4/standalone/deployments/ \
    && touch jboss-eap-6.4/standalone/deployments/ROOT.war.dodeploy \
    && rm *.war

COPY liferay-dependencies/*.zip $INSTALLDIR

# Get the Liferay dependencies and put into JBoss modules folder
RUN unzip -q *.zip \
    && mkdir -p jboss-eap-6.4/modules/com/liferay/portal/main \
    && mv $LFR_DEPS_FOLDER/*.jar jboss-eap-6.4/modules/com/liferay/portal/main \
    && touch jboss-eap-6.4/modules/com/liferay/portal/main/module.xml \
    && rm *.zip && rm -rf $LFR_DEPS_FOLDER

# Get the Liferay license
COPY liferay-license/*.xml $INSTALLDIR

RUN mkdir deploy && cp *.xml deploy/ && rm *.xml

COPY liferay-patching-tool/*.zip $INSTALLDIR

# Install de patching tool
RUN unzip -q *.zip && rm *.zip

# Add the modules descriptor for Liferay Dependencies
RUN echo $'<?xml version="1.0"?> \n\
<module xmlns="urn:jboss:module:1.1" name="com.liferay.portal"> \n\
    <resources> \n\
        <resource-root path="hsql.jar" /> \n\
        <resource-root path="portal-service.jar" /> \n\
        <resource-root path="portlet.jar" /> \n\
    </resources> \n\
    <dependencies> \n\
        <module name="javax.api" /> \n\
        <module name="javax.mail.api" /> \n\
        <module name="javax.servlet.api" /> \n\
        <module name="javax.servlet.jsp.api" /> \n\
        <module name="javax.transaction.api" /> \n\
    </dependencies> \n\
</module>' >> $INSTALLDIR/jboss-eap-6.4/modules/com/liferay/portal/main/module.xml

#
# Instrument the standalone.xml with the specific configurations for the portal
#
RUN sed -i '/<\/extensions>/a\\n    <system-properties>\n\t<property name="org.apache.catalina.connector.URI_ENCODING" value="UTF-8"\/>\n\t<property name="org.apache.catalina.connector.USE_BODY_ENCODING_FOR_QUERY_STRING" value="true"\/>\n    <\/system-properties>\n' $INSTALLDIR/jboss-eap-6.4/standalone/configuration/standalone.xml

RUN sed -i '/scan-interval="5000"/c \\t    <deployment-scanner path="deployments" relative-to="jboss.server.base.dir" scan-interval="5000" deployment-timeout="240"/>' $INSTALLDIR/jboss-eap-6.4/standalone/configuration/standalone.xml

RUN sed -i '/<\/security-domains>/i\\n\t\t<security-domain name="PortalRealm">\n\t\t    <authentication>\n\t\t\t<login-module code="com.liferay.portal.security.jaas.PortalLoginModule" flag="required"/>\n\t\t    </authentication>\n\t\t</security-domain>\n' $INSTALLDIR/jboss-eap-6.4/standalone/configuration/standalone.xml

RUN sed -i '/urn\:jboss\:domain\:web\:2\.2/a \\n\t    <configuration>\n\t\t<jsp-configuration development="true"/>\n\t    </configuration>\n' $INSTALLDIR/jboss-eap-6.4/standalone/configuration/standalone.xml

RUN sed -i 's/enable-welcome-root="true"/enable-welcome-root="false"/' $INSTALLDIR/jboss-eap-6.4/standalone/configuration/standalone.xml

# Create the default JBoss security policy
RUN echo $'grant { \n\
    permission java.security.AllPermission; \n\
};' > $INSTALLDIR/jboss-eap-6.4/bin/server.policy

# Activate the JBoss Security Manager
RUN sed -i '/# SECMGR="true"/c SECMGR="true"' $INSTALLDIR/jboss-eap-6.4/bin/standalone.conf

# Insert custom JVM Options, 1.5GB for heap size and 512MB for perm
RUN echo $'\n\
# Custom configuration for Liferay \n\
JAVA_OPTS="$JAVA_OPTS -Dfile.encoding=UTF-8 -Djava.net.preferIPv4Stack=true -Djava.security.policy=/opt/liferay/jboss-eap-6.4/bin/server.policy -Djboss.home.dir=/opt/liferay/jboss-eap-6.4 -Duser.timezone=GMT -Xmx1536m -XX:MaxPermSize=512m"' >> $INSTALLDIR/jboss-eap-6.4/bin/standalone.conf

# Ensure signals are forwarded to the JVM process correctly for graceful shutdown
ENV LAUNCH_JBOSS_IN_BACKGROUND true

# Expose the ports we're interested in
EXPOSE 8080 9990 8009

# Set the default command to run on boot
# This will boot JBoss in the standalone mode and bind to all interface
# CMD ["bash"]
CMD ["/opt/liferay/jboss-eap-6.4/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0"]


