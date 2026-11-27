# Práctica 5.2. Manejo de Excepciones, Cursores y Procesamiento de Datos

**Netec · PostgreSQL Avanzado · El Salvador**

[⬅️ Anterior](../Practica5-1/README.md) · [Índice](../README.md) · [Siguiente ➡️](../Practica5-3/README.md)

## Objetivo
Aplicar manejo de excepciones, cursores y procesamiento de datos al ciclo de pedidos de la empresa, conservando los datos del capítulo 4.

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

## Tarea 1. Excepciones y diagnóstico
```sql
DO $$
DECLARE v_codigo text; v_mensaje text;
BEGIN
 BEGIN
  PERFORM lab_c5_integrador.descuento(1000,150);
 EXCEPTION WHEN invalid_parameter_value THEN
  GET STACKED DIAGNOSTICS v_codigo=RETURNED_SQLSTATE,v_mensaje=MESSAGE_TEXT;
  RAISE NOTICE 'SQLSTATE %, mensaje %',v_codigo,v_mensaje;
 END;
END; $$;
-- Esperado: 22023; la sesión sigue operativa.
```
Un bloque EXCEPTION revierte sus cambios persistentes al capturar un error. Evitar WHEN OTHERS sin relanzar fallos inesperados. DETAIL y HINT permiten comunicar contexto sin revelar secretos.
## Tarea 2. Cursores y procesamiento por lotes
```sql
-- Datos sintéticos de volumen: 10000 pedidos, sin tocar los originales.
INSERT INTO lab_c5_integrador.pedidos(id_pedido,cliente,total,estado,fecha)
SELECT -100000-g,'Lote '||(g%100),g%1000+1,'PENDIENTE',timestamp '2026-01-01'
FROM generate_series(1,10000) g ON CONFLICT DO NOTHING;
CREATE TABLE lab_c5_integrador.lote_resultado(id_pedido bigint PRIMARY KEY, categoria text NOT NULL);
DO $$
DECLARE c CURSOR FOR SELECT id_pedido,total FROM lab_c5_integrador.pedidos WHERE id_pedido BETWEEN -110000 AND -100001 ORDER BY id_pedido;
 r record; n integer:=0;
BEGIN
 OPEN c;
 LOOP
  FETCH c INTO r;
  EXIT WHEN NOT FOUND;
  INSERT INTO lab_c5_integrador.lote_resultado VALUES(r.id_pedido,CASE WHEN r.total>=500 THEN 'ALTO' ELSE 'NORMAL' END) ON CONFLICT DO NOTHING;
  n:=n+1;
 END LOOP;
 CLOSE c;
 RAISE NOTICE 'Procesados %',n;
END; $$;
SELECT count(*) FROM lab_c5_integrador.lote_resultado; -- 10000
-- Cursor SQL: la transacción debe permanecer abierta.
BEGIN;
DECLARE c_pedidos NO SCROLL CURSOR FOR SELECT * FROM lab_c5_integrador.pedidos ORDER BY id_pedido;
FETCH FORWARD 100 FROM c_pedidos;
CLOSE c_pedidos;
COMMIT;
-- Alternativa set-based: medir sin persistir cambios adicionales.
BEGIN;
EXPLAIN (ANALYZE, BUFFERS) INSERT INTO lab_c5_integrador.lote_resultado
SELECT id_pedido,CASE WHEN total>=500 THEN 'ALTO' ELSE 'NORMAL' END
FROM lab_c5_integrador.pedidos WHERE id_pedido BETWEEN -110000 AND -100001
ON CONFLICT DO NOTHING;
ROLLBACK;
```
FETCH limita las filas entregadas al cliente, no garantiza que el plan no materialice datos. El bucle anterior es una sola transacción, no diez mil commits. Comparar operaciones equivalentes con el mismo estado inicial y repetir mediciones; no inferir velocidad del ejemplo con conflictos.
## Validación y reto
Comprobar 10000 filas y SQLSTATE 22023. Diseñar un cursor parametrizado y explicar cuándo preferir INSERT SELECT. Proponer lotes con checkpoints idempotentes para reanudar después de un fallo.

## Evidencias y diagnóstico
Guardar las consultas, resultados reales y explicación de errores. Si aparece 42P01, comprobar que se ejecutaron las prácticas anteriores; si aparece 42501, revisar permisos; si aparece 25P02, ejecutar ROLLBACK antes de continuar. No borrar objetos de otros capítulos. La limpieza se realiza únicamente al final del capítulo.

[Índice](../README.md) · [Continuar](../Practica5-3/README.md)
