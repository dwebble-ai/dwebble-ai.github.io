<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Cosmic Explorer 3D</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300&family=Josefin+Sans:wght@100;300;400&display=swap');
*{margin:0;padding:0;box-sizing:border-box;}
html,body{width:100%;height:100%;overflow:hidden;background:#00000f;font-family:'Cormorant Garamond',serif;color:#f0eeff;cursor:none;}
#c{position:fixed;inset:0;z-index:1;}
#cur{position:fixed;z-index:9999;pointer-events:none;width:18px;height:18px;border:1px solid rgba(240,200,96,.85);border-radius:50%;transform:translate(-50%,-50%);transition:width .15s,height .15s;mix-blend-mode:screen;}
#cur::after{content:'';position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);width:3px;height:3px;background:#f0c060;border-radius:50%;}
#cur.big{width:44px;height:44px;border-color:rgba(64,208,255,.7);}
#cur.drag{width:28px;height:28px;border-color:rgba(255,200,100,.9);border-width:2px;}

#nav{position:fixed;right:28px;top:50%;transform:translateY(-50%);display:flex;flex-direction:column;gap:14px;z-index:20;}
.nd{width:8px;height:8px;border-radius:50%;border:1px solid rgba(240,192,96,.35);cursor:pointer;transition:all .3s;position:relative;}
.nd::before{content:attr(data-l);position:absolute;right:18px;top:50%;transform:translateY(-50%);font-family:'Josefin Sans',sans-serif;font-size:10px;font-weight:300;letter-spacing:.2em;color:rgba(240,192,96,0);white-space:nowrap;transition:color .3s;text-transform:uppercase;}
.nd:hover::before{color:rgba(240,192,96,.85);}
.nd:hover{border-color:#f0c060;background:rgba(240,192,96,.25);}
.nd.on{background:#f0c060;border-color:#f0c060;box-shadow:0 0 12px #f0c060;}

#cht{position:fixed;top:40px;left:50%;transform:translateX(-50%);text-align:center;z-index:20;pointer-events:none;}
#chn{font-family:'Josefin Sans',sans-serif;font-size:10px;font-weight:100;letter-spacing:.5em;color:rgba(200,190,255,.4);text-transform:uppercase;}
#chname{font-size:clamp(1.4rem,3vw,2.4rem);font-weight:300;letter-spacing:.07em;margin-top:4px;text-shadow:0 0 50px rgba(240,200,255,.4);}

#ip{position:fixed;bottom:0;left:0;right:0;padding:24px 50px 28px;background:linear-gradient(0deg,rgba(0,0,15,.97) 0%,transparent 100%);z-index:20;pointer-events:none;}
#it{max-width:560px;font-size:clamp(.82rem,1.35vw,1rem);font-weight:300;line-height:1.85;color:rgba(220,215,255,.78);font-style:italic;}
#istat{margin-top:7px;font-family:'Josefin Sans',sans-serif;font-size:10px;font-weight:100;letter-spacing:.35em;color:#f0c060;text-transform:uppercase;}

#zc{position:fixed;bottom:55px;right:28px;z-index:20;display:flex;flex-direction:column;gap:5px;}
.zb{width:30px;height:30px;background:rgba(3,6,3,.8);border:1px solid rgba(240,192,96,.28);color:rgba(240,192,96,.65);font-size:1.1rem;cursor:pointer;border-radius:2px;display:flex;align-items:center;justify-content:center;transition:all .18s;font-family:'Josefin Sans',sans-serif;}
.zb:hover{border-color:#f0c060;color:#f0c060;}

#hint{position:fixed;bottom:30px;left:50%;transform:translateX(-50%);font-family:'Josefin Sans',sans-serif;font-size:9px;font-weight:300;letter-spacing:.38em;color:rgba(240,192,96,.3);text-transform:uppercase;z-index:20;animation:pu 3s ease-in-out infinite;white-space:nowrap;}
@keyframes pu{0%,100%{opacity:.25}50%{opacity:.85}}

#dc{position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);background:rgba(2,3,20,.96);border:1px solid rgba(240,192,96,.22);border-radius:4px;padding:34px 42px;max-width:480px;width:90%;z-index:50;display:none;backdrop-filter:blur(28px);pointer-events:all;}
#dc.show{display:block;animation:ci .4s cubic-bezier(.2,0,.2,1);}
@keyframes ci{from{opacity:0;transform:translate(-50%,-46%)}to{opacity:1;transform:translate(-50%,-50%)}}
#dct{font-size:1.6rem;font-weight:400;letter-spacing:.07em;color:#ffd880;margin-bottom:4px;}
#dcs{font-family:'Josefin Sans',sans-serif;font-size:10px;letter-spacing:.35em;color:rgba(200,190,255,.42);text-transform:uppercase;margin-bottom:16px;}
#dcb{font-size:.95rem;line-height:1.9;font-weight:300;font-style:italic;color:rgba(220,215,255,.88);}
#dcf{margin-top:18px;display:grid;grid-template-columns:1fr 1fr;gap:12px 24px;}
.dcfi{font-family:'Josefin Sans',sans-serif;font-size:10px;letter-spacing:.12em;text-transform:uppercase;color:rgba(200,190,255,.38);}
.dcfi span{display:block;color:#f0c060;font-size:11px;margin-top:2px;}
#dcc{position:absolute;top:12px;right:15px;background:none;border:none;color:rgba(200,190,255,.38);font-family:'Josefin Sans',sans-serif;font-size:11px;letter-spacing:.2em;cursor:pointer;transition:color .2s;}
#dcc:hover{color:#f0c060;}

#veil{position:fixed;inset:0;background:#00000f;z-index:100;pointer-events:none;opacity:0;transition:opacity .65s;}
#veil.dk{opacity:1;}
#prog{position:fixed;bottom:0;left:0;height:1px;background:#f0c060;z-index:30;transition:width .9s;box-shadow:0 0 8px #f0c060;}
</style>
</head>
<body>
<div id="veil"></div>
<canvas id="c"></canvas>
<div id="cur"></div>

<div id="cht">
  <div id="chn">Chapter I</div>
  <div id="chname">The Big Bang</div>
</div>

<div id="ip">
  <div id="it"></div>
  <div id="istat"></div>
</div>

<div id="nav"></div>

<div id="zc">
  <button class="zb" id="zi">+</button>
  <button class="zb" id="zo">&#8722;</button>
  <button class="zb" id="zr" style="font-size:.58rem;letter-spacing:.03em;">RST</button>
</div>

<div id="hint">Drag to Orbit &nbsp;&middot;&nbsp; Scroll to Navigate &nbsp;&middot;&nbsp; Click Objects</div>
<div id="prog" style="width:14.2%"></div>

<div id="dc">
  <button id="dcc" onclick="window._closeD()">&#10005; CLOSE</button>
  <div id="dct"></div>
  <div id="dcs"></div>
  <div id="dcb"></div>
  <div id="dcf"></div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
(function(){
'use strict';

let W=window.innerWidth, H=window.innerHeight;
let mxRaw=W/2, myRaw=H/2;

const cvs = document.getElementById('c');
const renderer = new THREE.WebGLRenderer({canvas:cvs, antialias:true});
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.setSize(W, H);
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.15;
renderer.shadowMap.enabled = true;

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x00000f);

const camera = new THREE.PerspectiveCamera(52, W/H, 0.1, 6000);

window.addEventListener('resize', ()=>{
  W=window.innerWidth; H=window.innerHeight;
  camera.aspect=W/H; camera.updateProjectionMatrix();
  renderer.setSize(W,H);
});

let camTheta = 0.3, camPhi = 1.1, camRadius = 80, targetRadius = 80;
let smoothTheta = camTheta, smoothPhi = camPhi, smoothRadius = camRadius;
let autoRotSpeed = 0.0018, autoRotating = true;
let isDragging = false, dragStartX = 0, dragStartY = 0, dragTheta = 0, dragPhi = 0;
const ZMIN=12, ZMAX=400;

function updateCamera() {
  smoothPhi = Math.max(0.18, Math.min(Math.PI - 0.18, smoothPhi));
  const x = smoothRadius * Math.sin(smoothPhi) * Math.cos(smoothTheta);
  const y = smoothRadius * Math.cos(smoothPhi);
  const z = smoothRadius * Math.sin(smoothPhi) * Math.sin(smoothTheta);
  camera.position.set(x, y, z);
  camera.lookAt(0, 0, 0);
}

cvs.addEventListener('mousedown', e=>{
  if (e.button !== 0) return;
  isDragging = true; autoRotating = false;
  dragStartX = e.clientX; dragStartY = e.clientY;
  dragTheta = smoothTheta; dragPhi = smoothPhi;
  document.getElementById('cur').classList.add('drag');
  e.preventDefault();
});
window.addEventListener('mouseup', e=>{
  if(!isDragging) return;
  isDragging = false;
  setTimeout(()=>{ autoRotating = true; }, 2000);
  document.getElementById('cur').classList.remove('drag');
});
window.addEventListener('mousemove', e=>{
  mxRaw = e.clientX; myRaw = e.clientY;
  document.getElementById('cur').style.left = mxRaw+'px';
  document.getElementById('cur').style.top  = myRaw+'px';
  if (!isDragging) return;
  const dx = (e.clientX - dragStartX) / W;
  const dy = (e.clientY - dragStartY) / H;
  smoothTheta = dragTheta + dx * Math.PI * 2.2;
  smoothPhi   = dragPhi   - dy * Math.PI * 1.4;
});

cvs.addEventListener('touchstart', e=>{
  const t=e.touches[0];
  isDragging=true; autoRotating=false;
  dragStartX=t.clientX; dragStartY=t.clientY;
  dragTheta=smoothTheta; dragPhi=smoothPhi;
},{passive:true});
window.addEventListener('touchend', ()=>{ isDragging=false; setTimeout(()=>autoRotating=true,2000); });
window.addEventListener('touchmove', e=>{
  if(!isDragging) return;
  const t=e.touches[0];
  const dx=(t.clientX-dragStartX)/W, dy=(t.clientY-dragStartY)/H;
  smoothTheta=dragTheta+dx*Math.PI*2.2;
  smoothPhi=dragPhi-dy*Math.PI*1.4;
},{passive:true});

document.getElementById('zi').onclick = ()=>{ targetRadius=Math.max(ZMIN,targetRadius-18); };
document.getElementById('zo').onclick = ()=>{ targetRadius=Math.min(ZMAX,targetRadius+18); };
document.getElementById('zr').onclick = ()=>{ targetRadius=CHAPTERS[curCh].dz||80; camTheta=0.3; camPhi=1.1; smoothTheta=0.3; smoothPhi=1.1; autoRotating=true; };

let scrollCooldown=false;
window.addEventListener('wheel', e=>{
  if(e.ctrlKey||e.metaKey){ targetRadius=Math.max(ZMIN,Math.min(ZMAX,targetRadius+e.deltaY*.2)); return; }
  if(transitionActive||scrollCooldown) return;
  scrollCooldown=true; setTimeout(()=>scrollCooldown=false,1800);
  const d=e.deltaY>0?1:-1, nx=Math.max(0,Math.min(CHAPTERS.length-1,curCh+d));
  if(nx!==curCh) goTo(nx);
},{passive:true});

window.addEventListener('keydown', e=>{
  if(e.key==='Escape') _closeD();
  if(e.key==='ArrowRight'||e.key==='ArrowDown') goTo(Math.min(CHAPTERS.length-1,curCh+1));
  if(e.key==='ArrowLeft'||e.key==='ArrowUp')   goTo(Math.max(0,curCh-1));
});

const ray = new THREE.Raycaster();
const mVec = new THREE.Vector2();
let clickTargets = [];

cvs.addEventListener('mousemove', e=>{
  if(isDragging) return;
  mVec.x=(e.clientX/W)*2-1; mVec.y=-((e.clientY/H)*2-1);
  ray.setFromCamera(mVec,camera);
  const hits=ray.intersectObjects(clickTargets.map(c=>c.mesh),false);
  document.getElementById('cur').classList.toggle('big', hits.length>0 && !isDragging);
});
cvs.addEventListener('click', e=>{
  if(isDragging) return;
  mVec.x=(e.clientX/W)*2-1; mVec.y=-((e.clientY/H)*2-1);
  ray.setFromCamera(mVec,camera);
  const hits=ray.intersectObjects(clickTargets.map(c=>c.mesh),false);
  if(hits.length>0){const ct=clickTargets.find(c=>c.mesh===hits[0].object);if(ct)showD(ct.detail);}
});

// ── BACKGROUND STARS ─────────────────────────────────────
function makeStarField(){
  const n=4500, geo=new THREE.BufferGeometry();
  const pos=new Float32Array(n*3), col=new Float32Array(n*3), sz=new Float32Array(n);
  for(let i=0;i<n;i++){
    const th=Math.random()*Math.PI*2, ph=Math.acos(2*Math.random()-1), r=280+Math.random()*1100;
    pos[i*3]=r*Math.sin(ph)*Math.cos(th); pos[i*3+1]=r*Math.sin(ph)*Math.sin(th); pos[i*3+2]=r*Math.cos(ph);
    const w=Math.random(), type=Math.random();
    if(type<0.1){ col[i*3]=.9;col[i*3+1]=.92;col[i*3+2]=1.; } // blue-white
    else if(type<0.25){ col[i*3]=1.;col[i*3+1]=.7;col[i*3+2]=.3; } // orange
    else if(type<0.35){ col[i*3]=1.;col[i*3+1]=.35;col[i*3+2]=.25; } // red
    else { col[i*3]=.68+w*.32; col[i*3+1]=.72+w*.12; col[i*3+2]=.85+w*.15; } // white-yellow
    sz[i]=0.3+Math.random()*0.5;
  }
  geo.setAttribute('position',new THREE.BufferAttribute(pos,3));
  geo.setAttribute('color',new THREE.BufferAttribute(col,3));
  return new THREE.Points(geo, new THREE.PointsMaterial({size:.45,vertexColors:true,sizeAttenuation:true}));
}
const bgStars = makeStarField();
scene.add(bgStars);

// ── SHADER REGISTRY ──────────────────────────────────────
const uniList = [];

function sunMat(){
  const m=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;varying vec2 vU;void main(){vN=normalize(normalMatrix*normal);vU=uv;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec2 vU;
      float h(vec2 p){return fract(sin(dot(p,vec2(127.1,311.7)))*43758.5);}
      float sn(vec2 p){vec2 i=floor(p);vec2 f=fract(p);vec2 u=f*f*(3.-2.*f);
        return mix(mix(h(i),h(i+vec2(1,0)),u.x),mix(h(i+vec2(0,1)),h(i+vec2(1,1)),u.x),u.y);}
      float fbm(vec2 p){float v=0.;float a=.5;for(int i=0;i<5;i++){v+=a*sn(p);p*=2.1;a*=.5;}return v;}
      void main(){
        vec2 uv=vU*6.+u_t*.007; float n=fbm(uv);
        float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),2.2);
        vec3 col=mix(vec3(1.,.96,.7),vec3(1.,.5,.08),n*n);
        col=mix(col,vec3(.95,.18,.02),rim*.75);
        float spot=sn(vU*14.+u_t*.004);
        col=mix(col,col*.3,step(.75,spot)*.5);
        gl_FragColor=vec4(col*2.2,1.);}`,
  });
  uniList.push(m.uniforms); return m;
}

function planetMat(type){
  const T={
    earth:{c1:'vec3(.06,.25,.65)',c2:'vec3(.18,.5,.18)',c3:'vec3(.88,.92,.96)',s:'.007'},
    gas:  {c1:'vec3(.78,.58,.2)', c2:'vec3(.55,.3,.08)',c3:'vec3(.92,.82,.52)',s:'.004'},
    lava: {c1:'vec3(.88,.18,.04)',c2:'vec3(.45,.04,.0)',c3:'vec3(1.,.65,.1)', s:'.011'},
    ice:  {c1:'vec3(.45,.65,.88)',c2:'vec3(.75,.88,1.)',c3:'vec3(1.,1.,1.)', s:'.003'},
    moon: {c1:'vec3(.19,.19,.19)',c2:'vec3(.38,.38,.38)',c3:'vec3(.48,.48,.45)',s:'.002'},
    rock: {c1:'vec3(.28,.22,.16)',c2:'vec3(.48,.38,.25)',c3:'vec3(.62,.52,.38)',s:'.006'},
  };
  const t=T[type]||T.earth;
  const m=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:Math.random()*100}},
    vertexShader:`varying vec3 vN;varying vec2 vU;void main(){vN=normalize(normalMatrix*normal);vU=uv;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec2 vU;
      float h(vec2 p){return fract(sin(dot(p,vec2(127.1,311.7)))*43758.5);}
      float sn(vec2 p){vec2 i=floor(p);vec2 f=fract(p);vec2 u=f*f*(3.-2.*f);
        return mix(mix(h(i),h(i+vec2(1,0)),u.x),mix(h(i+vec2(0,1)),h(i+vec2(1,1)),u.x),u.y);}
      float fbm(vec2 p){float v=0.;float a=.5;for(int i=0;i<6;i++){v+=a*sn(p);p*=2.1;a*=.5;}return v;}
      void main(){
        vec2 uv=vU*4.+u_t*${t.s}; float n=fbm(uv),n2=fbm(uv*2.3+1.7);
        vec3 c1=${t.c1},c2=${t.c2},c3=${t.c3};
        vec3 col=mix(c1,c2,n); col=mix(col,c3,n2*n2*.5);
        float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),3.2);
        col=mix(col,vec3(.3,.6,1.)*1.1,rim*.45);
        vec3 L=normalize(vec3(-1.,.5,1.));
        col*=(.1+max(dot(vN,L),0.)*.9);
        gl_FragColor=vec4(col,1.);}`,
  });
  uniList.push(m.uniforms); return m;
}

function earthMat(){
  const m=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;varying vec3 vP;varying vec2 vU;varying vec3 vViewDir;
      void main(){
        vN=normalize(normalMatrix*normal);
        vP=normalize(position); // unit sphere position — seamless 3D coords
        vU=uv;
        vec4 mvPos=modelViewMatrix*vec4(position,1.);
        vViewDir=normalize(-mvPos.xyz);
        gl_Position=projectionMatrix*mvPos;}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec3 vP;varying vec2 vU;varying vec3 vViewDir;
      // ── 3D noise — no UV seam ──
      float h3(vec3 p){return fract(sin(dot(p,vec3(127.1,311.7,74.7)))*43758.5);}
      float sn3(vec3 p){
        vec3 i=floor(p);vec3 f=fract(p);vec3 u=f*f*(3.-2.*f);
        return mix(mix(mix(h3(i),h3(i+vec3(1,0,0)),u.x),mix(h3(i+vec3(0,1,0)),h3(i+vec3(1,1,0)),u.x),u.y),
                   mix(mix(h3(i+vec3(0,0,1)),h3(i+vec3(1,0,1)),u.x),mix(h3(i+vec3(0,1,1)),h3(i+vec3(1,1,1)),u.x),u.y),u.z);}
      float fbm3(vec3 p){float v=0.;float a=.5;for(int i=0;i<4;i++){v+=a*sn3(p);p*=2.03;a*=.48;}return v;}

      void main(){
        vec3 L=normalize(vec3(-1.2,.5,1.4));

        // ── All sampling in 3D — zero seam ──
        // Domain warp in 3D for organic continent shapes
        vec3 warp1=vec3(fbm3(vP*2.8+vec3(.3,.7,1.1)), fbm3(vP*2.8+vec3(1.4,2.1,.5)), fbm3(vP*2.8+vec3(2.2,.4,1.8)));
        vec3 warp2=vec3(fbm3(vP*1.6+warp1*.4+vec3(3.2,1.8,.9)), fbm3(vP*1.6+warp1*.4+vec3(5.1,2.9,1.3)), fbm3(vP*1.6+warp1*.4+vec3(.8,4.1,2.4)));
        vec3 warpedP=vP+warp1*.14+warp2*.07;

        float elev=fbm3(warpedP*3.5);
        float elevDetail=fbm3(warpedP*9.+vec3(1.1,2.3,.7))*.18;
        float landMask=smoothstep(.49,.57, elev+elevDetail*.35);

        // Latitude from 3D position — seamless
        float lat=abs(vP.y); // 0=equator, 1=pole

        // ── Ocean ──
        float oDep=fbm3(vP*5.+vec3(u_t*.001,0.,0.));
        vec3 deepSea  =vec3(.015,.06,.22);
        vec3 midSea   =vec3(.03,.14,.42);
        vec3 coastSea =vec3(.05,.26,.50);
        vec3 ocean=mix(deepSea,midSea,oDep);
        float coastal=smoothstep(.43,.54,elev)*smoothstep(.57,.46,elev);
        ocean=mix(ocean,coastSea,coastal*.55);
        float shimmer=fbm3(vP*18.+vec3(u_t*.005,0.,0.))*.5+.5;
        ocean+=vec3(.01,.03,.06)*shimmer*(1.-lat*.4);

        // ── Land — greens + white poles, no brown ──
        float lD=fbm3(warpedP*7.+vec3(2.1,.8,1.4));
        float lM=fbm3(warpedP*18.+vec3(.4,3.1,.9))*.5+.5;

        vec3 rainforest=mix(vec3(.04,.20,.04), vec3(.06,.28,.05), lD);
        vec3 temperate =mix(vec3(.08,.24,.06), vec3(.13,.32,.09), lD);
        vec3 grassland  =mix(vec3(.14,.30,.08), vec3(.20,.38,.11), lM);
        vec3 tundra    =mix(vec3(.16,.24,.09), vec3(.22,.30,.12), lM);
        vec3 polarIce  =mix(vec3(.86,.91,.95), vec3(.94,.96,1.0), lD);
        vec3 snowcap   =vec3(.92,.95,1.0);

        vec3 land=rainforest;
        land=mix(land, temperate, smoothstep(.08,.30,lat));
        land=mix(land, grassland, smoothstep(.25,.46,lat));
        land=mix(land, tundra,    smoothstep(.40,.62,lat));
        land=mix(land, polarIce,  smoothstep(.58,.78,lat));
        land=mix(land, polarIce,  smoothstep(.76,1.0,lat));

        float mtnSnow=smoothstep(.70,.85,elev)*smoothstep(.0,.5,1.-lat*.5);
        land=mix(land,snowcap,mtnSnow*.45);
        land*=(.83+lM*.17);

        // ── Combine ──
        vec3 surf=mix(ocean,land,landMask);

        // ── Clouds in 3D too ──
        float c1=fbm3(vP*3.4+vec3(u_t*.0008,.2,.5));
        float c2=fbm3(vP*5.8+vec3(-.3,u_t*.0006,.8));
        float clouds=smoothstep(.54,.70,c1)*smoothstep(.50,.66,c2*.5+c1*.5);
        vec3 cloudCol=vec3(.90,.93,.97);
        float cDiff=max(dot(vN,L),0.);
        surf*=(1.-clouds*.18);
        surf=mix(surf,cloudCol*(0.65+cDiff*.35),clouds*.82);

        // ── Lighting ──
        float diff=max(dot(vN,L),0.);
        float scatter=smoothstep(-.2,.2,dot(vN,L))*.10;
        surf*=(.06+diff*.88+scatter);

        // Water specular
        vec3 H=normalize(L+vViewDir);
        float spec=pow(max(dot(vN,H),0.),70.)*1.4*(1.-landMask)*(1.-clouds);
        surf+=vec3(.85,.93,1.)*spec*diff;

        // ── Atmosphere rim ──
        float rim=pow(max(1.-dot(vN,vViewDir),0.),2.6);
        float dayRim=clamp(dot(vN,L)+.3,0.,1.);
        vec3 atm=mix(vec3(.3,.55,.95),vec3(.55,.3,.1),smoothstep(.0,.4,dot(vN,L))*smoothstep(.5,.1,dot(vN,L)));
        surf=mix(surf,atm*1.2,rim*.42*dayRim);
        surf+=vec3(.04,.07,.18)*rim*(1.-clamp(dot(vN,L)*4.,0.,1.))*.25;

        gl_FragColor=vec4(surf,1.);}`,
  });
  uniList.push(m.uniforms); return m;
}

// ── SCENE GROUP ──────────────────────────────────────────
let sg=new THREE.Group(); scene.add(sg);

function clearSG(){
  sg.traverse(o=>{
    if(o.geometry)o.geometry.dispose();
    if(o.material){
      if(Array.isArray(o.material))o.material.forEach(m=>m.dispose());
      else o.material.dispose();
    }
  });
  scene.remove(sg); sg=new THREE.Group(); scene.add(sg);
  uniList.length=0; clickTargets=[];
  scene.children.filter(c=>c.isLight).forEach(l=>scene.remove(l));
}

// ── CHAPTER BUILDERS ─────────────────────────────────────

/* ── CH 0: BIG BANG ── */
function ch0(){
  const fm=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;varying vec2 vU;void main(){vN=normalize(normalMatrix*normal);vU=uv;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec2 vU;
      float h(vec2 p){return fract(sin(dot(p,vec2(127.1,311.7)))*43758.5);}
      float sn(vec2 p){vec2 i=floor(p);vec2 f=fract(p);vec2 u=f*f*(3.-2.*f);
        return mix(mix(h(i),h(i+vec2(1,0)),u.x),mix(h(i+vec2(0,1)),h(i+vec2(1,1)),u.x),u.y);}
      float fbm(vec2 p){float v=0.;float a=.5;for(int i=0;i<5;i++){v+=a*sn(p);p*=2.1;a*=.5;}return v;}
      void main(){
        float n=fbm(vU*5.+u_t*.022);
        float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),1.8);
        vec3 col=mix(vec3(1.,1.,.9),vec3(1.,.45,.05),n*.9);
        col=mix(col,vec3(1.,.85,.3),rim*.6);
        gl_FragColor=vec4(col*2.4,1.);}`,
  });
  uniList.push(fm.uniforms);
  const fb=new THREE.Mesh(new THREE.SphereGeometry(5,128,128),fm);
  sg.add(fb);
  clickTargets.push({mesh:fb,detail:{title:'Primordial Fireball',sub:'T = 10⁻⁴³ Seconds',body:'At the Planck epoch, temperatures exceeded 10³² Kelvin and all four fundamental forces — gravity, electromagnetism, and the strong and weak nuclear forces — were unified into a single superforce. Space itself was a quantum foam of infinite curvature. Within the first three minutes, the universe had cooled enough for protons and neutrons to fuse into the first atomic nuclei. The ratio of hydrogen to helium we observe today — roughly 3:1 by mass — was locked in during these first few minutes and is one of the strongest confirmations of the Big Bang model.',facts:[{k:'Temperature',v:'10³² Kelvin'},{k:'Density',v:'10⁹⁶ kg/m³'},{k:'Size at Planck',v:'10⁻³⁵ m'},{k:'First Atoms',v:'380,000 years later'}]}});

  const ringTilts=[[0,0],[Math.PI/3,0],[Math.PI/5,Math.PI/4],[Math.PI*0.6,Math.PI/3],[Math.PI/2,Math.PI/2],[Math.PI/4,Math.PI],[Math.PI*0.8,Math.PI*1.5]];
  for(let i=0;i<7;i++){
    const r=new THREE.Mesh(
      new THREE.RingGeometry(14+i*9,14.4+i*9,128),
      new THREE.MeshBasicMaterial({color:new THREE.Color().setHSL(.07+i*.015,.9,.65),side:THREE.DoubleSide,transparent:true,opacity:.12})
    );
    r.rotation.x=ringTilts[i][0]; r.rotation.z=ringTilts[i][1];
    r.userData.ri=i; sg.add(r);
  }

  const N=8000,pp=new Float32Array(N*3),pc=new Float32Array(N*3),pv=new Float32Array(N*3);
  for(let i=0;i<N;i++){
    const th=Math.random()*Math.PI*2,ph=Math.acos(2*Math.random()-1),r=1+Math.random()*5;
    pp[i*3]=r*Math.sin(ph)*Math.cos(th); pp[i*3+1]=r*Math.sin(ph)*Math.sin(th); pp[i*3+2]=r*Math.cos(ph);
    const spd=.025+Math.random()*.08;
    pv[i*3]=Math.sin(ph)*Math.cos(th)*spd; pv[i*3+1]=Math.sin(ph)*Math.sin(th)*spd; pv[i*3+2]=Math.cos(ph)*spd;
    const t=Math.random();
    pc[i*3]=1; pc[i*3+1]=t>.5?.9:.5+Math.random()*.25; pc[i*3+2]=t>.8?.8:.1;
  }
  const pgeo=new THREE.BufferGeometry();
  pgeo.setAttribute('position',new THREE.BufferAttribute(pp,3));
  pgeo.setAttribute('color',new THREE.BufferAttribute(pc,3));
  const pts=new THREE.Points(pgeo,new THREE.PointsMaterial({size:.3,vertexColors:true,transparent:true,opacity:.9}));
  pts.userData.vel=pv; pts.userData.isBBP=true; sg.add(pts);

  sg.add(new THREE.Mesh(new THREE.SphereGeometry(24,32,32),new THREE.MeshBasicMaterial({color:0xff4400,transparent:true,opacity:.04,side:THREE.BackSide})));
}

/* ── CH 1: GALAXY ── */
function ch1(){
  const cm=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;void main(){vN=normalize(normalMatrix*normal);gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`varying vec3 vN;void main(){float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),1.5);gl_FragColor=vec4(mix(vec3(1.,.97,.8),vec3(1.,.65,.15),rim)*2.,1.);}`,
  });
  uniList.push(cm.uniforms);
  const core=new THREE.Mesh(new THREE.SphereGeometry(3,64,64),cm); sg.add(core);
  clickTargets.push({mesh:core,detail:{title:'Galactic Core',sub:'Supermassive Black Hole Region',body:'At the heart of the Milky Way lies Sagittarius A*, a supermassive black hole of 4 million solar masses. Stars orbit it at thousands of km/s — the star S2 completes a full orbit in just 16 years, reaching 3% the speed of light at closest approach. In 2022, the Event Horizon Telescope captured the first direct image of Sgr A* as a glowing orange ring of superheated gas. The region within a few light-years of the core is one of the most violent environments in the galaxy, filled with magnetar flares, supernovae remnants, and X-ray bursts.',facts:[{k:'Mass',v:'4 Million M☉'},{k:'Diameter',v:'~44 Million km'},{k:'Distance',v:'26,000 light-years'},{k:'First Image',v:'EHT, May 2022'}]}});

  const arms=4,spa=4000,tot=arms*spa;
  const gpos=new Float32Array(tot*3),gcol=new Float32Array(tot*3);
  for(let a=0;a<arms;a++){
    for(let i=0;i<spa;i++){
      const idx=(a*spa+i)*3,f=i/spa,r=f*65+.5,ang=(a/arms)*Math.PI*2+f*Math.PI*3.6,sp=f*7+.5;
      gpos[idx]  =Math.cos(ang)*r+(Math.random()-.5)*sp;
      gpos[idx+1]=(Math.random()-.5)*sp*.35*f;
      gpos[idx+2]=Math.sin(ang)*r+(Math.random()-.5)*sp;
      const w=1-f*.65;
      const col=Math.random();
      gcol[idx]=col>.8?.9:.65+w*.35; gcol[idx+1]=col>.8?.7:.7+w*.08; gcol[idx+2]=col>.8?1.:.9;
    }
  }
  const ggeo=new THREE.BufferGeometry();
  ggeo.setAttribute('position',new THREE.BufferAttribute(gpos,3));
  ggeo.setAttribute('color',new THREE.BufferAttribute(gcol,3));
  const spiral=new THREE.Points(ggeo,new THREE.PointsMaterial({size:.28,vertexColors:true}));
  spiral.userData.isGalaxy=true; sg.add(spiral);
  spiral.rotation.x = 0.4;
  core.rotation.x = 0.4;
  clickTargets.push({mesh:spiral,detail:{title:'Spiral Arms',sub:'Density Wave Structure',body:'Spiral arms are not fixed structures but density waves — like a slow traffic jam — sweeping through the galactic disk every few hundred million years. Stars enter the compressed region, slow, trigger bursts of new star formation, then exit. The brilliant blue-white giant stars born in the arms illuminate them brilliantly but live only a few million years before exploding as supernovae — seeding the galaxy with heavy elements forged in their cores. Our Sun sits in a minor arm called the Orion Spur, between the Perseus and Sagittarius arms.',facts:[{k:'Diameter',v:'100,000 ly'},{k:'Stars',v:'200–400 Billion'},{k:'Type',v:'Barred Spiral (SBbc)'},{k:'Our Location',v:'Orion Spur'}]}});

  sg.add(new THREE.Mesh(new THREE.SphereGeometry(78,32,32),new THREE.MeshBasicMaterial({color:0x180830,transparent:true,opacity:.08,side:THREE.BackSide})));

  const ddisk=new THREE.Mesh(new THREE.CylinderGeometry(68,68,.4,128,1,true),new THREE.MeshBasicMaterial({color:0x140818,side:THREE.DoubleSide,transparent:true,opacity:.28}));
  ddisk.userData.isGalaxy=true; sg.add(ddisk);

  // Dust — tiny scattered particles spread across the whole galaxy disk
  const dustN=3500, dustP=new Float32Array(dustN*3);
  for(let i=0;i<dustN;i++){
    const ang=Math.random()*Math.PI*2;
    const rad=3+Math.random()*70;
    dustP[i*3]  = Math.cos(ang)*rad*(1+(Math.random()-.5)*.15);
    dustP[i*3+1]= (Math.random()-.5)*4*(1-rad/80);
    dustP[i*3+2]= Math.sin(ang)*rad*(1+(Math.random()-.5)*.15);
  }
  const dustGeo=new THREE.BufferGeometry();
  dustGeo.setAttribute('position',new THREE.BufferAttribute(dustP,3));
  const dustPts=new THREE.Points(dustGeo,new THREE.PointsMaterial({size:.08,color:0x888899,transparent:true,opacity:.18}));
  dustPts.userData.isGalaxy=true;
  sg.add(dustPts);

  // Faint ambient particle dots — distant stars/objects, barely visible
  const dotN=1800, dotP=new Float32Array(dotN*3), dotC=new Float32Array(dotN*3);
  for(let i=0;i<dotN;i++){
    const ang=Math.random()*Math.PI*2;
    const rad=8+Math.random()*72;
    const ySpread=(Math.random()-.5)*18*(rad/72); // thinner at edges
    dotP[i*3]=Math.cos(ang)*rad;
    dotP[i*3+1]=ySpread;
    dotP[i*3+2]=Math.sin(ang)*rad;
    // Mostly white, occasional very faint warm or cool tint
    const tint=Math.random();
    dotC[i*3]  = tint>.9 ? .85 : .92;
    dotC[i*3+1]= tint>.9 ? .88 : .93;
    dotC[i*3+2]= tint>.9 ? 1.  : .96;
  }
  const dotGeo=new THREE.BufferGeometry();
  dotGeo.setAttribute('position',new THREE.BufferAttribute(dotP,3));
  dotGeo.setAttribute('color',new THREE.BufferAttribute(dotC,3));
  const dotPts=new THREE.Points(dotGeo,new THREE.PointsMaterial({size:.18,vertexColors:true,transparent:true,opacity:.28}));
  dotPts.userData.isGalaxy=true;
  sg.add(dotPts);
}

/* ── CH 2: THE SUN ── */
function ch2(){
  scene.add(new THREE.AmbientLight(0x111122,.5));

  const sun=new THREE.Mesh(new THREE.SphereGeometry(16,128,128),sunMat());
  sun.userData.spinY=0.008; sg.add(sun);
  clickTargets.push({mesh:sun,detail:{title:'The Sun',sub:'G2V Main Sequence Star',body:'Our Sun fuses 600 million tons of hydrogen into helium every second in its core at 15 million degrees. A photon generated in the core takes up to 100,000 years to random-walk to the surface — but only 8 minutes to reach Earth after that. The surface is covered in convection cells called granules, each the size of Texas, constantly churning hot plasma upward. Solar flares can release energy equivalent to billions of nuclear bombs in minutes. The Sun will spend another 5 billion years on the main sequence before swelling into a red giant large enough to engulf the orbit of Venus.',facts:[{k:'Core Temp',v:'15,700,000 K'},{k:'Surface Temp',v:'5,778 K'},{k:'Photon Travel',v:'~100,000 years to surface'},{k:'Remaining Life',v:'~5 Billion years'}]}});

  const coMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;void main(){vN=normalize(normalMatrix*normal);gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;void main(){
      float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),2.);
      float fl=.75+.25*sin(u_t*.22+vN.x*4.+vN.y*3.);
      vec3 c=mix(vec3(1.,.78,.18),vec3(.95,.3,.02),rim);
      gl_FragColor=vec4(c,rim*.52*fl);}`,
    transparent:true,depthWrite:false,side:THREE.BackSide,
  });
  uniList.push(coMat.uniforms);
  sg.add(new THREE.Mesh(new THREE.SphereGeometry(19.5,64,64),coMat));

  [23,32,44].forEach((r,i)=>{
    sg.add(new THREE.Mesh(new THREE.SphereGeometry(r,32,32),
      new THREE.MeshBasicMaterial({color:new THREE.Color(1,.35+i*.04,.04),transparent:true,opacity:.028-i*.006,side:THREE.BackSide})));
  });

  for(let p=0;p<12;p++){
    const a=(p/12)*Math.PI*2, r=16, ex=r+8+p*1.4;
    const curve=new THREE.QuadraticBezierCurve3(
      new THREE.Vector3(Math.cos(a)*r,Math.sin(a)*r,0),
      new THREE.Vector3(Math.cos(a+.3)*ex,Math.sin(a+.3)*ex,(p%3-1)*6),
      new THREE.Vector3(Math.cos(a+.6)*r,Math.sin(a+.6)*r,0)
    );
    const lg=new THREE.BufferGeometry().setFromPoints(curve.getPoints(50));
    const promMat=new THREE.ShaderMaterial({
      uniforms:{u_t:{value:p}},
      vertexShader:`varying float vP;attribute float lineIndex;void main(){vP=lineIndex;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
      fragmentShader:`uniform float u_t;varying float vP;void main(){float a=sin(vP*3.14159)*(.35+.15*sin(u_t*.3+vP*5.));gl_FragColor=vec4(1.,.55,.1,a);}`,
      transparent:true,depthWrite:false,
    });
    uniList.push(promMat.uniforms);
    sg.add(new THREE.Line(lg,new THREE.LineBasicMaterial({color:0xff8822,transparent:true,opacity:.4})));
  }

  // Solar wind particles
  const swN=2000,swP=new Float32Array(swN*3),swV=new Float32Array(swN*3);
  for(let i=0;i<swN;i++){
    const th=Math.random()*Math.PI*2,ph=Math.acos(2*Math.random()-1),r=18+Math.random()*4;
    swP[i*3]=r*Math.sin(ph)*Math.cos(th); swP[i*3+1]=r*Math.sin(ph)*Math.sin(th); swP[i*3+2]=r*Math.cos(ph);
    const spd=.04+Math.random()*.06;
    swV[i*3]=Math.sin(ph)*Math.cos(th)*spd; swV[i*3+1]=Math.sin(ph)*Math.sin(th)*spd; swV[i*3+2]=Math.cos(ph)*spd;
  }
  const swGeo=new THREE.BufferGeometry(); swGeo.setAttribute('position',new THREE.BufferAttribute(swP,3));
  const swPts=new THREE.Points(swGeo,new THREE.PointsMaterial({size:.22,color:0xffaa44,transparent:true,opacity:.5}));
  swPts.userData.vel=swV; swPts.userData.isSolarWind=true; sg.add(swPts);

  sg.add(new THREE.PointLight(0xfff4e0,3.5,900));
}

/* ── CH 3: SOLAR SYSTEM ── */
function ch3(){
  scene.add(new THREE.AmbientLight(0x060810,.7));
  const sunM=new THREE.Mesh(new THREE.SphereGeometry(5.5,64,64),sunMat()); sg.add(sunM);
  sg.add(new THREE.PointLight(0xfff8e0,3.5,800));
  clickTargets.push({mesh:sunM,detail:{title:'The Solar System',sub:'Our Stellar Neighborhood',body:'Eight planets orbit the Sun across 30 AU of space, all formed from the same spinning disk of gas and dust 4.6 billion years ago. The frost line — where water ice could survive — divided rocky inner worlds from gas and ice giants. Jupiter, the largest planet, contains more mass than all others combined and acted as a gravitational shield, deflecting many comets away from the inner solar system. The Late Heavy Bombardment 4 billion years ago may have delivered the water and organic molecules that seeded life on Earth.',facts:[{k:'Planets',v:'8'},{k:'Age',v:'4.6 Billion yr'},{k:'Largest Planet',v:'Jupiter — 318× Earth'},{k:'Known Moons',v:'290+'}]}});

  const PL=[
    {n:'Mercury',r:1.1,d:12, p:0.24,t:'rock',ti:0.03,col:0x998877},
    {n:'Venus',  r:1.7,d:19, p:0.62,t:'lava',ti:177, col:0xddbb88},
    {n:'Earth',  r:2,  d:27, p:1,   t:'earth',ti:23.5,col:0x4488ff,click:true},
    {n:'Mars',   r:1.3,d:37, p:1.88,t:'lava',ti:25,  col:0xcc5533},
    {n:'Jupiter',r:5.2,d:60, p:11.9,t:'gas', ti:3.1, col:0xddaa77,br:true},
    {n:'Saturn', r:4.2,d:82, p:29.5,t:'gas', ti:26.7,col:0xddcc88,sr:true},
    {n:'Uranus', r:2.8,d:104,p:84,  t:'ice', ti:97.8,col:0x88ccdd},
    {n:'Neptune',r:2.5,d:128,p:165, t:'ice', ti:28.3,col:0x4455ff},
  ];

  for(const pd of PL){
    const or2=new THREE.Mesh(
      new THREE.RingGeometry(pd.d-.12,pd.d+.12,256),
      new THREE.MeshBasicMaterial({color:0x334455,side:THREE.DoubleSide,transparent:true,opacity:.2})
    );
    or2.rotation.x=Math.PI/2; sg.add(or2);

    const piv=new THREE.Group();
    piv.userData.period=pd.p;
    piv.userData.isPiv=true;
    piv.userData.initAngle=Math.random()*Math.PI*2;
    sg.add(piv);

    const planet=new THREE.Mesh(new THREE.SphereGeometry(pd.r,64,64),planetMat(pd.t));
    planet.position.x=pd.d;
    planet.rotation.z=pd.ti*(Math.PI/180);
    planet.userData.spinY=0.015/(pd.p+.1);
    piv.add(planet);

    if(pd.sr){
      for(let ri=0;ri<4;ri++){
        const ir=pd.r*1.45+ri*2.6, or3=ir+2;
        const rr=new THREE.Mesh(
          new THREE.RingGeometry(ir,or3,128),
          new THREE.MeshBasicMaterial({color:new THREE.Color(.85,.75,.45),side:THREE.DoubleSide,transparent:true,opacity:.4-ri*.08})
        );
        rr.rotation.x=Math.PI/2.3; planet.add(rr);
      }
    } else if(pd.br){
      const rr=new THREE.Mesh(
        new THREE.RingGeometry(pd.r*1.4,pd.r*2.1,64),
        new THREE.MeshBasicMaterial({color:0xaa8833,side:THREE.DoubleSide,transparent:true,opacity:.25})
      );
      rr.rotation.x=Math.PI/2; planet.add(rr);
    }

    if(pd.click){
      clickTargets.push({mesh:planet,detail:{title:'Earth',sub:'Class M Terrestrial World',body:'A water world with a nitrogen-oxygen atmosphere, active plate tectonics, and the only confirmed harbor of life in the universe. The Moon stabilizes Earth\'s axial tilt, moderating climate over geological timescales and making complex life possible.',facts:[{k:'Radius',v:'6,371 km'},{k:'Distance',v:'1 AU = 150M km'},{k:'Moons',v:'1 (The Moon)'},{k:'Age',v:'4.54 Billion yr'}]}});
    }
  }

  const N=1200,ap=new Float32Array(N*3);
  for(let i=0;i<N;i++){
    const a=Math.random()*Math.PI*2,r=47+Math.random()*9,y=(Math.random()-.5)*2.5;
    ap[i*3]=Math.cos(a)*r; ap[i*3+1]=y; ap[i*3+2]=Math.sin(a)*r;
  }
  const ag=new THREE.BufferGeometry(); ag.setAttribute('position',new THREE.BufferAttribute(ap,3));
  sg.add(new THREE.Points(ag,new THREE.PointsMaterial({size:.38,color:0xaa9966,transparent:true,opacity:.45})));
}

/* ── CH 4: EARTH / BIOSPHERE ── */
function ch4(){
  const dLight=new THREE.DirectionalLight(0xfff6e8,2.2);
  dLight.position.set(-120,40,90); scene.add(dLight);
  const fillLight=new THREE.DirectionalLight(0x1a2a5a,.18);
  fillLight.position.set(80,-20,-60); scene.add(fillLight);
  scene.add(new THREE.AmbientLight(0x080d1a,.3));

  const earth=new THREE.Mesh(new THREE.SphereGeometry(18,64,64),earthMat());
  earth.rotation.z=23.5*(Math.PI/180);
  earth.userData.spinY=0.04;
  sg.add(earth);
  clickTargets.push({mesh:earth,detail:{title:'Earth — The Biosphere',sub:'The Living World',body:'Earth\'s biosphere is a razor-thin living film — extending roughly 10 km below the ocean floor to 10 km above sea level — on the surface of a rocky ball 12,700 km across. Photosynthetic cyanobacteria transformed the atmosphere 2.4 billion years ago in the Great Oxidation Event, filling it with oxygen and triggering the first mass extinction. That same oxygen layer, converted to ozone in the upper atmosphere, now shields all surface life from lethal ultraviolet radiation. The water cycle, plate tectonics, and the carbon cycle work together as a planetary-scale life support system.',facts:[{k:'Biosphere Depth',v:'~20 km total'},{k:'Ocean Cover',v:'71%'},{k:'Atmosphere',v:'78% N₂, 21% O₂'},{k:'Oldest Life',v:'~3.8 Billion yr ago'}]}});

  // City lights shader on the dark side
  const citylightMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;varying vec2 vU;void main(){vN=normalize(normalMatrix*normal);vU=uv;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec2 vU;
      float h(vec2 p){return fract(sin(dot(p,vec2(127.1,311.7)))*43758.5);}
      float sn(vec2 p){vec2 i=floor(p);vec2 f=fract(p);vec2 u=f*f*(3.-2.*f);
        return mix(mix(h(i),h(i+vec2(1,0)),u.x),mix(h(i+vec2(0,1)),h(i+vec2(1,1)),u.x),u.y);}
      void main(){
        vec3 L=normalize(vec3(-1.,.4,1.));
        float diff=dot(vN,L);
        // Only show on dark side
        float dark=smoothstep(0.,.15,-diff);
        float city=sn(vU*38.)*sn(vU*22.+.3)*sn(vU*14.+.7);
        float cl=step(.82,city)*dark;
        // Poles have no cities
        float pole=abs(vU.y-.5)*2.;
        cl*=smoothstep(.85,.7,pole);
        gl_FragColor=vec4(vec3(1.,.9,.6),cl*.9);}`,
    transparent:true,depthWrite:false,
  });
  uniList.push(citylightMat.uniforms);
  const cityLights=new THREE.Mesh(new THREE.SphereGeometry(18.02,64,64),citylightMat);
  cityLights.rotation.z=23.5*(Math.PI/180);
  sg.add(cityLights);

  // Clouds layer
  const cloudMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;varying vec2 vU;void main(){vN=normalize(normalMatrix*normal);vU=uv;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec2 vU;
      float h(vec2 p){return fract(sin(dot(p,vec2(127.1,311.7)))*43758.5);}
      float sn(vec2 p){vec2 i=floor(p);vec2 f=fract(p);vec2 u=f*f*(3.-2.*f);
        return mix(mix(h(i),h(i+vec2(1,0)),u.x),mix(h(i+vec2(0,1)),h(i+vec2(1,1)),u.x),u.y);}
      float fbm(vec2 p){float v=0.;float a=.5;for(int i=0;i<3;i++){v+=a*sn(p);p*=2.1;a*=.5;}return v;}
      void main(){
        vec2 uv=vU+vec2(u_t*.0003,0.);
        float cl=fbm(uv*4.8+1.7);
        float coverage=smoothstep(.48,.68,cl)*.75;
        vec3 L=normalize(vec3(-1.,.4,1.));
        float diff=.2+max(dot(vN,L),0.)*.8;
        gl_FragColor=vec4(vec3(.92,.94,.98)*diff,coverage);}`,
    transparent:true,depthWrite:false,
  });
  uniList.push(cloudMat.uniforms);
  const clouds=new THREE.Mesh(new THREE.SphereGeometry(18.4,64,64),cloudMat);
  clouds.rotation.z=23.5*(Math.PI/180);
  clouds.userData.spinY=0.038; // slightly different from earth
  sg.add(clouds);

  // Atmosphere glow
  const atMat=new THREE.ShaderMaterial({
    uniforms:{},
    vertexShader:`varying vec3 vN;void main(){vN=normalize(normalMatrix*normal);gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`varying vec3 vN;void main(){
      float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),2.2);
      vec3 c=mix(vec3(.18,.52,1.),vec3(.45,.75,1.),rim);
      gl_FragColor=vec4(c,rim*.65);}`,
    transparent:true,depthWrite:false,side:THREE.BackSide,
  });
  sg.add(new THREE.Mesh(new THREE.SphereGeometry(20.5,64,64),atMat));

  // Aurora particles at poles
  for(let pole=0;pole<2;pole++){
    const aN=800, aP=new Float32Array(aN*3), aC=new Float32Array(aN*3);
    const ySign=pole===0?1:-1;
    for(let i=0;i<aN;i++){
      const ang=Math.random()*Math.PI*2;
      const lat=(0.85+Math.random()*.12)*ySign;
      const cosLat=Math.sqrt(1-lat*lat);
      const r=19.2+Math.random()*.8;
      aP[i*3]=r*cosLat*Math.cos(ang); aP[i*3+1]=r*lat; aP[i*3+2]=r*cosLat*Math.sin(ang);
      const g=Math.random();
      aC[i*3]=0.; aC[i*3+1]=.5+g*.5; aC[i*3+2]=g>.5?.2:.8;
    }
    const aGeo=new THREE.BufferGeometry();
    aGeo.setAttribute('position',new THREE.BufferAttribute(aP,3));
    aGeo.setAttribute('color',new THREE.BufferAttribute(aC,3));
    sg.add(new THREE.Points(aGeo,new THREE.PointsMaterial({size:.35,vertexColors:true,transparent:true,opacity:.6})));
  }

  // Moon pivot
  const moonPiv=new THREE.Group(); moonPiv.userData.isMoonPiv=true; sg.add(moonPiv);
  const mmMat=planetMat('moon');
  const moon=new THREE.Mesh(new THREE.SphereGeometry(5,64,64),mmMat);
  moon.position.set(36,4,0);
  moon.userData.spinY=0.012;
  moonPiv.add(moon);
  clickTargets.push({mesh:moon,detail:{title:'The Moon',sub:'Earth\'s Stabilizing Companion',body:'The Moon formed when a Mars-sized body called Theia struck the proto-Earth 4.5 billion years ago, ejecting a disk of molten rock that coalesced into our satellite. It is the only other world humans have walked on. The Moon stabilizes Earth\'s 23.5° axial tilt, preventing the wild swings in obliquity that Mars experiences — swings that would cause catastrophic climate chaos on Earth. Tidal friction from the Moon is gradually slowing Earth\'s rotation; a day was only 6 hours long when Earth was young. The Moon is slowly receding — moving 3.8 cm farther away every year.',facts:[{k:'Distance',v:'384,400 km'},{k:'Radius',v:'1,737 km'},{k:'Recession Rate',v:'3.8 cm/year'},{k:'Only Visits',v:'Apollo 11–17, 1969–72'}]}});
}

