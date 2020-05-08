<script>
  import { onMount } from "svelte";
  import { interactable } from "./interactable";

  onMount(() => {
    const elements = [...document.querySelectorAll(".item")];

    if (elements) {
      for (const el of elements) {
        interactable(el);
      }
    }
  });

  let canvas;
  let element;
  const mouse = {
    x: 0,
    y: 0,
    startX: 0,
    startY: 0
  };

  function mouseDown(e) {
    if (e.target.id === "container") {
      const rects = [...canvas.querySelectorAll(".selection")];

      if (rects) {
        for (const rect of rects) {
          canvas.removeChild(rect);
        }
      }

      mouse.startX = mouse.x;
      mouse.startY = mouse.y;
      element = document.createElement("div");
      element.className = "selection";
      element.style.border = "1px dashed black";
      element.style.position = "absolute";
      element.style.left = mouse.x + "px";
      element.style.top = mouse.y + "px";
      canvas.appendChild(element);
    }
  }

  function setMousePosition(e) {
    const ev = e || window.event;

    if (ev.pageX) {
      mouse.x = ev.pageX + window.pageXOffset;
      mouse.y = ev.pageY + window.pageYOffset;
    } else if (ev.clientX) {
      mouse.x = ev.clientX + document.body.scrollLeft;
      mouse.y = ev.clientY + document.body.scrollTop;
    }
  }

  function mouseMove(e) {
    setMousePosition(e);
    if (element) {
      element.style.width = Math.abs(mouse.x - mouse.startX) + "px";
      element.style.height = Math.abs(mouse.y - mouse.startY) + "px";
      element.style.left =
        mouse.x - mouse.startX < 0 ? mouse.x + "px" : mouse.startX + "px";
      element.style.top =
        mouse.y - mouse.startY < 0 ? mouse.y + "px" : mouse.startY + "px";
    }
  }

  function mouseUp(e) {
    element = null;

    const rect = canvas.querySelector(".selection");
    const boxes = [...canvas.querySelectorAll(".item")];

    if (rect) {
      const inBounds = [];

      for (const box of boxes) {
        if (isInBounds(rect, box)) {
          inBounds.push(box);
        } else {
          box.style.boxShadow = "none";
          box.classList.remove("selected");
        }
      }

      if (inBounds.length >= 2) {
        for (const box of inBounds) {
          box.style.boxShadow = "0 0 3pt 3pt hsl(141, 53%, 53%)";
          box.classList.add("selected");
        }
      }

      if (rect) canvas.removeChild(canvas.querySelector(".selection"));
    }
  }

  function isInBounds(obj1, obj2) {
    const a = obj1.getBoundingClientRect();
    const b = obj2.getBoundingClientRect();

    return (
      a.x < b.x + b.width &&
      a.x + a.width > b.x &&
      a.y < b.y + b.height &&
      a.y + a.height > b.y
    );
  }
</script>

<style>
  #container {
    background: lightgray;
    height: 100vh;
  }
  .item {
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    background: lightblue;
    width: 100px;
    height: 100px;
    cursor: pointer;
    border-radius: 10px;
    padding: 1em;
    touch-action: none;
    transition: box-shadow 0.5s;
  }
  .item:hover {
    -webkit-box-shadow: 0px 10px 15px 0px rgba(0, 0, 0, 0.1);
    -moz-box-shadow: 0px 10px 15px 0px rgba(0, 0, 0, 0.1);
    box-shadow: 0px 10px 15px 0px rgba(0, 0, 0, 0.1);
  }
  .item:nth-child(1) {
    top: 100px;
    left: 100px;
  }
  .item:nth-child(2) {
    top: 30px;
    left: 300px;
  }
  .item:nth-child(3) {
    top: 400px;
    left: 320px;
  }
  .item:nth-child(4) {
    top: 250px;
    left: 200px;
  }
</style>

<div bind:this={canvas} id="container" on:mouseup={mouseUp} on:mousedown={mouseDown} on:mousemove={mouseMove}>
  <div class="item" data-x="0" data-y="0"><span>Item 1</span></div>
  <div class="item" data-x="0" data-y="0"><span>Item 2</span></div>
  <div class="item" data-x="0" data-y="0"><span>Item 3</span></div>
  <div class="item" data-x="0" data-y="0"><span>Item 4</span></div>
</div>