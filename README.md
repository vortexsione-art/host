<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>HOST - Inbox Pro Fix + Soft Delete</title>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&family=Poppins:wght@400;600&display=swap" rel="stylesheet">
<style>
*{box-sizing:border-box}
body{margin:0;min-height:100vh;background:#0a0a0a;display:flex;justify-content:center;align-items:center;padding:0;font-family:'JetBrains Mono',monospace}
.app{width:100%;max-width:850px;height:100vh;background:#111;display:flex;overflow:hidden}
@media(min-width:700px){body{padding:15px}.app{height:85vh;border-radius:16px;border:1px solid #222}}
.sidebar{width:38%;min-width:125px;max-width:200px;background:#0f0f0f;border-right:1px solid #222;display:flex;flex-direction:column}
.s-head{padding:12px 10px;background:#111;border-bottom:1px solid #222}
.s-head b{color:#00ff88;font-size:13px;display:block;white-space:nowrap}
#contacts{flex:1;overflow-y:auto}
.contact{padding:12px 10px;border-bottom:1px solid #1a1a1a;cursor:pointer;display:flex;align-items:center;gap:6px}
.contact.active{background:#002a1a;border-left:3px solid #00ff88}
.contact .name{color:#00ff88;font-size:12px;font-weight:700;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.contact .last{color:#666;font-size:10px;margin-top:3px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;font-family:'Poppins',sans-serif}
.main{flex:1;display:flex;flex-direction:column;background:#0a0a0a;min-width:0}
.m-head{padding:10px 12px;border-bottom:1px solid #222;background:#0f0f0f;display:flex;align-items:center;justify-content:space-between;gap:8px;min-height:58px}
.m-head .left{flex:1;min-width:0}
.m-head b{color:#00ff88;font-size:14px;display:block;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.m-head #chatInfo{font-size:10px;color:#555}
.m-head button#btnDelContact{flex-shrink:0;background:rgba(255,80,80,0.12);border:1px solid rgba(255,80,80,0.25);color:#ff6b6b;font-size:10px;padding:6px 12px;border-radius:20px;cursor:pointer;white-space:nowrap}
#chat{flex:1;overflow-y:auto;padding:20px 18px;display:flex;flex-direction:column;gap:12px;background:#0a0a0a;background-image:radial-gradient(#1e1e1e 1px, transparent 1px);background-size:22px 22px;align-items:center}
#chat > *{width:100%;max-width:360px}
.bubble{max-width:85%;padding:10px 14px;border-radius:12px;font-size:13px;border:1px solid #222;font-family:'Poppins',sans-serif;position:relative;cursor:pointer;word-wrap:break-word}
.me{align-self:flex-end;background:#002a1a;border-color:#00ff88;color:#d1ffe8;border-radius:12px 12px 3px 12px;margin-left:auto}
.you{align-self:flex-start;background:#1e1e1e;color:#ddd;border-radius:12px 12px 12px 3px;margin-right:auto}
.bubble.deleted{opacity:.6;font-style:italic;background:#1a1a1a;border-style:dashed;border-color:#333;color:#888}
.system{align-self:center;background:rgba(250,204,21,0.15);border:1px solid rgba(250,204,21,0.3);color:#facc15;font-size:11px;padding:6px 12px;border-radius:20px;text-align:center}
.bubble .trash{position:absolute;top:-8px;right:-8px;width:20px;height:20px;background:#ff1a1a;color:#fff;border-radius:50%;display:none;align-items:center;justify-content:center;font-size:11px}
.bubble:hover .trash{display:flex}
.meta{font-size:9px;opacity:.5;margin-top:6px;text-align:right;font-family:'JetBrains Mono',monospace}
.footer{padding:10px 12px;background:#0f0f0f;border-top:1px solid #222;display:flex;align-items:center;gap:10px}
.footer input{flex:1;min-width:0;background:#1e1e1e;border:1px solid #333;border-radius:24px;padding:12px 16px;color:#fff;outline:none;font-size:13px}
.footer button#btnSend{flex-shrink:0;width:44px;height:44px;border-radius:50%;background:#00ff88;color:#000;border:none;font-weight:700;font-size:16px;cursor:pointer;display:flex;align-items:center;justify-content:center}
#overlay{position:fixed;inset:0;background:rgba(0,0,0,0.7);backdrop-filter:blur(8px);display:none;align-items:center;justify-content:center;z-index:999}
#overlay.show{display:flex}
.modal{background:#1a1a1a;border:1px solid #333;border-radius:16px;padding:24px;width:90%;max-width:320px;text-align:center;animation:slideUp .25s}
@keyframes slideUp{from{transform:translateY(20px);opacity:0}to{transform:translateY(0);opacity:1}}
.modal h3{margin:8px 0 6px;font-size:15px;color:#fff;font-family:'Poppins',sans-serif}
.modal p{margin:0 0 18px;font-size:12px;color:#888}
.modal .btns{display:flex;gap:10px}
.modal button{flex:1;padding:12px;border:none;border-radius:10px;font-weight:700;font-size:12px;cursor:pointer}
.btn-cancel{background:#2a2a2a;color:#aaa}.btn-delete{background:#ff1a1a;color:#fff}.btn-delete-orange{background:#ff8c00;color:#000}
.toast{position:fixed;bottom:20px;left:50%;transform:translateX(-50%);background:#00ff88;color:#000;padding:10px 18px;border-radius:20px;font-size:11px;font-weight:700;display:none;z-index:1000}
.toast.show{display:block}
</style>
</head>
<body>
<div class="app">
  <div class="sidebar">
    <div class="s-head"><b>>_ HOST INBOX</b><div style="color:#555;font-size:10px"><span id="total">0</span> kontak</div></div>
    <div id="contacts"></div>
  </div>
  <div class="main">
    <div class="m-head"><div class="left"><b id="chatName">Pilih chat</b><div id="chatInfo">-</div></div><button id="btnDelContact" onclick="openDeleteContactModal()" style="display:none">hapus kontak</button></div>
    <div id="chat"><div style="color:#333;text-align:center;margin-top:80px;font-size:12px;width:100%">👈 Pilih kontak di kiri<br>Kalau user hapus, disini jadi 🚫</div></div>
    <div class="footer"><input id="pesan" placeholder="Balas..."><button id="btnSend" onclick="balas()">➤</button></div>
  </div>
</div>

<div id="overlay" onclick="closeModal()">
  <div class="modal" id="modalPesan" onclick="event.stopPropagation()"><div style="font-size:28px">🗑️</div><h3>Hapus Pesan?</h3><p id="modalPesanText">Pesan bakal hilang di host aja. Kalau user sudah hapus juga, hilang permanen.</p><div class="btns"><button class="btn-cancel" onclick="closeModal()">Batal</button><button class="btn-delete" onclick="confirmDeletePesan()">Hapus di Host</button></div></div>
  <div class="modal" id="modalKontak" onclick="event.stopPropagation()" style="display:none"><div style="font-size:28px">👤</div><h3>Hapus Kontak?</h3><p>Semua chat <b id="kontakNameDel"></b> hilang permanen</p><div class="btns"><button class="btn-cancel" onclick="closeModal()">Batal</button><button class="btn-delete-orange" onclick="confirmDeleteKontak()">Hapus Kontak</button></div></div>
</div>
<div id="toast" class="toast"></div>

<script>
let active=null, deleteIndex=null, deleteKontakName=null;
function showToast(m){let t=document.getElementById('toast');t.innerText=m;t.classList.add('show');setTimeout(()=>t.classList.remove('show'),2000);}
function loadContacts(){
  let list=JSON.parse(localStorage.getItem('semuaPesan')||'[]');
  // filter yang belum dihapus host
  let names=[...new Set(list.filter(x=>x.from==='pengirim' && !(x.deletedBy&&x.deletedBy.includes('host'))).map(x=>x.nama))];
  // juga ambil yang ada system rename
  let sysNames=[...new Set(list.filter(x=>x.from==='system').map(x=>x.newNama))];
  let allNames=[...new Set([...names, ...sysNames])];
  allNames.sort((a,b)=>{
    let la=[...list].reverse().find(m=>m.nama===a||m.to===a||m.newNama===a);
    let lb=[...list].reverse().find(m=>m.nama===b||m.to===b||m.newNama===b);
    return list.indexOf(lb)-list.indexOf(la);
  });
  document.getElementById('total').innerText=allNames.length;
  let box=document.getElementById('contacts');box.innerHTML='';
  if(!allNames.length){box.innerHTML='<div style="color:#333;padding:20px;text-align:center;font-size:11px">Belum ada chat</div>';return;}
  allNames.forEach(n=>{
    let msgs=list.filter(m=>(m.from==='pengirim'&&m.nama===n)||(m.from==='penerima'&&m.to===n)||(m.from==='system'&&m.newNama===n));
    let last=msgs[msgs.length-1];
    let d=document.createElement('div');d.className='contact'+(active===n?' active':'');
    d.innerHTML=`<div style="flex:1;min-width:0"><div class="name">${n}</div><div class="last">${last?(last.from==='system'?last.pesan:last.pesan.substring(0,22)):''}</div></div>`;
    d.onclick=()=>{active=n;document.getElementById('btnDelContact').style.display='block';loadContacts();tampilkanChat();};
    box.appendChild(d);
  });
}

function tampilkanChat(){
  if(!active) return;
  document.getElementById('chatName').innerText=active;
  document.getElementById('btnDelContact').style.display='block';
  let list=JSON.parse(localStorage.getItem('semuaPesan')||'[]');
  let withIndex=list.map((m,i)=>({...m,realIndex:i})).filter(m=>{
    if(m.from==='pengirim'&&m.nama===active) return true;
    if(m.from==='penerima'&&m.to===active) return true;
    if(m.from==='system'&&(m.oldNama===active||m.newNama===active)) return true;
    return false;
  });
  // filter yang sudah dihapus host -> jangan tampil di host
  withIndex=withIndex.filter(m=> !(m.deletedBy&&m.deletedBy.includes('host')));

  document.getElementById('chatInfo').innerText=withIndex.length+' pesan';
  let box=document.getElementById('chat');box.innerHTML='';
  if(!withIndex.length){box.innerHTML='<div style="color:#333;text-align:center;margin-top:40px;width:100%">Belum ada pesan</div>';return;}
  withIndex.forEach(item=>{
    let isMe=item.from==='penerima';
    if(item.from==='system'){
      box.innerHTML+=`<div style="width:100%;display:flex;justify-content:center"><div class="system">🔄 ${item.pesan}<div style="font-size:9px;opacity:.6">${item.jam}</div></div></div>`;
      return;
    }
    // CEK APAKAH USER SUDAH HAPUS
    let isDeletedByUser=item.deletedBy && item.deletedBy.includes(active) && !isMe;
    let text=isDeletedByUser?`🚫 pesan ini dihapus oleh ${active}`:item.pesan;
    let cls=isMe?'me':(isDeletedByUser?'you deleted':'you');
    let extraStyle=isDeletedByUser?'opacity:0.6;font-style:italic;border-style:dashed':''; 
    
    box.innerHTML+=`<div style="width:100%;max-width:360px;display:flex;${isMe?'justify-content:flex-end':'justify-content:flex-start'}">
      <div class="bubble ${cls}" style="${extraStyle}" onclick="openDeletePesanModal(${item.realIndex})">${text}<div class="trash">✕</div><div class="meta">${isMe?'host':'↳ '+item.nama} • ${item.jam}</div></div>
    </div>`;
  });
  box.scrollTop=box.scrollHeight;
}

function openDeletePesanModal(i){deleteIndex=i;document.getElementById('overlay').classList.add('show');document.getElementById('modalPesan').style.display='block';document.getElementById('modalKontak').style.display='none';}
function openDeleteContactModal(n){let t=n||active;if(!t)return;deleteKontakName=t;document.getElementById('kontakNameDel').innerText=t;document.getElementById('overlay').classList.add('show');document.getElementById('modalPesan').style.display='none';document.getElementById('modalKontak').style.display='block';}
function closeModal(){document.getElementById('overlay').classList.remove('show');}
function confirmDeletePesan(){
  let list=JSON.parse(localStorage.getItem('semuaPesan')||'[]');
  if(!list[deleteIndex].deletedBy) list[deleteIndex].deletedBy=[];
  if(!list[deleteIndex].deletedBy.includes('host')) list[deleteIndex].deletedBy.push('host');
  // kalau user juga sudah hapus, hapus permanen
  if(list[deleteIndex].deletedBy.includes(active) || list[deleteIndex].from==='penerima'){
    // untuk pesan host, langsung hapus permanen
    if(list[deleteIndex].from==='penerima' || list[deleteIndex].deletedBy.length>=2){
      list.splice(deleteIndex,1);
    }
  }
  localStorage.setItem('semuaPesan',JSON.stringify(list));
  closeModal();showToast('Pesan dihapus di host 🗑️');loadContacts();tampilkanChat();
}
function confirmDeleteKontak(){
  let list=JSON.parse(localStorage.getItem('semuaPesan')||'[]');
  let sisa=list.filter(m=>!((m.from==='pengirim'&&m.nama===deleteKontakName)||(m.from==='penerima'&&m.to===deleteKontakName)||(m.from==='system'&&(m.oldNama===deleteKontakName||m.newNama===deleteKontakName))));
  localStorage.setItem('semuaPesan',JSON.stringify(sisa));
  closeModal();showToast('Kontak '+deleteKontakName+' dihapus 👋');
  if(active===deleteKontakName){active=null;document.getElementById('chat').innerHTML='<div style="color:#333;text-align:center;margin-top:80px;width:100%">Kontak dihapus</div>';document.getElementById('chatName').innerText='Pilih chat';document.getElementById('btnDelContact').style.display='none';}
  loadContacts();
}
function balas(){if(!active){showToast('Pilih kontak dulu');return;}let p=document.getElementById('pesan').value.trim();if(!p)return;let d={nama:'host',pesan:p,jam:new Date().toLocaleTimeString('id-ID',{hour:'2-digit',minute:'2-digit'}),from:'penerima',to:active,deletedBy:[]};let l=JSON.parse(localStorage.getItem('semuaPesan')||'[]');l.push(d);localStorage.setItem('semuaPesan',JSON.stringify(l));document.getElementById('pesan').value='';tampilkanChat();loadContacts();}
document.getElementById('pesan').addEventListener('keydown',e=>{if(e.key==='Enter') balas();});
loadContacts();setInterval(()=>{loadContacts();if(active) tampilkanChat();},1000);
</script>
</body>
</html>
