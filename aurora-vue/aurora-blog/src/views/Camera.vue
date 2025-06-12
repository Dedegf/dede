<template>
  <div class="camera-page">
    <Breadcrumb :current="t('menu.camera')" />
    <div class="flex flex-col items-center mt-4">
      <video ref="video" autoplay playsinline class="w-full max-w-md rounded" />
      <canvas ref="canvas" class="hidden" />
      <img v-if="photo" :src="photo" class="snapshot mt-4" />
      <div class="flex space-x-2 mt-4">
        <button class="p-2 bg-blue-500 text-white rounded" @click="startCamera">{{ t('camera.start') }}</button>
        <button class="p-2 bg-green-500 text-white rounded" @click="takePhoto">{{ t('camera.take') }}</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import { useI18n } from 'vue-i18n'
import Breadcrumb from '@/components/Breadcrumb.vue'

export default defineComponent({
  name: 'Camera',
  components: { Breadcrumb },
  setup() {
    const { t } = useI18n()
    const video = ref<HTMLVideoElement | null>(null)
    const canvas = ref<HTMLCanvasElement | null>(null)
    const stream = ref<MediaStream | null>(null)
    const photo = ref('')

    const startCamera = async () => {
      try {
        stream.value = await navigator.mediaDevices.getUserMedia({ video: true })
        if (video.value) {
          video.value.srcObject = stream.value
        }
      } catch (err) {
        console.error(err)
      }
    }

    const takePhoto = () => {
      if (!video.value || !canvas.value) return
      const ctx = canvas.value.getContext('2d')
      if (!ctx) return
      canvas.value.width = video.value.videoWidth
      canvas.value.height = video.value.videoHeight
      ctx.drawImage(video.value, 0, 0)
      photo.value = canvas.value.toDataURL('image/png')
    }

    return { t, video, canvas, photo, startCamera, takePhoto }
  }
})
</script>

<style scoped>
.snapshot {
  width: 100%;
  max-width: 400px;
}
</style>
