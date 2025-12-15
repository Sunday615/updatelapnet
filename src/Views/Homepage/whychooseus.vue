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
        <article
          ref="card1"
          class="card cardClickable"
          role="button"
          tabindex="0"
          @click="openOverlay('banks')"
          @keydown.enter.prevent="openOverlay('banks')"
          @keydown.space.prevent="openOverlay('banks')"
        >
          <div class="cardTop">
            <div class="iconOrb" aria-hidden="true">
              <i class="fa-solid fa-building-columns"></i>
            </div>

            <div class="meta">
              <div class="label">{{ stat1Label }}</div>
              <div class="value">
                <span ref="count1El" class="count">0</span>
                <span class="suffix"></span>
              </div>
            </div>
          </div>

          <p class="sub">{{ stat1Sub }}</p>

          <div class="ctaRow" aria-hidden="true">
            <span class="ctaText">View members</span>
            <span class="ctaDot"></span>
          </div>
        </article>

        <!-- Box 2 -->
        <article
          ref="card2"
          class="card cardAccent cardClickable"
          role="button"
          tabindex="0"
          @click="openOverlay('right')"
          @keydown.enter.prevent="openOverlay('right')"
          @keydown.space.prevent="openOverlay('right')"
        >
          <div class="cardTop">
            <div class="iconOrb" aria-hidden="true">
              <i class="fa-solid fa-wallet"></i>
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

          <div class="ctaRow" aria-hidden="true">
            <span class="ctaText">View members</span>
            <span class="ctaDot"></span>
          </div>
        </article>
      </div>
    </div>

    <!-- Overlay / Modal -->
    <teleport to="body">
      <div
        v-if="overlayOpen"
        class="overlay"
        role="dialog"
        aria-modal="true"
        aria-label="Members Directory"
      >
        <div ref="overlayBackdrop" class="overlayBackdrop" @click="closeOverlay()"></div>

        <div ref="overlayPanel" class="overlayPanel">
          <header class="overlayHead">
            <div class="overlayTitleWrap">
          
              <h3 class="overlayTitle">
                <span class="overlayTitleStrong">ສະມາຊິກທັງໝົດຂອງ LAPNet</span>
                <span class="overlayTitleAccent">.</span>
              </h3>
              <p class="overlayDesc">
                19 ທະນາຄານສະມາຊິກ ແລະ 2 Fintech
              </p>
            </div>

            <button class="overlayClose" type="button" @click="closeOverlay()" aria-label="Close">
              <span class="x"></span>
            </button>
          </header>

          <div class="overlayGrid">
  <!-- LEFT: Member Banks (show only when banks) -->
  <section v-if="overlayType === 'banks'" class="panel panelActive">
    <div class="panelTop">
      <div class="panelIcon" aria-hidden="true">
        <i class="fa-solid fa-building-columns"></i>
      </div>
      <div>
        <div class="panelLabel">Member Banks</div>
        <div class="panelMeta">{{ memberBanks.length }} members</div>
      </div>
    </div>

    <div class="memberGrid">
      <article v-for="(m, i) in memberBanks" :key="'bank' + i" class="memberCard">
        <div class="logoWrap">
          <img v-if="m.logo" class="logoImg" :src="m.logo" :alt="m.name" loading="lazy" />
          <div v-else class="logoFallback" aria-hidden="true">
            {{ initials(m.name) }}
          </div>
        </div>
        <div class="memberName">{{ m.name }}</div>
      </article>
    </div>
  </section>

  <!-- RIGHT: Fintech (show only when right) -->
  <section v-else class="panel panelActive">
    <div class="panelTop">
      <div class="panelIcon alt" aria-hidden="true">
        <i class="fa-solid fa-wallet"></i>
      </div>
      <div>
        <div class="panelLabel">Fintech</div>
        <div class="panelMeta">{{ memberRight.length }} members</div>
      </div>
    </div>

    <div class="memberGrid">
      <article v-for="(m, i) in memberRight" :key="'right' + i" class="memberCard">
        <div class="logoWrap">
          <img v-if="m.logo" class="logoImg" :src="m.logo" :alt="m.name" loading="lazy" />
          <div v-else class="logoFallback" aria-hidden="true">
            {{ initials(m.name) }}
          </div>
        </div>
        <div class="memberName">{{ m.name }}</div>
      </article>
    </div>
  </section>
