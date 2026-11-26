# Capítulo 4 · WAL, replicación y alta disponibilidad de PostgreSQL

**Curso:** PostgreSQL Avanzado – Netec El Salvador  
**Entorno reutilizado:** PostgreSQL 18 · Debian 13 · Docker Compose · servicio `debian-pg` · base `curso` · esquema `empresa`.

## Objetivo del capítulo
Analizar la durabilidad, replicación, recuperación y continuidad operativa de PostgreSQL, aprovechando el entorno y los datos comerciales preparados en los capítulos 1, 2 y 3.

## Continuidad con capítulos anteriores
- [Capítulo 1](../Capitulo01/README.md): instalación, arquitectura y base de datos del curso.
- [Capítulo 2](../Capitulo02/README.md): concurrencia, administración, respaldos y recuperación.
- [Capítulo 3](../Capitulo03/README.md): observabilidad, estadísticas y medición del rendimiento.
- No se sustituye `curso` por `store`: el material disponible utiliza `curso` y el esquema `empresa`.

## Laboratorios

| Práctica | Tema | Guía |
|---|---|---|
| 4.1 | Archivado WAL y durabilidad | [README](Practica4-1/README.md) |
| 4.2 | Replicación física con streaming | [README](Practica4-2/README.md) |
| 4.3 | Replicación lógica | [README](Practica4-3/README.md) |
| 4.4 | Recuperación a un punto en el tiempo (PITR) | [README](Practica4-4/README.md) |
| 4.5 | Patroni y prevención de split-brain | [README](Practica4-5/README.md) |
| 4.6 | Configuración e implementación de PgBouncer | [README](Practica4-6/README.md) |
| 4.7 | HAProxy: enrutamiento por rol | [README](Practica4-7/README.md) |
| 4.8 | Arquitectura empresarial de alta disponibilidad | [README](Practica4-8/README.md) |

## Secuencia recomendada
1. Diagnosticar WAL y replicación desde el clúster existente.
2. Relacionar respaldos y recuperación con los escenarios comerciales del curso.
3. Analizar coordinación de liderazgo, pooling y enrutamiento.
4. Integrar los componentes en un diseño empresarial y documentar RPO, RTO y evidencias.

## Seguridad y alcance
Las guías incluyen consultas funcionales de diagnóstico sobre el entorno existente. Los escenarios de replicación, PITR y failover que requieran instancias adicionales se presentan como actividades de diseño y verificación, no como una instalación ya ejecutada. No se incluyen scripts, archivos Compose ni configuraciones nuevas. No ejecutar operaciones destructivas sobre la instalación del curso.

## Cierre: síntesis del capítulo
- **WAL:** durabilidad y fundamento de la recuperación.
- **Replicación:** continuidad mediante copias físicas o datos seleccionados.
- **PITR:** restauración histórica con respaldo base y WAL.
- **Patroni:** liderazgo y failover coordinado.
- **PgBouncer y HAProxy:** control y enrutamiento de conexiones.
- **Arquitectura integral:** RPO, RTO, observabilidad, simulacros y respaldos.

[⬅️ Capítulo 3](../Capitulo03/README.md) · [Comenzar práctica 4.1](Practica4-1/README.md)
