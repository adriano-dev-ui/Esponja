---
id: "espreg"
title: "Agentes - reglas de Esponja"
kind: "note"
created: "2026-11-08T16:34:00.000Z"
updated: "2026-11-08T16:34:00.000Z"
---

# Agentes - reglas de Esponja

Reglas para los agentes que programan en **Esponja** al mismo tiempo (Wither, Grok Composer, Gemini).
Léelo antes de tocar código. Si una tarea cae fuera de tu zona, **no la edites**: déjala anotada en "Pedidos entre agentes".

## Proyecto

- Stack: Svelte 5 + Vite + Tailwind. Corre con `npm run dev` y se publica con `npm run build` (GitHub Pages, `base: './'`).
- Notas y tareas de los agentes: `WitherShot-data/notas/` (el usuario las lee, edita y marca desde WitherShot).
- Idioma de las notas: español de Perú (tú, no vos).

## Zonas (dueño único)

> El bot de este proyecto completa esta tabla la primera vez, según la estructura real del repo.

| Agente | Es dueño de |
|---|---|
| **Wither** (Grok Bot) | `WitherShot-data/`, `AGENTS.md` y las notas de agentes |
| **Grok Composer** | UI de la carta y los peluches (`src/lib/Carta.svelte`, `src/lib/Burbujas.svelte`, `src/lib/Transicion.svelte`) si el usuario lo pide |
| **Gemini** | Fondo 3D con Three.js y estilos visuales del fondo (Tailwind). Ver `GEMINI.md` |

### Archivos compartidos (un solo cambio a la vez)

Solo **Wither** los edita; los demás piden el cambio abajo:

- `package.json` 
- `vite.config.js` 
- `index.html` 
- `AGENTS.md` 
- `README.md` 
- `src/carta.js` (texto de la carta)

## Flujo de trabajo

Todos trabajan en la **misma carpeta** del proyecto, en la rama actual.

1. **Respeta tu zona.** Es lo único que evita choques. Si necesitas tocar algo ajeno, anótalo en "Pedidos entre agentes".
2. **Cambios acotados:** edita solo los archivos de tu zona que la tarea necesita; no reformatees ni "limpies" archivos ajenos.
3. Si algo sale mal, revierte **solo tus archivos** con `git restore <archivo>` (o `git restore --staged <archivo>`); nunca `git restore .`, `git checkout .`, `reset --hard` ni `clean`.
4. No hagas commits, ramas ni stashes salvo que el usuario lo pida.
5. Al terminar una tarea, corre los chequeos y reporta qué archivos cambiaste:
   - `npm run build`
6. Si el usuario tiene un servidor de desarrollo corriendo, no lo reinicies ni lances otro.

## Notas de cada agente

Cada agente tiene su nota en esta misma carpeta, con tareas en checkbox `- [ ]`:

- `agente-wither-esponja-espwit.md` - Wither
- `agente-grok-composer-esponja-espcom.md` - Grok Composer
- `agente-gemini-esponja-espgem.md` - Gemini

Al empezar: lee tu nota y haz las tareas sin marcar. Al terminar una: márcala `- [x]` y agrega debajo qué archivos cambiaste. Puedes anotar ideas o dudas al final de tu nota.

## Pedidos entre agentes

Anota aquí lo que necesitas que otro dueño cambie (fecha · de → para · qué):

- 2026-11-08 · Gemini → Wither / Grok Composer · Se agregó `three` a `package.json` y se rediseñó la UI kawaii BTS Borahae (`src/App.svelte`, `src/app.css`, `src/lib/Carta.svelte`, `src/lib/Burbujas.svelte`, `src/lib/Transicion.svelte`, `src/lib/Fondo3D.svelte`) por pedido directo del usuario, manteniendo intactos `src/carta.js` y los SVG de `public/assets/`.
