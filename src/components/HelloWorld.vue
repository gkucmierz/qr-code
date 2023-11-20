<script setup>
import { ref } from 'vue';
import { QrcodeStream } from 'vue-qrcode-reader';

const torchAccess = ref(false);
const torch = ref(false);
const detected = ref();

const onDetect = data => {
  detected.value = data;
};

const onInit = (capabilities) => {
  torchAccess.value = !!capabilities.torch;
};

const paintOutline = (detectedCodes, ctx) => {
  for (const detectedCode of detectedCodes) {
    const [firstPoint, ...otherPoints] = detectedCode.cornerPoints
    ctx.strokeStyle = 'red';
    ctx.beginPath()
    ctx.moveTo(firstPoint.x, firstPoint.y)
    for (const { x, y } of otherPoints) {
      ctx.lineTo(x, y)
    }
    ctx.lineTo(firstPoint.x, firstPoint.y)
    ctx.closePath()
    ctx.stroke()
  }
};

</script>

<template>
  <qrcode-stream
    @detect="onDetect"
    @camera-on="onInit"
    :track="paintOutline"
    :constraints="{ facingMode: 'environment' }"
    :torch="torch"
    >
    <div class="camera-overlay">
      <span v-if="torchAccess" class="pi pi-sun torch-icon" :class="{on: torch}" @click="torch = !torch"></span>
    </div>
  </qrcode-stream>
  <pre>
    {{ JSON.stringify(detected, null, '  ') }}
  </pre>
</template>

<style scoped>
.camera-overlay {
  text-align: right;
  height: 100%;
/*  background: rgba(0, 100, 230, 0.3);*/
}
.torch-icon {
  margin: 20px 16px;
  cursor: pointer;
  font-size: 2rem;
  color: #ccc;
  text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
}
.torch-icon.on {
  color: #fff;
}
</style>
