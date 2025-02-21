mi_proyecto/
    │── docker-compose.yml  # Define los contenedores y la configuración
    │── .env                # Variables de entorno (ejemplo: ORACLE_PWD)
    │── nginx/              # Configuración y archivos de Nginx
    │   │── nginx.conf      # Configuración personalizada de Nginx
    │   │── index.html      # Página principal del sitio
    │── src/                # Código fuente del proyecto
    │   │── app/            # Archivos de la aplicación (PHP, Python, etc.)
    │   │── static/         # Archivos estáticos (CSS, JS, imágenes)
    │── db/                 # Scripts relacionados con la base de datos
    │   │── init.sql        # Script de inicialización para Oracle
    │── volumes/            # Directorio para volúmenes persistentes
    │── README.md           # Documentación del proyecto
