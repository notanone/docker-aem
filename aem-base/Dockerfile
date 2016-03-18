FROM centos:7
MAINTAINER Yury Bialykh

# Java
ENV JAVA_VERSION 8u74
ENV BUILD_VERSION b02
# AEM
ENV CQ_FOREGROUND y
ENV CQ_VERBOSE    y
ENV CQ_NOBROWSER  y
ENV CQ_JVM_OPTS   "-server -Xmx1024M -Xms512M"
ENV AEM_QUICKSTART_FILE AEM_6.1_Quickstart.jar
ENV AEM_WORKING_DIR /opt/aem
ENV AEM_CONFIG_SCRIPT_DIR /opt/aem-config
ENV AEM_CONFIG_JAR aem-config.jar

# Upgrading system
RUN yum -y upgrade && \
    yum -y install wget && \
    yum -y install python-setuptools && \
    yum clean all

RUN easy_install supervisor

# Downloading Java
RUN wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jre-$JAVA_VERSION-linux-x64.rpm" -O /tmp/jre-8-linux-x64.rpm

RUN yum -y install /tmp/jre-8-linux-x64.rpm && \
    alternatives --install /usr/bin/java jar /usr/java/latest/bin/java 200000 && \
    rm -rf /tmp/jre-8-linux-x64.rpm

#Copies required build media
ADD $AEM_QUICKSTART_FILE $AEM_WORKING_DIR/$AEM_QUICKSTART_FILE
ADD license.properties $AEM_WORKING_DIR/license.properties
ADD $AEM_CONFIG_JAR $AEM_CONFIG_SCRIPT_DIR/$AEM_CONFIG_JAR
ADD supervisord.conf /etc/supervisord.conf

# Extracts AEM
WORKDIR $AEM_WORKING_DIR
RUN java -Xmx2024M -jar $AEM_QUICKSTART_FILE -unpack -r nosamplecontent -v

RUN mkdir -p $AEM_WORKING_DIR/crx-quickstart/install
ADD hotfix/* $AEM_WORKING_DIR/crx-quickstart/install/
ADD start $AEM_WORKING_DIR/crx-quickstart/bin/start

# Adding whatever author/publish specific packages are out there
ONBUILD ADD packages/* $AEM_WORKING_DIR/crx-quickstart/install/

ENV JAVA_HOME /usr/java/latest

WORKDIR $AEM_WORKING_DIR
