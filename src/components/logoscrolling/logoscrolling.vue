
<template>
  <section class="marqueeWrap" aria-label="Partners">
    <!-- fade edges -->
    <div class="fade fadeLeft" aria-hidden="true"></div>
    <div class="fade fadeRight" aria-hidden="true"></div>

    <!-- Line 1: scroll RIGHT (11 logos) -->
    <div class="row">
      <div class="track toRight">
        <div class="set">
          <LogoItem v-for="(src, i) in logosLine1" :key="'l1a'+i" :src="src" />
        </div>
        <div class="set" aria-hidden="true">
          <LogoItem v-for="(src, i) in logosLine1" :key="'l1b'+i" :src="src" />
        </div>
      </div>
    </div>

    <!-- Line 2: scroll LEFT (10 logos) -->
    <div class="row row2">
      <div class="track toLeft">
        <div class="set">
          <LogoItem v-for="(src, i) in logosLine2" :key="'l2a'+i" :src="src" />
        </div>
        <div class="set" aria-hidden="true">
          <LogoItem v-for="(src, i) in logosLine2" :key="'l2b'+i" :src="src" />
        </div>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { defineComponent, h } from 'vue';
/**
 * ✅ ใส่โลโก้ของคุณตรงนี้ (รวม 21)
 * Line 1: 11 โลโก้
 * Line 2: 10 โลโก้
 */
const logosLine1 = [
  "/logos/logo1.png",
  "/logos/logo2.png",
  "/logos/logo3.png",
  "/logos/logo4.png",
  "/logos/logo5.png",
  "/logos/logo6.png",
  "/logos/logo7.png",
  "/logos/logo8.png",
  "/logos/logo9.png",
  "/logos/logo10.png",
  "/logos/logo11.png",
 
];

const logosLine2 = [
  "/logos/logo1.png",
  "/logos/logo2.png",
  "/logos/logo3.png",
  "/logos/logo4.png",
  "/logos/logo5.png",
  "/logos/logo6.png",
  "/logos/logo7.png",
  "/logos/logo8.png",
  "/logos/logo9.png",
  "/logos/logo10.png",
  "/logos/logo11.png",
 
];

/** small local component */
const LogoItem = defineComponent({
  props: { src: { type: String, required: true } },
  setup(props: { src: string }) {
    return () =>
      h('div', { class: 'logoCard' }, [
        h('img', { class: 'logoImg', src: props.src, alt: '', loading: 'lazy' })
      ]);
  },
});
</script>

<style scoped>
.marqueeWrap{
  position: relative;
  width: min(1240px, 96vw);
  margin: 0 auto;
  padding: 10px 0 6px;
  overflow: hidden;
}

.row{
  position: relative;
  overflow: hidden;
  padding: 10px 0;
}

.row2{ padding-top: 6px; }

.track{
  display: flex;
  width: max-content;
  gap: 18px;
  will-change: transform;
}

.set{
  display: flex;
  align-items: center;
  gap: 18px;
}

/* direction + speed */
.toLeft{
  animation: marqueeLeft 22s linear infinite;
}
.toRight{
  animation: marqueeRight 26s linear infinite;
}

/* pause on hover (optional, feels premium) */
.marqueeWrap:hover .toLeft,
.marqueeWrap:hover .toRight{
  animation-play-state: paused;
}

/* logo card */
.logoCard{
  width: 132px;
  height: 62px;
  border-radius: 16px;
  display: grid;
  place-items: center;

  background: rgba(255,255,255,0.06);
  border: 1px solid rgba(255,255,255,0.10);
  backdrop-filter: blur(10px);
  box-shadow:
    0 18px 40px rgba(0,0,0,0.28),
    inset 0 1px 0 rgba(255,255,255,0.05);

  transform: translateZ(0);
  transition: transform .25s ease, background .25s ease, border-color .25s ease;
}

.logoCard:hover{
  transform: translateY(-2px);
  background: rgba(255,255,255,0.085);
  border-color: rgba(255,255,255,0.14);
}

.logoImg{
  width: 72%;
  height: 72%;
  object-fit: contain;
  filter: drop-shadow(0 10px 22px rgba(0,0,0,0.25));
  opacity: 0.92;
}

/* gradient edge fades */
.fade{
  position:absolute;
  top:0; bottom:0;
  width: 140px;
  pointer-events:none;
  z-index: 2;
}
.fadeLeft{
  left:0;
  background: linear-gradient(90deg, rgba(7,5,15,1), rgba(7,5,15,0));
}
.fadeRight{
  right:0;
  background: linear-gradient(270deg, rgba(7,5,15,1), rgba(7,5,15,0));
}

/* keyframes (infinite seamless because we duplicated .set) */
@keyframes marqueeLeft{
  from { transform: translateX(0); }
  to   { transform: translateX(-50%); }
}
@keyframes marqueeRight{
  from { transform: translateX(-50%); }
  to   { transform: translateX(0); }
}

/* responsive */
@media (max-width: 760px){
  .logoCard{ width: 112px; height: 54px; border-radius: 14px; }
  .track, .set{ gap: 14px; }
  .fade{ width: 90px; }
}
</style>
