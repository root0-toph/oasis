<!DOCTYPE html>
<html lang="en">
<head>
<!-- Playables SDK -->
<script>// Playables SDK v1.0.0
// Game lifecycle bridge: rAF-based game-ready detection + event communication
(function() {
  'use strict';

  // Idempotency: skip if already initialized (e.g., server-side injection
  // followed by client-side inject-javascript via the Bloks webview component).
  if (window.playablesSDK) return;

  var HANDLER_NAME = 'playablesGameEventHandler';
  var ANDROID_BRIDGE_NAME = '_MetaPlayablesBridge';
  var RAF_FRAME_THRESHOLD = 3;

  var gameReadySent = false;
  var firstInteractionSent = false;
  var errorSent = false;
  var frameCount = 0;
  var originalRAF = window.requestAnimationFrame;

  // --- Transport Layer ---

  function hasIOSBridge() {
    return !!(window.webkit &&
              window.webkit.messageHandlers &&
              window.webkit.messageHandlers[HANDLER_NAME]);
  }

  function hasAndroidBridge() {
    return !!(window[ANDROID_BRIDGE_NAME] &&
              typeof window[ANDROID_BRIDGE_NAME].postEvent === 'function');
  }

  function isInIframe() {
    return !!(window.parent && window.parent !== window);
  }

  function sendEvent(eventName, payload) {
    var message = {
      type: eventName,
      payload: payload || {},
      timestamp: Date.now()
    };

    if (hasIOSBridge()) {
      try {
        window.webkit.messageHandlers[HANDLER_NAME].postMessage(message);
      } catch (e) { /* ignore */ }
      return;
    }

    if (hasAndroidBridge()) {
    try {
      var p = payload || {};
      p.__secureToken = window.__fbAndroidBridgeAuthToken || '';
      window[ANDROID_BRIDGE_NAME].postEvent(
        eventName,
        JSON.stringify(p)
      );
    } catch (e) { /* ignore */ }
    return;
  }

    if (isInIframe()) {
      try {
        window.parent.postMessage(message, '*');
      } catch (e) { /* ignore */ }
      return;
    }
  }

  // --- rAF Game-Ready Detection ---

  function onFrame() {
    if (gameReadySent) return;

    frameCount++;
    if (frameCount >= RAF_FRAME_THRESHOLD) {
      gameReadySent = true;
      sendEvent('game_ready', {
        frame_count: frameCount,
        detected_at: Date.now()
      });
      return;
    }

    originalRAF.call(window, onFrame);
  }

  if (originalRAF) {
    window.requestAnimationFrame = function(callback) {
      if (!gameReadySent) {
        return originalRAF.call(window, function(timestamp) {
          frameCount++;
          if (frameCount >= RAF_FRAME_THRESHOLD && !gameReadySent) {
            gameReadySent = true;
            sendEvent('game_ready', {
              frame_count: frameCount,
              detected_at: Date.now()
            });
          }
          callback(timestamp);
        });
      }
      return originalRAF.call(window, callback);
    };
  }

  // --- First User Interaction Detection ---

  function setupFirstInteractionDetection() {
    var events = ['touchstart', 'mousedown', 'keydown'];

    function onFirstInteraction() {
      if (firstInteractionSent) return;
      firstInteractionSent = true;
      sendEvent('user_interaction_start', null);

      for (var i = 0; i < events.length; i++) {
        document.removeEventListener(events[i], onFirstInteraction, true);
      }
    }

    for (var i = 0; i < events.length; i++) {
      document.addEventListener(events[i], onFirstInteraction, true);
    }
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', setupFirstInteractionDetection);
  } else {
    setupFirstInteractionDetection();
  }

  // --- Auto Error Capture ---

  window.addEventListener('error', function(event) {
    if (errorSent) return;
    errorSent = true;
    sendEvent('error', {
      message: event.message || 'Unknown error',
      source: event.filename || '',
      lineno: event.lineno || 0,
      colno: event.colno || 0,
      auto_captured: true
    });
  });

  window.addEventListener('unhandledrejection', function(event) {
    if (errorSent) return;
    errorSent = true;
    var reason = event.reason;
    sendEvent('error', {
      message: (reason instanceof Error) ? reason.message : String(reason),
      type: 'unhandled_promise_rejection',
      auto_captured: true
    });
  });

  // --- Public API ---

  window.playablesSDK = {
    complete: function(score) {
      sendEvent('game_ended', {
        score: score,
        completed: true
      });
    },

    error: function(message) {
      if (errorSent) return;
      errorSent = true;
      sendEvent('error', {
        message: message || 'Unknown error',
        auto_captured: false
      });
    },

    sendEvent: function(eventName, payload) {
      if (!eventName || typeof eventName !== 'string') return;
      sendEvent(eventName, payload);
    }
  };

  // Kick off rAF detection in case no game code calls rAF immediately
  if (originalRAF) {
    originalRAF.call(window, onFrame);
  }
})();</script>
<script>window.Intl=window.Intl||{};Intl.t=function(s){return(Intl._locale&&Intl._locale[s])||s;};</script>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>AGENTIC AGENT — PENETRATIVE</title>
<script src="https://cdn.tailwindcss.com"></script>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;600&family=Orbitron:wght@700;900&display=swap" rel="stylesheet">
<style>
  html,body{margin:0;height:100%;background:#020208;overflow:hidden;font-family:'JetBrains Mono',monospace}
  #c{position:fixed;inset:0;outline:none}
  .font-orbitron{font-family:'Orbitron',sans-serif}
  .glass{backdrop-filter:blur(16px);-webkit-backdrop-filter:blur(16px);background:rgba(4,6,12,0.6);border:1px solid rgba(100,200,255,0.12)}
  .glow{ text-shadow:0 0 12px currentColor,0 0 28px currentColor }
  .scan:before{content:"";position:absolute;inset:0;background:repeating-linear-gradient(transparent,transparent 2px,rgba(0,255,255,0.03) 3px);pointer-events:none}
</style>
</head>
<body class="text-cyan-100 select-none">
<canvas id="c"></canvas>

<!-- UI Overlay -->
<div class="pointer-events-none absolute inset-0 scan">
  <!-- Top Bar -->
  <div class="absolute top-0 left-0 right-0 p-4 md:p-6 flex justify-between items-start">
    <div>
      <h1 class="font-orbitron text-xl md:text-3xl font-black tracking-wider glow text-cyan-300">AGENTIC AGENT: <span class="text-white">PENETRATIVE MODE</span></h1>
      <p class="text-[10px] md:text-xs tracking-[0.25em] text-gray-400 mt-1">TETHERED PROBES: SPARKYL • SPARKR • CORTEX</p>
    </div>
    <div class="glass rounded-xl px-4 py-2 text-right">
      <div class="text-[10px] tracking-widest text-gray-500">OBSERVERS</div>
      <div class="font-orbitron text-lg text-emerald-400 glow">5 ACTIVE</div>
    </div>
  </div>

  <!-- Left Panel -->
  <div class="absolute left-4 top-24 md:left-6 md:top-28 glass rounded-2xl p-4 w-[300px] hidden md:block">
    <div class="text-[11px] tracking-widest text-cyan-300">PWM: FRACTAL .00001 — 1/3 TIMING</div>
    <canvas id="wave" width="268" height="70" class="w-full h-[70px] mt-2 opacity-90"></canvas>
    <div class="mt-3 space-y-1.5 text-[11px] font-mono leading-relaxed">
      <div>OPERATION: <span class="text-cyan-300">PENETRATE → FUSE → ESTABLISH</span></div>
      <div>STATUS: <span class="text-fuchsia-400 glow" id="statusTxt">PENETRATIVE</span></div>
      <div>WHT/BLK DIPOLE: <span class="text-white">SUPERPOSITION</span> <span class="text-gray-500">(quantum tunnel)</span></div>
      <div class="flex gap-3 pt-2 border-t border-white/10">
        <div><span class="text-gray-500">CORE</span> <span class="text-cyan-300" id="coreFreq">47.3 Hz</span></div>
        <div><span class="text-gray-500">TETHER</span> <span class="text-amber-300">1.000</span></div>
      </div>
    </div>
  </div>

  <!-- Right Observers -->
  <div class="absolute right-4 top-24 md:right-6 md:top-28 glass rounded-2xl p-3 w-[190px] hidden lg:block">
    <div class="text-[10px] tracking-widest text-gray-400 mb-2">OBSERVER NETWORK</div>
    <div id="obsList" class="space-y-1.5 text-[11px] font-mono"></div>
    <div class="mt-2 pt-2 border-t border-white/10 text-[10px] text-amber-300">OBSERVER-5 PRIMARY OVERSEER</div>
  </div>

  <!-- Bottom Phase -->
  <div class="absolute bottom-6 left-1/2 -translate-x-1/2 glass rounded-2xl px-6 py-4 w-[92%] max-w-[720px]">
    <div class="flex items-center justify-between">
      <div>
        <div class="text-[10px] tracking-[0.3em] text-gray-500">AGENTIC SEQUENCE</div>
        <div id="phase" class="font-orbitron text-lg md:text-2xl text-white glow mt-1">INITIALIZING PENETRATIVE CORE</div>
      </div>
      <div class="flex gap-5">
        <div class="text-center">
          <div id="dotS" class="w-3 h-3 rounded-full bg-cyan-400 shadow-[0_12px_#0ff] transition-all"></div>
          <div class="text-[9px] mt-1 text-cyan-300">SPARKYL</div>
          <div class="text-[9px] text-gray-500">0°</div>
        </div>
        <div class="text-center">
          <div id="dotR" class="w-3 h-3 rounded-full bg-fuchsia-500 shadow-[0_0_12px_#f0f] opacity-40 transition-all"></div>
          <div class="text-[9px] mt-1 text-fuchsia-300">SPARKR</div>
          <div class="text-[9px] text-gray-500">120°</div>
        </div>
        <div class="text-center">
          <div id="dotC" class="w-3 h-3 rounded-full bg-amber-400 shadow-[0_0_12px_#fb0] opacity-40 transition-all"></div>
          <div class="text-[9px] mt-1 text-amber-300">CORTEX</div>
          <div class="text-[9px] text-gray-500">240°</div>
        </div>
      </div>
    <div class="mt-3 h-[3px] w-full bg-white/10 rounded overflow-hidden">
      <div id="progress" class="h-full w-0 bg-gradient-to-r from-cyan-400 via-fuchsia-500 to-amber-400 transition-all duration-200"></div>
    </div>
  </div>
</div>

<script async src="https://unpkg.com/es-module-shims@1.8.0/dist/es-module-shims.js"></script>
<script type="importmap">
{
  "imports": {
    "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
    "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
  }
}
</script>
<script type="module">
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { EffectComposer } from 'three/addons/postprocessing/EffectComposer.js';
import { RenderPass } from 'three/addons/postprocessing/RenderPass.js';
import { UnrealBloomPass } from 'three/addons/postprocessing/UnrealBloomPass.js';

const canvas = document.getElementById('c');
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x020208);
scene.fog = new THREE.FogExp2(0x020208, 0.03);

