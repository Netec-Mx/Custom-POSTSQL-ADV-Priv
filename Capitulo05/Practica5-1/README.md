# Práctica 5.1. Desarrollo de Functions y Procedures en PL/pgSQL

**Netec · PostgreSQL Avanzado · El Salvador**

[⬅️ Anterior](../README.md) · [Índice](../README.md) · [Siguiente ➡️](../Practica5-2/README.md)

## Objetivo
Aplicar desarrollo de functions y procedures en pl/pgsql al ciclo de pedidos de la empresa, conservando los datos del capítulo 4.

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

## Tarea 1. Preparar el modelo empresarial
Se conserva el caso de pedidos del capítulo 4 y se toma una copia de su estructura y datos. Se añaden pedidos ficticios para pruebas reproducibles. Ejecutar una sola vez desde un esquema inexistente; si ya existe, revisar antes de reiniciar.
```sql
BEGIN;
CREATE SCHEMA lab_c5_integrador;
CREATE TABLE lab_c5_integrador.pedidos (
 id_pedido bigint PRIMARY KEY, cliente varchar(100) NOT NULL,
 total numeric(12,2) NOT NULL, estado varchar(20) NOT NULL,
 fecha timestamp DEFAULT CURRENT_TIMESTAMP
);
INSERT INTO lab_c5_integrador.pedidos
SELECT id_pedido, cliente, total, estado, fecha FROM lab_c4_integrador.pedidos;
-- Identificadores negativos reservados exclusivamente para este laboratorio.
INSERT INTO lab_c5_integrador.pedidos VALUES
(-501,'Cliente A',1000,'PENDIENTE', '2026-01-01'),
(-502,'Cliente B',500,'PENDIENTE', '2026-01-01');
COMMIT;
```
## Tarea 2. Funciones escalares, volatilidad y parámetros
```sql
-- Cálculo determinista: no depende de tablas ni del reloj.
CREATE FUNCTION lab_c5_integrador.descuento(p_total numeric, p_pct numeric)
RETURNS numeric LANGUAGE plpgsql IMMUTABLE STRICT AS $$
BEGIN
 IF p_total < 0 OR p_pct NOT BETWEEN 0 AND 100 THEN
  RAISE EXCEPTION 'Importe o porcentaje inválido' USING ERRCODE='22023';
 END IF;
 RETURN round(p_total * (1-p_pct/100),2);
END; $$;
SELECT lab_c5_integrador.descuento(1000,10); -- 900.00
-- STABLE: consulta datos con la instantánea de la sentencia.
CREATE FUNCTION lab_c5_integrador.total_cliente(p_cliente text)
RETURNS numeric LANGUAGE sql STABLE AS $$
 SELECT coalesce(sum(total),0) FROM lab_c5_integrador.pedidos WHERE cliente=p_cliente;
$$;
-- VOLATILE: puede cambiar incluso dentro de una sentencia.
CREATE FUNCTION lab_c5_integrador.instante() RETURNS timestamptz
LANGUAGE sql VOLATILE AS $$ SELECT clock_timestamp(); $$;
-- TABLE: devuelve varias filas y columnas.
CREATE FUNCTION lab_c5_integrador.pendientes(p_min numeric DEFAULT 0)
RETURNS TABLE(id bigint, importe numeric) LANGUAGE sql STABLE AS $$
 SELECT id_pedido,total FROM lab_c5_integrador.pedidos
 WHERE estado='PENDIENTE' AND total>=p_min;
$$;
CREATE FUNCTION lab_c5_integrador.ids_pendientes() RETURNS SETOF bigint
LANGUAGE sql STABLE AS $$ SELECT id_pedido FROM lab_c5_integrador.pedidos WHERE estado='PENDIENTE'; $$;
SELECT * FROM lab_c5_integrador.pendientes(500) WHERE id IN (-501,-502);
```
## Tarea 3. Procedimiento y control de flujo
```sql
CREATE PROCEDURE lab_c5_integrador.confirmar(IN p_id bigint, INOUT p_cambiado boolean)
LANGUAGE plpgsql AS $$
BEGIN
 -- UPDATE condicional atómico: evita confirmar dos veces.
 UPDATE lab_c5_integrador.pedidos SET estado='CONFIRMADO'
 WHERE id_pedido=p_id AND estado='PENDIENTE';
 p_cambiado := FOUND;
END; $$;
CALL lab_c5_integrador.confirmar(-501,false); -- true
CALL lab_c5_integrador.confirmar(-501,false); -- false
DO $$
DECLARE i integer := 0;
BEGIN
 LOOP
  i := i+1;
  EXIT WHEN i>5;
  CONTINUE WHEN i=3;
  IF i=1 THEN RAISE NOTICE 'Inicio';
  ELSIF i=5 THEN RAISE NOTICE 'Fin';
  ELSE RAISE NOTICE 'Iteración %',i; END IF;
 END LOOP;
END; $$;
SELECT proname, provolatile FROM pg_proc WHERE pronamespace='lab_c5_integrador'::regnamespace;
```
`i`, `s`, `v` representan IMMUTABLE, STABLE y VOLATILE. CALL no equivale a SELECT. El procedimiento no confirma transacciones internamente: el cliente decide COMMIT/ROLLBACK. SECURITY INVOKER es el valor por defecto; en producción revocar EXECUTE a PUBLIC y concederlo a roles autorizados. Un SECURITY DEFINER requiere search_path seguro y nombres calificados.
## Validación y reto
Verificar descuento 900, dos pedidos retornados antes de confirmar y resultados true/false del procedimiento. Implementar una función CASE para clasificar importes y un WHILE que recorra cinco valores. Explicar por qué una función que consulta precios no es IMMUTABLE.

## Evidencias y diagnóstico
Guardar las consultas, resultados reales y explicación de errores. Si aparece 42P01, comprobar que se ejecutaron las prácticas anteriores; si aparece 42501, revisar permisos; si aparece 25P02, ejecutar ROLLBACK antes de continuar. No borrar objetos de otros capítulos. La limpieza se realiza únicamente al final del capítulo.

[Índice](../README.md) · [Continuar](../Practica5-2/README.md)
