# Capítulo 3 · Análisis de I/O, estadísticas y optimización de PostgreSQL

**Curso:** PostgreSQL Avanzado – Netec El Salvador  
**Entorno de referencia:** PostgreSQL 18 · Debian · Docker Compose · servicio `debian-pg` · base de datos `curso`.

## Temario conceptual
- **3.1 Análisis de I/O:** lecturas, escrituras, buffers y métricas de disco.
- **3.2 Recolector de Estadísticas:** estimaciones, selectividad y `ANALYZE`.
- **3.3 Parámetros de Configuración:** memoria, concurrencia y ajustes OLTP/OLAP.
- **Resumen:** selección de medidas basada en evidencia reproducible.

## Laboratorios

| Práctica | Objetivo resumido | Archivo |
|---|---|---|
| 3.1 Monitoreo de Actividad y Rendimiento de Disco | Medir lecturas físicas y aciertos de caché de una tabla mediante estadísticas y `EXPLAIN (ANALYZE, BUFFERS)`. | [README](Practica3-1/README.md) |
| 3.2 Análisis de Estadísticas con ANALYZE | Observar estimaciones de filas antes y después de recopilar estadísticas. | [README](Practica3-2/README.md) |
| 3.3 Comparación de Planes de Ejecución | Comparar métodos de acceso secuencial e indexado mediante `EXPLAIN (ANALYZE, BUFFERS)`. | [README](Practica3-3/README.md) |
| 3.4 Optimización de Parámetros para Cargas OLTP | Reconocer las características de cargas OLTP: transacciones cortas y frecuentes. | [README](Practica3-4/README.md) |
| 3.5 Optimización de Parámetros para Cargas OLAP | Distinguir cargas OLAP basadas en lecturas y agregaciones masivas. | [README](Practica3-5/README.md) |

## Secuencia recomendada
1. Completar prácticas 3.1–3.3 para construir evidencia de rendimiento.
2. Aplicar pruebas de parámetros de sesión en 3.4 y 3.5.
3. Registrar planes, resultados reales, limitaciones y limpieza.

## Consideraciones de seguridad
- Cada práctica usa su propio esquema `lab_*` y termina con una limpieza explícita.
- No ejecutar en producción ni usar `ALTER SYSTEM` para estas demostraciones.
- No asumir que el servidor dispone de caché vacía ni que un plan será idéntico en todos los equipos.
- **Nota de continuidad:** el README de referencia del Capítulo 2 utiliza la base `curso`; por ello estos ejercicios también utilizan `curso`, no `laboratorios` de otras actividades.

[⬅️ Volver al Capítulo 2](../Capitulo02/README.md) · [Comenzar Práctica 3.1](Practica3-1/README.md)