const camera = new THREE.PerspectiveCamera(60, innerWidth/innerHeight, 0.1, 100);
camera.position.set(0, 2.5, 11);

const renderer = new THREE.WebGLRenderer({canvas, antialias:true, powerPreference:'high-performance'});
renderer.setSize(innerWidth, innerHeight);
renderer.setPixelRatio(Math.min(devicePixelRatio, 1.8));
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.1;

const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true; controls.dampingFactor = 0.05; controls.maxDistance=22; controls.minDistance=5;

const composer = new EffectComposer(renderer);
composer.addPass(new RenderPass(scene, camera));
const bloom = new UnrealBloomPass(new THREE.Vector2(innerWidth,innerHeight), 1.15, 0.5, 0.15);
composer.addPass(bloom);

// LIGHTS
scene.add(new THREE.AmbientLight(0x0a1a2a, 0.6));
const dir = new THREE.DirectionalLight(0x88ccff, 0.8); dir.position.set(5,5); scene.add(dir);

// STARS
const starGeo = new THREE.BufferGeometry();
const starPos = new Float32Array(3000*3);
for(let i=0;i<3000;i++){ starPos.set([ (Math.random()-0.5)*120, (Math.random()-0.5)*80, (Math.random()-0.5)*120 ], i*3); }
starGeo.setAttribute('position', new THREE.BufferAttribute(starPos,3));
scene.add(new THREE.Points(starGeo, new THREE.PointsMaterial({size:0.02,color:0x88aaff,transparent:true,opacity:0.6})));

