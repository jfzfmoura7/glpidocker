# Use a imagem base do Debian
FROM debian:stable-20240423
ENV DEBIAN_FRONTEND noninteractive

# Preparação do ambiente
RUN apt update && apt dist-upgrade -y && apt autoremove -y
RUN apt install -y lsb-release apt-transport-https ca-certificates wget
RUN wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
RUN echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list && apt update

# Instalando Apache e PHP 8.1
RUN apt install -y --no-install-recommends apache2 php8.1 php8.1-mysql php8.1-ldap

# Instalando extensões do PHP 8.1
RUN apt install -y --no-install-recommends php8.1-xmlrpc php8.1-imap curl php8.1-curl php8.1-gd php8.1-mbstring php8.1-xml php8.1-apcu

# Instalando outras extensões e utilitários necessários
RUN apt install -y --no-install-recommends php8.1-intl php8.1-zip php8.1-bz2 cron wget ca-certificates jq libldap-common libsasl2-2 libsasl2-modules libsasl2-modules-db

# Limpeza pós-instalação
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
RUN ln -sf /dev/stdout /var/log/apache2/access.log \
    && ln -sf /dev/stderr /var/log/apache2/error.log
RUN sed -i "s/#AddDefaultCharset/AddDefaultCharset/g" /etc/apache2/conf-enabled/charset.conf
RUN echo "ServerSignature Off" >> /etc/apache2/apache2.conf
RUN rm -f /var/www/html/index.html

# Copiando arquivos de configuração e scripts
COPY glpi.sh change_upload_max_filesize.php default_upload_max_filesize.php /opt/
COPY 000-default.conf /etc/apache2/sites-enabled/000-default.conf
RUN chmod +x /opt/glpi.sh

# Configurações do ambiente do Apache
ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2.pid"
ENV APACHE_RUN_DIR="/var/run/apache2"
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

# Configuração final
WORKDIR /root
ENTRYPOINT ["/opt/glpi.sh"]
EXPOSE 80
VOLUME ["/var/www/html"]
