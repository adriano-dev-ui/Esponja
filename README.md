# 💛 Regalo para Esponja

Carta pública: [https://adriano-dev-ui.github.io/Esponja/](https://adriano-dev-ui.github.io/Esponja/)

Tarjeta web kawaii de cumpleaños: sobre "Para Esponja 💛" → ola de burbujas → los 7 peluchitos BT21 (Shooky, Koya, Tata, Cooky, Chimmy, RJ, Mang) flotando. Al tocar cada uno brilla con su color y dice una frase. Abajo: "Feliz cumpleaños Esponja", "de Estrella" y confeti.

Sin fotos: solo texto, dibujos y animaciones (sitio público).

## Editar
- **Textos:** `src/carta.js` (título, firma, mensaje y la frase de cada peluche). Puedes cambiarlos cuando quieras.
- **Peluches:** reemplaza `public/assets/shooky.svg`, `koya.svg`, `tata.svg`, `cooky.svg`, `chimmy.svg`, `rj.svg`, `mang.svg` (mismo nombre, idealmente cuadrados).
- Abre `.../#carta` para ir directo a los peluches.

## Probar en tu compu
```bash
npm install
npm run dev
```

## Publicar en GitHub Pages
Cada push a `main` publica el sitio con `.github/workflows/deploy.yml` (source: GitHub Actions).

Hecho con Svelte, Tailwind, Three.js, GSAP y canvas-confetti. Sonidos: pitiditos con WebAudio (sin archivos).
