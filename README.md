# Drupal for CRS Testing

This is a simple container compose that brings up Drupal and ModSecurity + Apache + CRS so you can start testing quickly.

You'll need:

- docker
- docker-compose (integrated in latest versions of docker desktop)

Usage:
```sh
$ docker-compose up
Docker Compose is now in the Docker CLI, try `docker compose up`

Starting drupal ... done
Creating modsecurity-drupal ... done
Attaching to drupal, modsecurity-drupal
drupal                | AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.26.0.2. Set the 'ServerName' directive globally to suppress this message
drupal                | AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.26.0.2. Set the 'ServerName' directive globally to suppress this message
drupal                | [Tue Jun 29 14:00:37.365858 2021] [mpm_prefork:notice] [pid 1] AH00163: Apache/2.4.38 (Debian) PHP/7.1.33 configured -- resuming normal operations
drupal                | [Tue Jun 29 14:00:37.365935 2021] [core:notice] [pid 1] AH00094: Command line: 'apache2 -D FOREGROUND'
```

You can edit and change version in the `docker-compose.yml` file, to match your setup.

For quick tests, this image isn't bundled with MySQL or others. We suggest you to just use `SQLite`. In the install menu, select `SQLite` from the menu, and choose the default `sites/default/files/.ht.sqlite` file.
