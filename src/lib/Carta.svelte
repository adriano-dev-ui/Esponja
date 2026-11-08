<script>
  import { onMount } from 'svelte';
  import { gsap } from 'gsap';
  import confetti from 'canvas-confetti';
  import { carta } from '../carta.js';
  import { beep } from './beep.js';

  let hoja = $state();
  let activo = $state(null);
  let globo = $state();
  let vistos = $state([]);

  const base = import.meta.env.BASE_URL;
  const borahaePastel = ['#a855f7', '#d8b4fe', '#f472b6', '#fbcfe8', '#7dd3fc', '#fde047'];

  function lluvia() {
    window.dispatchEvent(new CustomEvent('esponja:pop', { detail: { color: '#a855f7' } }));
    confetti({
      particleCount: 100,
      spread: 85,
      origin: { y: 0.62 },
      colors: borahaePastel,
      shapes: ['circle', 'star'],
      scalar: 0.95,
    });
    setTimeout(
      () =>
        confetti({
          particleCount: 45,
          angle: 60,
          spread: 55,
          origin: { x: 0, y: 0.65 },
          colors: borahaePastel,
        }),
      220
    );
    setTimeout(
      () =>
        confetti({
          particleCount: 45,
          angle: 120,
          spread: 55,
          origin: { x: 1, y: 0.65 },
          colors: borahaePastel,
        }),
      380
    );
  }

  function tocar(m, e, i) {
    activo = m;
    const eraNuevo = !vistos.includes(m.id);
    if (eraNuevo) {
      vistos = [...vistos, m.id];
    }

    // Sonido kawaii musical
    const notas = [523.25, 587.33, 659.25, 698.46, 783.99, 880, 987.77];
    const f = notas[i % notas.length];
    beep(f, 0.09, 'sine', 0.06);
    setTimeout(() => beep(f * 1.25, 0.12, 'triangle', 0.05), 70);

    // Pulso de color hacia el fondo 3D Three.js
    window.dispatchEvent(new CustomEvent('esponja:pop', { detail: { color: m.color } }));

    // Rebote elástico kawaii del peluche
    const img = e.currentTarget.querySelector('img');
    gsap.fromTo(
      img,
      { scaleY: 0.78, scaleX: 1.18, rotate: -6 },
      { scaleY: 1, scaleX: 1, rotate: 0, duration: 0.65, ease: 'elastic.out(1.15, 0.38)' }
    );

    const r = e.currentTarget.getBoundingClientRect();
    confetti({
      particleCount: 18,
      spread: 55,
      startVelocity: 19,
      gravity: 0.65,
      scalar: 0.78,
      ticks: 95,
      origin: {
        x: (r.left + r.width / 2) / innerWidth,
        y: (r.top + r.height * 0.35) / innerHeight,
      },
      colors: [m.color, '#c084fc', '#ffffff'],
      shapes: ['star', 'circle'],
    });

    // Si completó los 7 peluchitos por primera vez, fiesta extra
    if (eraNuevo && vistos.length === carta.miembros.length) {
      setTimeout(lluvia, 300);
    }

    queueMicrotask(() => {
      if (globo) {
        gsap.fromTo(
          globo,
          { y: 10, scale: 0.94 },
          { y: 0, scale: 1, duration: 0.38, ease: 'back.out(2.1)' }
        );
      }
    });
  }

  onMount(() => {
    if (hoja) {
      gsap.fromTo(
        hoja.querySelectorAll('.peluche-card'),
        { y: 24, scale: 0.82 },
        {
          y: 0,
          scale: 1,
          duration: 0.6,
          stagger: 0.07,
          ease: 'back.out(1.8)',
          clearProps: 'transform',
        }
      );
    }
    lluvia();
  });
</script>

