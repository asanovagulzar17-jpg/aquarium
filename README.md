// feeding
let feeding=false,foods=[];
$('feed').onclick=()=>{feeding=!feeding;$('feed').classList.toggle('active',feeding);tank.classList.toggle('feeding',feeding)};
tank.addEventListener('pointerdown',e=>{if(!feeding)return;if(e.target.closest('.fish,.trash,.binbox'))return;
 const r=tank.getBoundingClientRect();const cx=e.clientX-r.left,cy=e.clientY-r.top;
 for(let i=0;i<4;i++){const el=document.createElement('div');el.className='food';const x=cx+Math.random()*40-20,y=cy+Math.random()*24-12;
  el.style.left=x+'px';el.style.top=y+'px';tank.appendChild(el);foods.push({el,x,y})}});
function eatFood(fd){fd.el.remove();foods=foods.filter(z=>z!==fd)}
// swimming loop
(function loop(){const W=tank.clientWidth,H=tank.clientHeight;
 foods.forEach(fd=>{fd.y+=.5;fd.el.style.left=fd.x+'px';fd.el.style.top=fd.y+'px';if(fd.y>H-10)eatFood(fd)});
 fishes.forEach(f=>{f.ph+=.03;
  let target=null,best=99999;
  foods.forEach(fd=>{const dx=fd.x-(f.x+60),dy=fd.y-(f.y+45),d=Math.hypot(dx,dy);if(d<best){best=d;target=fd}});
  if(target&&best<420){const dx=target.x-(f.x+60),dy=target.y-(f.y+45),d=Math.hypot(dx,dy)||1;
   f.vx=f.vx*.85+(dx/d)*1.6*.15;f.vy=f.vy*.85+(dy/d)*1.6*.15;
   f.x+=f.vx;f.y+=f.vy;
   if(d<26){eatFood(target);f.jump=24;bubbles(f.x+60,f.y+45)}
  } else {f.x+=f.vx;f.y+=f.vy+Math.sin(f.ph)*.35}
  if(f.x<0){f.x=0;f.vx=Math.abs(f.vx)}if(f.x>W-120){f.x=W-120;f.vx=-Math.abs(f.vx)}
  if(f.y<0){f.y=0;f.vy=Math.abs(f.vy)}if(f.y>H*.75){f.y=H*.75;f.vy=-Math.abs(f.vy)}
  if(!target&&Math.random()<.005)f.vy=(Math.random()-.5)*.6;
  let j=0;if(f.jump>0){j=-Math.sin((30-f.jump)/30*Math.PI)*40;f.jump--}
  f.el.style.transform=`translate(${f.x}px,${f.y+j}px)`;f.el.firstChild.style.transform=f.vx<0?'scaleX(-1)':'none'});
 requestAnimationFrame(loop)})();
// pollution game
// bin images
document.querySelectorAll('.binbox').forEach(b=>{b.querySelector('img').src=BIN_IMGS[b.dataset.cat]});
function showWrong(){const w=$('wrongmsg');w.style.display='block';setTimeout(()=>w.style.display='none',1400)}
$('dirty').onclick=()=>{if(dirty)return;dirty=true;tank.classList.add('dirty');$('bins').style.display='flex';
 const W=tank.clientWidth,H=tank.clientHeight;
 TRASH_IMGS.forEach(it=>{const t=document.createElement('div');t.className='trash';t.dataset.cat=it.cat;t.innerHTML=`<img src="${it.src}" alt="${it.name}">`;
  t.style.left=(20+Math.random()*(W-170))+'px';t.style.top=(20+Math.random()*(H*.6))+'px';t.style.animationDelay=(Math.random()*2)+'s';tank.appendChild(t);trashN++;
  t.onpointerdown=ev=>{ev.preventDefault();t.setPointerCapture(ev.pointerId);t.style.animation='none';t.style.zIndex=50;
   t.onpointermove=m=>{const r=tank.getBoundingClientRect();let x=m.clientX-r.left-32,y=m.clientY-r.top-32;
    x=Math.max(-20,Math.min(W-40,x));y=Math.max(-20,Math.min(H-20,y));t.style.left=x+'px';t.style.top=y+'px'};
   t.onpointerup=u=>{t.onpointermove=t.onpointerup=null;t.style.zIndex=5;
    let hit=null;document.querySelectorAll('.binbox').forEach(b=>{const br=b.getBoundingClientRect();
     if(u.clientX>br.left-10&&u.clientX<br.right+10&&u.clientY>br.top-10&&u.clientY<br.bottom+10)hit=b});
    if(hit){
     if(hit.dataset.cat===t.dataset.cat){t.remove();if(--trashN===0)clean();}
     else{showWrong();t.style.animation='bob 2s ease-in-out infinite';}
    } else {t.style.animation='bob 2s ease-in-out infinite';}
   }}})};
function clean(){dirty=false;tank.classList.remove('dirty');$('bins').style.display='none';const m=$('msg');m.style.display='flex';fishes.forEach(f=>f.jump=30);setTimeout(()=>m.style.display='none',2600)}
$('reset').onclick=()=>{fishes.forEach(f=>f.el.remove());fishes=[];count();document.querySelectorAll('.trash').forEach(t=>t.remove());trashN=0;dirty=false;tank.classList.remove('dirty');$('bins').style.display='none';tray.innerHTML='<span class="empty">Суреттерді қайта жүктеңіз</span>'};
</script>
</body>