</div>


          <footer class="overlayFooter">
            <div class="footerPill">
              <span class="pillDot" style="width: 25px; height:25px">
                <img  src="/logolapnet/fullcircle.png" alt="">
              </span>
              <span class="pillText">Lao National Payment Network</span>
            </div>

          
          </footer>
        </div>
      </div>
    </teleport>
  </section>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch, nextTick } from "vue";
import { gsap } from "gsap";

type OverlayType = "banks" | "right";
type Member = { name: string; logo?: string };

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

  memberBanks?: Member[];
  memberRight?: Member[];
};

const props = withDefaults(defineProps<Props>(), {
  title: "Built for real results",
  description:
    "We deliver secure, scalable experiences with modern design, clear performance, and dedicated support — helping your network grow with confidence.",

  stat1Value: 19,
  stat1Label: "Member Banks",
  stat1Suffix: "+",
  stat1Sub: "19 ທະນາຄານສະມາຊິກທີ່ເຂົ້າຮ່ວມກັບ Lao National Payment Network.",

  stat2Value: 2,
  stat2Label: "Members",
  stat2Suffix: "+",
  stat2Sub: "2 ສະມາຊິກທີ່ເຂົ້າຮ່ວມກັບ Lao National Payment Network.",

  // Replace with real names + logo URLs
 memberBanks: () => [


  // member banks 18
  { name: "ທະນາຄານ ການຄ້າຕ່າງປະເທດລາວ ມະຫາຊົນ", logo: "/logoallmember/circle_scale/BCEL.png" },
  { name: "ທະນາຄານ ພັດທະນາລາວ ຈຳກັດ", logo: "/logoallmember/circle_scale/LDB.PNG" },
  { name: "ທະນາຄານ ສົ່ງເສີມກະສິກໍາ ຈຳກັດ", logo: "/logoallmember/circle_scale/APBB.PNG" },
  { name: "ທະນາຄານ ຮ່ວມທຸລະກິດ ລາວ-ຫວຽດ", logo: "/logoallmember/circle_scale/lvb.PNG" },
  { name: "ທະນາຄານ ຮ່ວມພັດທະນາ ມະຫາຊົນ", logo: "/logoallmember/circle_scale/JDB.png" },
  { name: "ທະນາຄານ ເອັສທີ ຈຳກັດ", logo: "/logoallmember/circle_scale/STB.png" },
  { name: "ທະນາຄານ ບີໄອຊີ ລາວ ຈໍາກັດ", logo: "/logoallmember/circle_scale/BIC.png" },
  { name: "ທະນາຄານ ອຸດສະຫະກໍາ ແລະ ການຄ້າຈີນ ຈຳກັດ", logo: "/logoallmember/circle_scale/ICBC.png" },
  { name: "ທະນາຄານແຫ່ງ ປະເທດຈີນ ຈຳກັດ", logo: "/logoallmember/circle_scale/bankofchina.png" },
  { name: "ທະນາຄານ ຫວຽດຕິນ ລາວ ຈຳກັດ", logo: "/logoallmember/circle_scale/VTB.png" },
  { name: "ທະນາຄານ ອິນໂດຈີນ ຈຳກັດ", logo: "/logoallmember/circle_scale/IB.png" },
  { name: "ທະນາຄານ ເອຊີລີດາລາວ ຈໍາກັດ", logo: "/logoallmember/circle_scale/ACLB.png" },
  { name: "ທະນາຄານ ມາຣູຮານ ເຈແປນ ລາວ ຈຳກັດ", logo: "/logoallmember/circle_scale/Maruhan.png" },
  { name: "ທະນາຄານ ໄຊງ່ອນເທືອງຕິ່ນ ລາວ", logo: "/logoallmember/circle_scale/SACOM.PNG" },
  { name: "ທະນາຄານ ກະສິກອນໄທ ຈຳກັດ", logo: "/logoallmember/circle_scale/Kasikorn.png" },
  { name: "ທະນາຄານ ພາບລິກ", logo: "/logoallmember/circle_scale/PUB.png" },
  { name: "ທະນາຄານ ລາວ-ຝລັ່ງ ຈຳກັດ", logo: "/logoallmember/circle_scale/BFL.png" },
  { name: "ທະນາຄານ ພົງສະຫວັນ ຈໍາກັດ", logo: "/logoallmember/circle_scale/PSVB.png" },
  { name: "ທະນາຄານ ຫຸ້ນສ່ວນການຄ້າທະຫານ ສາຂາລາວ", logo: "/logoallmember/circle_scale/mb.png" },
 

],

  memberRight: () => [
  { name: "MmoneyX", logo: "/logoallmember/circle_scale/mmoney.png" },
  { name: "Umoney", logo: "/logoallmember/circle_scale/umoney.png" },
   
  ],
});

