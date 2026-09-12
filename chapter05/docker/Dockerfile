FROM composer:2 AS builder
WORKDIR /app

# composer.json と composer.lock をコピーして依存関係をインストール
COPY bookman/composer.json bookman/composer.lock ./
RUN composer install --no-dev --no-scripts --prefer-dist --no-autoloader

# --- Stage 2: Production Run (PHP-FPM 実行環境) ---
FROM php:8.3-fpm-alpine

# Laravel に必須の PHP 拡張機能 (PDO MySQL, Zip 等) をインストール
RUN apk add --no-cache \
        libzip-dev \
        icu-dev \
        oniguruma-dev \
    && docker-php-ext-install \
        pdo_mysql \
        mbstring \
        zip \
        bcmath

WORKDIR /var/www/html

# Stage 1 で構築した vendor とソースコードを丸ごとコピー
COPY --from=builder /app/vendor /var/www/html/vendor

# ★イメージ作成時点でパーミッションと所有権を完全に解放
RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 777 /var/www/html
