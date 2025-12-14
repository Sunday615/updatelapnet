<template>
  <div class="model-wrapper">
    <model-viewer
      :src="src"
      :poster="poster"
      auto-rotate
      camera-controls
      shadow-intensity="1"
      exposure="1"
      ar
      ar-modes="webxr scene-viewer quick-look"
      style="width: 100%; height: 100%;"
    >
    </model-viewer>
  </div>
</template>

<script setup>
defineProps({
  src: {
    type: String,
    required: true
  },
  poster: {
    type: String,
    default: ''
  }
})
</script>

<style scoped>
.model-wrapper {
  width: 100%;
  height: 500px;
  background: #000; /* เข้ากับ Dark theme 🔥 */
  border-radius: 16px;
  overflow: hidden;
}
</style>