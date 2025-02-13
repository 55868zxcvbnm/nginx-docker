# Guía para Configurar Nginx con Docker en Windows

## 🔹 PASO 1: Abrir PowerShell o CMD

Abre PowerShell o Símbolo del sistema (CMD) y ve a la carpeta donde quieres crear el proyecto. Por ejemplo:

```sh
cd C:\Users\Desktop
```

Ahora, crea una nueva carpeta para tu proyecto y entra en ella:

```sh
mkdir nginx-docker
cd nginx-docker
```

## 🔹 PASO 2: Descargar la Imagen de Nginx (No nesesario para este proyecto)

Si quieres descargar manualmente la imagen de Nginx, usa:

```sh
docker pull nginx:latest
```

Esto bajará la última versión disponible de Nginx desde Docker Hub.

## 🔹 PASO 3: Crear el Archivo `docker-compose.yml`

Este archivo servirá para levantar Nginx con Docker.

### ➤ Opción 1: Usando PowerShell

Ejecuta este comando para crear el archivo:

```sh
New-Item -Path . -Name "docker-compose.yml" -ItemType "file"
```

Ahora, edítalo con Notepad:

```sh
notepad docker-compose.yml
```

### ➤ Opción 2: Crearlo manualmente

Si prefieres, abre Bloc de notas y copia este código:

```yaml
version: '3.8'
services:
  nginx:
    image: nginx:latest  # Usamos la imagen oficial de Nginx
    container_name: mi_nginx  # Nombre del contenedor
    ports:
      - "8080:80"  # Mapea el puerto 8080 del host al 80 del contenedor
    volumes:
      - ./index.html:/usr/share/nginx/html/index.html  # Mapea el archivo HTML
```

Guarda el archivo con el nombre `docker-compose.yml` en la carpeta del proyecto.

## 🔹 PASO 4: Levantar el Contenedor de Nginx

Para iniciar el servidor, ejecuta:

```sh
docker-compose up -d
```

### ¿Qué hace este comando?

✅ Descarga la imagen (si no la tienes).
✅ Crea y ejecuta el contenedor en segundo plano (`-d` = detached mode).
✅ Monta tu página `index.html` dentro de Nginx.

## 🔹 PASO 5: Crear el Archivo `index.html`

Este archivo será la página web que servirá Nginx.

### ➤ Opción 1: Usando PowerShell

Ejecuta el siguiente comando para crear el archivo:

```sh
New-Item -Path . -Name "index.html" -ItemType "file"
```

Ahora, ábrelo en Notepad para editarlo:

```sh
notepad index.html
```

Luego, copia y pega este código dentro del archivo (este es un ejemplo):

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mi Página en Nginx</title>
</head>
<body>
    <h1>¡Hola, Docker y Nginx en Windows!</h1>
</body>
</html>
```
Guarda los cambios y cierra el archivo.

## 🔹 PASO 6: Probar la Página en el Navegador

Abre tu navegador y ve a:

👉 [http://localhost:8080](http://localhost:8080)

Si todo está bien, verás **"¡Hola, Docker y Nginx en Windows!"** en la pantalla. 🎉

## 🔹 PASO 7: Verificar que el Contenedor Está Corriendo

Para ver si el contenedor está en ejecución, usa:

```sh
docker ps
```

Deberías ver algo como esto:

```sh
CONTAINER ID   IMAGE        COMMAND                  PORTS                   NAMES
xxxxxxxxxxxx   nginx:latest "nginx -g 'daemon of…"   0.0.0.0:8080->80/tcp    mi_nginx
```

## 🔹 PASO 8: Detener y Eliminar el Contenedor

Si quieres detener el servidor, usa:

```sh
docker stop <ID_o_NOMBRE> Detiene un contenedor de forma segura
docker stop $(docker ps -q) Detiene todos los contenedores en ejecución
docker-compose down elimina el contenedor pero no la imagen
```

Esto eliminará el contenedor, pero no borrará la imagen de Nginx.

Si quieres borrar la imagen también, ejecuta:

```sh
docker rmi nginx:latest
```

## 🔹 EXTRA: Editar el Archivo HTML y Aplicar Cambios

Si editas `index.html`, los cambios se aplican automáticamente.

Si no ves los cambios, puedes reiniciar el contenedor con:

```sh
docker-compose restart
```