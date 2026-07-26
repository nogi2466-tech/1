<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>tetsudo-site2</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
:root {
  --color-keio:#8e44ad;
  --color-jr:#27ae60;
  --color-ote:#e74c3c;
  --color-chika:#2980b9;
  --color-etc:#7f8c8d;
  --color-data:#f1c40f;
  --color-fav:#ff8800;
}

body {
  margin:0;
  background:#0f0f0f;
  color:#eee;
  font-family:system-ui;
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
  flex-wrap:wrap;
  justify-content:center;
}

.nav button {
  padding:10px 18px;
  border:none;
  border-radius:8px;
  cursor:pointer;
  color:#fff;
  font-size:15px;
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
  border-left:6px solid #444;
}

.item-title { font-size:20px; font-weight:bold; }
.item-url { font-size:14px; color:#ccc; }
.item-detail { font-size:16px; margin-top:6px; }
.item-category { font-size:14px; opacity:0.9; }

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
}

.circle-tab {
  border-radius:999px;
  padding:8px 18px;
  border:1px solid #555;
  background:#222;
  color:#eee;
  cursor:pointer;
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
  <input id="searchInput" placeholder="タイトル検索"
         oninput="searchTitle()"
         style="width:100%; padding:10px; margin-bottom:12px; border-radius:8px; border:none; font-size:16px;">
  <div id="urlList"></div>
</section>

<section id="section-info" class="section">
  <div class="card">
    <h3>天気情報（東京）</h3>
    <div class="circle-tabs">
      <button class="circle-tab active" data-tab="now" onclick="switchWeatherTab('now')">現在</button>
      <button class="circle-tab" data-tab="today" onclick="switchWeatherTab('today')">今日</button>
      <button class="circle-tab" data-tab="week" onclick="switchWeatherTab('week')">1週間</button>
    </div>

    <div id="weather-now">読み込み中...</div>
    <div id="weather-today" style="display:none;">読み込み中...</div>
    <div id="weather-week" style="display:none;">読み込み中...</div>
  </div>

  <div class="card">
    <h3>現在時刻</h3>
    <div id="datetime">読み込み中...</div>
  </div>
</section>

<section id="section-settings" class="section">
  <div class="card">
    <h3>テーマ切り替え</h3>
    <button onclick="toggleTheme()">ライト / ダーク切り替え</button>
  </div>
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
let searchKeyword = "";

/* URLを表示するカテゴリ（すべてで表示するカテゴリ） */
const urlCategories = ["京王", "JR", "大手私鉄", "地下鉄", "その他"];

/* タイトル検索 */
window.searchTitle = function(){
  searchKeyword = document.getElementById("searchInput").value.trim();
  render();
};

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
  document.getElementById("nav-" + (cat==="all"?"all":cat)).classList.add("active");

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
    "よく使う":"var(--color-fav)"
  }[cat] || "#444";
}

/* URL描画（ここが重要） */
function render(){
  const list = document.getElementById("urlList");
  list.innerHTML = "";

  let filtered = urls;

  /* すべて → 京王・JR・大手私鉄・地下鉄・その他だけ表示 */
  if (currentCategory === "all") {
    filtered = urls.filter(u => urlCategories.includes(u.category));
  }

  /* 資料 → 資料だけ表示 */
  if (currentCategory === "資料") {
    filtered = urls.filter(u => u.category === "資料");
  }

  /* よく使う → よく使うだけ表示 */
  if (currentCategory === "よく使う") {
    filtered = urls.filter(u => u.category === "よく使う");
  }

  /* 情報・設定 → URLは一切表示しない */
  if (currentCategory === "情報" || currentCategory === "設定") {
    return;
  }

  /* 通常カテゴリ（京王・JR・大手私鉄・地下鉄・その他） */
  if (urlCategories.includes(currentCategory)) {
    filtered = urls.filter(u => u.category === currentCategory);
  }

  /* タイトル検索 */
  if(searchKeyword !== ""){
    filtered = filtered.filter(u => u.title.includes(searchKeyword));
  }

  /* 五十音順 */
  filtered.sort((a,b)=>a.title.localeCompare(b.title,"ja"));

  filtered.forEach((item)=>{
    const realIndex = urls.indexOf(item);

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

/* パスワード認証 */
window.checkPass = function(){
  if(document.getElementById("passInput").value==="0829"){
    isAuthed = true;
    document.getElementById("openAddBtn").style.display="inline-block";
    alert("認証成功");
    render();
  }else{
    alert("違います");
  }
};

/* モーダル（追加） */
window.openAddModal = function(){
  editIndex=null;

  document.getElementById("modalTitle").textContent="新規追加";
  document.querySelector(".modal-buttons .primary").textContent = "追加する";

  document.getElementById("formTitle").value="";
  document.getElementById("formUrl").value="";
  document.getElementById("formDetail").value="";
  document.getElementById("formCategory").value="京王";

  document.getElementById("modalBg").style.display="flex";
};

/* モーダル（編集） */
window.openEditModal = function(i){
  editIndex=i;
  const item=urls[i];

  document.getElementById("modalTitle").textContent="編集";
  document.querySelector(".modal-buttons .primary").textContent = "上書き保存";

  document.getElementById("formTitle").value=item.title;
  document.getElementById("formUrl").value=item.url;
  document.getElementById("formDetail").value=item.detail;
  document.getElementById("formCategory").value=item.category;

  document.getElementById("modalBg").style.display="flex";
};

/* モーダル閉じる */
window.closeModal = function(){
  document.getElementById("modalBg").style.display="none";
};

/* モーダル送信 */
window.submitModal = function(){
  const title=document.getElementById("formTitle").value.trim();
  const url=document.getElementById("formUrl").value.trim();
  const detail=document.getElementById("formDetail").value.trim();
  const category=document.getElementById("formCategory").value;

  if(!title || !url){
    alert("タイトルとURLは必須です");
    return;
  }

  const data={title,url,detail,category};

  if(editIndex===null){
    urls.push(data);
  }else{
    urls[editIndex]=data;
  }

  localStorage.setItem("urls",JSON.stringify(urls));
  cloudSave(true);
  render();
  closeModal();
};

/* 削除 */
window.removeUrl = function(i){
  if(!confirm("削除しますか？")) return;
  urls.splice(i,1);
  localStorage.setItem("urls",JSON.stringify(urls));
  cloudSave(true);
  render();
};

/* クラウド保存 */
window.cloudSave = function(silent=false){
  set(ref(db,"urlData"),urls).then(()=>{
    if(!silent) alert("保存しました");
  });
};

/* クラウド受信 */
window.cloudLoad = async function(){
  const snapshot = await get(ref(db,"urlData"));
  urls = snapshot.val() || [];
  localStorage.setItem("urls",JSON.stringify(urls));
  render();
  alert("クラウドから受信しました");
};

/* 自動同期 */
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
    const r=await fetch(`https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&appid=${weatherApiKey}&lang=ja&units=metric`);
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
    const r=await fetch(`https://api.openweathermap.org/data/2.5/onecall?lat=${lat}&lon=${lon}&appid=${weatherApiKey}&lang=ja&units=metric`);
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