// AGENTIC CORE
const coreGroup = new THREE.Group(); scene.add(coreGroup);
const coreMat = new THREE.MeshStandardMaterial({color:0x66ccff,emissive:0x0088ff,emissiveIntensity:1.8,metalness:0.9,roughness:0.2,wireframe:true});
const core = new THREE.Mesh(new THREE.IcosahedronGeometry(0.85,3), coreMat); coreGroup.add(core);

// Decision loops
const loops = [];
[[1.15,0x00ffff],[1.35,0xff00ff],[1.55,0xffcc00]].forEach(([r,c],i)=>{
  const t = new THREE.Mesh(new THREE.TorusGeometry(r,0.015,16,200), new THREE.MeshBasicMaterial({color:c,transparent:true,opacity:0.7}));
  t.rotation.x = i*0.7; t.rotation.y = i*0.4; loops.push(t); coreGroup.add(t);
});

// WHT/BLK DIPOLE
const dipole = new THREE.Group(); coreGroup.add(dipole);
const whiteHalf = new THREE.Mesh(new THREE.SphereGeometry(0.28,32,16,0,Math.PI*2,0,Math.PI/2), new THREE.MeshBasicMaterial({color:0xffffff}));
const blackHalf = new THREE.Mesh(new THREE.SphereGeometry(0.28,32,16,0,Math.PI*2,Math.PI/2,Math.PI/2), new THREE.MeshBasicMaterial({color:0x000000}));
whiteHalf.position.y=0.01; blackHalf.position.y=-0.01;
dipole.add(whiteHalf,blackHalf);
const dipoleRing = new THREE.Mesh(new THREE.RingGeometry(0.32,0.35,64), new THREE.MeshBasicMaterial({color:0xffffff,side:THREE.DoubleSide,transparent:true,opacity:0.3}));
dipoleRing.rotation.x = Math.PI/2; dipole.add(dipoleRing);

