# Capítulo 1. Arquitectura y Configuración del Servidor

**[Lista general 🗂️](../README.md)** | **[Comenzar ➡️](Practica1-1/README.md)**

## Objetivo de la práctica:

Instalar PostgreSQL 18 sobre Debian 13 en Docker y relacionar su arquitectura, memoria y almacenamiento con el diseño y procesamiento de datos empresariales.

## Duración aproximada:

210 minutos de prácticas guiadas; el capítulo completo incluye teoría y actividades de análisis conforme al temario de seis horas.

## Lista de laboratorios

| Práctica | Tema | Duración | Requisito |
| --- | --- | --- | --- |
| [1.1](Practica1-1/README.md) | Instalación y configuración inicial | 45 min | Docker funcionando |
| [1.2](Practica1-2/README.md) | Arquitectura y procesos | 35 min | 1.1 |
| [1.3](Practica1-3/README.md) | Configuración y memoria | 35 min | 1.1 |
| [1.4](Practica1-4/README.md) | PGDATA, WAL, FSM y VM | 30 min | 1.1 |
| [1.5](Practica1-5/README.md) | Modelo empresarial y tablespaces | 45 min | 1.1 |
| [1.6](Practica1-6/README.md) | Volumen de datos y particionamiento introductorio | 30 min | 1.5 |

## Tabla de ayuda:

| Elemento | Valor |
| --- | --- |
| Imagen base | `debian:13` (Trixie) |
| Motor | PostgreSQL 18.x instalado desde PGDG |
| Servicio Compose | `debian-pg` |
| Base de datos / usuario administrador | `curso` / `postgres` |
| Host / puerto desde el equipo | `127.0.0.1` / `5433` |
| Puerto dentro de Debian | `5432` |
| PGDATA y configuración | `/var/lib/postgresql/18/main` |
| Ejemplos SQL | Incluidos en el README de cada práctica |
| Memoria máxima del contenedor | 2 GB; Docker debe disponer de al menos 4 GB |
| Disco sugerido para este capítulo | 5 GB libres; prever más para retos de cinco millones de filas |

Los requisitos globales del Excel (16 GB de RAM, 32 GB recomendados y 100 GB de SSD) contemplan todos los capítulos y múltiples contenedores. Este capítulo ejecuta un solo contenedor. La versión mayor se fija en 18; la revisión menor de Debian y PostgreSQL depende de las actualizaciones disponibles al construir. Registrar `SELECT version()` y `docker image inspect postsql-priv-debian13-pg18:cap01` en las evidencias.

## Instrucciones

### Tarea 1. Distinguir las terminales

Todos los comandos `docker compose` se ejecutan **en el equipo anfitrión, desde `Capitulo01`**; los bloques de una sola línea funcionan en PowerShell y Bash.

| Entorno | Cómo acceder | Cómo salir |
| --- | --- | --- |
| Debian, como root para inspección del sistema | `docker compose exec debian-pg bash` | `exit` |
| Debian, como usuario de sistema postgres | `docker compose exec --user postgres debian-pg bash` | `exit` |
| SQL, mediante socket local y autenticación peer | `docker compose exec --user postgres debian-pg psql -X -d curso` | `\q` |

No pegar SQL en PowerShell ni comandos Linux en `psql`. Para conexiones locales, el usuario del sistema debe ser `postgres`; no se necesita contraseña. Para TCP, usar la contraseña de `.env`.

### Tarea 2. Iniciar y conservar el entorno

Seguir la [práctica 1.1](Practica1-1/README.md) para crear localmente `Dockerfile`, `entrypoint.sh`, `compose.yaml` y `.env` con los contenidos incluidos en su README. Después, iniciar el entorno desde `Capitulo01`. Copiar y ejecutar los bloques SQL de cada práctica en psql cuando se indique.

```text
docker compose up -d --build --wait --wait-timeout 180
docker compose ps
docker compose logs --tail 30 debian-pg
```

Detener al finalizar una sesión y reanudar después:

```text
docker compose stop
docker compose start --wait
```

`docker compose down` elimina el contenedor y la red del proyecto, conservando sus tres volúmenes. Al recrear, usar `docker compose up -d --wait`. Cambiar la contraseña en `.env` después de inicializar **no cambia** la contraseña almacenada; usar `\password postgres` dentro de `psql` si se necesita actualizarla.

### Tarea 3. Registrar evidencias

Guardar una captura o transcripción por tarea y responder las preguntas de análisis. Anotar filas, tamaños, parámetros y planes obtenidos. Los tiempos y los PID varían; no se evalúan contra valores fijos.

## Solución de Problemas

| Síntoma | Comprobación y solución |
| --- | --- |
| No se puede conectar al motor Docker | Iniciar Docker Desktop o Docker Engine y seleccionar contenedores Linux; verificar `docker info` |
| Puerto 5433 ocupado | Cambiar `PG_PORT` en `.env`, por ejemplo a `55433`, y ejecutar `docker compose up -d` |
| Falla de descarga APT | Verificar Internet, DNS y el proxy corporativo; revisar la salida de `docker compose build --progress plain` |
| `Peer authentication failed` o rol root inexistente | Usar `--user postgres` en el comando de acceso a `psql` |
| `relation already exists` | Volver a ejecutar únicamente el bloque de preparación de esa práctica; leer su alcance de limpieza |
| Contenedor no saludable | Revisar `docker compose logs --tail 100 debian-pg`; no borrar volúmenes para ocultar el error |

## Limpieza

Conservar el entorno para el siguiente capítulo. **Solo para reiniciar completamente el capítulo**, después de respaldar lo necesario, ejecutar desde `Capitulo01`:

```text
docker compose down --volumes
```

Este comando borra las bases de datos y ambos tablespaces de **este proyecto Compose**. Los tres volúmenes deben eliminarse juntos: los datos contienen referencias a los tablespaces.

## Recursos Adicionales

- Temario: `PostgreSQL Avanzado (Priv) 76 hrs VF1781714156.xlsx`, capítulo 1 y hoja de laboratorios.
- [Instalación oficial de PostgreSQL sobre Debian](https://www.postgresql.org/download/linux/debian/).
- [Servicios, volúmenes y healthcheck en Compose](https://docs.docker.com/reference/compose-file/services/).
- `POSTSQL_ADV/Capítulo1/README.md`: instalación Linux y arquitectura, consultada en la copia local; repositorio público: [POSTSQL_ADV](https://github.com/Netec-Mx/POSTSQL_ADV).
- Referencias locales consultadas: `POSTSQL_ESS/Modulo01` (instalación), `POSTSQL_ESS/Modulo02` (modelado), `POSTSQL_INT/Capitulo02` (tipos de datos) y `PBI-BAS/Capítulo1` (estructura didáctica). Los nombres locales son `POSTSQL_INT` y `PBI-BAS`, correspondientes a las referencias mencionadas como POSTSQL_INIT y PBI-BASS.

## Estado de validación

El entorno se construyó y los ejercicios se comprobaron sobre Debian 13 con PostgreSQL 18 en Docker. Ver [VALIDACION.md](VALIDACION.md) para los resultados, las condiciones de prueba y el procedimiento de aceptación del instructor.
