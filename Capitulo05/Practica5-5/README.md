# Práctica 5.5. Implementación de Vistas y Vistas Materializadas para Reportes

**Netec · PostgreSQL Avanzado · El Salvador**

[⬅️ Anterior](../Practica5-4/README.md) · [Índice](../README.md) · [Siguiente ➡️](../README.md#consolidación-y-ejercicio-integrador)

## Objetivo
Aplicar implementación de vistas y vistas materializadas para reportes al ciclo de pedidos de la empresa, conservando los datos del capítulo 4.

## Flujo de trabajo
```mermaid
flowchart LR
 A[Pedidos] --> B[Preparación] --> C[Prueba SQL] --> D[Validación] --> E[Evidencia]
```

## Preparación común
Desde Windows, entrar al contenedor existente (sin modificar Compose, Dockerfile ni entrypoint):
```bash
docker exec -it -u root postgres-ca-debian13-lab bash
# Confirmar el clúster principal y la base existente.
pg_lsclusters
runuser -u postgres -- psql -X -p 5432 -d lab_storage -v ON_ERROR_STOP=1
```
Dentro de psql:
```sql
SELECT current_database(), current_setting('server_version'), pg_is_in_recovery();
-- Debe ser lab_storage, PostgreSQL 18 y false.
SELECT to_regclass('lab_c4_integrador.pedidos');
```
Si no existe la tabla anterior, detenerse y revisar el capítulo 4. No trabajar sobre una réplica ni sobre Patroni. Ejecutar los bloques en orden, en psql con autocommit, salvo donde se indique BEGIN. Los resultados son esperados, no mediciones realizadas. Se usa un esquema nuevo `lab_c5_integrador` para conservar los pedidos originales. No limpiar entre prácticas.

## Tarea 1. Vistas, materialización y tabla resumen
```sql
CREATE VIEW lab_c5_integrador.v_reporte AS
SELECT cliente,count(*) AS cantidad,sum(total) AS importe
FROM lab_c5_integrador.pedidos GROUP BY cliente;
CREATE MATERIALIZED VIEW lab_c5_integrador.mv_reporte AS
SELECT * FROM lab_c5_integrador.v_reporte;
-- Índice único sin predicado ni expresiones: requisito para refresh concurrente.
CREATE UNIQUE INDEX c5_mv_cliente ON lab_c5_integrador.mv_reporte(cliente);
CREATE TABLE lab_c5_integrador.resumen AS SELECT * FROM lab_c5_integrador.v_reporte;
ALTER TABLE lab_c5_integrador.resumen ADD PRIMARY KEY(cliente);
INSERT INTO lab_c5_integrador.pedidos VALUES(-504,'Cliente Reporte',200,'PENDIENTE','2026-01-01');
SELECT * FROM lab_c5_integrador.v_reporte WHERE cliente='Cliente Reporte';
SELECT * FROM lab_c5_integrador.mv_reporte WHERE cliente='Cliente Reporte';
-- La vista normal devuelve 200; la materializada todavía no contiene la fila.
REFRESH MATERIALIZED VIEW CONCURRENTLY lab_c5_integrador.mv_reporte;
BEGIN;
-- Recarga completa atómica; bloquea accesos incompatibles durante TRUNCATE.
TRUNCATE lab_c5_integrador.resumen;
INSERT INTO lab_c5_integrador.resumen SELECT * FROM lab_c5_integrador.v_reporte;
COMMIT;
SELECT * FROM lab_c5_integrador.resumen WHERE cliente='Cliente Reporte';
ANALYZE lab_c5_integrador.pedidos;
ANALYZE lab_c5_integrador.mv_reporte;
ANALYZE lab_c5_integrador.resumen;
EXPLAIN (ANALYZE,BUFFERS) SELECT * FROM lab_c5_integrador.v_reporte WHERE cliente='Lote 1';
EXPLAIN (ANALYZE,BUFFERS) SELECT * FROM lab_c5_integrador.mv_reporte WHERE cliente='Lote 1';
EXPLAIN (ANALYZE,BUFFERS) SELECT * FROM lab_c5_integrador.resumen WHERE cliente='Lote 1';
```
La vista normal no almacena resultados; no garantiza mejoras de tiempo. La materializada requiere actualización explícita y espacio. CONCURRENTLY permite lecturas durante el refresco, requiere una vista poblada e índice único adecuado. Una tabla resumen admite actualización incremental diseñada por el equipo. Medir varias veces, registrar buffers, tiempo y frescura; no asumir porcentajes de mejora.
## Validación y reto
Verificar que las tres estrategias devuelven el mismo resultado tras actualizar. Diseñar una política de actualización cada cinco minutos y justificar el coste y el retraso tolerable. Comparar planes con filtros selectivos y sin filtros.

## Evidencias y diagnóstico
Guardar las consultas, resultados reales y explicación de errores. Si aparece 42P01, comprobar que se ejecutaron las prácticas anteriores; si aparece 42501, revisar permisos; si aparece 25P02, ejecutar ROLLBACK antes de continuar. No borrar objetos de otros capítulos. La limpieza se realiza únicamente al final del capítulo.

[Índice](../README.md) · [Continuar](../README.md#consolidación-y-ejercicio-integrador)
