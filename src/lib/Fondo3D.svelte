<script>
  import { onMount } from 'svelte';

  let canvas = $state();

  onMount(() => {
    let disposed = false;
    let rafId = 0;
    let cleanup = () => {};

    (async () => {
      // Carga diferida desde three.js/three.min.js local
      const localMod = await import('../../three.js/three.min.js');
      const THREE = localMod.WebGLRenderer
        ? localMod
        : localMod.default?.WebGLRenderer
          ? localMod.default
          : window.THREE;

      if (disposed || !canvas || !THREE) return;

      const renderer = new THREE.WebGLRenderer({
        canvas,
        alpha: true,
        antialias: true,
        powerPreference: 'low-power',
      });
      renderer.setPixelRatio(Math.min(window.devicePixelRatio || 1, 2));
      renderer.setSize(window.innerWidth, window.innerHeight, false);

      const scene = new THREE.Scene();
      const camera = new THREE.PerspectiveCamera(
        50,
        window.innerWidth / window.innerHeight,
        0.1,
        100
      );
      camera.position.z = 18;

      // Iluminación pastel suave estilo Borahae / BT21
      const ambient = new THREE.AmbientLight(0xf5e8ff, 1.4);
      scene.add(ambient);

      const dirLight1 = new THREE.DirectionalLight(0xffffff, 1.2);
      dirLight1.position.set(8, 14, 12);
      scene.add(dirLight1);

      const dirLight2 = new THREE.PointLight(0xc084fc, 1.8, 45);
      dirLight2.position.set(-10, -6, 8);
      scene.add(dirLight2);

      // Geometría de corazón 3D kawaii
      const heartShape = new THREE.Shape();
      heartShape.moveTo(0, 0.25);
      heartShape.bezierCurveTo(0, 0.25, -0.2, 0.65, -0.55, 0.65);
      heartShape.bezierCurveTo(-1.05, 0.65, -1.05, 0.05, -1.05, 0.05);
      heartShape.bezierCurveTo(-1.05, -0.35, -0.65, -0.72, 0, -1.05);
      heartShape.bezierCurveTo(0.65, -0.72, 1.05, -0.35, 1.05, 0.05);
      heartShape.bezierCurveTo(1.05, 0.05, 1.05, 0.65, 0.55, 0.65);
      heartShape.bezierCurveTo(0.2, 0.65, 0, 0.25, 0, 0.25);

      const extrudeSettings = {
        depth: 0.28,
        bevelEnabled: true,
        bevelSegments: 3,
        steps: 1,
        bevelSize: 0.14,
        bevelThickness: 0.14,
      };
      const heartGeo = new THREE.ExtrudeGeometry(heartShape, extrudeSettings);
      heartGeo.center();

      // Geometría de estrella diamante kawaii y burbuja pastel
      const starGeo = new THREE.OctahedronGeometry(0.58, 0);
      const bubbleGeo = new THREE.SphereGeometry(0.5, 20, 20);

      // Paleta BTS Borahae + BT21 Pastel
      const palette = [
        0xa855f7, // BTS Borahae Purple
        0xc084fc, // Lavanda brillante
        0xd8b4fe, // Lila pastel
        0xf472b6, // Rosa Cooky
        0xf9a8d4, // Rosa algodón
        0x7dd3fc, // Celeste Koya/Mang
        0xfde047, // Amarillo Chimmy/Estrella
      ];

      const items = [];
      const group = new THREE.Group();
      scene.add(group);

      const isMobile = window.innerWidth < 640;
      const count = isMobile ? 22 : 34;

      for (let i = 0; i < count; i++) {
        const colorHex = palette[i % palette.length];
        const kind = i % 3; // 0: corazón, 1: burbuja cristal, 2: estrella diamante
        let mesh;

        if (kind === 0) {
          const mat = new THREE.MeshStandardMaterial({
            color: colorHex,
            roughness: 0.3,
            metalness: 0.1,
            emissive: colorHex,
            emissiveIntensity: 0.2,
            transparent: true,
            opacity: 0.78,
          });
          mesh = new THREE.Mesh(heartGeo, mat);
          const s = 0.34 + (i % 5) * 0.09;
          mesh.scale.set(s, s, s);
        } else if (kind === 1) {
          const mat = new THREE.MeshPhongMaterial({
            color: colorHex,
            specular: 0xffffff,
            shininess: 90,
            emissive: colorHex,
            emissiveIntensity: 0.15,
            transparent: true,
            opacity: 0.48,
          });
          mesh = new THREE.Mesh(bubbleGeo, mat);
          const s = 0.46 + (i % 6) * 0.15;
          mesh.scale.set(s, s, s);
        } else {
          const mat = new THREE.MeshStandardMaterial({
            color: colorHex,
            roughness: 0.25,
            metalness: 0.2,
            emissive: colorHex,
            emissiveIntensity: 0.25,
            transparent: true,
            opacity: 0.72,
          });
          mesh = new THREE.Mesh(starGeo, mat);
          const s = 0.36 + (i % 4) * 0.11;
          mesh.scale.set(s, s * 1.25, s);
        }

        const spreadX = isMobile ? 16 : 28;
        const spreadY = 20;
        mesh.position.set(
          (Math.random() - 0.5) * spreadX,
          (Math.random() - 0.5) * spreadY,
          (Math.random() - 0.5) * 10 - 2
        );
        mesh.rotation.set(
          Math.random() * Math.PI,
          Math.random() * Math.PI,
          Math.random() * 0.5
        );

        group.add(mesh);
        items.push({
          mesh,
          baseX: mesh.position.x,
          speedY: 0.01 + Math.random() * 0.016,
          swayAmp: 0.3 + Math.random() * 0.6,
          swaySpeed: 0.6 + Math.random() * 1.1,
          rotSpeedX: (Math.random() - 0.5) * 0.012,
          rotSpeedY: (Math.random() - 0.5) * 0.018,
          phase: Math.random() * Math.PI * 2,
          spreadY,
        });
      }

      // Polvo de estrellas mágico
      const dotCanvas = document.createElement('canvas');
      dotCanvas.width = 64;
      dotCanvas.height = 64;
      const dCtx = dotCanvas.getContext('2d');
      const grad = dCtx.createRadialGradient(32, 32, 2, 32, 32, 30);
      grad.addColorStop(0, 'rgba(255, 255, 255, 1)');
      grad.addColorStop(0.35, 'rgba(233, 213, 255, 0.85)');
      grad.addColorStop(1, 'rgba(168, 85, 247, 0)');
      dCtx.fillStyle = grad;
      dCtx.fillRect(0, 0, 64, 64);
      const starTex = new THREE.CanvasTexture(dotCanvas);

      const dustCount = isMobile ? 90 : 150;
      const dustGeo = new THREE.BufferGeometry();
      const positions = new Float32Array(dustCount * 3);
      for (let i = 0; i < dustCount; i++) {
        positions[i * 3] = (Math.random() - 0.5) * 32;
        positions[i * 3 + 1] = (Math.random() - 0.5) * 24;
        positions[i * 3 + 2] = (Math.random() - 0.5) * 14 - 3;
      }
      dustGeo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
      const dustMat = new THREE.PointsMaterial({
        size: 0.55,
        map: starTex,
        transparent: true,
        opacity: 0.85,
        depthWrite: false,
        blending: THREE.AdditiveBlending,
      });
      const dust = new THREE.Points(dustGeo, dustMat);
      scene.add(dust);

      let targetX = 0;
      let targetY = 0;
      let boost = 0;

      const onPointerMove = (e) => {
        const x = (e.clientX / window.innerWidth) * 2 - 1;
        const y = -(e.clientY / window.innerHeight) * 2 + 1;
        targetX = x * 1.2;
        targetY = y * 0.8;
      };

      const onPop = (e) => {
        boost = 1.0;
        if (e?.detail?.color) {
          dirLight2.color.set(e.detail.color);
        }
      };

      const onResize = () => {
        if (!canvas) return;
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight, false);
      };

      let paused = false;
      const onVisibility = () => {
        paused = document.hidden;
      };

      window.addEventListener('pointermove', onPointerMove, { passive: true });
      window.addEventListener('esponja:pop', onPop);
      window.addEventListener('resize', onResize);
      document.addEventListener('visibilitychange', onVisibility);

      let t = 0;
      const animate = () => {
        if (disposed) return;
        rafId = requestAnimationFrame(animate);
        if (paused) return;

        t += 0.016;
        boost *= 0.94;

        camera.position.x += (targetX - camera.position.x) * 0.04;
        camera.position.y += (targetY - camera.position.y) * 0.04;
        camera.lookAt(0, 0, 0);

        const speedMult = 1 + boost * 3.0;

        for (let i = 0; i < items.length; i++) {
          const it = items[i];
          it.mesh.position.y += it.speedY * speedMult;
          if (it.mesh.position.y > it.spreadY / 2) {
            it.mesh.position.y = -it.spreadY / 2;
          }
          it.mesh.position.x =
            it.baseX + Math.sin(t * it.swaySpeed + it.phase) * it.swayAmp;
          it.mesh.rotation.x += it.rotSpeedX * speedMult;
          it.mesh.rotation.y += it.rotSpeedY * speedMult;
        }

        dust.rotation.y = t * 0.03;
        dust.rotation.x = Math.sin(t * 0.15) * 0.05;

        renderer.render(scene, camera);
      };

      animate();

      cleanup = () => {
        cancelAnimationFrame(rafId);
        window.removeEventListener('pointermove', onPointerMove);
        window.removeEventListener('esponja:pop', onPop);
        window.removeEventListener('resize', onResize);
        document.removeEventListener('visibilitychange', onVisibility);
        heartGeo.dispose();
        starGeo.dispose();
        bubbleGeo.dispose();
        dustGeo.dispose();
        starTex.dispose();
        renderer.dispose();
      };
    })();

    return () => {
      disposed = true;
      cleanup();
    };
  });
</script>

<canvas
  bind:this={canvas}
  class="pointer-events-none fixed inset-0 z-0 h-full w-full"
  aria-hidden="true"
></canvas>
