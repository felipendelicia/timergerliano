# Portada "El regreso a la plaza" — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Reemplazar el modo "En espera" de `index.html` por la portada de la juntada del 24/07 (Plaza 25 de Agosto, 22:00), estética callejón under, con countdown regresivo, CTAs de WhatsApp y og-image nueva.

**Architecture:** Sitio estático sin build. `index.html` se reescribe completo (linkea `styles.css` + CSS/JS inline propio). La escena "callejón" es un layer `position:fixed` detrás de las secciones (pared CSS + cono de luz ámbar con flicker). Se tocan además `archivo.html` (card actual), `sw.js` (cache bump), `CLAUDE.md` (correcciones) y se genera `photos/og-regreso-plaza.png` por screenshot headless.

**Tech Stack:** HTML/CSS/JS vanilla · Google Fonts (Syne, Instrument Serif) · chromium/chrome headless para screenshots · `python3 -m http.server` para preview.

**Nota sobre TDD:** No hay infraestructura de tests (sitio estático). El ciclo por task es: escribir → verificar con servidor local + screenshot headless (comandos y resultado esperado en cada step) → commit.

## Global Constraints

- Sin build ni dependencias; las páginas linkean `styles.css` y solo agregan su CSS/JS propio. NO duplicar tokens de color.
- Acento del sistema: verde `--accent` (`#2bff88`). El ámbar (`rgba(255,171,64,…)`) es SOLO escenografía (farol).
- Ancla del countdown, exacta: `new Date('2026-07-24T22:00:00-03:00')`.
- og-image: `photos/og-regreso-plaza.png`, exactamente 1200×630.
- Español rioplatense en todo texto visible. Tipografía siempre legible (nada de spray/distress).
- Commits SIN atribución a Claude/Anthropic.
- Screenshots headless SIEMPRE con `--force-prefers-reduced-motion` (si no, los `.reveal` salen en negro).
- El modo "En espera" NO se archiva (queda en git; se restaura post-juntada).

---

### Task 1: Nueva portada `index.html`

**Files:**
- Modify: `index.html` (reescritura completa)

**Interfaces:**
- Consumes: `styles.css` (tokens `--accent`, `--panel`, `--mut`, `--line`; primitivas `.nav`, `.eyebrow`, `.btn`, `.btn.v`, `.toast`, `.reveal`, grano global).
- Produces: portada que referencia `photos/og-regreso-plaza.png` (la crea la Task 2; que no exista aún no rompe nada — solo la usan los previews sociales).

- [ ] **Step 1: Reemplazar `index.html` completo con este contenido**

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover" />
<title>Los pibes de Gerli · EL REGRESO A LA PLAZA</title>
<meta name="description" content="EL REGRESO A LA PLAZA — jueves 24/07, 22:00, Plaza 25 de Agosto. Hasta que dé. Los mismos de siempre." />
<meta name="robots" content="index,follow" />
<meta name="theme-color" content="#000000" />
<link rel="canonical" href="https://felipendelicia.github.io/LPDG/" />

<!-- Open Graph (WhatsApp, Facebook, Telegram…) -->
<meta property="og:site_name" content="Los pibes de Gerli" />
<meta property="og:locale" content="es_AR" />
<meta property="og:type" content="website" />
<meta property="og:url" content="https://felipendelicia.github.io/LPDG/" />
<meta property="og:title" content="EL REGRESO A LA PLAZA — Jue 24/07 · 22:00" />
<meta property="og:description" content="Plaza 25 de Agosto · 22:00 · hasta que dé. Los mismos de siempre. ¿Opiniones?" />
<meta property="og:image" content="https://felipendelicia.github.io/LPDG/photos/og-regreso-plaza.png" />
<meta property="og:image:secure_url" content="https://felipendelicia.github.io/LPDG/photos/og-regreso-plaza.png" />
<meta property="og:image:type" content="image/png" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />
<meta property="og:image:alt" content="EL REGRESO A LA PLAZA — Jue 24/07 22:00 · Plaza 25 de Agosto" />

<!-- Twitter / X -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content="EL REGRESO A LA PLAZA — Jue 24/07 · 22:00" />
<meta name="twitter:description" content="Plaza 25 de Agosto · 22:00 · hasta que dé. Los mismos de siempre. ¿Opiniones?" />
<meta name="twitter:image" content="https://felipendelicia.github.io/LPDG/photos/og-regreso-plaza.png" />
<meta name="twitter:image:alt" content="EL REGRESO A LA PLAZA — Jue 24/07 22:00 · Plaza 25 de Agosto" />

