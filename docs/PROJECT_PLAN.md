# Plan Del Proyecto Kadari ART

## Estado Del Plan

La planificacion tecnica y funcional esta finalizada y aprobada. La implementacion no ha comenzado. Las decisiones marcadas como provisionales deben mantenerse simples hasta que exista una necesidad concreta de cambiarlas.

## Vision

Kadari ART sera el portafolio profesional de un artista y disenador. La experiencia publica debe sentirse como una galeria de arte contemporanea y una revista editorial: artistica, minimalista, abstracta, profesional y visualmente memorable.

La identidad utiliza azul oscuro, crema y magenta. El magenta es un acento y no debe dominar. Las obras son las protagonistas. La primera version sera solamente en espanol y debera funcionar en escritorio y celular.

## Arquitectura Aprobada

El sitio sera estatico y se generara durante cada despliegue.

```text
Kadari
   |
   v
Sanity Studio
   |
   v
Content Lake + Assets
   |
   | publicar o despublicar
   v
Webhook de Sanity
   |
   v
Build de Vercel
   |
   v
Eleventy + Nunjucks
   |
   v
HTML estatico en Vercel
```

Responsabilidades:

| Componente | Responsabilidad |
|---|---|
| Eleventy | Construir las paginas estaticas. |
| Nunjucks | Reutilizar plantillas HTML e insertar contenido. |
| HTML semantico | Estructura accesible del sitio publico. |
| CSS | Identidad visual, composicion y responsive. |
| JavaScript nativo | Menu, filtros, visor y mejoras progresivas. |
| Sanity Studio | Crear, editar, archivar y publicar contenido. |
| Sanity Content Lake | Guardar contenido estructurado. |
| Sanity Assets | Guardar y entregar imagenes y GIF mediante CDN. |
| Vercel | Construir, alojar y publicar el sitio. |
| GitHub | Conservar el codigo y su historial. |
| Funcion de contacto | Validar el formulario y enviar correo sin almacenar mensajes. |

No se usaran React, TypeScript, Vite, SPA, Tailwind, una base de datos propia ni autenticacion propia para el sitio publico.

## Eleventy Y Nunjucks

### Eleventy

Eleventy es una herramienta de construccion. Toma plantillas, datos y recursos y genera el sitio final en HTML. Se ejecuta localmente y en Vercel, no en el navegador del visitante.

Eleventy permite:

- Crear automaticamente una pagina por proyecto.
- Generar URLs limpias como `/proyectos/nombre-de-la-obra/`.
- Incluir SEO y metadatos sociales antes de cargar JavaScript.
- Reutilizar layouts sin duplicar HTML.
- Calcular el proyecto anterior y siguiente.
- Dejar de generar la ruta de una obra despublicada.

### Nunjucks

Nunjucks es el lenguaje de plantillas usado por Eleventy. Permite insertar datos de Sanity en HTML y reutilizar cabecera, pie, tarjetas, metadatos y layouts. El resultado entregado al visitante es HTML normal.

## Sanity Studio

Sanity Studio sera una aplicacion administrativa externa, alojada inicialmente por Sanity. Kadari sera la unica administradora en la primera version y usara el sistema de acceso de Sanity. Se recomendara MFA si la cuenta lo permite.

La configuracion y los esquemas se escribiran en JavaScript. No se crearan componentes React personalizados ni se agregaran plugins innecesarios en la primera version.

Sanity administrara contenido, no estructura visual. Los borradores no apareceran en las APIs publicas sin autenticacion. El dataset inicial sera publico para permanecer dentro del plan gratuito; por ello, todo documento publicado debe contener solamente informacion apta para ser publica.

### Estados Editoriales

| Estado | Comportamiento |
|---|---|
| Borrador | Visible solamente en Sanity Studio. |
| Publicado | Incluido en el siguiente build publico. |
| Despublicado | Excluido del siguiente build; su ruta devuelve 404. |
| Archivado | Despublicado y conservado para recuperacion. |
| Eliminado | Borrado definitivo despues de una confirmacion clara. |

