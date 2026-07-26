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

/* 起動画面 */
#startup {
  padding:24px;
  text-align:center;
}
.startup-title {
  font-size:20px;
  margin-bottom:20px;
}
.startup-buttons {
  display:flex;
  justify-content:center;
  gap:16px;
  flex-wrap:wrap;
}
.startup-buttons button {
  padding:12px 24px;
  border:none;
  border-radius:10px;
  font-size:16px;
  cursor:pointer;
  background:#0099ff;
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

.item-title {
  font-size:26px;
  font-weight:bold;
  text-align:left;
}

.item-detail {
  font-size:16px;
  margin-top:10px;
  line-height:1.6;
}

.item-category {
  font-size:14px;
  opacity:0.9;
  margin-top:8px;
}

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

/* お知らせモード用 */
#notice-nav {
  display:flex;
  gap:10px;
  padding:14px;
  background:#1a1a1a;
  justify-content:center;
  flex-wrap:wrap;
}
#notice-nav button {
  padding:10px 18px;
  border:none;
  border-radius:8px;
  cursor:pointer;
  color:#fff;
  font-size:15px;
}
#notice-nav button.active {
  outline:3px solid #fff;
}
#notice-nav-notice { background:#0099ff; }
#notice-nav-calendar { background:#27ae60; }
#notice-nav-files { background:#8e44ad; }

/* カレンダー強化版 */
.calendar {
  max-width:420px;
  margin:0 auto;
}
.calendar-header {
  display:flex;
  justify-content:space-between;
  align-items:center;
  margin-bottom:10px;
}
.calendar-grid {
  display:grid;
  grid-template-columns:repeat(7,1fr);
  gap:4px;
}
.calendar-cell {
  padding:8px;
  border-radius:6px;
  text-align:center;
  font-size:14px;
}
.calendar-cell-header {
  font-weight:bold;
}
.calendar-cell-day {
  cursor:pointer;
}
.calendar-cell-today-light {
  background:#ffeb3b;
  color:#000;
}
.calendar-cell-today-dark {
  background:#ff9800;
  color:#fff;
}
.calendar-cell-has-event::after {
  content:"●";
  display:block;
  font-size:10px;
  margin-top:2px;
}

