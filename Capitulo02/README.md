# Capítulo 2. Administración Avanzada y Alta Disponibilidad

**[⬅️ Capítulo 1](../Capitulo01/README.md)** | **[Comenzar ➡️](Practica2-1/README.md)**

## Objetivo de la práctica:

Dominar la concurrencia, el mantenimiento y la estrategia de respaldo y recuperación de PostgreSQL 18 sobre Debian 13 en Docker, aplicándolos a escenarios empresariales de alta disponibilidad.

## Duración aproximada:

480 minutos de prácticas guiadas; el capítulo completo incluye teoría y actividades de análisis conforme al temario.

## Lista de laboratorios

| Práctica | Tema | Duración | Requisito |
| --- | --- | --- | --- |
| [2.1](Practica2-1/README.md) | Análisis de concurrencia y niveles de aislamiento | 70 min | Capítulo 1 terminado |
| [2.2](Practica2-2/README.md) | Monitoreo de Bloqueos y Mantenimiento Preventivo | 65 min | 2.1 |
| [2.3](Practica2-3/README.md) | Administración de Índices y Optimización de Acceso a Datos | 60 min | 2.2 |
| [2.4](Practica2-4/README.md) | Implementación de Respaldos con pg_basebackup | 70 min | 2.3 |
| [2.5](Practica2-5/README.md) | Configuración de pgBackRest | 60 min | 2.4 |
| [2.6](Practica2-6/README.md) | Administración de Respaldos con Barman | 55 min | 2.5 |
| [2.7](Practica2-7/README.md) | Simulación de recuperación ante desastres | 65 min | 2.4 |

## Tabla de ayuda:

| Elemento | Valor |
| --- | --- |
| Entorno base | El contenedor `debian-pg` del capítulo 1 (Debian 13 + PostgreSQL 18) |
| Servicio Compose | `debian-pg`, proyecto `postsql-priv-cap01`, carpeta `Capitulo01` |
| Base de datos / usuario administrador | `curso` / `postgres` |
| Acceso SQL desde el equipo | `docker compose exec --user postgres debian-pg psql -X -d curso` |
| Esquemas de laboratorio | `lab_concurrencia`, `lab_vacuum`, `lab_indices`, `lab_fisico`, `lab_dr`, `lab_contingencia` |
| Base auxiliar temporal | `lab_restore_24` no se usa; clúster de prueba en `/tmp/restore_test` (2.4), bases `lab_dr_restore` y `lab_contingencia_aux` |
| Duración total del capítulo | ≈ 8 horas; las prácticas 2.5 y 2.6 son de diagnóstico y diseño |
| Seguridad | Todos los cambios son en laboratorio; no ejecutar sobre producción |

Los requisitos globales del curso (16 GB de RAM, 32 GB recomendados y 100 GB de SSD) se heredan del capítulo 1. Este capítulo reutiliza el mismo contenedor: no es necesario reconstruir la imagen. Las prácticas 2.5 y 2.6 requieren infraestructura adicional (pgBackRest o Barman) para los respaldos físicos operativos; sus README separan el diagnóstico reproducible de los comandos condicionados.

## Instrucciones

### Tarea 1. Preparar el entorno del capítulo

Todos los comandos `docker compose` se ejecutan **en el equipo anfitrión, desde `Capitulo01`**, donde reside el `compose.yaml` del servicio `debian-pg`; los bloques SQL se pegan en `psql`.

```text
cd ..\Capitulo01      # Bash: cd ../Capitulo01
docker compose up -d --wait --wait-timeout 180
docker compose ps
docker compose exec --user postgres debian-pg psql -X -d curso -c "SELECT version();"
```

El estado debe ser `healthy` y la versión debe comenzar por `PostgreSQL 18`. Si se conservó el entorno del capítulo 1, basta `docker compose start --wait`.

### Tarea 2. Recorrer las prácticas en orden

Cada práctica es autocontenida: crea su propio esquema `lab_*`, define su resultado esperado y termina con una limpieza que elimina sus objetos. Seguir el orden de la tabla para aprovechar las dependencias (2.3 usa el criterio de planes de 2.2; 2.7 y 2.8 aplican los respaldos de 2.4). Abrir las terminales que cada práctica indique; las tareas de concurrencia (2.1) requieren dos o tres sesiones simultáneas.

Al completar cada práctica, registrar captura o transcripción de las consultas y responder el ejercicio de análisis antes de la limpieza.

### Tarea 3. Registrar evidencias

Guardar por práctica: esquema y datos creados, resultados de las tareas, explicación breve de qué cambió y por qué, respuestas al ejercicio de análisis y evidencia de la limpieza. En 2.5 y 2.6, documentar el inventario y el diseño de configuración aunque la fase operativa quede condicionada a la infraestructura disponible.

## Solución de Problemas

| Síntoma | Comprobación y solución |
| --- | --- |
| `docker compose ps` no muestra el servicio | Ejecutar los comandos desde `Capitulo01`, no desde `Capitulo02`; verificar `docker version` |
| El contenedor arranca pero psql falla | Esperar el healthcheck (`docker compose ps` en estado `healthy`) y reintentar |
| `pg_basebackup` falla o es muy lento | Verificar `SHOW wal_level` y `max_wal_senders`; comprobar espacio en `/tmp` con `df -h` |
| Puerto 5433 ocupado en la Tarea 5 de 2.4 | Verificar que el clúster principal no lo usa; usar 5434 o detener el auxiliar con `pg_ctl stop` |
| La sesión B no se bloquea en 2.1 | Verificar que A mantiene la transacción con `FOR UPDATE` sin hacer `COMMIT` |
| `pg_stat_user_tables` no muestra cambios | Las estadísticas son asíncronas; esperar unos segundos o ejecutar `VACUUM (ANALYZE)` |
| Base auxiliar ya existe en 2.4, 2.7 u 8 | Eliminarla con `dropdb` tras comprobar que solo pertenece al laboratorio |

## Recursos Adicionales

- [Concurrencia MVCC y control de transacciones](https://www.postgresql.org/docs/18/mvcc.html).
- [Mantenimiento rutinario y autovacuum](https://www.postgresql.org/docs/18/maintenance.html).
- [Respaldos lógicos y físicos](https://www.postgresql.org/docs/18/backup.html).
- [Recuperación continua y PITR](https://www.postgresql.org/docs/18/continuous-archiving.html).
- [pgBackRest User Guide](https://pgbackrest.org/user-guide.html).
- [Barman Documentation](https://docs.pgbarman.org/).

**[⬅️ Capítulo 1](../Capitulo01/README.md)** | **[Lista general 🗂️](../README.md)**