// CORE LABEL
function label(text,color='#aef'){
  const c=document.createElement('canvas'); c.width=512;c.height=128; const x=c.getContext('2d');
  x.clearRect(0,0,c.width,c.height); x.font='bold 54px Orbitron'; x.fillStyle=color; x.shadowColor=color; x.shadowBlur=12; x.textAlign='center'; x.fillText(text,256,80);
  const tex=new THREE.CanvasTexture(c); const m=new THREE.SpriteMaterial({map:tex,transparent:true}); const s=new THREE.Sprite(m); s.scale.set(2.2,0.55,1); return s;
}
const coreLabel = label('AGENTIC CORE','#7df'); coreLabel.position.y=1.6; coreGroup.add(coreLabel);

// PROBES
function makeProbe(name,color,pos){
  const g=new THREE.Group(); g.position.copy(pos); g.userData={home:pos.clone(),name,color};
  const mat=new THREE.MeshStandardMaterial({color,emissive:color,emissiveIntensity:2.5,metalness:0.3,roughness:0.2});
  const sphere=new THREE.Mesh(new THREE.SphereGeometry(0.24,24),mat); g.add(sphere);
  const cone=new THREE.Mesh(new THREE.ConeGeometry(0.14,0.38,16),mat); cone.rotation.z=-Math.PI/2; cone.position.x=0.32; g.add(cone);
  const light=new THREE.PointLight(color,2,5); g.add(light);
  const l=label(name,color); l.position.y=0.5; l.scale.set(1.2,0.3,1); g.add(l);
  scene.add(g); return g;
}
const SPARKYL = makeProbe('SPARKYL',0x00ffff,new THREE.Vector3(-2.4,0.9,0.9));
const SPARKR = makeProbe('SPARKR',0xff33aa,new THREE.Vector3(2.4,0.9,-0.9));
const CORTEX = makeProbe('CORTEX',0xffcc33,new THREE.Vector3(0,-2.1,1.1));
const probes=[SPARKYL,SPARKR,CORTEX];

