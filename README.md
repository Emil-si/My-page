<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>犬系痕 - 您想我了</title>
<style>
  :root{
    --bg: #0a0a0a;
    --card-bg: rgba(255,255,255,0.04);
    --glass-border: rgba(255,255,255,0.12);
    --glass-blur: blur(16px);
    --text: #e5e5e5;
    --text-sub: #888888;
    --accent: #ffffff; /* 默认高冷白 */
    --accent-text: #000000;
    --bar: rgba(10,10,10,0.8);
  }
  html.light{
    --bg: #f2f2f2;
    --card-bg: rgba(255,255,255,0.7);
    --glass-border: rgba(0,0,0,0.1);
    --text: #111111;
    --text-sub: #666666;
    --accent: #111111;
    --accent-text: #ffffff;
    --bar: rgba(242,242,242,0.8);
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html{-webkit-text-size-adjust:100%;}
  body{
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
    background: var(--bg);
    background-image: radial-gradient(circle at 10% 10%, rgba(255,255,255,0.03), transparent 40%),
                      radial-gradient(circle at 90% 80%, rgba(255,255,255,0.03), transparent 40%);
    background-attachment: fixed;
    color:var(--text); min-height:100vh;
    padding-top:50px; padding-bottom:100px;
    transition:background .3s, color .3s;
    letter-spacing: 0.02em;
  }

  /* ============ 顶部工具栏 ============ */
  .toolbar{
    position:fixed; top:0; left:0; right:0; z-index:999;
    height:50px; display:flex; align-items:center; gap:8px; padding:0 16px;
    background:var(--bar); backdrop-filter:var(--glass-blur); -webkit-backdrop-filter:var(--glass-blur);
    border-bottom:1px solid var(--glass-border);
  }
  .toolbar .spacer{flex:1;}
  .tbtn{
    display:flex; align-items:center; justify-content:center; gap:5px; padding:6px 12px; border-radius:20px;
    border:1px solid var(--glass-border); background:rgba(255,255,255,0.05);
    color:var(--text); cursor:pointer; font-size:12px; font-family:monospace; transition:all .2s;
    backdrop-filter:blur(8px);
  }
  .tbtn:active{transform:scale(.95);}
  .tbtn.on{background:var(--accent); color:var(--accent-text); border-color:var(--accent);}
  .tbtn .ico{font-size:14px; line-height:1;}
  .edit-only{display:none !important;}
  body.editing .edit-only{display:flex !important;}
  
  /* 颜色选择器 */
  .color-picker-wrap { position:relative; overflow:hidden; width:34px; height:34px; border-radius:50%; border:1px solid var(--glass-border); display:flex; align-items:center; justify-content:center; background:rgba(255,255,255,0.05); }
  .color-picker-wrap input { position:absolute; width:50px; height:50px; top:-10px; left:-10px; opacity:0; cursor:pointer; }

  /* ============ 顶部导航栏 ============ */
  .top-nav {
    max-width: 640px; margin: 0 auto; display: flex; gap: 6px; padding: 12px 16px;
    position: sticky; top: 50px; z-index: 998;
    background: var(--bar); backdrop-filter: var(--glass-blur); border-bottom: 1px solid var(--glass-border);
    overflow-x: auto; scrollbar-width: none;
  }
  .top-nav::-webkit-scrollbar { display: none; }
  .nav-link {
    flex: 1 0 auto; text-align: center; padding: 8px 16px; border-radius: 20px;
    background: rgba(255,255,255,0.03); border: 1px solid var(--glass-border);
    backdrop-filter: var(--glass-blur); color: var(--text-sub);
    text-decoration: none; font-size: 12px; font-family: monospace; letter-spacing: 0.1em;
    transition: all 0.2s ease; cursor: pointer; white-space: nowrap;
  }
  .nav-link.active { background: var(--accent); color: var(--accent-text); border-color: var(--accent); }

  /* ============ 主卡片容器 ============ */
  .container { max-width:640px; margin: 0 auto; padding: 16px; }
  
  .main-card {
    background: var(--card-bg);
    backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border);
    border-radius: 24px; padding: 0 0 24px 0; overflow: hidden;
    box-shadow: 0 20px 40px rgba(0,0,0,0.3);
  }

  /* 封面与头像 */
  .cover-wrap { width:100%; height:220px; background:#1a1a1a; position:relative; overflow:hidden; }
  .cover-wrap img { width:100%; height:100%; object-fit:cover; display:block; filter: grayscale(20%); }
  
  .avatar-wrap { 
    width:80px; height:80px; border-radius:50%; border:3px solid var(--bg); 
    position:absolute; bottom:-40px; left:24px; background:var(--bg); 
    overflow:hidden; box-shadow:0 4px 20px rgba(0,0,0,0.5); z-index: 2;
  }
  .avatar-wrap img { width:100%; height:100%; object-fit:cover; }

  /* 标签与状态栏（封面内） */
  .cover-tags { position: absolute; bottom: 16px; right: 24px; display: flex; gap: 8px; }
  .status-badge { background: rgba(0,0,0,0.7); border: 1px solid rgba(255,255,255,0.2); color: #fff; padding: 4px 12px; border-radius: 4px; font-size: 10px; font-family: monospace; letter-spacing: 0.1em; backdrop-filter: blur(4px); }

  /* 个人信息区 */
  .profile-info { padding: 48px 24px 0; }
  .name-row { display: flex; align-items: center; gap: 12px; margin-bottom: 12px; }
  .name { font-size: 26px; font-weight: 700; letter-spacing: 0.02em; }
  .verified-badge { background: transparent; border: 1px solid var(--glass-border); color: var(--text-sub); padding: 2px 8px; border-radius: 4px; font-size: 10px; font-family: monospace; letter-spacing: 0.1em; }
  
  .meta-info { font-size: 14px; color: var(--text-sub); margin-bottom: 8px; font-family: monospace; line-height: 1.6; }
  .bio-text { font-size: 15px; line-height: 1.6; margin-bottom: 24px; }

  /* 标签云 */
  .tags-cloud { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 24px; }
  .tag { background: rgba(255,255,255,0.05); border: 1px solid var(--glass-border); padding: 6px 14px; border-radius: 8px; font-size: 12px; font-family: monospace; color: var(--text-sub); }
  
  /* 引言 */
  .quote { font-size: 13px; color: var(--text-sub); font-style: italic; margin-bottom: 24px; border-left: 2px solid var(--glass-border); padding-left: 12px; }

  /* 位置信息 */
  .location-info { display: flex; align-items: center; gap: 6px; font-size: 12px; color: var(--text-sub); margin-bottom: 8px; }
  .location-sub { font-size: 12px; color: var(--text-sub); margin-bottom: 24px; }

  /* 底部大按钮 */
  .action-btn {
    display: flex; justify-content: center; align-items: center; gap: 8px;
    width: 100%; padding: 16px; border-radius: 12px;
    background: var(--accent); color: var(--accent-text);
    border: none; font-size: 16px; font-weight: 600; cursor: pointer;
    transition: all 0.2s;
  }
  .action-btn:hover { transform: translateY(-2px); opacity: 0.9; }

  /* 底部版权信息 */
  .footer { text-align: center; font-size: 10px; color: var(--text-sub); padding: 24px 0; font-family: monospace; letter-spacing: 0.1em; }

  /* ============ 音乐播放器 ============ */
  .music-player {
    position: fixed; bottom: 30px; right: 20px; z-index: 1000;
    width: 56px; height: 56px; border-radius: 50%;
    background: var(--card-bg); backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border);
    box-shadow: 0 8px 30px rgba(0,0,0,0.4);
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; transition: all .3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    color: var(--text); font-size: 24px;
  }
  .music-player:hover { transform: scale(1.1); }
  .music-player.playing { animation: spin 4s linear infinite; background: var(--accent); color: var(--accent-text); border-color: var(--accent); }
  @keyframes spin { 100% { transform: rotate(360deg); } }

  /* ============ 编辑状态样式 ============ */
  body.editing [data-edit] { outline: 1px dashed var(--accent); outline-offset: 4px; border-radius: 4px; cursor: text; }
  body.editing [data-edit]:focus { background: rgba(255,255,255,0.05); outline: 1px solid var(--accent); }
  body.editing .cover-wrap:hover::after, body.editing .avatar-wrap:hover::after {
    content:"点击换图"; position:absolute; inset:0; display:flex; align-items:center; justify-content:center;
    background:rgba(0,0,0,0.6); color:#fff; font-size:12px; z-index:5;
  }
  .toast { position:fixed; left:50%; bottom:100px; transform:translateX(-50%) translateY(20px); background:var(--accent); color:var(--accent-text); padding:10px 20px; border-radius:20px; font-size:13px; opacity:0; pointer-events:none; transition:all 0.3s; z-index:2000; }
  .toast.show { opacity:1; transform:translateX(-50%) translateY(0); }
</style>
</head>
<body>

<!-- 音乐播放器 -->
<audio id="bgMusic" src="" loop preload="none"></audio>
<div class="music-player" id="musicPlayer" title="播放/暂停音乐">
  <span id="musicIcon">♪</span>
</div>

<!-- 顶部工具栏 -->
<div class="toolbar">
  <button class="tbtn" id="btnEdit"><span class="ico">✎</span><span class="label">编辑</span></button>
  <button class="tbtn edit-only" id="btnExport"><span class="ico">⬇</span><span class="label">导出</span></button>
  <button class="tbtn edit-only" id="btnReset"><span class="ico">⟲</span><span class="label">重置</span></button>
  <span class="spacer"></span>
  <div class="color-picker-wrap edit-only" title="修改主题颜色">
    <input type="color" id="colorPicker" value="#ffffff">
  </div>
  <button class="tbtn" id="btnTheme"><span class="ico" id="themeIcon">☀</span></button>
</div>

<!-- 顶部导航栏 -->
<div class="top-nav" id="topNav">
  <a class="nav-link active" data-target="panel-0">STEP: WELCOME</a>
  <a class="nav-link" data-target="panel-1">PERSONAL INFO</a>
  <a class="nav-link" data-target="panel-2">MY STYLE</a>
  <a class="nav-link" data-target="panel-3">CATEGORIES</a>
</div>

<!-- 主内容区 -->
<div class="container" id="app">
  
  <div class="main-card">
    <!-- 封面图 -->
    <div class="cover-wrap">
      <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Crect width='160' height='90' fill='%23111'/%3E%3Ctext x='80' y='49' font-size='8' fill='%23444' text-anchor='middle' font-family='monospace'%3E点击更换封面图%3C/text%3E%3C/svg%3E" alt="封面">
      <div class="cover-tags">
        <div class="status-badge">STATUS: ONLINE</div>
      </div>
    </div>

    <!-- 头像 -->
    <div class="avatar-wrap">
      <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23222'/%3E%3Ctext x='50' y='69' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像">
    </div>

    <!-- 个人信息 -->
    <div class="profile-info">
      <div class="name-row">
        <div class="name" data-edit>犬系痕</div>
        <div class="verified-badge">VERIFIED</div>
      </div>
      <div class="meta-info" data-edit>19 LeH 金牛ENTP。</div>
      <div class="bio-text" data-edit>小姐，这是我的名片。</div>
      
      <div class="tags-cloud">
        <span class="tag" data-edit>#白话疯犬</span>
        <span class="tag" data-edit>#媚骨勾引</span>
        <span class="tag" data-edit>#暧昧缱绻</span>
        <span class="tag" data-edit>#轻浮牛郎</span>
        <span class="tag" data-edit>#阴湿艳鬼</span>
        <span class="tag" data-edit>#爱食掺揉</span>
      </div>

      <div class="quote" data-edit>别用风格框住我吧，您该亲自来见我</div>

      <div class="location-info"><span>📍</span> <span data-edit>IP属地：雾山影</span></div>
      <div class="location-sub" data-edit>小姐，想见我的话别走错地方啊</div>

      <button class="action-btn" id="btnStart">
        <span data-edit>START / 开始了解</span> <span>→</span>
      </button>
    </div>
  </div>

  <div class="footer" data-edit>STEP 01 OF 04 · PROCEED TO PERSONAL INFORMATION</div>
</div>

<div class="toast" id="toast"></div>
<input type="file" id="fileInput" accept="image/*" hidden>

<script>
(function(){
  'use strict';
  var KEY_CONTENT = 'myPage.darkcard.v1';
  var KEY_THEME   = 'myPage.theme.v1';
  var KEY_COLOR   = 'myPage.color.v1';
  var app = document.getElementById('app');
  var fileInput = document.getElementById('fileInput');
  var toastEl = document.getElementById('toast');
  var currentImg = null;
  var editMode = false;
  var DEFAULT_HTML = app.innerHTML;

  // 初始化加载
  try {
    var saved = localStorage.getItem(KEY_CONTENT);
    if(saved) app.innerHTML = saved;
    var savedTheme = localStorage.getItem(KEY_THEME) || 'dark'; // 默认深色
    var savedColor = localStorage.getItem(KEY_COLOR) || '#ffffff';
    applyTheme(savedTheme === 'dark');
    applyColor(savedColor);
    document.getElementById('colorPicker').value = savedColor;
  } catch(e){}

  // 主题切换 (dark / light)
  function applyTheme(dark){
    document.documentElement.classList.toggle('light', !dark);
    document.getElementById('themeIcon').textContent = dark ? '☀' : '◐';
    localStorage.setItem(KEY_THEME, dark ? 'dark' : 'light');
    // 如果修改了主题，将按钮颜色同步
    var colorInput = document.getElementById('colorPicker');
    applyColor(colorInput.value);
  }
  document.getElementById('btnTheme').addEventListener('click', function(){
    var isDark = !document.documentElement.classList.contains('light');
    applyTheme(!isDark);
  });

  // 颜色切换
  function applyColor(color){
    document.documentElement.style.setProperty('--accent', color);
    // 自动计算文字颜色（黑白判断）
    var hex = color.replace('#', '');
    if (hex.length === 3) hex = hex.split('').map(function(c){return c+c}).join('');
    var r = parseInt(hex.substr(0,2), 16), g = parseInt(hex.substr(2,2), 16), b = parseInt(hex.substr(4,2), 16);
    var brightness = (r * 299 + g * 587 + b * 114) / 1000;
    document.documentElement.style.setProperty('--accent-text', brightness > 128 ? '#000000' : '#ffffff');
    localStorage.setItem(KEY_COLOR, color);
  }
  document.getElementById('colorPicker').addEventListener('input', function(e){ applyColor(e.target.value); });

  // 提示气泡
  var toastTimer;
  function toast(msg){
    toastEl.textContent = msg;
    toastEl.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(function(){ toastEl.classList.remove('show'); }, 1600);
  }

  // 序列化与保存
  function serialize(){
    var clone = app.cloneNode(true);
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    return clone.innerHTML;
  }
  var saveTimer;
  function scheduleSave(){
    clearTimeout(saveTimer);
    saveTimer = setTimeout(function(){
      try { localStorage.setItem(KEY_CONTENT, serialize()); } catch(err) { toast('存储空间不足，请压缩图片'); }
    }, 400);
  }

  // 编辑模式
  function setEdit(on){
    editMode = on;
    document.body.classList.toggle('editing', on);
    document.querySelectorAll('[data-edit]').forEach(function(el){
      if(on) { el.setAttribute('contenteditable', 'true'); el.setAttribute('spellcheck', 'false'); }
      else el.removeAttribute('contenteditable');
    });
    var btn = document.getElementById('btnEdit');
    btn.classList.toggle('on', on);
    btn.querySelector('.ico').textContent = on ? '✓' : '✎';
    btn.querySelector('.label').textContent = on ? '完成' : '编辑';
    if(on) toast('编辑已开启，点文字直接改');
    else { scheduleSave(); toast('已保存'); }
  }
  document.getElementById('btnEdit').addEventListener('click', function(){ setEdit(!editMode); });
  app.addEventListener('input', function(){ if(editMode) scheduleSave(); });

  // 图片压缩与替换
  app.addEventListener('click', function(e){
    if(!editMode) return;
    var img = e.target.closest('img[data-edit-img]');
    if(img){ e.preventDefault(); currentImg = img; fileInput.value = ''; fileInput.click(); }
  });
  fileInput.addEventListener('change', function(){
    var file = fileInput.files && fileInput.files[0];
    if(!file || !currentImg) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      var img = new Image();
      img.onload = function(){
        var canvas = document.createElement('canvas');
        var MAX = 800; var w = img.width; var h = img.height;
        var scale = Math.min(1, MAX / Math.max(w, h)); w *= scale; h *= scale;
        canvas.width = w; canvas.height = h;
        canvas.getContext('2d').drawImage(img, 0, 0, w, h);
        currentImg.src = canvas.toDataURL('image/jpeg', 0.8); // 压缩成JPEG
        scheduleSave(); toast('图片已更新');
      };
      img.src = ev.target.result;
    };
    reader.readAsDataURL(file);
  });

  // 顶部导航跳转
  document.querySelectorAll('.nav-link').forEach(function(link){
    link.addEventListener('click', function(){
      if(editMode) return; // 编辑模式下不跳转
      document.querySelectorAll('.nav-link').forEach(function(l){ l.classList.remove('active'); });
      this.classList.add('active');
      var target = this.getAttribute('data-target');
      toast('已切换至：' + this.textContent);
      // 这里可以替换为跳转到对应外部链接的功能，例如：
      // if(target === 'panel-1') window.location.href = 'https://...';
    });
  });

  // 音乐播放控制
  var bgMusic = document.getElementById('bgMusic');
  var musicPlayer = document.getElementById('musicPlayer');
  var musicIcon = document.getElementById('musicIcon');
  musicPlayer.addEventListener('click', function() {
    if (bgMusic.paused) {
      if (!bgMusic.src || bgMusic.src === window.location.href) {
        toast('请先在代码中设置音乐链接');
        return;
      }
      bgMusic.play().then(function() {
        musicPlayer.classList.add('playing');
        musicIcon.textContent = '❚❚';
      }).catch(function(err) {
        toast('播放失败，请检查音乐链接');
      });
    } else {
      bgMusic.pause();
      musicPlayer.classList.remove('playing');
      musicIcon.textContent = '♪';
    }
  });

  // 底部大按钮跳转（示例）
  document.getElementById('btnStart').addEventListener('click', function(e){
    if(editMode) return;
    toast('点击了开始了解');
  });

  // 导出与重置
  document.getElementById('btnExport').addEventListener('click', function(){
    var clone = document.documentElement.cloneNode(true);
    clone.querySelector('body').classList.remove('editing');
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    var blob = new Blob(['<!DOCTYPE html>\n' + clone.outerHTML], {type:'text/html;charset=utf-8'});
    var a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'my-profile.html';
    document.body.appendChild(a); a.click(); document.body.removeChild(a); toast('已导出文件');
  });
  document.getElementById('btnReset').addEventListener('click', function(){
    if(!confirm('确定恢复初始内容吗？所有修改将丢失！')) return;
    app.innerHTML = DEFAULT_HTML; setEdit(false); scheduleSave(); toast('已重置');
  });

  // 初始化
  setEdit(false);
})();
</script>
</body>
</html>
