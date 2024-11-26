# Imagen de la última versión de Ubuntu
FROM ubuntu:latest

# Información sobre la imagen
LABEL \
    authors="Jabier Zurro Aduriz" \
    version="1.0" \
    description="Apache2 + nano + HTTPS Tarea Evaluativa" \
    creationDate="25-11-2024"

# Instala Apache, OpenSSL y herramientas necesarias como nano
RUN apt-get update && apt-get install -y \
    apache2 \
    openssl \
    nano \
    && apt-get clean

# Habilita el módulo ssl para la configuración HTTPS
RUN a2enmod ssl

# Copia los certificados SSL al contenedor
COPY conf/certs/jzurro.key /etc/ssl/private/jzurro.key
COPY conf/certs/jzurro.pem /etc/ssl/certs/jzurro.pem

# Copia el archivo .htaccess para la autenticación básica
COPY conf/passwd /etc/apache2/passwd

# Crear directorios dentro del contenedor
RUN mkdir -p "/var/www/html/Default website" \
    && mkdir -p /var/www/html/jzurro-birt \
    && mkdir -p /var/www/html/jzurro-birt/privado

# Copiar y mover los archivos HTML y CSS para Default website
# Se crea un fichero temporal para evitar problemas con los espacios en blanco
# Después, se mueven los ficheros al directorio correspondiente
COPY html/index.html /tmp/index.html
COPY html/css_index/ /tmp/css_index/
RUN mv /tmp/index.html "/var/www/html/Default website/" && \
    mv /tmp/css_index/ "/var/www/html/Default website/css_index/"

# Copiar y mover los archivos HTML y CSS para User website (jzurro-birt)
COPY html/virtualhost.html /tmp/virtualhost.html
COPY html/css_virtualhost/ /tmp/css_virtualhost/
RUN mv /tmp/virtualhost.html /var/www/html/jzurro-birt/ && \
    mv /tmp/css_virtualhost/ /var/www/html/jzurro-birt/css_virtualhost/

# Copiar y mover los archivos HTML y CSS para User website (jzurro-birt/privado)
COPY html/privado.html /tmp/privado.html
COPY html/css_privado/ /tmp/css_privado/
RUN mv /tmp/privado.html /var/www/html/jzurro-birt/privado/ && \
    mv /tmp/css_privado/ /var/www/html/jzurro-birt/privado/css_privado/

# Copia la configuración del VirtualHost al contenedor. Habilita el fichero virtualhost 
# y deshabilita el fichero 000-default.conf
COPY conf/virtualhost.conf /etc/apache2/sites-available/virtualhost.conf
RUN a2ensite virtualhost.conf && a2dissite 000-default.conf

# Exponer los puertos para HTTP y HTTPS
EXPOSE 80 443

# Comando para iniciar Apache en primer plano
CMD ["apachectl", "-D", "FOREGROUND"]