# PRD · <Nombre de tu producto>

> Documento de requisitos del producto (Product Requirements Document).
> Complétalo en el Sprint 0 (historia H0.3) y pide la aprobación del profesor en un Issue.
> Cada requisito funcional (RF) se convierte en un Issue con la plantilla "Historia de usuario".

| Campo | Valor |
| --- | --- |
| Alumno | |
| Proyecto n.º | |
| Sitio de referencia | |
| Compañero revisor | |
| URL desplegada | (se completa en el Sprint 5) |

## 1. Problema

Los lectores frecuentes suelen perder el rastro de los libros que inician, abandonan o terminan porque dispersan sus notas, lecturas pendientes y opiniones entre chats, bloc de notas o listas mentales desordenadas. Esto le duele especialmente a lectores habituales y estudiantes que quieren llevar un historial claro de sus lecturas, calificar títulos y consultar reseñas confiables sin depender de plataformas lentas o saturadas de ruido social.

## 2. Usuarios

- **Usuario principal:** Lector habitual o estudiante universitario que lleva el control de sus libros (por leer, leyendo, leídos), actualiza su avance de páginas o capítulos, publica calificaciones y reseñas, y consulta recomendaciones. Entra principalmente desde su smartphone en ratos libres y desde su laptop/PC cuando organiza listas o redacta reseñas más largas.
- **Usuario secundario:** Curador o lector explorador que revisa reseñas, etiquetas o listas públicas de otros usuarios para decidir su próxima lectura sin necesidad de administrar un catálogo complejo.

## 3. Objetivo y métrica de éxito

Un lector registra un libro, actualiza su progreso de lectura o publica una reseña con calificación en menos de 15 segundos desde la vista principal.

## 4. Alcance del MVP

Entre 5 y 8 funcionalidades que SÍ entran este semestre.

1.Catálogo y búsqueda de libros: Registrar libros (título, autor, género, total de páginas), buscarlos y filtrarlos por estado o título.
2.Estanterías y estados de lectura: Clasificar libros en Por leer, Leyendo, Leído o Pausado/Abandonado.
3.Seguimiento de progreso: Registrar página o porcentaje actual y calcular automáticamente el avance de lectura.
4.Reseñas y calificaciones: Asignar puntuación (por ejemplo, de 1 a 5 estrellas) y escribir reseñas o notas de lectura por libro.
5.Listas personalizadas: Agrupar libros en colecciones temáticas o lecturas por semestre/interés.
6.Historial y estadísticas de lectura: Visualizar libros completados, páginas leídas y ritmo de lectura acumulado.

### Funcionalidades → carpetas de `src/` (screaming architecture)

Cada funcionalidad del alcance será una carpeta en `src/`. Nómbralas en inglés y en plural.

|Funcionalidad| Carpeta|
|====|====|
|Catálogo y búsqueda de libros| src/books|
|Estanterías y estados de lectura| src/shelves|
|Seguimiento de progreso| src/progress|
|Reseñas y calificaciones| src/reviews|
|Listas personalizadas| src/lists|
|Historial y estadísticas de lectura| src/stats|

## 5. Fuera de alcance

Lo que NO vas a construir.

1.Red social compleja con feed algorítmico, mensajes directos o sistema de seguidores tipo red masiva.
2.Importación o sincronización automática en tiempo real con tiendas externas, e-readers comerciales o APIs de terceros de pago.
3.Sistema de compraventa, préstamos físicos o facturación dentro de la aplicación.
4.Aplicaciones nativas compiladas para iOS o Android (se prioriza la aplicación web responsive).

## 6. Requisitos funcionales

| ID | Requisito | Prioridad (alta, media, baja) |
| --- | --- | --- |

|RF-01 | "Permitir crear, editar, listar y buscar libros con título, autor, género y cantidad total de páginas."|Alta|
|RF-02 | "Permitir asignar y cambiar el estado de lectura de un libro (Por leer, Leyendo, Leído, Abandonado)."|Alta|
|RF-03 | Permitir actualizar la página actual y validar que no supere el total de páginas del libro ni sea negativa.|Alta|
|RF-04 | "Permitir crear, editar y eliminar reseñas con calificación numérica y comentario de texto asociadas a un libro."|Alta|
|RF-05 | Permitir crear listas personalizadas y agregar o quitar libros dentro de cada lista.|Media|
|RF-06 | "Mostrar un resumen de estadísticas básicas (libros terminados, páginas registradas y promedio de calificación)."|Media|
|RF-07 | "Permitir filtrar y ordenar el catálogo y las reseñas por título, estado, calificación o fecha de actualización."|Baja|

## 7. Requisitos no funcionales

- Responsive, móvil primero (375 px y 1280 px).
- Lighthouse ≥ 90 en Accesibilidad y Buenas prácticas.
- 0 errores en la consola del navegador.
- Funciona con 10.000 registros sin congelarse.

## 8. Modelo de datos

Diagrama entidad-relación y tablas PostgreSQL con tipos, claves y restricciones.
GitHub dibuja diagramas Mermaid; edita este ejemplo:

erDiagram
  USER ||--o{ BOOK : registra
  USER ||--o{ READING_LIST : crea
  USER ||--o{ REVIEW : escribe
  BOOK ||--o{ REVIEW : recibe
  BOOK ||--o{ READING_PROGRESS : registra
  READING_LIST ||--o{ LIST_BOOK : incluye
  BOOK ||--o{ LIST_BOOK : pertenece_a

  USER {
    int id PK
    text name
    text email UK
    timestamptz created_at
  }
  BOOK {
    int id PK
    int user_id FK
    text title
    text author
    text genre
    int total_pages
    text shelf_status
    timestamptz created_at
  }
  READING_PROGRESS {
    int id PK
    int book_id FK
    int current_page
    timestamptz updated_at
  }
  REVIEW {
    int id PK
    int book_id FK
    int user_id FK
    int rating
    text comment
    timestamptz created_at
  }
  READING_LIST {
    int id PK
    int user_id FK
    text name
    text description
    timestamptz created_at
  }
  LIST_BOOK {
    int id PK
    int list_id FK
    int book_id FK
    int position
  }

## 9. Reto técnico distintivo

Mantener la interfaz fluida y sin bloqueos al manejar 10.000 registros de libros y reseñas, junto con la consistencia entre el progreso de lectura (current_page vs total_pages) y el estado en la estantería. Para resolverlo, aplicaré paginación/consultas filtradas en el servidor con índices en PostgreSQL sobre columnas de búsqueda y claves foráneas, evitando cargar miles de elementos sin paginar en el DOM y validando reglas de dominio tanto en la interfaz como en las restricciones de base de datos. (Si la guía o el catálogo te asignó un reto específico con otro nombre, cámbialo aquí por ese enunciado exacto).

## 10. Riesgos y preguntas abiertas

1.Rendimiento con 10.000 registros: ¿Qué volumen de datos genera exactamente el script de npm run seed de la plantilla y cómo se comportan las vistas de listado y búsqueda bajo ese volumen?.

2.Estructura base de la plantilla: ¿Qué framework web, ORM o estructura inicial trae plantilla-proyecto-lenguaje para acoplar las carpetas en src/ sin romper los scripts existentes (migrate, seed, dev, backlog)?.

3.Reglas de negocio en reseñas: ¿Un mismo usuario puede publicar más de una reseña sobre el mismo libro o solo se permite una reseña activa por libro y usuario?.

4.Alcance de autenticación en el MVP: ¿El entorno de desarrollo usa un usuario por defecto sembrado en el seed o requiere flujo completo de login/registro desde el Sprint 0?.
