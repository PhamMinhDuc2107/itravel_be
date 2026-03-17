FROM php:8.2-fpm

# Thêm libzip-dev vào đây
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    default-mysql-client \
    libzip-dev 

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Thêm zip vào cuối danh sách này
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd zip

RUN pecl install redis && docker-php-ext-enable redis

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www

RUN chown -R www-data:www-data /var/www