Para archivar una obra se despublicara, se marcara como archivada y se conservaran sus imagenes. Sanity impedira publicar accidentalmente un documento archivado. La vista previa de borradores queda reservada para el futuro.

## Vercel Y GitHub

GitHub conservara el codigo y el historial. Vercel estara conectado al repositorio y ejecutara el build de Eleventy cuando haya cambios de codigo o cuando Sanity active un Deploy Hook.

El build consultara exclusivamente la perspectiva publicada de Sanity y evitara usar una respuesta de contenido obsoleta. Las imagenes si se serviran desde el CDN de Sanity. Si Sanity no responde, el build debe fallar en lugar de publicar una galeria vacia; Vercel conservara el ultimo despliegue valido.

El dominio inicial sera el proporcionado por Vercel. El dominio personalizado se conectara posteriormente.

No se haran commits, pushes ni despliegues sin autorizacion. Cuando existan, `package-lock.json` y `studio/package-lock.json` se guardaran en Git. `node_modules`, `_site`, `.env` y caches se ignoraran, pero `.gitignore` no se creara hasta comenzar la preparacion tecnica.

## Inicio

Inicio sera la entrada visual a una exposicion digital, no una pagina larga con toda la informacion.

Contenido principal:

- Identidad o logotipo de Kadari.
- Composicion artistica, contemporanea y profesional.
- Una obra integrada en la composicion.
- Boton para Galeria.
- Boton para Sobre Kadari.
- Boton para Servicios.
- Boton para Contacto.

No incluira inicialmente galerias extensas, proyectos seleccionados, biografia completa, lista de servicios ni formulario de contacto.

`homePage.heroProject` elegira la obra usada en la composicion. La plantilla y el CSS decidiran como presentarla. El logotipo sera un recurso versionado en Git y no sera libremente editable desde Sanity.

## Galeria

La galeria mostrara obras publicadas ordenadas por `galleryPosition`. Cada imagen representa una obra diferente y abre su propio archivo artistico.

Patron principal de escritorio:

```text
+----------------------+--------------+
|                      | Obra pequena |
|     Obra grande      +--------------+
|                      | Obra pequena |
+----------------------+--------------+
```

El patron se repetira con variaciones controladas, como alternar la obra grande a la izquierda o derecha. Las variaciones seran definidas por las plantillas y Figma, no por campos libres del CMS.

En celular la galeria se convertira en una composicion vertical clara, conservara el orden editorial y evitara diferencias extremas de tamano.

El filtro por categoria funcionara en `/galeria/`. JavaScript recalculara los roles grande y pequeno de las obras visibles para evitar huecos. Sin JavaScript todas las obras seguiran visibles. No habra paginas independientes por categoria en la primera version.

## Archivo Artistico

Cada proyecto funcionara como archivo artistico o estudio de caso e incluira:

1. Titulo, categoria principal y ano.
2. Resultado final.
3. Descripcion.
4. Concepto.
5. Desarrollo.
6. Bocetos.
7. Pruebas y variaciones.
8. Etapas del proceso creativo.
9. Herramientas utilizadas.
10. Galeria ampliable.
11. Proyecto anterior y siguiente.

El orden visual exacto se definira en Figma. Anterior y siguiente se calcularan durante el build a partir de `galleryPosition`; no se editaran manualmente.

## Mapa De Rutas

| Ruta | Funcion |
|---|---|
| `/` | Entrada artistica y navegacion principal. |
| `/galeria/` | Composicion de obras y filtros. |
| `/proyectos/{slug}/` | Archivo artistico de cada proyecto publicado. |
| `/sobre-kadari/` | Biografia y presentacion profesional. |
| `/servicios/` | Servicios y comisiones. |
| `/contacto/` | Formulario, correo y redes sociales. |
| `/gracias/` | Confirmacion provisional del formulario. |
| `/404.html` | Pagina o proyecto no disponible. |
| `/sitemap.xml` | URLs publicas indexables. |
| `/robots.txt` | Reglas de indexacion. |
| `/api/contact` | Envio seguro del formulario. |