const root = ref<HTMLElement | null>(null);
const card1 = ref<HTMLElement | null>(null);
const card2 = ref<HTMLElement | null>(null);

const count1El = ref<HTMLElement | null>(null);
const count2El = ref<HTMLElement | null>(null);

let io: IntersectionObserver | null = null;
let played = false;

const animateCount = (el: HTMLElement, target: number) => {
  const obj = { v: 0 };
  gsap.to(obj, {
    v: target,
    duration: 1.25,
    ease: "power3.out",
    onUpdate: () => {
      el.textContent = Math.floor(obj.v).toLocaleString();
    },
    onComplete: () => {
      el.textContent = Math.round(target).toLocaleString();
    },
  });
};

const initials = (name: string) => {
  const parts = name.trim().split(/\s+/).filter(Boolean);
  const a = parts[0]?.[0] ?? "";
  const b = parts.length > 1 ? parts[parts.length - 1]?.[0] ?? "" : "";
  return (a + b).toUpperCase();
};

/** Overlay state */
const overlayOpen = ref(false);
const overlayType = ref<OverlayType>("banks");

const overlayBackdrop = ref<HTMLElement | null>(null);
const overlayPanel = ref<HTMLElement | null>(null);
let overlayTl: gsap.core.Timeline | null = null;

const openOverlay = (type: OverlayType) => {
  overlayType.value = type;
  overlayOpen.value = true;
};

const closeOverlay = () => {

  overlayTl?.kill();
  overlayTl = null;

  overlayOpen.value = false;

  // cleanup ทันที
  document.body.style.overflow = "";
  document.removeEventListener("keydown", onKey);
};

/** ESC close */
const onKey = (e: KeyboardEvent) => {
  if (e.key === "Escape" && overlayOpen.value) closeOverlay();
};

watch(overlayOpen, async (isOpen) => {
  if (isOpen) {
    document.addEventListener("keydown", onKey);
    document.body.style.overflow = "hidden";

    await nextTick();

    overlayTl?.kill();
    overlayTl = gsap.timeline({
      defaults: { ease: "power3.out" },
      onReverseComplete: () => {
        overlayOpen.value = false;
        overlayTl?.kill();
        overlayTl = null;
        document.body.style.overflow = "";
        document.removeEventListener("keydown", onKey);
      },
    });

    const panel = overlayPanel.value;
    const back = overlayBackdrop.value;
    if (!panel || !back) return;

    const head = panel.querySelectorAll(".overlayKicker, .overlayTitle, .overlayDesc");
    const cols = panel.querySelectorAll(".panel");
    const memberCards = panel.querySelectorAll(".memberCard");

    gsap.set(back, { opacity: 0 });
    gsap.set(panel, { opacity: 0, y: 18, scale: 0.98 });
    gsap.set(head, { opacity: 0, y: 10 });
    gsap.set(cols, { opacity: 0, y: 10 });
    gsap.set(memberCards, { opacity: 0, y: 10 });

    overlayTl
      .to(back, { opacity: 1, duration: 0.22 })
      .to(panel, { opacity: 1, y: 0, scale: 1, duration: 0.3 }, "-=0.06")
      .to(head, { opacity: 1, y: 0, duration: 0.45, stagger: 0.06 }, "-=0.1")
      .to(cols, { opacity: 1, y: 0, duration: 0.45, stagger: 0.07 }, "-=0.18")
      .to(memberCards, { opacity: 1, y: 0, duration: 0.35, stagger: 0.03 }, "-=0.22");
  } else {
    document.body.style.overflow = "";
    document.removeEventListener("keydown", onKey);
  }
});

