<script>
  // Transición "ola mágica de burbujas Borahae": cubre la pantalla y revela la carta.
  import { gsap } from 'gsap';
  let capa = $state();
  const N = 36;
  const items = Array.from({ length: N }, (_, i) => ({
    x: (i / N) * 110 - 5 + Math.random() * 6,
    s: 88 + Math.random() * 145,
    tono: i % 3,
  }));

  export function ola(cambiar) {
    const bs = capa.querySelectorAll('.burbuja');
    const h = innerHeight;
    capa.style.visibility = 'visible';
    gsap.set(bs, { y: () => h + 150 + Math.random() * 200, scale: 0.6, opacity: 1 });
    gsap
      .timeline({ onComplete: () => (capa.style.visibility = 'hidden') })
      .to(bs, {
        y: () => h * (0.08 + Math.random() * 0.82) - 100,
        scale: 1.65,
        duration: 0.52,
        ease: 'sine.out',
        stagger: { each: 0.008, from: 'random' },
      })
      .to(capa.querySelector('.velo'), { opacity: 1, duration: 0.22, ease: 'sine.inOut' }, 0.25)
      .add(cambiar, 0.5)
      .to(capa.querySelector('.velo'), { opacity: 0, duration: 0.38, ease: 'sine.inOut' }, 0.55)
      .to(
        bs,
        {
          y: () => -320 - Math.random() * 220,
          scale: 1,
          opacity: 0,
          duration: 0.58,
          ease: 'sine.in',
          stagger: { each: 0.006, from: 'random' },
        },
        0.5
      );
  }
</script>

<div
  bind:this={capa}
  class="pointer-events-none fixed inset-0 z-50 overflow-hidden"
  style="visibility:hidden"
  aria-hidden="true"
>
  <div
    class="velo absolute inset-0 bg-gradient-to-b from-purple-200/80 via-fuchsia-200/80 to-pink-200/80 backdrop-blur-sm"
    style="opacity:0"
  ></div>
  {#each items as b}
    <span
      class="burbuja grande g-{b.tono} absolute top-0"
      style="left:{b.x}%;width:{b.s}px;height:{b.s}px;margin-left:-{b.s / 2}px"
    ></span>
  {/each}
</div>

<style>
  .grande {
    background: radial-gradient(
      circle at 30% 26%,
      rgba(255, 255, 255, 0.95) 0 6%,
      rgba(255, 255, 255, 0.28) 7% 20%,
      rgba(216, 180, 254, 0.14) 52%,
      rgba(168, 85, 247, 0.52) 100%
    ) !important;
  }
  .g-1 {
    background: radial-gradient(
      circle at 30% 26%,
      rgba(255, 255, 255, 0.95) 0 6%,
      rgba(255, 255, 255, 0.28) 7% 20%,
      rgba(251, 207, 232, 0.14) 52%,
      rgba(236, 72, 153, 0.48) 100%
    ) !important;
  }
  .g-2 {
    background: radial-gradient(
      circle at 30% 26%,
      rgba(255, 255, 255, 0.95) 0 6%,
      rgba(255, 255, 255, 0.28) 7% 20%,
      rgba(186, 230, 253, 0.14) 52%,
      rgba(125, 211, 252, 0.5) 100%
    ) !important;
  }
</style>
