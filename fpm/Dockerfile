FROM php:8.5-fpm

# Configure docker php extension installer
RUN curl -sSLf https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions \
    -o /usr/local/bin/install-php-extensions && chmod +x /usr/local/bin/install-php-extensions \
\
# Install required php extensions
 && install-php-extensions mysqli zip gd imagick bcmath intl exif \
\
# Install required system packages
 && apt-get update && apt-get install --no-install-recommends -y unzip