<script>
  import { gsap } from 'gsap';
  import { onMount } from 'svelte';
  import Carta from './lib/Carta.svelte';
  import Burbujas from './lib/Burbujas.svelte';
  import Fondo3D from './lib/Fondo3D.svelte';
  import Transicion from './lib/Transicion.svelte';
  import { carta } from './carta.js';
  import { beep } from './lib/beep.js';

  let trans = $state();
  let fase = $state(location.hash === '#carta' ? 'carta' : 'intro');
  let sobre = $state();
  let abriendo = $state(false);

  onMount(() => {
    if (sobre) {
      gsap.fromTo(
        sobre,
        { y: 36, scale: 0.88 },
        {
          y: 0,
          scale: 1,
          duration: 0.9,
          ease: 'elastic.out(1, 0.65)',
          clearProps: 'transform',
        }
      );
    }
    const h = () => {
      if (location.hash === '#carta') fase = 'carta';
    };
    addEventListener('hashchange', h);
    return () => removeEventListener('hashchange', h);
  });

  function abrir() {
    if (abriendo) return;
    abriendo = true;
    beep(523.25, 0.1, 'sine', 0.07);
    setTimeout(() => beep(659.25, 0.1, 'sine', 0.07), 90);
    setTimeout(() => beep(783.99, 0.14, 'sine', 0.08), 180);
    setTimeout(() => beep(1046.5, 0.18, 'triangle', 0.07), 270);

    window.dispatchEvent(new CustomEvent('esponja:pop', { detail: { color: '#a855f7' } }));

    if (sobre) {
      const solapa = sobre.querySelector('.solapa-top');
      const papel = sobre.querySelector('.papel-interior');
      const sello = sobre.querySelector('.sello-corazon');
      gsap.to(sello, { scale: 1.35, opacity: 0, duration: 0.25, ease: 'back.in(2)' });
      gsap.to(solapa, { rotateX: 180, duration: 0.38, ease: 'power2.out' });
      gsap.to(papel, { y: -38, scale: 1.04, duration: 0.42, delay: 0.1, ease: 'back.out(1.7)' });
      gsap.to(sobre, { scale: 1.06, y: -8, duration: 0.4, ease: 'sine.out' });
    }

    setTimeout(() => {
      trans.ola(() => {
        fase = 'carta';
        abriendo = false;
        history.replaceState(null, '', '#carta');
      });
    }, 320);
  }
</script>

