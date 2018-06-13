# 
# docker build -t andrefabbro/jboss-base:latest .
#
FROM nimmis/java-centos:oracle-7-jdk
MAINTAINER Andre Fabbro <andre.fabbro@liferay.com>

ENV INSTALLDIR /opt/jboss

USER root

# Install packages necessary to run EAP
RUN yum update -y && yum -y install xmlstarlet saxon augeas bsdtar unzip && yum clean all

# Create a user and group used to launch processes
# The user ID 1000 is the default for the first "regular" user on Fedora/RHEL,
# so there is a high chance that this ID will be equal to the current user
# making it easier to use volumes (no permission issues)
# RUN groupadd -r jboss -g 1000 && useradd -u 1000 -r -g jboss -m -d /opt/jboss -s /sbin/nologin -c "JBoss user" jboss && \
#    chmod 755 /opt/jboss

# Set the working directory to jboss' user home directory
WORKDIR $INSTALLDIR

# Specify the user which should be used to execute all commands below
# USER jboss

COPY jboss-eap-6.4.0.zip $INSTALLDIR

RUN cd $INSTALLDIR && unzip -q jboss-eap-6.4.0.zip -d $INSTALLDIR && rm jboss-eap-6.4.0.zip

# Ensure signals are forwarded to the JVM process correctly for graceful shutdown
ENV LAUNCH_JBOSS_IN_BACKGROUND true

# Expose the ports we're interested in
EXPOSE 8080 9990

# Set the default command to run on boot
# This will boot JBoss in the standalone mode and bind to all interface
CMD ["/opt/jboss/jboss-eap-6.4/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0"]
