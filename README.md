# repo4-9
ريبو للتيست
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>Worm GPT - استعراض البيانات</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <header>
    <h1>Worm GPT</h1>
    <p id="project-desc">جاري تحميل وصف المشروع...</p>
  </header>

  <main>
    <section id="data-list-section">
      <h2>ملفات البيانات (JSON)</h2>
      <ul id="data-list">
        <!-- سيتم توليد القائمة تلقائيًا -->
      </ul>
    </section>
    <section id="data-view-section">
      <h2>محتوى الملف</h2>
      <pre id="data-view">اختر ملفاً من القائمة لعرض محتواه</pre>
    </section>
    <aside id="gallery">
      <h3>معرض الصور</h3>
      <div id="img-list">
        <!-- سيتم توليد الصور تلقائيًا -->
      </div>
    </aside>
  </main>

  <footer>
    <small>جميع الحقوق محفوظة &copy; 2025</small>
  </footer>
  <script src="script.js"></script>
</body>
</html>body {
  font-family: 'Tajawal', Arial, sans-serif;
  background: #222;
  color: #fafafa;
  margin: 0;
  direction: rtl;
}

header {
  background: #333;
  padding: 1.5em 1em 1em 1em;
  text-align: center;
  border-bottom: 2px solid #444;
}

header h1 {
  margin: 0 0 0.2em 0;
  color: #00e3a3;
}

header p {
  color: #ccc;
  font-size: 1.1em;
}

main {
  display: flex;
  flex-wrap: wrap;
  gap: 2em;
  max-width: 1200px;
  margin: 2em auto;
  padding: 0 1em;
}

section, aside {
  background: #292929;
  border-radius: 12px;
  padding: 1.5em 1em 1em 1em;
  box-shadow: 0 2px 12px #1118;
}

#data-list-section {
  flex: 1 1 240px;
  min-width: 220px;
  max-width: 320px;
}

#data-list {
  list-style: none;
  padding: 0;
}

#data-list li {
  margin-bottom: 0.8em;
}

#data-list button {
  width: 100%;
  background: #00e3a3;
  color: #222;
  font-weight: bold;
  border: none;
  border-radius: 6px;
  padding: 0.6em 1em;
  cursor: pointer;
  transition: background 0.2s;
}

#data-list button:hover {
  background: #00b37c;
}

#data-view-section {
  flex: 3 1 450px;
  min-width: 300px;
}

#data-view {
  background: #191919;
  color: #e6e6e6;
  border-radius: 8px;
  padding: 1em;
  font-size: 1.1em;
  overflow-x: auto;
  min-height: 200px;
}

#gallery {
  flex: 1 1 220px;
  min-width: 170px;
  max-width: 240px;
}

#gallery h3 {
  margin-top: 0;
}

#img-list {
  display: flex;
  flex-direction: column;
  gap: 1em;
}

#img-list img {
  width: 100%;
  border-radius: 8px;
  border: 2px solid #222;
  box-shadow: 0 1px 8px #0008;
}

footer {
  background: #222;
  color: #888;
  text-align: center;
  padding: 1em 0;
  border-top: 2px solid #444;
  margin-top: 2em;
  font-size: 1em;
}

@media (max-width: 900px) {
  main {
    flex-direction: column;
    gap: 1.5em;
  }
  #data-list-section, #data-view-section, #gallery {
    max-width: 100%;
    min-width: 0;
  }
}// إعداد روابط الريبو:
const repoOwner = "kafyasfngl";
const repoName = "worm-gpt";
const branch = "main";

// جلب وصف المشروع من README.md
fetch(`https://raw.githubusercontent.com/${repoOwner}/${repoName}/${branch}/README.md`)
  .then(res => res.ok ? res.text() : "")
  .then(md => {
    document.getElementById("project-desc").innerText = md ? md.replace(/^# .*\n?/, "") : "لم يتم العثور على وصف.";
  });

// جلب قائمة ملفات البيانات (JSON)
fetch(`https://api.github.com/repos/${repoOwner}/${repoName}/contents/`)
  .then(res => res.json())
  .then(files => {
    const dataFiles = files.filter(f => f.name.endsWith(".json") && f.type === "file");
    const list = document.getElementById("data-list");
    if (dataFiles.length === 0) {
      list.innerHTML = "<li>لا توجد ملفات بيانات JSON</li>";
      return;
    }
    dataFiles.forEach(file => {
      const li = document.createElement("li");
      const btn = document.createElement("button");
      btn.textContent = file.name;
      btn.onclick = () => loadJsonFile(file.download_url);
      li.appendChild(btn);
      list.appendChild(li);
    });
  });

// تحميل وعرض محتوى ملف JSON
function loadJsonFile(url) {
  fetch(url)
    .then(res => res.json())
    .then(json => {
      document.getElementById("data-view").innerText = JSON.stringify(json, null, 2);
    })
    .catch(() => {
      document.getElementById("data-view").innerText = "خطأ في تحميل الملف!";
    });
}

// جلب الصور من مجلد img
fetch(`https://api.github.com/repos/${repoOwner}/${repoName}/contents/img`)
  .then(res => res.json())
  .then(imgs => {
    if (!Array.isArray(imgs)) return;
    const imgDiv = document.getElementById("img-list");
    imgs.filter(f => f.type === "file" && /\.(jpg|jpeg|png|gif|webp)$/i.test(f.name)).forEach(img => {
      const image = document.createElement("img");
      image.src = img.download_url;
      image.alt = img.name;
      imgDiv.appendChild(image);
    });
    if (imgDiv.children.length === 0) imgDiv.innerHTML = "<span>لا توجد صور</span>";
  })
  .catch(() => {
    document.getElementById("img-list").innerHTML = "<span>تعذر تحميل الصور</span>";
  });
