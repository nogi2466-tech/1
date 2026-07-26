<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>tetsudo-site2</title>
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">

<style>
:root {
  --color-keio:#8e44ad;
  --color-jr:#27ae60;
  --color-ote:#e74c3c;
  --color-chika:#2980b9;
  --color-etc:#7f8c8d;
  --color-data:#f1c40f;
  --color-img:#d35400;
  --color-fav:#ff8800;
}

body {
  margin:0;
  background:#0f0f0f;
  color:#eee;
  font-family:system-ui;
  overflow-x:hidden;
}

body.light {
  background:#f5f5f5;
  color:#222;
}
body.light .card,
body.light .item,
body.light .modal {
  background:#fff;
  color:#222;
}
body.light .nav {
  background:#e0e0e0;
}
body.light .nav button {
  background:#ccc;
  color:#222;
}
body.light .nav button.active {
  background:#0099ff;
  color:#fff;
}

header {
  background:#0066cc;
  padding:14px;
  text-align:center;
  font-size:20px;
  font-weight:bold;
  color:#fff;
}

.nav {
  display:flex;
  gap:10px;
  padding:14px;
  background:#1a1a1a;
  justify-content:center;
  flex-wrap:wrap;
}

.nav button {
  padding:10px 18px;
  border:none;
  border-radius:8px;
  cursor:pointer;
  color:#fff;
  font-size:15px;
  flex-shrink:1;
}

#nav-all { background:#555; }
#nav-keio { background:var(--color-keio); }
#nav-jr { background:var(--color-jr); }
#nav-ote { background:var(--color-ote); }
#nav-chika { background:var(--color-chika); }
#nav-etc { background:var(--color-etc); }
#nav-data { background:var(--color-data); color:#000; }
#nav-fav { background:var(--color-fav); }
#nav-info { background:#0099ff; }
#nav-settings { background:#444; }

.nav button.active {
  outline:3px solid #fff;
}

.section { display:none; padding:16px; }
.section.active { display:block; }

.item {
  background:#1c1c1c;
  padding:18px;
  border-radius:12px;
  margin-bottom:14px;
  display:flex;
  justify-content:space-between;
  border-left:6px solid #444;
  width:100%;
  box-sizing:border-box;
}

