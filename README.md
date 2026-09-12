# Lab02 
### Sebastian Valverde Cedron - 000291195

## Introducción
Primero preparamos el entorno básico creando el archivo `.env` y las variables de entorno necesarias para la base de datos y para el mensaje de la API. También contamos con los archivos `Dockerfile` y `docker.compose.yaml` con la configuración mínima necesaria para empezar a probar el uso de Docker con un levantamiento de prueba.

## Instrucciones de ejecución

Por ser la primera vez, se debe construir la imagen de la API desde el `Dockerfile` (realiza `npm install` usando `package.json`), por lo que se levanta con:

```bash
docker compose -f docker.compose.yaml up --build
```

> **Importante:** la imagen de la API se construye localmente a partir del `Dockerfile`. Por eso `package.json` y `package-lock.json` forman parte del repositorio: sin ellos el build fallaría. `node_modules` está ignorado en `.gitignore` y se regenera con `npm install`.

Si la imagen ya está construida, se puede iniciar sin reconstruir:

```bash
docker compose -f docker.compose.yaml up 
```
Donde `-f` le indica a Docker Compose que utilice el archivo específico `docker.compose.yaml` para configurar los servicios. 

Salida del primer Docker Compose de prueba:

![Salida inicial Docker Compose](imagenes/01-docker-compose-up-inicial.png)

### Commit: 

```text
build(docker):agregar archivos base y variables de entorno como configuración inicial
```

Una vez verificado el correcto funcionamiento de Docker y la base de datos, se procede a crear la arquitectura de múltiples contenedores encargada de levantar la API 3 veces más el contenedor de la base de datos.

`docker.compose.yaml`:
```yaml
services:
  api:
    build: .
    ports:
      - "3000:3000"
  api2:
    build: .
    ports:
      - "3001:3000"
  api3:
    build: .
    ports:
      - "3002:3000"

  bd:
    image: 'postgres'
    environment:
      - 'POSTGRES_PASSWORD=${BD_PASSWD}'
```

Resultado de logs en Docker de las 3 llamadas:

![Logs de respuesta de las 3 APIs](imagenes/02-curl-3-apis.png)

### Commit: 

```text
feat(compose): escalar api a 3 replicas con bd
```

Para mayor ilustración se realizó un pequeño ajuste enviando un mensaje personalizado a cada API usando el archivo `.env` con las variables `MESSAGE1`, `MESSAGE2` y `MESSAGE3`.

![Llamado a APIs con mensaje personalizado](imagenes/03-curl-3-apis-personalizado.png)

### Commit: 

```text
fix(compose): asignar mensaje personalizado a cada api
```

---

## Redes y Volúmenes en Docker

### Tipos de Redes (Drivers de Red en Docker)
1. **Bridge (por defecto):** Es la red privada predeterminada que crea Docker en un host. Permite que los contenedores en el mismo host se comuniquen entre sí aislándolos de la red externa.
2. **Host:** Elimina el aislamiento de red entre el contenedor y el host. El contenedor comparte directamente las interfaces e IP del sistema anfitrión.
3. **Overlay:** Permite conectar múltiples demonios de Docker en distintos hosts físicos o virtuales, siendo fundamental para arquitecturas distribuidas (ej. Docker Swarm / Kubernetes).
4. **Macvlan / Ipvlan:** Asigna una dirección MAC/IP física a un contenedor, haciendo que parezca un dispositivo físico independiente conectado a la red local.
5. **None:** Desactiva por completo la pila de red en el contenedor, dejándolo totalmente aislado.

### Tipos de Volúmenes (Mecanismos de Almacenamiento en Docker)
1. **Volúmenes Administrados (Named Volumes):** Almacenados en una zona administrada directamente por Docker (`/var/lib/docker/volumes/`). Son independientes del ciclo de vida del contenedor, tienen un nombre explícito, son persistentes y representan la mejor opción para bases de datos.
2. **Volúmenes Anónimos (Anonymous Volumes):** Similares a los nombrados, pero creados sin especificar un nombre (ej. `- /var/lib/postgresql`). Docker les asigna un hash aleatorio largo. Aunque persisten los datos al reiniciar el contenedor, son difíciles de rastrear, reutilizar o administrar.
3. **Montajes Vinculados (Bind Mounts):** Mapean un archivo o directorio específico del sistema de archivos del host directamente al contenedor (ej. `./src/api:/app`). Son ideales para entornos de desarrollo en tiempo real.
4. **Montajes en Memoria (tmpfs Mounts):** Almacenan datos únicamente en la memoria RAM del host (no en disco), siendo temporales y extremadamente rápidos.


---

## Volúmenes en la BD

Se añadió la configuración del volumen dentro de `docker.compose.yaml` para definir el almacenamiento de PostgreSQL.

![Configuración del Volumen Postgres](imagenes/04-docker-compose-volumen-bd.png)

### Sustentación de la Elección del Volumen
Se eligió utilizar un **Named Volume (Volumen Administrado)** nombrado `postgres_data` mapeado a la ruta `/var/lib/postgresql` del contenedor por las siguientes razones:
- **Persistencia de Datos Independiente:** La información de la base de datos se conserva intacta aunque el contenedor sea eliminado (`docker compose down`).
- **Gestión Automatizada por Docker:** Al ser un volumen administrado por el motor de Docker, evita problemas de permisos de sistema operativo local y garantiza compatibilidad multiplataforma.
- **Rendimiento e Integridad:** Proporciona un rendimiento óptimo de I/O para motores SQL como PostgreSQL evitando cierres inesperados por bloqueo de archivos en el host.

---

### Verificación de Creación y Persistencia

Se vuelve a levantar el entorno con el nuevo build usando:

```bash
docker compose -f docker.compose.yaml up --build
```

Y se verifica la creación del volumen administrado:

```bash
docker volume ls
```
![Listado de volúmenes creados](imagenes/05-docker-volume-ls.png)

Para obtener una salida más detallada del volumen creado:

```bash
docker volume inspect sem2-iac-sebastianvalverde_postgres_data
```

![Detalle del volumen creado](imagenes/06-docker-volume-inspect.png)

Para realizar una prueba completa de persistencia, se creó una tabla dentro de PostgreSQL e insertó un usuario registrado como `"Sebastian Valverde"`:

```bash
# Crear tabla de prueba
docker compose -f docker.compose.yaml exec bd psql -U postgres -c "CREATE TABLE usuarios (id SERIAL PRIMARY KEY, nombre VARCHAR(50));"

# Insertar un registro de prueba
docker compose -f docker.compose.yaml exec bd psql -U postgres -c "INSERT INTO usuarios (nombre) VALUES ('Sebastian Valverde');"
```

![Verificación de tabla y usuario](imagenes/07-psql-verificacion-tabla-usuario.png)

Para comprobar la persistencia, eliminamos los contenedores existentes y los volvemos a levantar:

```bash
# Detener y eliminar los contenedores
docker compose -f docker.compose.yaml down

# Verificar la inexistencia de contenedores activos
docker compose -f docker.compose.yaml ps -a

# Volver a levantar la infraestructura
docker compose -f docker.compose.yaml up -d
```

![Estado de contenedores con Docker Compose](imagenes/08-docker-compose-ps.png)

Finalmente, ejecutamos la consulta a la base de datos comprobando que el registro de `"Sebastian Valverde"` persiste:

```bash
docker compose -f docker.compose.yaml exec bd psql -U postgres -c "SELECT * FROM usuarios;"
```

### Commit: 

```text
feat(compose): agregar volumen persistente a la bd
```
