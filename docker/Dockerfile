FROM php:8.1.13-apache

# Extention qui permet d'installer des extentions PHP
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

# Rendu exécutable et installations des extensions
RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions intl && \
    curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
    mv composer.phar /usr/local/bin/composer && \
    apt update && apt install -yqq zip git

# Copie le projet entier dans /var/www
COPY . /var/www/

# Copie la configuration du serveur apache et suppression du fichier par défaut de l'image docker
COPY ./docker/apache.conf /etc/apache2/sites-available/000-default.conf

# Enregistrement de la variable d'environnement à prod
ENV APP_ENV=prod


RUN rm -rf /var/www/vendor && \
    rm -rf /var/www/.git && \
    rm -rf /var/www/.env.local && \
    cd /var/www && \
    composer install && \
    php bin/console cache:clear && \
    php bin/console cache:warmup && \
    chown -R www-data:www-data /var/www

# Positionnement du repertoire de travail
WORKDIR /var/www/

# Ce lance au démarrage du container docker
ENTRYPOINT ["bash", "./docker/docker.sh"]

# Exposition sur le port 80
EXPOSE 80