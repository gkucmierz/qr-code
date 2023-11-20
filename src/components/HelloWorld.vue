<script setup>
import { ref } from 'vue';
import { QrcodeStream } from 'vue-qrcode-reader';

const torchAccess = ref(false);
const torch = ref(false);

const onDetect = (...a) => {
  console.log(a);
};

const onInit = (capabilities) => {
  torchAccess.value = !!capabilities.torch;
};

const paintOutline = (detectedCodes, ctx) => {
  for (const detectedCode of detectedCodes) {
    const [firstPoint, ...otherPoints] = detectedCode.cornerPoints

    ctx.strokeStyle = 'red'

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
  {{ torchAccess }}{{ torch }}
  <button @click="torch=!torch">switch torch</button>
  <qrcode-stream
    @detect="onDetect"
    @camera-on="onInit"
    :track="paintOutline"
    :constraints="{ facingMode: 'environment' }"
    :torch="torch"
    >
    <div class="camera-overlay">
    </div>
  </qrcode-stream>
</template>

<style scoped>
.camera-overlay {
  text-align: right;
  padding-top: 26px;
  padding-right: 20px;
/*  background: rgba(0, 100, 230, 30);*/
}
</style>
