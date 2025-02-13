# Guía Completa para Crear un Contenedor de Oracle Database con Docker

## 1️⃣ Crear una Cuenta en Oracle Cloud

Para descargar la imagen de Oracle Database, necesitas una cuenta en Oracle Container Registry.

1. Ve a [Oracle Container Registry](https://container-registry.oracle.com/).
2. Crea una cuenta gratuita o inicia sesión si ya tienes una.
3. Acepta los términos de licencia de Oracle Database en la sección "Database".
4. Verifica tu cuenta a través del correo electrónico recibido.

## 2️⃣ Iniciar Sesión en el Registro de Oracle

Antes de descargar la imagen de Oracle Database, necesitas iniciar sesión en Docker con tus credenciales de Oracle:

```sh
docker login container-registry.oracle.com
Introduce tu usuario y contraseña de Oracle cuando te lo solicite.
```

## 3️⃣ Descargar la Imagen de Oracle Database (No nesesario para este proceso)
Verifica las versiones disponibles en Oracle Container Registry. Luego, descarga la imagen correcta:

```sh
docker pull container-registry.oracle.com/database/free:latest
```
Alternativamente, si deseas la versión Enterprise:
```sh
docker pull container-registry.oracle.com/database/enterprise:latest
```
## 4️⃣ Crear un Archivo docker-compose.yml
Crea un archivo llamado docker-compose.yml en tu proyecto con el siguiente contenido:

```yaml
# Descarga y ejecuta un contenedor con Nginx.
# Mapea el puerto 8080 de la máquina al puerto 80 del contenedor.
# El contenedor de Nginx mostrará el archivo index.html desde tu directorio local.
# Permite actualizar el HTML sin reiniciar el contenedor gracias a los volúmenes.

    services:
    nginx:
      image: nginx:latest
      container_name: mi_nginx
      ports:
        - "8080:80"
      volumes:
        - ./index.html:/usr/share/nginx/html/index.html
      networks:
        - app_network  # Se une a la red personalizada

    oracle:
      image: container-registry.oracle.com/database/enterprise:latest
      container_name: oracle_db
      restart: always
      ports:
        - "1521:1521"  # Puerto de Oracle para conexiones SQL*Net
        - "5500:5500"  # Puerto de Oracle EM Express
      environment:
        - ORACLE_SID=ORCLCDB
        - ORACLE_PDB=ORCLPDB1 #Nombre del servicio
        - ORACLE_PWD=${ORACLE_PWD}  # Se usa un archivo .env para seguridad
        - ORACLE_CHARACTERSET=AL32UTF8
      volumes:
        - oracle_data:/opt/oracle/oradata
      networks:
        - app_network  # Se une a la red personalizada

    volumes:
    oracle_data:
    nginx_data:  # Agregamos un volumen para posibles archivos de Nginx

    networks:
    app_network:  # Red para comunicación entre servicios
      driver: bridge
```
## 5️⃣ Crear un Archivo .env para Variables de Entorno
Crea un archivo .env en la misma carpeta del docker-compose.yml,
dentro del archivo añadir la siguiente línea:

```sh
ORACLE_PWD=tu_contraseña_segura
```
## 6️⃣ Iniciar los Contenedores
Ejecuta el siguiente comando para levantar los servicios:
```sh
docker-compose up -d
```
Para verificar que los contenedores están corriendo:
```sh
docker ps
```
# Si deseas Acceder a la basee de datos directamente desde el contenedor:

```sh
docker exec -it oracle_db sqlplus sys/contraseña-del-archivo-env as sysdba
```

## 1️⃣ Verificar las bases de datos disponibles
Ejecuta este comando para ver los nombres de los Pluggable Databases (PDBs):
```sh
SHOW PDBS;
```
Verás algo como esto:

|   CON_ID   |   CON_NAME   |
|------------|--------------|
|      2     |   PDB$SEED   |
|      3     |   ORCLPDB1   |

El PDB que nos interesa aquí es ORCLPDB1.

## 2️⃣ Conectarse al Pluggable Database (PDB)
Ejecuta este comando en SQL*Plus:
```sh
ALTER SESSION SET CONTAINER = ORCLPDB1;
```
## 3️⃣ Crear un usuario para usar la base de datos
Ahora que estás dentro del PDB, puedes crear un usuario normalmente, ejecutar linea por linea los siguientes comandos:
```sh
CREATE USER user IDENTIFIED BY password;
GRANT CONNECT, RESOURCE TO user;
ALTER USER user QUOTA UNLIMITED ON USERS;
```
## 4️⃣ Conectarse con el nuevo usuario
Sal de SQL*Plus (contenedor) con EXIT; luego conéctate con:
```sh
docker exec -it oracle_db sqlplus usuario_creado/password@ORCLPDB1
```

# Crear una tabla en la base de datos dentro el contenedor
## 1️⃣Crear una tabla:
Para crear una tabla en Oracle, usa el siguiente comando SQL. Asegúrate de estar en el esquema correcto (usuario) donde quieres crear la tabla.

```sql
CREATE TABLE productos (
    id_producto NUMBER PRIMARY KEY,
    nombre VARCHAR2(100),
    precio NUMBER,
    cantidad NUMBER
);
```
## 2️⃣ Insertar datos en la tabla:
Una vez que la tabla ha sido creada, puedes insertar datos con el siguiente comando SQL:

```sql
INSERT INTO productos (id_producto, nombre, precio, cantidad)
VALUES (1, 'Producto A', 100, 50);

INSERT INTO productos (id_producto, nombre, precio, cantidad)
VALUES (2, 'Producto B', 150, 30);
```
## 3️⃣ Ver la base de datos y las tablas:
Ver las bases de datos disponibles (esquemas):

```sql
SELECT * FROM all_users;
```
Ver las tablas en tu esquema:

```sql
SELECT table_name FROM user_tables;
```

## 4️⃣Ver el contenido de una tabla:
Para ver el contenido de la tabla, puedes hacer una consulta SELECT como esta:

```sql
SELECT * FROM productos;
```
Esto te mostrará todos los registros en la tabla productos.

## 5️⃣Salir de SQL*Plus y del contenedor:
Para salir de SQL*Plus:

```sql
EXIT;
```
Para salir del contenedor Docker:

```bash
exit
```
Con estos pasos podrás crear tu tabla, llenarla con datos y hacer consultas, todo dentro de un contenedor Docker con Oracle Database.

# Acceder a Oracle Database desde un entorno grafico con (Oracle SQL Developer Extension for VSCode)

Para conectarte a la base de datos Oracle desde una herramienta externa como SQL Developer
crea una nueva conexión con los siguientes parámetros (revisar estos datos en docker-compose.yml):

```sh
Host: localhost
Puerto: 1521
Nombre del servicio: ORCLPDB1
Usuario: user_creado
```

# Iniciar, Detener y Eliminar los Contenedores
Iniciar los contenedores definidos en docker-compose.yml:
```sh
docker-compose up -d
```
Para detener todos los contenedores activos:
```sh
docker stop $(docker ps -q)
```
Si deseas eliminar los volúmenes de datos para empezar de nuevo:
```sh
docker-compose down -v
```

# 🚀 Conclusión

Con estos pasos, ahora tienes un contenedor de Oracle Database funcionando junto con Nginx. Puedes usar esta configuración para desarrollar aplicaciones que requieran una base de datos Oracle sin necesidad de instalar Oracle Database en tu sistema.
