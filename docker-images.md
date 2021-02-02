[Volver al indice](README.md)

# Docker Images

## Imagenes oficiales
Docker hub permite subir imagenes y descargar imagenes, el tag es una etiqueta que se refiere a una version exacta de un mongo, los tags nos permite versionar la aplicacion

https://hub.docker.com/_/mongo

```bash
# descargar imagen de mongo en su ultima version
docker pull mongo

# descargar imagen de mongo version especifica
docker pull mongo:3.6.5-jessie

# listar todas las imagenes descargadas
docker images
```

-----

## Creacion de una imagen

apache + php muy antigua
```bash
mkdir docker-images
cd docker-images
touch Dockerfile
```

Dockerfile
```
FROM centos
RUN yum install httpd -y
CMD apachectl -DFOREGROUND
```

comando de construccion de imagenes
```bash
# creacion de una imagen sin tag
docker build --tag apache-php-old .

# creacion de una imagen con tag
docker build --tag apache-php-old:primera .

# borrado de la imagen
docker rmi apache-php-old:primera
```

verificar las capas de creacion
```
> docker history -H apache-php-old:primera
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
be3f7179117b        2 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "apac…   0B
69c9f8c41ba7        3 minutes ago       /bin/sh -c yum install httpd -y                 40.5MB
300e315adb2f        8 weeks ago         /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           8 weeks ago         /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>           8 weeks ago         /bin/sh -c #(nop) ADD file:bd7a2aed6ede423b7…   209MB
```

creacion de un contenedor
```bash
# verificar los contenedores
docker ps -a

# creacion del contenedor apartir de nuestra imagen
docker run -d --name ApacheCentos -p 666:80 apache-php-old:primera
```

borrado de un contenedor

```bash
docker container stop <id>
docker container rm <id>
```

-----

## Archvo Dockerfile

- FROM : imagen inicial
- RUN : Comandos de ejecucion en el sistema
- COPY/ADD : copiar y agregar archivos de nuestro sistema a la imagen de docker
- ENV : definicion de variables de entorno
- WORKDIR : directorios de trabajo
- EXPOSE : exponer router
- LABEL : nombre del container
- USERS : definicion de usuarios
- VOLUME : definicion de volumenes
- CMD : ejecucion de procesos
- otros

## FROM / RUN / COPY / ADD

Descargar un template de boostrap y guardarlo dentro de la misma carpeta del archivo Dockerfile

Dockerfile
```
FROM centos
RUN yum install httpd -y

COPY Theme-DarkAdmin /var/www/html

CMD apachectl -DFOREGROUND
```

Compilar imagen
```
docker build --tag themedark:1.0.0 .
docker images
```

Desplegar container
```
docker run -d --name web-dark -p 666:80 themedark:1.0.0
```

ADD y COPY realizan las ACCIONES, solo que ADD permite descargar el archivo definido por medio de http

Dockerfile
```
FROM centos
RUN yum install httpd -y

ADD http://ip/file /var/www/html

CMD apachectl -DFOREGROUND
```

## ENV / WORKDIR / EXPOSE

Agregar variables de entorno

```
FROM centos
RUN yum install httpd -y

COPY Theme-DarkAdmin /var/www/html

ENV contenido prueba

RUN echo "$contenido" > /var/www/html/prueba.html

CMD apachectl -DFOREGROUND
```

Definir un directorio de trabajo

```
FROM centos
RUN yum install httpd -y

WORKDIR /var/www/html

COPY Theme-DarkAdmin .

ENV contenido prueba

RUN echo "$contenido" > prueba.html

CMD apachectl -DFOREGROUND
```

Exponer puertos

```
FROM centos
RUN yum install httpd -y

WORKDIR /var/www/html

COPY Theme-DarkAdmin .

ENV contenido prueba

RUN echo "$contenido" > prueba.html

EXPOSE 8080 

CMD apachectl -DFOREGROUND
```

## LABEL / USER / VOLUME