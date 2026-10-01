# Birra N Pizza

Web de ejemplo de una pizzería de masa madre y cerveza artesana, desarrollada con **HTML, CSS y JavaScript nativos**, sin frameworks ni herramientas de compilación.

Es un proyecto formativo: sirve como modelo para el alumnado de un curso de creación y publicación de páginas web. Por eso este README documenta no solo **qué** hay en el proyecto, sino **por qué** se tomó cada decisión.

> **Estado:** en desarrollo. La maquetación para móvil está prácticamente terminada y el sistema de tokens en dos capas, cerrado. Faltan la refactorización de los componentes globales, las versiones para tablet y escritorio, el JavaScript del menú y las páginas secundarias.

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
  - [Sistema de tokens](#sistema-de-tokens)
    - [Por qué dos capas](#por-qué-dos-capas)
    - [Primitivos (`primitivos.css`)](#primitivos-primitivoscss)
    - [Semánticos (`semanticos.css`)](#semánticos-semanticoscss)
    - [Criterios](#criterios)
    - [Correcciones sobre el diseño original](#correcciones-sobre-el-diseño-original)
    - [Qué se descartó](#qué-se-descartó)
    - [Control de calidad](#control-de-calidad)
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
│   ├── fonts.css
│   ├── primitivos.css
│   ├── semanticos.css
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

El CSS está dividido en cinco archivos, cada uno con una responsabilidad:

| Archivo | Función |
|---|---|
| `css/reset.css` | Iguala los estilos por defecto de los navegadores |
| `css/fonts.css` | Declara las tipografías (`@font-face`) |
| `css/primitivos.css` | Capa 1 de los tokens: los valores en bruto (rampas de color, medidas, tamaños de letra) |
| `css/semanticos.css` | Capa 2 de los tokens: la función de cada valor (`--color-primario`, `--espacio-md`) |
| `css/styles.css` | Aplica los tokens semánticos a la página |

Se cargan con varios `<link>` en el `<head>`, y no con `@import`, porque así el navegador descarga los archivos a la vez. Con `@import`, el navegador no descubre el segundo archivo hasta haber descargado el primero y se cargan en cadena.

```html
<link rel="stylesheet" href="css/reset.css">
<link rel="stylesheet" href="css/fonts.css">
<link rel="stylesheet" href="css/primitivos.css">
<link rel="stylesheet" href="css/semanticos.css">
<link rel="stylesheet" href="css/styles.css">
```

**El orden importa:** los primitivos antes que los semánticos, y estos antes que `styles.css`, porque cada capa lee a la anterior. Además, a igualdad de especificidad la cascada se resuelve por orden.

**Sobre el rendimiento:** cada hoja de estilos es una petición más, pero con HTTP/2 (el que sirve cualquier hosting actual) las peticiones viajan en paralelo por una misma conexión, y para archivos pequeños la diferencia es de milisegundos. En un proyecto real, una herramienta de compilación (Vite, Lightning CSS…) uniría y minificaría estos archivos al publicar. Aquí se mantienen separados por claridad y porque el proyecto no usa compilación.

### Organización de `styles.css`

1. **Base**: estilos de etiquetas (`body`, encabezados).
2. **Componentes globales**: piezas que se usan en toda la web (`.boton` y sus variantes).
3. **Secciones y componentes con `@scope`**: cabecera, portada, información, pizzas, tarjeta, cervezas, filosofía, bloque, ambiente, pie, opiniones, contacto, reservas…
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

## Sistema de tokens

Los tokens son las decisiones del sistema de diseño guardadas como variables CSS en `:root`. Si un valor cambia, se cambia en un único sitio y se actualiza en toda la web.

Están organizados en **dos capas**: primitivos y semánticos. Antes de esta organización existía un único `tokens.css`, con nombres como `--color-etiqueta-ambar-*` que describían el aspecto y no la función.

### Por qué dos capas

```
primitivos.css  →  semanticos.css  →  styles.css
(el valor)         (la función)        (la aplicación)
```

| Capa | Responde a | Ejemplo |
|---|---|---|
| Primitivos | ¿Qué valores existen? | `--rojo-60: #da2119;` |
| Semánticos | ¿Para qué sirve cada uno? | `--color-primario: var(--rojo-60);` |
| `styles.css` | ¿Dónde se usa? | `.boton { background: var(--color-accion); }` |

Ventajas:

- **Cambiar la marca es cambiar una línea.** `--color-primario: var(--rojo-60)` pasa a `var(--verde-60)` y toda la web se adapta, sin tocar un solo componente.
- **Un modo oscuro o una segunda marca** solo reescriben `semanticos.css`.
- **Los cambios por pantalla** se hacen en los semánticos, apuntando a otro primitivo.
- **`styles.css` solo lee semánticos.** Nunca un primitivo.

### Primitivos (`primitivos.css`)

Es la «caja de materiales». Se nombran por su **valor** (`rojo-60`, `medida-16`, `letra-18`) y no por su uso. Por eso no llevan el prefijo `color-` ni `espacio-`: así, cuando se escribe `--color-` o `--espacio-`, el autocompletado solo ofrece tokens semánticos y es difícil usar un primitivo por error.

**Colores.** Cuatro rampas de 10 peldaños (del 10, el más claro, al 100, el más oscuro) y el blanco:

| Rampa | Para qué sirve |
|---|---|
| `neutro` (cálido) | Fondos, textos y bordes |
| `rojo` | Marca, acción y avisos |
| `verde` | Confirmaciones y estados positivos |
| `ambar` | Etiquetas y detalles destacados |

**Regla de contraste.** Las rampas están calibradas para que el contraste con blanco dependa **solo del peldaño** y no del color:

| Peldaño | Contraste con blanco | Uso |
|---|---|---|
| 10 – 40 | < 3:1 | Solo fondos y decoración |
| 50 | ≥ 3:1 | Iconos, bordes de control, texto grande |
| 60 | ≥ 4,5:1 | Texto normal y botones con texto blanco (AA) |
| 70 | ≥ 5,7:1 | Texto normal con margen |
| 80 – 100 | ≥ 11:1 | Texto sobre fondos claros (AAA) |

Así, cambiar `rojo-60` por `verde-60` nunca rompe la accesibilidad.

**Medidas.** Una sola escala, múltiplos de 4 px, con pasos más largos al crecer: `0, 4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 56, 64, 80, 96, 112, 128`. El número del nombre son los píxeles de referencia, y el valor va en `rem` (`--medida-16: 1rem`), para que escale con la configuración de la persona usuaria. La usan el espaciado, los radios, el tamaño táctil mínimo (44) y las alturas de línea, que quedan así dentro de la misma rejilla de 4 px.

**Letra.** Escala propia, porque los tamaños de texto no encajan en la rejilla de 4 px: `12, 14, 16, 18, 20, 24, 28, 36, 40, 56`. Son solo los que se usan.

**Referencia: IBM Carbon.** La estructura de las rampas (10 peldaños con contraste calibrado por peldaño) sigue el modelo de la paleta de IBM Carbon. Los valores no son los de Carbon: se anclaron a los colores de la marca y se recalcularon.

**Cómo se construyeron.**

- Rojo, verde y neutro: generados con Huetone a partir de las anclas de la marca (`#bc0003` en el peldaño 70 del rojo, `#1c1917` en el 100 del neutro) y los objetivos de contraste de Carbon.
- Ámbar: calculado por script con los mismos objetivos de contraste, a partir de los dos colores que ya usaban las etiquetas (`#FEF3C7` y `#92400E`).

### Semánticos (`semanticos.css`)

Dan función a los primitivos. Es la única capa que lee `styles.css`.

| Categoría | Ejemplos |
|---|---|
| Colores | `--color-fondo`, `--color-texto`, `--color-primario`, `--color-accion`, `--color-error`, `--color-exito`, `--color-etiqueta-*` |
| Zonas oscuras | `--color-fondo-invertido`, `--color-texto-invertido`, `--color-borde-invertido` |
| Tipografía | `--fuente-titulos`, `--peso-negrita`, `--tamano-titulo-xl`, `--interlineado-texto-md` |
| Espaciado | `--espacio-2xs` a `--espacio-2xl`, `--tamano-tactil-minimo` |
| Maquetación | `--ancho-maximo`, `--margen-pagina`, `--separacion-columnas` |
| Bordes y radios | `--borde-base`, `--borde-grosor-acento`, `--radio-sm`, `--radio-completo` |
| Profundidad | `--sombra-base`, `--sombra-flotante`, `--capa-navegacion` |
| Interacción | `--transicion-base`, `--foco-anillo` |

**Cambios por pantalla.** Los `@media` redefinen semánticos para que apunten a **otro primitivo**, sin tocar los componentes:

```css
:root { --margen-pagina: var(--medida-16); }

@media (min-width: 640px) {
  :root { --margen-pagina: var(--medida-32); }
}
```

### Criterios

- **Nombres por función, no por aspecto:** `--color-primario` y no `--color-rojo`. Formato: `--categoría-rol-variante`. El prefijo `sobre-` indica el color del contenido que va encima de otro (`--color-sobre-accion`).
- **Ritmo vertical:** el espaciado usa múltiplos de 8 px (con 4 px como medio paso) y los interlineados, múltiplos de 4 px. Los interlineados son medidas fijas y no valores sin unidades, como sería habitual, porque cada uno va unido a un tamaño concreto y hacen falta valores exactos para cuadrar la rejilla.
- **Todo en `rem`:** si una persona aumenta el tamaño de letra en su navegador, toda la web escala con ella, rejilla incluida.
- **Mobile first:** los valores base son los de móvil, y los `@media` redefinen algunas variables para pantallas mayores. No hacen falta tokens duplicados como `-movil`.
- **Puntos de corte:** móvil < 640 px · tablet 640–1023 px · escritorio ≥ 1024 px. Se documentan en un comentario porque las variables CSS no se pueden usar dentro de una media query.
- **Derivados con `color-mix()`:** cuando un valor no tiene peldaño propio (el fondo alterno, entre `neutro-10` y `neutro-20`) o es una transparencia (las sombras), se mezcla en la capa semántica en lugar de crear un hexadecimal nuevo.

### Correcciones sobre el diseño original

El sistema de diseño se generó con Stitch. Se corrigieron varios puntos:

- **Contraste del texto:** el color de texto original (`#8D706C`) solo alcanzaba 4,2:1, por debajo del 4,5:1 que exige WCAG AA. Se sustituyó por `neutro-90`, que da 13,8:1 sobre el fondo.
- **El rojo tomate no pasaba AA:** el rojo de la marca (`#EA1D14`) da 4,48:1 con texto blanco, por 0,02 por debajo del mínimo. La marca pasa a `rojo-60` (`#da2119`), visualmente casi idéntico, con 5,0:1. El antiguo `--color-primario-intenso` sigue existiendo (`rojo-70`) para enlaces y texto pequeño.
- **Color de acción:** el sistema original usaba un azul para el botón principal. Con una marca roja competía con la identidad, así que el botón pasa a ser rojo. Se eliminó la familia azul de los primitivos.
- **Ritmo vertical:** las alturas de línea originales (1.1, 1.15…) daban valores con decimales. Se ajustaron a la rejilla de 4 px.
- **Titulares en móvil:** `h2` y `h3` quedaban idénticos en móvil. Se redujo `--tamano-titulo-md`.
- **Documentación y maqueta no coincidían:** la descripción decía que las tarjetas no llevaban sombra y la maqueta sí. Se creó `--sombra-base` para los elementos que se apoyan sobre la página, diferenciada de `--sombra-flotante`, reservada para los que quedan por encima del contenido.
- **Fondo alterno:** el sistema no lo definía; se tomó el valor de la maqueta y se reconstruyó con `color-mix()` sobre los neutros.
- **Color secundario (ladrillo):** ningún componente lo usaba, se eliminó.
- **Neutros más rosados:** la rampa de neutro cálido es algo más rosada que el crema original (`#F9F6F0`). Es una diferencia sutil y se aceptó para mantener la coherencia de la rampa.
- **Error y marca son casi el mismo rojo:** un mensaje de error se parece a un botón. Por eso el error **siempre** va acompañado de un icono o un texto explícito, nunca solo de color (que además es lo que pide WCAG).

### Qué se descartó

- **Una tercera capa de tokens por componente** (`--boton-fondo: var(--color-accion)`): con dos capas basta para una web de este tamaño. Sería útil con muchos componentes o con varias personas trabajando a la vez.
- **Un kit genérico completo** (11 familias de color, escalas de espaciado de 4, 16 y 28): se probó con una paleta de ejercicio, pero en el CSS solo entran las familias que el proyecto usa. El resto queda como kit de origen, no en el código publicado.
- **Primitivos para pesos, radios, sombras y z-index:** son pocos valores y cada uno tiene un único uso. Los radios reutilizan las medidas.
- **Generar la paleta con armonías de color** (complementario, tríada…): la marca ya estaba elegida. Solo habría servido para el verde, que se tomó de la referencia.
- **Otras paletas de referencia valoradas:** Tailwind (muy conocida, pero sin calibrar por contraste), Radix, Material, Primer, Ant Design, Evergreen, USWDS, Chakra, Stripe y Semrush. Se eligió IBM Carbon por su calibración de contraste por peldaño.

### Control de calidad

`styles.css` no debe usar nunca un primitivo. Esta búsqueda debe salir vacía:

```bash
git grep -nE -- "--(rojo|verde|neutro|ambar)-[0-9]|--medida-|--letra-|--blanco" -- css/styles.css
```

---

## Tipografías (`fonts.css`)

| Familia | Uso | Pesos |
|---|---|---|
| Oswald | Titulares, etiquetas y botones | 200–700 |
| Source Sans 3 | Texto de lectura (normal y cursiva) | 200–900 |

- **Alojadas en el proyecto**, no enlazadas desde Google Fonts: al cargarlas desde Google, el navegador de cada visitante envía su IP sin consentimiento, lo que plantea problemas con el RGPD.
- **Solo `woff2`:** todos los navegadores actuales lo soportan. El respaldo, si la fuente no carga, es la pila de `font-family` definida en los tokens semánticos, no otros formatos de archivo.
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

- **Contraste:** las rampas de color están calibradas por peldaño (el 60 cumple siempre WCAG AA con texto blanco), y cada color semántico indica en su comentario el contraste medido. `--color-primario` sobre el fondo da 4,52:1, justo en el límite: para texto pequeño y enlaces se usa `--color-primario-intenso` (6,1:1). `--color-neutro` (3,1:1) solo sirve para bordes e iconos, nunca para texto.
- **No depender solo del color:** los errores de formulario llevan icono o texto, porque el color de error es casi el de la marca.
- **Tamaño de letra:** 16 px para leer y escribir; menos solo para etiquetas cortas (mínimo 12 px). WCAG no fija un mínimo, pero Safari en iOS hace zoom en los campos de formulario de menos de 16 px.
- **Texto solo para lectores (`.solo-lectores`):** para botones que solo muestran un icono. No se usa `display: none`, que también lo oculta al lector de pantalla.
- **Iconos decorativos** con `aria-hidden="true"`.
- **Foco visible:** `:focus-visible` con el anillo de la marca (`--foco-anillo`), con contraste suficiente tanto sobre el fondo claro como sobre el pie oscuro. Nunca `outline: none` sin otro indicador.
- **Área táctil mínima** de 44 × 44 px (`--tamano-tactil-minimo`) en botones y enlaces pulsables.
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

También se usan `@layer`, variables CSS, anidamiento nativo, `:has()`, `aspect-ratio` y `color-mix()`, que tienen requisitos inferiores o equivalentes.

---

## Entorno de desarrollo

| Herramienta | Para qué |
|---|---|
| VS Code | Editor |
| Live Server (extensión) | Servidor local; imprescindible para el sprite de iconos |
| CSS Variable Autocomplete (extensión, de Vu Nguyen) | Sugiere los tokens de `primitivos.css` y `semanticos.css` al escribir en otros archivos. Sin ella, VS Code solo sugiere variables del mismo archivo |
| Emmet: *Wrap with Abbreviation* | Envolver una selección en una etiqueta (desde la paleta de comandos) |
| DevTools del navegador | Inspeccionar flex y grid (etiqueta *flex*/*grid* en el panel de Elementos), navegar con Tab para comprobar el foco |
| Stitch | Generación del sistema de diseño y la maqueta de referencia |
| Huetone | Generación y comprobación de rampas de color por contraste |
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
| El rojo de la marca no daba contraste suficiente con texto blanco | `rojo-60` (`#da2119`, 5,0:1) para marca y botón, `rojo-70` para enlaces y texto pequeño |
| La altura de los botones exigía 12 px de padding, fuera de la escala | `min-height` y centrado con flex, sin padding vertical |
| Centrar una letra dentro de un cuadrado | Grid con `place-items: center`, tamaño fijo y `aspect-ratio: 1` |
| El icono no cambiaba de color | `stroke="currentColor"` en el sprite |
| `space-between` no repartía el espacio | El contenedor padre limitaba el ancho de la lista |
| ¿Soporte para navegadores antiguos (`woff`, `ttf`)? | No: el CSS ya requiere navegadores modernos. Se documenta la política de compatibilidad |
| ¿Mapa de Google o alternativa europea? | Enlace (no mapa incrustado). Al ser un local ficticio, no se profundizó |
| ¿Primitivos solo con lo que se usa o escalas completas? | Escalas completas de 10 peldaños, pero solo de las familias que usa el proyecto |
| ¿Qué referencia seguir para las rampas de color? | IBM Carbon: contraste calibrado por peldaño. Se anclaron a los colores de la marca |
| ¿Botón de acción azul o rojo? | Rojo: una sola marca y una familia de color menos |
| El mismo nombre para primitivos y semánticos mezclaba las sugerencias del autocompletado | Primitivos por valor y sin prefijo (`rojo-60`, `medida-16`); semánticos por función (`color-primario`, `espacio-md`) |
| ¿Una tercera capa de tokens por componente? | No: con dos capas basta |
| El fondo alterno no tenía peldaño en la rampa | `color-mix()` entre `neutro-10` y `neutro-20` |
| ¿Un archivo CSS más para los componentes globales? | Se valoró el coste en rendimiento (despreciable con HTTP/2) y se decidió por orden y claridad. Pendiente de la refactorización |

---

## Cómo se ha desarrollado

El proyecto se ha desarrollado con **Claude** (Anthropic) como asistente de revisión, en un trabajo parecido a programar en pareja:

- **Escrito por mí:** el HTML de todas las secciones y la mayor parte de `styles.css`. Las decisiones de estructura, metodología, nombres y diseño, y las rampas de rojo, verde y neutro, generadas en Huetone.
- **Generado con ayuda y revisado:** `reset.css` (a partir de las reglas que ya usaba), `primitivos.css` y `semanticos.css` (a partir del sistema de diseño de Stitch y de las rampas de Huetone; la rampa de ámbar la calculó la IA con script, con los mismos objetivos de contraste), y los estilos iniciales de la cabecera y la sección de filosofía.
- **Revisión:** la IA señalaba problemas en forma de preguntas y yo los resolvía, salvo cuando pedía la solución directamente. Varias propuestas se rechazaron o se adaptaron (la estructura de carpetas, el subrayado del teléfono, la jerarquía de encabezados de filosofía, la organización de los tokens).

Se documenta porque usar una IA de forma responsable no significa ocultarlo, sino **revisar, preguntar y entender el código que se incorpora**.

---

## Pendiente

**Estructura y contenido**

- [ ] Páginas secundarias: el menú enlaza a `carta.html`, `cervezas.html` y `reservas.html`, que aún no existen. «Ver todas las pizzas» tiene el `href` vacío.
- [ ] Imágenes reales con su `alt` y sus atributos `width` y `height`.
- [ ] Revisar textos: tildes, espacios sin salto (`&nbsp;`) en precios, separadores (`·`), símbolo de grados (`°`).

**CSS**

- [ ] `.contenedor` como utilidad global: quitar las definiciones repetidas de cada `@scope` y dejar solo lo que añade cada sección. Revisar el margen lateral duplicado de la cabecera.
- [ ] `.boton-principal` y `.boton-secundario` como componentes globales (ahora están repetidos en la cabecera y la portada). Decidir si van en un `componentes.css` propio.
- [ ] Antetítulos y etiquetas: valorar un componente global.
- [ ] Bordes entre las filas de cervezas: una sola línea entre filas.
- [ ] Rojo de la marca en texto pequeño (antetítulos, botón secundario) → `--color-primario-intenso`.
- [ ] `small { font-size: inherit; }` también en el pie (el formulario ya lo tiene): el navegador reduce `<small>` por su cuenta.
- [ ] Comprobar que el logo del pie se ve sobre el fondo oscuro.
- [ ] Versiones para tablet y escritorio (grid, retícula de 12 columnas en escritorio).

**Otros**

- [ ] JavaScript del menú hamburguesa (`aria-expanded`).
- [ ] `site.webmanifest`: nombre, colores y rutas de iconos (aún tiene los valores de ejemplo). Los colores del manifiesto se escriben a mano y deben coincidir con `--color-fondo` y `--color-primario`.
- [ ] Open Graph: etiquetas `og:` para compartir en redes (herramientas: opengraph.xyz para previsualizar; Sharing Debugger de Facebook y Post Inspector de LinkedIn una vez publicada).
- [ ] Precarga de Oswald: `<link rel="preload">`.
- [ ] Comprobar que Oswald se ve distinta a 400 y a 700 (confirma que la fuente sigue siendo variable).

---

## Créditos

- **Iconos:** [Lucide](https://lucide.dev), licencia ISC.
- **Tipografías:** [Oswald](https://fonts.google.com/specimen/Oswald) y [Source Sans 3](https://fonts.google.com/specimen/Source+Sans+3), licencia SIL Open Font License.
- **Reset:** basado en los resets de Josh Comeau y Andy Bell.
- **Rampas de color:** estructura inspirada en la paleta de [IBM Carbon](https://carbondesignsystem.com/), con valores propios generados con [Huetone](https://huetone.ardov.me/).
- **Sistema de diseño y maqueta de referencia:** generados con Stitch.