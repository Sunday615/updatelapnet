<template>
  <section>
    <atmmockup  
    :nodes="nodes"
  hubLogo="/logolapnet/fullcircle.png"
  hubName="Lapnet"
  :hubLat="18"
  :hubLon="103"
  :cycleSeconds="15"
  :minSeparationDeg="26"
  :minNodeToHubDeg="40"
  />
  </section>
</template>

<script setup lang="ts">
import atmmockup from './globe.vue'

const nodes = [
  { name: "Binance", logo: "/logoallmember/circle_scale/mb.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/ACLB.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/APBB.PNG" },
  { name: "Binance", logo: "/logoallmember/circle_scale/bankofchina.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/BFL.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/BIC.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/IB.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/ICBC.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/JDB.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/Kasikorn.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/lcnb.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/PUB.PNG" },
  { name: "Binance", logo: "/logoallmember/circle_scale/lvb.PNG" },
  { name: "Binance", logo: "/logoallmember/circle_scale/Maruhan.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/BCEL.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/mmoney.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/PSVB.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/LDB.PNG" },
  { name: "Binance", logo: "/logoallmember/circle_scale/SACOM.PNG" },
  { name: "Binance", logo: "/logoallmember/circle_scale/STB.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/umoney.png" },
  { name: "Binance", logo: "/logoallmember/circle_scale/VTB.png" },
  

  // ...รวมให้ครบ 20
];


</script>
