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

  const nebulae: Attachment = (el) => {
    FOG({
      el,
      THREE,
      highlightColor: 0x27272,
      midtoneColor: 0x7a02b3,
      lowlightColor: 0x1050,
      baseColor: 0x0,
      blurFactor: 0.6,
      speed: 0.25,
      zoom: 0.5,
    });
  };

  let canvas: HTMLCanvasElement;

  const resize = () => {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  };

  const desired = (): number => Math.round(Math.sqrt(canvas.width * canvas.height) / 2);

  const random = (n: number): Star[] => [...Array(n)].map(_ => ({
    x: Math.random() * canvas.width,
    y: Math.random() * canvas.height,
    r: Math.random() * 0.75 + 0.5,
    vx: Math.random() * 0.15 - 0.075,
    vy: Math.random() * 0.15 - 0.075,
  }));

  const galaxy: Attachment = (el) => {
    canvas = el as HTMLCanvasElement;
    resize();
    const ctx = canvas.getContext("2d")!;
    ctx.fillStyle = "white";
    ctx.strokeStyle = "white";
    ctx.lineWidth = 0.5;
    let stars = random(desired());

    const frame = () => {
      requestAnimationFrame(frame);

      // clear
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // count
      if (stars.length !== desired()) {
        stars = random(desired());
      }

      for (let star of stars) {
        // draw
        ctx.beginPath();
        ctx.arc(star.x, star.y, star.r, 0, 2 * Math.PI);
        ctx.fill();

        if (star.r > 1.2) {
          ctx.beginPath();
          ctx.moveTo(star.x - 4, star.y);
          ctx.lineTo(star.x + 4, star.y);
          ctx.moveTo(star.x, star.y - 4);
          ctx.lineTo(star.x, star.y + 4);
          ctx.stroke();
        }

        // bounce
        if (star.x < 0 && star.vx < 0 || star.x > canvas.width && star.vx > 0) 
          star.vx *= -1; 
        if (star.y < 0 && star.vy < 0 || star.y > canvas.height && star.vy > 0)
          star.vy *= -1; 

        // move
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

<svelte:window onresize={resize}/>

<main 
  class="fixed inset-0 bg-black overflow-hidden flex justify-center items-center" 
  {@attach nebulae}
>
  <canvas class="absolute inset-0" {@attach galaxy}></canvas>

  <a 
    href="https://github.com/shadowninja55/"
    target="_blank"
  >
    <span class="font-sans text-white font-bold text-7xl md:text-8xl tracking-tighter italic opacity-50 hover:opacity-100 transition-opacity duration-300 ease-in-out">
      shadow
    </span>
  </a>
</main>

