FROM alpine:3.5
MAINTAINER Dominic Taylor <dominic@yobasystems.co.uk>

ARG plugins=git

RUN apk add --no-cache openssh-client git libcap tar php5-fpm curl \
    php5-curl php5-gd php5-zip php5-iconv php5-sqlite3 php5-mysql php5-mysqli php5-pgsql php5-json php5-phar php5-openssl

RUN curl --silent --show-error --fail --location \
      --header "Accept: application/tar+gzip, application/x-gzip, application/octet-stream" \
      "https://getcomposer.org/installer" \
    | php -- --install-dir=/usr/bin --filename=composer

RUN echo "clear_env = no" >> /etc/php5/php-fpm.conf

RUN curl --silent --show-error --fail --location --header "Accept: application/tar+gzip, application/x-gzip, application/octet-stream" -o - \
      "https://caddyserver.com/download/build?os=linux&arch=amd64&features=${plugins}" \
    | tar --no-same-owner -C /usr/bin/ -xz caddy \
 && chmod 0755 /usr/bin/caddy \
 && addgroup -S caddy \
 && adduser -D -S -H -s /sbin/nologin -G caddy caddy \
 && setcap cap_net_bind_service=+ep /usr/bin/caddy \
 && /usr/bin/caddy -version

EXPOSE 80 443 2015
WORKDIR /srv

COPY files/Caddyfile /etc/Caddyfile
COPY files/index.php /srv/index.php

RUN chown -R caddy:caddy /srv /var/log

USER caddy

ENTRYPOINT ["/usr/bin/caddy"]
CMD ["--conf", "/etc/Caddyfile"]