Sanity Studio se alojara inicialmente en una direccion separada, por ejemplo `kadari-art.sanity.studio`, y no dentro de `/admin/`.

## Modelo De Contenido

### Documentos

| Documento | Responsabilidad |
|---|---|
| `project` | Obra y estudio de caso. |
| `category` | Categoria principal y filtro. |
| `homePage` | Contenido minimo de Inicio y obra principal. |
| `aboutPage` | Biografia. |
| `servicesPage` | Servicios y comisiones. |
| `contactPage` | Textos de Contacto. |
| `siteSettings` | SEO general, correo y redes sociales. |

Las paginas globales seran documentos unicos para evitar duplicados.

### Proyecto

| Campo | Uso |
|---|---|
| `title` | Nombre obligatorio de la obra. |
| `slug` | URL obligatoria y unica. |
| `year` | Ano del proyecto. |
| `mainCategory` | Referencia fuerte a la categoria principal. |
| `tags` | Etiquetas adicionales sin duplicados. |
| `shortDescription` | Tarjetas, introduccion y SEO alternativo. |
| `coverImage` | Imagen de la galeria. |
| `finalImage` | Resultado final principal. |
| `description` | Descripcion general enriquecida. |
| `concept` | Concepto enriquecido. |
| `development` | Explicacion del desarrollo. |
| `processStages` | Etapas ordenadas del proceso. |
| `tools` | Herramientas utilizadas. |
| `galleryPosition` | Orden general numerico. |
| `isFeatured` | Marca editorial de obra destacada. |
| `featuredPosition` | Orden de destacadas reservado para un uso futuro aprobado. |
| `editorialState` | Activo o archivado. |
| `seo` | Metadatos especificos opcionales. |

`isFeatured` y `featuredPosition` se conservaran en el modelo, pero no generaran una seccion de proyectos seleccionados en Inicio durante la primera version.

### Etapas Del Proceso

Cada `processStage` tendra titulo, descripcion enriquecida y una lista ordenada de medios. Cada medio tendra imagen o GIF, tipo, texto alternativo obligatorio y pie opcional.

Tipos iniciales de medio:

- Boceto.
- Prueba.
- Variacion.
- Detalle.
- Proceso.
- Otro.

El video se podra incorporar posteriormente como un nuevo tipo de medio.

### Categoria

| Campo | Uso |
|---|---|
| `name` | Nombre visible obligatorio. |
| `slug` | Identificador unico. |
| `description` | Explicacion opcional. |
| `position` | Orden numerico de filtros. |
| `showInFilters` | Mostrar u ocultar el filtro. |

Las referencias desde proyectos seran fuertes. No se eliminara una categoria utilizada sin resolver sus referencias. Provisionalmente, ocultar una categoria elimina su filtro, pero no oculta sus obras de la galeria general.

### Inicio

| Campo | Uso |
|---|---|
| `mainText` | Texto principal corto si Figma lo requiere. |
| `heroProject` | Obra integrada en la composicion. |
| `galleryButtonLabel` | Etiqueta del acceso a Galeria. |
| `aboutButtonLabel` | Etiqueta del acceso a Sobre Kadari. |
| `servicesButtonLabel` | Etiqueta del acceso a Servicios. |
| `contactButtonLabel` | Etiqueta del acceso a Contacto. |
| `seo` | Metadatos de Inicio. |

### Paginas Y Ajustes

`aboutPage` gestionara titulo, biografia enriquecida, retrato, imagenes adicionales y SEO.

`servicesPage` gestionara titulo, introduccion, lista ordenada de servicios, texto de cierre y SEO. Cada servicio tendra titulo, descripcion y llamada a la accion opcional.

`contactPage` gestionara titulo, introduccion y textos que acompanan al formulario.

`siteSettings` gestionara nombre del sitio, SEO general, imagen social general, correo publico, WhatsApp opcional y enlaces sociales.

