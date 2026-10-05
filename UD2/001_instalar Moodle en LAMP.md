# Guía de Instalación de Moodle 5.1 en Debian 13 (con LAMP previo)

## 1. Requisitos previos

Asegúrate de tener instalado Apache, MariaDB/MySQL, PHP 8.2+ y las extensiones necesarias.

:warning: Si estamos trabajando con una máquina virtual, ahora es el momento de realizar una instantanea o punto de restauración :warning:

### Extensiones PHP

```bash
sudo apt update
sudo apt install php-curl php-gd php-intl php-mbstring php-xml php-zip php-soap php-xmlrpc php-bcmath php-ldap
sudo systemctl restart apache2
```

## 2. Crear base de datos

```bash
sudo mysql -u root -p
```

```sql
CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'moodleuser'@'localhost' IDENTIFIED BY 'ClaveSegura';
GRANT ALL PRIVILEGES ON moodle.* TO 'moodleuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

## 3. Descargar Moodle 5.1

```bash
cd /var/
sudo wget https://download.moodle.org/download.php/stable501/moodle-5.1.tgz
sudo tar -xvzf moodle-5.1.tgz
sudo rm -r moodle-5.1.tgz
```

## 4. Crear moodledata

```bash
cd /var/
sudo mkdir moodledata
sudo chown -R www-data:www-data moodledata
sudo chmod -R 755 moodledata
```

## 5. Permisos

```bash
sudo chown -R www-data:www-data /var/www/moodle
sudo chmod -R 755 /var/www/moodle
```

## 6. Configuración de Apache

Crear archivo de configuración para moodle:

```bash
sudo nano /etc/apache2/sites-available/moodle.conf
```

Contenido:

```text
<VirtualHost *:80>
    ServerName localhost # 127.0.0.1
    DocumentRoot /var/www/moodle/public
    <Directory /var/www/moodle/public>
        AllowOverride All
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
```

En caso de tener otro VirtualHost con Wordpress, añadir esta configuración en el fichero 000-default.conf

```text
# Moodle
Alias /moodle /var/www/moodle
<Directory /var/www/moodle>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>

# WordPress en /var/www/html
<Directory /var/www/html>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>


```

Activar:

```bash
sudo a2ensite moodle.conf
sudo a2enmod rewrite #no es necesario ejecutarlo cada vez que se crea un sitio nuevo
sudo systemctl reload apache2
```

Desactivar un sitio:

```bash
sudo a2dissite moodle.conf
sudo systemctl reload apache2
```

Ver log de errores de Apache en tiempo real

```bash
sudo tail -f /var/log/apache2/error.log
```

## 7. Ajustes PHP

```bash
sudo nano /etc/php/8.4/apache2/php.ini
```

Cambiar:

```text
max_input_vars = 5000
max_execution_time = 300
memory_limit = 512M
post_max_size = 256M
upload_max_filesize = 256M
```

## 8. Instalar vía navegador

Accede a:

```text
http://localhost
```

Sigue los pasos de configuración.

## 9. Instalación completada
