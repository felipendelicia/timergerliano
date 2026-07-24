# Portada "El regreso a la plaza" — diseño

**Fecha:** 2026-07-23 · **Estado:** aprobado (enfoque A)

## Contexto

Juntada confirmada: **jueves 24/07/2026, 22:00, Plaza 25 de Agosto**, hasta que dé.
Sin motivo ni organizador: es "El regreso" a las plazas después de una racha de juntadas en casas. No se cena, nadie lleva nada.

Reemplaza el modo "En espera" de `index.html`. El modo "En espera" **no** se archiva (queda en git; se restaura después de la juntada, como se hizo con SE BUSCA).

## Concepto visual

Callejón de barrios bajos, de noche — ejecutado con el sistema under existente (`styles.css`):

- Fondo negro con textura sutil de pared/revoque (CSS puro, sin assets).
- Cono de luz de farol tipo sodio (ámbar tenue) que titila sobre el hero — animación CSS, anulada por `prefers-reduced-motion` (ya cubierto por la regla global).
- Acento verde `--accent` del sistema para UI/neón (nota: el CLAUDE.md decía "violeta"; `styles.css` es la fuente de verdad → se corrige el CLAUDE.md).
- Syne bold mayúscula gigante y legible (nada de spray/distress); Instrument Serif itálica para la bajada.
- Grano B/N global ya provisto por `styles.css`.

## Estructura de `index.html`

1. **Nav** estándar (`.nav`), status dot: `confirmada`.
2. **Hero**: eyebrow "Juntada confirmada · Jue 24/07 · 22:00" · H1 `EL REGRESO A LA PLAZA` (con acento de color en "la plaza") · bajada serif itálica *"los mismos de siempre"* · escena de callejón (farol + pared).
3. **Datos**: punto de encuentro → **Plaza 25 de Agosto** linkeando al Google Maps provisto · 22:00 · "hasta que dé" · "sin cena, sin excusa: volvemos a la plaza".
4. **Countdown regresivo** a `2026-07-24T22:00:00-03:00` (const `ANCHOR`), celdas días:horas:min:seg estilo actual. Al llegar a 0 el reloj pasa a contar **hacia arriba** y el kicker cambia a "YA ARRANCÓ".
5. **CTA**:
   - Botón acento **"¿Opiniones?"** → `navigator.share`/`wa.me` con texto de convocatoria + link (el chiste de Gian como CTA principal).
   - Botón **"¿Viene Gian?"** → `wa.me/?text=` con mensaje listo para mandárselo (el usuario elige el chat de Gian).
   - Copiar link · Instalar app (se mantiene la lógica PWA existente).
6. **Footer**: link a `archivo.html` + meta "Gerli · Lanús".

## Meta / SEO / PWA

- `<title>`, OG y Twitter: "EL REGRESO A LA PLAZA — Jue 24/07 · 22:00".
- og-image nueva **1200×630**: `photos/og-regreso-plaza.png`, mismo estilo (generada con screenshot headless de un HTML temporal).
- JSON-LD: se actualiza la description del WebSite (sin schema Event, YAGNI).
- `sw.js`: bump de versión de cache para que el deploy pise el precache.

## Otros archivos

- `archivo.html`: actualizar la card de "portada actual" (título/labels/desc).
- `CLAUDE.md`: corregir "acento violeta" → "acento verde (`--accent:#2bff88`)".

## Verificación

- Preview local (`python3 -m http.server 8000`), mobile + desktop.
- Countdown correcto (ancla con zona `-03:00`); flip a count-up forzando `ANCHOR` en el pasado desde la consola.
- Botones WhatsApp abren con el texto correcto.
- Screenshot headless con `--force-prefers-reduced-motion` (reveals visibles).
- Deploy: push a `main` junto con el usuario ("vamos debugueando juntos").