/* ── CH 5: NEBULA ── */
function ch5(){
  scene.add(new THREE.AmbientLight(0x04021a,.6));
  // Central star light — warm blue-white, illuminates everything
  const starLight=new THREE.PointLight(0x99bbff,4.5,280); scene.add(starLight);
  // Soft fill from opposite side — very faint warm
  const fillLight=new THREE.PointLight(0xff8844,.3,200);
  fillLight.position.set(-40,-20,30); scene.add(fillLight);

  // ── 3D noise helpers (inline GLSL — shared across all shaders below) ──
  const noise3D=`
    float h3(vec3 p){return fract(sin(dot(p,vec3(127.1,311.7,74.7)))*43758.5);}
    float sn3(vec3 p){
      vec3 i=floor(p);vec3 f=fract(p);vec3 u=f*f*(3.-2.*f);
      return mix(mix(mix(h3(i),h3(i+vec3(1,0,0)),u.x),mix(h3(i+vec3(0,1,0)),h3(i+vec3(1,1,0)),u.x),u.y),
                 mix(mix(h3(i+vec3(0,0,1)),h3(i+vec3(1,0,1)),u.x),mix(h3(i+vec3(0,1,1)),h3(i+vec3(1,1,1)),u.x),u.y),u.z);}
    float fbm3(vec3 p){float v=0.;float a=.5;for(int i=0;i<3;i++){v+=a*sn3(p);p*=2.03;a*=.48;}return v;}
  `;

  // ── Central hot O-type star ──
  const starMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;varying vec3 vP;void main(){vN=normalize(normalMatrix*normal);vP=normalize(position);gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec3 vP;
      ${noise3D}
      void main(){
        float n=fbm3(vP*4.+u_t*.08)*.5+.5;
        float pulse=.94+.06*sin(u_t*2.1+n*3.);
        float surf=fbm3(vP*8.+u_t*.05)*.5+.5;
        vec3 hot=mix(vec3(.7,.88,1.),vec3(.4,.65,1.),n);
        hot=mix(hot,vec3(1.,1.,1.),surf*.4);
        // Rim is slightly cooler/darker
        float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),2.2);
        hot=mix(hot,vec3(.3,.5,.9),rim*.3);
        gl_FragColor=vec4(hot*2.8*pulse,1.);}`,
  });
  uniList.push(starMat.uniforms);
  const cStar=new THREE.Mesh(new THREE.SphereGeometry(3,32,32),starMat);
  sg.add(cStar);
  clickTargets.push({mesh:cStar,detail:{title:'Protostar',sub:'Newly Born O-Type Star',body:'O-type stars are the universe\'s most extreme furnaces — tens of thousands of times more luminous than the Sun, burning at surface temperatures up to 50,000 Kelvin. They live fast and die violently, exhausting their hydrogen in just a few million years before exploding as Type II supernovae. A single O-star can outshine an entire galaxy of smaller stars. Their intense ultraviolet radiation ionizes the surrounding gas, making it glow — the emission nebula we see here is lit entirely by the energy of these young giants. The heavy elements they forge and scatter in death will become the next generation of planets.',facts:[{k:'Surface Temp',v:'30,000–50,000 K'},{k:'Luminosity',v:'Up to 1,000,000 L☉'},{k:'Lifetime',v:'~3 Million years'},{k:'Death',v:'Type II Supernova'}]}});

  // Star corona glow
  const coronaMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;void main(){vN=normalize(normalMatrix*normal);gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;void main(){
      float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),1.8);
      float fl=.8+.2*sin(u_t*.4);
      gl_FragColor=vec4(vec3(.5,.7,1.),rim*.45*fl);}`,
    transparent:true,depthWrite:false,side:THREE.BackSide,
  });
  uniList.push(coronaMat.uniforms);
  sg.add(new THREE.Mesh(new THREE.SphereGeometry(5.5,32,32),coronaMat));

  // ── Nebula shell layers — all 3D noise, no UV seams ──
  const nebulaLayers=[
    {col:[.85,.12,.28], r:24, seed:0.0,  spd:.00035}, // Hα red inner
    {col:[.12,.48,.92], r:34, seed:1.7,  spd:.00028}, // OIII blue
    {col:[.55,.10,.75], r:44, seed:3.1,  spd:.00022}, // SII purple
    {col:[.08,.62,.50], r:56, seed:5.4,  spd:.00018}, // teal outer
    {col:[.82,.30,.08], r:68, seed:7.2,  spd:.00012}, // warm orange fringe
  ];
  for(const nl of nebulaLayers){
    const nMat=new THREE.ShaderMaterial({
      uniforms:{u_t:{value:Math.random()*80},u_col:{value:new THREE.Vector3(...nl.col)},u_spd:{value:nl.spd},u_seed:{value:nl.seed}},
      vertexShader:`varying vec3 vN;varying vec3 vP;void main(){vN=normalize(normalMatrix*normal);vP=normalize(position);gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
      fragmentShader:`uniform float u_t;uniform vec3 u_col;uniform float u_spd;uniform float u_seed;varying vec3 vN;varying vec3 vP;
        ${noise3D}
        void main(){
          float n=fbm3(vP*2.8+u_seed+u_t*u_spd);
          float n2=fbm3(vP*5.+u_seed+1.4+u_t*u_spd*.5);
          float cloud=smoothstep(.38,.68,n)*smoothstep(.32,.62,n2*.6+n*.4);
          float lit=dot(-vN,normalize(vP))*.3+.7;
          vec3 col=u_col*lit;
          gl_FragColor=vec4(col,cloud*.16);}`,
      transparent:true,depthWrite:false,side:THREE.BackSide,
    });
    uniList.push(nMat.uniforms);
    sg.add(new THREE.Mesh(new THREE.SphereGeometry(nl.r,24,24),nMat));
  }

  // ── Pillars of Creation — 3 pillars, low geo, simple lighting ──
  for(let p=0;p<3;p++){
    const angle=p/3*Math.PI*2+0.5;
    const dist=22+Math.random()*14;
    const height=20+Math.random()*12;
    const topR=2.5+Math.random()*1.5;
    const botR=0.6+Math.random()*.6;

    const pillarMat=new THREE.ShaderMaterial({
      uniforms:{u_t:{value:p*7.3},u_seed:{value:p*2.1}},
      vertexShader:`varying vec3 vN;varying vec3 vW;varying float vH;
        void main(){
          vN=normalize(normalMatrix*normal);
          vW=vec3(modelMatrix*vec4(position,1.));
          vH=(position.y+15.)/(30.);
          gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
      fragmentShader:`uniform float u_t;uniform float u_seed;varying vec3 vN;varying vec3 vW;varying float vH;
        ${noise3D}
        void main(){
          vec3 wp=vW*.1+u_seed;
          float n=fbm3(wp*2.2);
          float nFine=sn3(wp*6.)*.5+.5;
          vec3 col=mix(vec3(.08,.03,.12),vec3(.22,.07,.32),n);
          vec3 starDir=normalize(-vW);
          float edgeLit=max(dot(vN,starDir),0.);
          col=mix(col,mix(vec3(.72,.18,.38),vec3(.90,.35,.15),n),edgeLit*.5);
          float tipGlow=smoothstep(.6,1.,vH)*edgeLit;
          col=mix(col,vec3(.95,.55,.2),tipGlow*.5);
          col*=(.12+edgeLit*.88);
          col*=(.8+nFine*.2);
          float alpha=clamp((.65+n*.35)*(.55+nFine*.45),0.,1.);
          gl_FragColor=vec4(col,alpha*.92);}`,
      transparent:true,side:THREE.DoubleSide,depthWrite:false,
    });
    uniList.push(pillarMat.uniforms);

    const pillar=new THREE.Mesh(new THREE.CylinderGeometry(topR,botR,height,16,4),pillarMat);
    pillar.position.set(
      Math.cos(angle)*dist,
      (Math.random()-.5)*6,
      Math.sin(angle)*dist
    );
    // Natural tilt — leaning slightly, not perfectly vertical
    pillar.rotation.x=(Math.random()-.5)*.3;
    pillar.rotation.z=(Math.random()-.5)*.25;
    sg.add(pillar);
    clickTargets.push({mesh:pillar,detail:{title:'Pillar of Creation',sub:'Stellar Nursery Column',body:'These towering columns of gas and dust are stellar incubators. Dense knots inside them — called EGGs (Evaporating Gaseous Globules) — contain protostars in the process of formation. The intense UV radiation from nearby hot stars slowly erodes the pillar from the outside in.',facts:[{k:'Height',v:'Up to 4 light-years'},{k:'Composition',v:'H₂, dust, He'},{k:'Erosion',v:'Photoevaporation'},{k:'Famous',v:'Eagle Nebula, M16'}]}});
  }

  // ── Young star cluster — hot blue-white points ──
  const sN=300,sP=new Float32Array(sN*3),sC=new Float32Array(sN*3);
  for(let i=0;i<sN;i++){
    const th=Math.random()*Math.PI*2,ph=Math.acos(2*Math.random()-1),r=4+Math.random()*65;
    sP[i*3]=r*Math.sin(ph)*Math.cos(th); sP[i*3+1]=r*Math.sin(ph)*Math.sin(th); sP[i*3+2]=r*Math.cos(ph);
    const type=Math.random();
    if(type<.15){sC[i*3]=1.;sC[i*3+1]=.95;sC[i*3+2]=.7;} // warm yellow older
    else if(type<.35){sC[i*3]=1.;sC[i*3+1]=1.;sC[i*3+2]=1.;} // white
    else{sC[i*3]=.6;sC[i*3+1]=.75;sC[i*3+2]=1.;} // hot blue-white young
  }
  const sGeo=new THREE.BufferGeometry();
  sGeo.setAttribute('position',new THREE.BufferAttribute(sP,3));
  sGeo.setAttribute('color',new THREE.BufferAttribute(sC,3));
  sg.add(new THREE.Points(sGeo,new THREE.PointsMaterial({size:.28,vertexColors:true,transparent:true,opacity:.8})));
}

