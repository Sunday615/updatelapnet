<template>
  <div class="page">
    <div class="wrap">
    <header class="hero">
      <div>
        <div class="kicker">Roadmap</div>
        <h1>ຄວາມເປັນມາຂອງ Lao National Payment Network</h1>
        <p class="sub " style="display:flex ; align-items: center; ">
            <img src="/logolapnet/fullcircle.png" alt="" style="width: 30px; height:30px ; margin-right: 7px;">
            Lao Nationnal Payment Network
        </p>
      </div>
     
    </header>

    <section class="timeline" ref="timelineRef">
      <!-- Real line element so GSAP can animate it reliably -->
      <div class="line" aria-hidden="true">
        <div class="lineFill" ref="lineFillRef" />
      </div>

      <article
        v-for="(item, i) in items"
        :key="item.id"
        class="event"
        :data-side="item.side"
        :ref="(el) => setEventRef(el, i)"
      >
        <!-- Left side -->
        <div class="side left" :class="{ reveal: item.side === 'left' }">
          <div v-if="item.side === 'left'" class="card">
            <div class="date">{{ item.date }}</div>
            <h3 class="title">{{ item.title }}</h3>
            <p class="desc">{{ item.desc }}</p>
          </div>
        </div>

        <!-- Middle -->
        <div class="mid">
          <span class="connector" />
          <span class="node" aria-hidden="true" />
        </div>

        <!-- Right side -->
        <div class="side right" :class="{ reveal: item.side === 'right' }">
          <div v-if="item.side === 'right'" class="card">
            <div class="date">{{ item.date }}</div>
            <h3 class="title">{{ item.title }}</h3>
            <p class="desc">{{ item.desc }}</p>
          </div>
        </div>
      </article>
    </section>
    </div>
  </div>
</template>

