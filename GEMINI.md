# Nota para Gemini — fondo de la carta Esponja

Hola Gemini. Este proyecto es una carta web de cumpleaños para la prima del autor (apodada **Esponja**), firmada por **Estrella**. Está en `C:\Chipa\proyectos\Esponja` (Svelte 5 + Vite + Tailwind). Se publica en GitHub Pages (estático, `base: './'`).

## Qué te pedimos a ti

**Falta el diseño de fondo.** Hazlo tú, bien bonito, con:

- **Three.js** para un fondo 3D suave (partículas, burbujas o un cielo pastel tipo kawaii / BT21).
- **Tailwind** para el layout y los detalles visuales que no sean 3D.
- Que se vea premium en celular (prioridad) y en desktop.
- Liviano: el bundle no debe dispararse. Carga diferida de Three.js, limita `devicePixelRatio` a 2, pausa o baja calidad si `prefers-reduced-motion`.
- El fondo va **detrás** de los peluches y del texto; no debe tapar toques ni hacer ilegible la carta.

Inspiración: pastel, suave, un poco mágico. Los 7 peluches son BT21 (Shooky, Koya, Tata, Cooky, Chimmy, RJ, Mang). Colores que combinan: rosa, lavanda, celeste, crema, destellos morados 💜.

## Qué no cambies

- El texto principal: **«Feliz cumpleaños Esponja»** y **«de Estrella»**.
- Las frases de cada peluche en `src/carta.js` (ya están listas, sin placeholders).
- El flujo: sobre «Para Esponja 💛» → transición de burbujas → pantalla con los 7 peluches tocables.
- Los SVG de `public/assets/` (son dibujos propios basados en los peluches reales). Si hace falta, puedes ajustar tamaño o sombra, no reemplazar el carácter.

## Dónde encaja el fondo

Revisa `src/App.svelte`, `src/lib/Carta.svelte`, `src/lib/Burbujas.svelte` y `src/lib/Transicion.svelte`. El fondo 3D debería vivir como una capa fija a pantalla completa (por ejemplo un canvas detrás de todo). Las burbujas CSS actuales se pueden integrar, suavizar o complementar; no las dejes choppy.

## Criterio de listo

- `npm run build` pasa.
- Se ve bien en ~390×844 y en desktop.
- Los peluches se pueden tocar y las frases se leen claro sobre el fondo.
- GitHub Pages sigue funcionando (sin servidor).

Gracias.
