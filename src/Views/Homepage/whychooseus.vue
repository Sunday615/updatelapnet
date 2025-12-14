<template>
  <section ref="root" class="why">
    <!-- subtle glow/noise -->
    <div class="whyBg" aria-hidden="true">
      <div class="glow g1"></div>
      <div class="glow g2"></div>
      <div class="noise"></div>
    </div>

    <div class="whyInner">
      <header class="whyHead">
        <div class="kicker">WHY CHOOSE US</div>

        <h2 class="title">
          <span class="titleStrong">{{ title }}</span>
          <span class="titleAccent">.</span>
        </h2>

        <p class="desc">{{ description }}</p>
      </header>

      <div class="stats">
        <!-- Box 1 -->
        <article ref="card1" class="card">
          <div class="cardTop">
            <div class="iconOrb" aria-hidden="true">
              <span class="iconLine"></span>
            </div>

            <div class="meta">
              <div class="label">{{ stat1Label }}</div>
              <div class="value">
                <span ref="count1El" class="count">0</span>
            
              </div>
            </div>
          </div>

          <p class="sub">{{ stat1Sub }}</p>
        </article>

        <!-- Box 2 -->
        <article ref="card2" class="card cardAccent">
          <div class="cardTop">
            <div class="iconOrb" aria-hidden="true">
              <span class="iconLine"></span>
            </div>

            <div class="meta">
              <div class="label">{{ stat2Label }}</div>
              <div class="value">
                <span ref="count2El" class="count">0</span>
                <span class="suffix">{{ stat2Suffix }}</span>
              </div>
            </div>
          </div>

          <p class="sub">{{ stat2Sub }}</p>
        </article>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";
import { gsap } from "gsap";

type Props = {
  title?: string;
  description?: string;

  stat1Value?: number;
  stat1Label?: string;
  stat1Suffix?: string;
  stat1Sub?: string;

  stat2Value?: number;
  stat2Label?: string;
  stat2Suffix?: string;
  stat2Sub?: string;
};

const props = withDefaults(defineProps<Props>(), {
  title: "Built for real results",
  description:
    "We deliver secure, scalable experiences with modern design, clear performance, and dedicated support — helping your network grow with confidence.",

  stat1Value: 18,
  stat1Label: "Member Banks",
  stat1Suffix: "+",
  stat1Sub: "19 ທະນາຄານສະມາຊິກທີ່ເຂົ້າຮ່ວມກັບ Lao National Payment Network.",

  stat2Value: 2,
  stat2Label: "Daily Transactions",
  stat2Suffix: "+",
  stat2Sub: "2 ກະເປົາອີເລັກໂທນິກທີ່ເຂົ້າຮ່ວມກັບ Lao National Payment Network.",
});

const root = ref<HTMLElement | null>(null);
const card1 = ref<HTMLElement | null>(null);
const card2 = ref<HTMLElement | null>(null);

const count1El = ref<HTMLElement | null>(null);
const count2El = ref<HTMLElement | null>(null);

let io: IntersectionObserver | null = null;
let played = false;

const animateCount = (el: HTMLElement, target: number, suffix = "") => {
  const obj = { v: 0 };
  gsap.to(obj, {
    v: target,
    duration: 1.25,
    ease: "power3.out",
    onUpdate: () => {
      const n = Math.floor(obj.v);
      el.textContent = n.toLocaleString();
    },
    onComplete: () => {
      el.textContent = Math.round(target).toLocaleString();
      // suffix is rendered separately (span.suffix), but you can merge if you want
    },
  });
};

onMounted(() => {
  if (!root.value) return;

  // initial state
  gsap.set([card1.value, card2.value], { y: 18, opacity: 0 });
  gsap.set(root.value.querySelectorAll(".kicker, .title, .desc"), { y: 10, opacity: 0 });

  io = new IntersectionObserver(
    (entries) => {
      const entry = entries[0];
      if (!entry?.isIntersecting || played) return;
      played = true;

      const headingEls = root.value?.querySelectorAll(".kicker, .title, .desc") ?? [];
      const tl = gsap.timeline({ defaults: { ease: "power3.out" } });
      tl.to(headingEls, {
        y: 0,
        opacity: 1,
        duration: 0.55,
        stagger: 0.08,
      })
        .to([card1.value, card2.value], { y: 0, opacity: 1, duration: 0.55, stagger: 0.1 }, "-=0.2");

      if (count1El.value) animateCount(count1El.value, props.stat1Value, props.stat1Suffix);
      if (count2El.value) animateCount(count2El.value, props.stat2Value, props.stat2Suffix);
    },
    { threshold: 0.25 }
  );

  io.observe(root.value);
});

onBeforeUnmount(() => {
  io?.disconnect();
  io = null;
});
</script>