<script setup>
import { onBeforeUnmount, onMounted, ref } from "vue";
import gsap from "gsap";
import ScrollTrigger from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const items = ref([
  {
    id: "p1",
    side: "left",
    date: "Phase 01 • ",
    title: "ເລີ່ມກໍຕັ້ງບໍລິສັດ",
    desc: "ບໍລິສັດ ລາວເນເຊີນນໍ ເພເມັ້ນ ເນັດເວີກ ຈຳກັດ ຫືຼ LAPNet ດຳເນີນທຸລະກິດໃນຮູບແບບ ບໍລິສັດ ຈຳກັດ,  ເຊິ່ງໄດ້ຮັບອະນຸຍາດເປັນຜູ້ຄວບຄຸມລະບົບບັດທະນາຄານຮ່ວມກັນ ຫຼື Lao ATM Pool Switching (LAPS) ໂດຍແມ່ນໂຄງການຊ່ວຍເຫຼືອລະຫວ່າງລັດຖະບານ ສປ ຈີນ ຕໍ່ກັບ ສປປ ລາວ ຕາມສານແລກປ່ຽນວ່າດ້ວຍລັດຖະບານ ສປ ຈີນ ເຫັນດີຊ່ວຍເຫຼືອລັດຖະບານ ສປປ ລາວ ປະຕິບັດ ‘’ໂຄງການລະບົບຊຳລະດ້ວຍບັດອັດຕະໂນມັດຂອງທະນາຄານ’’.",
  },
  {
    id: "p2",
    side: "right",
    date: "Phase 02 • ",
    title: "ເປີດນຳໃຊ້ລະບົບຢ່າງເປັນທາງການ",
    desc: "ໂຄງການດັ່ງກ່າວໄດ້ສຳເລັດ ແລະ ເປີດນຳໃຊ້ລະບົບຢ່າງເປັນທາງການໃນວັນທີ 01/12/2015 ໂດຍຢູ່ພາຍໃຕ້ການບໍລິຫານຂອງ ທຫລ, ເຊິ່ງຂັ້ນເທິງ  ໄດ້ເຫັນດີມອບໝາຍໃຫ້ກົມເຕັກໂນໂລຊີຂໍ້ມູນຂ່າວສານເປັນຜູ້ຈັດຕັ້ງປະຕິບັດ ແລະ ໄດ້ສ້າງຕັ້ງພະແນກຄຸ້ມຄອງບັດທະນາຄານຂຶ້ນມາເພື່ອຄຸ້ມຄອງ ແລະ ເຮັດວຽກງານດັ່ງກ່າວໂດຍກົງ, ເຊິ່ງທາງຝ່າຍຈີນ ຮັບຜິດ ຊອບໃນການບຳລຸງຮັກສາທາງດ້ານເຕັກນິກຂອງລະບົບໃຫ້ ສປປ ລາວ ລ້າ ເປັນໄລຍະເວລາ 3 ປີ.",
  },
  {
    id: "p3",
    side: "left",
    date: "Phase 03 • ",
    title: "ຫັນປ່ຽນເປັນນິຕິບຸກຄົນ",
    desc: "ຜ່ານການຈັດຕັ້ງປະຕິບັດຕົວຈິງເຫັນວ່າການນຳໃຊ້ລະບົບດັ່ງກ່າວແມ່ນຍັງບໍ່ເຕັມປະສິດທິພາບເທົ່າທີ່ຄວນ ເນື່ອງຈາກການດຳເນີນງານແມ່ນບໍ່ສອດຄ່ອງກັບພາລະບົດບາດຂອງ ທຫລ ທີ່ເປັນຜູ້ຄຸ້ມຄອງມະຫາພາກດ້ານການເງິນ-ເງິນຕາ, ທັງເປັນອົງກອນທີ່ບໍ່ສະແຫວງຫາຜົນກຳໄລ ຈຶ່ງເຮັດໃຫ້ການຄຸຸ້ມຄອງບໍລິຫານລະບົບດັ່ງ ກ່າວ ຍັງມີລັກສະນະ ‘’ນະໂຍບາຍ’’. ດັ່ງນັ້ນ, ຄະນະພັກ, ຄະນະຜູ້ວ່າການ ທຫລ ຈຶ່ງມີມະຕິຕົກລົງເຫັນດີໃຫ້ຫັນເອົາວຽກງານ LAPS ເປັນນິຕິບຸກຄົນຕາມມະຕິກອງປະຊຸມຄະນະປະຈຳພັກ-ຄະນະຜູ້ວ່າການ ທຫລ ປະຈຳເດືອນ ກຸມພາ 2016.",
  },
  {
    id: "p4",
    side: "right",
    date: "Phase 04 • ",
    title: "ການບຳລຸງຮັກສາລະບົບ",
    desc: "ພາຍຫຼັງສິ້ນສຸດໄລຍະການບຳລຸງຮັກສາທາງດ້ານເຕັກນິກລ້າຈາກຝ່າຍຈີນໃນວັນທີ 01/12/2018, ສປປ ລາວ ຕ້ອງໄດ້ສືບຕໍ່ໃນການບໍາລຸງຮັກສາລະບົບເອງ. ໂດຍ ທຫລ ໄດ້ໂອນພາລະບົດບາດການດຳເນີນງານໃຫ້ບໍລິສັດ LAPNet ທີ່ຈະສ້າງຂຶ້ນ ເປັນຜູ້ສືບຕໍ່ດຳເນີນທຸລະກິດໃນການໃຫ້ບໍລິການລະບົບ LAPS ໃຫ້ມີປະສິດທິຜົນສູງສຸດ ແລະ ພັດທະນາໃຫ້ສາມາດເຊື່ອມໂຍງກັບພາກພື້ນ ແລະ ສາກົນ, ເພາະວ່າລະບົບຊຳລະທາງການເງິນແມ່ນຍິ່ງມີຄວາມສຳຄັນເປັນອັນດັບຕົ້ນໆຂອງຂະແໜງເສດຖະຖິດ-ການເງິນ ທີ່ຄວນໄດ້ຮັບການພັດທະນາໃຫ້ມີຄວາມ ເໝາະສົມ-ສອດຄ່ອງກັບຄວາມຕ້ອງການຂອງໜ່ວຍງານເສດຖະກິດ ທັງພາກລັດ ແລະ ເອກະຊົນ.",
  },
  {
    id: "p5",
    side: "left",
    date: "Phase 05 • ",
    title: "ບົດບາດຂອງ LAPNet",
    desc: "ບໍລິສັດລາວ ຈະເປັນສ່ວນໜຶ່ງໃນການປະກອບສ່ວນຊຸກຍູ້ເສດຖະກິດ ແລະ ໃຫ້ການສະໜັບສະໜູນບັນທະນາຄານທຸລະກິດໃຫ້ສາມາດເຊື່ອມໂຍງ  ກັນໃຫ້ໄດ້ຫລາຍຊ່ອງທາງຂຶ້ນກວ່າເກົ່າ ເພື່ອເປັນການອຳນວຍຄວາມສະດວກໃຫ້ແກ່ຜູ້ຊົມໃຊ້ໄດ້ສາມາດດຳເນີນທຸລະກຳທາງດ້ານການເງິນໄດ້ “ ທຸກທີ່, ທຸກເວລາ, ທຸກຊ່ອງທາງການຊຳລະ” .",
  },
]);

