FROM php:8.0-fpm

ARG SSH_USER
ARG SSH_PASSWORD

RUN apt-get update -y && apt-get install git -y

COPY deploy/config/php.ini /usr/local/etc/php/

# Install Postgre PDO
RUN apt-get install -y libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql

RUN docker-php-ext-install mysqli pdo pdo_mysql

COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN apt-get install -y openssh-server
RUN apt-get install -y iputils-ping


RUN apt-get -y install supervisor


RUN mkdir /var/run/sshd
RUN echo "$SSH_USER:$SSH_PASSWORD" | chpasswd
RUN echo "==============================="
RUN sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
RUN mkdir /root/.ssh

RUN rm -rf /etc/ssh/ssh_host_rsa_key
RUN rm -rf /etc/ssh/ssh_host_dsa_key
RUN rm -rf /etc/ssh/ssh_host_ed25519_key
RUN rm -rf /etc/ssh/ssh_host_ecdsa_key

RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ''
RUN ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key -N ''
RUN ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key -N ''
RUN ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N ''

RUN apt-get install vim -y
RUN apt-get install nano -y

COPY supervisor.conf /etc/supervisor.conf

WORKDIR /var/www

USER root

EXPOSE 9000 22
CMD ["supervisord", "-c", "/etc/supervisor.conf"]