<!-- PWA / instalable -->
<link rel="manifest" href="manifest.webmanifest" />
<meta name="mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-capable" content="yes" />
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
<meta name="apple-mobile-web-app-title" content="LPDG" />
<link rel="apple-touch-icon" href="photos/apple-touch-icon.png" />

<!-- iconos -->
<link rel="icon" href="photos/logo-lpdg.svg" type="image/svg+xml" />
<link rel="icon" href="photos/icon-192.png" sizes="192x192" type="image/png" />

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "Los pibes de Gerli",
  "alternateName": "LPDG",
  "url": "https://felipendelicia.github.io/LPDG/",
  "inLanguage": "es-AR",
  "description": "EL REGRESO A LA PLAZA — jueves 24/07, 22:00, Plaza 25 de Agosto. Hasta que dé.",
  "image": "https://felipendelicia.github.io/LPDG/photos/og-regreso-plaza.png"
}
</script>

<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;500;600;700;800&family=Instrument+Serif:ital@0;1&display=swap" rel="stylesheet" />
<link rel="stylesheet" href="styles.css" />

<style>
  /* ---- específico de la portada actual: EL REGRESO A LA PLAZA ---- */
  section{position:relative;z-index:1;min-height:100svh;display:flex;flex-direction:column;justify-content:center;
    padding:clamp(5rem,12vh,8rem) var(--pad-x) clamp(3rem,8vh,5rem);max-width:1280px;margin:0 auto}

  .st .dot{background:var(--accent)}

  /* escena: callejón de barrios bajos (pared + farol de sodio) */
  .alley{position:fixed;inset:0;z-index:0;pointer-events:none}
  .alley .wall{position:absolute;inset:0;opacity:.5;background:
    repeating-linear-gradient(90deg,rgba(245,245,243,.018) 0 2px,transparent 2px 90px),
    repeating-linear-gradient(0deg,rgba(245,245,243,.014) 0 1px,transparent 1px 26px),
    radial-gradient(140% 80% at 50% 0%,rgba(245,245,243,.04),transparent 60%)}
  .alley .lamp{position:absolute;top:0;left:34%;transform:translateX(-50%);width:min(96vw,980px);height:100svh;
    animation:flicker 7.5s steps(1,end) infinite;background:
    radial-gradient(42% 7% at 50% 97%,rgba(255,171,64,.09),transparent 70%),
    conic-gradient(from 180deg at 50% 0%,transparent 41.5%,rgba(255,171,64,.07) 46%,rgba(255,205,130,.15) 50%,rgba(255,171,64,.07) 54%,transparent 58.5%)}
  .alley .bulb{position:absolute;top:-4px;left:34%;transform:translateX(-50%);width:9px;height:9px;border-radius:50%;
    background:#ffd9a0;box-shadow:0 0 18px 7px rgba(255,180,80,.75),0 0 70px 26px rgba(255,150,40,.3);
    animation:flicker 7.5s steps(1,end) infinite}
  @keyframes flicker{0%,100%{opacity:1}86%{opacity:.35}87.5%{opacity:.95}91%{opacity:.5}92%{opacity:1}}

  /* hero */
  .sec-hero .eyebrow{margin-bottom:clamp(1.2rem,3vw,2rem)}
  h1{font-weight:800;text-transform:uppercase;line-height:.92;letter-spacing:-.025em;
    font-size:clamp(2.4rem,9.5vw,6.8rem);overflow-wrap:break-word}
  h1 .v{color:var(--accent)}
  .quote{font-family:var(--font-serif);font-style:italic;color:var(--mut);
    font-size:clamp(1.3rem,3.6vw,2.1rem);line-height:1.3;max-width:34ch;margin-top:clamp(2rem,5vw,3.5rem)}
  .quote .by{display:block;font-style:normal;font-family:var(--font-display);font-weight:600;
    font-size:.72rem;letter-spacing:.24em;text-transform:uppercase;color:var(--ink);margin-top:1rem}
  .scroll{position:absolute;left:var(--pad-x);bottom:clamp(1.6rem,5vh,2.6rem);
    font-weight:600;font-size:.68rem;letter-spacing:.28em;text-transform:uppercase;color:var(--mut);
    display:flex;align-items:center;gap:.7em}
  .scroll .ar{display:inline-block;animation:bob 1.8s ease-in-out infinite}
  @keyframes bob{50%{transform:translateY(6px)}}

  /* datos */
  .sec-datos .eyebrow{margin-bottom:0}
  .datos{margin-top:clamp(1.6rem,4vw,2.6rem);border-top:1px solid var(--line)}
  .drow{padding:clamp(1.1rem,3vw,1.8rem) 0;border-bottom:1px solid var(--line);display:flex;flex-direction:column;gap:.55rem}
  .dlbl{font-weight:600;font-size:.66rem;letter-spacing:.3em;text-transform:uppercase;color:var(--mut)}
  .dval{font-weight:800;text-transform:uppercase;line-height:.95;letter-spacing:-.02em;font-size:clamp(1.4rem,5.2vw,3.2rem)}
  .dval a{color:var(--accent);text-decoration:none;border-bottom:4px solid var(--accent);transition:color .2s,border-color .2s}
  .dval a:hover{color:#fff;border-color:#fff}

  /* contador */
  .sec-count{background:var(--panel)}
  .sec-count::before{content:"";position:absolute;inset:0;z-index:0;opacity:.5;pointer-events:none;
    background:radial-gradient(80% 60% at 50% 45%,rgba(43,255,136,.16),transparent 70%)}
  .count-inner{position:relative;z-index:1;width:100%}
  .kicker{font-weight:700;font-size:clamp(.9rem,2.6vw,1.3rem);letter-spacing:.12em;text-transform:uppercase;
    color:var(--ink);margin-bottom:clamp(1.5rem,4vw,2.6rem)}
  .kicker b{color:var(--accent)}
  .clockframe{position:relative;padding:clamp(1.4rem,4vw,3rem) 0}
  .clockframe::before,.clockframe::after{content:"";position:absolute;width:clamp(40px,6vw,110px);height:clamp(40px,6vw,110px)}
  .clockframe::before{top:0;left:0;border-left:clamp(8px,1.2vw,15px) solid var(--accent);border-top:clamp(8px,1.2vw,15px) solid var(--accent)}
  .clockframe::after{bottom:0;right:0;border-right:clamp(8px,1.2vw,15px) solid var(--accent);border-bottom:clamp(8px,1.2vw,15px) solid var(--accent)}
  .clock{display:flex;align-items:flex-start;justify-content:center;gap:clamp(.3rem,2.2vw,1.6rem);flex-wrap:wrap}
  .cell{display:flex;flex-direction:column;align-items:center;gap:.6rem;min-width:clamp(60px,18vw,150px)}
  .num{font-weight:800;font-variant-numeric:tabular-nums;line-height:.82;letter-spacing:-.04em;
    font-size:clamp(3.2rem,17vw,10rem);color:var(--ink);transition:transform .12s ease}
  .num.tick{transform:translateY(-4px)}
  .lbl{font-weight:600;font-size:clamp(.55rem,1.6vw,.78rem);letter-spacing:.32em;text-transform:uppercase;color:var(--mut)}
  .sep{font-weight:800;color:var(--accent);line-height:.82;font-size:clamp(2rem,10vw,6rem)}
  @media (max-width:480px){.sep{display:none}.cell{min-width:22vw}}
  .since{margin-top:clamp(1.6rem,4vw,2.6rem);font-weight:500;font-size:clamp(.78rem,2.2vw,1rem);
    letter-spacing:.14em;text-transform:uppercase;color:var(--mut)}
  .since b{color:var(--ink);font-weight:700}

  /* cta */
  .statement{font-weight:800;text-transform:uppercase;line-height:.9;letter-spacing:-.03em;
    font-size:clamp(2.5rem,12vw,8rem)}
  .statement .v{color:var(--accent)}
  .acts{display:flex;gap:1rem;flex-wrap:wrap;margin-top:clamp(2rem,5vw,3rem)}
  .lpdg{display:block;width:min(520px,80%);height:auto;margin-top:clamp(2.5rem,7vw,5rem);user-select:none}
  .foot{display:flex;justify-content:space-between;align-items:flex-end;gap:1rem;flex-wrap:wrap;
    margin-top:clamp(2rem,6vw,4rem);padding-top:1.4rem;border-top:1px solid var(--line)}
  .foot a{color:var(--ink);text-decoration:none;font-weight:700;font-size:clamp(.95rem,3vw,1.3rem);
    letter-spacing:.02em;text-transform:uppercase;display:inline-flex;align-items:center;gap:.5em;transition:color .2s}
  .foot a:hover{color:var(--accent)}
  .foot .meta{font-weight:500;font-size:.66rem;letter-spacing:.24em;text-transform:uppercase;color:var(--mut)}
</style>
</head>
<body>
  <nav class="nav">
    <a class="brand" href="index.html" aria-label="Los pibes de Gerli — inicio"><img src="photos/logo-lpdg.svg" alt="Los pibes de Gerli" /></a>
    <span class="nav-r">
      <span class="st"><span class="dot"></span>confirmada</span>
      <a href="archivo.html">Archivo</a>
    </span>
  </nav>

  <!-- escena: callejón -->
  <div class="alley" aria-hidden="true"><div class="wall"></div><div class="lamp"></div><div class="bulb"></div></div>

  <!-- HERO -->
  <section class="sec-hero">
    <p class="eyebrow reveal">Juntada confirmada · jue 24.07 · 22:00</p>
    <h1 class="reveal d1">El regreso a <span class="v">la plaza</span></h1>
    <p class="quote reveal d2">Los mismos de siempre. Sin casa, sin excusa: la calle otra vez.
      <span class="by">— Los pibes de Gerli</span></p>
    <span class="scroll reveal d3">scroll <span class="ar">↓</span></span>
  </section>

  <!-- DATOS -->
  <section class="sec-datos">
    <p class="eyebrow reveal">Los datos</p>
    <div class="datos">
      <div class="drow reveal"><span class="dlbl">Punto de encuentro</span>
        <span class="dval"><a href="https://www.google.com/maps/place/Plaza+25+de+Agosto/@-34.5799604,-58.4621187,18z/data=!4m6!3m5!1s0x95bcb5dfe367fd81:0x8c8caea04ae19350!8m2!3d-34.58018!4d-58.461912!16s%2Fg%2F11cmbjkbk8?entry=ttu&amp;g_ep=EgoyMDI2MDcyMS4wIKXMDSoASAFQAw%3D%3D" target="_blank" rel="noopener">Plaza 25 de Agosto ↗</a></span></div>
      <div class="drow reveal d1"><span class="dlbl">Cuándo</span><span class="dval">Jueves 24.07 · 22:00</span></div>
      <div class="drow reveal d2"><span class="dlbl">Hasta cuándo</span><span class="dval">Hasta que dé</span></div>
      <div class="drow reveal d3"><span class="dlbl">¿Cenamos?</span><span class="dval">No. Volvemos a la plaza.</span></div>
    </div>
  </section>

  <!-- CONTADOR -->
  <section class="sec-count">
    <div class="count-inner">
      <p class="kicker reveal" id="kicker">Cuánto falta para <b>el regreso</b></p>
      <div class="clockframe reveal d1">
        <div class="clock" id="clock">
          <div class="cell"><span class="num" id="d">00</span><span class="lbl">días</span></div>
          <span class="sep">:</span>
          <div class="cell"><span class="num" id="h">00</span><span class="lbl">horas</span></div>
          <span class="sep">:</span>
          <div class="cell"><span class="num" id="m">00</span><span class="lbl">min</span></div>
          <span class="sep">:</span>
          <div class="cell"><span class="num" id="s">00</span><span class="lbl">seg</span></div>
        </div>
      </div>
      <p class="since reveal d2">jue 24 jul 2026 · 22:00 · <b>Plaza 25 de Agosto</b></p>
    </div>
  </section>

  <!-- CTA -->
  <section class="sec-cta">
    <h2 class="statement reveal">Sin casa.<br>Sin excusa.<br><span class="v">La plaza.</span></h2>
    <div class="acts reveal d1">
      <button class="btn v" id="opinions">
        <svg viewBox="0 0 32 32" aria-hidden="true"><path d="M16 3a13 13 0 0 0-11 19.9L3 29l6.3-1.9A13 13 0 1 0 16 3zm0 2.4a10.6 10.6 0 1 1-5.6 19.6l-.4-.2-3.7 1.1 1.1-3.6-.3-.4A10.6 10.6 0 0 1 16 5.4zm-3.6 4.8c-.3 0-.7.1-1 .4-.4.4-1.3 1.3-1.3 3.1s1.4 3.6 1.5 3.9c.2.3 2.6 4.1 6.4 5.6 3.1 1.2 3.8 1 4.5.9.7-.1 2.2-.9 2.5-1.8.3-.9.3-1.6.2-1.8-.1-.2-.4-.3-.8-.5s-2.2-1.1-2.5-1.2c-.3-.1-.6-.2-.8.2-.2.3-.9 1.2-1.1 1.4-.2.2-.4.3-.8.1-.4-.2-1.6-.6-3-1.9-1.1-1-1.9-2.2-2.1-2.6-.2-.4 0-.6.2-.8l.6-.7c.2-.2.2-.4.4-.6.1-.3.1-.5 0-.7l-1-2.5c-.3-.6-.5-.5-.8-.5h-.5z"/></svg>
        ¿Opiniones?
      </button>
      <button class="btn" id="gian">¿Viene Gian?</button>
      <button class="btn" id="copy">Copiar link</button>
      <button class="btn" id="install" hidden>
        <svg viewBox="0 0 24 24" aria-hidden="true"><path d="M12 3v10.6l3.3-3.3 1.4 1.4L12 17.4 6.3 11.7l1.4-1.4L11 13.6V3h1zM5 19h14v2H5v-2z"/></svg>
        Instalar app
      </button>
    </div>
    <img class="lpdg reveal d2" src="photos/logo-lpdg.svg" alt="Los pibes de Gerli" />
    <div class="foot reveal">
      <a href="archivo.html">Portadas anteriores →</a>
      <span class="meta">Gerli · Lanús</span>
    </div>
  </section>

  <div class="toast" id="toast">link copiado</div>

<script>
  document.documentElement.classList.add('js');

  // ---- ancla: la juntada (cuenta regresiva; al llegar, cuenta para arriba) ----
  const ANCHOR = new Date('2026-07-24T22:00:00-03:00');
  const ANCHOR_MS = ANCHOR.getTime();

  const el = {d:document.getElementById('d'),h:document.getElementById('h'),m:document.getElementById('m'),s:document.getElementById('s')};
  const pad = (n)=>String(n).padStart(2,'0');
  function set(node,v){const x=pad(v);if(node.textContent!==x){node.textContent=x;node.classList.add('tick');setTimeout(()=>node.classList.remove('tick'),120);}}

  const kicker=document.getElementById('kicker');
  let started=false;
  function tick(){
    const now=Date.now();
    if(now>=ANCHOR_MS && !started){started=true;kicker.innerHTML='Ya arrancó · <b>estamos en la plaza</b>';}
    const sec = Math.floor(Math.abs(ANCHOR_MS-now)/1000);
    set(el.d, Math.floor(sec/86400));
    set(el.h, Math.floor(sec%86400/3600));
    set(el.m, Math.floor(sec%3600/60));
    set(el.s, sec%60);
    requestAnimationFrame(tick);
  }
  tick();

  // ---- reveal on scroll ----
  const io=new IntersectionObserver((entries)=>{
    entries.forEach(e=>{if(e.isIntersecting){e.target.classList.add('in');io.unobserve(e.target);}});
  },{threshold:.18});
  document.querySelectorAll('.reveal').forEach(n=>io.observe(n));

  // ---- compartir ----
  const SHARE='EL REGRESO A LA PLAZA — jueves 24/07, 22:00, Plaza 25 de Agosto. Hasta que dé. ¿Opiniones?';

  const toast=document.getElementById('toast');
  function showToast(m){toast.textContent=m;toast.classList.add('show');setTimeout(()=>toast.classList.remove('show'),1800);}

  document.getElementById('opinions').onclick=async()=>{
    const url=location.href;
    if(navigator.share){try{await navigator.share({title:'El regreso a la plaza',text:SHARE,url});return;}catch(e){if(e.name==='AbortError')return;}}
    window.open('https://wa.me/?text='+encodeURIComponent(SHARE+' '+url),'_blank');
  };
  document.getElementById('gian').onclick=()=>{
    const t='Gian: jueves 24/07, 22:00, Plaza 25 de Agosto. El regreso. ¿Venís o qué? '+location.href;
    window.open('https://wa.me/?text='+encodeURIComponent(t),'_blank');
  };
  document.getElementById('copy').onclick=async()=>{
    try{await navigator.clipboard.writeText(location.href);showToast('link copiado');}
    catch(e){showToast(location.href);}
  };

  // ---- PWA: instalar app ----
  const installBtn=document.getElementById('install');
  const standalone=matchMedia('(display-mode: standalone)').matches||navigator.standalone===true;
  const isIOS=/iphone|ipad|ipod/i.test(navigator.userAgent);
  let deferred=null;
  window.addEventListener('beforeinstallprompt',(e)=>{e.preventDefault();deferred=e;if(!standalone)installBtn.hidden=false;});
  if(isIOS && !standalone) installBtn.hidden=false;  // iOS no dispara beforeinstallprompt
  installBtn.onclick=async()=>{
    if(deferred){deferred.prompt();await deferred.userChoice;deferred=null;installBtn.hidden=true;return;}
    showToast('Compartir → “Agregar a inicio”');
  };
  window.addEventListener('appinstalled',()=>{installBtn.hidden=true;deferred=null;});

  // ---- service worker (offline + instalable) ----
  if('serviceWorker' in navigator){
    addEventListener('load',()=>navigator.serviceWorker.register('sw.js').catch(()=>{}));
  }
</script>
</body>
</html>
```

- [ ] **Step 2: Levantar server y sacar screenshots (desktop + mobile)**

```bash
cd /home/felipe/Documents/Repositories/LPDG
python3 -m http.server 8000 &>/dev/null &
SCRATCH=/tmp/claude-1000/-home-felipe-Documents-Repositories-LPDG/b4ebd237-6025-4679-877e-d6cf81a352d3/scratchpad
BROWSER=$(command -v chromium chromium-browser google-chrome google-chrome-stable 2>/dev/null | head -1)
"$BROWSER" --headless=new --force-prefers-reduced-motion --hide-scrollbars \
  --window-size=1440,3600 --screenshot="$SCRATCH/portada-desktop.png" http://localhost:8000/
"$BROWSER" --headless=new --force-prefers-reduced-motion --hide-scrollbars \
  --window-size=390,3400 --screenshot="$SCRATCH/portada-mobile.png" http://localhost:8000/
```

Expected: dos PNG generados sin error.

- [ ] **Step 3: Revisar los screenshots (Read de ambos PNG)**

Checklist visual:
- Hero: eyebrow verde "JUNTADA CONFIRMADA · JUE 24.07 · 22:00", H1 gigante con "LA PLAZA" en verde, cono de luz ámbar visible detrás, bajada serif itálica.
- Datos: 4 filas con labels chicos y valores enormes; "PLAZA 25 DE AGOSTO ↗" en verde subrayado.
- Contador: celdas grandes con valores coherentes (falta ~1 día → días=00 o 01, no 000s clavados en 00 salvo que corresponda), corners verdes.
- CTA: statement "SIN CASA. SIN EXCUSA. LA PLAZA.", botón verde "¿OPINIONES?", "¿VIENE GIAN?", "COPIAR LINK", logo Lpdg, footer.
- Mobile: sin overflow horizontal, H1 legible, separadores `:` ocultos (<480px).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: portada EL REGRESO A LA PLAZA (juntada 24/07 · Plaza 25 de Agosto)"
```

---

### Task 2: og-image `photos/og-regreso-plaza.png`

**Files:**
- Create: `photos/og-regreso-plaza.png` (1200×630)
- Scratch: `$SCRATCH/og-regreso.html` (temporal, NO se commitea)

**Interfaces:**
- Consumes: `photos/logo-lpdg.svg` (ruta absoluta en el HTML temporal).
- Produces: el PNG que `index.html` ya referencia en OG/Twitter/JSON-LD.

- [ ] **Step 1: Escribir el HTML temporal en el scratchpad**

Archivo: `$SCRATCH/og-regreso.html`

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8" />
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@700;800&family=Instrument+Serif:ital@1&display=swap" rel="stylesheet" />
<style>
  *{margin:0;padding:0;box-sizing:border-box}
  body{width:1200px;height:630px;background:#000;color:#f5f5f3;font-family:'Syne',sans-serif;
    position:relative;overflow:hidden;padding:64px 72px;display:flex;flex-direction:column;justify-content:space-between}
  .lamp{position:absolute;inset:0;background:
    conic-gradient(from 180deg at 30% -5%,transparent 41%,rgba(255,171,64,.10) 46%,rgba(255,205,130,.18) 50%,rgba(255,171,64,.10) 54%,transparent 59%)}
  .grain{position:absolute;inset:0;opacity:.07;mix-blend-mode:screen;
    background:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='200' height='200'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E")}
  .eyebrow{position:relative;color:#2bff88;font-weight:700;font-size:24px;letter-spacing:.4em;text-transform:uppercase;
    display:flex;align-items:center;gap:24px}
  .eyebrow::after{content:"";height:3px;width:160px;background:#2bff88}
  h1{position:relative;font-weight:800;font-size:118px;line-height:.9;letter-spacing:-.02em;text-transform:uppercase}
  h1 span{color:#2bff88}
  .sub{position:relative;font-family:'Instrument Serif',serif;font-style:italic;font-size:34px;color:#8a8a8f;margin-top:18px}
  .bottom{position:relative;display:flex;justify-content:space-between;align-items:flex-end}
  .meta{font-weight:700;font-size:26px;letter-spacing:.14em;text-transform:uppercase}
  .meta b{color:#2bff88}
  img{height:84px}
</style>
</head>
<body>
  <div class="lamp"></div><div class="grain"></div>
  <div class="eyebrow">Juntada confirmada</div>
  <div>
    <h1>El regreso<br>a <span>la plaza</span></h1>
    <div class="sub">los mismos de siempre</div>
  </div>
  <div class="bottom">
    <div class="meta">Jue 24.07 · 22:00 · <b>Plaza 25 de Agosto</b></div>
    <img src="/home/felipe/Documents/Repositories/LPDG/photos/logo-lpdg.svg" alt="" />
  </div>
</body>
</html>
```

- [ ] **Step 2: Screenshot 1200×630 directo a `photos/`**

```bash
"$BROWSER" --headless=new --hide-scrollbars --window-size=1200,630 --virtual-time-budget=5000 \
  --screenshot=/home/felipe/Documents/Repositories/LPDG/photos/og-regreso-plaza.png \
  "file://$SCRATCH/og-regreso.html"
file /home/felipe/Documents/Repositories/LPDG/photos/og-regreso-plaza.png
```

Expected: `PNG image data, 1200 x 630`.

- [ ] **Step 3: Verificar visualmente (Read del PNG)**

Checklist: fondo negro, cono ámbar desde arriba-izquierda, tipografía Syne cargada (bold real, no fallback), "LA PLAZA" en verde, fila inferior con datos + logo Lpdg. Si la fuente no cargó (texto fino/genérico), reintentar con `--virtual-time-budget=8000`.

- [ ] **Step 4: Commit**

```bash
git add photos/og-regreso-plaza.png
git commit -m "feat: preview social og-regreso-plaza (1200x630)"
```

---

### Task 3: Card "portada actual" en `archivo.html`

**Files:**
- Modify: `archivo.html:111-119` (solo la primera card)

**Interfaces:**
- Consumes: nada nuevo — la card embebe `index.html` por iframe (ya actualizado en Task 1).
- Produces: card actualizada; las cards de `legacy/` NO se tocan.

- [ ] **Step 1: Editar la primera card**

Reemplazar exactamente esto:

```html
      <a class="card reveal" href="index.html">
        <div class="shot"><iframe src="index.html" loading="lazy" tabindex="-1" scrolling="no" title="En espera"></iframe></div>
        <span class="open">abrir ↗</span>
        <div class="meta">
          <div class="row"><span class="idx">actual · standby</span><span class="tag live">en vivo</span></div>
          <div class="name">En espera</div>
          <div class="desc">Estado base · vuelve entre juntadas</div>
        </div>
      </a>
```

por esto:

```html
      <a class="card reveal" href="index.html">
        <div class="shot"><iframe src="index.html" loading="lazy" tabindex="-1" scrolling="no" title="El regreso a la plaza"></iframe></div>
        <span class="open">abrir ↗</span>
        <div class="meta">
          <div class="row"><span class="idx">actual · jul 2026</span><span class="tag live">en vivo</span></div>
          <div class="name">El regreso a la plaza</div>
          <div class="desc">Callejón under · Plaza 25 de Agosto · juntada 24.07</div>
        </div>
      </a>
```

- [ ] **Step 2: Screenshot de verificación**

```bash
"$BROWSER" --headless=new --force-prefers-reduced-motion --hide-scrollbars \
  --window-size=1440,2000 --screenshot="$SCRATCH/archivo.png" http://localhost:8000/archivo.html
```

Read del PNG. Expected: primera card con preview de la portada nueva, name "EL REGRESO A LA PLAZA", tag verde "EN VIVO"; las otras dos cards intactas.

- [ ] **Step 3: Commit**

```bash
git add archivo.html
git commit -m "feat: card de la portada El regreso a la plaza en el archivo"
```

---

### Task 4: Bump de cache en `sw.js`

**Files:**
- Modify: `sw.js:3-10`

**Interfaces:**
- Consumes: nada.
- Produces: `VERSION = 'lpdg-v2'` invalida el precache viejo en clientes instalados; `SHELL` suma `legacy/se-busca.html` (faltaba) y el og nuevo.

- [ ] **Step 1: Editar VERSION y SHELL**

Reemplazar:

```js
const VERSION = 'lpdg-v1';
const SHELL = [
  './', './index.html', './archivo.html', './styles.css', './manifest.webmanifest',
  './photos/logo-lpdg.svg', './photos/logo-lpdg.png', './photos/og-cover.png',
  './photos/icon-192.png', './photos/icon-512.png', './photos/apple-touch-icon.png',
  './photos/leon.jpeg', './photos/IMG-20250614-WA0026.jpg',
  './legacy/cumple-gianni.html'
];
```

por:

```js
const VERSION = 'lpdg-v2';
const SHELL = [
  './', './index.html', './archivo.html', './styles.css', './manifest.webmanifest',
  './photos/logo-lpdg.svg', './photos/logo-lpdg.png', './photos/og-cover.png',
  './photos/og-regreso-plaza.png',
  './photos/icon-192.png', './photos/icon-512.png', './photos/apple-touch-icon.png',
  './photos/leon.jpeg', './photos/IMG-20250614-WA0026.jpg',
  './legacy/cumple-gianni.html', './legacy/se-busca.html'
];
```

- [ ] **Step 2: Sanity check de sintaxis**

```bash
node --check sw.js
```

Expected: sin output (exit 0). Si no hay `node`, verificar a ojo que la lista cierra con `];`.

- [ ] **Step 3: Commit**

```bash
git add sw.js
git commit -m "chore: sw v2 — precache og-regreso-plaza y legacy/se-busca"
```

---

### Task 5: Correcciones en `CLAUDE.md`

**Files:**
- Modify: `CLAUDE.md` (línea de estética y gotcha del timer)

**Interfaces:** N/A (docs).

- [ ] **Step 1: Corregir el color del acento**

Reemplazar el fragmento:

```
acento **violeta** (`--violet`) con corchetes en "L"
```

por:

```
acento **verde brillante** (`--accent:#2bff88`)
```

- [ ] **Step 2: Actualizar el gotcha del timer**

Reemplazar:

```
- El timer de `index.html` cuenta hacia arriba desde un ancla fija: `ANCHOR = new Date('2026-07-10T19:00:00-03:00')` (última juntada). Para cambiar el origen, editar esa constante.
```

por:

```
- El timer de `index.html` usa un ancla fija `ANCHOR = new Date('2026-07-24T22:00:00-03:00')` (próxima juntada): cuenta regresiva hasta el ancla y después cuenta hacia arriba (kicker "Ya arrancó"). En modo "En espera" el ancla es la última juntada y solo cuenta hacia arriba. Para cambiar el origen, editar esa constante.
```

- [ ] **Step 3: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: CLAUDE.md — acento verde real y gotcha del timer regresivo"
```

---

### Task 6: Verificación final + deploy

**Files:** ninguno (solo verificación).

- [ ] **Step 1: Grep de sanidad sobre lo publicado**

```bash
grep -c 'og-regreso-plaza.png' index.html          # Expected: 4 (og:image, secure_url, twitter:image, JSON-LD)
grep -n '2026-07-24T22:00:00-03:00' index.html     # Expected: 1 línea (ANCHOR)
grep -n 'wa.me' index.html                          # Expected: 2 (opiniones fallback + gian)
grep -n 'target="_blank" rel="noopener"' index.html # Expected: 1 (link a Maps)
```

- [ ] **Step 2: Screenshot final de ambas páginas y mostrarlas al usuario**

Re-sacar `portada-desktop.png`, `portada-mobile.png` y `archivo.png` (mismos comandos de Tasks 1/3) y enviarlas con SendUserFile para el "debugueo juntos".

- [ ] **Step 3: Deploy (con el usuario)**

El usuario pidió "vamos debugueando juntos y lo deployamos en un rato": NO pushear hasta su OK. Con el visto bueno:

```bash
git push origin main
```

Expected: GitHub Pages actualiza https://felipendelicia.github.io/LPDG/ en ~1 min. Verificar preview de WhatsApp con el link (puede tardar por cache de scrapers; probar con `?v=2` si hace falta).
