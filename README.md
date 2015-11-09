# Apache / PHP docker image for development environment

A Docker Apache / mod_php image with some useful packages for development environments.
Do not use it for production.


## Configuration

Apache2, php5, Xdebug, composer, phpunit, phing

Apache serve a phpinfo() page. To test it and obtain information about the configuration:

    docker run -d -p 80:80 -p 443:443 alexisno/apache-php-dev

* `docker run ... alexisno/apache-php-dev` Run Apache in a new container
* `-d` Detached mode: run container in the background
* `-p 80:80 -p 443:443` Publish the container's ports 80 and 443 on the host so you can connect to the server

Open your brower at http://localhost/ and https://localhost/

Composer, phpunit and phing are available globally.

PHP is configured to send mail via a [`alexisno/mailcatcher-dev`](https://github.com/AlexisNo/docker-mailcatcher-dev) container.
Launch a [`alexisno/mailcatcher-dev`](https://github.com/AlexisNo/docker-mailcatcher-dev) container and use `--link mailcatcher:my-mailcatcher-container`.

Xdebug is configurated to accept any connection. Just send the appropriate request parameters.


## Common usage

This image should be used as a basic image for any project.

* Create an apache virtualhost for the development environment.
* Create a Dockerfile with your project dependencies and add the virtualhost to it's configuration.
* Create you own image with `docker build` or `docker-compose build`.

Run your new image with a command similar to this:

    docker run -d -p 80:80 -p 443:443 -v /path/to/your/project/sources:/var/www/project-name -v /path/to/project/data/apache/logs:/var/log/apache2 your-image-tag


## Self signed certificates

The image comes with a `gencert` command to generate self signed certificates.

Usage in child Dockerfile:

    RUN gencert <domain>

VirtualHost configuration:

  <VirtualHost *:443>
      SSLEngine on
      SSLCertificateFile /etc/ssl/certs/<domain>.crt
      SSLCertificateKeyFile /etc/ssl/private/<domain>.key
      # SSL Protocol Adjustments:
      BrowserMatch "MSIE [2-6]" \
                    nokeepalive ssl-unclean-shutdown \
                    downgrade-1.0 force-response-1.0
      # MSIE 7 and newer should be able to use keepalive
      BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

      # Complete with your configuration
      ...
  </VirtualHost>

Replace `<domain>` with the hostname you use for the development environment.
