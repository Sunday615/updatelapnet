<template>
    <section class="hero" ref="heroEl">
        <!-- soft glow blobs -->
        <div class="glow glow-1" ref="glow1El" aria-hidden="true"></div>
        <div class="glow glow-2" ref="glow2El" aria-hidden="true"></div>

        <div class="container">
            <!-- Top pill -->
            <a v-if="announcementText" class="pill" ref="pillEl" :href="announcementHref || '#'">
                <span class="pill-badge" v-if="announcementBadge">{{ announcementBadge }}</span>
                <span class="pill-text">{{ announcementText }}</span>
                <span class="pill-arrow" aria-hidden="true">→</span>
            </a>

            <!-- Headline -->
            <h1 class="title" ref="titleEl">
                {{ title }}
            </h1>

            <p class="subtitle" ref="subtitleEl">
                {{ subtitle }}
            </p>

            <!-- Mockup -->
            <div class="mockup-wrap" ref="wrapEl">
                <!-- GSAP animated stroke -->
                <svg class="stroke-svg" ref="svgEl" aria-hidden="true">
                    <rect ref="rectEl" class="stroke-rect" x="0" y="0" width="100%" height="100%" rx="22" ry="22" />
                    <rect ref="glowRectEl" class="stroke-rect-glow" x="0" y="0" width="100%" height="100%" rx="22"
                        ry="22" />
                </svg>

                <div class="mockup" ref="mockupEl">
                    <!-- premium shine -->
                    <div class="mockup-shine" ref="shineEl" aria-hidden="true"></div>

                    <!-- fake window chrome -->
                    <div class="chrome" aria-hidden="true">
                        <span class="dot red"></span>
                        <span class="dot yellow"></span>
                        <span class="dot green"></span>
                        <span class="chrome-line"></span>
                    </div>

                    <div class="mockup-body">
                        <!-- Put your own content (preferred) -->
                        <slot name="mockup">
                            <!-- Fallback: image mockup -->
                            <img v-if="mockupImageSrc" class="mockup-img" :src="mockupImageSrc"
                                :alt="mockupAlt || 'App mockup'" loading="lazy" />
                            <div v-else class="mockup-placeholder">
                                <div class="ph-chart">
                                    ການເຊື່ອມໂຍງ ລະບົບການຊຳລະຂ້າມແດນໃນຮູບແບບ QR Code ລະຫວ່າງປະເທດ
                                    ຈະອຳນວຍຄວາມສະດວກໃຫ້ກັບລູກຄ້າຂອງທະນາຄານ ໃຫ້ສາມາດເຮັດທຸລະກຳຊຳລະຂ້າມແດນ
                                    ໄດ້ຢ່າງປອດໄພ ສະດວກສະບາຍ ວ່ອງໄວ ໂດຍສາມາດນຳໃຊ້ບັນຊີເງິນກີບໃນການຊຳລະ
                                    ແລະ ຮ້ານຄ້າໃນ ສ ປປ ລາວ ກໍ່ສາມາດຮັບການສະແກນຈາກນັກທ່ອງທ່ຽວຕ່າງປະເທດໄດ້
                                    ໂດຍຈະອຳນວຍຄວາມສະດວກໃນການຮັບຊຳລະ ບໍ່ຫຍຸ້ງຍາກໃນການນຳໃຊ້ເງິນສົດ
                                    ແລະ ເງິນທອນຈາກຮ້ານຄ້າໃນເວລາຊຳລະເງິນ.
                                </div>
                            </div>
                        </slot>
                    </div>
                </div>

                <div class="mockup-shadow" aria-hidden="true"></div>
            </div>
        </div>
    </section>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import gsap from "gsap";
import ScrollTrigger from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const props = defineProps({
    // pill
    announcementText: { type: String, default: "" },
    announcementBadge: { type: String, default: "" },
    announcementHref: { type: String, default: "" },

    title: { type: String, default: "ຜົນປະໂຫຍດທີ່ໄດ້ຮັບ" },
    subtitle: { type: String, default: "​​" },

    primaryCta: { type: Object, default: () => ({ label: "Learn more", href: "#" }) },
    secondaryCta: { type: Object, default: () => ({ label: "Learn more", href: "#" }) },

    ratingText: { type: String, default: "1,000+ customers joined" },
    joinedText: { type: String, default: "" },

    mockupImageSrc: { type: String, default: "" },
    mockupAlt: { type: String, default: "" },
});

