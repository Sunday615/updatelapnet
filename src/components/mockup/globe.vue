<template>
  <div ref="wrap" class="globeWrap">
    <canvas ref="canvas" class="globeCanvas"></canvas>

    <!-- ✅ Lapnet logo at TOP -->
  

    <!-- ✅ Lapnet logo at CENTER (overlay only) -->
  
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, computed } from "vue";
import * as THREE from "three";
import gsap from "gsap";

type Endpoint = {
  name?: string;
  logo: string;
  lat?: number;
  lon?: number;
};

type ProductPair = {
  id: string;
  label?: string;
  from: Endpoint;
  to: Endpoint;
  color?: string;
};

const DEFAULT_NODES: Endpoint[] = Array.from({ length: 20 }).map((_, i) => ({
  name: `Logo ${i + 1}`,
  logo: `/logos/logo-${String(i + 1).padStart(2, "0")}.png`,
}));

const props = withDefaults(
  defineProps<{
    pairs?: ProductPair[];
    nodes?: Endpoint[];

    hubLogo?: string;
    hubName?: string;
    hubLat?: number;
    hubLon?: number;

    autoDistribute?: boolean;
    minSeparationDeg?: number;
    minNodeToHubDeg?: number;

    focusLat?: number;
    focusLon?: number;

    landMaskUrl?: string;

    dotPulseSpeed?: number;

    // ✅ slower & smoother
    cycleSeconds?: number;          // inbound + outbound (total cycle)
    microStaggerFrac?: number;      // 0..0.45
    flowTrailCount?: number;
    flowTrailSpacing?: number;
    activeLineOpacity?: number;
    baseLineOpacity?: number;

    nodePinScale?: number;
  }>(),
  {
    pairs: () => [],
    nodes: undefined,

    hubLogo: "/logos/lapnet.png",
    hubName: "Lapnet",
    hubLat: 18.0,
    hubLon: 103.0,

    autoDistribute: true,
    minSeparationDeg: 26,
    minNodeToHubDeg: 40,

    focusLat: 18.0,
    focusLon: 103.0,

    landMaskUrl: "/textures/earth-mask.png",

    dotPulseSpeed: 0.60,

    // ✅ default slower
    cycleSeconds: 12.0,
    microStaggerFrac: 0.24,
    flowTrailCount: 7,
    flowTrailSpacing: 0.06,
    activeLineOpacity: 0.9,
    baseLineOpacity: 0.28,

    nodePinScale: 0.14,
  }
);

const canvas = ref<HTMLCanvasElement | null>(null);
const wrap = ref<HTMLDivElement | null>(null);

const hub = computed(() => ({
  name: props.hubName,
  logo: props.hubLogo!,
  lat: props.hubLat!,
  lon: props.hubLon!,
}));

const nodes = computed<Endpoint[]>(() => {
  if (props.nodes && props.nodes.length > 0) return props.nodes;

  const ps = props.pairs ?? [];
  if (ps.length > 0) {
    const map = new Map<string, Endpoint>();
    for (const p of ps) {
      if (p.from?.logo) map.set(p.from.logo, p.from);
      if (p.to?.logo) map.set(p.to.logo, p.to);
    }
    if (hub.value.logo) map.delete(hub.value.logo);
    return Array.from(map.values()).slice(0, 50);
  }

  return DEFAULT_NODES;
});

let renderer: THREE.WebGLRenderer | null = null;
let scene: THREE.Scene | null = null;
let camera: THREE.PerspectiveCamera | null = null;

let raf = 0;
let ro: ResizeObserver | null = null;
const disposers: Array<() => void> = [];

const CFG = {
  radius: 1.22,
  dots: 4200,
  dotColor: "#C4B5FD",
  arcColor: "#A78BFA",
};

function clamp(x: number, a: number, b: number) {
  return Math.max(a, Math.min(b, x));
}
function smoothstep(edge0: number, edge1: number, x: number) {
  const t = clamp((x - edge0) / (edge1 - edge0), 0, 1);
  return t * t * (3 - 2 * t);
}
// ✅ more premium easing than smoothstep for progress
function easeInOutCubic(x: number) {
  const t = clamp(x, 0, 1);
  return t < 0.5 ? 4 * t * t * t : 1 - Math.pow(-2 * t + 2, 3) / 2;
}