const timelineRef = ref(null);
const lineFillRef = ref(null);
const eventRefs = ref([]);

function setEventRef(el, idx) {
  if (!el) return;
  eventRefs.value[idx] = el;
}

let triggers = [];

onMounted(() => {
  // LINE DRAW (scrub)
  if (lineFillRef.value && timelineRef.value) {
    gsap.set(lineFillRef.value, { scaleY: 0, transformOrigin: "top" });

    const st = ScrollTrigger.create({
      trigger: timelineRef.value,
      start: "top 70%",
      end: "bottom 70%",
      scrub: true,
      onUpdate: (self) => {
        gsap.to(lineFillRef.value, { scaleY: self.progress, ease: "none", overwrite: true, duration: 0 });
      },
    });

    triggers.push(st);
  }

  // EVENTS
  eventRefs.value.forEach((eventEl) => {
    if (!eventEl) return;

    const node = eventEl.querySelector(".node");
    const reveal = eventEl.querySelector(".reveal");
    const side = eventEl.dataset.side;
    const fromX = side === "left" ? -26 : 26;

    // Initial states (CSS handles most, but this makes it rock-solid)
    if (node) gsap.set(node, { opacity: 0, scale: 0.6 });
    if (reveal) gsap.set(reveal, { opacity: 0, x: fromX, y: 14 });

    const t = gsap.timeline({
      scrollTrigger: {
        trigger: eventEl,
        start: "top 78%",
        toggleActions: "play none none reverse",
      },
    });

    if (node) {
      t.to(node, {
        opacity: 1,
        scale: 1,
        duration: 0.55,
        ease: "back.out(2.2)",
      });
    }

    if (reveal) {
      t.to(
        reveal,
        {
          opacity: 1,
          x: 0,
          y: 0,
          duration: 0.6,
          ease: "power3.out",
        },
        0.05
      );
    }

    triggers.push(t.scrollTrigger);
  });
});

onBeforeUnmount(() => {
  // Clean up ScrollTriggers (important in SPAs)
  triggers.forEach((t) => t && t.kill && t.kill());
  triggers = [];
});
</script>

<style scoped>
.page{
  /* Theme tokens (scoped, inherited by children) */
  --bg0:#03081a;
  --bg1:#01030f;
  --text:#eaf1ff;
  --muted:rgba(234,241,255,.70);
  --line:rgba(127,168,255,.22);
  --accent:#5eead4;   /* teal */
  --accent2:#60a5fa;  /* blue */
  --accent3:#a78bfa;  /* violet */
  --card:rgba(255,255,255,.06);
  --stroke:rgba(255,255,255,.12);
  --shadow:0 20px 70px rgba(0,0,0,.62);
  --radius:18px;

  min-height:100vh;
  color:var(--text);
  font-family: Inter, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, sans-serif;
  background:
    radial-gradient(900px 600px at 72% 18%, rgba(96,165,250,.22), transparent 56%),
    radial-gradient(820px 540px at 28% 82%, rgba(167,139,250,.18), transparent 58%),
    radial-gradient(720px 460px at 50% 45%, rgba(94,234,212,.10), transparent 60%),
    linear-gradient(180deg, var(--bg0), var(--bg1));
  position:relative;
  overflow:hidden;
}

/* Soft sheen + subtle grid overlay */
.page::before{
  content:"";
  position:absolute;
  inset:-2px;
  background:radial-gradient(1200px 700px at 50% -10%, rgba(255,255,255,.06), transparent 58%);
  pointer-events:none;
  mix-blend-mode:soft-light;
}
.page::after{
  content:"";
  position:absolute;
  inset:0;
  background-image:
    linear-gradient(rgba(255,255,255,.05) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255,255,255,.05) 1px, transparent 1px);
  background-size:64px 64px;
  opacity:.06;
  pointer-events:none;
  mask-image:radial-gradient(circle at 50% 30%, black 0%, transparent 68%);
}

.wrap{
  position:relative;
  z-index:1;

  max-width:1100px;
  margin:0 auto;
  padding:72px 22px 90px;
  color:var(--text);
}

.hero{
  display:flex;
  align-items:flex-end;
  justify-content:space-between;
  gap:18px;
  margin-bottom:28px;
}

.kicker{
  font-size:12px;
  letter-spacing:.14em;
  text-transform:uppercase;
  color:rgba(234,240,255,.55);
}

