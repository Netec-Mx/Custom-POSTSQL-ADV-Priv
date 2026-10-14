# Imágenes de apoyo del capítulo 1

Generadas con la herramienta integrada `image_gen` y revisadas antes de incorporarlas al repositorio. Son ilustraciones conceptuales para complementar los diagramas y los pasos de las prácticas.

## entorno-debian-docker.png

- Archivo: [entorno-debian-docker.png](entorno-debian-docker.png).
- Práctica: [1.1](../../Capitulo01/Practica1-1/README.md).
- Prompt final utilizado:

```text
Use case: scientific-educational. Asset type: landscape bitmap illustration for a Spanish PostgreSQL laboratory README. Create a polished 3D isometric educational illustration on a clean light background, generous spacing, readable at laptop width. Subject: a laptop at left connected to a single translucent Docker container at center containing Debian 13 and PostgreSQL 18, and three persistent storage cylinders at right. Depict connection flow left to right. Exact text only: title 'Entorno del laboratorio'; laptop caption 'Equipo'; connection caption '127.0.0.1:5433'; container labels 'Docker', 'Debian 13', 'PostgreSQL 18', '5432'; three storage labels 'PGDATA', 'ts_hot', 'ts_cold'. All three storage cylinders connect independently to the container, none are inside the laptop. The host port 5433 maps to internal port 5432, visually clearly separated. Style: sophisticated editorial educational illustration, restrained blue and teal with warm amber storage accents, dimensional materials, crisp edges, no excessive decorative elements, no screenshot or fake UI, no additional text, no watermark. Landscape 1536x1024.
```

## procesos-postgresql.png

- Archivo: [procesos-postgresql.png](procesos-postgresql.png).
- Práctica: [1.2](../../Capitulo01/Practica1-2/README.md).
- Prompt final utilizado:

```text
Use case: scientific-educational. Asset type: landscape raster conceptual illustration for a Spanish PostgreSQL 18 architecture laboratory README. Create a highly legible polished 3D isometric educational infographic on light background. Title exact 'Procesos de PostgreSQL'. Show one central supervising server block labelled 'Postmaster' above five independent smaller worker blocks arranged spaciously below with branching connectors downward from the supervisor, labelled exactly 'Backend', 'Checkpointer', 'Background writer', 'WAL writer', 'Autovacuum'. Above-left show a small client computer labelled 'Cliente' connected to the Backend block, illustrating the dedicated backend connection. Keep supervisor connections separate from client connection and avoid any worker-to-worker pipeline. All blocks are processes within one softly outlined server region labelled 'PostgreSQL 18'. Technical meaning: one supervising process coordinating independent server processes, not five physical servers; Autovacuum block represents maintenance coordinator/workers conceptually. Minimal text exactly as specified, no explanatory paragraphs or technical measurements. Style consistent with a high quality teaching manual: blue teal process blocks with subtle amber accent, clean 3D material rendering, abundant whitespace, no fake terminal screenshot, no logos needed, no watermark. Landscape 1536x1024.
```

## almacenamiento-pgdata.png

- Archivo: [almacenamiento-pgdata.png](almacenamiento-pgdata.png).
- Práctica: [1.4](../../Capitulo01/Practica1-4/README.md).
- Prompt final utilizado:

```text
Use case: scientific-educational. Asset type: landscape raster educational cutaway illustration supporting a PostgreSQL storage README in Spanish. Create a sophisticated 3D isometric cutaway of a single storage cabinet labelled 'PGDATA' on clean pale background. Title exact 'Almacenamiento interno'. Inside show three clearly separated compartments labelled exactly 'base', 'global', 'pg_wal'. In the base compartment illustrate data pages and two smaller adjacent metadata cards, labels exactly 'Datos', 'FSM', 'VM'; visually group these as parts of a table stored in base. In global show small shared catalog files without additional text. In pg_wal show sequential journal segments distinct from table data pages. Below the image three brief exact captions, aligned and spacious: 'FSM: espacio reutilizable', 'VM: visibilidad de páginas', 'WAL: recuperación'. Do not draw arrows implying that WAL is stored inside table data or that FSM holds transaction logs. This is a conceptual illustration, not an actual filesystem screenshot. Match polished blue teal and amber 3D educational style, crisp typesetting, generous whitespace, minimal labels, no extra words, no watermark. Landscape 1536x1024.
```