function angleBetween(a: THREE.Vector3, b: THREE.Vector3) {
  const d = clamp(a.clone().normalize().dot(b.clone().normalize()), -1, 1);
  return Math.acos(d);
}

function latLonToVec3(lat: number, lon: number, r: number) {
  const phi = (90 - lat) * (Math.PI / 180);
  const theta = (lon + 180) * (Math.PI / 180);
  return new THREE.Vector3(
    -r * Math.sin(phi) * Math.cos(theta),
     r * Math.cos(phi),
     r * Math.sin(phi) * Math.sin(theta)
  );
}

function vec3ToUV(p: THREE.Vector3) {
  const n = p.clone().normalize();
  const u = 0.5 + Math.atan2(n.z, n.x) / (2 * Math.PI);
  const v = 0.5 - Math.asin(n.y) / Math.PI;
  return { u, v };
}

function fibonacciSphere(n: number, r: number) {
  const pts: THREE.Vector3[] = [];
  const golden = Math.PI * (3 - Math.sqrt(5));
  for (let i = 0; i < n; i++) {
    const y = 1 - (i / (n - 1)) * 2;
    const rr = Math.sqrt(1 - y * y);
    const theta = golden * i;
    const x = Math.cos(theta) * rr;
    const z = Math.sin(theta) * rr;
    pts.push(new THREE.Vector3(x, y, z).multiplyScalar(r));
  }
  return pts;
}

async function loadLandMaskSampler(url: string) {
  try {
    const img = new Image();
    img.src = url;

    await new Promise<void>((res, rej) => {
      img.onload = () => res();
      img.onerror = () => rej(new Error("mask load failed"));
    });

    const c = document.createElement("canvas");
    c.width = img.width;
    c.height = img.height;
    const ctx = c.getContext("2d", { willReadFrequently: true });
    if (!ctx) throw new Error("no ctx");

    ctx.drawImage(img, 0, 0);
    const data = ctx.getImageData(0, 0, c.width, c.height).data;

    return (u: number, v: number) => {
      const x = Math.min(c.width - 1, Math.max(0, Math.floor(u * c.width)));
      const y = Math.min(c.height - 1, Math.max(0, Math.floor(v * c.height)));
      const idx = (y * c.width + x) * 4;
      const rr = data[idx] ?? 0;
      const gg = data[idx + 1] ?? 0;
      const bb = data[idx + 2] ?? 0;
      return (rr + gg + bb) / 3 > 60;
    };
  } catch {
    return null;
  }
}

function computeBatchSizes(total: number): number[] {
  if (total <= 4) return [total];

  const g4 = Math.floor(total / 4);
  const r = total % 4;

  if (r === 0) return Array(g4).fill(4);
  if (r === 1) return g4 >= 1 ? [...Array(g4 - 1).fill(4), 3, 3] : [3, 3];
  if (r === 2) return g4 >= 2 ? [...Array(g4 - 2).fill(4), 3, 3, 4] : [3, 3];
  return [...Array(g4).fill(4), 3];
}

function buildBatchPlan(totalRoutes: number) {
  const sizes = computeBatchSizes(totalRoutes);
  const batchCount = sizes.length;

  const batchIndexArr = new Array<number>(totalRoutes).fill(0);
  const inBatchIndexArr = new Array<number>(totalRoutes).fill(0);
  const batchSizeArr = new Array<number>(totalRoutes).fill(1);

  let cursor = 0;
  for (let b = 0; b < batchCount; b++) {
    const s = sizes[b]!;
    for (let k = 0; k < s; k++) {
      if (cursor >= totalRoutes) break;
      batchIndexArr[cursor] = b;
      inBatchIndexArr[cursor] = k;
      batchSizeArr[cursor] = s;
      cursor++;
    }
  }

  const startFracByBatch: number[] = [];
  const windowFracByBatch: number[] = [];
  let before = 0;
  for (let b = 0; b < batchCount; b++) {
    const s = sizes[b]!;
    startFracByBatch[b] = before / totalRoutes;
    windowFracByBatch[b] = s / totalRoutes;
    before += s;
  }

  return { sizes, batchCount, batchIndexArr, inBatchIndexArr, batchSizeArr, startFracByBatch, windowFracByBatch };
}

