# .uzo — Mini-tienda B.uzo

Mini-tienda de una sola página (single-page) para el bolso **B.uzo**, con catálogo, carrito, formulario de pedido y envío directo por WhatsApp.

## Descripción

Aplicación HTML estática (sin build, sin backend) con:

- **Home** — hero con logo, carrusel auto-scroll de 5 fotos, sección "Cómo funciona" en 3 pasos, contador en vivo de bolsones pedidos/cupos del drop, y botones "Hacer pedido" + "Ver colección".
- **Catálogo** — grid 2x2 con el B.uzo disponible y 3 productos "Coming Soon".
- **Carrito** — lista de items con control de cantidad.
- **Formulario de pedido** — nombre, tipo de entrega (Pick Up / Delivery), captura de ubicación GPS opcional, dirección, nota y envío del pedido por WhatsApp.
- **Panel de Tweaks** (botón flotante abajo a la derecha cuando se activa) — permite editar en vivo: tamaño del logo, etiqueta del lanzamiento, número de drop, fotos del carrusel (subir desde el dispositivo), bolsones pedidos, cupos totales, etc.

## Estructura de archivos

```
.uzo/
├── README.md            ← este archivo
├── uzo.html             ← app completa (HTML + CSS + JS inline)
├── tweaks-panel.jsx     ← componentes React del panel de Tweaks
├── logo.png             ← logo .uzo (header)
└── buzo-logo.png        ← logo B.uzo (hero)
```

> **Nota:** la app es 100% estática. No requiere `package.json` ni proceso de build. Todas las dependencias se cargan desde CDN.

## Cómo abrirlo localmente

### Opción 1 — abrir directo en el navegador
Doble clic en `uzo.html`. **Limitación:** algunos navegadores bloquean cargar el archivo `tweaks-panel.jsx` con protocolo `file://`, así que el panel de Tweaks puede no funcionar. La tienda principal sí funciona.

### Opción 2 — servidor local (recomendado)
Necesitás Python 3 (viene preinstalado en macOS y Linux):

```bash
cd ruta/al/proyecto
python3 -m http.server 8000
```

Luego abrí: <http://localhost:8000/uzo.html>

Alternativas:
- Node: `npx serve .` y abrir la URL que muestra
- VS Code: extensión "Live Server" → clic derecho en `uzo.html` → "Open with Live Server"

## Cómo desplegarlo en Netlify

### Opción A — Drag & drop (más simple)
1. Entrá a <https://app.netlify.com/drop>
2. Arrastrá la carpeta completa del proyecto al área indicada.
3. Netlify te asigna una URL pública (`https://nombre-aleatorio.netlify.app`). Listo.

### Opción B — Desde Git
1. Subí el proyecto a un repo de GitHub/GitLab/Bitbucket.
2. En Netlify: **Add new site → Import an existing project**.
3. Conectá el repo. **Build settings:**
   - Build command: *(dejar vacío)*
   - Publish directory: `.` (raíz del repo) o el subfolder donde está `uzo.html`.
4. Deploy.

### Opción C — Netlify CLI
```bash
npm install -g netlify-cli
netlify deploy --dir=. --prod
```

> Si querés que la URL raíz (`/`) abra `uzo.html`, renombralo a `index.html` o creá un archivo `_redirects` con: `/  /uzo.html  200`

## Dependencias externas (todas via CDN)

Cargadas desde `uzo.html`:

| Recurso | URL | Uso |
|---|---|---|
| **Cormorant Garamond** (300, 400, 500) | `fonts.googleapis.com` | Títulos serif (logo title, h1, números) |
| **DM Sans** (300, 400, 500) | `fonts.googleapis.com` | Texto sans-serif (UI general) |
| **React 18.3.1** (development build) | `unpkg.com` | Panel de Tweaks |
| **ReactDOM 18.3.1** (development build) | `unpkg.com` | Panel de Tweaks |
| **Babel Standalone 7.29.0** | `unpkg.com` | Compila el JSX del panel de Tweaks en el navegador |

> **Producción:** para mejor performance reemplazá los builds de React `*.development.js` por `*.production.min.js` (mismo path en unpkg). El CDN de Babel Standalone es pesado (~3MB); si te importa la velocidad de carga, lo ideal es pre-compilar el JSX, pero entonces perdés la edición en vivo del panel de Tweaks.

Sin Babel/React la tienda principal **sí funciona** — solo se pierde el panel de Tweaks.

