<template>
    <section ref="root" class="hero">
        <!-- background -->
        <div class="bg" aria-hidden="true">
            <div class="bgRadial"></div>
            <div class="bgGrid"></div>
            <div class="bgNoise"></div>
        </div>

        <header ref="nav" class="nav">
            <div class="navInner">
                <div class="brand">
                    <span class="brandMark"><img src="/logolapnet/fullcircle.png" alt="" /></span>
                    <span class="brandText">LAPNET</span>
                </div>

                <!-- Desktop links -->
                <nav class="navLinks">
                    <productDropdown />
                    <memberdropdown />
                    <a href="/joinus" class="navLink">ຂ່າວສານ ແລະ ກິດຈະກຳ</a>
                    <a href="" class="navLink">ຮ່ວມງານກັບເຮົາ</a>
                    <aboutusdropdown />
                    <a href="" class="navLink">ຕິດຕໍ່ພວກເຮົາ</a>
                </nav>

                <!-- Right actions -->
                <div class="navRight">
                    <a class="iconBtn" href="https://www.facebook.com/laonationalpaymentnetwork/?locale=th_TH"
                        target="_blank" rel="noopener" aria-label="Facebook">
                        <i class="fa-brands fa-facebook-f"></i>
                    </a>

                    <a class="iconBtn" href="#" aria-label="Email">
                        <i class="fa-solid fa-envelope"></i>
                    </a>

                    <button class="navCta">Login</button>
                    <sidebar class="sidebar-lapnet" ref="sidebarRef" />
                    <!-- Mobile only -->
                    <button class="menuBtn" type="button" @click.stop="sidebarOpen = true"
                        :aria-expanded="sidebarOpen ? 'true' : 'false'" aria-label="Open menu">
                        <!-- ✅ Hamburger icon -->
                        <span class="hamburger" aria-hidden="true">
                            <span></span><span></span><span></span>
                        </span>

                        <!-- ✅ Text beside icon -->
                        <span class="menuText">Menu</span>
                    </button>


                </div>
            </div>
        </header>

        <div class="container">
            <div class="copy">
                <div ref="pill" class="pill">
                    <span class="pillDot"></span>
                    Lao National Payment Network
                </div>

                <h1 ref="title" class="title">
                    Anywhere Anytime
                    <span class="titleAccent"></span> Any <span style="width: 3px;"></span><span class="titleAI">Payment
                        Channel</span>
                </h1>

                <p ref="desc" class="desc">
                    ພັດທະນາລະບົບການຊໍາລະທຸລະກຳຍ່ອຍໃຫ້ເປັນສູນກາງການຊໍາລະຂອງບັນດາຜູ້ໃຫ້ບໍລິການຊໍາລະ ທັງພາຍໃນ ແລະ ສາກົນ,
                    ແນໃສ່ໃຫ້ປະຊາຊົນລາງໄດ້ໃຊ້ບໍລິການຊໍາລະທີ່ສະດວກວ່ອງໄວ, ທັນສະໄໝ, ປອດໄພ, ຕົ້ນທືນຕໍາ ແລະ
                    ເພື່ອໃຫ້ບໍລິສັດການເປັນໂຄງຮ່າງພື້ນຖານໃຫ້ແກ່ການຊໍາລະໃນ ສປປ ລາວ.
                </p>

                <div ref="actions" class="actions">
                    <button class="btnPrimary">Let’s start</button>
                    <button class="btnGhost">Preview</button>
                </div>
            </div>

            <div ref="deviceStage" class="deviceStage">
                <div ref="deviceGlow" class="deviceGlow" aria-hidden="true"></div>

                <div ref="globeEl" class="globeStage">
                    <LapnetGlobe class="globe" />
                </div>
            </div>
        </div>

        <div class="bottomFade" aria-hidden="true"></div>
    </section>



    <!-- ✅ Sidebar (ใช้ v-model คุม เปิด/ปิด) -->
    <Teleport to="body">
        <homepage_sidebar v-model="sidebarOpen" logo-src="/logolapnet/fullcircle.png" company-name="LAPNET"
            company-tagline="Lao National Payment Network" />
    </Teleport>
    <visionherosectiom />

    <whychooseus title="Why choose LAPNET"
        description="ບໍລິສັດ ລາວເນ ເຊີນນໍ ເພເມັ້ນ ເນັດເວີກ ຈຳກັດ (LAPNet) ເຊິ່ງບໍລິສັດ LAPNet ເປັນຜູ້ໃຫ້ບໍລິການລະບົບ ການຊໍາລະທຸລະກໍາຍ່ອຍ ຕາມທີ່ໄດ້ກໍານົດໄວ້ໃນກົດໝາຍວ່າດ້ວຍລະບົບ ການຊໍາລະ. ຊຶ່ງເຮັດໜ້າທີ່ເປັນໂຕກາງໃນການ ເຊື່ອມໂຍງກັບທຸກຂະແໜງການຊຳລະທີ່ເປັນທະນາຄານ, ສະຖາບັນການເງິນ ແລະ ຜູ້ໃຫ້ບໍລິການ ການຊຳລະແຫ່ງດຽວໃນ ສປປ ລາວ;"
        :stat1Value="19" stat1Label="Member Banks"
        stat1Suffix="19 ທະນາຄານສະມາຊິກທີ່ເຂົ້າຮ່ວມກັບ Lao National Payment Network" :stat2Value="2" stat2Label="Fintech"
        stat2Suffix="" />

    <div class="scollingmember">
        <allmemberscrolling />
    </div>

    <productherosectionhomepage />



