# Birra N Pizza

Web de ejemplo de una pizzería de masa madre y cerveza artesana, desarrollada con **HTML, CSS y JavaScript nativos**, sin frameworks ni herramientas de compilación.

Es un proyecto formativo: sirve como modelo para el alumnado de un curso de creación y publicación de páginas web. Por eso este README documenta no solo **qué** hay en el proyecto, sino **por qué** se tomó cada decisión.

> **Estado:** en desarrollo. La maquetación para móvil está casi terminada. Faltan el footer, las versiones para tablet y escritorio, el JavaScript del menú y las páginas secundarias.

---

## Índice

- [Birra N Pizza](#birra-n-pizza)
  - [Índice](#índice)
  - [Estructura del proyecto](#estructura-del-proyecto)
  - [Convenciones](#convenciones)
    - [Nombres de archivos y carpetas](#nombres-de-archivos-y-carpetas)
    - [Clases y variables CSS](#clases-y-variables-css)
    - [Rutas](#rutas)
  - [Arquitectura CSS](#arquitectura-css)
    - [Organización de `styles.css`](#organización-de-stylescss)
  - [Metodología de clases: `@scope`](#metodología-de-clases-scope)
    - [Por qué no BEM](#por-qué-no-bem)
    - [Cómo funciona](#cómo-funciona)
    - [Reglas que se han aprendido por el camino](#reglas-que-se-han-aprendido-por-el-camino)
  - [Reset (`reset.css`)](#reset-resetcss)
  - [Tokens de diseño (`tokens.css`)](#tokens-de-diseño-tokenscss)
    - [Criterios](#criterios)
    - [Correcciones sobre el diseño original](#correcciones-sobre-el-diseño-original)
  - [Tipografías (`fonts.css`)](#tipografías-fontscss)
  - [Iconos](#iconos)
  - [HTML semántico](#html-semántico)
  - [Accesibilidad](#accesibilidad)
  - [Cabecera del documento (`<head>`) y SEO](#cabecera-del-documento-head-y-seo)
  - [Política de compatibilidad](#política-de-compatibilidad)
  - [Entorno de desarrollo](#entorno-de-desarrollo)
  - [Registro de decisiones](#registro-de-decisiones)
  - [Cómo se ha desarrollado](#cómo-se-ha-desarrollado)
  - [Pendiente](#pendiente)
  - [Créditos](#créditos)

---

## Estructura del proyecto

```
mi-web/
├── index.html
├── contacto.html
├── servicios.html
├── css/
│   ├── reset.css
│   ├── tokens.css
│   ├── fonts.css
│   └── styles.css
├── js/
│   └── main.js
├── assets/
│   ├── img/
│   ├── icons/
│   │   ├── iconos.svg          ← sprite con todos los iconos
│   │   ├── favicon.ico
│   │   ├── favicon.svg
│   │   ├── favicon-96x96.png
│   │   ├── apple-touch-icon.png
│   │   └── site.webmanifest
│   ├── fonts/
│   │   ├── oswald.woff2
│   │   ├── source-sans-3.woff2
│   │   └── source-sans-3-italic.woff2
│   └── media/
└── README.md
```

El proyecto se organiza según la función de cada archivo:

- **Raíz (`*.html`)**: las páginas. `index.html` es la página principal, la que el servidor muestra por defecto.
- **`css/`**: el código de presentación.
- **`js/`**: el código de comportamiento.
- **`assets/`**: los recursos que usa el código pero que no se editan como código (imágenes, iconos, tipografías, vídeo y audio).
- **`README.md`**: la documentación.

La separación entre estructura (HTML), presentación (CSS), comportamiento (JS) y recursos reproduce de forma simplificada la organización profesional: con herramientas como Vite, estas mismas carpetas pasan a estar dentro de `src/`.

**Por qué esta estructura y no otra:** se descartó una estructura totalmente plana (`css/`, `js/`, `img/` sueltas) por no parecerse a la de un entorno real, y también la estructura completa de Vite (`src/`, `public/`, `dist/`, `package.json`) por añadir complejidad antes de tiempo. Esta es un punto intermedio.

---

## Convenciones

### Nombres de archivos y carpetas

Minúsculas, sin espacios, sin tildes ni ñ, y con guiones como separador (`sobre-nosotros.html`, `logo-hotel.png`). Los servidores Linux distinguen entre mayúsculas y minúsculas: `Logo.png` y `logo.png` son archivos distintos, y es el error más habitual al publicar.

### Clases y variables CSS

**En español**, con la misma regla: sin tildes ni ñ (`.boton`, `--tamano-titulo-xl`). Se tomó esta decisión porque parte del alumnado tiene un nivel de inglés bajo, y los nombres en inglés añadían una dificultad que no tenía que ver con lo que se quiere enseñar. Las propiedades de CSS (`color`, `padding`) siguen en inglés, porque son parte del lenguaje.

Se mantienen sin traducir dos cosas: las tallas (`xs`, `sm`, `md`, `lg`, `xl`), que son internacionales, y `hover`, que es el nombre de la pseudoclase `:hover`.

La tilde **sí** se usa en el texto que lee el usuario («Alérgenos», «Encuéntranos»). La regla afecta solo a los nombres técnicos.

### Rutas

Todas las rutas son **relativas** (`css/styles.css`, `../assets/fonts/oswald.woff2`). Una ruta que empieza por `/` apunta a la raíz del dominio: falla al abrir el HTML desde el disco (`file://`) y si la web se publica en una subcarpeta.

---

## Arquitectura CSS

El CSS está dividido en cuatro archivos, cada uno con una responsabilidad:

| Archivo | Función |
|---|---|
| `css/reset.css` | Iguala los estilos por defecto de los navegadores |
| `css/tokens.css` | Guarda los valores del sistema de diseño (variables) |
| `css/fonts.css` | Declara las tipografías (`@font-face`) |
| `css/styles.css` | Aplica los tokens a la página |

Se cargan con varios `<link>` en el `<head>`, y no con `@import`, porque así el navegador descarga los archivos a la vez:

```html
<link rel="stylesheet" href="css/reset.css">
<link rel="stylesheet" href="css/tokens.css">
<link rel="stylesheet" href="css/fonts.css">
<link rel="stylesheet" href="css/styles.css">
```

### Organización de `styles.css`

1. **Base**: estilos de etiquetas (`body`, encabezados).
2. **Componentes globales**: piezas que se usan en toda la web (`.boton` y sus variantes).
3. **Secciones y componentes con `@scope`**: cabecera, portada, información, pizzas, tarjeta, cervezas, filosofía, bloque, ambiente…
4. **Utilidades**: clases de uso general (`.contenedor`, `.solo-lectores`).

---

## Metodología de clases: `@scope`

### Por qué no BEM

BEM (`.tarjeta__titulo--destacado`) nació cuando CSS no tenía forma de limitar un estilo a una parte de la página: el aislamiento se conseguía con nombres largos y únicos. Hoy CSS tiene `@scope`, que resuelve ese problema de forma nativa y permite **nombres cortos que se pueden repetir** en distintos componentes sin chocar.

BEM se sigue enseñando porque aparece en muchos proyectos existentes y hay que saber leerlo, pero este proyecto no lo usa.

### Cómo funciona

```css
@scope (.cabecera) to (.menu > *) {
  :scope { /* el propio <header class="cabecera"> */ }
  .logo { /* solo el logo de la cabecera */ }
  .acciones { /* no choca con el .acciones de la portada */ }
}
```

- **Raíz** (`.cabecera`): donde empieza el ámbito. Las reglas de dentro solo afectan a elementos que estén dentro de ella.
- **`:scope`**: la propia raíz, sin repetir su nombre.
- **Límite** (`to`, opcional): donde termina el ámbito. Se usa cuando dentro hay otro componente con su propio `@scope`.
- **`to (.x)` frente a `to (.x > *)`**: el límite es exclusivo. Con `to (.menu)`, el `.menu` queda fuera y no se puede colocar. Con `to (.menu > *)`, el `.menu` queda dentro (se puede colocar), pero su contenido queda fuera. Es el llamado «ámbito en donut».

### Reglas que se han aprendido por el camino

- **La raíz de un `@scope` es un selector como cualquier otro.** Si dos componentes distintos se llaman `.tarjeta`, el `@scope (.tarjeta)` se aplica a los dos. Por eso las tarjetas de la sección de información se llaman `.ficha`, y los apartados de la filosofía, `.bloque`. **Los nombres de las raíces deben ser únicos; los de dentro pueden repetirse.**
- **Proximidad:** una regla dentro de un `@scope` gana a una regla global aunque la global aparezca más tarde en el archivo. Si dos ámbitos se contradicen, gana el más cercano en el HTML.
- **No aumenta la especificidad:** `.logo` dentro de `@scope (.cabecera)` pesa lo mismo que una clase suelta, (0,1,0). En cambio, `.cabecera .logo` pesa (0,2,0).
- **Los ámbitos no se anidan:** cada componente tiene su propio bloque `@scope` al mismo nivel, para que el comportamiento sea predecible.
- **El anidamiento nativo** (`&:hover`, `@media` dentro de una regla) se usa para estados y media queries, no para simular jerarquías de componentes.

---

## Reset (`reset.css`)

Cada navegador aplica sus propios estilos por defecto. El reset corrige esas diferencias para que todos partan de la misma base. Es un reset moderno, basado en los de Josh Comeau y Andy Bell: corto, centrado en lo que realmente falla y con mejoras de accesibilidad. Normalize.css se descartó porque no se actualiza desde 2018.

- **Capa de cascada (`@layer reset`)**: las reglas de `styles.css`, que no están en ninguna capa, siempre ganan al reset, sin importar la especificidad.
- **Modelo de caja**: `box-sizing: border-box` en todos los elementos.
- **Márgenes a cero**: todo el espaciado sale de los tokens.
- **Imágenes y multimedia**: se comportan como bloques, no se salen de su contenedor y mantienen la proporción.
- **Formularios**: heredan la tipografía de la página.
- **Texto**: titulares equilibrados (`text-wrap: balance`), párrafos sin palabras sueltas en la última línea (`pretty`) y partición de palabras muy largas.
- **Listas**: solo pierden las viñetas si llevan `role="list"`, porque Safari deja de anunciar como lista las listas sin viñetas.
- **Enlaces sin clase**: toman el color del texto y mantienen el subrayado. Los enlaces con clase son componentes y deciden su propio aspecto.
- **Accesibilidad**: `scroll-margin` para que la navegación fija no tape las anclas y respeto a la preferencia de movimiento reducido.

El reset no contiene diseño: ni colores, ni fuentes de marca, ni tamaños.

---

## Tokens de diseño (`tokens.css`)

Los tokens son las decisiones del sistema de diseño guardadas como variables CSS en `:root`. Si un valor cambia, se cambia en este archivo y se actualiza en toda la web.

| Categoría | Ejemplos |
|---|---|
| Colores | `--color-fondo`, `--color-texto`, `--color-primario`, `--color-accion` |
| Tipografía | `--fuente-titulos`, `--peso-negrita`, `--tamano-titulo-xl`, `--interlineado-texto-md` |
| Espaciado | `--espacio-2xs` a `--espacio-2xl` |
| Maquetación | `--ancho-maximo`, `--margen-pagina`, `--separacion-columnas` |
| Bordes y radios | `--borde-base`, `--radio-sm`, `--radio-completo` |
| Profundidad | `--sombra-base`, `--sombra-flotante`, `--capa-navegacion` |
| Interacción | `--transicion-base`, `--foco-anillo` |

### Criterios

- **Nombres por función, no por aspecto:** `--color-primario` y no `--rojo-tomate`. Formato: `--categoría-rol-variante`.
- **Ritmo vertical:** el espaciado usa múltiplos de 8 px y los interlineados, múltiplos de 4 px. Los interlineados están en `rem` y no sin unidades, como sería habitual, porque cada uno va unido a un tamaño concreto y hacen falta valores exactos para cuadrar la rejilla.
- **Todo en `rem`:** si una persona aumenta el tamaño de letra en su navegador, toda la web escala con ella, rejilla incluida.
- **Mobile first:** los valores base son los de móvil, y los `@media` redefinen algunas variables para pantallas mayores. No hacen falta tokens duplicados como `-movil`.
- **Puntos de corte:** móvil < 640 px · tablet 640–1023 px · escritorio ≥ 1024 px. Se documentan en un comentario porque las variables CSS no se pueden usar dentro de una media query.

### Correcciones sobre el diseño original

El sistema de diseño se generó con Stitch. Se corrigieron varios puntos:

- **Contraste del texto:** el color de texto original (`#8D706C`) solo alcanzaba 4,2:1, por debajo del 4,5:1 que exige WCAG AA. Se sustituyó por `#2A1614`.
- **Rojo para texto pequeño:** el rojo tomate de la marca no cumple con texto pequeño, así que se añadió `--color-primario-intenso`.
- **Ritmo vertical:** las alturas de línea originales (1.1, 1.15…) daban valores con decimales. Se ajustaron a la rejilla de 4 px.
- **Titulares en móvil:** `h2` y `h3` quedaban idénticos en móvil. Se redujo `--tamano-titulo-md`.
- **Documentación y maqueta no coincidían:** la descripción decía que las tarjetas no llevaban sombra y la maqueta sí. Se creó `--sombra-base` para los elementos que se apoyan sobre la página, diferenciada de `--sombra-flotante`, reservada para los que quedan por encima del contenido.
- **Fondo alterno:** el sistema no lo definía; se tomó el valor de la maqueta.

---

## Tipografías (`fonts.css`)

| Familia | Uso | Pesos |
|---|---|---|
| Oswald | Titulares, etiquetas y botones | 200–700 |
| Source Sans 3 | Texto de lectura (normal y cursiva) | 200–900 |

- **Alojadas en el proyecto**, no enlazadas desde Google Fonts: al cargarlas desde Google, el navegador de cada visitante envía su IP sin consentimiento, lo que plantea problemas con el RGPD.
- **Solo `woff2`:** todos los navegadores actuales lo soportan. El respaldo, si la fuente no carga, es la pila de `font-family` definida en los tokens, no otros formatos de archivo.
- **Fuentes variables:** un solo archivo por familia contiene todos los pesos. El `@font-face` declara un **rango** (`font-weight: 200 700`); con un valor único, el navegador generaría negritas falsas.
- **Subconjunto `Latin`** (incluye tildes, ñ, ¿ y ¡) más los caracteres de la carta: `€“”‘’«»–—…·•°ºª×→½`.
- **Generadas con Transfonter**, con *Family support* y *Fix vertical metrics* activadas y `font-display: swap`.

---

## Iconos

- **Origen:** SVG copiados de [Lucide](https://lucide.dev), sin instalar ninguna librería. Encaja con el sistema de diseño (trazo de 2 px, extremos redondeados, caja de 24 × 24).
- **Por qué no Font Awesome:** evita una dependencia externa y las fuentes de iconos son una técnica antigua (cargan cientos de iconos para usar unos pocos, fallan si la fuente no carga y algunos lectores leen el carácter).
- **Sprite (`assets/icons/iconos.svg`):** todos los iconos en un archivo, cada uno como `<symbol>`. En el HTML se usan con `<use>`:

```html
<svg aria-hidden="true" width="24" height="24">
  <use href="assets/icons/iconos.svg#telefono"></use>
</svg>
```

- **Color:** los atributos de trazo están en un `<g>` dentro de cada símbolo, con `stroke="currentColor"`. El icono toma el color de texto del elemento que lo contiene.
- **⚠️ Necesita servidor local:** si se abre el HTML con doble clic (`file://`), Chrome bloquea el `<use>` que apunta a otro archivo y los iconos no se ven. Hay que usar Live Server.

---

## HTML semántico

- **`<a>` para ir a algún sitio, `<button>` para hacer algo en la página.** «Reservar» y «Ver la carta» son enlaces aunque parezcan botones. El menú hamburguesa es un botón.
- **Un solo `h1` por página**, en la portada. El logo no es un encabezado.
- **`hgroup`** agrupa el título con su antetítulo y su subtítulo. La especificación actual admite `<p>` antes y después del encabezado. La versión antigua, con varios `h`, se eliminó en 2022.
- **Toda sección tiene encabezado.** Si el diseño no lo muestra (sección de información), se añade un `h2` con la clase `.solo-lectores`.
- **Listas para elementos repetidos:** datos de la portada, alérgenos y cervezas son `ul > li` con `role="list"`.
- **`article`** solo para contenido que tiene sentido por sí mismo (tarjetas de pizza, bloques de filosofía). Las filas de cerveza no lo son: dependen del contexto de la sección.
- **`header` dentro de `article`** para el nombre y el precio de cada pizza. Es la cabecera del artículo, no de la web.
- **Orden del documento frente a orden visual:** la imagen de la tarjeta va al final del HTML y se muestra arriba con `order: -1`. Así, el lector de pantalla anuncia primero el nombre. Solo se hace con elementos que no reciben el foco.
- **Enlaces que lanzan acciones:** los teléfonos son enlaces `tel:`, y la dirección, un enlace a un servicio de mapas. Se descartó incrustar el mapa con `<iframe>` porque cargaría contenido de Google sin consentimiento.

---

## Accesibilidad

- **Contraste:** cada color de `tokens.css` indica en su comentario si cumple WCAG AA para texto. El rojo tomate solo se usa en texto grande, bordes e iconos.
- **Tamaño de letra:** 16 px para leer y escribir; menos solo para etiquetas cortas (mínimo 12 px). WCAG no fija un mínimo, pero Safari en iOS hace zoom en los campos de formulario de menos de 16 px.
- **Texto solo para lectores (`.solo-lectores`):** para botones que solo muestran un icono. No se usa `display: none`, que también lo oculta al lector de pantalla.
- **Iconos decorativos** con `aria-hidden="true"`.
- **Foco visible:** `:focus-visible` con el anillo de la marca. Nunca `outline: none` sin otro indicador.
- **Área táctil mínima** de 44 × 44 px en los botones de icono.
- **Menú:** el botón usa `aria-expanded` y `aria-controls`. El mismo atributo controla el CSS con `:has()`.
- **Subrayado:** los enlaces del texto lo mantienen. En la sección de información se eliminó en el teléfono por coherencia visual con el horario y la dirección; es una decisión consciente, compensada con el anillo de foco.
- **Movimiento reducido:** se respeta la preferencia del sistema operativo.

---

## Cabecera del documento (`<head>`) y SEO

- **`lang="es"`** en `<html>`: indica a los lectores de pantalla el idioma.
- **`<title>`**: fórmula *qué + dónde | marca*, sin pasar de unos 60 caracteres.
- **`<meta name="description">`**: el texto bajo el título en los resultados de búsqueda (150–160 caracteres).
- **`<meta name="keywords">`** no se usa: Google la ignora desde 2009.
- **Iconos:** generados con RealFaviconGenerator.
- **Manifiesto web (`site.webmanifest`):** archivo JSON que usa Android al «Añadir a pantalla de inicio» (nombre bajo el icono, iconos grandes, colores). Los colores se escriben a mano, porque un JSON no puede leer variables CSS. Las rutas se resuelven desde la ubicación del propio manifiesto.

---

## Política de compatibilidad

La web está pensada para navegadores con soporte para **`@scope`**, que es la característica más reciente del proyecto:

| Navegador | Versión mínima |
|---|---|
| Chrome / Edge | 118 |
| Safari (macOS e iOS) | 17.4 |
| Firefox | 146 (diciembre de 2025) |

Con Firefox 146, `@scope` pasó a estar disponible en todos los navegadores principales. En navegadores anteriores, el contenido sigue siendo accesible, pero sin los estilos de los componentes.

También se usan `@layer`, variables CSS, anidamiento nativo, `:has()` y `aspect-ratio`, que tienen requisitos inferiores.

---

## Entorno de desarrollo

| Herramienta | Para qué |
|---|---|
| VS Code | Editor |
| Live Server (extensión) | Servidor local; imprescindible para el sprite de iconos |
| CSS Variable Autocomplete (extensión, de Vu Nguyen) | Sugiere los tokens de `tokens.css` al escribir en otros archivos. Sin ella, VS Code solo sugiere variables del mismo archivo |
| Emmet: *Wrap with Abbreviation* | Envolver una selección en una etiqueta (desde la paleta de comandos) |
| DevTools del navegador | Inspeccionar flex y grid (etiqueta *flex*/*grid* en el panel de Elementos), navegar con Tab para comprobar el foco |
| Stitch | Generación del sistema de diseño y la maqueta de referencia |
| Transfonter | Conversión de fuentes a `woff2` y generación de `@font-face` |
| RealFaviconGenerator | Favicon y manifiesto |

---

## Registro de decisiones

Problemas que surgieron durante el desarrollo y cómo se resolvieron:

| Problema | Decisión |
|---|---|
| Las clases en inglés añadían dificultad al alumnado | Clases y tokens en español |
| BEM generaba nombres largos y repetición | `@scope` con nombres cortos |
| Las tarjetas de información heredaban los estilos de las tarjetas de pizza | Raíces de `@scope` con nombres únicos (`.ficha`) |
| Los encabezados de filosofía chocaban con los de sus bloques | `@scope (.filosofia) to (.bloque > *)` y un `@scope (.bloque)` aparte |
| El rojo de la marca no daba contraste suficiente con texto blanco | `--color-primario-intenso` para texto pequeño y fondos de botón |
| La altura de los botones exigía 12 px de padding, fuera de la escala | `min-height: 2.5rem` y centrado con flex, sin padding vertical |
| Centrar una letra dentro de un cuadrado | Grid con `place-items: center`, tamaño fijo y `aspect-ratio: 1` |
| El icono no cambiaba de color | `stroke="currentColor"` en el sprite |
| `space-between` no repartía el espacio | El contenedor padre limitaba el ancho de la lista |
| ¿Soporte para navegadores antiguos (`woff`, `ttf`)? | No: el CSS ya requiere navegadores modernos. Se documenta la política de compatibilidad |
| ¿Mapa de Google o alternativa europea? | Enlace (no mapa incrustado). Al ser un local ficticio, no se profundizó |

---

## Cómo se ha desarrollado

El proyecto se ha desarrollado con **Claude** (Anthropic) como asistente de revisión, en un trabajo parecido a programar en pareja:

- **Escrito por mí:** el HTML de todas las secciones y la mayor parte de `styles.css`. Las decisiones de estructura, metodología, nombres y diseño.
- **Generado con ayuda y revisado:** `reset.css` (a partir de las reglas que ya usaba), `tokens.css` (a partir del sistema de diseño de Stitch, con las correcciones descritas arriba) y los estilos iniciales de la cabecera y la sección de filosofía.
- **Revisión:** la IA señalaba problemas en forma de preguntas y yo los resolvía, salvo cuando pedía la solución directamente. Varias propuestas se rechazaron o se adaptaron (la estructura de carpetas, el subrayado del teléfono, la jerarquía de encabezados de filosofía).

Se documenta porque usar una IA de forma responsable no significa ocultarlo, sino **revisar, preguntar y entender el código que se incorpora**.

---

## Pendiente

**Estructura y contenido**

- [ ] Footer.
- [ ] Secciones de opiniones, reservas (formulario) y contacto.
- [ ] Páginas secundarias: el menú enlaza a `carta.html`, `cervezas.html` y `reservas.html`, que aún no existen. «Ver todas las pizzas» tiene el `href` vacío.
- [ ] Imágenes reales con su `alt` y sus atributos `width` y `height`.
- [ ] Revisar textos: tildes, espacios sin salto (`&nbsp;`) en precios, separadores (`·`), símbolo de grados (`°`).

**CSS**

- [ ] `.contenedor` como utilidad global: quitar las definiciones repetidas de cada `@scope` y dejar solo lo que añade cada sección. Revisar el margen lateral duplicado de la cabecera.
- [ ] `.boton-principal` y `.boton-secundario` como componentes globales (ahora están repetidos en la cabecera y la portada).
- [ ] Antetítulos: valorar un componente global.
- [ ] Bordes entre las filas de cervezas: una sola línea entre filas.
- [ ] Rojo tomate en texto pequeño (antetítulos, botón secundario) → `--color-primario-intenso`.
- [ ] Versiones para tablet y escritorio (grid, retícula de 12 columnas en escritorio).

**Tokens**

- [ ] Arquitectura en dos niveles: **primitivos** (`--ambar-100`, `--rojo-700`) y **semánticos** que apuntan a ellos. Ahora hay nombres como `--color-etiqueta-ambar-*`, que describen el aspecto y no la función.
- [ ] Decidir el color de acción (azul del sistema o rojo intenso) y crear `--color-sobre-primario` si hace falta.

**Otros**

- [ ] JavaScript del menú hamburguesa (`aria-expanded`).
- [ ] `site.webmanifest`: nombre, colores y rutas de iconos (aún tiene los valores de ejemplo).
- [ ] Open Graph: etiquetas `og:` para compartir en redes (herramientas: opengraph.xyz para previsualizar; Sharing Debugger de Facebook y Post Inspector de LinkedIn una vez publicada).
- [ ] Precarga de Oswald: `<link rel="preload">`.
- [ ] Comprobar que Oswald se ve distinta a 400 y a 700 (confirma que la fuente sigue siendo variable).

---

## Créditos

- **Iconos:** [Lucide](https://lucide.dev), licencia ISC.
- **Tipografías:** [Oswald](https://fonts.google.com/specimen/Oswald) y [Source Sans 3](https://fonts.google.com/specimen/Source+Sans+3), licencia SIL Open Font License.
- **Reset:** basado en los resets de Josh Comeau y Andy Bell.
- **Sistema de diseño y maqueta de referencia:** generados con Stitch.