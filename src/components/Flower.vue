<style scoped>
button:hover {
  border-color: #646cff;
}
button:focus,
button:focus-visible {
  outline: 4px auto -webkit-focus-ring-color;
}

.svg-container {
  width: 100%;
  height: 50vh;
  background-color: #adade8;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  padding: 2rem;
}
.svg-axis {
  stroke: rgb(255, 255, 255);
  stroke-width: 2px;
}
.svg-flower-center {
  stroke: orange;
  stroke-width: 2px;
  fill: yellow;
}
.svg-flower-petal {
  stroke: rgb(180, 180, 180);
  stroke-width: 2px;
  fill: white;
}
</style>

<template>
  <div class="card">
    <button type="button" @click="petalCount++">
      count is {{ petalCount }}
    </button>
    <button id="drawPetals" type="button" @click="drawPetals">
      re-draw the flower
    </button>
    <input
      type="number"
      id="petals-number"
      min="1"
      max="30"
      v-model="petalCount"
      placeholder="number of petals "
    />
    <input
      type="number"
      id="petals-length"
      min="50"
      max="60"
      v-model="petalLength"
      placeholder="length of petals "
    />
    <p class="read-the-docs">
      Here is a nice petal's flower. <br />
      Using this polar equation :<br />
      <strong
        >r = {{ petalLength }} x (2 + 2 x cos( {{ petalCount }} x θ))
      </strong>
    </p>
    <div class="svg-container">
      <svg height="500" width="500">
        <line x1="250" y1="0" x2="250" y2="500" class="svg-axis" />
        <line x1="0" y1="250" x2="500" y2="250" class="svg-axis" />
        <polyline :points="coordinatesString" class="svg-flower-petal" />
        <circle cx="250" cy="250" r="40" class="svg-flower-center" />
      </svg>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from "vue";
import { Angle, Point } from "ts-simple-2d-geometry";

const props = defineProps({
  initialPetals: {
    type: Number,
    required: true,
    default: 6,
  },
});

const petalCount = ref(6);
const petalLength = ref(45);
const coordinatesString = ref("0,0 ");

function calcPetalsCoordinates(petalNumber: number, petalLength: number) {
  const svg = document.querySelector<SVGSVGElement>("svg")!;
  const svgWidth = svg.clientWidth;
  const svgHeight = svg.clientHeight;
  const svgCenterX = svgWidth / 2;
  const svgCenterY = svgHeight / 2;
  let coordinatesString = ""; // to accumulate the points coordinates
  // let's center the drawing on the svg
  const offsetX = svgCenterX;
  const offsetY = svgCenterY;
  console.log(`## in calcPetalsCoordinates PetalCount : ${petalNumber}`);
  for (let angle = 0; angle <= 360; angle += 1) {
    const myAngle = new Angle(angle, "degrees");
    let radius =
      petalLength * (2 + 2 * Math.cos(petalNumber * myAngle.toRadians()));
    let TempPoint = Point.fromPolar(radius, myAngle, `P-${angle}`);
    // and move the point so it's centered
    TempPoint.moveRel(offsetX, offsetY);
    coordinatesString += TempPoint.toString(",", false) + " ";
  }
  return coordinatesString;
}

//draw a svg flower with then number of given petals
const drawPetals = () => {
  coordinatesString.value = calcPetalsCoordinates(
    petalCount.value,
    petalLength.value,
  );
};

onMounted(() => {
  console.log(
    `## Flower.vue in onMounted initialPetals : ${props.initialPetals}`,
  );
  petalCount.value = props.initialPetals;
  drawPetals();
});
</script>