</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch } from "vue";
import gsap from "gsap";
import visionherosectiom from "./visionherosectiom.vue";
import allmemberscrolling from "../../components/swiper/memberscrolling/allmemberscrolling.vue";
import memberdropdown from "../../components/dropdown-homepage/memberdropdown.vue";
import aboutusdropdown from "../../components/dropdown-homepage/aboutusdropdown.vue";
import productDropdown from "../../components/dropdown-homepage/product-dropdown.vue";
import LapnetGlobe from "../../components/mockup/atmmockup.vue";
import homepage_sidebar from "../../components/sidebar/homepage_sidebar.vue";
import whychooseus from "./whychooseus.vue";
import productherosectionhomepage from "./productherosectionhomepage.vue";



const sidebarOpen = ref(false);

const root = ref<HTMLElement | null>(null);
const nav = ref<HTMLElement | null>(null);
const pill = ref<HTMLElement | null>(null);
const title = ref<HTMLElement | null>(null);
const desc = ref<HTMLElement | null>(null);
const actions = ref<HTMLElement | null>(null);

const deviceStage = ref<HTMLElement | null>(null);
const deviceGlow = ref<HTMLElement | null>(null);
const globeEl = ref<HTMLElement | null>(null);

let cleanup: (() => void) | null = null;

const lockScroll = (on: boolean) => {
    document.documentElement.style.overflow = on ? "hidden" : "";
};

watch(sidebarOpen, (v) => lockScroll(v));

onMounted(() => {
    const reduce = window.matchMedia?.("(prefers-reduced-motion: reduce)")?.matches ?? false;

    const onKey = (e: KeyboardEvent) => {
        if (e.key === "Escape") sidebarOpen.value = false;
    };
    window.addEventListener("keydown", onKey);

    const ctx = gsap.context(() => {
        const tl = gsap.timeline({ defaults: { ease: "power3.out" } });

        tl.from(nav.value, { y: -16, opacity: 0, duration: 0.7 })
            .from(pill.value, { y: 18, opacity: 0, duration: 0.7 }, "-=0.35")
            .from(title.value, { y: 22, opacity: 0, duration: 0.9 }, "-=0.42")
            .from(desc.value, { y: 18, opacity: 0, duration: 0.7 }, "-=0.55")
            .from(actions.value, { y: 16, opacity: 0, duration: 0.7 }, "-=0.5")
            .from(globeEl.value, { y: 26, opacity: 0, scale: 0.985, duration: 1.0 }, "-=0.55")
            .from(deviceGlow.value, { opacity: 0, duration: 0.9 }, "-=0.8");

        if (!reduce) {
            gsap.to(globeEl.value, {
                y: -10,
                duration: 3.8,
                repeat: -1,
                yoyo: true,
                ease: "sine.inOut",
            });

            const stage = deviceStage.value;
            if (stage) {
                const qx = gsap.quickTo(globeEl.value, "rotationX", { duration: 0.8, ease: "power3.out" });
                const qy = gsap.quickTo(globeEl.value, "rotationY", { duration: 0.8, ease: "power3.out" });
                const qg = gsap.quickTo(deviceGlow.value, "x", { duration: 0.9, ease: "power3.out" });

                const onMove = (e: MouseEvent) => {
                    const r = stage.getBoundingClientRect();
                    const nx = (e.clientX - r.left) / r.width - 0.5;
                    const ny = (e.clientY - r.top) / r.height - 0.5;
                    qx(-ny * 4.0);
                    qy(nx * 5.5);
                    qg(nx * 18);
                };

                stage.addEventListener("mousemove", onMove);
                stage.addEventListener("mouseleave", () => {
                    qx(0);
                    qy(0);
                    qg(0);
                });

                cleanup = () => stage.removeEventListener("mousemove", onMove);
            }
        }
    }, root);

    cleanup = (() => {
        const prev = cleanup;
        return () => {
            prev?.();
            ctx.revert();
            window.removeEventListener("keydown", onKey);
            lockScroll(false);
        };
    })();
});