// refs for premium animations
const heroEl = ref(null);
const glow1El = ref(null);
const glow2El = ref(null);
const pillEl = ref(null);
const titleEl = ref(null);
const subtitleEl = ref(null);

const wrapEl = ref(null);
const mockupEl = ref(null);
const shineEl = ref(null);

const svgEl = ref(null);
const rectEl = ref(null);
const glowRectEl = ref(null);

// tweens
let strokeTween, pulseTween, floatTween, glowTween1, glowTween2, enterTl;
let ro;
let cleanupTilt = null;

const prefersReduced =
    typeof window !== "undefined" &&
    window.matchMedia &&
    window.matchMedia("(prefers-reduced-motion: reduce)").matches;

function setupStroke() {
    const rect = rectEl.value;
    const glowRect = glowRectEl.value;
    if (!rect || !glowRect) return;

    const length = rect.getTotalLength();
    const dash = Math.max(140, length * 0.2);
    const gap = Math.max(0, length - dash);

    gsap.set([rect, glowRect], {
        strokeDasharray: `${dash} ${gap}`,
        strokeDashoffset: 0,
        opacity: 1,
    });

    strokeTween?.kill();
    strokeTween = gsap.to([rect, glowRect], {
        strokeDashoffset: -length,
        duration: 3.2,
        ease: "none",
        repeat: -1,
    });

    pulseTween?.kill();
    pulseTween = gsap.to(glowRect, {
        opacity: 0.9,
        duration: 1.6,
        yoyo: true,
        repeat: -1,
        ease: "sine.inOut",
    });
}

function setupEntrance() {
    const items = [pillEl.value, titleEl.value, subtitleEl.value, wrapEl.value].filter(Boolean);

    enterTl?.kill();
    enterTl = gsap.timeline({
        defaults: { ease: "power3.out" },
        scrollTrigger: {
            trigger: heroEl.value,
            start: "top 75%",
            once: true,
        },
    });

    enterTl.from(items, {
        opacity: 0,
        y: 18,
        filter: "blur(10px)",
        duration: 0.9,
        stagger: 0.12,
        clearProps: "filter",
    });

    enterTl.from(wrapEl.value, { scale: 0.985, duration: 0.8, ease: "power2.out" }, "<");
}

function setupAmbient() {
    if (prefersReduced) return;

    floatTween?.kill();
    floatTween = gsap.to(wrapEl.value, {
        y: -10,
        duration: 3.8,
        yoyo: true,
        repeat: -1,
        ease: "sine.inOut",
    });

    glowTween1?.kill();
    glowTween2?.kill();

    glowTween1 = gsap.to(glow1El.value, {
        x: 30,
        y: 20,
        duration: 8,
        yoyo: true,
        repeat: -1,
        ease: "sine.inOut",
    });

    glowTween2 = gsap.to(glow2El.value, {
        x: -26,
        y: 18,
        duration: 9,
        yoyo: true,
        repeat: -1,
        ease: "sine.inOut",
    });
}

function setupTiltAndShine() {
    const wrap = wrapEl.value;
    const card = mockupEl.value;
    const shine = shineEl.value;
    if (!wrap || !card || !shine) return;

    gsap.set(wrap, { perspective: 1000 });
    gsap.set(card, { transformStyle: "preserve-3d" });

    // idle shine sweep
    if (!prefersReduced) {
        gsap.fromTo(
            shine,
            { xPercent: -40, opacity: 0.45 },
            { xPercent: 40, opacity: 0.7, duration: 3.5, ease: "sine.inOut", repeat: -1, yoyo: true }
        );
    }

    const onMove = (e) => {
        if (prefersReduced) return;

        const r = wrap.getBoundingClientRect();
        const px = (e.clientX - r.left) / r.width;
        const py = (e.clientY - r.top) / r.height;

        const rotY = (px - 0.5) * 10;
        const rotX = -(py - 0.5) * 8;

        gsap.to(card, {
            rotateX: rotX,
            rotateY: rotY,
            y: -6,
            duration: 0.35,
            ease: "power3.out",
        });

        gsap.to(shine, {
            xPercent: (px - 0.5) * 40,
            yPercent: (py - 0.5) * 40,
            opacity: 0.9,
            duration: 0.35,
            ease: "power3.out",
        });
    };

    const onLeave = () => {
        gsap.to(card, { rotateX: 0, rotateY: 0, y: 0, duration: 0.6, ease: "power3.out" });
        gsap.to(shine, { xPercent: 0, yPercent: 0, opacity: 0.55, duration: 0.6, ease: "power3.out" });
    };

    wrap.addEventListener("pointermove", onMove, { passive: true });
    wrap.addEventListener("pointerleave", onLeave, { passive: true });

    cleanupTilt = () => {
        wrap.removeEventListener("pointermove", onMove);
        wrap.removeEventListener("pointerleave", onLeave);
    };
}

onMounted(() => {
    requestAnimationFrame(() => {
        setupStroke();
        setupEntrance();
        setupAmbient();
        setupTiltAndShine();
    });

    ro = new ResizeObserver(() => setupStroke());
    if (wrapEl.value) ro.observe(wrapEl.value);
});

onBeforeUnmount(() => {
    strokeTween?.kill();
    pulseTween?.kill();
    floatTween?.kill();
    glowTween1?.kill();
    glowTween2?.kill();
    enterTl?.kill();

    ScrollTrigger.getAll().forEach((t) => t.kill());
    ro?.disconnect();
    cleanupTilt?.();
});
</script>

<style scoped>
.hero {
    position: relative;
    overflow: hidden;
    padding: clamp(56px, 8vw, 96px) 16px 72px;
    background:
        radial-gradient(1200px 700px at 50% 0%, rgba(8, 0, 169, 0.35), transparent 60%),
        radial-gradient(900px 600px at 70% 20%, rgba(4, 155, 255, 0.18), transparent 65%),
        linear-gradient(180deg, #14003a 0%, #0b0822 50%, #070716 100%);
    color: #fff;
}

.container {
    max-width: 1100px;
    margin: 0 auto;
    text-align: center;
    position: relative;
    z-index: 1;
}

.glow {
    position: absolute;
    inset: auto;
    width: 540px;
    height: 540px;
    filter: blur(70px);
    opacity: 0.6;
    border-radius: 999px;
    z-index: 0;
}

.glow-1 {
    top: -220px;
    left: -180px;
    background: rgba(49, 32, 159, 0.65);
}

.glow-2 {
    top: -260px;
    right: -220px;
    background: rgba(14, 151, 255, 0.35);
}

/* pill */
.pill {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    padding: 10px 14px;
    border-radius: 999px;
    background: rgba(255, 255, 255, 0.08);
    border: 1px solid rgba(255, 255, 255, 0.12);
    text-decoration: none;
    color: rgba(255, 255, 255, 0.9);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
    margin: 0 auto 22px;
    max-width: min(680px, 100%);
}

.pill:hover {
    background: rgba(255, 255, 255, 0.1);
}

.pill-badge {
    font-size: 12px;
    line-height: 1;
    padding: 6px 10px;
    border-radius: 999px;
    background: rgba(0, 0, 0, 0.25);
    border: 1px solid rgba(255, 255, 255, 0.12);
    color: rgba(255, 255, 255, 0.85);
    white-space: nowrap;
}

.pill-text {
    font-size: 14px;
    opacity: 0.95;
}

.pill-arrow {
    opacity: 0.85;
}

/* headline */
.title {
    font-size: clamp(38px, 5vw, 64px);
    line-height: 1.05;
    letter-spacing: -0.02em;
    margin: 0 0 14px;
    font-weight: 700;
}

.subtitle {
    max-width: 760px;
    margin: 0 auto 22px;
    font-size: clamp(15px, 1.6vw, 18px);
    line-height: 1.55;
    color: rgba(255, 255, 255, 0.72);
}

/* mockup wrap */
.mockup-wrap {
    position: relative;
    max-width: 980px;
    margin: 0 auto;
}

/* stroke overlay */
.stroke-svg {
    position: absolute;
    inset: -2px;
    width: calc(100% + 4px);
    height: calc(100% + 4px);
    pointer-events: none;
    z-index: 4;
}

.stroke-rect,
.stroke-rect-glow {
    fill: none;
    stroke-linecap: round;
    vector-effect: non-scaling-stroke;
}

.stroke-rect {
    stroke-width: 2;
    stroke: rgba(120, 190, 255, 0.95);
    filter: drop-shadow(0 0 10px rgba(120, 190, 255, 0.35));
    opacity: 0.95;
}

.stroke-rect-glow {
    stroke-width: 7;
    stroke: rgba(170, 120, 255, 0.35);
    opacity: 0.65;
}

/* mockup */
.mockup {
    border-radius: 22px;
    overflow: hidden;
    border: 1px solid rgba(255, 255, 255, 0.12);
    background: rgba(15, 12, 34, 0.55);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    box-shadow: 0 28px 90px rgba(0, 0, 0, 0.55);
    position: relative;
    will-change: transform;
}

/* premium shine */
.mockup-shine {
    position: absolute;
    inset: -20%;
    z-index: 1;
    pointer-events: none;
    opacity: 0.55;
    background: radial-gradient(500px 280px at 30% 20%,
            rgba(255, 255, 255, 0.16),
            rgba(255, 255, 255, 0.05) 35%,
            transparent 70%);
    mix-blend-mode: screen;
    transform: translateZ(30px);
}

.chrome {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 12px 14px;
    background: rgba(0, 0, 0, 0.25);
    border-bottom: 1px solid rgba(255, 255, 255, 0.08);
    position: relative;
    z-index: 2;
}

.dot {
    width: 10px;
    height: 10px;
    border-radius: 999px;
    opacity: 0.85;
}

.red { background: #ff5f57; }
.yellow { background: #febc2e; }
.green { background: #28c840; }

.chrome-line {
    margin-left: 10px;
    height: 10px;
    width: 180px;
    border-radius: 999px;
    background: rgba(255, 255, 255, 0.08);
}

.mockup-body {
    padding: 18px;
    background:
        radial-gradient(1200px 500px at 50% -20%, rgba(0, 20, 169, 0.18), transparent 60%),
        linear-gradient(180deg, rgba(0, 0, 0, 0.08), rgba(0, 0, 0, 0.28));
    position: relative;
    z-index: 2;
}

.mockup-img {
    width: 100%;
    height: auto;
    border-radius: 16px;
    border: 1px solid rgba(255, 255, 255, 0.10);
    background: rgba(0, 0, 0, 0.25);
}

/* fallback placeholder */
.mockup-placeholder {
    border-radius: 16px;
    border: 1px solid rgba(255, 255, 255, 0.10);
    background: rgba(0, 0, 0, 0.22);
    padding: 18px;
    display: grid;
    gap: 12px;
}

.ph-title,
.ph-row,
.ph-chart {
    border-radius: 12px;
    background: rgba(255, 255, 255, 0.08);
}

.ph-title {
    height: 40px;
    padding-top: 5px;
    margin: 0 auto;
    font-weight: 600;
    font-size: var(--fs-sm);
    width: 42%;
}

.ph-row {
    height: 14px;
    width: 78%;
}

.ph-chart {
    height: 220px;
    width: 100%;
    padding-left: 30px;
    padding-top: 30px;
    padding-right: 30px;
    font-size: var(--fs-xs);
}

.mockup-shadow {
    position: absolute;
    inset: 70% 10% -30% 10%;
    background: rgba(53, 26, 255, 0.25);
    filter: blur(70px);
    z-index: -1;
}

/* reduced motion */
@media (prefers-reduced-motion: reduce) {
    .mockup-wrap,
    .glow,
    .mockup-shine {
        animation: none !important;
        transition: none !important;
    }
}
</style>