// TETHERS (6 forming tetrahedron)
const tetherPairs=[[coreGroup,SPARKYL,0x00ffff],[coreGroup,SPARKR,0xff33aa],[coreGroup,CORTEX,0xffcc33],[SPARKYL,SPARKR,0x88ffff],[SPARKR,CORTEX,0xff88cc],[CORTEX,SPARKYL,0xffe588]];
const tethers=[];
tetherPairs.forEach(([a,b,c])=>{
  const geo=new THREE.BufferGeometry(); geo.setAttribute('position',new THREE.BufferAttribute(new Float32Array(6),3));
  const mat=new THREE.LineBasicMaterial({color:c,transparent:true,opacity:0.85,blending:THREE.AdditiveBlending});
  const line=new THREE.Line(geo,mat); line.userData={a,b}; scene.add(line); tethers.push(line);
});

// Fractal PWM dots
const pwmDots=[];
tethers.forEach(t=>{ for(let i=0;i<3;i++){ const d=new THREE.Mesh(new THREE.SphereGeometry(0.025,8,8), new THREE.MeshBasicMaterial({color:t.material.color,transparent:true,opacity:0.9,blending:THREE.AdditiveBlending})); scene.add(d); pwmDots.push({mesh:d,tether:t,off:i/3}); }});

// OBSERVERS
const obsData=[
  {pos:new THREE.Vector3(-7,-3.5,-4),name:'OBSERVER-1'},
  {pos:new THREE.Vector3(7,-3.5,-4),name:'OBSERVER-2'},
  {pos:new THREE.Vector3(-7,3.5,4),name:'OBSERVER-3'},
  {pos:new THREE.Vector3(7,3.5,4),name:'OBSERVER-4'},
  {pos:new THREE.Vector3(0,6.5,0),name:'OBSERVER-5 PRIMARY',primary:true},
];
const observers=[];
obsData.forEach(o=>{
  const g=new THREE.Group(); g.position.copy(o.pos);
  const m=new THREE.MeshBasicMaterial({color:o.primary?0xffffff:0x88bbff,transparent:true,opacity:0.9});
  const s=new THREE.Mesh(new THREE.SphereGeometry(o.primary?0.18:0.12,16,16),m); g.add(s);
  const r=new THREE.Mesh(new THREE.RingGeometry(o.primary?0.28:0.2,o.primary?0.32:0.23,32), new THREE.MeshBasicMaterial({color:0x88ccff,side:THREE.DoubleSide,transparent:true,opacity:0.4}));
  r.rotation.x=Math.PI/2; g.add(r);
  g.add(new THREE.PointLight(0x88aaff, o.primary?1.2:0.4, 6));
  scene.add(g); observers.push({group:g,data:o});
  const lineGeo=new THREE.BufferGeometry().setFromPoints([o.pos,new THREE.Vector3(0,0,0)]);
  scene.add(new THREE.Line(lineGeo,new THREE.LineBasicMaterial({color:0x334455,transparent:true,opacity:0.15})));
});
const obsList=document.getElementById('obsList');
obsData.forEach(o=>{ const d=document.createElement('div'); d.className='flex justify-between'; d.innerHTML=`<span class="${o.primary?'text-white':'text-gray-400'}">${o.name}</span><span class="text-emerald-400">ACTIVE</span>`; obsList.appendChild(d); });