onBeforeUnmount(() => {
    cleanup?.();
    cleanup = null;
});
</script>

<style scoped>
.whyus {
    max-width: 1380px;
    height: auto;

    margin: 0 auto;

}

.scollingmember {
    width: 100%;
    height: auto;
}

/* ---- hero base ---- */
.hero {
    position: relative;
    min-height: 100svh;
    padding: 22px 18px 70px;
    overflow: hidden;
    background: #07050f;
    color: rgba(255, 255, 255, 0.92);
}

.bg {
    position: absolute;
    inset: 0;
    pointer-events: none;
    z-index: 0;
}

.bgRadial {
    position: absolute;
    inset: -20%;
    background:
        radial-gradient(980px 520px at 50% 18%, rgba(0, 200, 255, 0.12), transparent 62%),
        radial-gradient(980px 620px at 42% 40%, rgba(0, 0, 191, 0.18), transparent 65%),
        radial-gradient(880px 620px at 60% 72%, rgba(124, 58, 237, 0.12), transparent 68%);
    filter: blur(2px);
}

.bgGrid {
    position: absolute;
    inset: 0;
    background-image:
        linear-gradient(rgba(255, 255, 255, 0.06) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255, 255, 255, 0.06) 1px, transparent 1px);
    background-size: 58px 58px;
    opacity: 0.06;
    mask-image: radial-gradient(closest-side at 50% 25%, rgba(0, 0, 0, 1), rgba(0, 0, 0, 0));
}

.bgNoise {
    position: absolute;
    inset: 0;
    opacity: 0.08;
    background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='180' height='180'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.8' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='180' height='180' filter='url(%23n)' opacity='.6'/%3E%3C/svg%3E");
    background-size: 180px 180px;
    mix-blend-mode: overlay;
}

/* ---- nav ---- */
.nav {
    position: relative;
    z-index: 2;
    max-width: 1380px;
    margin: 0 auto;
}

.navInner {
    height: 56px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 10px 14px;
    border-radius: 999px;
    background: rgba(10, 8, 18, 0.55);
    border: 1px solid rgba(255, 255, 255, 0.10);
    backdrop-filter: blur(10px);
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.35), inset 0 1px 0 rgba(255, 255, 255, 0.06);
}

.brand {
    display: flex;
    align-items: center;
    gap: 10px;
    user-select: none;
}

.brandMark {
    width: 50px;
    height: 50px;
    display: grid;
    place-items: center;
}

.brandMark img {
    width: 100%;
    height: 100%;
    object-fit: contain;
}

.brandText {
    font-weight: 800;
    letter-spacing: 0.12em;
    font-size: 12px;
    opacity: 0.92;
}

.navLinks {
    display: flex;
    gap: 32px;
    align-items: center;
}

.navLink {
    color: rgb(255, 255, 255);
    font-size: var(--fs-xs);
    text-decoration: none;
    transition: opacity .2s ease, transform .2s ease;
    cursor: pointer;
}

.navLink:hover {
    opacity: 1;
    transform: translateY(-1px);
}

.navRight {
    display: flex;
    align-items: center;
    gap: 10px;
}

.iconBtn {
    width: 36px;
    height: 36px;
    border-radius: 999px;
    display: grid;
    place-items: center;
    border: 1px solid rgba(255, 255, 255, 0.12);
    background: rgba(10, 8, 18, 0.35);
    color: rgba(255, 255, 255, 0.92);
    text-decoration: none;
    transition: transform .2s ease, background .2s ease, border-color .2s ease;
    backdrop-filter: blur(10px);
}

.iconBtn:hover {
    transform: translateY(-1px);
    background: rgba(10, 8, 18, 0.55);
    border-color: rgba(255, 255, 255, 0.16);
}

.iconBtn i {
    font-size: 14px;
}

.navCta {
    height: 36px;
    padding: 0 14px;
    border-radius: 999px;
    border: 1px solid rgba(255, 255, 255, 0.12);
    background: rgba(255, 255, 255, 0.92);
    color: rgba(8, 6, 14, 1);
    font-weight: 700;
    font-size: 13px;
    cursor: pointer;
    transition: transform .2s ease;
}

.navCta:hover {
    transform: translateY(-1px);
}

.menuText {
    font-weight: 700;
    font-size: 13px;
    letter-spacing: 0.02em;

}

.menuBtn {
    display: none;
    align-items: center;
    gap: 10px;
    /* ระยะห่าง icon กับ文字 */
    height: 36px;
    padding: 0 12px;
    border-radius: 999px;
    border: 1px solid rgba(255, 255, 255, 0.12);
    background: rgba(10, 8, 18, 0.35);
    color: rgba(255, 255, 255, 0.92);
    cursor: pointer;
}

.menuBtn:hover {
    transform: translateY(-1px);
    background: rgba(10, 8, 18, 0.55);
}