## WhatsApp del pedido — número configurable

El número de WhatsApp al que se envían los pedidos está en:

**Archivo:** `uzo.html`
**Línea:** **561**

```js
window.open(`https://wa.me/595981000000?text=${encodeURIComponent(msg)}`,'_blank');
```

Cambiá `595981000000` por tu número en formato internacional **sin signo `+`, sin espacios, sin guiones**:
- Paraguay: `595` + tu número (ej. `595981234567`)
- Argentina: `549` + código de área + número
- México: `52` + número
- España: `34` + número

Ejemplo:
```js
window.open(`https://wa.me/5491155551234?text=${encodeURIComponent(msg)}`,'_blank');
```

✅ **Confirmado:** el carrito y el envío por WhatsApp funcionan. Flujo: agregar al carrito → ajustar cantidad → "Continuar pedido" → completar formulario → "Enviar pedido por WhatsApp" abre WhatsApp Web/App con el mensaje pre-armado (producto, cantidad, cliente, tipo de entrega, ubicación GPS si Delivery, nota y total).

## Tweaks — qué se puede editar en vivo

Activá el botón **Tweaks** en la barra de herramientas (cuando lo abrís desde el editor de diseño). El panel flotante permite ajustar:

- **Lanzamiento:** etiqueta superior ("Nuevo lanzamiento"), bolsones disponibles, número de drop.
- **Contador "En vivo":** bolsones pedidos, cupos totales, etiqueta del drop.
- **Fotos del drop (5):** subir/quitar cada foto del carrusel desde tu dispositivo.
- **Logo header:** tamaño en px.
- **Logo B.uzo (hero):** ancho en %.
- **Título principal (Home):** mostrar/ocultar y editar texto.

Los cambios se persisten en el bloque `EDITMODE-BEGIN…EDITMODE-END` del HTML cuando se hacen desde el editor.

## Pendiente / mejoras sugeridas

### Pendiente funcional
- [ ] **Sincronizar precio del catálogo con el carrito.** El catálogo muestra `Gs. 380.000` pero el carrito y el total siguen calculándose con `PRICE = 85000` (línea 384 de `uzo.html`). Hay que cambiar `const PRICE = 85000;` a `const PRICE = 380000;` y actualizar los textos `Gs. 85,000 c/u` y `Gs. 85,000` que aparecen como placeholder en el HTML del carrito (alrededor de las líneas 310 y 321).
- [ ] **Configurar el número de WhatsApp real** (línea 561, ver sección anterior).
- [ ] **Cargar las 5 fotos reales del producto** (por ahora son placeholders "Foto próximamente"). Se pueden subir desde el panel de Tweaks o pegar URLs/data-URIs directamente en el bloque `EDITMODE-BEGIN`.
- [ ] **Productos "Coming Soon"** del catálogo: tienen `pname: "Próximo"` genérico — falta definirlos cuando estén listos.
- [ ] **Variantes del B.uzo** (color seleccionable en el catálogo): los 3 puntos de color funcionan visualmente pero no se persiste la selección al carrito ni se incluye en el mensaje de WhatsApp.

### Mejoras opcionales
- [ ] **Precio dinámico** desde el bloque de Tweaks (hoy está hardcoded en `PRICE`).
- [ ] **Stock por variante** en lugar de un solo contador global.
- [ ] **Validación más fuerte del formulario** (teléfono, dirección obligatoria si delivery).
- [ ] **Pantalla de confirmación post-WhatsApp** (hoy se abre WhatsApp y queda el carrito como estaba).
- [ ] **Persistir el carrito en `localStorage`** para que sobreviva un refresh.
- [ ] **Analytics** (Google Analytics, Plausible, etc.) — no hay nada integrado.
- [ ] **Meta tags Open Graph + Twitter Card** para previews al compartir el link.
- [ ] **Favicon** (no está configurado).
- [ ] **Imagen real del producto** en las cards del catálogo (hoy es un SVG ilustrado).
- [ ] **Compresión de imágenes** (`logo.png` y `buzo-logo.png`) — convenir a `.webp` para reducir peso.
- [ ] **Pre-compilar el JSX** del panel de Tweaks para no depender de Babel en runtime en producción.
- [ ] **Versión sin Tweaks** (build de producción): bastaría con borrar el bloque `<!-- Tweaks panel -->` del final de `uzo.html` antes de desplegar a producción si querés que el panel ni siquiera cargue para los usuarios finales.
