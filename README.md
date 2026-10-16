<!DOCTYPE html>
<html lang="th">
<head>
<meta http-equiv="Content-Security-Policy" content="default-src 'none'; script-src 'unsafe-inline'; style-src 'unsafe-inline'; img-src data:; font-src data:; connect-src 'none'; form-action 'none'; base-uri 'none'"><script>(function(){function mk(){var m={};return{getItem:function(k){k=String(k);return Object.prototype.hasOwnProperty.call(m,k)?m[k]:null;},setItem:function(k,v){m[String(k)]=String(v);},removeItem:function(k){delete m[String(k)];},clear:function(){m={};},key:function(i){var ks=Object.keys(m);return i>=0&&i<ks.length?ks[i]:null;},get length(){return Object.keys(m).length;}};}var names=['localStorage','sessionStorage'];for(var i=0;i<names.length;i++){var n=names[i],ok=false;try{ok=!!window[n];}catch(e){ok=false;}if(!ok){try{Object.defineProperty(window,n,{value:mk(),configurable:true,writable:false});}catch(e){}}}})();</script><script>(function(){var sid="app-codemode/59ba3e19-c4ef-4fe2-ae45-08d527c5a3bf",runId='r'+Math.random().toString(36).slice(2,10);var P=window.performance&&typeof performance.now==='function';var t0=P?performance.now():Date.now();function now(){return Math.max(0,Math.round((P?performance.now():Date.now())-t0));}var q=[],dropped=0,timer=null,errN=0,otherN=0;var ERR_BUDGET=300,OTHER_BUDGET=1500,MAX_MSG=500,MAX_DATA=800,BATCH=100;function ser(v){if(v===undefined)return '';var s;try{s=typeof v==='string'?v:JSON.stringify(v,function(k,x){if(typeof x==='bigint')return String(x);if(x instanceof Error)return{name:x.name,message:x.message,stack:String(x.stack||'').split('\n').slice(0,3).join(' | ')};return x;});}catch(e){try{s=String(v);}catch(e2){s='[unserializable]';}}if(s==null)s='';s=String(s);return s.length>MAX_DATA?s.slice(0,MAX_DATA-1)+'\u2026':s;}function flush(){timer=null;if(!q.length&&!dropped)return;var batch=q.splice(0,BATCH),d=dropped;dropped=0;try{parent.postMessage({type:'vfs:log',surfaceId:sid,runId:runId,entries:batch,dropped:d},'*');}catch(e){}if(q.length)schedule();}function schedule(){if(timer==null)timer=setTimeout(flush,50);}function push(level,source,msg,data){try{var isErr=level==='error';if(isErr?errN>=ERR_BUDGET:otherN>=OTHER_BUDGET){dropped++;schedule();return;}if(isErr)errN++;else otherN++;var m=String(msg==null?'':msg);if(m.length>MAX_MSG)m=m.slice(0,MAX_MSG-1)+'\u2026';var e={level:level,source:source,message:m,t:now()};var ds=ser(data);if(ds)e.data=ds;q.push(e);if(isErr){if(timer!=null){clearTimeout(timer);timer=null;}flush();}else{schedule();}}catch(e){}}function fmtArgs(a){var p=[];for(var i=0;i<a.length;i++){p.push(typeof a[i]==='string'?a[i]:ser(a[i]));}return p.join(' ');}window.bmsDebug={log:function(m,d){push('info','app',m,d);},info:function(m,d){push('info','app',m,d);},warn:function(m,d){push('warn','app',m,d);},error:function(m,d){push('error','app',m,d);},debug:function(m,d){push('debug','app',m,d);}};var lv={log:'info',info:'info',warn:'warn',error:'error'};for(var k in lv){(function(name,level){var orig=console[name];console[name]=function(){try{if(typeof orig==='function')orig.apply(console,arguments);}catch(e){}push(level,'console',fmtArgs(arguments));};})(k,lv[k]);}window.addEventListener('error',function(ev){var msg=ev&&ev.message?ev.message:'error';var where=ev&&ev.filename?(' @'+ev.filename+':'+(ev.lineno||0)+':'+(ev.colno||0)):'';var st=ev&&ev.error&&ev.error.stack?String(ev.error.stack).split('\n').slice(0,3).join(' | '):'';push('error','uncaught',msg+where,st||undefined);});window.addEventListener('unhandledrejection',function(ev){var r=ev&&ev.reason,msg=r&&r.message?r.message:String(r);var st=r&&r.stack?String(r.stack).split('\n').slice(0,3).join(' | '):'';push('error','rejection','unhandled rejection: '+msg,st||undefined);});window.addEventListener('load',function(){push('info','host','window load');});push('info','host','run started');})();</script><script>(function(){var seq=0,pending={};window.addEventListener('message',function(e){var d=e.data;if(!d||(d.type!=='vfs:data:result'&&d.type!=='vfs:llm:result'&&d.type!=='vfs:tts:result'&&d.type!=='vfs:mermaid:result')||d.surfaceId!=="app-codemode/59ba3e19-c4ef-4fe2-ae45-08d527c5a3bf")return;var p=pending[d.requestId];if(!p)return;delete pending[d.requestId];if(d.ok){p.resolve(d.type==='vfs:llm:result'?(d.text||''):d.type==='vfs:tts:result'?{audio:d.audio,mime:d.mime}:d.type==='vfs:mermaid:result'?(d.svg||''):(d.rows||[]));}else{p.reject(new Error(d.error||'live query failed'));}});function ask(msg,ms){return new Promise(function(res,rej){var rid='r'+(++seq);pending[rid]={resolve:res,reject:rej};msg.surfaceId="app-codemode/59ba3e19-c4ef-4fe2-ae45-08d527c5a3bf";msg.requestId=rid;parent.postMessage(msg,'*');setTimeout(function(){if(pending[rid]){delete pending[rid];rej(new Error('หมดเวลารอข้อมูลจากระบบ'));}},ms||20000);});}window.bmsData={query:function(id){return ask({type:'vfs:data',queryId:String(id)});},sql:function(statement,params){return ask({type:'vfs:sql',sql:String(statement),params:params||undefined});}};window.bmsAI={ask:function(prompt){return ask({type:'vfs:llm',prompt:String(prompt)});}};var ttsCtx=null,ttsSrc=null;function ttsContext(){if(!ttsCtx){var C=window.AudioContext||window.webkitAudioContext;if(!C)throw new Error('เบราว์เซอร์นี้ไม่รองรับเสียง');ttsCtx=new C();}if(ttsCtx.state==='suspended'){try{ttsCtx.resume();}catch(e){}}return ttsCtx;}window.bmsTTS={speak:function(text,opts){opts=opts||{};var ctx;try{ctx=ttsContext();}catch(e){return Promise.reject(e);}return ask({type:'vfs:tts',text:String(text),voice:opts.voice?String(opts.voice):undefined},60000).then(function(r){return new Promise(function(res,rej){ctx.decodeAudioData(r.audio.slice(0),function(buf){if(ttsSrc){try{ttsSrc.stop();}catch(e){}}var s=ctx.createBufferSource();s.buffer=buf;s.connect(ctx.destination);s.onended=function(){if(ttsSrc===s){ttsSrc=null;}res();};ttsSrc=s;s.start(0);},function(){rej(new Error('ถอดรหัสเสียงไม่สำเร็จ'));});});});},stop:function(){if(ttsSrc){try{ttsSrc.stop();}catch(e){}ttsSrc=null;}}};window.bmsMermaid={render:function(code,opts){opts=opts||{};return ask({type:'vfs:mermaid',code:String(code),theme:opts.theme?String(opts.theme):undefined},90000);},renderAll:function(root){var nodes=(root||document).querySelectorAll('.mermaid'),jobs=[];for(var i=0;i<nodes.length;i++){(function(el){if(el.getAttribute('data-mermaid'))return;var src=el.textContent||'';if(!src.replace(/\s/g,''))return;el.setAttribute('data-mermaid','pending');jobs.push(window.bmsMermaid.render(src,{theme:el.getAttribute('data-theme')||undefined}).then(function(svg){el.innerHTML=svg;el.setAttribute('data-mermaid','done');},function(err){el.setAttribute('data-mermaid','failed');el.setAttribute('data-mermaid-error',String((err&&err.message)||err));}));})(nodes[i]);}return Promise.all(jobs);}};if(document.readyState==='loading'){document.addEventListener('DOMContentLoaded',function(){window.bmsMermaid.renderAll();});}else{window.bmsMermaid.renderAll();}var vfsDl={},blobMap=new Map(),lastDl=null,lastDlAt=0;if(typeof URL!=='undefined'&&typeof URL.createObjectURL==='function'){var origCreate=URL.createObjectURL.bind(URL),origRevoke=typeof URL.revokeObjectURL==='function'?URL.revokeObjectURL.bind(URL):null;URL.createObjectURL=function(obj){var u=origCreate(obj);try{if(obj&&typeof Blob!=='undefined'&&obj instanceof Blob){blobMap.set(u,obj);}}catch(e){}return u;};if(origRevoke){URL.revokeObjectURL=function(u){setTimeout(function(){blobMap.delete(u);try{origRevoke(u);}catch(e){}},5000);};}}function postDl(fn,mime,data,enc){try{parent.postMessage({type:'vfs:download',surfaceId:"app-codemode/59ba3e19-c4ef-4fe2-ae45-08d527c5a3bf",filename:String(fn||'download'),mime:String(mime||'application/octet-stream'),data:String(data||''),encoding:enc==='base64'?'base64':'utf8'},'*');}catch(e){}}function dlDataUri(uri,fn){var m=/^data:([^;,]*)(?:;charset=[^;,]*)?(;base64)?,([\s\S]*)$/i.exec(uri);if(!m)return false;var mime=m[1]||'application/octet-stream',isB64=!!m[2],body=m[3]||'';if(isB64){postDl(fn,mime,body,'base64');}else{try{postDl(fn,mime,decodeURIComponent(body),'utf8');}catch(e){postDl(fn,mime,body,'utf8');}}return true;}function dlBlob(b,fn,fallbackMime){if(!b)return false;var mime=b.type||fallbackMime||'application/octet-stream';if(typeof FileReader!=='undefined'){var fr=new FileReader();fr.onload=function(){if(typeof fr.result==='string')dlDataUri(fr.result,fn);};fr.readAsDataURL(b);return true;}if(typeof b.text==='function'){b.text().then(function(t){postDl(fn,mime,t,'utf8');});return true;}return false;}function dlHref(rawHref,fn){if(!rawHref)return false;var h=String(rawHref).trim();if(/^data:/i.test(h))return dlDataUri(h,fn||'download');if(/^blob:/i.test(h)){var b=blobMap.get(h);if(b)return dlBlob(b,fn||(b.name||'download'));return false;}var p=h.split(/[?#]/)[0];var k=p.startsWith('/')?p:'/'+p.replace(/^\.\//,'');var vf=vfsDl[k];if(vf){postDl(fn||vf.n,vf.m,vf.d,vf.e);return true;}return false;}function shouldDlAnchor(a){if(!a||typeof a.getAttribute!=='function')return false;var raw=a.getAttribute('href')||a.href||'';if(!raw)return false;if(a.hasAttribute('download'))return true;if(/^(?:data:|blob:)/i.test(raw))return true;var p=String(raw).split(/[?#]/)[0];var k=p.startsWith('/')?p:'/'+p.replace(/^\.\//,'');return !!vfsDl[k];}function runAnchorDl(a){var now=Date.now();if(lastDl===a&&now-lastDlAt<150)return true;lastDl=a;lastDlAt=now;var raw=a.getAttribute('href')||a.href||'';var fn=a.getAttribute('download')||a.download||'';return dlHref(raw,fn);}if(typeof HTMLAnchorElement!=='undefined'&&HTMLAnchorElement.prototype){var origAClick=HTMLAnchorElement.prototype.click;HTMLAnchorElement.prototype.click=function(){if(shouldDlAnchor(this)&&runAnchorDl(this))return;if(typeof origAClick==='function')return origAClick.apply(this,arguments);};}window.addEventListener('click',function(ev){var t=ev&&ev.target;var a=t&&typeof t.closest==='function'?t.closest('a'):null;if(a&&shouldDlAnchor(a)){if(runAnchorDl(a)){ev.preventDefault();ev.stopPropagation();}}},true);window.bmsDownload=function(content,filename,mime){var fn=String(filename||'download');var m=String(mime||'text/plain;charset=utf-8');if(typeof Blob!=='undefined'&&content instanceof Blob){dlBlob(content,fn,m);return;}postDl(fn,m,typeof content==='string'?content:JSON.stringify(content),'utf8');};if(!window.saveAs){window.saveAs=function(b,fn){window.bmsDownload(b,fn);};}})();</script>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>ตัวชี้วัด: ผู้ป่วยจิตเวชกลับมารักษาซ้ำ (28/90/180 วัน)</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:"Sarabun","Segoe UI",Tahoma,sans-serif;background:#eef2f7;color:#16233a;padding:18px}
.wrap{max-width:1240px;margin:0 auto}
header{background-color:#123a6b;background-image:linear-gradient(135deg,#123a6b,#1f6fb2);color:#fff;border-radius:14px;padding:20px 24px;box-shadow:0 6px 18px rgba(18,58,107,.25)}
header h1{font-size:22px;line-height:1.35}
header .sub{font-size:13px;color:#dbe8f6;margin-top:6px}
.tag{display:inline-block;background-color:#1b4f86;border:1px solid rgba(255,255,255,.35);border-radius:20px;padding:3px 11px;font-size:12px;margin:8px 6px 0 0}
.bar{display:flex;flex-wrap:wrap;gap:10px;align-items:center;background:#fff;border-radius:12px;padding:12px 16px;margin:14px 0;box-shadow:0 2px 8px rgba(20,40,70,.08)}
.bar label{font-size:13px;font-weight:600;color:#41546f}
select,button{font-family:inherit;font-size:13px;padding:7px 10px;border-radius:8px;border:1px solid #c8d4e3;background:#fff;color:#16233a}
button{cursor:pointer;background:#1f6fb2;color:#fff;border-color:#1f6fb2;font-weight:600}
button.ghost{background:#fff;color:#1f6fb2}
.kpis{display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:12px}
.kpi{background:#fff;border-radius:12px;padding:14px 16px;border-left:5px solid #1f6fb2;box-shadow:0 2px 8px rgba(20,40,70,.08)}
.kpi .t{font-size:12.5px;color:#5b6d86;font-weight:600}
.kpi .v{font-size:30px;font-weight:800;margin:4px 0;letter-spacing:-.5px}
.kpi .d{font-size:12px;color:#6b7c94}
.kpi.good{border-left-color:#12855a}.kpi.good .v{color:#12855a}
.kpi.bad{border-left-color:#c92a2a}.kpi.bad .v{color:#c92a2a}
.kpi.warn{border-left-color:#e8590c}.kpi.warn .v{color:#e8590c}
.card{background:#fff;border-radius:12px;padding:16px 18px;margin-top:14px;box-shadow:0 2px 8px rgba(20,40,70,.08)}
.card h2{font-size:15.5px;color:#123a6b;margin-bottom:4px}
.card .hint{font-size:12px;color:#6b7c94;margin-bottom:12px}
table{width:100%;border-collapse:collapse;font-size:13px}
th,td{padding:7px 8px;text-align:center;border-bottom:1px solid #e6ecf4}
th{background:#f4f7fb;color:#3d5474;font-weight:700;font-size:12.5px}
td.g{text-align:left;font-weight:600;white-space:nowrap}
td.n{font-size:11.5px;color:#7b8ba1}
tr.tot td{background:#f8fafd;font-weight:800;border-top:2px solid #cfdcec}
.chip{display:inline-block;padding:2px 8px;border-radius:20px;font-size:11.5px;font-weight:700;color:#fff}
.legend{display:flex;flex-wrap:wrap;gap:12px;font-size:12px;margin:10px 0}
.legend span{display:flex;align-items:center;gap:5px}
.dot{width:11px;height:11px;border-radius:3px;display:inline-block}
.chart{display:flex;flex-direction:column;gap:9px}
.row{display:grid;grid-template-columns:150px 1fr;gap:10px;align-items:center}
.row .lbl{font-size:12.5px;font-weight:600;text-align:right;color:#3d5474}
.bars{display:flex;flex-direction:column;gap:3px}
.b{height:15px;border-radius:4px;position:relative;min-width:2px}
.b span{position:absolute;right:5px;top:0;font-size:10.5px;color:#fff;font-weight:700;line-height:15px}
svg{width:100%;height:280px}
.note{font-size:12.5px;color:#4a5c76;line-height:1.75}
.note li{margin-left:18px}
.ins{font-size:13.5px;line-height:1.85}
.ins b{color:#123a6b}
footer{font-size:11.5px;color:#7b8ba1;text-align:center;margin:16px 0 4px}
</style>
</head>
<body>
<div class="wrap">
<header>
  <h1>ตัวชี้วัด: อัตราผู้ป่วยโรคทางจิตเวชกลับมารักษาเป็นผู้ป่วยในซ้ำ<br>ภายใน 28 วัน / 90 วัน / 180 วัน</h1>
  <div class="sub">โรงพยาบาลพระศรีมหาโพธิ์ · กลุ่มงานจิตเวชและสารเสพติด · ข้อมูลจาก HOSxP (ตาราง ipt + iptdiag)</div>
  <div><span class="tag" id="tagPeriod">ช่วงข้อมูล</span><span class="tag">แยกตามกลุ่มโรค ICD-10 บทที่ F</span><span class="tag">สำหรับผู้บริหาร</span></div>
</header>

<div class="bar">
  <label>ปีงบประมาณ (พ.ศ.)</label>
  <select id="fYear"><option value="all">ทุกปี</option></select>
  <label>ตัวชี้วัด</label>
  <select id="fMetric">
    <option value="r28">กลับซ้ำภายใน 28 วัน</option>
    <option value="r90">กลับซ้ำภายใน 90 วัน</option>
    <option value="r180">กลับซ้ำภายใน 180 วัน</option>
  </select>
  <button id="btnCsv">ดาวน์โหลด CSV</button>
  <button class="ghost" id="btnReset">รีเซ็ต</button>
</div>

<div class="kpis" id="kpis"></div>

<div class="card">
  <h2>1) ตารางตัวชี้วัด แยกกลุ่มโรค × ช่วงเวลา</h2>
  <div class="hint">ตัวเลขในช่อง = อัตรากลับมารักษาซ้ำ (%) · ตัวเลขใต้ช่อง = จำนวนกลับซ้ำ / จำนวนจำหน่ายทั้งหมด (ราย) · สีเขียว = ผ่านเกณฑ์, แดง = ไม่ผ่าน</div>
  <div style="overflow-x:auto"><table id="tbl"></table></div>
</div>

<div class="card">
  <h2>2) กราฟแท่งเปรียบเทียบระหว่างกลุ่มโรค</h2>
  <div class="hint" id="chartHint"></div>
  <div class="legend" id="legend"></div>
  <div class="chart" id="chart"></div>
</div>

<div class="card">
  <h2>3) แนวโน้มรายปี (Trend)</h2>
  <div class="hint">เส้นแสดงอัตรากลับซ้ำของแต่ละกลุ่มโรคตามปีที่เลือกตัวชี้วัด</div>
  <svg id="trend" viewBox="0 0 900 280" preserveAspectRatio="none"></svg>
  <div class="legend" id="legend2"></div>
</div>

<div class="card">
  <h2>4) สรุปประเด็นสำคัญสำหรับผู้บริหาร</h2>
  <div class="ins" id="insight"></div>
</div>

<div class="card">
  <h2>หมายเหตุ / วิธีคำนวณ</h2>
  <div class="note"><ul>
    <li><b>ประชากร (denominator)</b>: การจำหน่ายผู้ป่วยใน (ipt.dchdate ไม่ว่าง) ที่มี<b>การวินิจฉัยหลัก (diagtype='1')</b> อยู่ในกลุ่ม ICD-10 บทที่ F (F00–F99) โดยใช้การวินิจฉัยหลัก 1 รหัสต่อ AN</li>
    <li><b>ตัวตั้ง (numerator)</b>: มีการรับไว้รักษาเป็นผู้ป่วยใน (AN ใหม่) ด้วย HN เดิม ภายใน 28 / 90 / 180 วัน นับจากวันจำหน่าย (ipt.regdate − ipt.dchdate) ไม่จำกัดกลุ่มโรคของการนอนครั้งใหม่</li>
    <li><b>การแบ่งกลุ่มโรค</b>: F10 = สุรา · F11–F19 = สารเสพติด · F20–F29 = จิตเภท · F30–F39 = อารมณ์ · F40–F48 = วิตกกังวล · F00–F09 = อินทรีย์ · F50–F99 = อื่น ๆ</li>
    <li><b>เกณฑ์อ้างอิง</b>: ตัวชี้วัด 28 วัน ใช้เป้าหมาย &lt; ร้อยละ 2 (ตามแผนตัวชี้วัด) · 90 และ 180 วัน ยังไม่มีเกณฑ์ในแผน จึงแสดงค่าเทียบเคียงจากข้อมูลจริง</li>
    <li><b>ข้อจำกัด</b>: ปี พ.ศ. 2569 เป็นข้อมูลไม่เต็มปี (ถึงวันที่ดึงข้อมูล) ทำให้อัตรา 90/180 วันต่ำกว่าความเป็นจริงเล็กน้อย (censoring) · ยังไม่ตัดการนอนซ้ำที่เกิดจากการส่งต่อ/ย้ายหอผู้ป่วย</li>
  </ul></div>
</div>
<footer> ข้อมูล ณ วันที่ 16 ตุลาคม 2569 จากฐานข้อมูล HOSxP ตัวเลขคำนวณจากฐานจริง ไม่ใช่ค่าประมาณ</footer>
</div>
<script>/* ข้อมูลจริงจาก HOSxP: [กลุ่มโรค, ปี(ค.ศ.), จำหน่าย(ราย), กลับซ้ำ28, กลับซ้ำ90, กลับซ้ำ180] */
var RAW = [
["1.สุรา (F10)",2022,137,7,10,17],["1.สุรา (F10)",2023,155,8,16,23],["1.สุรา (F10)",2024,98,11,15,16],["1.สุรา (F10)",2025,125,7,9,16],["1.สุรา (F10)",2026,87,9,14,15],
["2.สารเสพติด (F11-F19)",2022,786,103,144,197],["2.สารเสพติด (F11-F19)",2023,1014,124,166,213],["2.สารเสพติด (F11-F19)",2024,1156,150,193,243],["2.สารเสพติด (F11-F19)",2025,1174,136,185,241],["2.สารเสพติด (F11-F19)",2026,832,94,130,148],
["3.จิตเภท (F20-F29)",2022,1728,598,716,817],["3.จิตเภท (F20-F29)",2023,2503,1140,1273,1393],["3.จิตเภท (F20-F29)",2024,2489,1307,1419,1512],["3.จิตเภท (F20-F29)",2025,2266,1062,1208,1296],["3.จิตเภท (F20-F29)",2026,1391,517,583,615],
["4.อารมณ์ (F30-F39)",2022,422,63,98,114],["4.อารมณ์ (F30-F39)",2023,464,65,98,130],["4.อารมณ์ (F30-F39)",2024,476,103,149,172],["4.อารมณ์ (F30-F39)",2025,424,87,119,141],["4.อารมณ์ (F30-F39)",2026,331,32,54,61],
["5.วิตกกังวล (F40-F48)",2022,24,3,4,5],["5.วิตกกังวล (F40-F48)",2023,29,5,7,7],["5.วิตกกังวล (F40-F48)",2024,14,1,2,2],["5.วิตกกังวล (F40-F48)",2025,21,6,7,9],["5.วิตกกังวล (F40-F48)",2026,12,2,2,2],
["6.อินทรีย์ (F00-F09)",2022,73,35,37,42],["6.อินทรีย์ (F00-F09)",2023,113,60,65,71],["6.อินทรีย์ (F00-F09)",2024,106,66,66,70],["6.อินทรีย์ (F00-F09)",2025,95,64,66,73],["6.อินทรีย์ (F00-F09)",2026,73,39,42,42],
["7.อื่นๆ (F50-F99)",2022,185,78,103,117],["7.อื่นๆ (F50-F99)",2023,240,110,151,164],["7.อื่นๆ (F50-F99)",2024,218,100,129,145],["7.อื่นๆ (F50-F99)",2025,229,110,135,147],["7.อื่นๆ (F50-F99)",2026,199,66,84,90]
];
var GROUPS = ["1.สุรา (F10)","2.สารเสพติด (F11-F19)","3.จิตเภท (F20-F29)","4.อารมณ์ (F30-F39)","5.วิตกกังวล (F40-F48)","6.อินทรีย์ (F00-F09)","7.อื่นๆ (F50-F99)"];
var COLOR = {"1.สุรา (F10)":"#e8590c","2.สารเสพติด (F11-F19)":"#c92a2a","3.จิตเภท (F20-F29)":"#1f6fb2","4.อารมณ์ (F30-F39)":"#7048e8","5.วิตกกังวล (F40-F48)":"#0ca678","6.อินทรีย์ (F00-F09)":"#b8860b","7.อื่นๆ (F50-F99)":"#5c7cfa"};
var YEARS = [2022,2023,2024,2025,2026];
var MI = {r28:3, r90:4, r180:5};
var TARGET28 = 2.0;
var state = { year:"all", metric:"r28" };

function be(y){ return y+543; }
function pct(n,d){ return d>0 ? (n*100/d) : 0; }
function fmt(v){ return v.toFixed(2); }
function agg(rows, mi){
  var d=0,n=0; rows.forEach(function(r){ d+=r[2]; n+=r[mi]; });
  return {d:d, n:n, rate: pct(n,d)};
}
function rowsFor(year){ return RAW.filter(function(r){ return year==="all" || r[1]===year; }); }
function rowsForGroup(g, year){ return RAW.filter(function(r){ return r[0]===g && (year==="all" || r[1]===year); }); }

function colorFor(rate, mi){
  if(mi!==3) return "#1f6fb2";
  if(rate < TARGET28) return "#12855a";
  if(rate < TARGET28*2) return "#e8590c";
  return "#c92a2a";
}

/* ---------- KPI ---------- */
function renderKpi(){
  var rows = rowsFor(state.year);
  var out = [];
  var tot = agg(rows, MI[state.metric]);
  var cls = state.metric==="r28" ? (tot.rate<TARGET28?"good":tot.rate<TARGET28*2?"warn":"bad") : "good";
  out.push('<div class="kpi '+cls+'"><div class="t">อัตรากลับมารักษาซ้ำ '+state.metric.slice(1)+' วัน (รวมทุกกลุ่มโรค)</div><div class="v">'+fmt(tot.rate)+'%</div><div class="d">กลับซ้ำ '+tot.n+' / จำหน่าย '+tot.d+' ราย'+(state.metric==="r28"?" · เป้าหมาย &lt; 2%":"")+'</div></div>');
  var t28 = agg(rows,3), t90 = agg(rows,4), t180 = agg(rows,5);
  out.push('<div class="kpi"><div class="t">ผู้ป่วยในจิตเวชที่จำหน่าย</div><div class="v">'+t28.d.toLocaleString()+'</div><div class="d">ราย (นับครั้งที่จำหน่าย) · '+state.year.replace("all","ทุกปี").replace(/^(\\d{4})$/,function(m){return be(+m);})+'</div></div>');
  out.push('<div class="kpi"><div class="t">อัตรากลับซ้ำ 90 วัน</div><div class="v">'+fmt(t90.rate)+'%</div><div class="d">'+t90.n+' ราย</div></div>');
  out.push('<div class="kpi"><div class="t">อัตรากลับซ้ำ 180 วัน</div><div class="v">'+fmt(t180.rate)+'%</div><div class="d">'+t180.n+' ราย</div></div>');
  var ranked = GROUPS.map(function(g){ var a=agg(rowsForGroup(g,state.year),MI[state.metric]); return {g:g, rate:a.rate, n:a.n, d:a.d}; })
    .filter(function(x){ return x.d>0; }).sort(function(a,b){ return b.rate-a.rate; });
  if(ranked.length){
    var w = ranked[0], b = ranked[ranked.length-1];
    out.push('<div class="kpi bad"><div class="t">กลุ่มโรคกลับซ้ำสูงสุด ('+state.metric.slice(1)+' วัน)</div><div class="v" style="font-size:17px">'+w.g+'</div><div class="d">'+fmt(w.rate)+'% ('+w.n+'/'+w.d+' ราย)</div></div>');
    out.push('<div class="kpi good"><div class="t">กลุ่มโรคกลับซ้ำต่ำสุด ('+state.metric.slice(1)+' วัน)</div><div class="v" style="font-size:17px">'+b.g+'</div><div class="d">'+fmt(b.rate)+'% ('+b.n+'/'+b.d+' ราย)</div></div>');
  }
  document.getElementById("kpis").innerHTML = out.join("");
}

/* ---------- ตาราง ---------- */
function renderTable(){
  var mi = MI[state.metric];
  var h = "";
  if(state.year==="all"){
    h += "<tr><th style='text-align:left'>กลุ่มโรค (ICD-10)</th>";
    YEARS.forEach(function(y){ h += "<th>พ.ศ. "+be(y)+"</th>"; });
    h += "<th>รวม</th></tr>";
    GROUPS.forEach(function(g){
      h += "<tr><td class='g'>"+g+"</td>";
      YEARS.forEach(function(y){
        var a = agg(rowsForGroup(g,y), mi);
        if(a.d===0){ h += "<td class='n'>-</td>"; return; }
        h += "<td><span class='chip' style='background:"+colorFor(a.rate,mi)+"'>"+fmt(a.rate)+"</span><div class='n'>"+a.n+"/"+a.d+"</div></td>";
      });
      var t = agg(rowsForGroup(g,"all"), mi);
      h += "<td><b>"+fmt(t.rate)+"</b><div class='n'>"+t.n+"/"+t.d+"</div></td></tr>";
    });
    var all = agg(rowsFor("all"), mi);
    h += "<tr class='tot'><td class='g'>รวมทุกกลุ่มโรค</td>";
    YEARS.forEach(function(y){ var a=agg(rowsFor(y),mi); h += "<td>"+(a.d?fmt(a.rate):"-")+"<div class='n'>"+a.n+"/"+a.d+"</div></td>"; });
    h += "<td>"+fmt(all.rate)+"<div class='n'>"+all.n+"/"+all.d+"</div></td></tr>";
  } else {
    h += "<tr><th style='text-align:left'>กลุ่มโรค (ICD-10)</th><th>จำหน่าย (ราย)</th><th>กลับซ้ำ 28 วัน</th><th>กลับซ้ำ 90 วัน</th><th>กลับซ้ำ 180 วัน</th></tr>";
    GROUPS.forEach(function(g){
      var a = agg(rowsForGroup(g,state.year), 3);
      if(a.d===0) return;
      var r28=agg(rowsForGroup(g,state.year),3), r90=agg(rowsForGroup(g,state.year),4), r180=agg(rowsForGroup(g,state.year),5);
      h += "<tr><td class='g'>"+g+"</td><td>"+a.d+"</td>";
      [[r28,3],[r90,4],[r180,5]].forEach(function(p){
        h += "<td><span class='chip' style='background:"+colorFor(p[0].rate,p[1])+"'>"+fmt(p[0].rate)+"</span><div class='n'>"+p[0].n+"/"+p[0].d+"</div></td>";
      });
      h += "</tr>";
    });
    var t3=agg(rowsFor(state.year),3), t4=agg(rowsFor(state.year),4), t5=agg(rowsFor(state.year),5);
    h += "<tr class='tot'><td class='g'>รวมทุกกลุ่มโรค</td><td>"+t3.d+"</td><td>"+fmt(t3.rate)+"</td><td>"+fmt(t4.rate)+"</td><td>"+fmt(t5.rate)+"</td></tr>";
  }
  document.getElementById("tbl").innerHTML = h;
}

/* ---------- กราฟแท่ง ---------- */
function renderChart(){
  var mi = MI[state.metric];
  var rows = rowsFor(state.year);
  var data = GROUPS.map(function(g){ var a=agg(rowsForGroup(g,state.year),mi); return {g:g, rate:a.rate, n:a.n, d:a.d}; })
    .filter(function(x){ return x.d>0; }).sort(function(a,b){ return b.rate-a.rate; });
  var max = Math.max.apply(null, data.map(function(x){ return x.rate; }).concat([1]));
  document.getElementById("chartHint").innerHTML = "ตัวชี้วัด: <b>กลับมารักษาซ้ำภายใน "+state.metric.slice(1)+" วัน</b> · ช่วง: <b>"+(state.year==="all"?"ทุกปี (พ.ศ. 2565–2569)":"ปีงบประมาณ พ.ศ. "+be(state.year))+"</b> · เรียงจากมากไปน้อย";
  document.getElementById("chart").innerHTML = data.map(function(x){
    var w = Math.max(2, x.rate/max*100);
    return "<div class='row'><div class='lbl'>"+x.g+"</div><div class='bars'><div class='b' style='width:"+w+"%;background:"+COLOR[x.g]+"'><span>"+fmt(x.rate)+"%</span></div><div class='n' style='text-align:left'>กลับซ้ำ "+x.n+" จาก "+x.d+" ราย</div></div></div>";
  }).join("");
  document.getElementById("legend").innerHTML = GROUPS.map(function(g){ return "<span><i class='dot' style='background:"+COLOR[g]+"'></i>"+g+"</span>"; }).join("");
}

/* ---------- แนวโน้ม ---------- */
function renderTrend(){
  var mi = MI[state.metric];
  var svg = document.getElementById("trend");
  svg.setAttribute("preserveAspectRatio","xMidYMid meet");
  var W=900,H=280,P={l:52,r:16,t:18,b:34};
  var series = GROUPS.map(function(g){
    return {g:g, pts: YEARS.map(function(y){ var a=agg(rowsForGroup(g,y),mi); return {y:y, v:a.rate, d:a.d}; })};
  }).filter(function(s){ return s.pts.some(function(p){ return p.d>0; }); });
  var max = 1;
  series.forEach(function(s){ s.pts.forEach(function(p){ if(p.d>0 && p.v>max) max=p.v; }); });
  max = Math.ceil(max/5)*5; if(max<5) max=5;
  var x = function(i){ return P.l + i*(W-P.l-P.r)/(YEARS.length-1); };
  var y = function(v){ return H-P.b - v/max*(H-P.t-P.b); };
  var s = "";
  for(var t=0;t<=max;t+=max/5){ s += "<line x1='"+P.l+"' y1='"+y(t)+"' x2='"+(W-P.r)+"' y2='"+y(t)+"' stroke='#e6ecf4'/><text x='"+(P.l-8)+"' y='"+(y(t)+4)+"' font-size='11' fill='#7b8ba1' text-anchor='end'>"+t+"%</text>"; }
  YEARS.forEach(function(yr,i){ s += "<text x='"+x(i)+"' y='"+(H-12)+"' font-size='12' fill='#3d5474' text-anchor='middle'>พ.ศ. "+be(yr)+"</text>"; });
  series.forEach(function(se){
    var pts = se.pts.filter(function(p){ return p.d>0; });
    if(pts.length>1){
      s += "<polyline fill='none' stroke='"+COLOR[se.g]+"' stroke-width='2.5' points='"+pts.map(function(p){ return x(YEARS.indexOf(p.y))+","+y(p.v); }).join(" ")+"'/>";
    }
    pts.forEach(function(p){ s += "<circle cx='"+x(YEARS.indexOf(p.y))+"' cy='"+y(p.v)+"' r='4' fill='"+COLOR[se.g]+"'/><text x='"+x(YEARS.indexOf(p.y))+"' y='"+(y(p.v)-9)+"' font-size='10.5' font-weight='700' fill='"+COLOR[se.g]+"' text-anchor='middle'>"+fmt(p.v)+"</text>"; });
  });
  if(mi===3){ s += "<line x1='"+P.l+"' y1='"+y(TARGET28)+"' x2='"+(W-P.r)+"' y2='"+y(TARGET28)+"' stroke='#12855a' stroke-dasharray='6 4'/><text x='"+(W-P.r)+"' y='"+(y(TARGET28)-5)+"' font-size='11' fill='#12855a' text-anchor='end'>เป้าหมาย &lt; 2%</text>"; }
  svg.innerHTML = s;
  document.getElementById("legend2").innerHTML = series.map(function(se){ return "<span><i class='dot' style='background:"+COLOR[se.g]+"'></i>"+se.g+"</span>"; }).join("");
}

/* ---------- สรุปผู้บริหาร ---------- */
function renderInsight(){
  var mi = MI[state.metric];
  var rows = rowsFor(state.year);
  var t = agg(rows, mi);
  var ranked = GROUPS.map(function(g){ var a=agg(rowsForGroup(g,state.year),mi); return {g:g, rate:a.rate, n:a.n, d:a.d}; })
    .filter(function(x){ return x.d>0; }).sort(function(a,b){ return b.rate-a.rate; });
  var top3 = ranked.slice(0,3);
  var t28 = agg(rows,3);
  var lastY = YEARS[YEARS.length-1], prevY = YEARS[YEARS.length-2];
  var a1 = agg(rowsFor(lastY), mi), a0 = agg(rowsFor(prevY), mi);
  var dir = a1.rate > a0.rate ? "เพิ่มขึ้น" : (a1.rate < a0.rate ? "ลดลง" : "ทรงตัว");
  var share = t28.d>0 ? (top3.reduce(function(s,x){return s+x.n;},0)*100/t28.n) : 0;
  var html = "";
  html += "<p><b>ภาพรวม:</b> ช่วง"+(state.year==="all"?"ปีงบประมาณ พ.ศ. 2565–2569":"ปีงบประมาณ พ.ศ. "+be(state.year))+" มีผู้ป่วยในที่จำหน่ายด้วยการวินิจฉัยกลุ่มจิตเวช "+t28.d.toLocaleString()+" ราย อัตรากลับมารักษาซ้ำภายใน "+state.metric.slice(1)+" วัน เท่ากับ <b>"+fmt(t.rate)+"%</b> ("+t.n+" ราย) "+(mi===3?(t.rate<TARGET28?"ซึ่ง<b>ผ่านเกณฑ์</b>เป้าหมาย &lt; ร้อยละ 2":"ซึ่ง<b>สูงกว่าเกณฑ์</b>เป้าหมาย &lt; ร้อยละ 2"):"")+"</p>";
  html += "<p><b>กลุ่มที่ต้องเฝ้าระวัง (กลับซ้ำสูงสุด 3 อันดับ):</b> "+top3.map(function(x,i){ return (i+1)+") "+x.g+" "+fmt(x.rate)+"% ("+x.n+"/"+x.d+" ราย)"; }).join(" · ")+" — คิดเป็น <b>"+fmt(share)+"%</b> ของการกลับซ้ำ 28 วันทั้งหมด จึงควรเป็นกลุ่มเป้าหมายหลักของการวางระบบติดตามหลังจำหน่าย</p>";
  html += "<p><b>แนวโน้ม:</b> เมื่อเทียบปีล่าสุด (พ.ศ. "+be(lastY)+" = "+fmt(a1.rate)+"%) กับปีก่อนหน้า (พ.ศ. "+be(prevY)+" = "+fmt(a0.rate)+"%) อัตราโดยรวม<b>"+dir+"</b> "+(dir==="เพิ่มขึ้น"?"ควรทบทวนกระบวนการ discharge planning และการนัดติดตาม":"สะท้อนผลดีของระบบติดตามหลังจำหน่าย ควรคงมาตรการไว้")+"</p>";
  html += "<p><b>ข้อเสนอเชิงบริหาร:</b> (1) ตั้งเป้าลดอัตรากลับซ้ำ 28 วันของกลุ่มเสี่ยงสูงให้ต่ำกว่าร้อยละ 2 (2) จัดระบบโทรติดตามภายใน 7 วัน + นัดคลินิกจิตเวชภายใน 14 วัน สำหรับผู้ป่วยกลุ่มเสี่ยง (3) ติดตามตัวชี้วัด 90/180 วันควบคู่ เพื่อดูผลของ Retention/Remission (4) ทบทวนปัจจัยเสี่ยงรายบุคคลของผู้ป่วยที่กลับซ้ำซ้ำ ๆ</p>";
  html += "<p class='n'>หมายเหตุ: ปี พ.ศ. 2569 เป็นข้อมูลไม่เต็มปี (censoring) ทำให้ค่าอัตรา 90 และ 180 วันต่ำกว่าความเป็นจริง</p>";
  document.getElementById("insight").innerHTML = html;
}

function renderAll(){ renderKpi(); renderTable(); renderChart(); renderTrend(); renderInsight(); }

/* ---------- ตัวกรอง / ส่งออก ---------- */
(function init(){
  var sel = document.getElementById("fYear");
  YEARS.forEach(function(y){ var o=document.createElement("option"); o.value=y; o.textContent="ปีงบประมาณ พ.ศ. "+be(y); sel.appendChild(o); });
  sel.value = "all";
  sel.onchange = function(){ state.year = this.value==="all" ? "all" : parseInt(this.value,10); renderAll(); };
  document.getElementById("fMetric").onchange = function(){ state.metric = this.value; renderAll(); };
  document.getElementById("btnReset").onclick = function(){ state={year:"all",metric:"r28"}; sel.value="all"; document.getElementById("fMetric").value="r28"; renderAll(); };
  document.getElementById("btnCsv").onclick = function(){
    var mi = MI[state.metric];
    var lines = ["กลุ่มโรค,ปี(พ.ศ.),จำหน่าย(ราย),กลับซ้ำ(ราย),อัตรา(%)"];
    GROUPS.forEach(function(g){ YEARS.forEach(function(y){
      var a = agg(rowsForGroup(g,y), mi);
      if(a.d>0) lines.push('"'+g+'",'+be(y)+","+a.d+","+a.n+","+fmt(a.rate));
    });});
    var all = agg(rowsFor("all"), mi);
    lines.push('"รวมทุกกลุ่มโรค",ทุกปี,'+all.d+","+all.n+","+fmt(all.rate));
    var blob = new Blob(["\\ufeff"+lines.join("\\n")], {type:"text/csv;charset=utf-8"});
    var a = document.createElement("a"); a.href = URL.createObjectURL(blob);
    a.download = "readmission_psych_"+state.metric+"_"+state.year+".csv"; a.click();
  };
  document.getElementById("tagPeriod").textContent = "ข้อมูลปี พ.ศ. 2565–2569 (ค.ศ. 2022–2026)";
  renderAll();
})();
</script>
</body>
</html>
<script>(function(){function m(){var d=document,b=d.body,e=d.documentElement;var fb=Math.max(e.scrollHeight,b?b.scrollHeight:0);if(!b||!b.children||!b.children.length)return fb;var y=window.scrollY||e.scrollTop||0,max=0,i,r;for(i=0;i<b.children.length;i++){r=b.children[i].getBoundingClientRect();if(r.bottom+y>max)max=r.bottom+y;}var cs=window.getComputedStyle?window.getComputedStyle(b):null;if(cs){max+=(parseFloat(cs.paddingBottom)||0)+(parseFloat(cs.marginBottom)||0);}return max>0?Math.ceil(max):fb;}var mutated=false;if('MutationObserver' in window){new MutationObserver(function(){mutated=true;}).observe(document.documentElement,{childList:true,subtree:true,characterData:true,attributes:true});}function h(){var c=mutated;mutated=false;parent.postMessage({type:'vfs:height',surfaceId:"app-codemode/59ba3e19-c4ef-4fe2-ae45-08d527c5a3bf",height:m(),contentChanged:c},'*');}window.addEventListener('load',h);if('ResizeObserver' in window){new ResizeObserver(h).observe(document.documentElement);}window.addEventListener('pointerdown',function(){try{window.focus();}catch(e){}},true);h();})();</script>
