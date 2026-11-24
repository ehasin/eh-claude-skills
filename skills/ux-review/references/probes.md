# Browser probes

Run these with the browser's JavaScript tool (Claude in Chrome: `javascript_tool`) on the page being reviewed. P1–P5 are read-only: they measure, they never change the page. P6 only adds an overlay to your own tab. Each returns a small JSON object; record the numbers, not the raw dump.

Automated checks catch only part of the real problems. Treat a clean probe as "nothing obvious found", never as a pass on its own.

## P1: Install performance observers

Run as early as possible on a page load (right after navigating, then reload if you navigated before installing). It keeps collecting until P2 reads it.

```js
(() => {
  const s = (window.__uxr = { lcp: null, cls: 0, maxEvent: 0, events: 0, longTasks: 0 });
  const watch = (type, fn, opts = {}) => {
    try { new PerformanceObserver(l => l.getEntries().forEach(fn)).observe({ type, buffered: true, ...opts }); }
    catch (e) { s['no_' + type] = true; }
  };
  watch('largest-contentful-paint', e => { s.lcp = Math.round(e.startTime); });
  watch('layout-shift', e => { if (!e.hadRecentInput) s.cls += e.value; });
  watch('event', e => { s.events++; s.maxEvent = Math.max(s.maxEvent, Math.round(e.duration)); }, { durationThreshold: 16 });
  watch('longtask', () => { s.longTasks++; });
  return 'uxr observers installed';
})()
```

## P2: Read performance

Run after the page has settled, and again after you've interacted with it (for interaction latency).

```js
(() => {
  const nav = performance.getEntriesByType('navigation')[0];
  const fcp = performance.getEntriesByName('first-contentful-paint')[0];
  const res = performance.getEntriesByType('resource');
  const s = window.__uxr || {};
  const kb = n => Math.round(n / 1024);
  return {
    observers: window.__uxr ? 'ok' : 'P1 not installed: lcp/cls/interaction unknown',
    ttfbMs: nav ? Math.round(nav.responseStart) : null,
    fcpMs: fcp ? Math.round(fcp.startTime) : null,
    lcpMs: s.lcp ?? null,
    cls: s.cls != null ? Math.round(s.cls * 1000) / 1000 : null,
    slowestInteractionMs: s.maxEvent || null,
    interactionsSeen: s.events || 0,
    longTasks: s.longTasks || 0,
    domContentLoadedMs: nav ? Math.round(nav.domContentLoadedEventEnd) : null,
    loadMs: nav ? Math.round(nav.loadEventEnd) : null,
    requests: res.length,
    transferKB: kb(res.reduce((t, r) => t + (r.transferSize || 0), 0)),
    jsKB: kb(res.filter(r => r.initiatorType === 'script').reduce((t, r) => t + (r.transferSize || 0), 0)),
    slowestRequests: res.slice().sort((a, b) => b.duration - a.duration).slice(0, 5)
      .map(r => `${Math.round(r.duration)}ms ${r.name.split('?')[0].slice(-80)}`)
  };
})()
```

`slowestInteractionMs` is the longest event duration seen, a rough stand-in for INP in one session. `transferKB` is 0 for cached or cross-origin resources without `Timing-Allow-Origin`; say so if it looks too low.

## P3: Accessibility and structure

