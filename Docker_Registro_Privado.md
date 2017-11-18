# COMO CREAR UN REGISTRO PRIVADO

Un registro es un servidor donde se almacenan imágenes de docker. Un registro privado puede montarse en una red local para ahorrar ancho de banda en las descargas desde Internet.




## En el servidor

## Instalar docker en Ubuntu
```bash
sudo  apt  install  docker.io
```

## Añadir usuario al grupo docker
```bash 
sudo  adduser  `id -un`  docker
```


__Pasos para crear un registro local e incorporar imágenes__


### Creamos directorio para el registro
```bash
mkdir  ~/registro
```

### Descargamos contenedor de registro (registry:2)

```bash
docker run -d -p 5000:5000 \
              -v /home/`id -un`/registro/:/var/lib/registry \
              --restart always \ 
              --name registry \
              registry:2
```

### Incorporamos la imagen hello-world a nuestro registro con el nombre hola

```bash
docker  pull  hello-world
docker  tag   hello-world   localhost:5000/hola
docker  push  localhost:5000/hola
```

### (Opcional) Borramos caché

```bash
docker  rmi  hello-world
docker  rmi  localhost:5000/hola  # don't worry, no se borrará la imagen que posee el registro registry
```

### (Opcional) Si necesitamos parar el registro
```bash
docker  stop  registry
docker  rm  -v  registry
``` 


## En el cliente

## Instalar docker en Ubuntu
```bash
sudo  apt  install  docker.io
```

## Añadir usuario al grupo docker
```bash
sudo  adduser  `id -un`  docker
```


__Pasos para descargar imágenes__

En los equipos cliente debemos configurar el demonio para que permita la conexión a sitios "inseguros" (sin HTTPS).

### Permitimos registros inseguros

Editar el archivo de configuración del demonio:
```bash
nano /etc/docker/daemon.json
```

Añadir la siguiente línea:
```bash
{ "insecure-registries":["172.20.7.0:5000"] }
```

> Colocar en lugar de `172.20.7.0` la dirección IP del servidor de registro.

### Reiniciamos daemon

```bash
sudo  systemctl  restart  docker
```

### Usamos la imagen hola del registro privado

```bash
docker  run  172.20.7.0:5000/hola
```
> Colocar en lugar de `172.20.7.0` la dirección IP del servidor de registro.