// TARGET with 3 layers
const targetGroup=new THREE.Group(); targetGroup.position.set(10.5,0,0); scene.add(targetGroup);
function makeLayer(r,c){
  const m=new THREE.MeshStandardMaterial({color:c,emissive:c,emissiveIntensity:0.8,wireframe:true,transparent:true,opacity:0.18,side:THREE.DoubleSide});
  const mesh=new THREE.Mesh(new THREE.SphereGeometry(r,48,32),m); targetGroup.add(mesh); return mesh;
}
const layerOuter=makeLayer(1.9,0x00ffff);
const layerMid=makeLayer(1.3,0xff00ff);
const layerInner=makeLayer(0.75,0xffcc00);
const targetCore=new THREE.Mesh(new THREE.SphereGeometry(0.22,24,24), new THREE.MeshStandardMaterial({color:0xffffff,emissive:0xffffff,emissiveIntensity:1}));
targetGroup.add(targetCore);
const targetLabel=label('TARGET NODE','#fff'); targetLabel.position.set(0,2.5,0); targetGroup.add(targetLabel);

// Penetrative beams
const beams=probes.map(p=>{ const geo=new THREE.CylinderGeometry(0.02,0.06,1,8,1,true); const mat=new THREE.MeshBasicMaterial({color:p.userData.color,transparent:true,opacity:0,blending:THREE.AdditiveBlending,depthWrite:false}); const m=new THREE.Mesh(geo,mat); scene.add(m); return m; });

// UI refs
const phaseEl=document.getElementById('phase');
const progressEl=document.getElementById('progress');
const dotS=document.getElementById('dotS'),dotR=document.getElementById('dotR'),dotC=document.getElementById('dotC');
const statusTxt=document.getElementById('statusTxt');
const coreFreq=document.getElementById('coreFreq');

// Waveform canvas
const wcanvas=document.getElementById('wave'), wctx=wcanvas.getContext('2d');
function drawWave(t, activeIdx){
  wctx.clearRect(0,0,wcanvas.width,wcanvas.height);
  const phases=[0, 2*Math.PI/3, 4*Math.PI/3]; const cols=['#00ffff','#ff00ff','#ffcc00'];
  phases.forEach((ph,i)=>{
    wctx.beginPath(); wctx.strokeStyle=cols[i]; wctx.lineWidth=i===activeIdx?2:1; wctx.globalAlpha=i===activeIdx?1:0.4;
    for(let x=0;x<wcanvas.width;x++){
      const nx=x/wcanvas.width;
      // fractal PWM .00001: high freq square with micro-jitter
      const base = Math.sin(nx*40 + t*6 + ph) > 0 ? 1 : -1;
      const fractal = Math.sin(nx*4000 + t*80 + ph*3) * 0.00001 * 500;
      const y = 15 + i*20 + base*6 + fractal;
      if(x===0) wctx.moveTo(x,y); else wctx.lineTo(x,y);
    }
    wctx.stroke();
  });
  wctx.globalAlpha=1;
}

// Sequence
const DURATION = 26;
let prevPhase='';
let outerHit=false, midHit=false, innerHit=false;