/* ファイル */
.file-list {
  display:flex;
  flex-direction:column;
  gap:10px;
}
.file-card {
  display:flex;
  align-items:center;
  gap:10px;
  padding:10px;
  border-radius:10px;
  background:#1c1c1c;
}
.file-thumb {
  width:48px;
  height:48px;
  border-radius:6px;
  background:#333;
  display:flex;
  align-items:center;
  justify-content:center;
  font-size:12px;
}
body.light .file-card { background:#fff; }
body.light .file-thumb { background:#ddd; }
</style>
</head>
<body>

<header>tetsudo-site2</header>
<script type="module">
/* Firebase 読み込み */
import { initializeApp } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-app.js";
import { getDatabase, ref, set, onValue, get } from "https://www.gstatic.com/firebasejs/12.14.0/firebase-database.js";
import { 
  getAuth, 
  signInAnonymously,
  GoogleAuthProvider,
  signInWithPopup
} from "https://www.gstatic.com/firebasejs/12.14.0/firebase-auth.js";
import { 
  getStorage, 
  ref as sRef, 
  uploadBytes, 
  getDownloadURL 
} from "https://www.gstatic.com/firebasejs/12.14.0/firebase-storage.js";

/* Firebase設定 */
const firebaseConfig = {
  apiKey:"AIzaSyD55Pawag1UichGwM-Uxddivb8lFr7QOU8",
  authDomain:"tetsudo-site6.firebaseapp.com",
  databaseURL:"https://tetsudo-site6-default-rtdb.firebaseio.com",
  projectId:"tetsudo-site6",
  storageBucket:"tetsudo-site6.appspot.com",
  messagingSenderId:"563943849207",
  appId:"1:563943849207:web:1c813365201cb431d6e7f2"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase();
const auth = getAuth();
const storage = getStorage();

/* ローカルデータ */
let urls = JSON.parse(localStorage.getItem("urls") || "[]");
let currentCategory = "all";
let editIndex = null;
let isAuthed = false;
let searchKeyword = "";
let currentMode = null;

/* Googleログイン用 */
let googleAccessToken = null;
let calendarEvents = [];

/* 起動画面 */
window.openMode = function(mode){
  currentMode = mode;
  document.getElementById("startup").style.display = "none";
  if(mode === "urls"){
    document.getElementById("urlsMode").style.display = "block";
  }else{
    document.getElementById("noticeMode").style.display = "block";
  }
};

/* お知らせモード内タブ */
window.setNoticeSection = function(name){
  ["notice","calendar","files"].forEach(id=>{
    document.getElementById("notice-section-"+id).classList.remove("active");
    document.getElementById("notice-section-"+id).style.display = "none";
    document.getElementById("notice-nav-"+id).classList.remove("active");
  });
  document.getElementById("notice-section-"+name).classList.add("active");
  document.getElementById("notice-section-"+name).style.display = "block";
  document.getElementById("notice-nav-"+name).classList.add("active");
};

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

/* 画面切り替え（URLモード） */
window.showSection = function(name){
  document.querySelectorAll("#urlsMode .section").forEach(s=>s.classList.remove("active"));
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
    "よく使う":"var(--color-fav)"
  }[cat] || "#444";
}

/* URL描画 */
function render(){
  const list = document.getElementById("urlList");
  list.innerHTML = "";

  let filtered = urls;

  if(currentCategory !== "all"){
    filtered = filtered.filter(u => u.category === currentCategory);
  } else {
    filtered = filtered.filter(u => u.category !== "資料");
  }

  if(searchKeyword !== ""){
    filtered = filtered.filter(u => u.title.includes(searchKeyword));
  }

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
      <div class="item-inner" style="width:100%;">
        <div class="item-title">${item.title}</div>
        <div class="item-detail">
          ${(item.detail || "").replace(/\n/g, "<br>")}
        </div>
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
  document.getElementById("modalSubmitBtn").textContent = "追加する";

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
  document.getElementById("modalSubmitBtn").textContent = "上書き保存";

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
  render();
  closeModal();
};

/* 削除 */
window.removeUrl = function(i){
  if(!confirm("削除しますか？")) return;

  urls.splice(i,1);
  localStorage.setItem("urls",JSON.stringify(urls));
  render();
};

/* クラウド保存 */
window.cloudSave = function(){
  set(ref(db,"urlData"), urls).then(()=>{
    alert("クラウドに保存しました");
  });
};

/* クラウド受信 */
window.cloudLoad = async function(){
  const snapshot = await get(ref(db,"urlData"));
  urls = snapshot.val() || [];
  localStorage.setItem("urls", JSON.stringify(urls));
  render();
  alert("クラウドから受信しました");
};

/* 自動同期 */
onValue(ref(db,"urlData"), snap=>{
  urls = snap.val() || [];
  localStorage.setItem("urls", JSON.stringify(urls));
  render();
});
/* Googleログイン */
window.googleLogin = async function(){
  const provider = new GoogleAuthProvider();
  try{
    const result = await signInWithPopup(auth, provider);
    const credential = GoogleAuthProvider.credentialFromResult(result);
    googleAccessToken = credential.accessToken;

    alert("Googleログイン成功");

    await loadCalendarEvents();
    drawCalendar();
    loadTodayNotice();
  }catch(e){
    console.error(e);
    alert("Googleログイン失敗");
  }
};

/* Googleカレンダー予定取得 */
async function loadCalendarEvents(){
  if(!googleAccessToken){
    calendarEvents = [];
    return;
  }

  const now = new Date();
  const first = new Date(now.getFullYear(), now.getMonth(), 1);
  const last  = new Date(now.getFullYear(), now.getMonth()+1, 0);

  const timeMin = first.toISOString();
  const timeMax = last.toISOString();

  const url = `https://www.googleapis.com/calendar/v3/calendars/primary/events`
    + `?timeMin=${encodeURIComponent(timeMin)}`
    + `&timeMax=${encodeURIComponent(timeMax)}`
    + `&singleEvents=true`
    + `&orderBy=startTime`;

  const res = await fetch(url, {
    headers:{ Authorization:`Bearer ${googleAccessToken}` }
  });

  if(!res.ok){
    console.error("Calendar API error", await res.text());
    calendarEvents = [];
    return;
  }

  const data = await res.json();
  calendarEvents = (data.items || []).map(ev=>{
    const start = ev.start.dateTime || ev.start.date;
    const d = new Date(start);
    return {
      title: ev.summary || "(無題)",
      year: d.getFullYear(),
      month: d.getMonth(),
      day: d.getDate()
    };
  });
}

/* カレンダー強化版描画 */
function drawCalendar(){
  const grid = document.getElementById("calendar-grid");
  const label = document.getElementById("calendar-month-label");
  if(!grid || !label) return;

  grid.innerHTML = "";
  const first = new Date(calendarYear, calendarMonth, 1);
  const last = new Date(calendarYear, calendarMonth+1, 0);
  const startDay = first.getDay();
  const daysInMonth = last.getDate();

  label.textContent = `${calendarYear}年 ${calendarMonth+1}月`;

  const headers = ["日","月","火","水","木","金","土"];
  headers.forEach(h=>{
    const cell = document.createElement("div");
    cell.className = "calendar-cell calendar-cell-header";
    cell.textContent = h;
    grid.appendChild(cell);
  });

  for(let i=0;i<startDay;i++){
    const cell = document.createElement("div");
    cell.className = "calendar-cell";
    grid.appendChild(cell);
  }

  const today = new Date();
  const isLight = document.body.classList.contains("light");

  for(let d=1; d<=daysInMonth; d++){
    const cell = document.createElement("div");
    cell.className = "calendar-cell calendar-cell-day";
    cell.textContent = d;

    /* 今日強調 */
    if(today.getFullYear()===calendarYear &&
       today.getMonth()===calendarMonth &&
       today.getDate()===d){
      cell.classList.add(isLight ? "calendar-cell-today-light" : "calendar-cell-today-dark");
    }

    /* Google予定がある日をマーク */
    const hasEvent = calendarEvents.some(ev =>
      ev.year===calendarYear &&
      ev.month===calendarMonth &&
      ev.day===d
    );
    if(hasEvent){
      cell.classList.add("calendar-cell-has-event");
    }

    cell.onclick = ()=>showDayEvents(calendarYear, calendarMonth, d);
    grid.appendChild(cell);
  }
}

/* 月移動 */
window.prevMonth = function(){
  calendarMonth--;
  if(calendarMonth<0){
    calendarMonth=11;
    calendarYear--;
  }
  drawCalendar();
};
window.nextMonth = function(){
  calendarMonth++;
  if(calendarMonth>11){
    calendarMonth=0;
    calendarYear++;
  }
  drawCalendar();
};

/* 選択した日の予定表示 */
function showDayEvents(y,m,d){
  const el = document.getElementById("selectedDayEvents");

  const events = calendarEvents.filter(ev =>
    ev.year===y && ev.month===m && ev.day===d
  );

  if(events.length===0){
    el.textContent = `${y}年${m+1}月${d}日の予定はありません`;
    return;
  }

  const lines = events.map(ev => `・${ev.title}`);
  el.innerHTML = `${y}年${m+1}月${d}日の予定<br>${lines.join("<br>")}`;
}

/* 今日のお知らせ */
function loadTodayNotice(){
  const el = document.getElementById("todayNotice");
  const now = new Date();
  const y = now.getFullYear();
  const m = now.getMonth();
  const d = now.getDate();

  if(!googleAccessToken){
    el.textContent = "Googleでログインすると、今日の予定がここに表示されます。";
    return;
  }

  const events = calendarEvents.filter(ev =>
    ev.year===y && ev.month===m && ev.day===d
  );

  if(events.length===0){
    el.textContent = "今日の予定はありません。";
    return;
  }

  const lines = events.map(ev => `・${ev.title}`);
  el.innerHTML = `今日（${y}年${m+1}月${d}日）の予定<br>${lines.join("<br>")}`;
}

/* ファイル追加（モーダル形式に統合済み） */
window.uploadFile = async function(){
  const title = document.getElementById("fileTitle").value.trim();
  const fileInput = document.getElementById("fileInput");
  if(!title || !fileInput.files[0]){
    alert("タイトルとファイルは必須です");
    return;
  }
  const file = fileInput.files[0];
  const path = `files/${Date.now()}_${file.name}`;
  const refFile = sRef(storage, path);

  await uploadBytes(refFile, file);
  const url = await getDownloadURL(refFile);

  const listRef = ref(db,"files");
  const snapshot = await get(listRef);
  const files = snapshot.val() || [];
  files.push({title, url, name:file.name, type:file.type});
  await set(listRef, files);

  document.getElementById("fileTitle").value = "";
  fileInput.value = "";
  loadFiles();
};

/* ファイル一覧 */
async function loadFiles(){
  const el = document.getElementById("fileList");
  if(!el) return;
  el.innerHTML = "";

  const snapshot = await get(ref(db,"files"));
  const files = snapshot.val() || [];

  files.forEach(f=>{
    const card = document.createElement("div");
    card.className = "file-card";

    const thumb = document.createElement("div");
    thumb.className = "file-thumb";

    let label = "FILE";
    if(f.type.startsWith("image/")) label = "IMG";
    else if(f.type==="application/pdf") label = "PDF";
    else if(f.type.startsWith("video/")) label = "VID";

    thumb.textContent = label;

    const title = document.createElement("div");
    title.textContent = f.title;

    card.appendChild(thumb);
    card.appendChild(title);
    card.onclick = ()=>window.open(f.url,"_blank");

    el.appendChild(card);
  });
}

/* 初期化 */
async function init(){
  await signInAnonymously(auth);
  await cloudLoad();
  render();
  startClock();
  loadCurrentWeather();
  loadTodayWeather();
  loadWeeklyWeather();
  initCalendar();
  loadTodayNotice();
  loadFiles();
}

init();
</script>

</body>
</html>
