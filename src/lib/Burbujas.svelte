<script>
  // Capa ambiental de burbujas iridiscentes + destellos kawaii (solo CSS transform/opacity).
  const { n = 16 } = $props();
  const r = (i, k) => ((Math.sin(i * 12.9898 + k * 78.233) * 43758.5453) % 1 + 1) % 1;

  const burbujas = $derived(
    Array.from({ length: n }, (_, i) => ({
      x: r(i, 1) * 96 + 2,
      s: 12 + r(i, 2) * 30,
      d: 7 + r(i, 3) * 7,
      del: -r(i, 4) * 14,
      sw: 2.8 + r(i, 5) * 2.4,
      a: 10 + r(i, 6) * 20,
      hue: i % 3, // 0: violeta, 1: rosa, 2: celeste
    }))
  );

  const destellos = $derived(
    Array.from({ length: 14 }, (_, i) => ({
      x: r(i + 20, 1) * 92 + 4,
      y: r(i + 20, 2) * 88 + 6,
      s: 10 + r(i + 20, 3) * 12,
      d: 2.2 + r(i + 20, 4) * 2.5,
      del: -r(i + 20, 5) * 4,
      char: i % 4 === 0 ? '💜' : i % 2 === 0 ? '✦' : '✧',
    }))
  );
</script>

<div class="capa" aria-hidden="true">
  {#each destellos as st}
    <span
      class="destello"
      style="left:{st.x}%;top:{st.y}%;font-size:{st.s}px;animation-duration:{st.d}s;animation-delay:{st.del}s"
    >{st.char}</span>
  {/each}

  {#each burbujas as b}
    <div class="sube" style="left:{b.x}%;animation-duration:{b.d}s;animation-delay:{b.del}s">
      <div class="vaiven" style="--a:{b.a}px;animation-duration:{b.sw}s;animation-delay:{b.del}s">
        <span class="burbuja tono-{b.hue}" style="width:{b.s}px;height:{b.s}px"></span>
      </div>
    </div>
  {/each}
</div>

<style>
  .capa {
    position: absolute;
    inset: 0;
    overflow: hidden;
    pointer-events: none;
    z-index: 1;
  }
  .sube {
    position: absolute;
    bottom: 0;
    animation: sube linear infinite;
  }
  .vaiven {
    animation: vaiven ease-in-out infinite alternate;
  }
  .destello {
    position: absolute;
    color: rgba(168, 85, 247, 0.55);
    text-shadow: 0 0 10px rgba(216, 180, 254, 0.9);
    animation: titila ease-in-out infinite alternate;
    user-select: none;
  }
  @keyframes titila {
    from {
      transform: scale(0.65) rotate(-12deg);
      opacity: 0.25;
    }
    to {
      transform: scale(1.2) rotate(12deg);
      opacity: 0.85;
    }
  }
  @keyframes sube {
    from {
      transform: translate3d(0, 45px, 0);
      opacity: 0;
    }
    12% {
      opacity: 0.95;
    }
    85% {
      opacity: 0.85;
    }
    to {
      transform: translate3d(0, -110vh, 0);
      opacity: 0;
    }
  }
  @keyframes vaiven {
    from {
      transform: translateX(calc(var(--a) * -1));
    }
    to {
      transform: translateX(var(--a));
    }
  }
  :global(.burbuja) {
    display: block;
    border-radius: 50%;
    background: radial-gradient(
      circle at 30% 28%,
      rgba(255, 255, 255, 0.96) 0 13%,
      rgba(255, 255, 255, 0.32) 14% 32%,
      rgba(216, 180, 254, 0.14) 58%,
      rgba(192, 132, 252, 0.55) 100%
    );
    box-shadow:
      inset 0 0 0 1.5px rgba(255, 255, 255, 0.85),
      inset -3px -4px 8px rgba(168, 85, 247, 0.35),
      0 4px 12px rgba(168, 85, 247, 0.12);
  }
  .tono-1 {
    background: radial-gradient(
      circle at 30% 28%,
      rgba(255, 255, 255, 0.96) 0 13%,
      rgba(255, 255, 255, 0.32) 14% 32%,
      rgba(251, 207, 232, 0.15) 58%,
      rgba(244, 114, 182, 0.5) 100%
    );
  }
  .tono-2 {
    background: radial-gradient(
      circle at 30% 28%,
      rgba(255, 255, 255, 0.96) 0 13%,
      rgba(255, 255, 255, 0.32) 14% 32%,
      rgba(186, 230, 253, 0.15) 58%,
      rgba(125, 211, 252, 0.5) 100%
    );
  }
</style>
