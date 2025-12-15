<script setup>
import { ref, computed, nextTick, onMounted, onBeforeUnmount } from "vue";
import gsap from "gsap";
import ScrollTrigger from "gsap/ScrollTrigger";
import main_navbar from "../../../components/miannavbar/main_navbar.vue";
import mainfooter from "../../../components/footer/mainfooter/mainfooter.vue";
import secondfooter from "../../../components/footer/mainfooter/secondfooter.vue";
import piechart from "../piechart/piechart.vue";
import timeline from "../piechart/timeline.vue";

</script>

<template>
  <main_navbar title="ຄວາມເປັນມາ" :breadcrumb="[
    'ໜ້າຫຼັກ',
    'ກ່ຽວກັບພວກເຮົາ',
    'ຄວາມເປັນມາ'
  ]" background-image="/aboutus/navigatormission-bg.png" />
  
  <section class="maintimeline" >
    <timeline/>
  </section>
  <div class="boxmargin" style="width: 100%; height:15vh"></div>
  <div class="titleholder">
    <h1>ໂຄງສ້າງຜູ້ຖືຫຸ້ນ LAPNet</h1>
  </div>
  <div class="boxmargin" style="width: 100%; height:15vh"></div>
  <div class="shareholders">
    <piechart />
  </div>
  <div class="boxmargin" style="width: 100%;height:15vh"></div>
  <secondfooter />
</template>

<style scoped>
  .maintimeline{
    width: 100%;
    height: auto;
  
  }
.titleholder h1 {
  font-size: var(--fs-hero);
  font-weight: bold;
  background: #5BBDFF;
  background: linear-gradient(to right, #5BBDFF 0%, #1500CF 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.titleholder {
  width: 70%;
  margin: 0 auto;
  height: 15vh;
  border-bottom: 1px solid #0000001b;
  text-align: center;
}

.shareholders {
  margin: 0 auto;
  width: 90%;
  height: auto;
 
}


</style>