onMounted(() => {
  if (!root.value) return;

  gsap.set([card1.value, card2.value], { y: 18, opacity: 0 });
  gsap.set(root.value.querySelectorAll(".kicker, .title, .desc"), { y: 10, opacity: 0 });

  io = new IntersectionObserver(
    (entries) => {
      const entry = entries[0];
      if (!entry?.isIntersecting || played) return;
      played = true;

      const headingEls = root.value?.querySelectorAll(".kicker, .title, .desc") ?? [];
      const tl = gsap.timeline({ defaults: { ease: "power3.out" } });

      tl.to(headingEls, { y: 0, opacity: 1, duration: 0.55, stagger: 0.08 }).to(
        [card1.value, card2.value],
        { y: 0, opacity: 1, duration: 0.55, stagger: 0.1 },
        "-=0.2"
      );

      if (count1El.value) animateCount(count1El.value, props.stat1Value);
      if (count2El.value) animateCount(count2El.value, props.stat2Value);
    },
    { threshold: 0.25 }
  );

  io.observe(root.value);
});

onBeforeUnmount(() => {
  io?.disconnect();
  io = null;
  document.removeEventListener("keydown", onKey);
  document.body.style.overflow = "";
  overlayTl?.kill();
  overlayTl = null;
});
</script>