function pickSpreadPoints(count: number, candidates: THREE.Vector3[], minAngleRad: number) {
  if (candidates.length === 0) return [];
  let best: THREE.Vector3[] = [];
  let bestMin = -1;

  const tries = 10;
  for (let t = 0; t < tries; t++) {
    const chosen: THREE.Vector3[] = [];
    chosen.push(candidates[Math.floor(Math.random() * candidates.length)]!);

    while (chosen.length < count) {
      let bestCand: THREE.Vector3 | null = null;
      let bestScore = -1;

      for (const c of candidates) {
        let minD = Infinity;
        for (const s of chosen) minD = Math.min(minD, angleBetween(c, s));
        if (minD > bestScore) {
          bestScore = minD;
          bestCand = c;
        }
      }
      if (!bestCand) break;
      chosen.push(bestCand);
    }

    let minPair = Infinity;
    for (let i = 0; i < chosen.length; i++) {
      for (let j = i + 1; j < chosen.length; j++) {
        minPair = Math.min(minPair, angleBetween(chosen[i]!, chosen[j]!));
      }
    }

    if (minPair > bestMin) {
      bestMin = minPair;
      best = chosen;
    }
    if (bestMin >= minAngleRad) break;
  }

  return best.slice(0, count);
}

function makeCircleTexture(size = 64) {
  const c = document.createElement("canvas");
  c.width = c.height = size;
  const ctx = c.getContext("2d");
  if (!ctx) throw new Error("no ctx");

  ctx.clearRect(0, 0, size, size);
  const r = size * 0.34;
  const cx = size / 2;
  const cy = size / 2;

  ctx.beginPath();
  ctx.arc(cx, cy, r, 0, Math.PI * 2);
  ctx.closePath();
  ctx.fillStyle = "rgba(255,255,255,1)";
  ctx.fill();

  ctx.globalCompositeOperation = "destination-in";
  const g = ctx.createRadialGradient(cx, cy, r * 0.9, cx, cy, r);
  g.addColorStop(0, "rgba(255,255,255,1)");
  g.addColorStop(1, "rgba(255,255,255,0)");
  ctx.fillStyle = g;
  ctx.fillRect(0, 0, size, size);
  ctx.globalCompositeOperation = "source-over";

  const tex = new THREE.CanvasTexture(c);
  tex.colorSpace = THREE.SRGBColorSpace;
  return tex;
}

function makeDotGlobe(points: THREE.Vector3[]) {
  const geom = new THREE.BufferGeometry();
  const pos = new Float32Array(points.length * 3);
  const seed = new Float32Array(points.length);
  const scale = new Float32Array(points.length);

  for (let i = 0; i < points.length; i++) {
    pos[i * 3 + 0] = points[i]!.x;
    pos[i * 3 + 1] = points[i]!.y;
    pos[i * 3 + 2] = points[i]!.z;
    seed[i] = Math.random();
    scale[i] = 0.65 + Math.random() * 1.4;
  }

  geom.setAttribute("position", new THREE.BufferAttribute(pos, 3));
  geom.setAttribute("aSeed", new THREE.BufferAttribute(seed, 1));
  geom.setAttribute("aScale", new THREE.BufferAttribute(scale, 1));

  const mat = new THREE.ShaderMaterial({
    transparent: true,
    depthWrite: false,
    blending: THREE.NormalBlending,
    uniforms: {
      uTime: { value: 0 },
      uPulseSpeed: { value: props.dotPulseSpeed }, // ใช้คุมจังหวะรวม
      uColor: { value: new THREE.Color(CFG.dotColor) },
      uSize: { value: 18.0 },
    },
    vertexShader: `
      uniform float uTime;
      uniform float uPulseSpeed;
      uniform float uSize;

      attribute float aSeed;
      attribute float aScale;

      varying float vAlpha;

      float saturate(float x) { return clamp(x, 0.0, 1.0); }

      void main() {
        vec3 p = position;
        vec4 mv = modelViewMatrix * vec4(p, 1.0);

        // forward-facing fade (hide back dots)
        vec3 n = normalize((modelMatrix * vec4(p, 0.0)).xyz);
        vec3 v = normalize(cameraPosition - (modelMatrix * vec4(p, 1.0)).xyz);
        float fwd = dot(n, v);
        float frontFade = smoothstep(-0.15, 0.55, fwd);

     
        float basePulse = 0.72 + 0.28 * sin(uTime * uPulseSpeed + aSeed * 6.2831);

      
        float twFreq = 0.65 + 2.75 * fract(aSeed * 17.0);        // per-point frequency
        float tw = 0.5 + 0.5 * sin(uTime * twFreq + aSeed * 19.7); // 0..1

       
        float spark = smoothstep(0.92, 1.0, tw); // rare highlight 0..1

      
        float twinkleGain = (0.78 + 0.22 * tw) + (0.45 * spark);
        vAlpha = basePulse * twinkleGain * frontFade;

   
        float sizeBoost = 0.92 + 0.10 * tw + 0.12 * spark;
        float size = uSize * aScale * sizeBoost * (1.0 / max(0.001, -mv.z));

        gl_PointSize = size;
        gl_Position = projectionMatrix * mv;
      }
    `,
    fragmentShader: `
      uniform vec3 uColor;
      varying float vAlpha;

      void main() {
        vec2 uv = gl_PointCoord - 0.5;
        float d = length(uv);

        // circle with soft edge (ยังไม่ใช่ glow)
        float circle = smoothstep(0.5, 0.18, d);

        // center micro-contrast (ทำให้ดูเป็น “spark” แบบคมๆ)
        float core = smoothstep(0.18, 0.0, d); // stronger in center

        float a = circle * vAlpha * (0.90 + 0.10 * core);
        gl_FragColor = vec4(uColor, a);
      }
    `,
  });

  const pts = new THREE.Points(geom, mat);

  disposers.push(() => {
    geom.dispose();
    mat.dispose();
  });

  return { pts, mat };
}


