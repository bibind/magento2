FROM ubuntu:18.04

# Setup common packages, repo info, etc
RUN apt-get update && apt-get dist-upgrade -y && apt-get install -y whois sudo vim htop software-properties-common sendmail git iproute2 iputils-ping lsof curl unzip && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update

# Install packages
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y apache2 apache2-bin apache2-data apache2-utils libapache2-mod-fcgid php7.2-bcmath php7.2-cli php7.2-common php7.2-curl php7.2-fpm php7.2-gd php7.2-intl php7.2-json php7.2-mbstring php7.2-mysql php7.2-opcache php7.2-readline php7.2-soap php7.2-xml php7.2-xsl php7.2-zip php-xdebug yarn mariadb-client
RUN curl -sL https://deb.nodesource.com/setup_8.x | bash -
RUN apt-get install -y nodejs

# Setup PHP
RUN mkdir /run/php && touch /run/php/php-fpm.sock

# Setup apache
RUN ln -s /etc/apache2/mods-available/proxy.conf /etc/apache2/mods-enabled/proxy.conf && ln -s /etc/apache2/mods-available/proxy.load /etc/apache2/mods-enabled/proxy.load && ln -s /etc/apache2/mods-available/headers.load /etc/apache2/mods-enabled/headers.load && ln -s /etc/apache2/mods-available/proxy_fcgi.load /etc/apache2/mods-enabled/proxy_fcgi.load && ln -s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/rewrite.load && ln -s /etc/apache2/mods-available/ssl.conf /etc/apache2/mods-enabled/ssl.conf && ln -s /etc/apache2/mods-available/ssl.load /etc/apache2/mods-enabled/ssl.load && ln -s /etc/apache2/mods-available/socache_shmcb.load /etc/apache2/mods-enabled/socache_shmcb.load

# Create new user
RUN useradd -m -p `mkpasswd "inchoo"` -s /bin/bash inchoo && adduser inchoo sudo && ln -s /var/www/html /home/inchoo/html && echo "alias php-debug='php -d xdebug.remote_autostart=1'" > /home/inchoo/.bash_aliases

# Add custom configuration
ADD config/fpm.conf /etc/php/7.2/fpm/pool.d/www.conf

# Add custom scripts
ADD start.sh /start.sh
ADD scripts/robo.phar /usr/bin/robo
ADD scripts/composer.phar /usr/bin/composer

RUN chmod 755 /start.sh && chmod 755 /usr/bin/robo && chmod 755 /usr/bin/composer

# Cleanup
RUN apt-get autoremove -y && apt-get clean && rm -rf /var/cache/apk/* /var/tmp/* /tmp/*

WORKDIR /var/www/html

CMD ["/start.sh"]
