---
id: "espgem"
title: "Agente · Gemini · Esponja"
kind: "note"
created: "2026-11-08T16:34:00.000Z"
updated: "2026-11-08T16:50:00.000Z"
---

# Agente · Gemini · Esponja

Reglas generales: ver nota **Agentes - reglas de Esponja** (`agentes-reglas-de-esponja-espreg.md`).

Zona: Fondo 3D con Three.js, estilos visuales (Tailwind/CSS), animaciones y rediseño kawaii BTS Borahae pedido por el usuario.

## Tareas

- [x] Diseñar el fondo con Three.js y Tailwind (ver `GEMINI.md`): pastel kawaii, liviano, detrás de los peluches, sin tocar el texto ni los SVG
- [x] Corregir el diseño de Grokbot: tipografía kawaii (`Fredoka` + `Quicksand`), sobre 3D estilo BTS Borahae 💜, layout centrado de los 7 peluches BT21 (móvil y desktop), globo de diálogo con avatar y animaciones interactivas

## Hecho

- **`src/lib/Fondo3D.svelte`** _(nuevo)_: Escena 3D con carga diferida (`import('three')`), corazones 3D extruidos, estrellas diamante, burbujas translúcidas y polvo de estrellas en paleta BTS Borahae (`#a855f7`, `#c084fc`, `#d8b4fe`, `#f472b6`, `#7dd3fc`, `#fde047`). Incluye paralaje suave al mover el puntero, reacción de color al tocar cada peluche (`esponja:pop`), `devicePixelRatio` limitado a 2, pausa en pestaña oculta y soporte para `prefers-reduced-motion`.
- **`src/app.css`**: Tipografía kawaii redondeada (`Fredoka` y `Quicksand` desde Google Fonts), fondo aurora `.bg-borahae`, tarjetas `.glass-kawaii` con blur y bordes luminosos, efecto `.text-sticker` y botones `.shimmer-badge`.
- **`src/App.svelte`**: Integración de `<Fondo3D />` y rediseño completo del sobre introductorio en estilo kawaii BTS Borahae con stickers flotantes, tarjeta interior asomándose, sello de corazón animado y acorde musical WebAudio al abrir.
- **`src/lib/Carta.svelte`**: Corrección del layout roto en móvil/desktop usando tarjetas centradas responsivas (4 + 3 centrados en celular, 7 en fila en desktop), contador de mensajitos descubiertos (7 corazones de colores), globo de diálogo estilo chat kawaii con mini avatar del peluche activo y fiesta extra de confeti al descubrir los 7.
- **`src/lib/Burbujas.svelte`**: Burbujas iridiscentes en 3 tonos pastel + destellos flotantes (`💜`, `✦`, `✧`) y corrección del warning de Svelte 5 (`$derived`).
- **`src/lib/Transicion.svelte`**: Ola de burbujas y velo con gradiente lavanda/rosa Borahae y `backdrop-blur`.
- **`package.json`**: Se instaló `three` para que el build de Vite empaquete el chunk diferido 3D.

## Ideas / dudas

- El fondo 3D escucha el evento global `esponja:pop` con `{ detail: { color } }`, cambiando el color de la luz puntual 3D y acelerando suavemente las partículas al tocar cada peluche BT21.