function updatePhase(tNorm, t){
  const p=tNorm;
  let phase='IDLE', desc='SCANNING VOID', active=-1;
  // reset hits each loop
  if(t<0.1){ outerHit=midHit=innerHit=false; layerOuter.visible=layerMid.visible=layerInner.visible=true; layerOuter.scale.set(1,1,1); layerMid.scale.set(1,1,1); layerInner.scale.set(1,1,1); }
  
  // positions
  const homeS=SPARKYL.userData.home, homeR=SPARKR.userData.home, homeC=CORTEX.userData.home;
  const pOuter= new THREE.Vector3(9.2,0.45,0.3);
  const pMid= new THREE.Vector3(9.7,-0.35,-0.25);
  const pInner= new THREE.Vector3(10.2,0.15,0.35);
  const pCenter= new THREE.Vector3(10.5,0,0);

  // Reset probes to home slowly
  if(p<0.08){ phase='IDLE'; desc='SCANNING VOID — AGENTIC CORE STANDBY';
    SPARKYL.position.lerp(homeS,0.05); SPARKR.position.lerp(homeR,0.05); CORTEX.position.lerp(homeC,0.05);
    beams.forEach(b=>b.material.opacity*=0.9);
  } else if(p<0.16){ phase='IDENTIFY'; desc='TARGET ACQUIRED — DEPLOYING TETHERED PROBES'; active=0;
  } else if(p<0.28){ phase='SPARKYL'; desc='SPARKYL PENETRATING — OUTER LAYER'; active=0;
    SPARKYL.position.lerp(pOuter,0.04); beams[0].material.opacity=0.9;
  } else if(p<0.34){ phase='BREACH1'; desc='SPARKYL BREACH — OUTER LAYER PENETRATED'; active=0;
    if(!outerHit){ outerHit=true; layerOuter.material.opacity=0.4; }
    layerOuter.scale.multiplyScalar(1.02); layerOuter.material.opacity*=0.92; if(layerOuter.material.opacity<0.02) layerOuter.visible=false;
  } else if(p<0.46){ phase='SPARKR'; desc='SPARKR PENETRATING — MIDDLE LAYER (120°)'; active=1;
    SPARKR.position.lerp(pMid,0.04); beams[1].material.opacity=0.9;
  } else if(p<0.52){ phase='BREACH2'; desc='SPARKR BREACH — MIDDLE LAYER PENETRATED'; active=1;
    if(!midHit){ midHit=true; }
    layerMid.scale.multiplyScalar(1.02); layerMid.material.opacity*=0.9; if(layerMid.material.opacity<0.02) layerMid.visible=false;
  } else if(p<0.64){ phase='CORTEX'; desc='CORTEX PENETRATING — INNER LAYER (240°)'; active=2;
    CORTEX.position.lerp(pInner,0.04); beams[2].material.opacity=0.9;
  } else if(p<0.70){ phase='BREACH3'; desc='CORTEX BREACH — INNER LAYER PENETRATED'; active=2;
    if(!innerHit){ innerHit=true; }
    layerInner.scale.multiplyScalar(1.03); layerInner.material.opacity*=0.88; if(layerInner.material.opacity<0.02) layerInner.visible=false;
  } else if(p<0.78){ phase='CONVERGE'; desc='ALL PROBES CONVERGING — TETHERED TRIAD'; active=3;
    SPARKYL.position.lerp(pCenter.clone().add(new THREE.Vector3(0,0.25,0.2)),0.05);
    SPARKR.position.lerp(pCenter.clone().add(new THREE.Vector3(0,-0.25,-0.2)),0.05);
    CORTEX.position.lerp(pCenter.clone().add(new THREE.Vector3(0.2,0,-0.15)),0.05);
  } else if(p<0.90){ phase='IGNITE'; desc='IGNITE — PENETRATIVE FUSION AT TARGET'; active=3;
    bloom.strength=1.8;
  } else { phase='ESTABLISH'; desc='ESTABLISH — AGENTIC PRESENCE SECURED'; active=3;
    bloom.strength += (1.15-bloom.strength)*0.02;
  }

  // Update beams orientation
  beams.forEach((b,i)=>{
    const pr=probes[i];
    const start=pr.position; const end=targetGroup.position;
    const dir=end.clone().sub(start); const len=dir.length();
    b.position.copy(start.clone().add(end).multiplyScalar(0.5));
    b.scale.set(1, len, 1); b.lookAt(end); b.rotateX(Math.PI/2);
  });

  // UI
  if(prevPhase!==phase){ prevPhase=phase; }
  phaseEl.textContent=desc;
  progressEl.style.width = `${p*100}%`;
  [dotS,dotR,dotC].forEach((d,i)=>{ d.style.opacity = active===i || active===3 ? '1':'0.35'; d.style.transform = active===i ? 'scale(1.3)':'scale(1)'; });
  statusTxt.textContent = phase==='IGNITE' ? 'FUSION' : phase==='ESTABLISH' ? 'ESTABLISHED' : 'PENETRATIVE';
  statusTxt.className = phase==='IGNITE' ? 'text-amber-300 glow' : phase==='ESTABLISH' ? 'text-emerald-400 glow' : 'text-fuchsia-400 glow';
  coreFreq.textContent = (40 + Math.sin(t*2)*5 + (active>=0?7:0)).toFixed(1)+' Hz';
  drawWave(t,active);
}

