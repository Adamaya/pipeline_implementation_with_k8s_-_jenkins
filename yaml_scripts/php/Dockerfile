FROM centos

RUN yum install httpd httpd-tools -y
RUN yum install php -y

COPY *.php /var/www/html/

EXPOSE 80

CMD /usr/sbin/httpd -DFOREGROUND && tail -f /dev/null