<style scoped>
/* ====== Layout ====== */
.why {
  position: relative;
  overflow: hidden;

  padding: clamp(22px, 4vw, 224px);
  color: rgba(255, 255, 255, 0.92);

  /* modern dark-blue gradient */
  background:
    radial-gradient(900px 520px at 12% 18%, rgba(4, 29, 255, 0.16), transparent 60%),
    radial-gradient(840px 520px at 80% 34%, rgba(99, 102, 241, 0.16), transparent 62%),
    linear-gradient(180deg, #06081a 0%, #071437 45%, #0a1b46 70%, #2d2bd6 140%);
  border: 1px solid rgba(255, 255, 255, 0.10);
  box-shadow: 0 30px 90px rgba(0, 0, 0, 0.45), inset 0 1px 0 rgba(255, 255, 255, 0.05);
}

.whyInner {
  position: relative;
  z-index: 1;
  max-width: 1380px;
  margin: 0 auto;
}

.whyHead {
  max-width: 1380px;
}

.kicker {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  font-size: 12px;
  letter-spacing: 0.18em;
  font-weight: 700;
  opacity: 0.86;
  text-transform: uppercase;
}

.kicker::before {
  content: "";
  width: 18px;
  height: 10px;
  border-radius: 999px;
  background: linear-gradient(90deg, rgba(56,189,248,1), rgba(99,102,241,1));
  box-shadow: 0 0 16px rgba(56,189,248,0.35);
}

.title {
  margin: 10px 0 10px;
  font-size: clamp(34px, 4.6vw, 58px);
  line-height: 1.02;
  letter-spacing: -0.02em;
  font-weight: 800;
}

.titleStrong {
  color: rgba(255, 255, 255, 0.96);
}

.titleAccent {
  background: linear-gradient(90deg, rgba(56,189,248,1), rgba(99,102,241,1));
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.desc {
  margin: 0;
  max-width: 88ch;
  font-size: 15px;
  line-height: 1.7;
  color: rgba(255, 255, 255, 0.72);
}

/* ====== Stats Cards ====== */
.stats {
  margin-top: 18px;
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 14px;
}

.card {
  position: relative;
  border-radius: 18px;
  padding: 16px 16px 14px;
  background: rgba(8, 12, 28, 0.55);
  border: 1px solid rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(14px);
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.35);
  overflow: hidden;
}

.card::before {
  content: "";
  position: absolute;
  inset: -1px;
  border-radius: inherit;
  background: radial-gradient(700px 140px at 12% 0%, rgba(56,189,248,0.18), transparent 70%);
  pointer-events: none;
}

.cardAccent::before {
  background: radial-gradient(700px 140px at 12% 0%, rgba(99,102,241,0.22), transparent 70%);
}

.cardTop {
  display: flex;
  gap: 12px;
  align-items: center;
}

.iconOrb {
  width: 42px;
  height: 42px;
  border-radius: 999px;
  background:
    radial-gradient(circle at 30% 20%, rgba(255,255,255,0.35), transparent 45%),
    linear-gradient(135deg, rgba(56,189,248,0.22), rgba(99,102,241,0.22));
  border: 1px solid rgba(255,255,255,0.18);
  box-shadow: 0 0 22px rgba(56,189,248,0.18);
  display: grid;
  place-items: center;
}

.iconLine {
  width: 16px;
  height: 2px;
  border-radius: 99px;
  background: rgba(255,255,255,0.8);
  position: relative;
}
.iconLine::after {
  content: "";
  position: absolute;
  width: 10px;
  height: 2px;
  border-radius: 99px;
  background: rgba(255,255,255,0.55);
  right: -2px;
  top: 6px;
}

.meta {
  display: grid;
  gap: 2px;
}

.label {
  font-size: 15px;
  
  text-transform: uppercase;
  color: rgba(255, 255, 255, 0.68);
  font-weight: 700;
}

.value {
  display: flex;
  align-items: baseline;
  gap: 6px;
}

.count {
  font-size: 34px;
  line-height: 1;
  font-weight: 900;
  letter-spacing: -0.02em;
}

.suffix {
  font-weight: 900;
  font-size: 18px;
  opacity: 0.9;
  background: linear-gradient(90deg, rgba(56,189,248,1), rgba(99,102,241,1));
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.sub {
  margin: 10px 0 0;
  font-size: 14px;
  line-height: 1.6;
  color: rgba(255, 255, 255, 0.70);
}

/* ====== Background details ====== */
.whyBg {
  position: absolute;
  inset: 0;
  pointer-events: none;
  z-index: 0;
}

.glow {
  position: absolute;
  width: 720px;
  height: 520px;
  filter: blur(70px);
  opacity: 0.42;
  border-radius: 999px;
}
.g1 {
  left: -220px;
  top: -240px;
  background: rgba(56, 189, 248, 0.35);
}
.g2 {
  right: -240px;
  bottom: -260px;
  background: rgba(99, 102, 241, 0.35);
}

.noise {
  position: absolute;
  inset: 0;
  opacity: 0.08;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='180' height='180'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.8' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='180' height='180' filter='url(%23n)' opacity='.6'/%3E%3C/svg%3E");
  background-size: 180px 180px;
  mix-blend-mode: overlay;
}

/* ====== Responsive ====== */
@media (max-width: 860px) {
  .stats {
    grid-template-columns: 1fr;
  }
}
</style>