<style scoped>
/* ====== Layout ====== */
.why {
  position: relative;
  overflow: hidden;

  padding: clamp(22px, 4vw, 224px);
  color: rgba(255, 255, 255, 0.92);

  background:
    radial-gradient(900px 520px at 12% 18%, rgba(4, 29, 255, 0.16), transparent 60%),
    radial-gradient(840px 520px at 80% 34%, rgba(99, 102, 241, 0.16), transparent 62%),
    linear-gradient(180deg, #06081a 0%, #071437 45%, #0a1b46 70%, #2d2bd6 140%);
  border: 1px solid rgba(255, 255, 255, 0.1);
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
  background: linear-gradient(90deg, rgba(56, 189, 248, 1), rgba(99, 102, 241, 1));
  box-shadow: 0 0 16px rgba(56, 189, 248, 0.35);
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
  background: linear-gradient(90deg, rgba(56, 189, 248, 1), rgba(99, 102, 241, 1));
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
  user-select: none;
}

.cardClickable {
  cursor: pointer;
  transition: transform 180ms ease, border-color 180ms ease, box-shadow 180ms ease;
  outline: none;
}

.cardClickable:hover {
  transform: translateY(-2px);
  border-color: rgba(255, 255, 255, 0.22);
  box-shadow: 0 24px 64px rgba(0, 0, 0, 0.45);
}

.cardClickable:focus-visible {
  box-shadow: 0 24px 64px rgba(0, 0, 0, 0.45), 0 0 0 3px rgba(56, 189, 248, 0.22);
}

.card::before {
  content: "";
  position: absolute;
  inset: -1px;
  border-radius: inherit;
  background: radial-gradient(700px 140px at 12% 0%, rgba(56, 189, 248, 0.18), transparent 70%);
  pointer-events: none;
}

.cardAccent::before {
  background: radial-gradient(700px 140px at 12% 0%, rgba(99, 102, 241, 0.22), transparent 70%);
}

.cardTop {
  display: flex;
  gap: 12px;
  align-items: center;
}
.iconOrb i{
  font-size: 1.2rem;
}
.iconOrb {
  width: 42px;
  height: 42px;
  border-radius: 999px;
  background:
    radial-gradient(circle at 30% 20%, rgba(255, 255, 255, 0.35), transparent 45%),
    linear-gradient(135deg, rgba(56, 189, 248, 0.22), rgba(99, 102, 241, 0.22));
  border: 1px solid rgba(255, 255, 255, 0.18);
  box-shadow: 0 0 22px rgba(56, 189, 248, 0.18);
  display: grid;
  place-items: center;
}

.iconLine {
  width: 16px;
  height: 2px;
  border-radius: 99px;
  background: rgba(255, 255, 255, 0.8);
  position: relative;
}
.iconLine::after {
  content: "";
  position: absolute;
  width: 10px;
  height: 2px;
  border-radius: 99px;
  background: rgba(255, 255, 255, 0.55);
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
  background: linear-gradient(90deg, rgba(56, 189, 248, 1), rgba(99, 102, 241, 1));
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.sub {
  margin: 10px 0 0;
  font-size: 14px;
  line-height: 1.6;
  color: rgba(255, 255, 255, 0.7);
}

.ctaRow {
  margin-top: 12px;
  display: inline-flex;
  align-items: center;
  gap: 10px;
  opacity: 0.9;
}
.ctaText {
  font-size: 13px;
  font-weight: 800;
  letter-spacing: 0.02em;
  color: rgba(255, 255, 255, 0.78);
}
.ctaDot {
  width: 26px;
  height: 10px;
  border-radius: 999px;
  background: linear-gradient(90deg, rgba(56, 189, 248, 1), rgba(99, 102, 241, 1));
  box-shadow: 0 0 18px rgba(99, 102, 241, 0.22);
}

/* ====== Overlay / Modal (Fit screen) ====== */
.overlay {
  position: fixed;
  inset: 0;
  z-index: 9999;
  display: grid;
  place-items: center;
  padding: 14px;
}

.overlayBackdrop {
  position: absolute;
  inset: 0;
  background: rgba(2, 4, 12, 0.72);
  backdrop-filter: blur(10px);
}

/* ✅ Fits viewport + scroll inside */
.overlayPanel {
  position: relative;
  z-index: 1;

  width: min(980px, 100%);
  height: min(820px, calc(100vh - 28px));
  max-height: calc(100vh - 28px);

  border-radius: 22px;
  overflow: hidden;

  display: flex;
  flex-direction: column;

  background:
    radial-gradient(900px 400px at 10% 0%, rgba(56, 189, 248, 0.12), transparent 55%),
    radial-gradient(900px 400px at 90% 0%, rgba(99, 102, 241, 0.12), transparent 60%),
    rgba(10, 14, 30, 0.72);
  border: 1px solid rgba(255, 255, 255, 0.14);
  box-shadow: 0 30px 120px rgba(0, 0, 0, 0.65), inset 0 1px 0 rgba(255, 255, 255, 0.06);
}

.overlayHead {
  padding: 16px 16px 10px;
  display: flex;
  justify-content: space-between;
  gap: 16px;
  flex: 0 0 auto;
}

.overlayKicker {
  font-size: 11px;
  letter-spacing: 0.2em;
  font-weight: 800;
  opacity: 0.82;
  text-transform: uppercase;
}

.overlayTitle {
  margin: 8px 0 8px;
  font-size: clamp(22px, 3vw, 32px);
  line-height: 1.06;
  font-weight: 900;
  letter-spacing: -0.02em;
}
.overlayTitleStrong {
  color: rgba(255, 255, 255, 0.95);
}
.overlayTitleAccent {
  background: linear-gradient(90deg, rgba(56, 189, 248, 1), rgba(99, 102, 241, 1));
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
}

.overlayDesc {
  margin: 0;
  color: rgba(255, 255, 255, 0.7);
  line-height: 1.6;
  font-size: 14px;
  max-width: 74ch;
}

.overlayClose {
  width: 42px;
  height: 42px;
  border-radius: 14px;
  border: 1px solid rgba(255, 255, 255, 0.14);
  background: rgba(255, 255, 255, 0.06);
  cursor: pointer;
  display: grid;
  place-items: center;
  transition: transform 180ms ease, background 180ms ease, border-color 180ms ease;
}
.overlayClose:hover {
  transform: translateY(-1px);
  background: rgba(255, 255, 255, 0.08);
  border-color: rgba(255, 255, 255, 0.22);
}
.x {
  width: 16px;
  height: 16px;
  position: relative;
}
.x::before,
.x::after {
  content: "";
  position: absolute;
  inset: 0;
  margin: auto;
  width: 16px;
  height: 2px;
  border-radius: 99px;
  background: rgba(255, 255, 255, 0.85);
}
.x::before {
  transform: rotate(45deg);
}
.x::after {
  transform: rotate(-45deg);
}

/* ✅ Scroll area inside modal */
.overlayGrid {
  padding: 6px 16px 12px;
  flex: 1 1 auto;
  overflow: auto;

  display: grid;
  grid-template-columns: 1fr; /* ✅ แสดงแค่คอลัมน์เดียว */
  gap: 12px;
}

.panel {
  border-radius: 18px;
  padding: 14px;
  background: rgba(4, 8, 22, 0.55);
  border: 1px solid rgba(255, 255, 255, 0.12);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.06);
  transition: border-color 180ms ease, box-shadow 180ms ease, transform 180ms ease;
}

.panelActive {
  border-color: rgba(56, 189, 248, 0.22);
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.35), inset 0 1px 0 rgba(255, 255, 255, 0.06);
  transform: translateY(-1px);
}