h1{
  margin:.35rem 0 0;
  font-size:clamp(28px, 3.6vw, 44px);
  line-height:1.1;
  font-weight:800;
}

.sub{
  max-width:520px;
  color:var(--muted);
  margin:10px 0 0;
  line-height:1.6;
  font-size:14px;
}

.chip{
  display:inline-flex;
  align-items:center;
  gap:8px;
  padding:10px 12px;
  border-radius:999px;
  background:rgba(255,255,255,.06);
  border:1px solid rgba(255,255,255,.12);
  box-shadow:0 10px 30px rgba(0,0,0,.25);
  color:rgba(234,240,255,.74);
  font-size:12px;
  user-select:none;
  white-space:nowrap;
}
.dot{
  width:8px;height:8px;border-radius:50%;
  background:linear-gradient(135deg,var(--accent2),var(--accent3));
  box-shadow:0 0 0 6px rgba(96,165,250,.14);
}

.timeline{
  position:relative;
  margin-top:26px;
  padding:26px 0;
}

/* GSAP-animated line */
.line{
  position:absolute;
  left:50%;
  top:0;
  transform:translateX(-50%);
  width:2px;
  height:100%;
  background:rgba(255,255,255,.05);
}
.lineFill{
  width:100%;
  height:100%;
  transform-origin:top;
  background:linear-gradient(
    180deg,
    rgba(96,165,250,0),
    rgba(96,165,250,.65) 22%,
    rgba(167,139,250,.62) 72%,
    rgba(167,139,250,0)
  );
  filter:drop-shadow(0 0 14px rgba(96,165,250,.35));
}

.event{
  position:relative;
  display:grid;
  grid-template-columns:1fr 72px 1fr;
  align-items:center;
  padding:18px 0;
  min-height:120px;
}

.side{ padding:10px 18px; }
.side.left{ text-align:left; }
.side.right{ text-align:left; }

.date{
  font-size:12px;
  letter-spacing:.12em;
  text-transform:uppercase;
  color:rgba(234,240,255,.55);
  margin-bottom:8px;
}

.title{
  font-weight:600;
  font-size:16px;
  line-height:1.2;
  margin:0 0 8px;
}

.desc{
  margin:0;
  color:var(--muted);
  font-size:13px;
  line-height:1.6;
}

.mid{
  display:flex;
  align-items:center;
  justify-content:center;
  position:relative;
  height:100%;
}

.node{
  width:14px;
  height:14px;
  border-radius:50%;
  background:linear-gradient(135deg,var(--accent2),var(--accent3));
  box-shadow:
    0 0 0 6px rgba(96,165,250,.11),
    0 0 0 14px rgba(167,139,250,.08),
    0 12px 40px rgba(96,165,250,.20);
  border:1px solid rgba(255,255,255,.18);
}

.connector{
  position:absolute;
  top:50%;
  left:50%;
  transform:translateY(-50%);
  height:1px;
  width:42px;
  background:linear-gradient(90deg, rgba(140,170,255,.0), rgba(140,170,255,.55));
  opacity:.85;
}

.event[data-side="left"] .connector{ left:auto; right:50%; transform:translateY(-50%) scaleX(-1); }

.card{
  display:inline-block;
  max-width:420px;
  padding:14px 16px;
  border-radius:var(--radius);
  background:linear-gradient(180deg, rgba(255,255,255,.075), rgba(255,255,255,.045));
  border:1px solid var(--stroke);
  box-shadow:var(--shadow);
  backdrop-filter: blur(10px);
  position:relative;
}

.event[data-side="left"] .card{ margin-left:auto; }

.card::after{
  content:"";
  position:absolute;
  inset:-1px;
  border-radius:var(--radius);
  background:
    radial-gradient(400px 120px at 10% 10%, rgba(96,165,250,.12), transparent 50%),
    radial-gradient(420px 140px at 90% 80%, rgba(167,139,250,.12), transparent 55%);
  pointer-events:none;
  opacity:.7;
  filter:blur(18px);
  z-index:-1;
}

/* Responsive: stack */
@media (max-width: 820px){
  .hero{flex-direction:column;align-items:flex-start}
  .line{left:22px; transform:none;}
  .event{grid-template-columns:72px 1fr;}
  .side.left,.side.right{ text-align:left; }
  .side{grid-column:2; padding-left:10px;}
  .mid{grid-column:1; justify-content:flex-start;}
  .connector{display:none;}
  .event[data-side="left"] .card{margin-left:0;}
}
</style>
