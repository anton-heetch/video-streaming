<script setup>
import Hls from 'hls.js'
import { ref, onMounted } from 'vue'

const video = ref(null)

const isIOS = (function () {
  const iosQuirkPresent = function () {
    const audio = new Audio()

    audio.volume = 0.5
    return audio.volume === 1 // volume cannot be changed from "1" on iOS 12 and below
  }

  const isIOS = /iPad|iPhone|iPod/.test(navigator.userAgent)
  const isAppleDevice = navigator.userAgent.includes('Macintosh')
  const isTouchScreen = navigator.maxTouchPoints >= 1 // true for iOS 13 (and hopefully beyond)

  return isIOS || (isAppleDevice && (isTouchScreen || iosQuirkPresent()))
})()

onMounted(() => {
  if (Hls.isSupported()) {
    const hls = new Hls()
    const stream =
      'https://stream.avald.kz/aa954d36-cc44-4db3-bda3-acbf75f6aebb/index.m3u8'

    hls.loadSource(stream)
    hls.attachMedia(video.value)
    hls.on(Hls.Events.MANIFEST_PARSED, () => {
      video.value.play()
    })
  }
  console.log(isIOS)
})
</script>
<template>
  <h3>Video Stream</h3>
  <video v-if="!isIOS" ref="video" controls autoplay muted playsinline></video>
  <video v-else autoplay muted playsinline>
    <source
      src="https://stream.avald.kz/aa954d36-cc44-4db3-bda3-acbf75f6aebb/index.m3u8"
      type="video/mp4"
    />
  </video>
</template>
<style lang="scss">
video {
  width: 100%;
  height: auto;
}
</style>
