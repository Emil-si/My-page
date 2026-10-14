<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>我的主页</title>
<style>
  :root{
    --bg: linear-gradient(135deg, #f5f7fa 0%, #e8ecf1 100%);
    --card: rgba(255, 255, 255, 0.7);
    --text:#1a1a1a; --text-sub:#666666;
    --border: rgba(0,0,0,0.08); --accent:#1a1a1a; --accent-text:#ffffff;
    --bar:rgba(255,255,255,0.6);
    --shadow: 0 10px 40px -10px rgba(0,0,0,0.08);
    --blur: blur(16px);
  }
  html.dark{
    --bg: linear-gradient(135deg, #0b0c10 0%, #15171e 100%);
    --card: rgba(30, 32, 38, 0.6);
    --text:#f5f5f5; --text-sub:#a3a3a3;
    --border: rgba(255,255,255,0.08); --accent:#ffffff; --accent-text:#171717;
    --bar:rgba(15,17,22,0.6);
    --shadow: 0 10px 40px -10px rgba(0,0,0,0.5);
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html{-webkit-text-size-adjust:100%;}
  body{
    font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Noto Sans",Arial,sans-serif;
    background: var(--bg);
    background-attachment: fixed;
    color:var(--text); font-weight:300;
    transition:background .3s,color .3s; min-height:100vh;
    padding-top:56px; padding-bottom: 90px; /* 给底部音乐按钮留出空间 */
  }

  /* ============ 顶部工具栏 ============ */
  .toolbar{
    position:fixed; top:0; left:0; right:0; z-index:999;
    height:56px; display:flex; align-items:center; gap:6px;
    padding:0 14px;
    background:var(--bar);
    -webkit-backdrop-filter:var(--blur); backdrop-filter:var(--blur);
    border-bottom:1px solid var(--border);
    transition:background .3s,border-color .3s;
  }
  .toolbar .spacer{flex:1;}
  .tbtn{
    display:flex; align-items:center; gap:5px;
    padding:7px 12px; border-radius:9px;
    border:1px solid var(--border); background:transparent;
    color:var(--text); cursor:pointer;
    font-size:12px; font-family:ui-monospace,SFMono-Regular,Menlo,monospace;
    letter-spacing:.04em; white-space:nowrap;
    transition:all .2s;
  }
  .tbtn:hover{background:var(--border);}
  .tbtn:active{transform:scale(.96);}
  .tbtn.on{background:var(--accent); color:var(--accent-text); border-color:var(--accent);}
  .tbtn .ico{font-size:13px; line-height:1;}
  .edit-only{display:none !important;}
  body.editing .edit-only{display:flex !important;}

  /* ============ 顶部信息 ============ */
  header{max-width:720px; margin:0 auto; padding:28px 20px 0;}
  .brand{display:flex; align-items:center; gap:12px;}
  .brand-text{display:flex; flex-direction:column; gap:3px; min-width:0;}
  .brand-name{
    font-size:15px; font-weight:500; letter-spacing:.08em;
    text-transform:uppercase; line-height:1.3;
  }
  .brand-sub{
    font-size:11px; color:var(--text-sub);
    font-family:ui-monospace,SFMono-Regular,Menlo,monospace; line-height:1.3;
  }

  /* ============ 图片容器 ============ */
  .img-wrap{overflow:hidden; background:var(--border); flex:none; position: relative;}
  .img-wrap img{width:100%; height:100%; object-fit:cover; display:block;}
  .avatar-wrap{width:42px; height:42px; border-radius:14px; box-shadow: var(--shadow);}
  .cover-wrap{
    width:100%; aspect-ratio:16/9; border-radius:16px;
    margin-bottom:18px; box-shadow: var(--shadow);
  }

  /* ============ 导航 ============ */
  nav{
    display:flex; gap:4px; padding:5px;
    background:var(--card); border-radius:12px;
    max-width:720px; margin:18px auto 0;
    overflow-x:auto; -webkit-overflow-scrolling:touch;
    scrollbar-width:none;
    box-shadow: var(--shadow);
    backdrop-filter: var(--blur);
    border: 1px solid var(--border);
  }
  nav::-webkit-scrollbar{display:none;}
  .nav-btn{
    flex:1 0 auto; padding:7px 12px; border:none; border-radius:9px;
    background:transparent; color:var(--text-sub); cursor:pointer;
    font-size:11px; font-family:ui-monospace,SFMono-Regular,Menlo,monospace;
    letter-spacing:.06em; white-space:nowrap;
    transition:all .2s;
  }
  .nav-btn.active{background:var(--accent); color:var(--accent-text); font-weight:600;}
  .nav-btn:hover:not(.active){background:color-mix(in srgb,var(--bg) 65%,transparent);}

  /* ============ 内容区 ============ */
  main{max-width:720px; margin:0 auto; padding:20px 20px 40px;}
  .panel{display:none;}
  .panel.active{display:block; animation:fade .3s ease;}
  @keyframes fade{from{opacity:0;transform:translateY(8px);}to{opacity:1;transform:none;}}

  .card{
    position:relative;
    background:var(--card); border:1px solid var(--border);
    border-radius:18px; padding:24px; margin-bottom:16px;
    transition:all .3s;
    box-shadow: var(--shadow);
    backdrop-filter: var(--blur);
  }
  .card:hover { transform: translateY(-2px); box-shadow: 0 15px 45px -10px rgba(0,0,0,0.15); }
  .card h2{font-size:20px; font-weight:500; margin-bottom:10px; letter-spacing:.01em; line-height:1.4;}
  .card h3{
    font-size:12px; font-weight:600; margin-bottom:8px;
    color:var(--text-sub); text-transform:uppercase; letter-spacing:.1em;
  }
  .card p{font-size:14px; line-height:1.8; color:var(--text-sub);}
  .card p + p{margin-top:10px;}

  .tags{display:flex; flex-wrap:wrap; gap:8px; margin-top:14px;}
  .tag{
    padding:4px 12px; border-radius:999px;
    border:1px solid var(--border); font-size:12px;
    color:var(--text-sub);
    font-family:ui-monospace,SFMono-Regular,Menlo,monospace;
    background: rgba(0,0,0,0.02);
  }
  html.dark .tag { background: rgba(255,255,255,0.03); }

  /* 卡片删除按钮 */
  .card-del{
    display:none; position:absolute; top:12px; right:12px;
    width:26px; height:26px; border-radius:8px;
    border:1px solid var(--border); background:var(--card);
    color:var(--text-sub); cursor:pointer; font-size:16px;
    line-height:1; align-items:center; justify-content:center;
    z-index:2; transition: all 0.2s;
  }
  .card-del:hover{background:#e5484d; color:#fff; border-color:#e5484d;}
  body.editing .card-del{display:flex;}

  /* 添加卡片按钮 */
  .add-card{
    display:none; width:100%; padding:16px;
    border:2px dashed var(--border); border-radius:16px;
    background:transparent; color:var(--text-sub); cursor:pointer;
    font-size:12px; font-family:ui-monospace,SFMono-Regular,Menlo,monospace;
    letter-spacing:.06em; margin-bottom:16px;
    transition:all .2s;
  }
  .add-card:hover{background:var(--card); border-color:var(--text-sub); backdrop-filter: var(--blur);}
  body.editing .add-card{display:block;}

  /* 底部 */
  footer{
    max-width:720px; margin:0 auto; padding:0 20px 20px;
    display:flex; justify-content:space-between; gap:12px;
    font-size:10px; color:var(--text-sub);
    font-family:ui-monospace,SFMono-Regular,Menlo,monospace;
    letter-spacing:.06em;
  }

  /* ============ 音乐播放器 ============ */
  .music-player {
    position: fixed; bottom: 24px; right: 24px; z-index: 1000;
    width: 50px; height: 50px; border-radius: 50%;
    background: var(--card); border: 1px solid var(--border);
    box-shadow: 0 8px 30px rgba(0,0,0,0.15);
    backdrop-filter: var(--blur); -webkit-backdrop-filter: var(--blur);
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; transition: all .3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    color: var(--text); font-size: 22px;
  }
  .music-player:hover { transform: scale(1.1); }
  .music-player.playing {
    animation: spin 4s linear infinite;
    background: var(--accent); color: var(--accent-text); border-color: var(--accent);
  }
  @keyframes spin { 100% { transform: rotate(360deg); } }

  /* ============ 编辑状态提示 ============ */
  body.editing [data-edit]{
    outline:1px dashed color-mix(in srgb,var(--text-sub) 45%,transparent);
    outline-offset:4px; border-radius:3px;
    cursor:text; transition:background .15s,outline-color .15s;
  }
  body.editing [data-edit]:hover{
    background:color-mix(in srgb,var(--accent) 7%,transparent);
    outline-color:var(--accent);
  }
  body.editing [data-edit]:focus{
    background:color-mix(in srgb,var(--accent) 7%,transparent);
    outline:1px solid var(--accent);
    outline-offset:4px;
  }
  body.editing [data-edit]:empty::before{
    content:"点击输入…";
    color:var(--text-sub); opacity:.45;
  }

  /* 图片 hover 遮罩 */
  body.editing .img-wrap{position:relative; cursor:pointer;}
  body.editing .img-wrap::after{
    content:"更换图片";
    position:absolute; inset:0;
    display:flex; align-items:center; justify-content:center;
    background:rgba(0,0,0,.58); color:#fff;
    font-size:11px; font-family:ui-monospace,monospace;
    letter-spacing:.06em; border-radius: inherit;
    opacity:0; transition:opacity .18s; pointer-events:none;
  }
  body.editing .img-wrap:hover::after{opacity:1;}
  body.editing .avatar-wrap::after{content:"换"; font-size:12px;}

  /* 提示气泡 */
  .toast{
    position:fixed; left:50%; bottom:90px; z-index:1000;
    transform:translateX(-50%) translateY(14px);
    padding:9px 16px; border-radius:10px;
    background:var(--accent); color:var(--accent-text);
    font-size:12px; font-family:ui-monospace,monospace;
    letter-spacing:.05em;
    opacity:0; pointer-events:none;
    transition:opacity .2s, transform .2s;
    box-shadow: 0 10px 30px rgba(0,0,0,0.2);
  }
  .toast.show{opacity:1; transform:translateX(-50%) translateY(0);}

  /* ============ 手机适配 ============ */
  @media (max-width:640px){
    .tbtn{padding:7px 9px;}
    .tbtn .label{display:none;}
    .tbtn .ico{font-size:15px;}
    header{padding-top:22px;}
    .brand-name{font-size:13px;}
    .card{padding:18px; border-radius:16px;}
    .card h2{font-size:18px;}
    nav{border-radius:11px;}
    .nav-btn{font-size:10px; padding:7px 9px;}
    .music-player { bottom: 16px; right: 16px; width: 46px; height: 46px; font-size: 20px; }
    .toast { bottom: 80px; }
  }
</style>
</head>
<body>

<!-- ==================== 音乐播放器 ==================== -->
<!-- 注意：将 src 替换为你自己的音乐链接，或者保留此默认无版权音乐 -->
<audio id="bgMusic" src="https://www.soundhelix.com/examples/mp3/SoundHelix-Song-1.mp3" loop preload="none"></audio>
<div class="music-player" id="musicPlayer" title="播放/暂停音乐">
  <span id="musicIcon">♪</span>
</div>

<!-- ==================== 工具栏 ==================== -->
<div class="toolbar">
  <button class="tbtn" id="btnEdit" title="切换编辑模式">
    <span class="ico">✎</span><span class="label">编辑</span>
  </button>
  <button class="tbtn edit-only" id="btnExport" title="导出为 HTML 文件">
    <span class="ico">⬇</span><span class="label">导出</span>
  </button>
  <button class="tbtn edit-only" id="btnImport" title="从 HTML 文件导入">
    <span class="ico">⬆</span><span class="label">导入</span>
  </button>
  <button class="tbtn edit-only" id="btnReset" title="恢复初始内容">
    <span class="ico">⟲</span><span class="label">重置</span>
  </button>
  <span class="spacer"></span>
  <button class="tbtn" id="btnTheme" title="切换深浅色">
    <span class="ico" id="themeIcon">◐</span><span class="label" id="themeLabel">深色</span>
  </button>
</div>

<!-- ==================== 页面主体 ==================== -->
<div id="app">

  <header>
    <div class="brand">
      <div class="img-wrap avatar-wrap">
        <img data-edit-img
             src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23262626'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23ffffff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E"
             alt="头像">
      </div>
      <div class="brand-text">
        <span class="brand-name" data-edit>你的名字</span>
        <span class="brand-sub" data-edit>你的副标题 / 标签</span>
      </div>
    </div>
  </header>

  <nav id="nav">
    <button class="nav-btn active" data-target="panel-0">
      <span data-edit-nav>01 · WELCOME</span>
    </button>
    <button class="nav-btn" data-target="panel-1">
      <span data-edit-nav>02 · INFO</span>
    </button>
    <button class="nav-btn" data-target="panel-2">
      <span data-edit-nav>03 · STYLE</span>
    </button>
    <button class="nav-btn" data-target="panel-3">
      <span data-edit-nav>04 · CATEGORIES</span>
    </button>
  </nav>

  <main>

    <!-- 栏目 01 -->
    <section class="panel active" id="panel-0">
      <div class="card">
        <div class="img-wrap cover-wrap">
          <img data-edit-img
               src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Cdefs%3E%3ClinearGradient id='g' x1='0' y1='0' x2='1' y2='1'%3E%3Cstop offset='0' stop-color='%23333'/%3E%3Cstop offset='1' stop-color='%230c0d0e'/%3E%3C/linearGradient%3E%3C/defs%3E%3Crect width='160' height='90' fill='url(%23g)'/%3E%3Ctext x='80' y='49' font-size='7' fill='%23888' text-anchor='middle' font-family='monospace'%3ECLICK TO CHANGE%3C/text%3E%3C/svg%3E"
               alt="封面">
        </div>
        <h2 data-edit>欢迎来到我的主页</h2>
        <p data-edit>这里是一段自我介绍。点击这段文字就能直接修改，改成你自己的名字、兴趣，或者想对访客说的话。</p>
      </div>

      <div class="card">
        <h3 data-edit>关键词</h3>
        <div class="tags">
          <span class="tag" data-edit>标签一</span>
          <span class="tag" data-edit>标签二</span>
          <span class="tag" data-edit>标签三</span>
          <span class="tag" data-edit>标签四</span>
        </div>
      </div>

      <button class="add-card" onclick="addCard('panel-0')">＋ 添加一张卡片</button>
    </section>

    <!-- 栏目 02 -->
    <section class="panel" id="panel-1">
      <div class="card">
        <h3 data-edit>基本信息</h3>
        <p data-edit>这里可以写你的基本信息，比如职业、所在地、联系方式等等。改成你自己的就好。</p>
      </div>
      <div class="card">
        <h3 data-edit>更多说明</h3>
        <p data-edit>可以继续添加卡片，每张卡片是一段独立的内容。</p>
      </div>
      <button class="add-card" onclick="addCard('panel-1')">＋ 添加一张卡片</button>
    </section>

    <!-- 栏目 03 -->
    <section class="panel" id="panel-2">
      <div class="card">
        <h3 data-edit>我的风格</h3>
        <p data-edit>这里可以写你的风格、态度或者喜欢的东西。改成你自己的文字就行。</p>
      </div>
      <button class="add-card" onclick="addCard('panel-2')">＋ 添加一张卡片</button>
    </section>

    <!-- 栏目 04 -->
    <section class="panel" id="panel-3">
      <div class="card">
        <h3 data-edit>分类内容</h3>
        <p data-edit>这里可以放你的分类信息，比如作品、链接、推荐等等。自由发挥。</p>
      </div>
      <button class="add-card" onclick="addCard('panel-3')">＋ 添加一张卡片</button>
    </section>

  </main>

  <footer>
    <span data-edit>MY PAGE · 2026</span>
    <span data-edit>STATUS: ONLINE</span>
  </footer>

</div>

<div class="toast" id="toast"></div>
<input type="file" id="fileInput" accept="image/*" hidden>
<input type="file" id="importInput" accept=".html,.htm,text/html" hidden>

<script>
(function(){
  'use strict';

  var KEY_CONTENT = 'myPage.content.v1';
  var KEY_THEME   = 'myPage.theme.v1';

  var app         = document.getElementById('app');
  var nav         = document.getElementById('nav');
  var fileInput   = document.getElementById('fileInput');
  var importInput = document.getElementById('importInput');
  var toastEl     = document.getElementById('toast');
  var musicPlayer = document.getElementById('musicPlayer');
  var bgMusic     = document.getElementById('bgMusic');
  var musicIcon   = document.getElementById('musicIcon');

  var editMode    = false;
  var currentImg  = null;
  var navEditEl   = null;
  var DEFAULT_HTML = app.innerHTML;

  /* ============ 音乐播放控制 ============ */
  musicPlayer.addEventListener('click', function() {
    if (bgMusic.paused) {
      bgMusic.play().then(function() {
        musicPlayer.classList.add('playing');
        musicIcon.textContent = '❚❚'; // 暂停符号
        toast('音乐播放中');
      }).catch(function(err) {
        toast('播放失败，请检查音乐链接');
        console.error('播放失败:', err);
      });
    } else {
      bgMusic.pause();
      musicPlayer.classList.remove('playing');
      musicIcon.textContent = '♪'; // 音符符号
      toast('音乐已暂停');
    }
  });

  /* ============ 恢复已保存内容 ============ */
  try{
    var saved = localStorage.getItem(KEY_CONTENT);
    if(saved){ app.innerHTML = saved; }
  }catch(e){}

  /* ============ 主题 ============ */
  function applyTheme(dark){
    document.documentElement.classList.toggle('dark', dark);
    document.getElementById('themeIcon').textContent  = dark ? '☀' : '◐';
    document.getElementById('themeLabel').textContent = dark ? '浅色' : '深色';
    try{ localStorage.setItem(KEY_THEME, dark ? 'dark' : 'light'); }catch(e){}
  }
  var savedTheme = 'light';
  try{ savedTheme = localStorage.getItem(KEY_THEME) || 'light'; }catch(e){}
  applyTheme(savedTheme === 'dark');

  document.getElementById('btnTheme').addEventListener('click', function(){
    applyTheme(!document.documentElement.classList.contains('dark'));
  });

  /* ============ 提示气泡 ============ */
  var toastTimer;
  function toast(msg){
    toastEl.textContent = msg;
    toastEl.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(function(){ toastEl.classList.remove('show'); }, 1600);
  }

  /* ============ 序列化 ============ */
  function serialize(){
    var clone = app.cloneNode(true);
    clone.querySelectorAll('[contenteditable]').forEach(function(el){
      el.removeAttribute('contenteditable');
    });
    clone.querySelectorAll('.panel').forEach(function(p, i){
      p.classList.toggle('active', i === 0);
    });
    clone.querySelectorAll('.nav-btn').forEach(function(b, i){
      b.classList.toggle('active', i === 0);
    });
    return clone.innerHTML;
  }

  /* ============ 保存 ============ */
  var saveTimer;
  function scheduleSave(){
    clearTimeout(saveTimer);
    saveTimer = setTimeout(save, 400);
  }
  function save(){
    try{
      localStorage.setItem(KEY_CONTENT, serialize());
    }catch(err){
      toast('保存失败：存储空间可能已满');
    }
  }

  /* ============ 编辑模式 ============ */
  function applyEditableState(){
    document.querySelectorAll('[data-edit]').forEach(function(el){
      if(editMode){
        el.setAttribute('contenteditable', 'plaintext-only');
        if(el.contentEditable !== 'plaintext-only'){
          el.setAttribute('contenteditable', 'true');
        }
      }else{
        el.removeAttribute('contenteditable');
      }
    });
  }

  function setEdit(on){
    editMode = on;
    document.body.classList.toggle('editing', on);
    applyEditableState();
    var btn = document.getElementById('btnEdit');
    btn.classList.toggle('on', on);
    btn.querySelector('.ico').textContent   = on ? '✓' : '✎';
    btn.querySelector('.label').textContent = on ? '完成' : '编辑';
    if(on) toast('编辑已开启 · 点文字直接改');
    else { save(); toast('已保存'); }
  }

  document.getElementById('btnEdit').addEventListener('click', function(){ setEdit(!editMode); });

  app.addEventListener('input', function(){ if(editMode) scheduleSave(); });

  /* ============ 标签切换 ============ */
  nav.addEventListener('click', function(e){
    var btn = e.target.closest('.nav-btn');
    if(!btn || navEditEl) return;
    var target = btn.getAttribute('data-target');
    document.querySelectorAll('.panel').forEach(function(p){ p.classList.toggle('active', p.id === target); });
    document.querySelectorAll('.nav-btn').forEach(function(b){ b.classList.toggle('active', b === btn); });
  });

  /* ============ 导航标签双击改名 ============ */
  nav.addEventListener('dblclick', function(e){
    if(!editMode) return;
    var el = e.target.closest('[data-edit-nav]');
    if(!el) return;
    el.setAttribute('contenteditable', 'plaintext-only');
    el.focus();
    var range = document.createRange(); range.selectNodeContents(el);
    var sel = window.getSelection(); sel.removeAllRanges(); sel.addRange(range);
    navEditEl = el;
  });
  document.addEventListener('mousedown', function(e){
    if(navEditEl && !navEditEl.contains(e.target)){
      navEditEl.removeAttribute('contenteditable'); navEditEl = null; save();
    }
  });

  /* ============ 图片压缩 ============ */
  function compressImage(file, maxSize, quality){
    maxSize = maxSize || 1200; quality = quality || 0.82;
    return new Promise(function(resolve, reject){
      var reader = new FileReader();
      reader.onload = function(){
        var img = new Image();
        img.onload = function(){
          var w = img.naturalWidth, h = img.naturalHeight;
          var scale = Math.min(1, maxSize / Math.max(w, h));
          w = Math.max(1, Math.round(w * scale)); h = Math.max(1, Math.round(h * scale));
          var canvas = document.createElement('canvas');
          canvas.width = w; canvas.height = h;
          canvas.getContext('2d').drawImage(img, 0, 0, w, h);
          var out = '';
          try{ out = canvas.toDataURL('image/webp', quality); }catch(err){}
          if(!out || out.indexOf('data:image/webp') !== 0) out = canvas.toDataURL('image/jpeg', quality);
          resolve(out);
        };
        img.onerror = reject; img.src = reader.result;
      };
      reader.onerror = reject; reader.readAsDataURL(file);
    });
  }

  /* ============ 点击图片替换 ============ */
  app.addEventListener('click', function(e){
    if(!editMode) return;
    var img = e.target.closest('img[data-edit-img]');
    if(img){ e.preventDefault(); currentImg = img; fileInput.value = ''; fileInput.click(); }
  });

  fileInput.addEventListener('change', function(){
    var file = fileInput.files && fileInput.files[0];
    if(!file || !currentImg) return;
    toast('正在处理图片…');
    compressImage(file).then(function(dataUrl){
      currentImg.src = dataUrl; save(); toast('图片已更新');
    }).catch(function(){ toast('图片读取失败'); });
  });

  /* ============ 添加 / 删除卡片 ============ */
  window.addCard = function(panelId){
    var panel = document.getElementById(panelId);
    if(!panel) return;
    var card = document.createElement('div');
    card.className = 'card';
    card.innerHTML = '<button class="card-del" contenteditable="false" title="删除这张卡片">×</button><h3 data-edit>新卡片标题</h3><p data-edit>点击这里输入内容。</p>';
    panel.insertBefore(card, panel.querySelector('.add-card'));
    applyEditableState(); save();
    var target = card.querySelector('h3');
    if(target){ target.focus(); var range = document.createRange(); range.selectNodeContents(target); var sel = window.getSelection(); sel.removeAllRanges(); sel.addRange(range); }
  };

  app.addEventListener('click', function(e){
    var del = e.target.closest('.card-del');
    if(!del || !editMode) return;
    e.preventDefault();
    if(confirm('确定删除这张卡片吗？')){ del.closest('.card').remove(); save(); toast('卡片已删除'); }
  });

  /* ============ 导出 HTML ============ */
  document.getElementById('btnExport').addEventListener('click', function(){
    var clone = document.documentElement.cloneNode(true);
    clone.querySelector('body').classList.remove('editing');
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    clone.querySelectorAll('.panel').forEach(function(p, i){ p.classList.toggle('active', i === 0); });
    clone.querySelectorAll('.nav-btn').forEach(function(b, i){ b.classList.toggle('active', i === 0); });
    var editBtn = clone.querySelector('#btnEdit');
    if(editBtn){ editBtn.classList.remove('on'); var ic = editBtn.querySelector('.ico'); if(ic) ic.textContent = '✎'; var lb = editBtn.querySelector('.label'); if(lb) lb.textContent = '编辑'; }
    var blob = new Blob(['<!DOCTYPE html>\n' + clone.outerHTML], {type:'text/html;charset=utf-8'});
    var url  = URL.createObjectURL(blob); var a = document.createElement('a');
    a.href = url; a.download = 'my-page.html'; document.body.appendChild(a); a.click(); document.body.removeChild(a);
    setTimeout(function(){ URL.revokeObjectURL(url); }, 2000); toast('已导出 my-page.html');
  });

  /* ============ 导入 HTML ============ */
  document.getElementById('btnImport').addEventListener('click', function(){ importInput.value = ''; importInput.click(); });
  importInput.addEventListener('change', function(){
    var file = importInput.files && importInput.files[0]; if(!file) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      try{
        var doc = new DOMParser().parseFromString(ev.target.result, 'text/html');
        var imported = doc.getElementById('app');
        if(!imported) throw new Error('no app');
        app.innerHTML = imported.innerHTML; applyEditableState(); save(); toast('导入成功');
      }catch(err){ alert('导入失败：这个文件里没有找到可识别的内容。'); }
    };
    reader.readAsText(file);
  });

  /* ============ 重置 ============ */
  document.getElementById('btnReset').addEventListener('click', function(){
    if(!confirm('确定要恢复初始内容吗？\n你所有修改都会被清空，且无法撤销。')) return;
    app.innerHTML = DEFAULT_HTML; applyEditableState(); save(); toast('已恢复初始内容');
  });

  /* ============ 初始化 ============ */
  setEdit(false);
})();
</script>
</body>
</html>