# --------------------------------------
# Stage 1: frontend build stage
# --------------------------------------
FROM public.ecr.aws/docker/library/node:18-alpine AS frontend-builder

WORKDIR /laravel

COPY ./pingcrm /laravel

RUN npm install && npm run build
RUN mkdir /public && cp -a public/* /public

# --------------------------------------
# Stage 2: PHP-FPM
# --------------------------------------
FROM public.ecr.aws/docker/library/php:8.4-fpm AS php-fpm

WORKDIR /srv

COPY ./pingcrm /srv
COPY --from=frontend-builder /public /srv/public

RUN cp .env.example .env
RUN chown -R www-data:www-data storage database

RUN apt-get update && apt-get install -y \
    git \
    unzip \
    zip \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libz-dev

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd exif

COPY --from=composer /usr/bin/composer /usr/bin/composer
RUN composer install

#RUN touch database/database.sqlite
#RUN php artisan migrate:fresh --seed

EXPOSE 9000

# --------------------------------------
# Stage 3: Nginx
# --------------------------------------
FROM public.ecr.aws/nginx/nginx:latest AS nginx

WORKDIR /usr/share/nginx/html

COPY ./srv/default.conf /etc/nginx/conf.d/default.conf
COPY --from=php-fpm /srv/public/ /usr/share/nginx/html

EXPOSE 80
