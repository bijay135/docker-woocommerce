FROM php:8.4-cli

# Configure docker php extension installer
RUN curl -sSLf https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions \
    -o /usr/local/bin/install-php-extensions && chmod +x /usr/local/bin/install-php-extensions \
\
# Install required php extensions
 && install-php-extensions mysqli zip gd imagick bcmath intl exif \
\
# Install wp cli
 && curl -sSLf https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    -o /usr/local/bin/wp && chmod +x /usr/local/bin/wp