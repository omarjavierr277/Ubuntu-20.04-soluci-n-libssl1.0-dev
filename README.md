### Actualizando todo ubuntu

```bash
$ sudo apt update
$ apt upgrade -y
```

### Instalar Apache2

```bash
$ sudo apt install apache2 locales-all
```
#### Modificar en `/etc/apache2/apache2.conf`

> Reemplazar `None` por `All`.

```conf
<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>
```

#### Iniciamos el servicio `apache2`

```bash
$ sudo service apache2 start
```

#### Activar el modulo rewrite

```bash
$ sudo a2enmod rewrite
$ sudo service apache2 restart
```
### Instalar PHP 7.4 y extensiones necesarias

```bash
$ sudo apt install php libapache2-mod-php php-cli php-mbstring curl php-curl zip unzip php-zip php-xml php-gd php-pgsql
```

#### Cambios basicos en el archivo de configuración `/etc/php/7.4/apache2/php.ini`

```ini
max_execution_time = 180 ; default 30
max_input_time = 360 ; default 60
memory_limit = 512M ; default 128M
post_max_size = 100M ; Default 8M
upload_max_filesize = 100M ; Default 2M
date.timezone = UTC ; esta opción está desabilitdo, me gusta manejarlo en timezone +00
```
> Reiniciamos el servicio apache2.

> reemplazamos el archivo `index.html` por `index.php` que se encuentra en `/var/www/html`.

```php
<?php

echo "<pre>";
print_r(new \DateTime('now'));
echo "</pre>";

phpinfo();
```

### Instalar composer 2

```bash
$ cd ~
$ curl -sS https://getcomposer.org/installer -o composer-setup.php
$ HASH=`curl -sS https://composer.github.io/installer.sig`
$ echo $HASH
$ php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
$ sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
$ composer -v
```

### Instalar PostgreSQL 12

```bash
$ sudo apt install postgresql postgresql-contrib
```

> No inicie el servicio antes de cambiar el archivo de configuración.

#### Modificar en el archivo de configuración `/etc/postgresql/12/main/postgresql.conf`

```conf
> listen_addresses = '*'         # what IP address(es) to listen on;
> timezone = 'UTC'
```

#### Iniciar el servicio postgresql

```bash
$ sudo service postgresql start
```

#### Modificar contraseña del usuario `postgres`

```bash
$ sudo -i -u postgres
$ psql
$ \password
$ \q
```
> Cuando escriba `\password` le pedirá que agrege la nueva contraseña y la confirme.

> `\q` es para salir de la consola de postgresql, luego tiene que salir de postgresql usando `exit`.

### Instalar Redis

```bash
$ sudo apt install redis-server
```

#### Modificar el archivo de configuración `/etc/redis/redis.conf`

```conf
> supervised systemd
```

> El valor `supervised` por defecto es `no`

#### Iniciar el servicio redis

```bash
$ sudo service redis-server start
```



## El problema Ubuntu 20.04 con libreria libssl1.0-dev

##### Necesitaba instalar ruby ​​2.3.x en realidad, era 2.3.3, estoy tratando de hacerlo en Ubuntu 20.04, he seguido  [esta guía](https://laravel.com/docs/broadcasting) , pero se detiene con este error.


```
Last 10 log lines:
installing rdoc:              /home/user/.rbenv/versions/2.3.3/share/ri/2.3.0/system
installing capi-docs:         /home/user/.rbenv/versions/2.3.3/share/doc/ruby
The Ruby openssl extension was not compiled.
ERROR: Ruby install aborted due to missing extensions
Try running `apt-get install -y libssl-dev` to fetch missing dependencies.
```

### La solución

Edite este archivo `/etc/apt/sources.listy` agregue esta línea al final.

```config
deb http://security.ubuntu.com/ubuntu bionic-security main
```

Después de esa carrera:

```bash
$ sudo apt update && apt-cache policy libssl1.0-dev
```

Finalmente,

```bash
$ sudo apt-get install libssl1.0-dev
```

#### Link de Referencia
> [https://www.garron.me/en/linux/install-ruby-2-3-3-ubuntu.html](https://www.garron.me/en/linux/install-ruby-2-3-3-ubuntu.html)

