FROM ubuntu:20.04
LABEL maintainer="leonardo.sala@psi.ch"
RUN apt-get update -y --fix-missing

# COPY tzscript.sh /usr/local/bin
# RUN /usr/local/bin/tzscript.sh
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get install -y tzdata mariadb-client apache2 \
  php php-mbstring php-snmp php-gd php-mysql php-zip php-curl php-ldap php-redis \
  php-xml locales graphviz wget nano curl
RUN ln -fs /usr/share/zoneinfo/Europe/Zurich /etc/localtime
RUN dpkg-reconfigure --frontend noninteractive tzdata
#RUN yum -y install httpd \
#  php php-mbstring php-snmp php-zip php-ldap\
#  php-xml php-gettext php-pdo php-mysqlnd php-json wget && yum -y clean all
#  a2enmod rewrite authnz_ldap && rm /var/www/html/index.html
# RUN rm /var/www/html/index.html
RUN apt-get -y install mariadb-client apache2 \
  php php-mbstring php-snmp php-gd php-mysql php-zip php-curl php-ldap php-redis \
  php-xml locales graphviz wget nano && \
  localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8 && \
  a2enmod rewrite authnz_ldap 
RUN rm /var/www/html/index.html

ENV LANG en_US.utf8


ENV version=19.01

RUN wget https://opendcim.org/packages/openDCIM-${version}.tar.gz
RUN tar xvzf openDCIM-${version}.tar.gz 
RUN mv openDCIM-${version}/* /var/www/html/
# COPY dcim/ /var/www/html/
RUN rm -rf openDCIM-${version}.tar.gz openDCIM-${version}
COPY db-inc.php /var/www/html/db.inc.php
# Use a different installer for the containers - automatic, and requiring the db to be new or at least at 19.01
# RUN cp /var/www/html/container-install.php /var/www/html/install.php
COPY opendcim.conf /etc/apache2/sites-available/000-default.conf
# COPY 100-opendcim.ini /etc/php/7.2/apache2/conf.d

# RUN mkdir /var/www/html/pictures
RUN mkdir -p /var/www/html/vendor/mpdf/ttfontdata && mkdir -p /var/www/html/assets/pictures && mkdir -p /var/www/html/assets/reports && mkdir -p /var/www/html/assets/drawings && chown -R www-data:www-data /var/www/html && \
  chmod -R 775 /var/www/html/assets /var/www/html/vendor/mpdf/ttfontdata 
  #&& rm -f /var/log/apache2/*.log && \
  #ln -s /dev/stderr /var/log/apache2/error.log && ln -s /dev/stdout /var/log/apache2/access.log

ENV OPENDCIM_DB_HOST=db
ENV OPENDCIM_AUTH=LDAP
# ENV OPENDCIM_AUTH=Apache

RUN a2enmod ssl
RUN echo ${OPENDCIM_AUTH}
RUN bash -c 'if [ "${OPENDCIM_AUTH}" == "Apache" ]; then htpasswd -bc /var/www/html/.htpasswd admin admin; fi'
ENV OPENDCIM_HOSTNAME=localhost

CMD apachectl -D FOREGROUND