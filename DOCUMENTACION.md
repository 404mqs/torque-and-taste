# Torque & Taste — Documentación técnica

Sitio: **https://torqueandtaste.com**
Repo: **404mqs/torque-and-taste** · Rama: **main** · Hosting: **GitHub Pages** (gratis, repo público)

Toda la web es **un solo archivo** (`index.html`) con HTML + CSS + JavaScript juntos.
Las fotos viven en la carpeta `fotos/`. Las guías/material local están en `guia/` y `preview/`
(ambas en `.gitignore`, no se publican).

---

## Flujo de edición

El papá edita con ayuda de Claude y commitea por el **editor web de GitHub** (no usa editor local):
- **Texto / código:** Claude le pasa el código listo y él lo pega/commitea.
- **Fotos (archivos):** las sube él con **"Add file → Upload files"** en GitHub. Van siempre a `fotos/`.

Regla mental: **el papá sube archivos; Claude escribe código que apunta a esos archivos.**

---

## Estructura de archivos

```
index.html              # toda la web (HTML+CSS+JS)
CNAME                   # dominio (torqueandtaste.com)
fotos/
  hero.jpg              # fondo del hero (nombre fijo)
  fangio.jpg            # foto circuito Fangio (nombre fijo)
  mendoza.jpg           # foto circuito Wine & Wheels (nombre fijo)
  biscayne.jpg          # foto circuito Biscayne (nombre fijo)
  galeria/             # galería "From the Road": se llena sola con lo que haya acá
    *.jpg
guia/                   # (local, gitignored) guía PDF + instrucciones para el papá
preview/                # (local, gitignored) capturas/scratch de verificación
DOCUMENTACION.md        # este archivo
```

---

## Funcionalidades implementadas

### 1. Fotos

**Slots con nombre fijo** — el código apunta a un nombre exacto en `fotos/`; si el archivo
existe se muestra, si no, cae a un placeholder (`onerror`). Subir/reemplazar = subir con el
mismo nombre.
- `fotos/hero.jpg` → fondo del hero (vía CSS en `.hero-bg`).
- `fotos/fangio.jpg`, `fotos/mendoza.jpg`, `fotos/biscayne.jpg` → tarjetas de circuito.

**Galería auto-poblada** (sección "From the Road") — un script lee la carpeta
`fotos/galeria/` con la **API de GitHub**
(`https://api.github.com/repos/404mqs/torque-and-taste/contents/fotos/galeria`) y dibuja
una grilla con lo que encuentre. Detalles:
- El **nombre del archivo** se usa como título (guiones → espacios, capitaliza; se ignora un
  prefijo numérico tipo `01-` que sólo sirve para ordenar).
- **Caché** en `localStorage` (`tt_galeria_v2`, 30 min) para no gastar la API en cada visita.
  Si se borra una foto, hay que esperar a que venza el caché o forzar recarga (Ctrl+F5);
  además los tiles cuya imagen ya no existe se ocultan solos (`onerror`).
- **Lightbox**: clic en una foto la abre en grande.
- Requiere repo **público** (la API anónima no lee repos privados).

### 2. Multi-idioma (ES / PT / EN)

- **Detección automática** por idioma del sistema operativo del visitante
  (`navigator.languages`): español → `es`, portugués → `pt`, resto → `en` (por defecto).
- **Selector manual** ES/PT/EN en el menú (desktop y mobile). La elección se guarda en
  `localStorage` (`tt_lang`).
- **Cómo está armado** (en `index.html`):
  - Cada texto traducible tiene `data-i18n="clave"` (o `data-i18n-ph="clave"` para
    placeholders de inputs).
  - Las traducciones están en el objeto `I18N = { es:{...}, pt:{...}, en:{...} }`.
  - `applyLang(lang)` recorre los `[data-i18n]` y reemplaza el contenido; también ajusta
    `<title>` y `<meta description>` por idioma.
- **⚠️ Mantenimiento:** todo texto nuevo o modificado debe ir en los **3 idiomas** (agregar la
  clave en `I18N.es`, `I18N.pt` y `I18N.en`). Es el error más fácil de cometer.

### 3. Google Analytics (GA4)

- Snippet en el `<head>`, **inerte** hasta cargar el ID real en `window.GA_MEASUREMENT_ID`
  (placeholder actual: `G-XXXXXXXXXX`). Cuando se ponga el ID (formato `G-XXXXXXXXXX`),
  empieza a medir solo.
- Sin cartelito de cookies por ahora (sitio personal, riesgo bajo). Alternativa cookieless
  posible: Cloudflare Web Analytics.

### 4. Hero con foto

- El hero usa `fotos/hero.jpg` como fondo, con un degradado oscuro doble (más fuerte arriba,
  detrás del menú, y a la izquierda, donde va el título) para que el texto se lea. Punto focal
  de la imagen bajado (`center 58%`) para no dejar la zona más brillante detrás del texto.

---

## Cómo agregar contenido (resumen)

- **Cambiar foto de hero/circuito:** subir `hero.jpg` / `fangio.jpg` / `mendoza.jpg` /
  `biscayne.jpg` a `fotos/`.
- **Sumar foto a la galería:** subir cualquier imagen a `fotos/galeria/` (el nombre = título).
- **Sección/contenido nuevo con texto:** Claude escribe el HTML **con `data-i18n` y las 3
  traducciones**; la foto se sube antes a `fotos/` y el código la referencia.

Material para el papá (en `guia/`, local): `Guia-Agregar-Fotos-TorqueAndTaste.pdf` y
`Instrucciones-Proyecto-Claude.txt` (este último se pega en las instrucciones de su proyecto
de Claude).

---

## Notas / gotchas

- **Ver cambios en vivo:** GitHub Pages tarda 1–2 min en reconstruir. Recargar fuerte
  (**Ctrl+F5**) para saltear caché del navegador y de la galería.
- **Capturas de verificación** (proceso interno, headless Chrome): usar
  `--force-prefers-reduced-motion` (si no, las animaciones `.reveal` dejan el contenido
  invisible) y limitar `.hero{min-height}` (si no, el hero `100vh` se come la captura).
- `index_4.html` (en Downloads) quedó **obsoleto**: no tiene idiomas, hero con foto ni
  Analytics. La fuente de verdad es `index.html` del repo.

---

## Historial (commits principales — 2026-06-14)

- `Soporte de fotos`: 3 circuitos con foto automática + galería auto-poblada + 16 fotos.
- `Multi-idioma (ES/PT/EN) + Google Analytics + hero con foto + foto de Biscayne`.
- `Hero más legible (overlay reforzado + foco más bajo) y galería robusta`.
- `Fotos: nueva de Wine & Wheels (Mendoza) + 2 a galería (Toyota 99 y cascos)`.
