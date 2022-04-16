FROM php:8.1-alpine

RUN docker-php-ext-install mysqli pdo pdo_mysql

RUN curl https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer 

# See https://github.com/symfony/demo
ADD https://github.com/symfony-cli/symfony-cli/releases/download/v5.4.7/symfony-cli_5.4.7_x86_64.apk /symfony-cli.apk
RUN apk add --allow-untrusted --no-cache /symfony-cli.apk
RUN composer create-project symfony/symfony-demo /app/demo

WORKDIR /app/demo
CMD ["symfony", "serve", "--no-tls"]
EXPOSE 8000