.item-title { font-size:20px; font-weight:bold; }
.item-url { font-size:14px; color:#ccc; }
.item-detail { font-size:16px; margin-top:6px; }
.item-category { font-size:14px; opacity:0.9; }

.item-buttons {
  display:flex;
  flex-direction:column;
  gap:6px;
}
.primary { background:#0099ff; color:#fff; border:none; padding:8px; border-radius:6px; }
.gray { background:#444; color:#fff; border:none; padding:8px; border-radius:6px; }
.danger { background:#c33; color:#fff; border:none; padding:8px; border-radius:6px; }

.card {
  background:#1c1c1c;
  padding:16px;
  border-radius:12px;
  margin-bottom:16px;
}

.circle-tabs {
  display:flex;
  gap:10px;
  justify-content:center;
  flex-wrap:wrap;
}

.circle-tab {
  border-radius:999px;
  padding:8px 18px;
  border:1px solid #555;
  background:#222;
  color:#eee;
  cursor:pointer;
  font-size:14px;
}

.circle-tab.active {
  background:#0099ff;
  border-color:#0099ff;
}

.weather-week {
  display:flex;
  flex-wrap:wrap;
  gap:10px;
  justify-content:center;
}

.weather-day {
  background:#222;
  padding:10px;
  border-radius:8px;
  width:130px;
  text-align:center;
}

.modal-bg {
  position:fixed;
  inset:0;
  background:#000a;
  display:none;
  align-items:center;
  justify-content:center;
  z-index:1000;
}

.modal {
  background:#1c1c1c;
  padding:20px;
  border-radius:14px;
  width:90%;
  max-width:420px;
  box-sizing:border-box;
}

.modal input,
.modal textarea,
.modal select {
  width:calc(100% - 20px);
  margin-left:10px;
  margin-right:10px;
  padding:12px;
  border-radius:8px;
  border:none;
  background:#2a2a2a;
  color:#fff;
  font-size:15px;
  margin-bottom:12px;
}

textarea {
  min-height:120px;
  resize:none;
}

.modal-buttons {
  display:flex;
  justify-content:flex-end;
  gap:10px;
}
</style>
</head>
<body>

<header>tetsudo-site2</header>

<div class="nav">
  <button id="nav-all" class="active" onclick="setCategory('all')">すべて</button>
  <button id="nav-keio" onclick="setCategory('京王')">京王</button>
  <button id="nav-jr" onclick="setCategory('JR')">JR</button>
  <button id="nav-ote" onclick="setCategory('大手私鉄')">大手私鉄</button>
  <button id="nav-chika" onclick="setCategory('地下鉄')">地下鉄</button>
  <button id="nav-etc" onclick="setCategory('その他')">その他</button>
  <button id="nav-data" onclick="setCategory('資料')">資料</button>
  <button id="nav-fav" onclick="setCategory('よく使う')">よく使う</button>
  <button id="nav-info" onclick="showSection('info')">情報</button>
  <button id="nav-settings" onclick="showSection('settings')">設定</button>
</div>

<section id="section-urls" class="section active">

  <!-- 🔍 タイトル検索欄 -->
  <input id="searchInput" placeholder="タイトル検索"
         oninput="searchTitle()"
         style="width:100%; padding:10px; margin-bottom:12px; border-radius:8px; border:none; font-size:16px;">

  <div id="urlList"></div>
</section>
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-app.js";
import { getDatabase, ref, set, onValue, get } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-database.js";
import { getAuth, signInAnonymously } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-auth.js";

/* Firebase設定 */
const firebaseConfig = {
  apiKey:"d47572a1cd7e50746a614ef286b5375c",
  authDomain:"tetsudo-site6.firebaseapp.com",
  databaseURL:"https://tetsudo-site6-default-rtdb.firebaseio.com",
  projectId:"tetsudo-site6",
  storageBucket:"tetsudo-site6.firebasestorage.app",
  messagingSenderId:"563943849207",
  appId:"1:563943849207:web:1c813365201cb431d6e7f2"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase();
const auth = getAuth();

/* ローカルデータ */
let urls = JSON.parse(localStorage.getItem("urls") || "[]");
let currentCategory = "all";
let editIndex = null;
let isAuthed = false;
let searchKeyword = "";  /* ← タイトル検索用 */

/* タイトル検索 */
window.searchTitle = function(){
  searchKeyword = document.getElementById("searchInput").value.trim();
  render();
};

/* ナビID変換 */
function navIdForCategory(cat){
  return {
    "all":"nav-all",
    "京王":"nav-keio",
    "JR":"nav-jr",
    "大手私鉄":"nav-ote",
    "地下鉄":"nav-chika",
    "その他":"nav-etc",
    "資料":"nav-data",
    "よく使う":"nav-fav"
  }[cat] || "nav-all";
}

/* 画面切り替え */
window.showSection = function(name){
  document.querySelectorAll(".section").forEach(s=>s.classList.remove("active"));
  document.querySelectorAll(".nav button").forEach(b=>b.classList.remove("active"));

  if(name==="info"){
    document.getElementById("section-info").classList.add("active");
    document.getElementById("nav-info").classList.add("active");
  }else if(name==="settings"){
    document.getElementById("section-settings").classList.add("active");
    document.getElementById("nav-settings").classList.add("active");
  }else{
    document.getElementById("section-urls").classList.add("active");
    document.getElementById("nav-all").classList.add("active");
  }
};

/* カテゴリ切り替え */
window.setCategory = function(cat){
  currentCategory = cat;
  showSection("urls");

  document.querySelectorAll(".nav button").forEach(b=>b.classList.remove("active"));
  document.getElementById(navIdForCategory(cat)).classList.add("active");

  render();
};

/* カード色 */
function categoryBorderColor(cat){
  return {
    "京王":"var(--color-keio)",
    "JR":"var(--color-jr)",
    "大手私鉄":"var(--color-ote)",
    "地下鉄":"var(--color-chika)",
    "その他":"var(--color-etc)",
    "資料":"var(--color-data)",
    "画像":"var(--color-img)",
    "よく使う":"var(--color-fav)"
  }[cat] || "#444";
}

/* URL描画 */
function render(){
  const list = document.getElementById("urlList");
  list.innerHTML = "";

  let filtered = urls;

  /* カテゴリ絞り込み */
  if(currentCategory !== "all"){
    filtered = filtered.filter(u => u.category === currentCategory);
  }

  /* タイトル検索 */
  if(searchKeyword !== ""){
    filtered = filtered.filter(u => u.title.includes(searchKeyword));
  }

  /* 五十音順 */
  filtered.sort((a,b)=>a.title.localeCompare(b.title,"ja"));

  filtered.forEach((item)=>{
    const realIndex = urls.indexOf(item);  /* ← 編集ズレ防止 */

    const div = document.createElement("div");
    div.className="item";
    div.style.borderLeftColor = categoryBorderColor(item.category);

    div.addEventListener("click", (e) => {
      if (e.target.tagName.toLowerCase() === "button") return;
      window.open(item.url, "_blank");
    });

    let buttonsHtml = "";
    if(isAuthed){
      buttonsHtml = `
        <button class="gray" onclick="openEditModal(${realIndex})">編集</button>
        <button class="danger" onclick="removeUrl(${realIndex})">削除</button>
      `;
    }

    div.innerHTML = `
      <div class="item-inner">
        <div class="item-title">${item.title}</div>
        <div class="item-url">${item.url}</div>
        <div class="item-detail">${item.detail||""}</div>
        <div class="item-category">カテゴリ: ${item.category}</div>
      </div>
      <div class="item-buttons">${buttonsHtml}</div>
    `;
    list.appendChild(div);
  });
}
/* Firebase保存 */
window.cloudSave = function(silent=false){
  set(ref(db,"urlData"),urls).then(()=>{
    if(!silent) alert("保存しました");
  });
};

/* Firebase受信（強制読み込み） */
window.cloudLoad = async function(){
  const snapshot = await get(ref(db,"urlData"));
  urls = snapshot.val() || [];
  localStorage.setItem("urls",JSON.stringify(urls));
  render();
  alert("クラウドから受信しました");
};

/* 自動同期（リアルタイム） */
onValue(ref(db,"urlData"),snap=>{
  urls=snap.val()||[];
  localStorage.setItem("urls",JSON.stringify(urls));
  render();
});

/* 天気API */
const weatherApiKey="d47572a1cd7e50746a614ef286b5375c";
const lat=35.68, lon=139.76;

/* 現在の天気 */
async function loadCurrentWeather(){
  const el=document.getElementById("weather-now");
  try{
    const r=await fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${weatherApiKey}&lang=ja&units=metric`);
    const d=await r.json();
    el.innerHTML=`
      <div style="text-align:center;">
        <img src="https://openweathermap.org/img/wn/${d.weather[0].icon}@4x.png" width="80">
        <div>${d.weather[0].description}</div>
        <div>${d.main.temp}℃</div>
      </div>`;
  }catch{ el.textContent="失敗"; }
}

/* 今日の天気 */
async function loadTodayWeather(){
  const el=document.getElementById("weather-today");
  try{
    const r=await fetch(`https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lon}&appid=${weatherApiKey}&lang=ja&units=metric`);
    const d=await r.json();
    const t=d.daily[0];
    el.innerHTML=`
      <div style="text-align:center;">
        <img src="https://openweathermap.org/img/wn/${t.weather[0].icon}@4x.png" width="80">
        <div>${t.weather[0].description}</div>
        <div>${t.temp.max}℃ / ${t.temp.min}℃</div>
        <div>降水確率 ${Math.round(t.pop*100)}%</div>
      </div>`;
  }catch{ el.textContent="失敗"; }
}

/* 1週間の天気 */
async function loadWeeklyWeather(){
  const el=document.getElementById("weather-week");
  try{
    const r=await fetch(`https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lon}&appid=${weatherApiKey}&lang=ja&units=metric`);
    const d=await r.json();
    const days=d.daily.slice(0,7);

    const wrap=document.createElement("div");
    wrap.className="weather-week";

    days.forEach((day,i)=>{
      const dt=new Date(day.dt*1000);
      const label=i===0?"今日":`${dt.getMonth()+1}/${dt.getDate()}`;

      const box=document.createElement("div");
      box.className="weather-day";
      box.innerHTML=`
        <div>${label}</div>
        <img src="https://openweathermap.org/img/wn/${day.weather[0].icon}.png" width="40">
        <div>${day.weather[0].description}</div>
        <div>${day.temp.max}℃ / ${day.temp.min}℃</div>
      `;
      wrap.appendChild(box);
    });

    el.innerHTML="";
    el.appendChild(wrap);
  }catch{ el.textContent="失敗"; }
}

/* 天気タブ切り替え */
window.switchWeatherTab = function(tab){
  document.querySelectorAll(".circle-tab").forEach(b=>{
    b.classList.toggle("active", b.dataset.tab===tab);
  });

  document.getElementById("weather-now").style.display = tab==="now"?"block":"none";
  document.getElementById("weather-today").style.display = tab==="today"?"block":"none";
  document.getElementById("weather-week").style.display = tab==="week"?"block":"none";
};

/* 時計 */
function startClock(){
  setInterval(()=>{
    const now=new Date();
    document.getElementById("datetime").textContent=
      `${now.getFullYear()}年${now.getMonth()+1}月${now.getDate()}日 `
      +`${now.getHours()}時${String(now.getMinutes()).padStart(2,"0")}分${String(now.getSeconds()).padStart(2,"0")}秒`;
  },1000);
}

/* テーマ切り替え */
window.toggleTheme = function(){
  document.body.classList.toggle("light");
  localStorage.setItem("theme",document.body.classList.contains("light")?"light":"dark");
};

if(localStorage.getItem("theme")==="light"){
  document.body.classList.add("light");
}

/* 初期化 */
signInAnonymously(auth).then(()=>cloudLoad());
render();
startClock();
loadCurrentWeather();
loadTodayWeather();
loadWeeklyWeather();
</script>

</body>
</html>
