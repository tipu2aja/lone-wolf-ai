
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#090a0d">
<title>LONE WOLF AI</title>
<style>
body{margin:0;padding:16px;background:#090a0d;color:#eee;font-family:Arial}
header{text-align:center;padding:20px;color:#d7ad54}
header .wolf{font-size:42px}
h1{letter-spacing:3px}
nav{display:grid;grid-template-columns:repeat(3,1fr);gap:8px}
button{padding:13px;background:#191b20;color:#d7ad54;border:1px solid #51432a;border-radius:10px;font-weight:bold}
button.active{background:#51432a}
.panel{background:#191b20;padding:14px;margin-top:15px;border-radius:12px}
h3{color:#d7ad54}
textarea,input{width:100%;box-sizing:border-box;padding:12px;margin:7px 0;background:#090a0d;color:white;border:1px solid #51432a;border-radius:9px}
textarea{min-height:75px}
#chat{height:280px;overflow:auto}
.msg{padding:10px;margin:8px 0;border-radius:9px;white-space:pre-wrap;line-height:1.5}
.user{background:#51432a;text-align:right}
.bot{background:#292b30}
.hidden{display:none}
.small{font-size:12px;color:#aaa}
</style>
</head>
<body>

<header>
<div class="wolf">🐺</div>
<h1>LONE WOLF AI</h1>
<p class="small">PRIVATE TESTER · VERSION 0.2</p>
</header>

<nav>
<button onclick="tab('assistant')">AI ASSISTANT</button>
<button onclick="tab('notes')">CATATAN</button>
<button onclick="tab('settings')">PENGATURAN</button>
</nav>

<section id="assistant" class="panel">
<h3>✦ AI Assistant</h3>
<div id="chat">
<div class="msg bot">Halo! Selamat datang di LONE WOLF AI.</div>
</div>
<textarea id="question" placeholder="Tulis pertanyaan di sini..."></textarea>
<button id="send" onclick="sendMessage()" style="width:100%">KIRIM PESAN</button>
<p id="status" class="small">Status: belum terhubung.</p>
</section>

<section id="notes" class="panel hidden">
<h3>CATATAN PRIBADI</h3>
<textarea id="note" placeholder="Tulis catatan..."></textarea>
<button onclick="saveNote()" style="width:100%">SIMPAN CATATAN</button>
<div id="notesList"></div>
</section>

<section id="settings" class="panel hidden">
<h3>PENGATURAN AI</h3>
<p class="small">Pengaturan koneksi akan disimpan di browser HP ini.</p>
<label class="small">Alamat Worker AI</label>
<input id="endpoint" placeholder="https://alamat-worker.workers.dev/chat">
<label class="small">Kode akses</label>
<input id="token" type="password" placeholder="Masukkan kode akses">
<button onclick="saveSettings()" style="width:100%">SIMPAN PENGATURAN</button>
<p class="small">Koneksi AI memerlukan backend Cloudflare Worker. Jangan memasukkan API key pribadi di sini.</p>
</section>

<script>
function tab(id){
document.querySelectorAll('section').forEach(s=>s.classList.add('hidden'));
document.getElementById(id).classList.remove('hidden');
}

let config=JSON.parse(localStorage.getItem('lw_config')||'{}');
document.getElementById('endpoint').value=config.endpoint||'';
document.getElementById('token').value=config.token||'';

let history=JSON.parse(localStorage.getItem('lw_chat')||'[]');

function showMessage(role,text){
let d=document.createElement('div');
d.className='msg '+(role==='user'?'user':'bot');
d.textContent=text;
document.getElementById('chat').appendChild(d);
document.getElementById('chat').scrollTop=999999;
}

history.forEach(m=>showMessage(m.role,m.content));

function saveSettings(){
config={
endpoint:document.getElementById('endpoint').value.trim(),
token:document.getElementById('token').value.trim()
};
localStorage.setItem('lw_config',JSON.stringify(config));
alert('Pengaturan tersimpan.');
}

async function sendMessage(){
let q=document.getElementById('question').value.trim();
if(!q)return;

config=JSON.parse(localStorage.getItem('lw_config')||'{}');

if(!config.endpoint||!config.token){
alert('Atur koneksi AI terlebih dahulu.');
tab('settings');
return;
}

document.getElementById('question').value='';
showMessage('user',q);
history.push({role:'user',content:q});
document.getElementById('send').disabled=true;
document.getElementById('status').textContent='AI sedang berpikir...';

try{
let response=await fetch(config.endpoint,{
method:'POST',
headers:{
'Content-Type':'application/json',
'X-App-Token':config.token
},
body:JSON.stringify({messages:history.slice(-12)})
});

let data=await response.json();
if(!response.ok)throw new Error(data.error||'Koneksi gagal');

let answer=data.response||'Belum ada jawaban.';
history.push({role:'assistant',content:answer});
showMessage('assistant',answer);
localStorage.setItem('lw_chat',JSON.stringify(history));
document.getElementById('status').textContent='Jawaban diterima.';
}catch(e){
history.pop();
document.getElementById('status').textContent='Gagal: '+e.message;
}finally{
document.getElementById('send').disabled=false;
}
}

function saveNote(){
let n=document.getElementById('note').value.trim();
if(!n)return;
let a=JSON.parse(localStorage.getItem('lw_notes')||'[]');
a.unshift(n);
localStorage.setItem('lw_notes',JSON.stringify(a));
document.getElementById('note').value='';
renderNotes();
}

function renderNotes(){
let a=JSON.parse(localStorage.getItem('lw_notes')||'[]');
document.getElementById('notesList').innerHTML='';
a.forEach(n=>{
let d=document.createElement('div');
d.className='msg bot';
d.textContent=n;
document.getElementById('notesList').appendChild(d);
});
}
renderNotes();
</script>
</body>
</html>
