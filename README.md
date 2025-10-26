<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Kasir Online Makanan Elegan</title>
<meta name="description" content="Kasir Online Makanan Elegan - Aplikasi web untuk pemesanan, pembayaran, dan pencetakan struk makanan.">
<meta name="keywords" content="kasir online, pemesanan makanan, kasir restoran, akbar kusuma, sistem kasir web">
<meta name="author" content="Akbar Kusuma Hermawan">

<style>
:root{
  --bg:#0f172a;
  --panel:#1e293b;
  --gold:#facc15;
  --text:#f8fafc;
  --muted:#cbd5e1;
}
*{box-sizing:border-box}
body{
  margin:0;
  font-family:"Poppins",sans-serif;
  background:var(--bg);
  color:var(--text);
  height:100vh;
  overflow:hidden;
}

/* ================= LOGIN PAGE ================== */
#loginPage{
  display:flex;
  align-items:center;
  justify-content:center;
  height:100%;
}
.login-box{
  background:var(--panel);
  padding:40px 50px;
  border-radius:16px;
  box-shadow:0 8px 24px rgba(0,0,0,0.6);
  text-align:center;
  width:340px;
}
.login-box h2{color:var(--gold)}
.login-box input{
  width:100%;padding:10px;margin:8px 0;
  border:none;border-radius:6px;
}
.login-box button{
  width:100%;padding:10px;background:var(--gold);
  color:#111;border:none;border-radius:6px;
  font-weight:600;cursor:pointer;
}
.login-box button:hover{background:#eab308}

/* ================= MAIN APP ================== */
#app{display:none;flex-direction:column;height:100%}

header{
  background:var(--panel);
  padding:16px;
  display:flex;
  justify-content:space-between;
  align-items:center;
  box-shadow:0 3px 10px rgba(0,0,0,0.3);
}
header h1{margin:0;color:var(--gold);font-size:22px}
header button{
  background:#ef4444;
  color:#fff;border:none;
  padding:8px 12px;border-radius:8px;
  cursor:pointer;
}
header button:hover{background:#dc2626}

main{
  display:grid;
  grid-template-columns:2fr 1fr;
  gap:20px;
  flex:1;
  padding:20px;
  overflow:auto;
}

.menu-grid{
  display:grid;
  grid-template-columns:repeat(auto-fill,minmax(200px,1fr));
  gap:18px;
}
.card{
  background:var(--panel);
  border-radius:14px;
  overflow:hidden;
  box-shadow:0 8px 18px rgba(0,0,0,0.5);
  text-align:center;
}
.card img{
  width:100%;height:140px;object-fit:cover;
}
.card h3{color:var(--gold);margin:8px 0 4px}
.card p{margin:0 0 10px;color:var(--muted)}
.card button{
  background:var(--gold);color:#111;
  border:none;padding:8px 12px;border-radius:8px;
  margin-bottom:12px;cursor:pointer;font-weight:600;
}
.card button:hover{background:#eab308}

.cart{
  background:var(--panel);
  border-radius:14px;
  padding:16px;
  box-shadow:0 8px 18px rgba(0,0,0,0.5);
}
.cart h2{margin:0 0 10px;color:var(--gold);font-size:18px}
table{width:100%;border-collapse:collapse}
th,td{padding:6px;font-size:14px;text-align:left}
th{color:var(--muted)}
td:last-child{text-align:right}
.btn-small{
  border:none;background:#334155;color:white;
  padding:4px 8px;border-radius:6px;cursor:pointer;
}
.btn-small:hover{background:#475569}

.tot-row{
  display:flex;justify-content:space-between;
  color:var(--gold);font-weight:700;margin-top:8px;
}
.actions{margin-top:10px;display:flex;gap:10px}
.actions button{
  flex:1;padding:10px;border:none;border-radius:8px;
  font-weight:600;cursor:pointer;
}
.clear{background:#ef4444;color:white}
.pay{background:#10b981;color:white}
.clear:hover{background:#dc2626}
.pay:hover{background:#059669}

/* receipt overlay */
#overlay{
  display:none;position:fixed;inset:0;
  background:rgba(0,0,0,0.7);
  align-items:center;justify-content:center;
}
.receipt{
  background:white;color:black;
  padding:16px;border-radius:10px;
  width:340px;max-width:90%;
}
.receipt h3{margin-top:0}
.receipt pre{white-space:pre-wrap;font-family:monospace}
.receipt button{
  border:none;padding:8px 10px;border-radius:8px;
  margin-right:8px;cursor:pointer;font-weight:600;
}
.print{background:#0ea5a6;color:white}
.close{background:#ef4444;color:white}
</style>
</head>

<body>
<!-- LOGIN PAGE -->
<section id="loginPage">
  <div class="login-box">
    <h2>Login Kasir</h2>
    <input type="text" id="user" placeholder="Username">
    <input type="password" id="pass" placeholder="Password">
    <button onclick="login()">Masuk</button>
  </div>
</section>

<!-- MAIN APP -->
<section id="app">
  <header>
    <h1>🍽️ Kasir Online Makanan Elegan</h1>
    <button onclick="logout()">Logout</button>
  </header>

  <main>
    <div>
      <h2 style="color:var(--gold);margin-top:0">Menu Makanan & Minuman</h2>
      <div class="menu-grid" id="menuGrid"></div>
    </div>

    <div class="cart">
      <h2>🧾 Keranjang</h2>
      <table id="cartTable">
        <thead>
          <tr><th>Item</th><th>Qty</th><th>Subtotal</th></tr>
        </thead>
        <tbody id="cartBody"><tr><td colspan="3" style="text-align:center;color:var(--muted)">Belum ada pesanan</td></tr></tbody>
      </table>

      <div class="tot-row">
        <span>Total</span><span>Rp <span id="totalAmount">0</span></span>
      </div>

      <div style="margin-top:10px">
        <label>Uang Diterima:</label>
        <input type="number" id="cashInput" placeholder="Masukkan uang pelanggan" style="width:100%;padding:8px;border-radius:8px;border:none;margin-top:5px">
      </div>

      <div class="actions">
        <button class="clear" onclick="clearAll()">Hapus Semua</button>
        <button class="pay" onclick="pay()">Bayar</button>
      </div>
    </div>
  </main>
</section>

<!-- RECEIPT -->
<div id="overlay">
  <div class="receipt">
    <h3>🧾 Struk Pembayaran</h3>
    <pre id="receiptText"></pre>
    <div style="text-align:right">
      <button class="print" onclick="printReceipt()">Cetak</button>
      <button class="close" onclick="closeReceipt()">Tutup</button>
    </div>
  </div>
</div>

<script>
/* ============ LOGIN SYSTEM ============ */
function login(){
  const u = document.getElementById("user").value;
  const p = document.getElementById("pass").value;
  if(u==="kasir" && p==="12345"){
    localStorage.setItem("kasirLogin","true");
    document.getElementById("loginPage").style.display="none";
    document.getElementById("app").style.display="flex";
  }else{
    alert("Username atau password salah!");
  }
}
function logout(){
  localStorage.removeItem("kasirLogin");
  location.reload();
}
window.onload=function(){
  if(localStorage.getItem("kasirLogin")==="true"){
    document.getElementById("loginPage").style.display="none";
    document.getElementById("app").style.display="flex";
  }
}

/* ============ MENU DATA ============ */
const MENU = [
  {id:"ngsp",name:"Nasi Goreng Spesial",price:25000,img:"https://i.ibb.co/9s3f8vP/nasi-goreng-spesial.jpg"},
  {id:"ngsf",name:"Nasi Goreng Seafood",price:30000,img:"https://i.ibb.co/THsnDk7/nasi-goreng-seafood.jpg"},
  {id:"mior",name:"Mie Ayam Original",price:20000,img:"https://i.ibb.co/VQdfy0D/mie-ayam-biasa.jpg"},
  {id:"mibs",name:"Mie Ayam Bakso",price:25000,img:"https://i.ibb.co/3NwkPPn/mie-ayam-bakso.jpg"},
  {id:"esteh",name:"Es Teh Manis",price:8000,img:"https://i.ibb.co/nRLdnRQ/es-teh-manis.jpg"},
  {id:"jus",name:"Jus Alpukat",price:15000,img:"https://i.ibb.co/bRpQyCQ/kopi.jpg"}
];
const menuGrid=document.getElementById("menuGrid");
MENU.forEach(item=>{
  const c=document.createElement("div");
  c.className="card";
  c.innerHTML=`<img src="${item.img}" alt="${item.name}">
  <h3>${item.name}</h3>
  <p>Rp ${item.price.toLocaleString()}</p>
  <button onclick="addToCart('${item.id}')">Tambah</button>`;
  menuGrid.appendChild(c);
});

/* ============ CART SYSTEM ============ */
let cart=[];
const cartBody=document.getElementById("cartBody");
const totalAmount=document.getElementById("totalAmount");
function addToCart(id){
  const p=MENU.find(x=>x.id===id);
  const f=cart.find(x=>x.id===id);
  if(f)f.qty++; else cart.push({...p,qty:1});
  renderCart();
}
function renderCart(){
  cartBody.innerHTML="";
  if(cart.length===0){
    cartBody.innerHTML='<tr><td colspan="3" style="text-align:center;color:var(--muted)">Belum ada pesanan</td></tr>';
    totalAmount.textContent="0";return;
  }
  let total=0;
  cart.forEach(it=>{
    const sub=it.price*it.qty;total+=sub;
    const tr=document.createElement("tr");
    tr.innerHTML=`<td>${it.name}</td>
    <td><button class="btn-small" onclick="changeQty('${it.id}',-1)">-</button> ${it.qty} <button class="btn-small" onclick="changeQty('${it.id}',1)">+</button></td>
    <td>Rp ${sub.toLocaleString()}</td>`;
    cartBody.appendChild(tr);
  });
  totalAmount.textContent=total.toLocaleString();
}
function changeQty(id,d){
  const f=cart.find(x=>x.id===id);
  if(!f)return;
  f.qty+=d;if(f.qty<=0)cart=cart.filter(x=>x.id!==id);
  renderCart();
}
function clearAll(){if(confirm("Hapus semua pesanan?")){cart=[];renderCart();}}

/* ============ PAYMENT & RECEIPT ============ */
function pay(){
  if(cart.length===0)return alert("Keranjang kosong!");
  const total=cart.reduce((s,i)=>s+i.price*i.qty,0);
  const cash=parseInt(document.getElementById("cashInput").value||0);
  if(isNaN(cash)||cash<total)return alert("Uang tidak cukup!");
  const kemb=cash-total;
  showReceipt(cart,total,cash,kemb);
  cart=[];renderCart();document.getElementById("cashInput").value="";
}
function showReceipt(items,total,cash,kemb){
  const now=new Date().toLocaleString("id-ID");
  let str=`WARUNG AKBAR\n--------------------------\n${now}\n\n`;
  items.forEach(i=>{
    str+=`${i.name} x${i.qty} = Rp ${(i.price*i.qty).toLocaleString()}\n`;
  });
  str+=`--------------------------\nTOTAL: Rp ${total.toLocaleString()}\nBAYAR: Rp ${cash.toLocaleString()}\nKEMBALI: Rp ${kemb.toLocaleString()}\n\nTerima kasih! 😊`;
  document.getElementById("receiptText").textContent=str;
  document.getElementById("overlay").style.display="flex";
}
function closeReceipt(){document.getElementById("overlay").style.display="none";}
function printReceipt(){
  const txt=document.getElementById("receiptText").textContent;
  const w=window.open("","_blank");
  w.document.write(`<pre>${txt}</pre>`);
  w.print();w.close();
  document.getElementById("overlay").style.display="none";
}
</script>
</body>
</html>
