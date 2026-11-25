# source image
FROM dunglas/frankenphp:1-php8.5

ARG USER=frankenstein

RUN \
    groupadd -g 1000 $USER; \
    useradd -u 1000 -g 1000 $USER; \
    mkdir /home/$USER; \
    setcap CAP_NET_BIND_SERVICE=+eip /usr/local/bin/frankenphp; \
    chown -R $USER:$USER /data/caddy && chown -R $USER:$USER /config/caddy

# Update system packages and add other needed packages
RUN apt-get update
RUN apt-get -y install bash rsync git --fix-missing --no-install-recommends --assume-yes

# Install php extension
RUN install-php-extensions intl zip pdo pdo_pgsql mbstring redis xml bcmath opcache xdebug

# Copy opcache settings
COPY php/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY php/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Add composer
COPY --from=composer /usr/bin/composer /usr/bin/composer

# Copy configuration files for php - this copies the default development config provided in the image.
# If needed, we can overwrite some directives in the Caddyfile itself
RUN cp $PHP_INI_DIR/php.ini-development $PHP_INI_DIR/php.ini

# Set working directory
WORKDIR /var/www

# switch to the user who will be running the app
USER ${USER}:${USER}

# Expose ports
EXPOSE 80
EXPOSE 443
EXPOSE 443/udp

# Run the Caddy server
CMD composer install; frankenphp run --config /etc/caddy/Caddyfile
