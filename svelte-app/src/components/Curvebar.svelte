<script>
  import { onMount } from "svelte";

  let angle = 0;

  onMount(() => {
    const curvebar = document.getElementById("curvebar");
    const icons = curvebar.querySelectorAll(".icon");
    const iconsCount = icons.length;
    const angleStep = 360 / iconsCount;

    // calculate the position of each icon by angleStep and size of curvebar
    icons.forEach((icon, index) => {
      const angle = angleStep * index;
      const radius = curvebar.offsetWidth / 2.25;
      const iconSize = icon.offsetWidth;
      const iconAngle = (angle * Math.PI) / 180;

      const x = radius + radius * Math.cos(iconAngle) - iconSize / 2;
      const y = radius + radius * Math.sin(iconAngle) - iconSize / 2;

      icon.style.transform = `translate(${x}px, ${y}px)`;
    });

    // increase size of icon on mouse hover
    const innerIcons = curvebar.querySelectorAll(".icon .emoji");
    innerIcons.forEach((innerIcon, index) => {
      innerIcon.addEventListener("mouseenter", () => {
        innerIcon.style.transform = "scale(1.35)";

        // decrease size of other icons
        innerIcons.forEach((innerIcon2, index2) => {
          if (index2 !== index) {
            const dist = Math.abs(index - index2);
            if (dist < 2) {
              innerIcon2.style.transform = `scale(1.1)`;
            } else if (dist > 2 && dist < 4) {
              innerIcon2.style.transform = `scale(0.8)`;
            } else {
              innerIcon2.style.transform = `scale(0.65)`;
            }
          }
        });
      });
      innerIcon.addEventListener("mouseleave", () => {
        innerIcons.forEach((innerIcon2) => {
          innerIcon2.style.transform = "scale(1)";
        });
      });
    });

    // rotate curvebar on mouse scroll
    window.addEventListener("wheel", (event) => {
      angle += event.deltaY / 10;
      curvebar.style.transform = `rotate(${angle}deg)`;
    });
  });
</script>

<div class="curvebar-container">
  <div class="curvebar" id="curvebar">
    <div class="empty-space" />
    <a class="icon" href="#">
      <span class="emoji">👤</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">⚙️</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">💬</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">🗑️</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">🔋</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">📅</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">☁️</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">📶</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">✉️</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">🗑️</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">🔋</span>
    </a>
    <a class="icon" href="#">
      <span class="emoji">📅</span>
    </a>
    <div class="empty-space" />
  </div>
</div>

<style>
  .curvebar-container {
    position: fixed;
    top: -15%;
    left: -10%;
    z-index: 99;
  }

  .curvebar {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 300px;
    height: 300px;
    border-radius: 50%;
    background-color: #392338;
    overflow: hidden;
    position: relative;
  }

  .icon {
    position: absolute;
    top: 0;
    left: 0;
    width: 50px;
    height: 50px;
    transition: transform 0.5s ease;
    text-decoration: none;
  }

  .icon .emoji {
    font-size: 2em;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
    transition: transform 0.5s ease;
  }

  .empty-space {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    width: 140px;
    height: 140px;
    background-color: #93c5fd;
  }
</style>
