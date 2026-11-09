# Práctica 3.4. Optimización de Parámetros para Cargas OLTP

**[⬅️ Anterior](../Practica3-3/README.md)** | **[Lista general 🗂️](../README.md)** | **[Siguiente ➡️](../Practica3-5/README.md)**

## Objetivo de la práctica
- Reconocer las características de cargas OLTP: transacciones cortas y frecuentes.
- Evaluar el efecto de `synchronous_commit` y `statement_timeout` en sesiones de laboratorio.
- Comparar latencias y throughput de un lote transaccional controlado, documentando el riesgo de durabilidad.

## Conceptos fundamentales
**OLTP** (Online Transaction Processing) prioriza transacciones pequeñas, frecuentes y concurrentes. `synchronous_commit=on` espera la confirmación local de WAL de acuerdo con el modo de commit; `off` puede reducir latencia, **pero admite pérdida de transacciones recientemente confirmadas ante un fallo del servidor**. `statement_timeout` limita la duración de cada sentencia y evita operaciones descontroladas. `shared_buffers` y `max_connections` son parámetros de instancia que requieren análisis de capacidad; este ejercicio no los modifica. La prueba compara una única sesión, no representa automáticamente una carga concurrente real.

## Diagrama de arquitectura o procedimiento
```mermaid
flowchart LR
 A[Tabla OLTP] --> B[Medición baseline]
 B --> C[SET LOCAL en transacción]
 C --> D[Repetir lote]
 D --> E[Comparar tiempos y durabilidad]
```

## Duración aproximada
65 minutos.

## Requisitos y preparación
- PostgreSQL 18 y Docker Compose; servicio `debian-pg` en ejecución.
- Ejecutar los comandos de anfitrión desde la carpeta que contiene `compose.yaml`.
- Base de datos `curso` y acceso local como usuario del sistema `postgres`.
- **Seguridad:** laboratorio aislado; no ejecutar cargas, cambios de configuración ni limpiezas en producción.
- Los tiempos, planes y contadores dependen del hardware, caché y estadísticas: registrar resultados reales, no asumir valores fijos.

**Acceso a psql desde el anfitrión:**
```bash
# -X evita configuraciones personales de psql; -d selecciona la BD
 docker compose exec --user postgres debian-pg psql -X -d curso
```

### Tarea 1. Crear tabla de operaciones
**En psql `curso`:**
```sql
-- Separar el escenario OLTP de otros laboratorios
CREATE SCHEMA IF NOT EXISTS lab_oltp;
DROP TABLE IF EXISTS lab_oltp.movimientos;
CREATE TABLE lab_oltp.movimientos (
  id bigint GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
  cuenta integer NOT NULL,
  importe numeric(12,2) NOT NULL,
  creado_en timestamptz NOT NULL DEFAULT clock_timestamp()
);
-- Ver parámetros actuales sin cambiarlos globalmente
SHOW synchronous_commit;
SHOW statement_timeout;
SHOW shared_buffers;
SHOW max_connections;
```
### Tarea 2. Medir un lote con durabilidad habitual
**Desde el anfitrión (no dentro de psql):**
```bash
# 	iming en psql imprime la duración de cada sentencia
# Ejecutar 1.000 inserciones en una transacción con commit síncrono
 docker compose exec -T --user postgres debian-pg psql -X -d curso   -v ON_ERROR_STOP=1 -c "BEGIN; SET LOCAL synchronous_commit = on; INSERT INTO lab_oltp.movimientos(cuenta, importe) SELECT n % 100, 10 FROM generate_series(1,1000) n; COMMIT;"
```
**Nota:** el tiempo externo del comando incluye arranque de `psql`. Para una comparación más fina, activar `\timing on` en `psql` y repetir varias veces. No asumir diferencias grandes en entornos rápidos.
### Tarea 3. Comparar una transacción con commit asíncrono
**En psql `curso`:**
```sql
-- Iniciar una transacción aislada
BEGIN;
-- Ajuste temporal solo para esta transacción
SET LOCAL synchronous_commit = off;
-- Insertar otras 1.000 operaciones
INSERT INTO lab_oltp.movimientos(cuenta, importe)
SELECT n % 100, 20 FROM generate_series(1,1000) AS n;
-- Confirmar; menor espera no equivale a mayor seguridad
COMMIT;
-- Verificar que SET LOCAL no cambió el valor global
SHOW synchronous_commit;
```
**Advertencia:** `synchronous_commit=off` reduce garantías de durabilidad frente a un fallo; utilizar únicamente para demostración y nunca como recomendación universal.
### Tarea 4. Aplicar límite de tiempo de forma local
```sql
-- Limitar la duración de una sentencia dentro de esta transacción
BEGIN;
SET LOCAL statement_timeout = '500ms';
-- La siguiente sentencia debería exceder el límite
SELECT pg_sleep(2);
-- Tras el error, la transacción queda abortada: liberar el estado
ROLLBACK;
-- Consultar el valor fuera de la transacción
SHOW statement_timeout;
-- Validar filas insertadas por ambas pruebas
SELECT count(*) AS movimientos FROM lab_oltp.movimientos;
```
**Esperado:** 2.000 movimientos si ambas inserciones finalizaron; `pg_sleep(2)` cancela la sentencia con `statement_timeout` y requiere `ROLLBACK`.
### Tarea 5. Evaluar resultados
Registrar: duración del lote `on`, duración del lote `off`, número de filas y justificación de riesgos. Para evaluar concurrencia real se requiere una herramienta como `pgbench`, repeticiones y condiciones controladas.

## Resultado esperado
El estudiante demuestra el alcance de `SET LOCAL`, observa un timeout controlado y documenta diferencias de latencia sin confundirlas con mejoras garantizadas de throughput ni omitir riesgos de durabilidad.

## Ejercicio de análisis
1. ¿Qué riesgo introduce `synchronous_commit=off` ante un fallo abrupto?
2. ¿Por qué no se debe cambiar `max_connections` sin evaluar memoria y pool de conexiones?
3. ¿Por qué una medición de una sola sesión no representa toda una carga OLTP?

## Evidencias de aprendizaje
- Transcripción o captura de las sentencias y resultados obtenidos.
- Comparación documentada de las métricas antes y después, con interpretación.
- Respuestas a las preguntas de análisis y evidencia de limpieza.

## Limpieza
**En psql `curso`:**
```sql
-- Eliminar las operaciones ficticias del laboratorio
DROP SCHEMA IF EXISTS lab_oltp CASCADE;
```

## Bibliografía oficial y técnica
- [PostgreSQL 18 · Monitoring Database Activity](https://www.postgresql.org/docs/18/monitoring-stats.html)
- [PostgreSQL 18 · EXPLAIN](https://www.postgresql.org/docs/18/using-explain.html)
- [PostgreSQL 18 · ANALYZE](https://www.postgresql.org/docs/18/sql-analyze.html)
- [PostgreSQL 18 · Resource Consumption](https://www.postgresql.org/docs/18/runtime-config-resource.html)


**[⬅️ Anterior](../Practica3-3/README.md)** | **[Lista general 🗂️](../README.md)** | **[Siguiente ➡️](../Practica3-5/README.md)**
