<script>
  import { onMount, afterUpdate, onDestroy } from "svelte";

  let canvas;
  let ctx;
  let animationFrameId;

  let gradientPosition = 0;
  let mouse = { x: 0, y: 0 };
  let isMouseOver = false;
  let isDoubleClickEnabled = false;

  onMount(() => {
    canvas = document.getElementById("background-canvas");
    ctx = canvas.getContext("2d");

    resizeCanvas();

    window.addEventListener("resize", resizeCanvas);
    window.addEventListener("mousemove", handleMouseMove);
    window.addEventListener("mouseover", handleMouseOver);
    window.addEventListener("mouseout", handleMouseOut);
    canvas.addEventListener("contextmenu", handleContextMenu);
    canvas.addEventListener("dblclick", handleDoubleClick);

    animate();
  });

  afterUpdate(() => {
    resizeCanvas();
  });

  onDestroy(() => {
    window.removeEventListener("resize", resizeCanvas);
    window.removeEventListener("mousemove", handleMouseMove);
    window.removeEventListener("mouseover", handleMouseOver);
    window.removeEventListener("mouseout", handleMouseOut);
    canvas.removeEventListener("contextmenu", handleContextMenu);
    canvas.removeEventListener("dblclick", handleDoubleClick);
    cancelAnimationFrame(animationFrameId);
  });

  function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  }

  function handleMouseMove(event) {
    mouse.x = event.clientX;
    mouse.y = event.clientY;
  }

  function handleMouseOver() {
    isMouseOver = true;
  }

  function handleMouseOut() {
    isMouseOver = false;
  }

  function handleContextMenu(event) {
    event.preventDefault();
  }

  function handleDoubleClick(event) {
    event.preventDefault();
  }

  function animate() {
    animationFrameId = requestAnimationFrame(animate);

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    drawBackground();
    repelWaves();
    gradientPosition += 0.6; // Increase the speed of the waves
  }

  function drawBackground() {
    ctx.fillStyle = "rgb(147, 197, 253)";
    ctx.fillRect(0, 0, canvas.width, canvas.height);
  }

  function repelWaves() {
    const layerCount = 5;
    const layers = [
      { amplitude: 50, frequency: 0.01, phaseShift: 0.01, opacity: 0.4 },
      { amplitude: 55, frequency: 0.02, phaseShift: 0.02, opacity: 0.3 },
      { amplitude: 40, frequency: 0.03, phaseShift: 0.03, opacity: 0.2 },
      { amplitude: 45, frequency: 0.04, phaseShift: 0.04, opacity: 0.1 },
      { amplitude: 35, frequency: 0.05, phaseShift: 0.05, opacity: 0.05 },
    ];

    const repelDistance = 200;

    for (let i = 0; i < layerCount; i++) {
      const yOffset = canvas.height / 2 + i * 60; // Adjust the vertical position of the waves

      ctx.beginPath();

      ctx.moveTo(0, yOffset);

      for (let x = 0; x < canvas.width; x++) {
        const grooveFactor = Math.sin(
          (x / canvas.width) * Math.PI + (gradientPosition / 100) * Math.PI
        );

        let y =
          yOffset +
          (layers[i].amplitude + layers[i].amplitude * grooveFactor) *
            Math.sin(
              layers[i].frequency * x + layers[i].phaseShift * gradientPosition
            );

        if (isMouseOver) {
          const distanceToMouse = Math.sqrt(
            Math.pow(x - mouse.x, 2) + Math.pow(y - mouse.y, 2)
          );
          const repelAmount = (repelDistance - distanceToMouse) / repelDistance;

          if (distanceToMouse < repelDistance) {
            y += repelAmount * (yOffset - y);
          }
        }

        ctx.lineTo(x, y);
      }

      ctx.lineTo(canvas.width, canvas.height);
      ctx.lineTo(0, canvas.height);
      ctx.closePath();

      ctx.fillStyle = `rgba(0, 0, 0, ${layers[i].opacity})`;
      ctx.fill();
    }
  }
</script>

<div class="relative">
  <canvas
    id="background-canvas"
    class="bg-gradient-to-r from-black via-gray-900 to-gray-700"
  />
</div>

<style>
  canvas {
    position: fixed;
    top: 0;
    left: 0;
  }
</style>
