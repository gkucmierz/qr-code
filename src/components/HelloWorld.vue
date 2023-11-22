<script setup>
import { ref } from 'vue';
import { QrcodeStream } from 'vue-qrcode-reader';

const torchAccess = ref(false);
const torch = ref(false);
const detected = ref();
const facingMode = ref('environment');
const error = ref('');
const paused = ref(false);

const onDetect = data => {
  // setTimeout(() => {
    detected.value = data;
  // }, 1000);
  // console.log(data);
  // paused.value = true;
  // setTimeout(() => paused.value = false, 0);
};

const onInit = (capabilities) => {
  torchAccess.value = true;//!!capabilities.torch;
};

const paintOutline = (detectedCodes, ctx) => {
  for (const detectedCode of detectedCodes) {
    const [firstPoint, ...otherPoints] = detectedCode.cornerPoints;
    ctx.strokeStyle = 'red';
    ctx.beginPath();
    ctx.moveTo(firstPoint.x, firstPoint.y);
    for (const { x, y } of otherPoints) {
      ctx.lineTo(x, y);
    }
    ctx.lineTo(firstPoint.x, firstPoint.y);
    ctx.closePath();
    ctx.stroke();
  }
};

const switchCamera = () => {
  const modes = ['user', 'environment'];
  facingMode.value = modes[(modes.indexOf(facingMode.value) + 1) % modes.length];
};

const onError = (err) => {
  error.value = err;
};

</script>

<template>
  <div>
    <qrcode-stream
      @detect="onDetect"
      @camera-on="onInit"
      @error="onError"
      :paused="paused"
      :track="paintOutline"
      :constraints="{ facingMode: facingMode }"
      :torch="torch"
      >
      <div class="camera-overlay">
        <span class="pi pi-camera camera-icon" @click="switchCamera"></span>
        <span v-if="torchAccess" class="pi pi-sun torch-icon" :class="{on: torch}" @click="torch = !torch"></span>
      </div>
    </qrcode-stream>
  </div>
  <pre style="color: red">
    {{error}}
  </pre>
  <pre>
    {{ JSON.stringify(detected, null, '  ') }}
  </pre>
</template>

<style scoped>
.camera-overlay {
  display: flex;
  justify-content: space-between;
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
.camera-icon {
  margin: 20px 16px;
  cursor: pointer;
  font-size: 2rem;
  color: #ccc;
  text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
}
</style>