```js
(() => {
  const vis = el => { const r = el.getBoundingClientRect(), s = getComputedStyle(el);
    return r.width > 0 && r.height > 0 && s.visibility !== 'hidden' && s.display !== 'none' && s.opacity !== '0'; };
  const byIds = ids => (ids || '').split(/\s+/).map(id => document.getElementById(id)?.textContent || '').join(' ');
  const accName = el => [el.getAttribute('aria-label'), byIds(el.getAttribute('aria-labelledby')), el.textContent,
    el.getAttribute('title'), [...el.querySelectorAll('img[alt], svg[aria-label]')].map(i => i.getAttribute('alt') || i.getAttribute('aria-label')).join(' '), el.value]
    .map(s => (s || '').trim()).find(Boolean) || '';
  const brief = el => el.outerHTML.replace(/\s+/g, ' ').slice(0, 140);
  const inText = el => el.tagName === 'A' && el.parentElement && /^(P|LI|SPAN|TD|DD)$/.test(el.parentElement.tagName);
  const controls = [...document.querySelectorAll('button, a[href], [role=button], [role=link], [role=tab], [role=menuitem]')].filter(vis);
  const fields = [...document.querySelectorAll('input, select, textarea')]
    .filter(e => vis(e) && !['hidden', 'submit', 'button', 'reset', 'image'].includes(e.type));
  const targets = [...document.querySelectorAll('button, a[href], input, select, textarea, [role=button], [tabindex]:not([tabindex="-1"])')]
    .filter(e => vis(e) && !inText(e));
  const viewport = document.querySelector('meta[name=viewport]')?.content || null;
  return {
    title: document.title,
    lang: document.documentElement.lang || null,
    viewport,
    zoomBlocked: !!viewport && /user-scalable\s*=\s*(no|0)|maximum-scale\s*=\s*1(\.0)?\b/.test(viewport),
    horizontalScroll: document.documentElement.scrollWidth > window.innerWidth + 1,
    h1Count: document.querySelectorAll('h1').length,
    headings: [...document.querySelectorAll('h1, h2, h3')].filter(vis).map(h => `${h.tagName}: ${h.textContent.trim().slice(0, 60)}`).slice(0, 25),
    landmarks: ['main', 'nav', 'header', 'footer'].filter(t => document.querySelector(`${t}, [role=${t === 'header' ? 'banner' : t === 'footer' ? 'contentinfo' : t === 'nav' ? 'navigation' : t}]`)),
    skipLink: !![...document.querySelectorAll('a[href^="#"]')].find(a => /skip|main content/i.test(a.textContent)),
    imagesWithoutAlt: [...document.images].filter(i => vis(i) && !i.hasAttribute('alt')).map(i => i.currentSrc.split('?')[0].slice(-80)).slice(0, 15),
    unnamedControls: controls.filter(e => !accName(e)).map(brief).slice(0, 15),
    unlabeledFields: fields.filter(e => !(e.labels && e.labels.length) && !e.getAttribute('aria-label') && !e.getAttribute('aria-labelledby'))
      .map(e => `${brief(e)}${e.placeholder ? '  [placeholder only]' : ''}`).slice(0, 15),
    fieldsMissingAutocomplete: fields.filter(e => /email|tel|name|address|postal|zip|password|cc-|card/i.test(`${e.name} ${e.id} ${e.type}`) && !e.getAttribute('autocomplete'))
      .map(e => `${e.type} name=${e.name || e.id}`).slice(0, 10),
    smallTargets: targets.map(e => { const r = e.getBoundingClientRect(); return { e, w: Math.round(r.width), h: Math.round(r.height) }; })
      .filter(t => t.w < 24 || t.h < 24).map(t => `${t.w}x${t.h} ${(accName(t.e) || t.e.tagName).slice(0, 40)}`).slice(0, 15),
    positiveTabindex: document.querySelectorAll('[tabindex]:not([tabindex="0"]):not([tabindex^="-"])').length,
    reducedMotionRules: [...document.styleSheets].some(ss => { try { return [...ss.cssRules].some(r => /prefers-reduced-motion/.test(r.conditionText || r.media?.mediaText || '')); } catch (e) { return false; } })
  };
})()
```

`reducedMotionRules: false` can also mean the rules live in a cross-origin stylesheet that can't be read; only report it if the page visibly animates.

## P4: Text contrast

Approximate: it uses the nearest solid background. Text over a background image or gradient (patterned page backgrounds are common) is measured against the nearest solid colour under the image, or the page's background colour, and marked `approx: true`; confirm those by eye on a screenshot before filing. Effective opacity of the element and its ancestors is applied. Colours the parser can't read (e.g. `oklch()`) are counted as `unparsed`.

