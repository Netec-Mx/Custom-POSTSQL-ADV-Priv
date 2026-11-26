<a href="https://netec.com">
  <img src="https://static.wixstatic.com/media/96edd2_934dddb3230b4b16b1d1c56c51143738~mv2.png" alt="Logo de Netec" width="300"/>
</a>

# PostgreSQL Avanzado

## Plataforma de laboratorios

Te damos la bienvenida a la **plataforma de laboratorios** del curso **PostgreSQL Avanzado**. Aquí podrás explorar diferentes tecnologías a través de prácticas guiadas. ¡Desarrolla tus habilidades y lleva tus conocimientos al siguiente nivel!

## Lista de laboratorios

Cada uno de estos laboratorios está diseñado para ofrecerte una experiencia práctica. Haz clic en los enlaces para comenzar.

### Capítulo 1. Arquitectura y Configuración del Servidor

#### [Práctica 1.1. Instalación del Entorno PostgreSQL sobre Debian 13 (MV-Docker)](Capitulo01/Practica1-1/README.md)

  - **Descripción**: Construir el entorno Debian 13 con PostgreSQL 18 en Docker, verificar autenticación y comprobar persistencia de datos.

  - ⏱️ **Duración estimada**: 45 min.

#### [Práctica 1.2. Explorando la Arquitectura Interna de PostgreSQL](Capitulo01/Practica1-2/README.md)

  - **Descripción**: Identificar procesos del servidor, relacionar conexiones con backends y observar actividad y mantenimiento.

  - ⏱️ **Duración estimada**: 35 min.

#### [Práctica 1.3. Análisis de Configuración y Memoria](Capitulo01/Practica1-3/README.md)

  - **Descripción**: Comparar ordenaciones en disco y memoria, distinguir parámetros y aplicar cambios por recarga y reinicio.

  - ⏱️ **Duración estimada**: 35 min.

#### [Práctica 1.4. Explorando PGDATA y WAL](Capitulo01/Practica1-4/README.md)

  - **Descripción**: Localizar archivos internos, medir generación de WAL y observar FSM y Visibility Map.

  - ⏱️ **Duración estimada**: 30 min.

#### [Práctica 1.5. Diseño de una Base de Datos Empresarial](Capitulo01/Practica1-5/README.md)

  - **Descripción**: Implementar un modelo relacional empresarial, validar restricciones y preparar tablespaces con volúmenes persistentes.

  - ⏱️ **Duración estimada**: 45 min.

#### [Práctica 1.6. Grandes Volúmenes de Información](Capitulo01/Practica1-6/README.md)

  - **Descripción**: Generar un millón de filas, comparar poda de particiones y evaluar índices mediante planes y mediciones reales.

  - ⏱️ **Duración estimada**: 30 min.

### Capítulo 2. Administración Avanzada y Alta Disponibilidad

#### [Práctica 2.1. Análisis de concurrencia y niveles de aislamiento](Capitulo02/Practica2-1/README.md)

  - **Descripción**: Identificar el efecto de MVCC sobre lecturas concurrentes, reproducir lecturas en READ COMMITTED y REPEATABLE READ, y detectar un bloqueo y su sesión responsable.

  - ⏱️ **Duración estimada**: 70 min.

#### [Práctica 2.2. Monitoreo de Bloqueos y Mantenimiento Preventivo](Capitulo02/Practica2-2/README.md)

  - **Descripción**: Monitorear bloqueos, distinguir VACUUM, ANALYZE y VACUUM FULL, medir filas obsoletas e identificar parámetros de autovacuum.

  - ⏱️ **Duración estimada**: 65 min.

#### [Práctica 2.3. Administración de Índices y Optimización de Acceso a Datos](Capitulo02/Practica2-3/README.md)

  - **Descripción**: Crear índices para filtros y rangos, comparar planes con EXPLAIN (ANALYZE, BUFFERS) y explicar sus costos de lectura, escritura y mantenimiento.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 2.4. Implementación de Respaldos con pg_basebackup](Capitulo02/Practica2-4/README.md)

  - **Descripción**: Crear una copia física base del clúster en formato plano y tar, verificar su integridad y diferenciarla de la copia lógica y del PITR completo.

  - ⏱️ **Duración estimada**: 70 min.

#### [Práctica 2.5. Configuración de pgBackRest](Capitulo02/Practica2-5/README.md)

  - **Descripción**: Reconocer los componentes de una stanza y un repositorio, diseñar la configuración mínima de un respaldo empresarial y verificar comandos sin alterar el clúster base.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 2.6. Administración de Respaldos con Barman](Capitulo02/Practica2-6/README.md)

  - **Descripción**: Explicar el papel del servidor central de respaldos, preparar un inventario de instancias y parámetros, y ejecutar validaciones de Barman cuando esté instalado.

  - ⏱️ **Duración estimada**: 55 min.

