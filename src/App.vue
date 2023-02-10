<script setup lang="ts">
import { gsap } from "gsap";
function handleKeydown(e: KeyboardEvent, HTMLElement: string) {
  switch (e.key) {
    case "ArrowDown":
    case "s":
      tween.to(HTMLElement, {
        y: yValue + 2,
      });
      yValue += 2;
      direction = "down";
      break;
    case "ArrowRight":
    case "d":
      tween.to(HTMLElement, {
        x: xValue + 2,
      });
      xValue += 2;
      direction = "right";
      break;
    case "ArrowLeft":
    case "a":
      tween.to(HTMLElement, {
        x: xValue - 2,
      });
      xValue -= 2;
      direction = "left";
      break;
    case "ArrowUp":
    case "w":
      tween.to(HTMLElement, {
        y: yValue - 2,
      });
      yValue -= 2;
      direction = "up";
      break;
    default:
      break;
  }
}

let divs: div[] = [];
const fixedDance = 40;
// Tween from left to right
const xAxisForAppearingMeat = Math.random() * 95;
const yAxisForAppearingMeat = Math.random() * 95;
let xValue = 0;
let yValue = 0;
const beafPath =
  "https://images.unsplash.com/photo-1529692236671-f1f6cf9683ba?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1770&q=80";
const img = document.createElement("img");
img.src = beafPath;
img.style.height = "8%";
img.style.width = "8%";
img.style.position = "relative";
img.style.top = `${yAxisForAppearingMeat}%`;
img.style.left = `${xAxisForAppearingMeat}%`;

img.style.zIndex = "1";
img.style.borderRadius = "50%";
let speed = 0.005;
let direction = "";

interface div {
  [x: string]: any;
  newDiv: HTMLDivElement;
}
function tail() {
  // gsap go to x and y and do not use lastdiv
  gsap.to(".snake", {
    duration: speed,
    delay: 0.1,
    ease: "none",
    x: xValue,
    y: yValue,
  });
}
// create a class that create a new div
class Div implements div {
  constructor() {
    this.newDiv = document.createElement("div");
    this.newDiv.className = `div${divs.length}`;
    this.newDiv.style.height = "50px";
    this.newDiv.style.width = "50px";
    this.newDiv.style.borderRadius = "50%";
    this.newDiv.style.position = "absolute";
    this.newDiv.style.backgroundColor = "green";
    this.newDiv.style.left = `${xValue + 20}px`;
    this.newDiv.style.top = `${yValue}px`;
    this.newDiv.style.zIndex = "1";
  }
  newDiv: HTMLDivElement;

  getNewDiv() {
    return this.newDiv;
  }

  updateDiv() {
    // get the last div placed through the dom
    const lastDiv = document.getElementsByClassName(
      `div${divs.length - 1}`
    )[0] as HTMLDivElement;

    return lastDiv;
  }
}
const newDiv = new Div().getNewDiv();
const tween = gsap.timeline({
  defaults: {
    duration: speed,
    ease: "none",
    onComplete: () => {
      document.getElementsByClassName("snakeArea")[0].appendChild(img);

      let localDirection = direction;
      if (localDirection === "right") {
        tween.to(".snake", {
          x: xValue + 2,
        });
        xValue += 2;
        newDiv.style.left = `${xValue - fixedDance}px`;
        newDiv.style.top = `${yValue}px`;
      }
      if (localDirection === "left") {
        tween.to(".snake", {
          x: xValue - 2,
        });
        xValue -= 2;
      }
      if (localDirection === "up") {
        tween.to(".snake", {
          y: yValue - 2,
        });
        yValue -= 2;
      }
      if (localDirection === "down") {
        tween.to(".snake", {
          y: yValue + 2,
        });
        yValue += 2;
        newDiv.style.left = `${xValue}px`;
        newDiv.style.top = `${yValue - fixedDance}px`;
      }

      let snakeSize = document
        .getElementsByClassName("snake")[0]
        .getClientRects();
      let snakeAreaSize = document
        .getElementsByClassName("snakeArea")[0]
        .getClientRects();
      let snakeLeft = snakeSize[0].left;
      let snakeRight = snakeSize[0].right;
      let snakeTop = snakeSize[0].top;
      let snakeBottom = snakeSize[0].bottom;
      let snakeAreaLeft = snakeAreaSize[0].left;
      let snakeAreaRight = snakeAreaSize[0].right;
      let snakeAreaTop = snakeAreaSize[0].top;
      let snakeAreaBottom = snakeAreaSize[0].bottom;

      const meatArea = document.querySelector("img")?.getClientRects()[0];
      if (
        snakeLeft + 10 <= snakeAreaLeft ||
        snakeRight >= snakeAreaRight ||
        snakeTop + 10 <= snakeAreaTop ||
        snakeBottom >= snakeAreaBottom
      ) {
        alert("Game Over");
        tween.pause();
      }
      const meatAreaLeft = meatArea?.left;
      const meatAreaRight = meatArea?.right;
      const meatAreaTop = meatArea?.top;
      const meatAreaBottom = meatArea?.bottom;

      if (meatAreaLeft && meatAreaRight && meatAreaTop && meatAreaBottom) {
        if (
          snakeLeft + 10 <= meatAreaRight &&
          snakeRight >= meatAreaLeft &&
          snakeTop + 10 <= meatAreaBottom &&
          snakeBottom >= meatAreaTop
        ) {
          img.style.left = `${Math.random() * 95}%`;
          img.style.top = `${Math.random() * 95}%`;
          let score = document.getElementsByClassName("score")[0];
          if (!score.textContent) return;
          score.textContent = `Score: ${
            Number(score.textContent.split(" ")[1]) + 10
          }`;
          divs.push(new Div());

          // query the last div
          const lastDiv = document.getElementsByClassName(
            `div${divs.length - 1}`
          )[0] as HTMLDivElement;

          lastDiv.style.left = `${snakeAreaLeft + 20}`;
          lastDiv.style.top = `${snakeAreaTop + 20}`;
          document.getElementsByClassName("snakeArea")[0].appendChild(lastDiv);
        }
      }
    },
  },
});

addEventListener("keydown", (e) => {
  e.preventDefault();

  handleKeydown(e, ".snake");
  handleKeydown(e, ".div0");
});
</script>

<template>
  <div class="snakeArea">
    <div class="snake"></div>
    <div class="left"></div>
    <div class="right"></div>
    <div class="top"></div>
    <div class="bottom"></div>
  </div>
  <p class="score">Score: 0</p>
</template>

<style scoped>
.score {
  padding-left: 5em;
  padding-top: 0.3em;
  position: relative;
  bottom: 0;
  left: 0;
  z-index: 1;
  font-size: 2rem;
  color: black;
  margin: 0;
  margin-left: 5%;
  margin-bottom: 5%;
}
.snake {
  height: 50px;
  width: 50px;
  background-color: green;
  position: relative;
  border-radius: 50%;
}

.snakeArea {
  margin-top: 5%;
  height: 90vh;
  width: 80%;
  margin: 0 auto;
  padding: 0;
  position: relative;
}

.right {
  height: 100%;
  width: 1%;
  background-color: black;
  position: absolute;
  right: 0%;
  top: 0;
}

.left {
  height: 100%;
  width: 1%;
  background-color: black;
  position: absolute;
  left: 0%;
  top: 0;
}

.top {
  height: 1%;
  width: 100%;
  background-color: black;
  position: absolute;
  top: 0;
  left: 0;
}
.bottom {
  height: 1%;
  width: 100%;
  background-color: black;
  position: absolute;
  bottom: 0;
  left: 0;
}
</style>
