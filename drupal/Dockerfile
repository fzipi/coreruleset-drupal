#
#
ARG PHP_BASE=7.1-apache-buster

FROM debian:stable-slim as source

ARG DRUPAL_VERSION

RUN set -eux; \
  \
  apt-get update; \
  apt-get install -y --no-install-recommends \
  ca-certificates \
  git; \
  mkdir /opt/drupal; \
  git clone https://git.drupalcode.org/project/drupal.git --branch ${DRUPAL_VERSION} --depth 1 /opt/drupal


# from https://www.drupal.org/docs/system-requirements/php-requirements
FROM php:${PHP_BASE}

# install the PHP extensions we need
RUN set -eux; \
	\
	if command -v a2enmod; then \
	a2enmod rewrite; \
	fi; \
	\
	savedAptMark="$(apt-mark showmanual)"; \
	\
	apt-get update; \
	apt-get install -y --no-install-recommends \
	libfreetype6-dev \
	libjpeg-dev \
	libpng-dev \
	libpq-dev \
	libzip-dev \
	; \
	\
	docker-php-ext-configure gd \
	--with-freetype \
	--with-jpeg=/usr \
	; \
	\
	docker-php-ext-install -j "$(nproc)" \
	gd \
	opcache \
	pdo_mysql \
	pdo_pgsql \
	zip \
	; \
	\
	# reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
	apt-mark auto '.*' > /dev/null; \
	apt-mark manual $savedAptMark; \
	ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
	| awk '/=>/ { print $3 }' \
	| sort -u \
	| xargs -r dpkg-query -S \
	| cut -d: -f1 \
	| sort -u \
	| xargs -rt apt-mark manual; \
	\
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	rm -rf /var/lib/apt/lists/*

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
	echo 'opcache.memory_consumption=128'; \
	echo 'opcache.interned_strings_buffer=8'; \
	echo 'opcache.max_accelerated_files=4000'; \
	echo 'opcache.revalidate_freq=60'; \
	echo 'opcache.fast_shutdown=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini

COPY --from=composer:1 /usr/bin/composer /usr/local/bin/

COPY --from=source /opt/drupal /opt/drupal

# https://www.drupal.org/node/3060/release
WORKDIR /opt/drupal

RUN set -eux; \
	export COMPOSER_HOME="$(mktemp -d)"; \
	mkdir sites/default/files; \
	chmod a+w sites/default/files; \
	composer install --no-dev; \
	rmdir /var/www/html; \
	ln -sf /opt/drupal /var/www/html; \
	cp ./sites/default/default.settings.php ./sites/default/settings.php; \
	chown www-data ./sites/default/settings.php; \
	# delete composer cache
	rm -rf "$COMPOSER_HOME"

ENV PATH=${PATH}:/opt/drupal/vendor/bin

# vim:set ft=dockerfile:
