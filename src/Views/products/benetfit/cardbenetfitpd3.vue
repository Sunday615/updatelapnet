<template>
    <section class="hero">
        <!-- soft glow blobs -->
        <div class="glow glow-1" aria-hidden="true"></div>
        <div class="glow glow-2" aria-hidden="true"></div>

        <div class="container">
            <!-- Top pill -->
            <a v-if="announcementText" class="pill" :href="announcementHref || '#'">
                <span class="pill-badge" v-if="announcementBadge">{{ announcementBadge }}</span>
                <span class="pill-text">{{ announcementText }}</span>
                <span class="pill-arrow" aria-hidden="true">→</span>
            </a>

            <!-- Headline -->
            <h1 class="title">
                {{ title }}
            </h1>

            <p class="subtitle">
                {{ subtitle }}
            </p>

            <!-- CTAs -->


            <!-- Social proof -->


            <!-- Logos row -->


            <!-- Mockup -->
            <div class="mockup-wrap">
                <div class="mockup">
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
                                <div class="ph-title">ດ້ານທະນາຄານສະມາຊິກ</div>

                                <div class="ph-chart">ຫຼຸດຕົ້ນທຶນທາງດ້ານບຸກຂະລາກອນທີ່ໃຫ້ບໍລິການໂອນເງິນຂ້າມທະນາຄານຜ່ານ Counter ຫຼື ປ່ອງບໍລິການຂອງທະນາຄານ ໃຫ້ລູກຄ້າຫັນມານຳໃຊ້ຕູ້ ATM ໃນການເຮັດທຸລະກຳ ແລະ ຍັງສາມາດເພີ່ມຈຳນວນທຸລະກຳການໂອນ ເພາະສາມາດໂອນຢູ່ຕູ້ ATM ຂອງທະນາຄານໃດກໍ່ໄດ້ທີ່ເປັນສະມາຊິກຂອງບໍລິສັດ LAPNet.
                                </div>
                                <div class="ph-title">ດ້ານທະນາຄານສະມາຊິກ</div>

                                <div class="ph-chart">ເປັນການອຳນວຍຄວາມສະດວກ ແລະ ປະຢັດເວລາ ໂດຍລູກຄ້າບໍ່ຈໍາເປັນຕ້ອງລໍຖ້າການໃຊ້ບໍລິການໂອນເງິນຂ້າມທະນາຄານຜ່ານ counter ຫຼື ປ່ອງບໍລິການຂອງທະນາຄານ; ລູກຄ້າສາມາດເຮັດທຸລະກຳໂອນເງິນຂ້າມທະນາຄານໄດ້ດ້ວຍຕົນເອງ ດ້ວຍການໂອນເງິນທີ່ຕູ້ ATM ຂອງທະນາຄານສະມາຊິກ ຊຶ່ງປັດຈຸບັນມີ 15 ທະນາຄານທີ່ສາມາດໃຫ້ບໍລິການໄດ້.
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
const props = defineProps({


    title: { type: String, default: "ຜົນປະໂຫຍດທີ່ໄດ້ຮັບ" },
    subtitle: {
        type: String,
        default:
            "​​",
    },

    primaryCta: {
        type: Object,
        default: () => ({ label: "Learn more", href: "#" }),
    },
    secondaryCta: {
        type: Object,
        default: () => ({ label: "Learn more", href: "#" }),
    },

  

    ratingText: { type: String, default: "1,000+ customers joined" },
    joinedText: { type: String, default: "" },

    

    mockupImageSrc: { type: String, default: "" },
    mockupAlt: { type: String, default: "" },
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

/* CTAs */
.cta-row {
    display: flex;
    gap: 12px;
    justify-content: center;
    flex-wrap: wrap;
    margin: 18px 0 22px;
}

.btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    height: 44px;
    padding: 0 18px;
    border-radius: 12px;
    text-decoration: none;
    font-weight: 650;
    font-size: 14px;
    border: 1px solid transparent;
    transition: transform .15s ease, background .15s ease, border-color .15s ease;
}

.btn:active {
    transform: translateY(1px);
}

.btn-primary {
    background: rgba(255, 255, 255, 0.1);
    border-color: rgba(255, 255, 255, 0.18);
    box-shadow: 0 18px 55px rgba(0, 0, 0, 0.35);
    backdrop-filter: blur(10px);
}

.btn-primary:hover {
    background: rgba(255, 255, 255, 0.13);
}

.btn-ghost {
    background: rgba(255, 255, 255, 0.92);
    color: #111;
}

.btn-ghost:hover {
    background: #fff;
}

/* social proof */
.social {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 14px;
    margin: 6px 0 26px;
    flex-wrap: wrap;
}

.avatars {
    display: inline-flex;
    align-items: center;
}

.avatar {
    width: 34px;
    height: 34px;
    border-radius: 999px;
    border: 2px solid rgba(255, 255, 255, 0.12);
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.35);
    margin-left: -10px;
}

.avatar:first-child {
    margin-left: 0;
}

.rating {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    color: rgba(255, 255, 255, 0.8);
}

.stars {
    letter-spacing: 2px;
    color: #ffcf5a;
    font-size: 14px;
}

.rating-text {
    text-align: left;
    display: grid;
    gap: 2px;
}

.rating-strong {
    font-size: 13px;
    font-weight: 650;
}

.rating-sub {
    font-size: 12px;
    opacity: 0.75;
}

/* logos */
.logos {
    display: flex;
    justify-content: center;
    gap: clamp(18px, 3vw, 40px);
    flex-wrap: wrap;
    margin: 6px 0 34px;
    opacity: 0.9;
}

.logo {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    padding: 8px 10px;
    border-radius: 12px;
    text-decoration: none;
    border: 1px solid rgba(255, 255, 255, 0.08);
    background: rgba(255, 255, 255, 0.03);
}

.logo img {
    height: 26px;
    width: auto;
    opacity: 0.9;
}

/* mockup */
.mockup-wrap {
    position: relative;
    max-width: 980px;
    margin: 0 auto;
}

.mockup {
    border-radius: 22px;
    overflow: hidden;
    border: 1px solid rgba(255, 255, 255, 0.12);
    background: rgba(15, 12, 34, 0.55);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    box-shadow: 0 28px 90px rgba(0, 0, 0, 0.55);
}

.chrome {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 12px 14px;
    background: rgba(0, 0, 0, 0.25);
    border-bottom: 1px solid rgba(255, 255, 255, 0.08);
}

.dot {
    width: 10px;
    height: 10px;
    border-radius: 999px;
    opacity: 0.85;
}

.red {
    background: #ff5f57;
}

.yellow {
    background: #febc2e;
}

.green {
    background: #28c840;
}

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
    width: 100%;
    border: 0.5px solid white;
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
</style>