```js
(() => {
  const parse = c => { const m = /^rgba?\(([^)]+)\)$/.exec(c); if (!m) return null;
    const p = m[1].split(/[\s,\/]+/).filter(Boolean).map(parseFloat); return { r: p[0], g: p[1], b: p[2], a: p.length > 3 ? p[3] : 1 }; };
  const lum = ({ r, g, b }) => [r, g, b].map(v => { v /= 255; return v <= 0.04045 ? v / 12.92 : Math.pow((v + 0.055) / 1.055, 2.4); })
    .reduce((t, v, i) => t + v * [0.2126, 0.7152, 0.0722][i], 0);
  const blend = (fg, bg) => ({ r: fg.r * fg.a + bg.r * (1 - fg.a), g: fg.g * fg.a + bg.g * (1 - fg.a), b: fg.b * fg.a + bg.b * (1 - fg.a), a: 1 });
  const pageBg = () => { for (const e of [document.body, document.documentElement]) { const c = parse(getComputedStyle(e).backgroundColor); if (c && c.a >= 0.95) return c; }
    return { r: 255, g: 255, b: 255, a: 1 }; };
  // Nearest solid background; if an image or gradient sits in between, keep walking and mark the result approximate.
  const background = el => { let approx = false;
    for (let e = el; e; e = e.parentElement) { const s = getComputedStyle(e);
      if (s.backgroundImage && s.backgroundImage !== 'none') approx = true;
      const c = parse(s.backgroundColor); if (c === null && s.backgroundColor && !/^rgba?\(/.test(s.backgroundColor)) return 'unparsed';
      if (c && c.a >= 0.95) return { c, approx }; }
    return { c: pageBg(), approx }; };
  const opacity = el => { let o = 1; for (let e = el; e; e = e.parentElement) o *= parseFloat(getComputedStyle(e).opacity); return o; };
  const seen = new Set(), fails = []; let checked = 0, approxCount = 0, unparsed = 0;
  const walker = document.createTreeWalker(document.body, NodeFilter.SHOW_TEXT, { acceptNode: n => n.textContent.trim() ? 1 : 2 });
  while (walker.nextNode() && checked < 1500) {
    const el = walker.currentNode.parentElement; if (!el || seen.has(el)) continue; seen.add(el);
    const s = getComputedStyle(el), r = el.getBoundingClientRect(), op = opacity(el);
    if (!r.width || !r.height || s.visibility === 'hidden' || s.display === 'none' || op === 0) continue;
    checked++;
    const fg0 = parse(s.color), bg = background(el);
    if (!fg0 || bg === 'unparsed') { unparsed++; continue; }
    if (bg.approx) approxCount++;
    const fg = blend({ ...fg0, a: fg0.a * op }, bg.c);
    const L1 = lum(fg), L2 = lum(bg.c), ratio = (Math.max(L1, L2) + 0.05) / (Math.min(L1, L2) + 0.05);
    const size = parseFloat(s.fontSize), large = size >= 24 || (size >= 18.66 && parseInt(s.fontWeight, 10) >= 700);
    const need = large ? 3 : 4.5;
    if (ratio < need) fails.push({ ratio: Math.round(ratio * 100) / 100, need, px: Math.round(size), approx: bg.approx,
      opacity: Math.round(op * 100) / 100, text: el.textContent.trim().replace(/\s+/g, ' ').slice(0, 50) });
  }
  fails.sort((a, b) => a.ratio - b.ratio);
  return { checked, failing: fails.length, approxOverImages: approxCount, unparsed, worst: fails.slice(0, 15) };
})()
```

Disabled controls are exempt from contrast rules; drop them from findings.

## P5: Where is focus?

Run after each Tab press during the keyboard pass.

```js
(() => {
  const el = document.activeElement;
  if (!el || el === document.body) return { focus: 'body (nothing focused)' };
  const s = getComputedStyle(el), r = el.getBoundingClientRect();
  const visibleRing = (s.outlineStyle !== 'none' && parseFloat(s.outlineWidth) > 0) || (s.boxShadow && s.boxShadow !== 'none');
  return {
    focus: `${el.tagName.toLowerCase()}${el.id ? '#' + el.id : ''} "${(el.getAttribute('aria-label') || el.textContent || el.value || '').trim().slice(0, 40)}"`,
    ringDetected: visibleRing,
    outline: `${s.outlineStyle} ${s.outlineWidth} ${s.outlineColor}`,
    onScreen: r.bottom > 0 && r.top < innerHeight && r.right > 0 && r.left < innerWidth,
    rect: [Math.round(r.left), Math.round(r.top), Math.round(r.width), Math.round(r.height)]
  };
})()
```

`ringDetected: false` doesn't prove focus is invisible (it might be a background or border change): confirm with a screenshot before filing it.

## P6: Mobile frame (when the window can't be resized)

Lays a 390×844 frame of the same page over the current one. Media queries inside it see a 390 px viewport, so you get the real mobile layout. It's same-origin, so it shares the app's local storage and the probes can run inside it. It changes only your own tab, never the app. Remove it when done (last line).

```js
(() => {
  document.getElementById('uxr-mobile')?.remove();
  const f = document.createElement('iframe');
  f.id = 'uxr-mobile'; f.src = location.href;
  Object.assign(f.style, { position: 'fixed', left: '40px', top: '40px', width: '390px', height: '844px',
    zIndex: 2147483647, border: '2px solid #000', background: '#fff' });
  document.body.appendChild(f);
  return 'mobile frame added at 40,40 (CSS px); screenshot or zoom that region';
})()
```

To run a probe inside it, wrap the probe so it uses the frame's window and document:

```js
(() => { const w = document.getElementById('uxr-mobile').contentWindow, d = w.document;
  return { inner: [w.innerWidth, w.innerHeight], horizontalScroll: d.documentElement.scrollWidth > w.innerWidth + 1,
    targets: [...d.querySelectorAll('button, a[href], input, select, [role=button]')]
      .map(e => { const r = e.getBoundingClientRect(); return r.width ? `${Math.round(r.width)}x${Math.round(r.height)} ${(e.getAttribute('aria-label') || e.textContent || e.tagName).trim().slice(0, 30)}` : null; })
      .filter(Boolean) };
})()
```

Remove: `document.getElementById('uxr-mobile')?.remove()`

If the app refuses to be framed (blank frame, or an `X-Frame-Options` / `frame-ancestors` error in the console), say "mobile not tested: window can't be resized and the app can't be framed".
