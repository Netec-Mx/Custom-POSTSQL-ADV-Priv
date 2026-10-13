# Validación del capítulo 1

## Entorno comprobado

Validación realizada el **13 de octubre de 2026** con Docker Engine 28.3.3 y Compose v2.39.2. Se construyó el Dockerfile desde `debian:13` y se ejecutó PostgreSQL **18.6** sobre Debian GNU/Linux **13**, arquitectura amd64.

Se utilizó el proyecto independiente `postsql-priv-cap01-validacion` con puerto local 55433 y tres volúmenes exclusivos de prueba. El proyecto de uso del participante conserva el puerto 5433 por defecto. Las comprobaciones SQL también se ejecutaron en una instancia temporal independiente de PostgreSQL 18.4 sobre Windows.

## Comprobaciones realizadas

| Comprobación | Resultado |
| --- | --- |
| Sintaxis de Compose y Bash | Configuración válida y `bash -n` sin errores |
| Construcción desde Debian e instalación PGDG | Imagen construida; paquetes PostgreSQL 18 instalados |
| Inicialización con checksums y estado saludable | PGDATA inicializado; base `curso`; contenedor healthy |
| Ejemplos SQL integrados | Los siete ejemplos previamente ejecutados se incorporaron completos en los README de sus prácticas; contenido comprobado antes de retirar los archivos SQL |
| Acceso local y TCP | Peer local operativo; contraseña TCP correcta aceptada e incorrecta rechazada |
| Procesos y estadísticas | Consultas sobre actividad, WAL, bgwriter y checkpointer válidas en PostgreSQL 18 |
| Sesiones concurrentes | Sesión A observada active con espera PgSleep desde una sesión B independiente |
| Persistencia | Contenedor eliminado y recreado sin borrar volúmenes; fila de evidencia y modelo empresarial conservados |
| Ordenación de 200 000 filas | 64 kB: external merge, 15 536 kB en disco; 64 MB: quicksort, 23 332 kB en memoria |
| Configuración por recarga | work_mem 8 MB aplicado y restaurado a 4 MB |
| Configuración por reinicio | shared_buffers con pending_restart; 256 MB tras reiniciar; restaurado a 128 MB |
| WAL, FSM y VM | Diferencia WAL positiva; cambios de visibilidad y espacio reutilizable observables |
| Eliminación y mantenimiento | Conteo final de `lab_storage.paginas`: 15 000 filas |
| Modelo empresarial | Totales 800.00 y 150.00; precio histórico conservado al cambiar catálogo |
| Integridad | Duplicado, venta huérfana y cantidad cero rechazados |
| Carga de volumen | 1 000 000 filas por tabla y 10 000 fallidas |
| Equivalencia | EXCEPT ALL en ambas direcciones: conjuntos idénticos |
| Particiones | Q1: 246 600; Q2: 249 340; Q3: 252 072; Q4: 251 988 |
| Planes e índices | Consultas con poda activa/desactivada, B-tree, parcial y reto BRIN ejecutadas |
| Casos negativos | Volumen fuera del rango rechazado; eliminación de una fila detectada por validación |
| README temáticos | Bloques SQL ejecutados con adaptación de salidas/conexiones para las pruebas |
| Navegación | Seis README de prácticas, enlaces locales válidos y bloques de código cerrados |
| Configuración incluida en README | Dockerfile, entrypoint, Compose y valores de `.env` documentados para crear los archivos localmente |
| Imágenes de apoyo | Tres PNG incorporados a las prácticas 1.1, 1.2 y 1.4; textos y rutas revisados |

Los tamaños de sort son observaciones de esta ejecución y no umbrales de aprobación. Las comparaciones de planes no son un benchmark de producción. El reto de cinco millones de filas está preparado como extensión y no forma parte de la validación de volumen realizada.

## Procedimiento de aceptación para el instructor

1. Seguir [1.1](Practica1-1/README.md) desde una copia del repositorio, crear `Dockerfile`, `entrypoint.sh`, `compose.yaml` y `.env` con los bloques del README, y ejecutar `docker compose up -d --build --wait --wait-timeout 180` desde `Capitulo01`.
2. Confirmar Debian 13, PostgreSQL 18 y el estado saludable; probar socket local y TCP con la contraseña definida.
3. Completar [1.2](Practica1-2/README.md) con dos sesiones simultáneas, relacionando PID y estado de espera.
4. Completar [1.3](Practica1-3/README.md), registrar ambos sorts y terminar con work_mem 4 MB y shared_buffers 128 MB.
5. Completar [1.4](Practica1-4/README.md), registrar WAL/FSM/VM y volver a habilitar autovacuum de la tabla.
6. Crear tablespaces y ejecutar modelo/pruebas de [1.5](Practica1-5/README.md).
7. Cargar un millón de filas y ejecutar validación de [1.6](Practica1-6/README.md). Exigir verdadero en los tres indicadores y revisar las mediciones documentadas.
8. Ejecutar `docker compose down` **sin `--volumes`**, recrear con `up -d --wait` y confirmar que los datos siguen disponibles.

Conservar los volúmenes del participante para continuar el curso. Los entornos temporales de validación se detienen y sus recursos exclusivos se retiran al terminar las comprobaciones.