function makeStarfield(count = 1200, radius = 18) {
  const g = new THREE.BufferGeometry();
  const p = new Float32Array(count * 3);

  for (let i = 0; i < count; i++) {
    const u = Math.random();
    const v = Math.random();
    const theta = 2 * Math.PI * u;
    const phi = Math.acos(2 * v - 1);
    const rr = radius * (0.7 + Math.random() * 0.3);

    p[i * 3 + 0] = rr * Math.sin(phi) * Math.cos(theta);
    p[i * 3 + 1] = rr * Math.cos(phi);
    p[i * 3 + 2] = rr * Math.sin(phi) * Math.sin(theta);
  }

  g.setAttribute("position", new THREE.BufferAttribute(p, 3));
  const m = new THREE.PointsMaterial({
    size: 0.02,
    opacity: 0.60,
    transparent: true,
    depthWrite: false,
    blending: THREE.NormalBlending,
  });

  const s = new THREE.Points(g, m);
  disposers.push(() => {
    g.dispose();
    m.dispose();
  });
  return s;
}

function makeLogoPin(worldPos: THREE.Vector3, normal: THREE.Vector3, logoTex: THREE.Texture | undefined, size = 0.20) {
  const group = new THREE.Group();
  if (logoTex) {
    const mat = new THREE.SpriteMaterial({
      map: logoTex,
      transparent: true,
      opacity: 1,
      depthWrite: false,
      alphaTest: 0.35,
      blending: THREE.NormalBlending,
    });

    const spr = new THREE.Sprite(mat);
    spr.scale.set(size, size, 1);
    spr.position.copy(normal.clone().multiplyScalar(0.07));
    group.add(spr);

    disposers.push(() => mat.dispose());
  }

  group.position.copy(worldPos.clone().add(normal.clone().multiplyScalar(0.02)));
  return group;
}

function makeHubMarker(worldPos: THREE.Vector3, normal: THREE.Vector3, circleTex: THREE.Texture) {
  const mat = new THREE.SpriteMaterial({
    map: circleTex,
    color: new THREE.Color("#ffffff"),
    transparent: true,
    opacity: 0.9,
    blending: THREE.NormalBlending,
    depthWrite: false,
    alphaTest: 0.15,
  });

  const spr = new THREE.Sprite(mat);
  spr.scale.set(0.11, 0.11, 1);
  spr.position.copy(normal.clone().multiplyScalar(0.06));

  const group = new THREE.Group();
  group.add(spr);
  group.position.copy(worldPos.clone().add(normal.clone().multiplyScalar(0.02)));

  disposers.push(() => mat.dispose());
  return group;
}