<main class="bg-borahae relative h-full w-full overflow-clip text-purple-950 select-none">
  <Fondo3D />
  <Burbujas n={16} />

  {#if fase === 'intro'}
    <div class="relative z-10 flex h-full flex-col items-center justify-center p-5">
      <!-- Badge superior estilo BTS Borahae -->
      <div class="mb-6 inline-flex items-center gap-2 rounded-full border border-white/85 bg-white/75 px-4 py-1.5 text-xs sm:text-sm font-bold tracking-wider text-purple-700 shadow-md backdrop-blur-md">
        <span>💜</span>
        <span>BT21 UNIVERSE ✦ BORAHAE</span>
        <span>✨</span>
      </div>

      <button
        type="button"
        bind:this={sobre}
        onclick={abrir}
        class="btn-pop group relative flex flex-col items-center gap-6 focus:outline-none"
        aria-label="Abrir sobre para Esponja"
      >
        <!-- Resplandor mágico detrás del sobre -->
        <div class="pointer-events-none absolute -inset-6 rounded-[2.5rem] bg-gradient-to-tr from-purple-400/35 via-pink-300/35 to-sky-300/35 blur-2xl transition-all duration-500 group-hover:scale-110 group-hover:opacity-95"></div>

        <!-- Sobre Kawaii 3D -->
        <div class="sobre-flota relative h-56 w-80 sm:h-64 sm:w-96">
          <!-- Stickers flotantes alrededor del sobre -->
          <span class="sticker-flota absolute -left-4 -top-4 z-30 text-2xl sm:text-3xl drop-shadow-sm">🎀</span>
          <span class="sticker-flota delay-1 absolute -right-4 -top-3 z-30 text-2xl sm:text-3xl drop-shadow-sm">💜</span>
          <span class="sticker-flota delay-2 absolute -left-3 -bottom-3 z-30 text-xl sm:text-2xl drop-shadow-sm">✨</span>
          <span class="sticker-flota delay-3 absolute -right-3 -bottom-2 z-30 text-xl sm:text-2xl drop-shadow-sm">⭐</span>

          <!-- Cuerpo trasero del sobre -->
          <div class="absolute inset-0 rounded-3xl border-2 border-white/90 bg-gradient-to-b from-purple-200 via-fuchsia-100 to-pink-100 shadow-[0_20px_50px_rgba(147,51,234,0.28)]"></div>

          <!-- Tarjetita interior que se asoma -->
          <div class="papel-interior absolute inset-x-4 top-3 bottom-6 z-10 flex flex-col items-center justify-start rounded-2xl border-2 border-dashed border-purple-200 bg-white/95 pt-3 shadow-inner transition-transform duration-500 group-hover:-translate-y-3">
            <div class="flex items-center gap-1.5 text-xs font-bold tracking-widest text-purple-400 uppercase">
              <span>✦</span>
              <span>Happy Birthday</span>
              <span>✦</span>
            </div>
            <div class="mt-1 flex gap-1.5 text-sm">
              <span>🍪</span><span>🐨</span><span>❤️</span><span>🐰</span><span>🐶</span><span>🦙</span><span>🦄</span>
            </div>
          </div>

          <!-- Bolsillo frontal del sobre -->
          <div class="bolsillo-frontal absolute inset-0 z-20 flex flex-col items-center justify-end rounded-3xl pb-5 sm:pb-6">
            <!-- Costura decorativa kawaii -->
            <div class="pointer-events-none absolute inset-2.5 rounded-2xl border-2 border-dashed border-white/75"></div>

            <div class="relative z-10 rounded-2xl bg-white/85 px-5 py-1.5 shadow-sm backdrop-blur-sm border border-purple-200/80">
              <p class="font-display text-2xl sm:text-3xl font-bold tracking-wide text-purple-800 drop-shadow-[0_1px_0_#fff]">
                {carta.para}
              </p>
            </div>
            <p class="relative z-10 mt-1 text-xs font-bold tracking-wider text-purple-600/90">
              De: Estrella ⭐
            </p>
          </div>

          <!-- Solapa superior del sobre -->
          <div class="solapa-top absolute inset-x-0 top-0 z-20 h-[56%] origin-top"></div>

          <!-- Sello de corazón central -->
          <div class="sello-corazon absolute left-1/2 top-[44%] z-30 flex h-14 w-14 sm:h-16 sm:w-16 -translate-x-1/2 -translate-y-1/2 items-center justify-center rounded-full border-2 border-white bg-gradient-to-br from-purple-500 via-fuchsia-500 to-pink-400 shadow-[0_6px_20px_rgba(168,85,247,0.45)] transition-transform duration-300 group-hover:scale-110">
            <span class="text-2xl sm:text-3xl drop-shadow">💛</span>
          </div>
        </div>

        <!-- Botón de acción brillante -->
        <span class="shimmer-badge inline-flex items-center gap-2 rounded-full border-2 border-white/90 px-6 py-2.5 font-display text-base sm:text-lg font-bold text-white shadow-[0_10px_25px_rgba(168,85,247,0.38)]">
          <span>Toca para abrir tu cartita</span>
          <span class="pastel-bob">✨</span>
        </span>
      </button>
    </div>
  {:else}
    <Carta />
  {/if}

  <Transicion bind:this={trans} />
</main>

<style>
  .sobre-flota {
    animation: levita 3.8s ease-in-out infinite;
    perspective: 900px;
  }
  @keyframes levita {
    0%, 100% { transform: translateY(0px) rotate(-0.8deg); }
    50% { transform: translateY(-10px) rotate(0.8deg); }
  }
  .bolsillo-frontal {
    background: linear-gradient(165deg, rgba(243, 232, 255, 0.92) 0%, rgba(233, 213, 255, 0.96) 50%, rgba(251, 207, 232, 0.95) 100%);
    clip-path: polygon(0 28%, 50% 54%, 100% 28%, 100% 100%, 0 100%);
  }
  .solapa-top {
    background: linear-gradient(180deg, #d8b4fe 0%, #f0abfc 100%);
    clip-path: polygon(0 0, 100% 0, 50% 92%);
    border-radius: 24px 24px 0 0;
    filter: drop-shadow(0 6px 8px rgba(126, 34, 206, 0.16));
    transform-style: preserve-3d;
  }
  .sticker-flota {
    animation: stickerBob 2.6s ease-in-out infinite alternate;
  }
  .delay-1 { animation-delay: -0.7s; }
  .delay-2 { animation-delay: -1.4s; }
  .delay-3 { animation-delay: -2.1s; }
  @keyframes stickerBob {
    from { transform: translateY(-3px) rotate(-8deg) scale(0.95); }
    to { transform: translateY(4px) rotate(8deg) scale(1.08); }
  }
</style>