<div class="no-scrollbar relative z-10 flex h-full w-full flex-col overflow-y-auto px-3 py-4 sm:px-8 sm:py-6">
  <div bind:this={hoja} class="m-auto flex w-full max-w-4xl flex-col items-center text-center">
    <!-- Tarjeta principal cristal kawaii -->
    <div class="glass-kawaii relative w-full rounded-[2rem] px-4 py-5 sm:px-8 sm:py-7">
      <!-- Decoraciones de esquina -->
      <span class="pointer-events-none absolute left-4 top-3 text-lg sm:text-2xl opacity-85">💜</span>
      <span class="pointer-events-none absolute right-4 top-3 text-lg sm:text-2xl opacity-85">✨</span>

      <!-- Cabecera -->
      <div class="cabecera flex flex-col items-center">
        <span class="mb-2 inline-flex items-center gap-1.5 rounded-full bg-purple-100/95 border border-purple-300/80 px-3.5 py-0.5 text-[11px] sm:text-xs font-bold tracking-widest text-purple-700 uppercase shadow-xs">
          <span>✦</span>
          <span>BT21 Birthday Party · Borahae</span>
          <span>✦</span>
        </span>

        <h1 class="titulo titulo-borahae font-display text-3xl sm:text-5xl md:text-6xl font-bold leading-tight">
          {carta.titulo}
          <span class="pastel-bob ml-1">🎂</span>
        </h1>

        {#if carta.mensaje}
          <p class="mt-2 max-w-lg text-sm sm:text-base font-bold text-purple-800/95 leading-snug">
            {carta.mensaje}
          </p>
        {/if}

        <!-- Contador kawaii de mensajitos descubiertos -->
        <div class="mt-2.5 inline-flex items-center gap-1.5 rounded-full bg-white/85 border border-purple-200 px-3.5 py-1 text-xs font-bold text-purple-700 shadow-xs">
          <span>Mensajitos:</span>
          <div class="flex items-center gap-1">
            {#each carta.miembros as m (m.id)}
              <span
                class="inline-block h-2.5 w-2.5 rounded-full transition-transform duration-300 {vistos.includes(m.id) ? 'scale-125 shadow-xs' : 'bg-purple-200'}"
                style={vistos.includes(m.id) ? `background:${m.color}` : ''}
                title={m.nombre}
              ></span>
            {/each}
          </div>
          <span class="ml-0.5 text-[11px] text-purple-500">({vistos.length}/{carta.miembros.length})</span>
        </div>
      </div>

      <!-- Escenario centrado de los 7 peluchitos BT21 (4 arriba + 3 centrados en móvil, 7 en fila en desktop) -->
      <div class="mt-5 sm:mt-6 flex w-full flex-wrap items-stretch justify-center gap-2 sm:gap-3">
        {#each carta.miembros as m, i (m.id)}
          {@const seleccionado = activo?.id === m.id}
          {@const visto = vistos.includes(m.id)}
          <button
            type="button"
            class="peluche-card peluche group relative flex w-[80px] sm:w-[106px] shrink-0 flex-col items-center justify-between rounded-2xl p-1.5 sm:p-2.5 transition-transform duration-200 focus:outline-none {seleccionado
              ? 'bg-white shadow-lg -translate-y-1.5'
              : 'bg-white/65 hover:bg-white/90 hover:-translate-y-1 shadow-sm'}"
            style="--c:{m.color}; border: 2px solid {seleccionado ? m.color : 'rgba(255,255,255,0.9)'}; {seleccionado ? `box-shadow: 0 10px 24px -6px ${m.color}66;` : ''}"
            onclick={(e) => tocar(m, e, i)}
            aria-label="{m.nombre} ({m.miembro})"
          >
            <!-- Indicador de visto -->
            {#if visto}
              <span
                class="absolute right-1.5 top-1.5 z-20 flex h-4 w-4 items-center justify-center rounded-full text-[9px] text-white shadow-xs"
                style="background:{m.color}"
              >♥</span>
            {/if}

            <span class="flota relative flex items-center justify-center py-0.5" style="animation-delay:-{i * 0.45}s">
              <span class="brillo {seleccionado ? 'on' : ''}"></span>
              <img
                src="{base}assets/{m.id}.svg"
                alt={m.nombre}
                width="88"
                height="88"
                class="peluche-img relative block origin-bottom object-contain drop-shadow-[0_5px_8px_rgba(88,28,135,0.18)] transition-transform duration-300 group-hover:scale-105"
                draggable="false"
              />
            </span>

            <!-- Etiqueta de nombre + miembro de BTS -->
            <span
              class="mt-1.5 w-full rounded-xl bg-white/95 px-1.5 py-0.5 text-center shadow-2xs border border-purple-100"
            >
              <span class="font-display block text-xs sm:text-sm font-bold leading-tight" style="color:{m.color}">
                {m.nombre}
              </span>
              {#if m.miembro}
                <span class="block text-[10px] sm:text-[11px] font-bold text-purple-600/85 leading-tight">
                  {m.miembro}
                </span>
              {/if}
            </span>
          </button>
        {/each}
      </div>

      <!-- Globo de diálogo kawaii del peluche activo -->
      <div class="mt-4 sm:mt-5 flex min-h-22 w-full items-center justify-center">
        {#if activo}
          <div
            bind:this={globo}
            class="relative flex w-full max-w-xl items-center gap-3 sm:gap-4 rounded-3xl bg-white/95 px-4 py-3 sm:px-6 sm:py-3.5 text-left shadow-xl border-2"
            style="border-color:{activo.color}; box-shadow: 0 12px 30px -8px {activo.color}55;"
          >
            <!-- Mini avatar circular del peluche hablando -->
            <div
              class="flex h-12 w-12 sm:h-14 sm:w-14 shrink-0 items-center justify-center rounded-2xl p-1 shadow-inner"
              style="background:{activo.color}22; border: 1.5px solid {activo.color}55"
            >
              <img
                src="{base}assets/{activo.id}.svg"
                alt={activo.nombre}
                width="48"
                height="48"
                class="h-10 w-10 sm:h-12 sm:w-12 object-contain"
              />
            </div>

            <div class="flex-1">
              <div class="flex flex-wrap items-center gap-1.5">
                <span class="font-display text-sm sm:text-base font-bold" style="color:{activo.color}">
                  {activo.nombre}
                </span>
                {#if activo.miembro}
                  <span
                    class="rounded-full px-2 py-0.5 text-[10px] sm:text-xs font-bold text-white"
                    style="background:{activo.color}"
                  >
                    {activo.miembro} · BTS
                  </span>
                {/if}
              </div>
              <p class="mt-0.5 text-sm sm:text-base font-bold text-purple-950 leading-snug">
                “{activo.frase}”
              </p>
            </div>
          </div>
        {:else}
          <div class="flex items-center gap-2 rounded-2xl border border-dashed border-purple-300 bg-white/75 px-5 py-3 text-sm sm:text-base font-bold text-purple-700 shadow-xs">
            <span class="pastel-bob">👆</span>
            <span>Toca cualquier peluchito BT21 para leer su deseo de cumple ✨</span>
          </div>
        {/if}
      </div>

      <!-- Firma y pie de la carta -->
      <div class="mt-4 sm:mt-5 flex flex-col sm:flex-row items-center justify-between gap-3 border-t border-purple-200/80 pt-3.5">
        <div class="flex flex-col sm:items-start items-center">
          <p class="firma-borahae font-display text-2xl sm:text-3xl font-bold">
            {carta.firma} ⭐💜
          </p>
          <p class="text-xs sm:text-sm font-bold text-purple-500">{carta.fecha}</p>
        </div>

        <button
          type="button"
          class="btn-pop shimmer-badge inline-flex items-center gap-2 rounded-full border-2 border-white px-5 py-2.5 font-display text-sm sm:text-base font-bold text-white shadow-[0_8px_20px_rgba(168,85,247,0.35)]"
          onclick={lluvia}
        >
          <span>Magia Borahae</span>
          <span>💜🎊</span>
        </button>
      </div>
    </div>
  </div>
</div>

<style>
  .peluche-img {
    width: 68px;
    height: 68px;
  }
  @media (min-width: 640px) {
    .peluche-img {
      width: 86px;
      height: 86px;
    }
  }
  .flota {
    animation: flota 3.2s ease-in-out infinite;
  }
  @keyframes flota {
    50% {
      transform: translateY(-7px) rotate(2deg);
    }
  }
  .brillo {
    position: absolute;
    inset: 4%;
    border-radius: 50%;
    background: radial-gradient(circle, var(--c) 0%, transparent 70%);
    opacity: 0;
    transform: scale(0.6);
    transition: opacity 0.35s, transform 0.35s;
  }
  .brillo.on,
  .peluche:hover .brillo {
    opacity: 0.72;
    transform: scale(1.28);
  }
</style>