.hamburger {
    width: 18px;
    height: 12px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
}

.hamburger span {
    display: block;
    height: 2px;
    border-radius: 99px;
    background: rgba(255, 255, 255, 0.92);
    opacity: 0.92;
}

.menuText {
    font-weight: 700;
    font-size: 13px;
    letter-spacing: 0.02em;
}

/* ---- hero content ---- */
.container {
    position: relative;
    z-index: 1;
    max-width: 1380px;
    margin: 0 auto;
    padding-top: 26px;
    display: grid;
    grid-template-columns: 1.05fr 0.95fr;
    gap: 26px;
    align-items: center;
}

.copy {
    padding: 22px 6px;
}

.pill {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    padding: 8px 12px;
    border-radius: 999px;
    border: 1px solid rgba(255, 255, 255, 0.12);
    background: rgba(10, 8, 18, 0.55);
    backdrop-filter: blur(10px);
    font-size: 12px;
    color: rgba(255, 255, 255, 0.78);
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.05);
}

.pillDot {
    width: 10px;
    height: 10px;
    border-radius: 999px;
    background: linear-gradient(271deg, rgba(0, 0, 191, 1) 35%, rgba(0, 200, 255, 1) 100%);
    box-shadow: 0 0 18px rgba(0, 200, 255, 0.35);
}

.title {
    margin: 16px 0 10px;
    font-size: clamp(44px, 5.2vw, 78px);
    line-height: 0.98;
    letter-spacing: -0.02em;
    font-weight: 700;
}

.titleAI {

    background: linear-gradient(271deg, rgba(0, 0, 191, 1) 35%, rgba(0, 200, 255, 1) 100%);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
    text-shadow: 0 18px 60px rgba(0, 200, 255, 0.14);

}

.desc {
    max-width: 520px;
    color: rgba(255, 255, 255, 0.70);
    font-size: 15px;
    line-height: 1.6;
    margin: 10px 0 18px;
}

.actions {
    display: flex;
    gap: 12px;
    align-items: center;
}

.btnPrimary {
    height: 44px;
    padding: 0 18px;
    border-radius: 999px;
    border: 1px solid rgba(255, 255, 255, 0.14);
    background: rgba(255, 255, 255, 0.92);
    color: rgba(8, 6, 14, 1);
    font-weight: 800;
    cursor: pointer;
    box-shadow: 0 18px 50px rgba(0, 0, 0, 0.35);
    transition: transform .2s ease;
}

.btnPrimary:hover {
    transform: translateY(-1px);
}

.btnGhost {
    height: 44px;
    padding: 0 18px;
    border-radius: 999px;
    border: 1px solid rgba(255, 255, 255, 0.14);
    background: rgba(10, 8, 18, 0.35);
    color: rgba(255, 255, 255, 0.86);
    backdrop-filter: blur(10px);
    cursor: pointer;
    transition: transform .2s ease, background .2s ease;
}

.btnGhost:hover {
    transform: translateY(-1px);
    background: rgba(10, 8, 18, 0.5);
}

/* ---- right stage ---- */
.deviceStage {
    position: relative;
    display: grid;
    place-items: center;
    isolation: isolate;
    padding-top: 18px;
}

.deviceGlow {
    position: absolute;
    inset: -140px -180px;
    z-index: 0;
    background: linear-gradient(271deg, rgb(1, 0, 44) 35%, rgba(0, 98, 255, 0.548) 100%);
    filter: blur(78px);
    opacity: 0.78;
    transform: translateY(-12px);
    border-radius: 999px;
    pointer-events: none;
}

.globeStage {
    position: relative;
    z-index: 1;
    width: min(820px, 92vw);
    height: 740px;
    border-radius: 26px;
    backdrop-filter: blur(10px);
    transform: perspective(1200px) rotateX(6deg);
    transform-origin: center;
}

.globe {
    width: 100%;
    height: 100%;
    display: block;
}

.bottomFade {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    height: 160px;
    background: linear-gradient(180deg, transparent, rgba(7, 5, 15, 1));
    z-index: 1;
    pointer-events: none;
}

/* ✅ responsive navbar */
@media (max-width: 980px) {
    .container {
        grid-template-columns: 1fr;
        gap: 10px;
    }

    .copy {
        padding: 18px 2px;
    }

    .navLinks {
        display: none;
    }

    .menuBtn {
        display: flex;
    }

    .globeStage {
        width: min(760px, 96vw);
        height: 420px;
    }
}

@media (max-width: 520px) {
    .globeStage {
        height: 340px;
    }

    .title {
        font-size: 44px;
    }

    .iconBtn {
        width: 34px;
        height: 34px;
        display: none;
    }

    .navCta {
        height: 34px;
    }

    .menuBtn {
        height: 34px;
    }
}
</style>
