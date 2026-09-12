# Lab02 
### Sebastian Valverde Cedron - 000291195

## Introduccion
Primero preparamos el entorno básico creando el .env y las variables de entorno necesarias para la base de datos y para el mensaje de la api. También tenemos los archivos dockerfile y docker-compose.yaml. con la configuración mínima necesaria para empezar a testear el uso de docker con un levantamiento de prueba.

## instruccion

para inicializar el contenedor base podemos usar:
```bash
docker compose -f docker.compose.yaml up 
```
donde -f le dice a docker compose que use el archivo docker.compose.yaml para configurar los servicios. 

salida del primer docker compose de prueba:

```bash
~/Projects/University/Sem2-IAC-SebastianValverde master*
❯ docker compose -f docker.compose.yaml up
[+] up 3/3
 ✔ Network sem2-iac-sebastianvalverde_default Created                                                                              0.0s
 ✔ Container sem2-iac-sebastianvalverde-bd-1  Created                                                                              0.1s
 ✔ Container sem2-iac-sebastianvalverde-api-1 Created                                                                              0.1s
Attaching to api-1, bd-1
bd-1   | The files belonging to this database system will be owned by user "postgres".

api-1  | > sem2-iac-sebastianvalverde@1.0.0 start /opt
api-1  | > node src/api/server.js
bd-1   | This user must also own the server process.

api-1  | 
bd-1   | 
bd-1   | The database cluster will be initialized with locale "en_US.utf8".

bd-1   | The default database encoding has accordingly been set to "UTF8".
bd-1   | The default text search configuration will be set to "english".
bd-1   | 
bd-1   | Data page checksums are enabled.
bd-1   | 
bd-1   | fixing permissions on existing directory /var/lib/postgresql/18/docker ... ok
bd-1   | creating subdirectories ... ok
bd-1   | selecting dynamic shared memory implementation ... posix
bd-1   | selecting default "max_connections" ... 100
bd-1   | selecting default "shared_buffers" ... 128MB
bd-1   | selecting default time zone ... Etc/UTC
bd-1   | creating configuration files ... ok
api-1  | [2026-09-12T17:20:36.985] [INFO] default - Start server at port 3000
bd-1   | running bootstrap script ... ok
bd-1   | performing post-bootstrap initialization ... ok
bd-1   | syncing data to disk ... ok
bd-1   | 
bd-1   | 
bd-1   | Success. You can now start the database server using:
bd-1   | 
bd-1   |     pg_ctl -D /var/lib/postgresql/18/docker -l logfile start
bd-1   | 
bd-1   | initdb: warning: enabling "trust" authentication for local connections
bd-1   | initdb: hint: You can change this by editing pg_hba.conf or using the option -A, or --auth-local and --auth-host, the next time you run initdb.
bd-1   | waiting for server to start....2026-09-12 17:20:37.318 UTC [53] LOG:  starting PostgreSQL 18.6 (Debian 18.6-1.pgdg13+2) on x86_64-pc-linux-gnu, compiled by gcc (Debian 14.2.0-19) 14.2.0, 64-bit
bd-1   | 2026-09-12 17:20:37.319 UTC [53] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
bd-1   | 2026-09-12 17:20:37.322 UTC [59] LOG:  database system was shut down at 2026-09-12 17:20:37 UTC
bd-1   | 2026-09-12 17:20:37.323 UTC [53] LOG:  database system is ready to accept connections
bd-1   |  done
bd-1   | server started
bd-1   | 
bd-1   | /usr/local/bin/docker-entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*
bd-1   | 
bd-1   | 2026-09-12 17:20:37.429 UTC [53] LOG:  received fast shutdown request
bd-1   | waiting for server to shut down....2026-09-12 17:20:37.431 UTC [53] LOG:  aborting any active transactions
bd-1   | 2026-09-12 17:20:37.432 UTC [53] LOG:  background worker "logical replication launcher" (PID 62) exited with exit code 1
bd-1   | 2026-09-12 17:20:37.432 UTC [57] LOG:  shutting down
bd-1   | 2026-09-12 17:20:37.433 UTC [57] LOG:  checkpoint starting: shutdown immediate
bd-1   | 2026-09-12 17:20:37.438 UTC [57] LOG:  checkpoint complete: wrote 0 buffers (0.0%), wrote 3 SLRU buffers; 0 WAL file(s) added, 0 removed, 0 recycled; write=0.001 s, sync=0.001 s, total=0.006 s; sync files=2, longest=0.001 s, average=0.001 s; distance=0 kB, estimate=0 kB; lsn=0/1761918, redo lsn=0/1761918
bd-1   | 2026-09-12 17:20:37.446 UTC [53] LOG:  database system is shut down
bd-1   |  done
bd-1   | server stopped
bd-1   | 
bd-1   | PostgreSQL init process complete; ready for start up.
bd-1   | 
bd-1   | 2026-09-12 17:20:37.546 UTC [1] LOG:  starting PostgreSQL 18.6 (Debian 18.6-1.pgdg13+2) on x86_64-pc-linux-gnu, compiled by gcc (Debian 14.2.0-19) 14.2.0, 64-bit
bd-1   | 2026-09-12 17:20:37.546 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
bd-1   | 2026-09-12 17:20:37.546 UTC [1] LOG:  listening on IPv6 address "::", port 5432
bd-1   | 2026-09-12 17:20:37.547 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
bd-1   | 2026-09-12 17:20:37.549 UTC [73] LOG:  database system was shut down at 2026-09-12 17:20:37 UTC
bd-1   | 2026-09-12 17:20:37.551 UTC [1] LOG:  database system is ready to accept connections
```