La direccion receptora del formulario sera una variable segura de Vercel y no un campo editorial publico.

### Texto Enriquecido

Portable Text permitira:

- Titulos `h2` y `h3`.
- Parrafos.
- Listas ordenadas y no ordenadas.
- Enlaces.
- Citas.
- Negrita y cursiva.
- Imagenes con texto alternativo y pie.

No permitira `h1`, colores, tamanos, alineaciones arbitrarias, CSS, HTML libre, tablas ni cambios de estructura visual.

## Campos Editables

Kadari podra editar:

- Obras y proyectos.
- Imagenes finales.
- Bocetos e imagenes del proceso.
- Titulos, categorias, etiquetas, anos y descripciones.
- Concepto y desarrollo.
- Herramientas.
- Posicion general y posicion destacada.
- Obra principal de Inicio.
- Textos cortos de Inicio.
- Biografia.
- Servicios.
- Informacion de contacto.
- Redes sociales.
- SEO editorial.

## Control Visual

Kadari no podra editar desde Sanity:

- Colores.
- Tipografias.
- Tamanos.
- Margenes o espaciado.
- Columnas.
- Distribucion.
- Clases CSS.
- HTML libre.
- Animaciones.
- Diseno de tarjetas.
- Rol grande o pequeno de una obra en la galeria.

El diseno se definira en Figma y se implementara en plantillas y CSS.

## Imagenes Y GIF

Las obras se almacenaran en Sanity, no en Git. El repositorio guardara logo, iconos y recursos propios de la interfaz.

Las imagenes usaran dimensiones explicitas, formatos y tamanos responsivos, carga diferida cuando corresponda y el CDN de Sanity. La imagen principal visible al cargar tendra prioridad. Cada uso de imagen tendra texto alternativo contextual.

Los GIF se admitiran. Cuando deban conservar animacion se evitaran transformaciones que los conviertan en imagenes estaticas. Los videos quedan para una fase futura.

## Contacto

Contacto combinara formulario, correo y redes sociales. WhatsApp se mostrara solamente si Kadari publica un numero profesional.

El formulario enviara mensajes al correo de Kadari sin almacenarlos en una base de datos. Una funcion pequena de Vercel validara la solicitud y usara un proveedor de correo externo. Los secretos permaneceran en variables de entorno. La proteccion inicial incluira validacion del servidor y honeypot; CAPTCHA se agregara solo si resulta necesario.

## Seguridad

- No guardar secretos, contrasenas, tokens ni URLs secretas en Git.
- No exponer tokens de escritura de Sanity en el sitio publico.
- Tratar el Deploy Hook de Vercel como secreto.
- Guardar credenciales de correo en variables de entorno de Vercel.
- Usar la autenticacion de Sanity y recomendar MFA.
- Publicar solamente informacion apta para ser publica.
- Consultar solo contenido publicado durante el build.
- Fallar el build si la fuente de contenido no responde.
- Solicitar confirmacion antes de eliminaciones definitivas.
- Preservar el historial Git y los cambios locales del usuario.

## SEO

Cada proyecto tendra una URL limpia, titulo, descripcion e imagen social propios. El build generara:

- Elemento `title`.
- Meta description.
- URL canonica.
- Open Graph.
- Metadatos de Twitter.
- Imagen social.
- Sitemap.
- Robots.
- Idioma `es`.

Los campos SEO especificos seran opcionales y usaran como respaldo el titulo, la descripcion breve, la portada y los ajustes generales. Las obras despublicadas o archivadas no apareceran en el sitemap.

## Estructura De Carpetas

Estructura prevista para la implementacion:

```text
kadari-art-portfolio/
|-- README.md
|-- AGENTS.md
|-- package.json
|-- package-lock.json
|-- eleventy.config.js
|-- .gitignore
|-- .env.example
|-- docs/
|   |-- PROJECT_PLAN.md
|   `-- STATUS.md
|-- src/
|   |-- _data/
|   |-- _includes/
|   |   |-- layouts/
|   |   `-- components/
|   |-- assets/
|   |   |-- css/
|   |   |-- js/
|   |   |-- images/
|   |   `-- fonts/
|   |-- galeria/
|   |-- proyectos/
|   |-- sobre-kadari/
|   |-- servicios/
|   |-- contacto/
|   |-- index.njk
|   |-- 404.njk
|   |-- sitemap.njk
|   `-- robots.njk
|-- lib/
|   |-- sanity.js
|   |-- queries.js
|   |-- images.js
|   `-- portable-text.js
|-- api/
|   `-- contact.js
|-- studio/
|   |-- package.json
|   |-- package-lock.json
|   |-- sanity.config.js
|   |-- sanity.cli.js
|   |-- structure.js
|   `-- schemaTypes/
|       |-- documents/
|       |-- objects/
|       `-- index.js
|-- .opencode/
|   `-- agents/
|       `-- kadari.md
`-- _site/
```

`src`, `lib` y `api` pertenecen al sitio publico. `studio` pertenece a Sanity Studio. `_site`, `node_modules` y caches se generan automaticamente y no se versionan. Los archivos de plantillas, estilos, scripts, consultas, esquemas y configuracion se escriben o revisan directamente.

## Viaje De Una Obra

1. Kadari inicia sesion en Sanity Studio.
2. Crea un proyecto y completa su contenido.
3. Sube portada, resultado final, bocetos y variaciones.
4. Sanity guarda textos estructurados y activos de imagen.
5. Mientras sea borrador, el proyecto no aparece publicamente.
6. Kadari publica el proyecto.
7. Sanity activa el Deploy Hook de Vercel.
8. Vercel ejecuta Eleventy.
9. Eleventy consulta los documentos publicados.
10. Nunjucks inserta cada proyecto en las plantillas.
11. Eleventy genera la ruta limpia y actualiza la galeria.
12. Vercel publica el nuevo HTML.
13. El visitante recibe HTML estatico e imagenes desde el CDN de Sanity.

Al despublicar, otro build deja de generar la pagina y la galeria deja de incluirla. La ruta devuelve 404 despues del despliegue.

## Etapas De Implementacion

### Etapa 1: Preparacion Tecnica

- Preservar y documentar el estado Git.
- Crear la estructura tecnica base.
- Configurar Node, npm, Eleventy y Nunjucks.
- Crear scripts de desarrollo y build.
- Crear `.gitignore` y `.env.example`.
- Generar una pagina minima.
- Conectar GitHub con Vercel.
- Verificar un primer despliegue autorizado.

Resultado: sitio minimo construible y desplegable.

### Etapa 2: Configuracion De Sanity

- Crear proyecto y dataset publico.
- Configurar Sanity Studio en JavaScript.
- Configurar la cuenta de Kadari.
- Crear documentos, objetos y validaciones.
- Limitar Portable Text.
- Configurar documentos unicos y referencias fuertes.
- Crear proyectos de prueba.
- Verificar imagenes y GIF.

Resultado: administracion de contenido sin control del diseno.

### Etapa 3: Construccion Del Sitio Publico

- Conectar Eleventy con contenido publicado.
- Crear layouts y componentes semanticos.
- Construir Inicio como entrada visual.
- Construir Galeria con grupos de tres y filtros.
- Construir los archivos artisticos.
- Crear Sobre Kadari, Servicios y Contacto.
- Generar anterior y siguiente.
- Implementar SEO, 404, sitemap y robots.
- Implementar imagenes responsivas y visor accesible.

Resultado: sitio funcional con diseno provisional.

### Etapa 4: Implementacion Del Diseno De Figma

- Incorporar tipografias aprobadas.
- Convertir la paleta en variables CSS.
- Implementar reticula, espaciado y responsive.
- Definir la composicion final de Inicio.
- Refinar variaciones de Galeria.
- Disenar el archivo artistico.
- Implementar estados interactivos y movimiento accesible.
- Verificar que las obras sean protagonistas.

Resultado: sitio visualmente fiel a Figma.

### Etapa 5: Funciones Administrativas Y Contacto

- Probar crear, editar, publicar y despublicar.
- Probar archivo, recuperacion y eliminacion confirmada.
- Probar categorias, referencias y posiciones numericas.
- Configurar webhook de Sanity y Deploy Hook de Vercel.
- Implementar la funcion de contacto.
- Elegir proveedor de correo y proteccion antispam.
- Documentar el uso de Studio para Kadari.

Resultado: gestion autonoma y formulario operativo.

### Etapa 6: Pruebas Y Publicacion

- Cargar entre 6 y 12 proyectos.
- Revisar textos alternativos y pies.
- Probar escritorio, tablet y celular.
- Probar teclado y accesibilidad basica.
- Probar filtros, visor, publicacion, despublicacion y 404.
- Comprobar metadatos sociales, rendimiento y seguridad.
- Verificar que no haya secretos en Git.
- Revisar limites gratuitos.
- Publicar con el dominio de Vercel.
- Conectar el dominio personalizado posteriormente.

Resultado: primera version publica, segura y mantenible.

## Decisiones Aprobadas

- Eleventy y Nunjucks para generar el sitio publico.
- HTML semantico, CSS y JavaScript nativo en el navegador.
- Sanity Studio externo para administrar contenido.
- GitHub para codigo y Vercel para publicacion.
- Primera version solamente en espanol.
- Uso inicial de planes gratuitos.
- Inicio breve como entrada artistica.
- Galeria de una obra grande y dos pequenas.
- Archivo artistico individual para cada proyecto.
- Categoria principal y varias etiquetas por obra.
- Orden general numerico y filtro por categoria.
- Una obra principal en Inicio mediante `heroProject`.
- Varias obras pueden conservar una marca editorial destacada.
- Imagenes y GIF en la primera version; video posterior.
- Texto enriquecido limitado y sin control visual.
- URLs limpias y metadatos sociales por proyecto.
- Formulario enviado por correo sin guardar mensajes.
- Despublicar y archivar antes de eliminar definitivamente.
- Sin autenticacion ni base de datos propias y sin un backend completo; solo la funcion minima de contacto.

## Decisiones Pendientes No Bloqueantes

| Decision | Valor provisional |
|---|---|
| Uso publico de destacadas secundarias | Se guardan, pero no aparecen en Inicio. |
| Categoria oculta | Oculta el filtro, no las obras. |
| Espera al despublicar | Se acepta el tiempo del nuevo despliegue. |
| Proveedor de correo | Elegir una opcion con plan gratuito. |
| Proteccion antispam | Validacion y honeypot; CAPTCHA solo si hace falta. |
| Ruta `/gracias/` | Se conserva provisionalmente. |
| Formato del ano | Un unico ano inicialmente. |
| Posiciones repetidas | Permitir y desempatar de forma estable. |
| Cambio de slug publicado | Desaconsejado; redireccion manual si ocurre. |
| Analitica | No agregar sin aprobacion. |
| Pagina de privacidad | Decidir segun formulario, analitica y jurisdiccion. |
| WhatsApp | Oculto hasta disponer de numero profesional. |
| Tipografias y licencias | Pendientes de Figma. |
| Reticula, espaciado y breakpoints | Pendientes de Figma. |
| Variaciones exactas de Galeria | Pendientes de Figma. |
| Obras y recursos finales | Pendientes de seleccion y optimizacion. |
| Dominio y fecha de lanzamiento | Pendientes. |

## Funciones Futuras

- Vista previa de borradores dentro del diseno real.
- Videos y streaming.
- Reordenacion mediante arrastrar y soltar.
- Colaboradores y roles administrativos adicionales.
- Version en ingles.
- Paginas independientes para categorias o etiquetas.
- Uso publico de proyectos destacados secundarios.
- Automatizacion adicional de archivo y recuperacion.
