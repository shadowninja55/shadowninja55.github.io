<script lang="ts">
  import type { Attachment } from "svelte/attachments";
  import * as THREE from "three";
  import FOG from "vanta/dist/vanta.fog.min";

  type Star = {
    x: number;
    y: number;
    r: number;
    vx: number;
    vy: number;
  };

  const vanta: Attachment = (el) => {
    FOG({
      el,
      THREE,
      highlightColor: 0x23f52,
      midtoneColor: 0x2d06ac,
      lowlightColor: 0x110078,
      baseColor: 0x000000,
      blurFactor: 0.6,
      speed: 0.5,
      zoom: 0.5,
    });
  };

  const stars: Attachment = (el) => {
    const canvas = el as HTMLCanvasElement;
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    const ctx = canvas.getContext("2d")!;

    let stars = [];
    for (let i = 0; i < Math.sqrt(canvas.width * canvas.height) / 2; i++) {
      stars.push({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height,
        r: Math.random() * 0.75 + 0.25,
        vx: Math.random() * 0.1 - 0.05,
        vy: Math.random() * 0.1 - 0.05,
      });
    }

    const frame = () => {
      requestAnimationFrame(frame);
      ctx.fillStyle = "white";
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      for (let star of stars) {
        ctx.beginPath();
        ctx.arc(star.x, star.y, star.r, 0, 2 * Math.PI);
        ctx.fill();
        if (star.x < 0 && star.vx < 0 || star.x > canvas.width && star.vx > 0) 
          star.vx *= -1; 
        if (star.y < 0 && star.vy < 0 || star.y > canvas.height && star.vy > 0)
          star.vy *= -1; 
        star.x += star.vx;
        star.y += star.vy;
      }
    };
    frame();
  };
</script>

<svelte:head>
  <title>
    shadow
  </title>
</svelte:head>

<main 
  class="fixed inset-0 bg-black overflow-hidden flex justify-center items-center" 
  {@attach vanta}
>
  <canvas class="absolute inset-0" {@attach stars}></canvas>

  <a 
    href="https://github.com/shadowninja55/"
    target="_blank"
  >
    <span class="font-sans text-white font-bold text-7xl md:text-8xl tracking-tighter italic opacity-50 hover:opacity-100 transition-opacity duration-300 ease-in-out">
      shadow
    </span>
  </a>
</main>

