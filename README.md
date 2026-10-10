# BiblioFácil — Sistema de consulta de biblioteca

Proyecto académico de **Línea de Énfasis I · Programación y Desarrollo de Software**. Esta entrega integra la Fase 1, la práctica de contrato REST, la práctica de maquetación y los lineamientos de la Fase 2 con una interfaz funcional basada en datos simulados.

## 1. Propuesta

### Nombre del sistema
**BiblioFácil**.

### Problema que resuelve
Los estudiantes necesitan localizar rápidamente material bibliográfico sin recorrer manualmente todo el catálogo. BiblioFácil organiza los libros por categoría, permite buscarlos por título o autor y muestra su disponibilidad.

### Usuarios
- **Estudiante:** consulta, busca y filtra libros.
- **Bibliotecario/administrador:** gestiona el catálogo y la disponibilidad.
- **Sistema:** valida, filtra y presenta la información.

### Alcance
**SÍ hace en esta iteración**
1. Lista libros y muestra título, autor, categoría, ISBN y disponibilidad.
2. Busca y filtra en vivo por texto, categoría y disponibilidad.
3. Permite agregar un libro mediante formulario validado y lo muestra sin recargar la página.

**NO hace todavía**
1. No persiste información en una base de datos real; usa datos simulados en memoria.
2. No implementa autenticación ni roles reales.
3. No ejecuta préstamos reales ni notificaciones; esos recursos quedan definidos en el contrato para fases posteriores.

## 2. Backlog — historias de usuario

### HU-01 — Consultar libros por categoría
**Como** estudiante **quiero** consultar los libros por categoría **para** encontrar rápido el material que necesito.

**Criterios de aceptación**
- **Dado** que existen libros en “Bases de datos”, **cuando** selecciono esa categoría, **entonces** veo solo esos libros y su disponibilidad.
- **Dado** que una categoría no tiene coincidencias, **cuando** la selecciono, **entonces** veo “No hay libros en esta categoría o con los filtros seleccionados”.

### HU-02 — Buscar libros
**Como** estudiante **quiero** buscar por título o autor **para** localizar un libro sin revisar todo el catálogo.

**Criterios de aceptación**
- Dado un texto parcial válido, cuando escribo en el buscador, entonces la lista se actualiza en vivo.
- Si no hay coincidencias, se muestra el estado de lista vacía.

### HU-03 — Consultar disponibilidad
**Como** estudiante **quiero** conocer la disponibilidad de cada libro **para** saber si puedo solicitarlo.

**Criterios de aceptación**
- Cada tarjeta muestra “Disponible” o “Prestado”.
- El usuario puede filtrar por disponibilidad.

### HU-04 — Consultar datos del libro
**Como** estudiante **quiero** ver los datos principales de un libro **para** identificar correctamente el material.

**Criterios de aceptación**
- Cada tarjeta presenta título, autor, categoría e ISBN.
- La tarjeta presenta un botón de acción “Ver detalles”.

### HU-05 — Registrar un libro
**Como** bibliotecario **quiero** registrar un libro **para** incorporarlo al catálogo.

**Criterios de aceptación**
- El formulario exige título, autor, ISBN de 13 dígitos y categoría.
- Al guardar datos válidos, el libro aparece en el listado sin recargar la página.

### HU-06 — Filtrar resultados
**Como** estudiante **quiero** combinar filtros **para** reducir los resultados a lo que realmente necesito.

**Criterios de aceptación**
- Categoría, disponibilidad y búsqueda pueden actuar simultáneamente.
- “Limpiar filtros” restaura el catálogo.

### HU-07 — Usar la aplicación desde distintos dispositivos
**Como** estudiante **quiero** consultar el catálogo desde móvil, tableta o computador **para** usarlo desde el dispositivo disponible.

**Criterios de aceptación**
- La interfaz se adapta a 375 px, 768 px y 1280 px.
- No se pierde ninguna función esencial por el cambio de tamaño.

### HU-08 — Recibir retroalimentación del sistema
**Como** usuario **quiero** ver estados de carga, vacío y error **para** entender qué está ocurriendo.

**Criterios de aceptación**
- Antes de recibir los datos aparece “Cargando catálogo...”.
- Si no hay resultados aparece un mensaje de lista vacía.
- Si falla el acceso a datos aparece un mensaje de error comprensible.