#### [Práctica 2.7. Simulación de recuperación ante desastres](Capitulo02/Practica2-7/README.md)

  - **Descripción**: Definir RPO y RTO en un escenario de interrupción, ejecutar una restauración lógica aislada y diseñar un simulacro físico con PITR.

  - ⏱️ **Duración estimada**: 65 min.

### Capítulo 3. Análisis de I/O, estadísticas y optimización de PostgreSQL

#### [Práctica 3.1. Monitoreo de Actividad y Rendimiento de Disco](Capitulo03/Practica3-1/README.md)

  - **Descripción**: Medir lecturas físicas y aciertos de caché de una tabla, interpretar contadores acumulados de I/O y relacionar el acceso a disco con decisiones de optimización.

  - ⏱️ **Duración estimada**: 65 min.

#### [Práctica 3.2. Análisis de Estadísticas con ANALYZE](Capitulo03/Practica3-2/README.md)

  - **Descripción**: Observar estimaciones de filas antes y después de recopilar estadísticas, inspeccionar pg_stats y pg_stat_user_tables, y justificar cuándo ejecutar ANALYZE.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 3.3. Comparación de Planes de Ejecución](Capitulo03/Practica3-3/README.md)

  - **Descripción**: Comparar accesos secuenciales e indexados, medir costos, tiempos, filas y buffers, y justificar una optimización basada en evidencias.

  - ⏱️ **Duración estimada**: 70 min.

#### [Práctica 3.4. Optimización de Parámetros para Cargas OLTP](Capitulo03/Practica3-4/README.md)

  - **Descripción**: Evaluar el efecto de synchronous_commit y statement_timeout en transacciones cortas y comparar latencias y throughput, documentando el riesgo de durabilidad.

  - ⏱️ **Duración estimada**: 65 min.

#### [Práctica 3.5. Optimización de Parámetros para Cargas OLAP](Capitulo03/Practica3-5/README.md)

  - **Descripción**: Medir planes de agregación y ordenación con distintos valores de work_mem, identificar ordenaciones en disco y explicar los límites del paralelismo.

  - ⏱️ **Duración estimada**: 75 min.

### Capítulo 4. WAL, replicación y alta disponibilidad de PostgreSQL

#### [Práctica 4.1. Archivado WAL y durabilidad](Capitulo04/Practica4-1/README.md)

  - **Descripción**: Comprobar el funcionamiento del WAL y diseñar un archivado seguro.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 4.2. Replicación física con streaming](Capitulo04/Practica4-2/README.md)

  - **Descripción**: Reconocer el primario y preparar una réplica física sin alterar el clúster del curso.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 4.3. Replicación lógica](Capitulo04/Practica4-3/README.md)

  - **Descripción**: Definir una publicación lógica de datos comerciales y evaluar su alcance.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 4.4. Recuperación a un punto en el tiempo (PITR)](Capitulo04/Practica4-4/README.md)

  - **Descripción**: Planificar una recuperación histórica a partir de un respaldo base y WAL archivados.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 4.5. Patroni y prevención de split-brain](Capitulo04/Practica4-5/README.md)

  - **Descripción**: Comprender el liderazgo, el quórum y la promoción segura de un standby.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 4.6. Configuración e implementación de PgBouncer](Capitulo04/Practica4-6/README.md)

  - **Descripción**: Evaluar cómo el pooling reduce la presión de conexiones de las aplicaciones.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 4.7. HAProxy: enrutamiento por rol](Capitulo04/Practica4-7/README.md)

  - **Descripción**: Diseñar un endpoint estable para escrituras y verificar el rol del destino.

  - ⏱️ **Duración estimada**: 60 min.

#### [Práctica 4.8. Arquitectura empresarial de alta disponibilidad](Capitulo04/Practica4-8/README.md)

  - **Descripción**: Integrar replicación, Patroni, HAProxy, PgBouncer y respaldos en un simulacro.

  - ⏱️ **Duración estimada**: 60 min.

---

## 📬 **Contacto y más información**

Si tienes alguna pregunta o necesitas más detalles, no dudes en [contactarnos](mailto:soporte@netec.com). También puedes encontrar más recursos en nuestra [página](https://netec.com).

---

¡Gracias por visitar nuestra plataforma! No olvides revisar todos los laboratorios y comenzar tu viaje de aprendizaje hoy mismo.