function makeHubRoute(hubPos: THREE.Vector3, nodePos: THREE.Vector3, circleTex: THREE.Texture, color: string) {
  const a = hubPos.clone().normalize();
  const b = nodePos.clone().normalize();
  const axis = new THREE.Vector3().crossVectors(a, b).normalize();
  const angle = Math.acos(clamp(a.dot(b), -1, 1));

  const pts: THREE.Vector3[] = [];
  const steps = 140;
  for (let i = 0; i <= steps; i++) {
    const t = i / steps;
    const v = a.clone().applyAxisAngle(axis, angle * t).multiplyScalar(CFG.radius);
    const lift = Math.sin(Math.PI * t) * 0.30;
    v.add(v.clone().normalize().multiplyScalar(lift));
    pts.push(v);
  }

  const curve = new THREE.CatmullRomCurve3(pts);
  const ptsFwd = curve.getPoints(260);
  const ptsRev = [...ptsFwd].reverse();

  const baseGeom = new THREE.BufferGeometry().setFromPoints(ptsFwd);
  const baseMat = new THREE.LineDashedMaterial({
    color: new THREE.Color(color),
    transparent: true,
    opacity: props.baseLineOpacity!,
    dashSize: 0.10,
    gapSize: 0.06,
  });
  const baseLine = new THREE.Line(baseGeom, baseMat);
  baseLine.computeLineDistances();

  const outGeom = new THREE.BufferGeometry().setFromPoints(ptsFwd);
  outGeom.setDrawRange(0, 2);
  const outMat = new THREE.LineBasicMaterial({
    color: new THREE.Color(color),
    transparent: true,
    opacity: 0,
    blending: THREE.NormalBlending,
    depthWrite: false,
  });
  const outLine = new THREE.Line(outGeom, outMat);

  const inGeom = new THREE.BufferGeometry().setFromPoints(ptsRev);
  inGeom.setDrawRange(0, 2);
  const inMat = new THREE.LineBasicMaterial({
    color: new THREE.Color(color),
    transparent: true,
    opacity: 0,
    blending: THREE.NormalBlending,
    depthWrite: false,
  });
  const inLine = new THREE.Line(inGeom, inMat);

  const trailCount = Math.max(2, props.flowTrailCount ?? 7);
  const spacing = clamp(props.flowTrailSpacing ?? 0.06, 0.01, 0.2);

  type TrailDot = { spr: THREE.Sprite; mat: THREE.SpriteMaterial; offset: number; base: number };
  const makeTrail = () => {
    const dots: TrailDot[] = [];
    for (let i = 0; i < trailCount; i++) {
      const isHead = i === 0;
      const mat = new THREE.SpriteMaterial({
        map: circleTex,
        color: new THREE.Color(color),
        transparent: true,
        opacity: 0,
        blending: THREE.NormalBlending,
        depthWrite: false,
        alphaTest: 0.15,
      });

      const spr = new THREE.Sprite(mat);
      const base = isHead ? 0.10 : 0.075;
      spr.scale.set(base, base, 1);
      dots.push({ spr, mat, offset: i * spacing, base });
    }
    const group = new THREE.Group();
    dots.forEach((d) => group.add(d.spr));
    return { dots, group };
  };

  const outTrail = makeTrail();
  const inTrail = makeTrail();

  const updateTrail = (dots: TrailDot[], curvePts: THREE.Vector3[], u: number, fade: number) => {
    for (let i = 0; i < dots.length; i++) {
      const d = dots[i]!;
      const uu = u - d.offset;
      if (uu <= 0) {
        d.mat.opacity = 0;
        continue;
      }

      const idx = Math.floor(clamp(uu, 0, 1) * (curvePts.length - 1));
      d.spr.position.copy(curvePts[idx]!);

      const decay = 1 - i / dots.length;
      d.mat.opacity = (i === 0 ? 0.95 : 0.55 * decay) * fade;

      if (i === 0) {
        const s = d.base * (1 + 0.08 * Math.sin(u * 12.0));
        d.spr.scale.set(s, s, 1);
      } else {
        const s = d.base * (0.95 + 0.12 * decay);
        d.spr.scale.set(s, s, 1);
      }
    }
  };

  const update = (t: number, phaseIn: boolean, u: number) => {
    (baseMat as any).dashOffset = -t * 0.25;

    const fade = smoothstep(0.02, 0.10, u) * (1.0 - smoothstep(0.92, 0.995, u));

    const totalFwd = ptsFwd.length;
    const totalRev = ptsRev.length;

    if (phaseIn) {
      outMat.opacity = 0;
      inMat.opacity = props.activeLineOpacity! * fade;

      const k = Math.max(2, Math.floor(u * (totalRev - 1)));
      inGeom.setDrawRange(0, k);
      outGeom.setDrawRange(0, 2);

      updateTrail(inTrail.dots, ptsRev, u, fade);
      updateTrail(outTrail.dots, ptsFwd, 0, 0);
    } else {
      inMat.opacity = 0;
      outMat.opacity = props.activeLineOpacity! * fade;

      const k = Math.max(2, Math.floor(u * (totalFwd - 1)));
      outGeom.setDrawRange(0, k);
      inGeom.setDrawRange(0, 2);

      updateTrail(outTrail.dots, ptsFwd, u, fade);
      updateTrail(inTrail.dots, ptsRev, 0, 0);
    }
  };

  disposers.push(() => {
    baseGeom.dispose();
    baseMat.dispose();
    outGeom.dispose();
    outMat.dispose();
    inGeom.dispose();
    inMat.dispose();
    outTrail.dots.forEach((d) => d.mat.dispose());
    inTrail.dots.forEach((d) => d.mat.dispose());
  });

  return { baseLine, outLine, inLine, outDots: outTrail.group, inDots: inTrail.group, update };
}