## 3. Contrato de API REST

La especificación detallada está en [`contrato-api.txt`](contrato-api.txt). La API se versiona desde `/api/v1` y utiliza recursos en plural.

### Resumen
| Verbo | Ruta | Respuesta |
|---|---|---|
| GET | `/api/v1/libros` | 200 |
| GET | `/api/v1/libros/{id}` | 200 / 404 |
| POST | `/api/v1/libros` | 201 / 400 |
| PUT | `/api/v1/libros/{id}` | 200 / 400 / 404 |
| DELETE | `/api/v1/libros/{id}` | 204 / 404 |
| GET | `/api/v1/categorias` | 200 |
| GET | `/api/v1/categorias/{id}/libros` | 200 / 404 |
| POST | `/api/v1/prestamos` | 201 / 400 / 409 |
| GET | `/api/v1/usuarios/{id}/prestamos` | 200 / 404 |

**Filtro por query string:**
`GET /api/v1/libros?categoria=Bases%20de%20datos&disponible=true&page=0&size=20`

**Recurso anidado:**
`GET /api/v1/usuarios/{id}/prestamos`

### Ejemplo JSON — crear libro
```json
{
  "titulo": "Clean Code",
  "autor": "Robert C. Martin",
  "isbn": "9780132350884",
  "categoriaId": 3,
  "disponible": true
}
```

## 4. Arquitectura de la Fase 2

```text
index.html          Estructura semántica
css/estilos.css     Presentación, Grid/Flexbox y responsive
js/datos.js         Datos simulados con forma similar al JSON futuro
js/api.js           Única capa de acceso a datos
js/ui.js            Renderizado del DOM
js/app.js           Eventos, filtros y orquestación
docs/               Bosquejos de pantallas
```

El frontend no accede directamente a `datos.js`: `app.js` solicita información mediante `api.js`. Esto permite sustituir posteriormente la simulación por `fetch()` contra Spring Boot sin rediseñar toda la interfaz.

## 5. Práctica — contrato de API

Recursos principales elegidos: **libros, categorías y préstamos**. Para cada uno se definieron operaciones de consulta, creación, actualización y eliminación, códigos HTTP, ejemplos JSON, filtro mediante query string y relaciones anidadas. Véase `contrato-api.txt`.

## 6. Práctica — pantalla principal

La pantalla utiliza HTML semántico (`header`, `nav`, `aside`, `main`, `section`, `article`, `footer`), variables CSS, Grid para el layout, Flexbox dentro de bloques y tarjetas adaptables. El catálogo se genera desde JavaScript, no está escrito manualmente en HTML.

## 7. Fase 2 — interfaz funcional con datos simulados

- [x] Estructura exigida del repositorio.
- [x] Listado generado desde JavaScript.
- [x] Formulario con validación HTML.
- [x] Alta de un registro sin recargar.
- [x] Búsqueda en vivo mediante `filter`.
- [x] Filtro por categoría y disponibilidad.
- [x] Diseño responsive.
- [x] Estado de carga.
- [x] Estado de lista vacía.
- [x] Manejo de error.
- [x] Acceso a datos centralizado en `api.js`.
- [x] Datos simulados mediante promesas.

## 8. Ejecución

Por utilizar módulos ES6, se recomienda servir la carpeta con **Live Server** en VS Code. Abra `index.html` mediante Live Server. No requiere `npm install` ni backend en esta fase.

## 9. Bosquejos

Los bosquejos se encuentran en `docs/`:
- `bosquejo-principal.png`
- `bosquejo-detalle.png`
- `bosquejo-administracion.png`

## 10. Commits sugeridos

Realice los commits de manera progresiva; no suba todo como un único commit:

```text
docs: define propuesta backlog y contrato de API
feat: maqueta la pantalla principal del catalogo
feat: agrega datos simulados y listado de libros
feat: agrega busqueda y filtros del catalogo
feat: agrega formulario para registrar libros
style: adapta la interfaz a movil tableta y escritorio
docs: documenta la fase 2 del proyecto
```

## 11. Próxima fase

El contrato REST queda preparado para implementar posteriormente el backend con Java y Spring Boot. En ese momento `api.js` podrá sustituir las promesas simuladas por llamadas `fetch()` a `/api/v1/...`.