/* ── CH 6: BLACK HOLE ── */
function ch6(){
  scene.add(new THREE.AmbientLight(0x020408,.9));

  // ── Accretion disk — using RingGeometry so UV coords are proper ──
  const diskMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec2 vU;varying vec3 vPos;void main(){vU=uv;vPos=position;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec2 vU;varying vec3 vPos;
      float h(vec2 p){return fract(sin(dot(p,vec2(127.1,311.7)))*43758.5);}
      float sn(vec2 p){vec2 i=floor(p);vec2 f=fract(p);vec2 u=f*f*(3.-2.*f);
        return mix(mix(h(i),h(i+vec2(1,0)),u.x),mix(h(i+vec2(0,1)),h(i+vec2(1,1)),u.x),u.y);}
      float fbm(vec2 p){float v=0.;float a=.5;for(int i=0;i<5;i++){v+=a*sn(p);p*=2.1;a*=.5;}return v;}
      void main(){
        // vU.x = angle 0-1, vU.y = radial 0(inner) to 1(outer)  [RingGeometry UVs]
        float radial=vU.y; // 0=inner edge, 1=outer edge
        float ang=vU.x*6.28318+u_t*.055;

        // Turbulent flow lines
        float swirl=fbm(vec2(ang*.5-radial*2.+u_t*.04,radial*4.));
        float swirl2=fbm(vec2(ang*.3+radial*3.-u_t*.028,swirl));

        // Doppler: one side blue-shifted, opposite red-shifted
        float doppler=sin(ang);

        // Hot color ramp: white-hot inner, orange-red outer
        vec3 innerHot=vec3(1.,1.,.85);
        vec3 midOrange=vec3(1.,.45,.05);
        vec3 outerDim=vec3(.4,.08,.02);
        vec3 col=mix(innerHot,midOrange,radial*.8);
        col=mix(col,outerDim,radial*radial*.6);

        // Doppler color shift
        col=mix(col,vec3(1.,1.,.4),max(0.,doppler)*.35);
        col=mix(col,vec3(.2,.4,1.),max(0.,-doppler)*.25);

        // Turbulence variation
        col*=(.6+swirl2*.8);
        col+=vec3(1.,.8,.3)*swirl*(1.-radial)*.5;

        // Fade at outer edge, boost at inner
        float fade=smoothstep(1.,.6,radial)*smoothstep(0.,.08,radial);
        float innerGlow=pow(1.-radial,.8)*.4;

        // Photon sphere bright ring at inner edge
        float photonRing=smoothstep(.08,.0,radial)*2.;

        float alpha=fade*(.55+swirl*.25)+photonRing*.7;
        gl_FragColor=vec4(col*(1.+photonRing),clamp(alpha,0.,1.));}`,
    transparent:true,side:THREE.DoubleSide,depthWrite:false,
  });
  uniList.push(diskMat.uniforms);

  // Use proper RingGeometry — inner radius just outside event horizon, tilted for 3D
  const disk=new THREE.Mesh(new THREE.RingGeometry(10.5, 52, 256, 8),diskMat);
  disk.rotation.x = -Math.PI/2.6;
  sg.add(disk);
  clickTargets.push({mesh:disk,detail:{title:'Accretion Disk',sub:'Superheated Infalling Matter',body:'Matter spiraling into a black hole forms a superheated accretion disk rotating at a significant fraction of the speed of light. The Doppler effect makes the side rotating toward us appear dramatically brighter and bluer — a phenomenon clearly visible in the 2019 image of M87*. At the inner edge, the innermost stable circular orbit, matter plunges inward in a final death spiral. The energy released by infalling matter — up to 42% of its rest mass — makes active galactic nuclei the most efficient energy sources in the universe, far outstripping nuclear fusion.',facts:[{k:'Temperature',v:'Up to 10⁷ Kelvin'},{k:'Inner Orbit',v:'3× Schwarzschild radius'},{k:'Energy Efficiency',v:'Up to 42% of mc²'},{k:'First Image',v:'M87*, April 2019'}]}});

  // Event horizon — deep void sphere with subtle lensing rim
  const ehMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec3 vN;varying vec3 vW;void main(){vN=normalize(normalMatrix*normal);vW=vec3(modelMatrix*vec4(position,1.));gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec3 vN;varying vec3 vW;
      void main(){
        // Deep black center — event horizon is absolute darkness
        float rim=pow(max(1.-dot(vN,vec3(0,0,1)),0.),5.);
        // Very faint blue-purple photon capture glow at extreme edge
        vec3 col=vec3(.02,.01,.06)*rim;
        gl_FragColor=vec4(col,1.);}`,
  });
  uniList.push(ehMat.uniforms);
  const eh=new THREE.Mesh(new THREE.SphereGeometry(9.5,256,256),ehMat); sg.add(eh);
  clickTargets.push({mesh:eh,detail:{title:'Event Horizon',sub:'The Point of No Return',body:'The event horizon is not a physical surface — it is a mathematical boundary where escape velocity equals the speed of light. An outside observer watching something fall toward it would see it slow, redshift, and fade, frozen in time at the threshold. The infalling observer feels nothing special crossing it — but their future now points only toward the singularity. Stephen Hawking showed that quantum effects near the horizon cause black holes to slowly radiate energy — Hawking radiation — meaning they will eventually evaporate entirely. For a stellar-mass black hole this will take 10⁶⁷ years — vastly longer than the current age of the universe.',facts:[{k:'Schwarzschild R',v:'~3 km per M☉'},{k:'Escape Velocity',v:'= speed of light'},{k:'Hawking Temp',v:'~60 nanokelvin (10M☉)'},{k:'Evaporation Time',v:'~10⁶⁷ years'}]}});

  // Photon sphere ring (light orbiting at 1.5x Schwarzschild radius)
  const photonMat=new THREE.ShaderMaterial({
    uniforms:{u_t:{value:0}},
    vertexShader:`varying vec2 vU;void main(){vU=uv;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
    fragmentShader:`uniform float u_t;varying vec2 vU;
      void main(){
        float brightness=.5+.5*sin(vU.x*50.+u_t*2.);
        gl_FragColor=vec4(vec3(.6,.75,1.)*brightness,brightness*.7);}`,
    transparent:true,side:THREE.DoubleSide,depthWrite:false,
  });
  uniList.push(photonMat.uniforms);
  // Photon ring slightly tilted same as disk
  const photonRing=new THREE.Mesh(new THREE.RingGeometry(10.4,10.9,512),photonMat);
  photonRing.rotation.x=-Math.PI/2.6;
  sg.add(photonRing);

  // Second photon ring (relativistic counterrotating image)
  sg.add(new THREE.Mesh(new THREE.RingGeometry(10.0,10.35,512),new THREE.MeshBasicMaterial({color:0x8899ff,side:THREE.DoubleSide,transparent:true,opacity:.35})));

  // Relativistic jets (along Y axis, perpendicular to disk)
  for(let j=0;j<2;j++){
    const dj=j===0?1:-1;
    const jMat=new THREE.ShaderMaterial({
      uniforms:{u_t:{value:0}},
      vertexShader:`varying float vY;varying vec2 vU;void main(){vY=position.y;vU=uv;gl_Position=projectionMatrix*modelViewMatrix*vec4(position,1.);}`,
      fragmentShader:`uniform float u_t;varying float vY;varying vec2 vU;
        void main(){
          float f=abs(vY)/80.;
          // Knots of plasma in the jet
          float knot=.5+.5*sin(f*18.-u_t*.8+vU.x*6.28);
          float a=smoothstep(1.,0.,f)*(.35+.3*knot)*.7;
          vec3 inner=vec3(.5,.7,1.);
          vec3 outer=vec3(.15,.25,.8);
          vec3 col=mix(inner,outer,f);
          col+=vec3(.4,.6,1.)*knot*(.8-f);
          gl_FragColor=vec4(col,a);}`,
      transparent:true,depthWrite:false,side:THREE.DoubleSide,
    });
    uniList.push(jMat.uniforms);
    const jet=new THREE.Mesh(new THREE.CylinderGeometry(1.8,.1,80,32,8,true),jMat);
    jet.position.y=dj*40; sg.add(jet);

    // Jet outer glow
    const jGlowMat=new THREE.MeshBasicMaterial({color:0x2244aa,transparent:true,opacity:.12,side:THREE.BackSide});
    const jGlow=new THREE.Mesh(new THREE.CylinderGeometry(4.5,.3,80,16,1,true),jGlowMat);
    jGlow.position.y=dj*40; sg.add(jGlow);
  }

  // Gravitational lensing — warped star ring
  const ln=320,lp=new Float32Array(ln*3),lc=new Float32Array(ln*3);
  for(let i=0;i<ln;i++){
    const a=(i/ln)*Math.PI*2, r=28+Math.sin(a*3)*2.5+Math.random()*2;
    lp[i*3]=Math.cos(a)*r;
    lp[i*3+1]=(Math.random()-.5)*4;
    lp[i*3+2]=Math.sin(a)*r;
    const b=.5+Math.random()*.5;
    lc[i*3]=b*.6; lc[i*3+1]=b*.8; lc[i*3+2]=1.;
  }
  const lg=new THREE.BufferGeometry(); lg.setAttribute('position',new THREE.BufferAttribute(lp,3)); lg.setAttribute('color',new THREE.BufferAttribute(lc,3));
  sg.add(new THREE.Points(lg,new THREE.PointsMaterial({size:.45,vertexColors:true,transparent:true,opacity:.7})));

  // Hawking radiation particles drifting outward from horizon
  const hrN=1500,hrP=new Float32Array(hrN*3),hrV=new Float32Array(hrN*3),hrC=new Float32Array(hrN*3);
  for(let i=0;i<hrN;i++){
    const th=Math.random()*Math.PI*2,ph=Math.acos(2*Math.random()-1),r=9.5+Math.random()*1;
    hrP[i*3]=r*Math.sin(ph)*Math.cos(th); hrP[i*3+1]=r*Math.sin(ph)*Math.sin(th); hrP[i*3+2]=r*Math.cos(ph);
    const spd=.012+Math.random()*.025;
    hrV[i*3]=Math.sin(ph)*Math.cos(th)*spd; hrV[i*3+1]=Math.sin(ph)*Math.sin(th)*spd; hrV[i*3+2]=Math.cos(ph)*spd;
    hrC[i*3]=.4; hrC[i*3+1]=.5; hrC[i*3+2]=1.;
  }
  const hrGeo=new THREE.BufferGeometry();
  hrGeo.setAttribute('position',new THREE.BufferAttribute(hrP,3));
  hrGeo.setAttribute('color',new THREE.BufferAttribute(hrC,3));
  const hrPts=new THREE.Points(hrGeo,new THREE.PointsMaterial({size:.18,vertexColors:true,transparent:true,opacity:.55}));
  hrPts.userData.vel=hrV; hrPts.userData.isHR=true; sg.add(hrPts);

  // Distant background stars pulled into streaks near horizon (visual effect)
  const bsN=400,bsP=new Float32Array(bsN*3),bsC=new Float32Array(bsN*3);
  for(let i=0;i<bsN;i++){
    const th=Math.random()*Math.PI*2,ph=Math.acos(2*Math.random()-1),r=65+Math.random()*40;
    bsP[i*3]=r*Math.sin(ph)*Math.cos(th); bsP[i*3+1]=r*Math.sin(ph)*Math.sin(th); bsP[i*3+2]=r*Math.cos(ph);
    bsC[i*3]=.7; bsC[i*3+1]=.75; bsC[i*3+2]=1.;
  }
  const bsGeo=new THREE.BufferGeometry(); bsGeo.setAttribute('position',new THREE.BufferAttribute(bsP,3)); bsGeo.setAttribute('color',new THREE.BufferAttribute(bsC,3));
  sg.add(new THREE.Points(bsGeo,new THREE.PointsMaterial({size:.4,vertexColors:true,transparent:true,opacity:.55})));
}

// ── CHAPTERS CONFIG ──────────────────────────────────────
const CHAPTERS=[
  {num:'Chapter I',   name:'The Big Bang',       dz:80,  text:'In the beginning, a singularity of infinite density erupted, expanding faster than light. Within the first second, quarks combined into protons and neutrons. Within three minutes, hydrogen and helium nuclei had formed. The universe was opaque, a blazing fog of plasma — it would take 380,000 years for it to cool enough for the first atoms to form and light to travel freely for the first time.',  stat:'13.8 Billion Years Ago · T = 10⁻⁴³ Seconds · All forces unified', build:ch0},
  {num:'Chapter II',  name:'Galaxy Formation',    dz:135, text:'Over hundreds of millions of years, dark matter halos collapsed under gravity, pulling ordinary matter into the first galaxies. These early structures were small and irregular — they merged, collided, and grew over billions of years into the grand spirals and ellipticals we see today. The Milky Way itself is still consuming smaller dwarf galaxies. Dark matter, invisible and undetected directly, makes up 85% of all matter and is the scaffolding on which every galaxy is built.',  stat:'13 Billion Years Ago · First Galaxies · Dark matter scaffolding', build:ch1},
  {num:'Chapter III', name:'A Living Star',        dz:65,  text:'Stars are the universe\'s nuclear furnaces, forging every element heavier than hydrogen in their cores. Our Sun fuses 600 million tons of hydrogen into helium every second at 15 million degrees. It radiates energy that took up to 100,000 years to travel from the core to the surface — but only 8 minutes to reach Earth. Solar flares and coronal mass ejections can release energy equivalent to billions of nuclear bombs, sending charged particles hurtling across the solar system.',  stat:'4.6 Billion Years Old · G2V Main Sequence · 5 Billion Years Remaining', build:ch2},
  {num:'Chapter IV',  name:'The Solar System',    dz:180, text:'From a collapsing cloud of gas and dust, a spinning disk formed around our young Sun 4.6 billion years ago. Rocky worlds built up near the heat through countless collisions. Gas and ice giants grew enormous in the cold outer reaches. Jupiter\'s gravity stirred the asteroid belt into chaos, preventing a ninth planet from ever forming. The Late Heavy Bombardment — 4 billion years ago — pelted the inner planets with debris, possibly delivering water and organic molecules to Earth.',  stat:'4.6 Billion Years Ago · 8 Planets · 900,000+ Known Asteroids', build:ch3},
  {num:'Chapter V',   name:'A World with Life',   dz:72,  text:'On one pale blue dot, 150 million kilometers from an ordinary star, liquid water pooled in ancient oceans. Within a billion years, simple chemistry became biology. Photosynthetic bacteria transformed the atmosphere, flooding it with oxygen — a poison to the life that came before, but a gift to everything that followed. Today 8.7 million species share this thin biosphere. Auroras dance at the poles. City lights trace the coastlines on the night side. In 4 billion years the Sun will swallow this world.',  stat:'3.8 Billion Years of Life · 8.7M Species · 1 Known Biosphere', build:ch4},
  {num:'Chapter VI',  name:'Stellar Nursery',     dz:90,  text:'Inside colossal molecular clouds thousands of light-years across, pockets of gas collapse under their own gravity until nuclear fusion ignites. The newborn stars blaze with intense ultraviolet radiation, slowly carving glowing cavities and sculpting towering pillars of gas and dust — the Pillars of Creation. Inside these columns, dense knots called EGGs hide protostars still gathering mass. Each pillar will be eroded away in a few million years, but the stars born within them will burn for billions.',  stat:'Active Star Formation · Giant Molecular Cloud · T-Tauri Phase', build:ch5},
  {num:'Chapter VII', name:'Black Hole',           dz:75,  text:'When a star more than 20 times the mass of the Sun exhausts its fuel, gravity wins absolutely. The core collapses in milliseconds. The outer layers rebound in a supernova visible across galaxies. What remains is a singularity — infinite density, zero volume — wrapped in an event horizon. Infalling gas forms a superheated accretion disk blazing at millions of degrees. Powerful magnetic fields launch relativistic jets of plasma thousands of light-years into space. Hawking radiation slowly bleeds mass back into the universe, but for stellar-mass black holes this process will take longer than the current age of the universe.',  stat:'Timeless · Schwarzschild Radius ~3km per Solar Mass · Hawking Temp ~60nK', build:ch6},
];

// ── CHAPTER NAVIGATION ───────────────────────────────────
let curCh=0, transitionActive=false;

function buildNav(){
  const nav=document.getElementById('nav');
  CHAPTERS.forEach((ch,i)=>{
    const d=document.createElement('div');
    d.className='nd'+(i===0?' on':''); d.dataset.l=ch.name;
    d.onclick=()=>goTo(i); nav.appendChild(d);
  });
}

function updateUI(){
  const ch=CHAPTERS[curCh];
  document.getElementById('chn').textContent=ch.num;
  document.getElementById('chname').textContent=ch.name;
  document.getElementById('it').textContent=ch.text;
  document.getElementById('istat').textContent=ch.stat;
  document.getElementById('prog').style.width=((curCh+1)/CHAPTERS.length*100)+'%';
  document.querySelectorAll('.nd').forEach((d,i)=>d.classList.toggle('on',i===curCh));
  targetRadius=ch.dz||80;
  smoothPhi=1.15;
}

function goTo(i){
  if(transitionActive||i===curCh) return;
  _closeD(); transitionActive=true;
  const veil=document.getElementById('veil'); veil.classList.add('dk');
  setTimeout(()=>{
    curCh=i; clearSG(); CHAPTERS[i].build(); updateUI();
    veil.classList.remove('dk');
    setTimeout(()=>transitionActive=false,800);
  },660);
}

// ── DETAIL CARD ──────────────────────────────────────────
function showD(d){
  document.getElementById('dct').textContent=d.title;
  document.getElementById('dcs').textContent=d.sub;
  document.getElementById('dcb').textContent=d.body;
  document.getElementById('dcf').innerHTML=(d.facts||[]).map(f=>`<div class="dcfi">${f.k}<span>${f.v}</span></div>`).join('');
  const el=document.getElementById('dc'); el.classList.remove('show'); void el.offsetWidth; el.classList.add('show');
}
function _closeD(){ document.getElementById('dc').classList.remove('show'); }
window._closeD=_closeD;

// ── MAIN LOOP ────────────────────────────────────────────
let elapsed=0, lastTS=performance.now();

function animate(ts){
  requestAnimationFrame(animate);
  const dt=Math.min((ts-lastTS)/1000,.05); lastTS=ts; elapsed+=dt;

  smoothRadius+=(targetRadius-smoothRadius)*.05;

  if(autoRotating && !isDragging){
    smoothTheta+=autoRotSpeed;
  }

  updateCamera();
  bgStars.rotation.y=elapsed*.003;

  for(const u of uniList){ if(u.u_t) u.u_t.value=elapsed; }

  sg.traverse(o=>{
    if(o.userData.isBBP){
      const pos=o.geometry.attributes.position.array,v=o.userData.vel;
      for(let i=0;i<pos.length;i+=3){
        pos[i]+=v[i]; pos[i+1]+=v[i+1]; pos[i+2]+=v[i+2];
        if(Math.hypot(pos[i],pos[i+1],pos[i+2])>140){pos[i]*=.01;pos[i+1]*=.01;pos[i+2]*=.01;}
      }
      o.geometry.attributes.position.needsUpdate=true;
    }

    if(o.userData.ri!==undefined){
      const ph=(elapsed*.07+o.userData.ri/7)%1, sc=ph*5+.2;
      o.scale.set(sc,sc,sc);
      o.material.opacity=Math.max(0,(1-ph)*.14-o.userData.ri*.012);
    }

    if(o.userData.isGalaxy) o.rotation.y=elapsed*.004;

    if(o.userData.isPiv){
      const speed=0.12/o.userData.period;
      o.rotation.y=(o.userData.initAngle||0)+elapsed*speed;
    }

    if(o.userData.spinY) o.rotation.y+=o.userData.spinY*dt*60;

    if(o.userData.isMoonPiv) o.rotation.y=elapsed*.014;

    // Solar wind particles
    if(o.userData.isSolarWind){
      const pos=o.geometry.attributes.position.array,v=o.userData.vel;
      for(let i=0;i<pos.length;i+=3){
        pos[i]+=v[i]; pos[i+1]+=v[i+1]; pos[i+2]+=v[i+2];
        const r=Math.hypot(pos[i],pos[i+1],pos[i+2]);
        if(r>65){pos[i]*=.28;pos[i+1]*=.28;pos[i+2]*=.28;}
      }
      o.geometry.attributes.position.needsUpdate=true;
    }

    // Hawking radiation particles
    if(o.userData.isHR){
      const pos=o.geometry.attributes.position.array,v=o.userData.vel;
      for(let i=0;i<pos.length;i+=3){
        pos[i]+=v[i]; pos[i+1]+=v[i+1]; pos[i+2]+=v[i+2];
        const r=Math.hypot(pos[i],pos[i+1],pos[i+2]);
        if(r>35){pos[i]*=.27;pos[i+1]*=.27;pos[i+2]*=.27;}
      }
      o.geometry.attributes.position.needsUpdate=true;
    }
  });

  renderer.render(scene,camera);
}

// ── INIT ─────────────────────────────────────────────────
buildNav();
CHAPTERS[0].build();
updateUI();
animate(performance.now());

})();
</script>
</body>
</html>

