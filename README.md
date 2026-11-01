<!ndex.html>
<html langhtml<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#090a0d">
<title>LONE WOLF AI</title>
<style>
body{margin:0;background:#090a0d;color:#eee;font-family:Arial;padding:20px}
header{text-align:center;color:#d7ad54;padding:25px 0}
h1{letter-spacing:3px}
p{color:#aaa;line-height:1.5}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:12px}
button{background:#191b20;color:#d7ad54;border:1px solid #51432a;border-radius:12px;padding:20px 10px;font-size:15px}
textarea{width:100%;box-sizing:border-box;background:#191b20;color:white;border:1px solid #51432a;border-radius:10px;padding:12px;margin-top:10px}
#hasil{white-space:pre-wrap;color:#d7ad54;margin-top:15px}
</style>
</head>
<body>
<header>
<div style="font-size:40px">🐺</div>
<h1>LONE WOLF AI</h1>
<p>PRIVATE TESTER · VERSION 0.1</p>
</header>

<h3>Selamat datang, Lone Wolf.</h3>
<p>Ruang kerja digital pribadi untuk kreativitas dan kendaraan.</p>

<div class="grid">
<button onclick="pilih('AI ASSISTANT')">✦<br>AI Assistant</button>
<button onclick="pilih('AI IMAGE')">▧<br>AI Image</button>
<button onclick="pilih('AI DESIGN')">◇<br>AI Design</button>
<button onclick="pilih('AI VIDEO')">▶<br>AI Video</button>
<button onclick="pilih('AUTOMOTIVE')">⚙<br>Automotive</button>
<button onclick="pilih('RIWAYAT')">◷<br>Riwayat</button>
</div>

<h3 id="judul">Pilih modul</h3>
<textarea id="catatan" rows="5" placeholder="Tuliskan ide atau catatan di sini..."></textarea>
<button style="width:100%;margin-top:10px" onclick="simpan()">SIMPAN CATATAN</button>
<div id="hasil"></div>

<script>
let modul="Beranda";
function pilih(n){
modul=n;
document.getElementById("judul").innerText=n;
document.getElementById("hasil").innerText=
n==="AI IMAGE"?"Versi berikutnya akan menambahkan alat gambar.":
n==="AI VIDEO"?"Versi berikutnya akan menambahkan alat video.":
n==="AI ASSISTANT"?"Asisten AI belum terhubung. Saat ini kamu bisa menyimpan catatan.":
"Modul "+n+" siap diuji.";
}
function simpan(){
let t=document.getElementById("catatan").value.trim();
if(!t){alert("Tulis catatan dahulu.");return;}
let a=JSON.parse(localStorage.getItem("lw_notes")||"[]");
a.unshift({modul:modul,isi:t,waktu:new Date().toLocaleString("id-ID")});
localStorage.setItem("lw_notes",JSON.stringify(a));
document.getElementById("hasil").innerText="Catatan berhasil disimpan!";
document.getElementById("catatan").value="";
}
</script>
</body>
</html>