onMounted(async () => {
  if (!canvas.value || !wrap.value) return;

  const reduce = window.matchMedia?.("(prefers-reduced-motion: reduce)")?.matches ?? false;

  renderer = new THREE.WebGLRenderer({
    canvas: canvas.value,
    antialias: true,
    alpha: true,
    powerPreference: "high-performance",
  });
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.outputColorSpace = THREE.SRGBColorSpace;
  renderer.toneMapping = THREE.NoToneMapping;
  renderer.setClearColor(0x000000, 0);

  scene = new THREE.Scene();

  camera = new THREE.PerspectiveCamera(45, 1, 0.1, 80);
  camera.position.set(0, 0, 3.7);

  scene.add(new THREE.AmbientLight(new THREE.Color("#ffffff"), 0.35));
  const key = new THREE.DirectionalLight(new THREE.Color("#ffffff"), 1.0);
  key.position.set(3, 2, 3);
  scene.add(key);

  scene.add(makeStarfield());

  const globe = new THREE.Group();
  scene.add(globe);

  const focusV = latLonToVec3(props.focusLat!, props.focusLon!, 1).normalize();
  const q = new THREE.Quaternion().setFromUnitVectors(focusV, new THREE.Vector3(0, 0, 1));
  globe.quaternion.copy(q);

  const sampleLand = props.landMaskUrl ? await loadLandMaskSampler(props.landMaskUrl) : null;

  const candidates = fibonacciSphere(CFG.dots * (sampleLand ? 2 : 1), CFG.radius);
  const dotPoints: THREE.Vector3[] = [];

  for (const p of candidates) {
    if (!sampleLand) {
      dotPoints.push(p);
      if (dotPoints.length >= CFG.dots) break;
      continue;
    }
    const { u, v } = vec3ToUV(p);
    if (sampleLand(u, v)) dotPoints.push(p);
    if (dotPoints.length >= CFG.dots) break;
  }

  const { pts: dotField, mat: dotMat } = makeDotGlobe(dotPoints);
  globe.add(dotField);

  const circleTex = makeCircleTexture(64);
  disposers.push(() => circleTex.dispose());

  const loader = new THREE.TextureLoader();
const urls = Array.from(
  new Set([hub.value.logo, ...nodes.value.map((n) => n.logo)].filter(Boolean))
);


  const texMap = new Map<string, THREE.Texture>();
  await Promise.all(
    urls.map(async (url) => {
      try {
        const tex = await loader.loadAsync(url);
        tex.colorSpace = THREE.SRGBColorSpace;
        texMap.set(url, tex);
        disposers.push(() => tex.dispose());
      } catch {}
    })
  );

  const hubVec = latLonToVec3(hub.value.lat, hub.value.lon, CFG.radius);
  const hubN = hubVec.clone().normalize();

  const routesGroup = new THREE.Group();
  globe.add(routesGroup);
 const hubTex = texMap.get(hub.value.logo);

// ✅ ถ้าโหลดโลโก้ได้ -> ใช้โลโก้ Lapnet แทนจุดสีขาว
if (hubTex) {
  routesGroup.add(makeLogoPin(hubVec, hubN, hubTex, 0.29)); // ปรับขนาดได้
} else {
  // ✅ fallback (ถ้าโลโก้ไม่โหลด) -> ใช้จุดสีขาวเหมือนเดิม
  routesGroup.add(makeHubMarker(hubVec, hubN, circleTex));
}


  const minSepRad = (props.minSeparationDeg! * Math.PI) / 180;
  const minHubRad = (props.minNodeToHubDeg! * Math.PI) / 180;
  const safeCandidates = dotPoints.filter((p) => angleBetween(p, hubVec) > minHubRad);

  const need = nodes.value.length;
  const chosen = props.autoDistribute
    ? pickSpreadPoints(need, safeCandidates.length ? safeCandidates : dotPoints, minSepRad)
    : [];

  const totalRoutes = nodes.value.length;
  const plan = buildBatchPlan(totalRoutes);

  const routeUpdaters: Array<(t: number) => void> = [];

  for (let i = 0; i < nodes.value.length; i++) {
    const node = nodes.value[i]!;
    const nodePos = chosen[i]
      ? chosen[i]!.clone()
      : latLonToVec3(node.lat ?? (Math.random() * 180 - 90), node.lon ?? (Math.random() * 360 - 180), CFG.radius);

    const nodeN = nodePos.clone().normalize();
    const color = i % 2 === 0 ? CFG.arcColor : "#9CA3AF";

    routesGroup.add(makeLogoPin(nodePos, nodeN, texMap.get(node.logo), props.nodePinScale!));

    const route = makeHubRoute(hubVec, nodePos, circleTex, color);
    routesGroup.add(route.baseLine);
    routesGroup.add(route.outLine);
    routesGroup.add(route.inLine);
    routesGroup.add(route.outDots);
    routesGroup.add(route.inDots);

    const b = plan.batchIndexArr[i] ?? 0;
    const k = plan.inBatchIndexArr[i] ?? 0;
    const s = plan.batchSizeArr[i] ?? 1;

    routeUpdaters.push((t) => {
      const cyc = props.cycleSeconds!;
      const half = cyc / 2;
      const local = t % cyc;

      const phaseIn = local < half;
      const phaseT = phaseIn ? local : (local - half);

      const start = (plan.startFracByBatch[b] ?? 0) * half;
      const window = (plan.windowFracByBatch[b] ?? (1 / plan.batchCount)) * half;

      const frac = clamp(props.microStaggerFrac ?? 0.24, 0, 0.45);
      const microMax = window * frac;
      const micro = s <= 1 ? 0 : (k / Math.max(1, s - 1)) * microMax;

      const denom = Math.max(0.0001, window - microMax);

      const uRaw = (phaseT - start - micro) / denom;
      const u = (uRaw <= 0 || uRaw >= 1) ? 0 : easeInOutCubic(uRaw); // ✅ smoother

      route.update(t, phaseIn, u);
    });
  }

  const resize = () => {
    if (!renderer || !camera || !wrap.value) return;
    const rect = wrap.value.getBoundingClientRect();
    const w = Math.max(1, Math.floor(rect.width));
    const h = Math.max(1, Math.floor(rect.height));
    camera.aspect = w / h;
    camera.updateProjectionMatrix();
    renderer.setSize(w, h, false);
  };

  ro = new ResizeObserver(resize);
  ro.observe(wrap.value);
  resize();

  let isDown = false;
  let lastX = 0;
  let lastY = 0;
  const target = { rx: 0, ry: 0 };
  const current = { rx: 0, ry: 0 };

  const onDown = (e: PointerEvent) => {
    isDown = true;
    lastX = e.clientX;
    lastY = e.clientY;
  };
  const onUp = () => (isDown = false);
  const onMove = (e: PointerEvent) => {
    const w = window.innerWidth || 1;
    const h = window.innerHeight || 1;
    const nx = (e.clientX / w) * 2 - 1;
    const ny = (e.clientY / h) * 2 - 1;

    target.rx = -ny * 0.12;
    target.ry = nx * 0.22;

    if (!isDown) return;
    const dx = e.clientX - lastX;
    const dy = e.clientY - lastY;
    lastX = e.clientX;
    lastY = e.clientY;

    globe.rotation.y += dx * 0.004;
    globe.rotation.x += dy * 0.003;
  };

  canvas.value.addEventListener("pointerdown", onDown);
  window.addEventListener("pointerup", onUp);
  window.addEventListener("pointermove", onMove);

  disposers.push(() => {
    canvas.value?.removeEventListener("pointerdown", onDown);
    window.removeEventListener("pointerup", onUp);
    window.removeEventListener("pointermove", onMove);
  });

  if (!reduce) {
    globe.scale.set(0.96, 0.96, 0.96);
    gsap.to(globe.scale, { x: 1, y: 1, z: 1, duration: 1.05, ease: "power3.out" });
    gsap.fromTo(camera.position, { z: 4.2 }, { z: 3.7, duration: 1.1, ease: "power3.out" });
  }

  const clock = new THREE.Clock();

  const tick = () => {
    raf = requestAnimationFrame(tick);
    if (!renderer || !scene || !camera) return;

    const t = clock.getElapsedTime();

    if ((dotMat as any)?.uniforms?.uTime) (dotMat as any).uniforms.uTime.value = t;
    if ((dotMat as any)?.uniforms?.uPulseSpeed) (dotMat as any).uniforms.uPulseSpeed.value = props.dotPulseSpeed!;

    for (const u of routeUpdaters) u(t);

    const baseSpin = reduce ? 0.03 : 0.07;
    globe.rotation.y += 0.002 * baseSpin;

    current.rx = THREE.MathUtils.lerp(current.rx, target.rx, 0.05);
    current.ry = THREE.MathUtils.lerp(current.ry, target.ry, 0.05);
    globe.rotation.x = THREE.MathUtils.lerp(globe.rotation.x, current.rx, 0.03);
    globe.rotation.y = THREE.MathUtils.lerp(globe.rotation.y, globe.rotation.y + current.ry * 0.01, 0.03);

    renderer.render(scene, camera);
  };

  tick();
});