.panelTop {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 12px;
}
.panelIcon i{
  font-size: 1.2rem;
  color: #fff;
}
.panelIcon {
  width: 34px;
  height: 34px;
  display: flex;
justify-content: center;
align-items: center;
  border-radius: 12px;
  background:
    radial-gradient(circle at 30% 20%, rgba(255, 255, 255, 0.35), transparent 50%),
    linear-gradient(135deg, rgba(56, 189, 248, 0.22), rgba(99, 102, 241, 0.18));
  border: 1px solid rgba(255, 255, 255, 0.16);
}
.panelIcon.alt {
  background:
    radial-gradient(circle at 30% 20%, rgba(255, 255, 255, 0.35), transparent 50%),
    linear-gradient(135deg, rgba(99, 102, 241, 0.22), rgba(56, 189, 248, 0.18));
}

.panelLabel {
  font-size: 14px;
  font-weight: 900;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  color: rgba(255, 255, 255, 0.82);
}
.panelMeta {
  font-size: 13px;
  color: rgba(255, 255, 255, 0.62);
}

/* ====== Member Cards (left & right same style) ====== */
.memberGrid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 10px;
}

.memberCard {
  border-radius: 16px;
  padding: 12px;
  background:
    radial-gradient(700px 160px at 12% 0%, rgba(56, 189, 248, 0.14), transparent 70%),
    rgba(255, 255, 255, 0.04);
  border: 1px solid rgba(255, 255, 255, 0.12);
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.06);
  transition: transform 160ms ease, border-color 160ms ease, background 160ms ease;
}

.panelActive .memberCard {
  background:
    radial-gradient(700px 160px at 12% 0%, rgba(99, 102, 241, 0.18), transparent 70%),
    rgba(255, 255, 255, 0.04);
}

.memberCard:hover {
  transform: translateY(-1px);
  border-color: rgba(255, 255, 255, 0.18);
  background: rgba(255, 255, 255, 0.055);
}

.logoWrap {
  width: 44px;
  height: 44px;
  border-radius: 14px;
  display: grid;
  place-items: center;
  margin-bottom: 10px;
  background: rgba(255, 255, 255, 0.06);
  border: 1px solid rgba(255, 255, 255, 0.12);
  box-shadow: 0 0 22px rgba(56, 189, 248, 0.1);
  overflow: hidden;
}

.logoImg {
  width: 100%;
  height: 100%;
  object-fit: contain;
  padding: 8px;
  filter: drop-shadow(0 10px 18px rgba(0, 0, 0, 0.3));
}

.logoFallback {
  font-weight: 900;
  letter-spacing: 0.08em;
  font-size: 12px;
  color: rgba(255, 255, 255, 0.85);
}

.memberName {
  font-size: 14px;
  font-weight: 500;
  color: rgba(255, 255, 255, 0.9);
  line-height: 1.2;
}

/* Footer fixed */
.overlayFooter {
  padding: 12px 16px 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 12px;
  flex: 0 0 auto;
  border-top: 1px solid rgba(255, 255, 255, 0.08);
  background: rgba(10, 14, 30, 0.35);
  backdrop-filter: blur(8px);
}

.footerPill {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 10px 12px;
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.pillDot {
  width: 10px;
  height: 10px;
  border-radius: 99px;
  background: linear-gradient(90deg, rgba(56, 189, 248, 1), rgba(99, 102, 241, 1));
  box-shadow: 0 0 18px rgba(56, 189, 248, 0.22);
}

.pillText {
  font-size: 13px;
  font-weight: 800;
  color: rgba(255, 255, 255, 0.74);
}

.footerBtn {
  border-radius: 14px;
  padding: 10px 14px;
  border: 1px solid rgba(255, 255, 255, 0.16);
  background: rgba(255, 255, 255, 0.06);
  color: rgba(255, 255, 255, 0.88);
  font-weight: 900;
  cursor: pointer;
  transition: transform 160ms ease, background 160ms ease, border-color 160ms ease;
}
.footerBtn:hover {
  transform: translateY(-1px);
  background: rgba(255, 255, 255, 0.08);
  border-color: rgba(255, 255, 255, 0.22);
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
@media (max-width: 960px) {
  .overlayGrid {
    grid-template-columns: 1fr;
  }
  .memberGrid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 860px) {
  .stats {
    grid-template-columns: 1fr;
  }
}
</style>
