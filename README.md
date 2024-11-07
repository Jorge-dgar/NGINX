# NGINX
Aquí tienes el README.md estructurado y redactado como si describieras en primera persona los pasos realizados en la práctica:

---

# Práctica: Instalación y Configuración de Servidor Web Nginx

Este documento describe el proceso paso a paso que seguí para instalar, configurar y verificar el funcionamiento de un servidor web Nginx en Debian, así como para configurar FTPS y transferir archivos al servidor. La web configurada tiene el nombre `webNginx2` y la IP es `192.168.56.10`.

## Pasos Realizados

### 1. Instalación de Nginx

Lo primero que hice fue instalar Nginx en mi sistema Debian. Actualicé los repositorios e instalé el paquete de Nginx con los siguientes comandos:

```bash
sudo apt update
sudo apt install nginx
```

Después de la instalación, verifiqué que el servicio Nginx estuviera activo usando:

```bash
systemctl status nginx
```

### 2. Creación de la Estructura de Carpetas para el Sitio Web

Para organizar los archivos del sitio web, creé la estructura de carpetas necesaria en el directorio `/var/www/`. Esta estructura es importante para que Nginx pueda encontrar y servir el sitio web correctamente. La carpeta que creé es:

```bash
sudo mkdir -p /var/www/webNginx2/html
```

### 3. Subida de Archivos al Servidor Web

Luego, subí los archivos del sitio web de ejemplo desde un repositorio en GitHub. Para esto, cloné el repositorio directamente en la carpeta `webNginx2/html`:

```bash
git clone https://github.com/cloudacademy/static-website-example /var/www/webNginx2/html
```

Después, cambié el propietario de esta carpeta al usuario de Nginx (`www-data`) y asigné los permisos adecuados para evitar problemas de acceso:

```bash
sudo chown -R www-data:www-data /var/www/webNginx2/html
sudo chmod -R 755 /var/www/webNginx2
```

### 4. Configuración del Servidor Nginx

Para que Nginx pueda servir el sitio web `webNginx2`, creé un archivo de configuración específico en el directorio `/etc/nginx/sites-available/` y agregué las directivas necesarias:

```bash
sudo nano /etc/nginx/sites-available/webNginx2
```

Dentro de este archivo, añadí la configuración del servidor:

```nginx
server {
    listen 80;
    server_name webNginx2;
    root /var/www/webNginx2/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Para habilitar el sitio, creé un enlace simbólico en el directorio `sites-enabled` y reinicié el servicio de Nginx para aplicar los cambios:

```bash
sudo ln -s /etc/nginx/sites-available/webNginx2 /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

### 5. Comprobaciones

#### 5.1 Configuración del Archivo `/etc/hosts`

Como aún no dispongo de un servidor DNS, realicé una configuración manual en el archivo `/etc/hosts` de mi máquina anfitriona. De esta forma, asocié la IP `192.168.56.10` con el nombre del servidor `webNginx2`:

```plaintext
192.168.56.10 webNginx2
```

#### 5.2 Verificación de Logs

Para asegurarme de que las peticiones se están registrando correctamente, revisé los archivos de logs de Nginx. Los registros se encuentran en las siguientes ubicaciones:

- Accesos: `/var/log/nginx/access.log`
- Errores: `/var/log/nginx/error.log`

### 6. Configuración y Uso de FTPS

Además, configuré FTPS en Debian para transferir archivos de forma segura. Para ello, instalé `vsftpd`:

```bash
sudo apt-get update
sudo apt-get install vsftpd
```

Luego, configuré la carpeta FTP en el directorio del usuario y generé certificados SSL para una conexión segura:

```bash
mkdir /home/mi_usuario/ftp
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt
```

Finalmente, modifiqué el archivo de configuración de `vsftpd` para habilitar FTPS, agregando las rutas a los certificados y configuraciones adicionales para asegurar la conexión. Reinicié el servicio para aplicar estos cambios:

```bash
sudo systemctl restart vsftpd
```

### 7. Tarea Final

Para la tarea final, configuré un nuevo dominio en el servidor con el mismo proceso, usando FTPES para transferir los archivos y configurando permisos adecuados en `/var/www/`.
