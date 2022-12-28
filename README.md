# Docker PHP-FPM 8.1 & Nginx 1.22 on Alpine Linux

PHP-FPM 8.1 & Nginx 1.22 container image for Docker, built on [Alpine Linux](https://www.alpinelinux.org/).

Repository: <https://github.com/TrafeX/docker-php-nginx>

- Built on the lightweight and secure Alpine Linux distribution
- Multi-platform, supporting AMD4, ARMv6, ARMv7, ARM64
- Very small Docker image size (+/-40MB)
- Uses PHP 8.1 for better performance, lower CPU usage & memory footprint
- Optimized for 100 concurrent users
- Optimized to only use resources when there's traffic (by using PHP-FPM's `on-demand` process manager)
- The services Nginx, PHP-FPM and supervisord run under a non-privileged user (nobody) to make it more secure
- The logs of all the services are redirected to the output of the Docker container (visible with `docker logs -f <container name>`)
- Follows the KISS principle (Keep It Simple, Stupid) to make it easy to understand and adjust the image to your needs

## Adding composer

If you need [Composer](https://getcomposer.org/) in your project, here's an easy way to add it.

```Dockerfile
FROM trafex/php-nginx:latest

# Install composer from the official image
COPY --from=composer /usr/bin/composer /usr/bin/composer

# Run composer install to install the dependencies
RUN composer install --optimize-autoloader --no-interaction --no-progress
```

### Building with composer

If you are building an image with source code in it and dependencies managed by composer then the definition can be improved.
The dependencies should be retrieved by the composer but the composer itself (`/usr/bin/composer`) is not necessary to be included in the image.

```Dockerfile
FROM composer AS composer

# copying the source directory and install the dependencies with composer
COPY <your_directory>/ /app

# run composer install to install the dependencies
RUN composer install \
  --optimize-autoloader \
  --no-interaction \
  --no-progress

# continue stage build with the desired image and copy the source including the
# dependencies downloaded by composer
FROM trafex/php-nginx
COPY --chown=nginx --from=composer /app /var/www/html
```
