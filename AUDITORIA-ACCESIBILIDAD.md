# Auditoría de accesibilidad y buenas prácticas

**Proyecto:** Dermclar Lab, prototipo ecommerce estático  
**Fecha:** 11 de septiembre de 2026  
**Alcance:** `index.html` y `styles.css`  
**Referencia:** WCAG 2.2, nivel AA; revisión estática y comprobación de carga en navegador local.

## Resumen ejecutivo

El prototipo tiene una base semántica razonable, `lang="es"`, título descriptivo, viewport responsive, jerarquía de encabezados y etiquetas de formulario. Sin embargo, **todavía no cumple WCAG 2.2 AA**.

Los riesgos principales son:

1. Contraste insuficiente en varios textos color salvia y coral.
2. Falta de estilos visibles de foco para teclado.
3. Falta de enlace para saltar directamente al contenido principal.
4. Controles que parecen funcionales pero no tienen comportamiento real en un prototipo HTML/CSS.
5. Formulario sin mensajes de error ni atributos de autocompletado.
6. Enlaces con destinos inexistentes o ambiguos.

## Hallazgos

### A-01 · Contraste de color insuficiente

**Severidad:** Alta  
**WCAG:** 1.4.3 Contraste mínimo (AA), 1.4.11 Contraste no textual (AA)

El color `--sage: #77978c` se usa en textos pequeños como `.eyebrow`, `.product-type`, `.product-note`, `.collection-meta span` y enlaces. Sobre fondos claros como `#fbfaf7`, `#f5f3ed` y blanco, el contraste debe verificarse y probablemente no alcanza 4.5:1 para texto normal. En el botón claro se usa una combinación más sólida, pero debe confirmarse con un analizador.

También hay elementos visuales finos en `rgba(..., .3)` y `rgba(..., .4)` sobre fondos claros. Las líneas y bordes que comunican información deben alcanzar 3:1 frente al fondo cuando son necesarios para distinguir componentes.

**Recomendación:** oscurecer el salvia para textos pequeños, por ejemplo hacia un tono cercano a `#52766b`, y validar todas las combinaciones con axe, Lighthouse o Colour Contrast Analyser. No depender solo del color para estados o categorías.

### A-02 · Indicador de foco no definido

**Severidad:** Alta  
**WCAG:** 2.4.7 Foco visible (AA), 2.4.11 Foco no oculto (AA)

No existe una regla `:focus` o `:focus-visible` en la hoja de estilos. El navegador puede mostrar un foco por defecto, pero el diseño no lo garantiza y algunos reset o navegadores pueden hacerlo poco visible. Todos los enlaces, botones e inputs deben mostrar claramente el elemento activo.

**Recomendación:** añadir un estilo consistente, por ejemplo un `outline` de 3 px con color de alto contraste y `outline-offset` suficiente. No usar `outline: none` sin una alternativa equivalente.

### A-03 · Falta enlace de salto al contenido

**Severidad:** Media  
**WCAG:** 2.4.1 Evitar bloques (A)

La página comienza con una barra promocional, navegación y varios enlaces antes del elemento `<main>`. No hay un enlace “Saltar al contenido principal”. Esto aumenta el número de pulsaciones para una persona que navega con teclado o lector de pantalla.

**Recomendación:** colocar como primer elemento del `<body>` un enlace hacia `#inicio`, oculto visualmente hasta recibir foco.

### A-04 · Enlaces y controles sin funcionalidad o destino válido

**Severidad:** Alta para producción / Media para prototipo  
**WCAG:** 2.1.1 Teclado (A), 2.4.4 Propósito de los enlaces (A), 3.2.2 Al recibir entradas (A)

El botón `+` de cada producto es un `<button>`, pero no actualiza la bolsa ni comunica un nuevo estado. El formulario tiene `action` implícito y no muestra confirmación. Además, existen enlaces hacia `#buscar`, `#carrito`, `#contacto`, `#legal`, `#instagram` y `#facebook` cuyos destinos no están presentes como elementos con esos IDs.

Los tabs “Todos”, “Rostro” y “Cuerpo” se presentan como enlaces, pero no filtran productos ni exponen un estado accesible. El enlace “Menú” tampoco abre un menú.

**Recomendación:**

- Implementar la interacción con JavaScript accesible o convertir los controles no disponibles en enlaces honestos hacia páginas reales.
- Usar `aria-current="page"` o `aria-selected` únicamente si se implementa el patrón correspondiente.
- Añadir una región de estado para anunciar “Producto agregado a la bolsa”.
- Eliminar destinos ficticios o crear las secciones correspondientes.

### A-05 · Formulario incompleto para errores y autocompletado

**Severidad:** Media  
**WCAG:** 1.3.5 Identificar propósito de entrada (AA), 3.3.1 Identificación de errores (A), 3.3.3 Sugerencias ante errores (AA), 3.3.2 Etiquetas o instrucciones (A)

