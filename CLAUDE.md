# ByteSync — Landing

Sitio estático multipágina (HTML + CSS + JS vanilla). **Sin build, sin npm, sin framework.** Se abre directamente el `.html` o con un server estático. Idioma del contenido: español (`<html lang="es">`).

## Estructura

```
landing/
├── index.html       → css/inicio.css     + js/animations.js
├── nosotros.html    → css/nosotros.css   + js/animations.js + js/animations-tres.js
├── servicios.html   → css/servicios.css  + js/animations-cuatro.js
├── contacto.html    → css/contacto.css   + js/animations.js
├── css/
│   ├── styles.css   ← GLOBAL, se carga en las 4 páginas (siempre primero)
│   └── {inicio,nosotros,servicios,contacto}.css  ← uno por página
├── js/
│   ├── app-header.js / app-footer.js  ← Web Components (Shadow DOM) en las 4 páginas
│   └── animations*.js
└── images/          ← todos los assets (png, svg, mp4)
```

Orden fijo en cada página: `<link styles.css>` → `<link {pagina}.css>` → GSAP CDN → contenido → `app-header.js`, `app-footer.js`, `animations*.js` al final del `<body>`.

## Sistema visual (RESPETAR SIEMPRE)

### Tipografía
- **Lato** (Google Fonts, `@import` en `styles.css`, pesos 100–900 + itálicas). No agregar otras fuentes.
- `h1`: 24px → 30px desde `min-width: 500px`.
- `p`: `font-weight: 300; line-height: 20px`.
- Pesos usados: 300 (texto), 500 (descripciones), 700 (botones/negritas), 800 (`.text-degradado`).

### Paleta
Variables globales en `styles.css` (`:root`) — poco usadas, la mayoría de colores están hardcodeados:
```css
--bg-dark: #020b18;  --celeste: #006793;  --fuccia: #DB1689;
```
Colores reales del diseño:

| Uso | Valor |
|---|---|
| Fondo del `body` | `linear-gradient(to bottom, #15274D, #040930)` |
| Texto degradado (`.text-degradado`) | `linear-gradient(90deg, #fa48c7, #12AAF4)` + `background-clip: text`, `font-weight: 800` |
| Botón principal (`.btn-contact`) | `linear-gradient(120deg, #004e92, #d63384, #004e92)` animado (`gradientShift` 5s, `background-size: 300% 300%`) |
| Borde degradado (`.meritos`) | fondo `linear-gradient(#1a2f5c, #0c1d42)` padding-box + borde `linear-gradient(to right, #d63384, #004e92)` border-box |
| Overlay de banners/secciones con foto | `linear-gradient(45deg, rgba(28,40,88,.678), rgba(163,24,114,.699), rgba(28,40,88,.74))` sobre la imagen |
| Cards servicios | fucsia `#A4368D` / azul `#16284E` |
| Franja CTA | `linear-gradient(90deg, #0B163D, #741B59, #0B163D)` |
| Sección Fénix | `linear-gradient(135deg, #B33496, #800D62)` |
| Cajas proceso (`.caja-x`) | fondo `#0e1a40`, borde `rgba(255,255,255,.12)`; estado `.connected` → borde blanco + `translateY(-6px)` |
| Inputs contacto | fondo `#0E1C3B`, borde `#545A67` |
| Texto | blanco; acentos rosados `#FA92CC`, `rgb(255,154,213)`, celeste `#94DFFF` |

`contacto.css` tiene su propio `:root` local (`--accent-start #9b3cff`, `--accent-end #ff3fb9`, `--radius 10px`, `--gap 20px`, etc.).

### Botones (cada uno tiene su propio radio — no unificar)

| Clase | Fondo | Radio | Padding |
|---|---|---|---|
| `.btn-contact` (index hero) | degradado animado | `4px` | `12px 16px` → `16px 28px` ≥700px |
| `.btn-conocenos` | `#05122c`, texto `#FA92CC`, hover `#010d27` | `20px` | `14px 26px` |
| `.btn-contactdos` (franja) | blanco, texto `#741B59`, hover `rgb(255,206,206)` | `20px` | `12px 20px`, ancho 100% → 200px ≥400px |
| `.btn-chirra` (servicios) | `#9B227D` | `8px` | `14px 20px` (160px ancho en móvil) |
| `.btn-fenix` (servicios) | `#16254D` | `8px` | `14px 20px` |
| `.btn-submit` (contacto) | `linear-gradient(90deg, #792166, #B212E1)`, hover invertido | `12px` | `16px 24px` |

