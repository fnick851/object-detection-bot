<template>
  <h1>Object Detection Bot</h1>
  <div class="video-row">
    <video ref="videoRef" width="478" height="360" autoplay />
  </div>
  <div class="guess-row">
    <p><i>Show me something ...</i></p>
    <p v-if="guess" class="guess-answer">
      <strong>I see {{ guess }}</strong>
    </p>
  </div>
</template>

<script>
import { onMounted, ref } from "vue";

export default {
  name: "App",
  setup() {
    const videoRef = ref(null);
    const guess = ref(null);

    onMounted(() => {
      const video = videoRef.value;
      function detectObject(model, video) {
        model.detect(video).then((predictions) => {
          guess.value = predictions[0].class;
        });
        window.requestAnimationFrame(() => detectObject(model, video));
      }
      navigator.mediaDevices
        .getUserMedia({
          video: true,
        })
        .then((stream) => {
          video.srcObject = stream;
          video.addEventListener("loadeddata", () => {
            cocoSsd.load().then((model) => {
              detectObject(model, video);
            });
          });
        });
    });

    return {
      videoRef,
      guess,
    };
  },
};
</script>

<style scoped>
h1 {
  text-align: center;
}
.video-row {
  display: flex;
  justify-content: center;
}
video {
  border: 3px solid lightgrey;
  border-radius: 3px;
}
.guess-row {
  text-align: center;
}
.guess-answer {
  color: darkolivegreen;
}
</style>