El campo tiene una etiqueta visible y `type="email"`, lo cual es positivo. Sin embargo, falta `autocomplete="email"`; tampoco hay un contenedor para mostrar errores de formato o una confirmación de envío. La validación nativa del navegador por sí sola no ofrece una experiencia consistente ni suficiente para producción.

**Recomendación:** añadir `autocomplete="email"`, `aria-describedby` y un mensaje asociado. En una versión funcional, devolver el foco al campo con error y describir cómo corregirlo.

### A-06 · Estructura de navegación mejorable

**Severidad:** Media  
**WCAG:** 1.3.1 Información y relaciones (A), 2.4.6 Encabezados y etiquetas (AA)

La navegación principal está correctamente dentro de `<nav>`, pero no se identifica una navegación secundaria o de pie cuando corresponda. La sección “Beneficios” usa un `aria-label` útil, aunque los elementos numerados no tienen una estructura de lista. El bloque de testimonios utiliza `<blockquote>` correctamente, pero las flechas decorativas no son controles.

**Recomendación:** usar listas (`<ul><li>`) para grupos de enlaces y beneficios repetidos, y reservar texto visible y etiquetas claras para controles interactivos.

### A-07 · Texto dependiente de tipografías externas

**Severidad:** Baja  
**WCAG:** 1.4.12 Espaciado del texto (AA), 1.4.10 Reflow (AA)

La página carga `DM Sans` y `Playfair Display` desde Google Fonts. Hay fuentes de respaldo, pero no hay garantía de disponibilidad, carga o rendimiento. El CSS usa `clamp`, lo que ayuda al responsive, pero debe probarse con zoom del 200% y 400%.

**Recomendación:** comprobar el reflow sin desplazamiento horizontal a 320 CSS px, probar zoom 200% y 400%, y valorar alojar las fuentes localmente para evitar cambios de layout o dependencia de terceros.

### A-08 · Imágenes y gráficos decorativos

**Severidad:** Baja  
**WCAG:** 1.1.1 Contenido no textual (A)

Las composiciones del hero y laboratorio están marcadas con `aria-hidden="true"`, una decisión correcta porque son decorativas. Las imágenes de producto están construidas con CSS y texto visible. Si en el futuro se sustituyen por fotografías, deberán incorporar `alt` útil cuando aporten información o `alt=""` cuando sean decorativas.

## Aspectos que sí cumplen o están bien encaminados

- El documento declara `lang="es"`.
- Existe `<title>` descriptivo y meta viewport.
- Se usa `<header>`, `<nav>`, `<main>`, `<section>` y `<footer>`.
- Los campos de formulario tienen `<label>` visible y el email usa `type="email"`.
- Los botones de producto tienen nombres accesibles mediante `aria-label`.
- La decoración visual está separada de la información mediante `aria-hidden`.
- Hay diseño responsive con puntos de quiebre para tablet y móvil.
- Los objetivos táctiles de los botones de producto tienen 32 px, por encima del mínimo de 24 × 24 px de WCAG 2.2 AA, aunque conviene ampliarlos a 44 × 44 px por ergonomía.
- No se detectaron errores de sintaxis en la comprobación del editor.

## Plan de corrección recomendado

### Prioridad 1

1. Añadir `:focus-visible` para enlaces, botones e inputs.
2. Corregir el contraste de los textos salvia y revisar los bordes visuales.
3. Añadir enlace “Saltar al contenido”.
4. Evitar que los controles aparenten hacer algo si todavía no existe la funcionalidad.

### Prioridad 2

1. Completar validación, mensajes de error y confirmación del formulario.
2. Añadir `autocomplete="email"` y relaciones `aria-describedby`.
3. Corregir enlaces con IDs inexistentes.
4. Convertir grupos repetitivos en listas semánticas.

### Prioridad 3

1. Ejecutar pruebas con zoom al 200% y 400%.
2. Probar teclado completo y lector de pantalla NVDA o VoiceOver.
3. Ejecutar axe DevTools y Lighthouse en una versión servida por HTTP.
4. Confirmar el contraste de todas las combinaciones en estados normal, hover y foco.

## Limitaciones de esta auditoría

Esta revisión fue estática y se realizó sobre el HTML/CSS disponible. No se pudo certificar el cumplimiento completo de WCAG AA sin:

- Prueba con teclado en todos los flujos.
- Prueba con lector de pantalla.
- Prueba a 200% y 400% de zoom.
- Medición automatizada de contraste y árbol de accesibilidad.
- Verificación de comportamiento tras implementar carrito, búsqueda, menú, diagnóstico y envío del formulario.

**Conclusión:** el prototipo es una buena base visual y semántica, pero debe corregir los hallazgos A-01 a A-05 antes de considerarse conforme con WCAG 2.2 AA.