### Radios de borde
`4px` botón hero · `8px` inputs y botones de app · `12px` cajas proceso / `.meritos` móvil · `15px` `.meritos` · `20px` botones pill y `article` de servicios.

### Layout
- `.container`: `max-width: 1300px; margin: 0 auto; padding: 0 20px`. Usar siempre esta clase para el ancho.
- Header **fijo** de 70px de alto → todo lo que va debajo compensa con `margin-top: 70px` (`.space` en index, `.banner-head` en las demás páginas).
- `html, body { overflow-x: hidden }` — los blurs decorativos van `position: absolute; z-index: -1`.
- Mobile-first: el CSS base es móvil y crece con `min-width`. Excepciones puntuales usan `max-width` (`840px` en servicios, `950px` en el header, `1020px` en el footer, `800px` para ocultar el overlay del proceso).

### Breakpoints en uso
`400`, `450`, `500`, `700`, `942`, `950`, `1100`, `1200`, `1250` (min-width) y `800`, `840`, `950`, `1020` (max-width). **Reutilizar estos valores**, no inventar nuevos.

## Header y Footer (Web Components)

`app-header.js` y `app-footer.js` definen `<app-header>` / `<app-footer>` con `attachShadow({mode:'open'})` y todo el HTML + CSS dentro de un template string.

Consecuencias importantes:
- **Los estilos del Shadow DOM están aislados**: para cambiar el header/footer hay que editar el `<style>` dentro del propio JS, no los `.css`.
- Las variables de `:root` **no** llegan bien a estos componentes (`var(--gray)` en el header y `var(--dark)` en el footer no están definidas → quedan sin valor).
- Las rutas de imágenes dentro del componente son relativas al HTML (`images/...`), no al JS.
- Menú móvil: `.hamburger` togglea `.active` en `.hamburger` y `.menu` (aparece bajo `max-width: 950px`, el menú entra desde `left: -100%`). Los `.nav-link` cierran el menú al hacer clic.
- El footer es solo markup, sin JS de comportamiento.

## Animaciones (GSAP)

GSAP 3.12.5 + ScrollTrigger por CDN (`nosotros.html` carga además MotionPathPlugin). Patrones del proyecto:
- Todo se envuelve en guardas `if (document.querySelector(...))` o `document.addEventListener("DOMContentLoaded", ...)` — mantenerlo así al agregar animaciones.
- `scrollTrigger: { start: "top 80%" / "top 85%", once: true }` es la convención para entradas.
- `animations-cuatro.js` (servicios) hace un `gsap.set([...], {opacity: 0})` inicial anti-parpadeo: **si agregas un elemento animado ahí, súmalo a esa lista**.
- `animations-tres.js` (nosotros) genera por JS un `<svg class="process-overlay">` dentro de `.process-wrap`, dibuja la línea punteada que une las `.caja-x` y va agregando la clase `.connected`. Depende de que las cajas existan y de sus posiciones reales → si tocas el layout de esa sección, revisa el path.

### Deuda técnica conocida
- `js/animations-dos.js` **no se referencia en ninguna página** (versión antigua de contacto + conector). No editarlo esperando efecto.
- `.text-degradado` está duplicado en `inicio.css`, `nosotros.css` y `servicios.css`; `.banner-head` en `nosotros.css`, `servicios.css` y `contacto.css`. Si se cambia el degradado o el banner, hay que tocar los tres archivos.
- `animations-tres.js` contiene tres bloques concatenados con `gsap.registerPlugin` repetido.
- El `@media (max-width: 950px)` del header (`app-header.js`) queda sin llave de cierre antes de `</style>`.
- `contacto.html` tiene líneas en blanco antes del `<!DOCTYPE>`.
- El formulario de contacto no tiene `action` real ni JS de envío.

## Reglas al editar

1. Respetar la línea gráfica existente: colores, degradados, radios, paddings y tipografía de las tablas de arriba. No introducir tonos ni medidas nuevas si ya existe un equivalente.
2. Cambios de una sola página → su CSS de página. Cambios transversales → `styles.css`.
3. Header/footer → editar el template dentro de `app-header.js` / `app-footer.js`.
4. Mobile-first con los breakpoints ya listados.
5. Nuevas imágenes van a `images/` y se referencian como `images/archivo.ext` desde el HTML y como `../images/archivo.ext` desde el CSS.
6. Sin dependencias nuevas ni build step; GSAP se sigue cargando por CDN.