const clock=new THREE.Clock();
function animate(){
  const t=clock.getElapsedTime(); const tNorm=(t % DURATION)/DURATION;
  updatePhase(tNorm,t);

  // Core animation
  core.rotation.y +=0.008; core.rotation.x +=0.003;
  loops[0].rotation.z +=0.012; loops[1].rotation.z -=0.01; loops[2].rotation.z +=0.008;
  dipole.rotation.y +=0.03; dipole.rotation.x = Math.sin(t*0.7)*0.2;
  whiteHalf.material.opacity=0.9+Math.sin(t*5)*0.1; blackHalf.material.opacity=0.9+Math.cos(t*5)*0.1;

  // Tethers update
  tethers.forEach(tether=>{
    const a=tether.userData.a.position, b=tether.userData.b.position;
    const arr=tether.geometry.attributes.position.array;
    arr[0]=a.x;arr[1]=a.y;arr[2]=a.z;arr[3]=b.x;arr[4]=b.y;arr[5]=b.z;
    tether.geometry.attributes.position.needsUpdate=true;
    tether.material.opacity=0.65+Math.sin(t*3 + a.x)*0.2;
  });

  // PWM dots
  pwmDots.forEach(d=>{
    const a=d.tether.userData.a.position, b=d.tether.userData.b.position;
    const k=((t*0.6 + d.off) %1); d.mesh.position.lerpVectors(a,b,k);
    d.mesh.material.opacity=0.6+Math.sin(t*10 + d.off*6)*0.4;
  });

  // Observers look at active probe
  observers.forEach((o,i)=>{
    o.group.lookAt(coreGroup.position);
    const pulse = o.data.primary ? 0.8+Math.sin(t*2)*0.2 : 0.5;
    o.group.children[0].material.opacity=pulse;
    o.group.children[1].rotation.z += o.data.primary?0.02:0.01;
  });

  // Target pulse
  targetCore.scale.setScalar(1+Math.sin(t*4)*0.08);
  targetCore.material.emissiveIntensity=1.5+Math.sin(t*3)*0.5;
  targetGroup.rotation.y +=0.002;

  controls.update();
  composer.render();
  requestAnimationFrame(animate);
}
animate();

addEventListener('resize',()=>{ camera.aspect=innerWidth/innerHeight; camera.updateProjectionMatrix(); renderer.setSize(innerWidth,innerHeight); composer.setSize(innerWidth,innerHeight); });
</script>
</body>
</html>