onBeforeUnmount(() => {
  cancelAnimationFrame(raf);
  ro?.disconnect();
  ro = null;

  disposers.forEach((d) => d());
  disposers.length = 0;

  renderer?.dispose();
  renderer = null;

  scene = null;
  camera = null;
});
</script>

<style scoped>
.globeWrap {
  width: min(1240px, 96vw);
  aspect-ratio: 16 / 9;
  max-height: 680px;
  margin: 0 auto;
  position: relative;
  border-radius: 30px;

  background:
    radial-gradient(980px 580px at 50% 35%, rgba(124, 58, 237, 0.10), transparent 60%),
    radial-gradient(760px 520px at 50% 20%, rgba(34, 211, 238, 0.05), transparent 62%),
    linear-gradient(180deg, rgba(5, 2, 13, 1), rgba(4, 1, 10, 1));
  overflow: hidden;

  box-shadow:
    0 28px 80px rgba(0,0,0,0.55),
    inset 0 1px 0 rgba(255,255,255,0.06);
}

.globeCanvas { width: 100%; height: 100%; display: block; }

/* top */
.hubTop{
  position:absolute;
  top: 14px;
  left: 50%;
  transform: translateX(-50%);
  display:flex;
  align-items:center;
  gap: 10px;
  padding: 10px 12px;
  border-radius: 999px;
  background: rgba(10, 6, 20, 0.55);
  border: 1px solid rgba(255,255,255,0.10);
  backdrop-filter: blur(10px);
  pointer-events:none;
}
.hubTopLogo{
  width: 34px;
  height: 34px;
  border-radius: 999px;
  object-fit: contain;
  background: rgba(255,255,255,0.10);
  border: 1px solid rgba(255,255,255,0.10);
  padding: 6px;
}
.hubTopText{
  font-size: 12px;
  font-weight: 700;
  color: rgba(255,255,255,0.88);
}

/* ✅ center */

</style>
