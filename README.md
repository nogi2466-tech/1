<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>聖介の鉄道URLアプリ</title>
  <style>
    body { font-family: system-ui, sans-serif; margin: 0; padding: 0; background: #111; color: #eee; }
    header { background: #222; padding: 10px 16px; display: flex; justify-content: space-between; align-items: center; }
    header h1 { font-size: 18px; margin: 0; }
    main { padding: 16px; }
    .tabs { display: flex; gap: 8px; margin-bottom: 16px; }
    .tab { padding: 6px 10px; border-radius: 4px; background: #333; cursor: pointer; font-size: 13px; }
    .tab.active { background: #0078d4; }
    .section { display: none; }
    .section.active { display: block; }
    .toolbar { margin-bottom: 12px; display: flex; gap: 8px; flex-wrap: wrap; }
    button { padding: 6px 10px; border-radius: 4px; border: none; cursor: pointer; font-size: 13px; }
    button.primary { background: #0078d4; color: #fff; }
    button.danger { background: #c0392b; color: #fff; }
    button.gray { background: #444; color: #fff; }
    input, select { padding: 6px 8px; border-radius: 4px; border: 1px solid #555; background: #222; color: #eee; font-size: 13px; }
    .list { margin-top: 8px; }
    .item { padding: 8px; margin-bottom: 6px; background: #1c1c1c; border-radius: 4px; display: flex; justify-content: space-between; align-items: center; }
    .item-main { max-width: 70%; }
    .item-title { font-size: 14px; font-weight: 600; }
    .item-url { font-size: 12px; color: #aaa; }
    .item-category { font-size: 11px; color: #ccc; margin-top: 2px; }
    .item-buttons { display: flex; gap: 4px; }
    .info-card { background: #1c1c1c; padding: 10px; border-radius: 4px; margin-bottom: 10px; }
    .info-title { font-size: 14px; font-weight: 600; margin-bottom: 4px; }
  </style>
</head>
<body>
  <header>
    <h1>聖介の鉄道URLアプリ</h1>
    <div>
      <button class="gray" onclick="showSection('urls')">URL一覧</button>
      <button class="gray" onclick="showSection('info')">情報</button>
      <button class="gray" onclick="showSection('settings')">設定</button>
    </div>
  </header>

  <main>
    <!-- URL一覧セクション -->
    <section id="section-urls" class="section active">
      <div class="toolbar">
        <input id="newTitle" type="text" placeholder="タイトル">
        <input id="newUrl" type="text" placeholder="URL">
        <select id="newCategory">
          <option value="京王">京王</option>
          <option value="JR">JR</option>
          <option value="大手私鉄">大手私鉄</option>
          <option value="その他">その他</option>
        </select>
        <button class="primary" onclick="addUrl()">追加</button>
        <button class="gray" onclick="filterCategory('all')">すべて</button>
        <button class="gray" onclick="filterCategory('京王')">京王</button>
        <button class="gray" onclick="filterCategory('JR')">JR</button>
        <button class="gray" onclick="filterCategory('大手私鉄')">大手私鉄</button>
        <button class="gray" onclick="filterCategory('その他')">その他</button>
      </div>
      <div id="urlList" class="list"></div>
    </section>

    <!-- 情報セクション（天気＋時刻） -->
    <section id="section-info" class="section">
      <div class="info-card">
        <div class="info-title">現在の天気（東京）</div>
        <div id="weather">読み込み中...</div>
      </div>
      <div class="info-card">
        <div class="info-title">現在時刻</div>
        <div id="datetime">読み込み中...</div>
      </div>
      <button class="primary" onclick="loadWeather()">天気を更新</button>
    </section>

    <!-- 設定セクション（クラウド保存・受信） -->
    <section id="section-settings" class="section">
      <div class="info-card">
        <div class="info-title">クラウド同期</div>
        <p style="font-size:12px; color:#ccc;">
          URL一覧をクラウド（Googleスプレッドシート）に保存し、他のデバイスで同じ状態を受信できます。
        </p>
        <button class="primary" onclick="cloudSave()">クラウド保存</button>
        <button class="gray" onclick="cloudLoad()">クラウド受信</button>
      </div>
    </section>
  </main>

  <script>
    // ====== データ管理 ======
    let urls = [];

    function loadLocal() {
      const saved = localStorage.getItem("urls");
      if (saved) {
        urls = JSON.parse(saved);
      } else {
        urls = [];
      }
      render();
    }

    function render() {
      const list = document.getElementById("urlList");
      list.innerHTML = "";
      const filtered = urls.filter(item => {
        if (currentCategory === "all") return true;
        return item.category === currentCategory;
      });
      filtered.forEach((item, index) => {
        const div = document.createElement("div");
        div.className = "item";

        const main = document.createElement("div");
        main.className = "item-main";
        const t = document.createElement("div");
        t.className = "item-title";
        t.textContent = item.title;
        const u = document.createElement("div");
        u.className = "item-url";
        u.textContent = item.url;
        const c = document.createElement("div");
        c.className = "item-category";
        c.textContent = "カテゴリ: " + item.category;
        main.appendChild(t);
        main.appendChild(u);
        main.appendChild(c);

        const btns = document.createElement("div");
        btns.className = "item-buttons";
        const editBtn = document.createElement("button");
        editBtn.className = "gray";
        editBtn.textContent = "編集";
        editBtn.onclick = () => editUrl(index);
        const delBtn = document.createElement("button");
        delBtn.className = "danger";
        delBtn.textContent = "削除";
        delBtn.onclick = () => removeUrl(index);
        btns.appendChild(editBtn);
        btns.appendChild(delBtn);

        div.appendChild(main);
        div.appendChild(btns);
        list.appendChild(div);
      });
    }

    function addUrl() {
      const title = document.getElementById("newTitle").value.trim();
      const url = document.getElementById("newUrl").value.trim();
      const category = document.getElementById("newCategory").value;

      if (!title || !url) {
        alert("タイトルとURLを入力してください");
        return;
      }

      urls.push({ title, url, category });
      localStorage.setItem("urls", JSON.stringify(urls));
      cloudSave();
      render();

      document.getElementById("newTitle").value = "";
      document.getElementById("newUrl").value = "";
    }

    function editUrl(index) {
      const item = urls[index];
      const newTitle = prompt("タイトルを入力", item.title);
      if (newTitle === null) return;
      const newUrl = prompt("URLを入力", item.url);
      if (newUrl === null) return;
      const newCategory = prompt("カテゴリを入力（京王 / JR / 大手私鉄 / その他）", item.category);
      if (newCategory === null) return;

      urls[index] = {
        title: newTitle.trim(),
        url: newUrl.trim(),
        category: newCategory.trim()
      };

      localStorage.setItem("urls", JSON.stringify(urls));
      cloudSave();
      render();
      alert("編集内容をクラウドに同期しました");
    }

    function removeUrl(index) {
      if (!confirm("このURLを削除しますか？")) return;
      urls.splice(index, 1);
      localStorage.setItem("urls", JSON.stringify(urls));
      cloudSave();
      render();
    }

    let currentCategory = "all";
    function filterCategory(cat) {
      currentCategory = cat;
      render();
    }

    // ====== セクション切り替え ======
    function showSection(name) {
      ["urls", "info", "settings"].forEach(id => {
        document.getElementById("section-" + id).classList.remove("active");
      });
      document.getElementById("section-" + name).classList.add("active");
    }

    // ====== 天気API ======
    async function loadWeather() {
      const apiKey = "d47572a1cd7e50746a614ef286b5375c"; // 聖介のOpenWeather APIキー
      const url = `https://api.openweathermap.org/data/2.5/weather?q=Tokyo&appid=${apiKey}&lang=ja&units=metric`;

      try {
        const res = await fetch(url);
        const data = await res.json();

        const weather = data.weather[0].description;
        const temp = data.main.temp;
        const humidity = data.main.humidity;

        document.getElementById("weather").innerHTML =
          `天気：${weather}<br>気温：${temp}℃<br>湿度：${humidity}%`;
      } catch (e) {
        document.getElementById("weather").innerText = "天気情報を取得できませんでした";
      }
    }

    // ====== 時刻リアルタイム更新 ======
    function startClock() {
      setInterval(() => {
        const now = new Date();
        const y = now.getFullYear();
        const m = now.getMonth() + 1;
        const d = now.getDate();
        const h = now.getHours();
        const min = now.getMinutes().toString().padStart(2, "0");
        const sec = now.getSeconds().toString().padStart(2, "0");
        document.getElementById("datetime").textContent =
          `${y}年${m}月${d}日 ${h}時${min}分${sec}秒`;
      }, 1000);
    }

    // ====== クラウド保存・受信（GAS） ======
    const GAS_URL = "https://script.google.com/macros/s/AKfycbyJVP7mnaKUerEO5CJBf1_DYVcoSoK1MfxJQ3xufcnQufE1oPJ3M3ZvWsB3P-lZFsgskg/exec";

    function cloudSave() {
      fetch(GAS_URL, {
        method: "POST",
        body: JSON.stringify(urls)
      })
      .then(() => console.log("クラウド保存完了"))
      .catch(() => console.log("クラウド保存失敗"));
    }

    function cloudLoad() {
      fetch(GAS_URL)
        .then(res => res.json())
        .then(data => {
          urls = data;
          localStorage.setItem("urls", JSON.stringify(urls));
          render();
          alert("クラウドから受信しました");
        })
        .catch(() => alert("クラウド受信に失敗しました"));
    }

    // 初期化
    loadLocal();
    startClock();
    loadWeather();
  </script>
</body>
</html>
