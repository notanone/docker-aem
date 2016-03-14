FROM centos:7
MAINTAINER Yury Bialykh

ENV DISPATCHER_FILE dispatcher-apache2.4-4.1.12.so   
ENV DISPATCHER_TAR dispatcher-apache2.4-linux-x86-64-ssl10-4.1.12.tar.gz
ENV DISPATCHER_MOD_URL https://www.adobeaemcloud.com/content/companies/public/adobe/dispatcher/dispatcher/_jcr_content/top/download_11/file.res/$DISPATCHER_TAR
ENV HTTPD_LOG_DIR /var/log/httpd

RUN yum -y update && \
    yum -y install httpd && \
    yum -y install wget && \
    yum -y install tar && \
    yum clean all

# Download the Dispatcher
RUN wget $DISPATCHER_MOD_URL
RUN mkdir -p dispatcher
RUN tar -C dispatcher -zxvf $DISPATCHER_TAR
RUN ls ./dispatcher

# Copy Dispatcher
RUN cp ./dispatcher/$DISPATCHER_FILE /usr/lib64/httpd/modules/$DISPATCHER_FILE
RUN ln -s /usr/lib64/httpd/modules/$DISPATCHER_FILE /usr/lib64/httpd/modules/mod_dispatcher.so

ADD dispatcher.conf /etc/httpd/conf.modules.d/dispatcher.conf
ADD dispatcher.any /etc/httpd/conf.d/dispatcher.any
ADD 80-dispatcher.conf /etc/httpd/conf.d/80-dispatcher.conf

EXPOSE 80

# Simple startup script to avoid some issues observed with container restart 
ADD run-httpd.sh /run-httpd.sh
RUN chmod -v +x /run-httpd.sh

# Allow httpd user to modify /var/www
RUN chown -R apache:apache /var/www

CMD ["/run-httpd.sh"]
