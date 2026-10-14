<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>我的主页</title>
<style>
  :root{
    --glass-bg: rgba(255, 255, 255, 0.55);
    --glass-border: rgba(255, 255, 255, 0.35);
    --glass-blur: blur(16px);
    --bg: #eef0f4;
    --card: #ffffff;
    --text: #111111;
    --text-sub: #888888;
    --border: #eeeeee;
    --accent: #ff2442;
    --bar: rgba(255,255,255,0.75);
  }
  html.dark{
    --glass-bg: rgba(30, 32, 38, 0.55);
    --glass-border: rgba(255, 255, 255, 0.1);
    --glass-blur: blur(16px);
    --bg: #0f1116;
    --card: #1c1c1e;
    --text: #f5f5f5;
    --text-sub: #888888;
    --border: #2c2c2e;
    --bar: rgba(28,28,30,0.75);
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html{-webkit-text-size-adjust:100%;}
  body{
    font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,sans-serif;
    background: var(--bg);
    background-image: radial-gradient(circle at 20% 20%, rgba(255,36,66,0.08), transparent 50%),
                      radial-gradient(circle at 80% 80%, rgba(0,122,255,0.06), transparent 50%);
    background-attachment: fixed;
    color:var(--text); min-height:100vh;
    padding-top:50px; padding-bottom:80px; transition:background .3s,color .3s;
  }

  /* 工具栏 */
  .toolbar{
    position:fixed; top:0; left:0; right:0; z-index:999;
    height:50px; display:flex; align-items:center; gap:8px; padding:0 12px;
    background:var(--bar); backdrop-filter:var(--glass-blur); -webkit-backdrop-filter:var(--glass-blur);
    border-bottom:1px solid var(--glass-border);
  }
  .toolbar .spacer{flex:1;}
  .tbtn{
    display:flex; align-items:center; gap:5px; padding:6px 10px; border-radius:8px;
    border:1px solid var(--glass-border); background:rgba(255,255,255,0.2);
    color:var(--text); cursor:pointer; font-size:12px; font-family:monospace; transition:all .2s;
    backdrop-filter:blur(8px); -webkit-backdrop-filter:blur(8px);
  }
  .tbtn:active{transform:scale(.95);}
  .tbtn.on{background:var(--accent); color:#fff; border-color:var(--accent);}
  .tbtn .ico{font-size:14px; line-height:1;}
  .edit-only{display:none !important;}
  body.editing .edit-only{display:flex !important;}
  .color-picker-wrap { position:relative; overflow:hidden; width:32px; height:32px; border-radius:8px; border:1px solid var(--glass-border); display:flex; align-items:center; justify-content:center; background:rgba(255,255,255,0.2); }
  .color-picker-wrap input { position:absolute; width:50px; height:50px; top:-10px; left:-10px; opacity:0; cursor:pointer; }

  /* 顶部导航 */
  .top-nav {
    max-width: 640px;
    margin: 0 auto;
    display: flex;
    gap: 8px;
    padding: 12px 16px;
    position: sticky;
    top: 50px;
    z-index: 998;
    background: var(--bar);
    backdrop-filter: var(--glass-blur);
    -webkit-backdrop-filter: var(--glass-blur);
    border-bottom: 1px solid var(--glass-border);
  }
  .nav-link {
    flex: 1;
    text-align: center;
    padding: 8px 12px;
    border-radius: 10px;
    background: var(--glass-bg);
    border: 1px solid var(--glass-border);
    backdrop-filter: var(--glass-blur);
    -webkit-backdrop-filter: var(--glass-blur);
    color: var(--text-sub);
    text-decoration: none;
    font-size: 13px;
    font-weight: 500;
    transition: all 0.2s ease;
    cursor: pointer;
  }
  .nav-link:hover {
    background: rgba(255,255,255,0.7);
    color: var(--text);
    transform: translateY(-1px);
  }
  .nav-link.active {
    background: var(--accent);
    color: #fff;
    border-color: var(--accent);
  }

  /* 页面主体 */
  .container { max-width:640px; margin:0 auto; background:var(--glass-bg); backdrop-filter:var(--glass-blur); -webkit-backdrop-filter:var(--glass-blur); border:1px solid var(--glass-border); min-height:100vh; box-shadow:0 0 30px rgba(0,0,0,0.05); }

  /* 封面和头像 */
  .cover-wrap { width:100%; height:240px; background:#e0e0e0; position:relative; overflow:hidden; }
  .cover-wrap img { width:100%; height:100%; object-fit:cover; display:block; }
  .profile-header { padding:0 16px; display:flex; justify-content:flex-end; position:relative; }
  .avatar-wrap { width:80px; height:80px; border-radius:12px; border:4px solid var(--glass-bg); position:absolute; top:-40px; left:16px; background:var(--glass-bg); backdrop-filter:var(--glass-blur); -webkit-backdrop-filter:var(--glass-blur); overflow:hidden; box-shadow:0 4px 12px rgba(0,0,0,0.1); }
  .avatar-wrap img { width:100%; height:100%; object-fit:cover; }
  .header-actions { display:flex; gap:16px; margin-top:10px; font-size:14px; color:var(--text-sub); font-weight:500; }
  .header-actions span { cursor:pointer; }

  /* 个人信息 */
  .profile-info { padding:16px; border-bottom:1px solid var(--glass-border); }
  .name { font-size:22px; font-weight:700; margin-bottom:4px; }
  .handle { font-size:14px; color:var(--text-sub); margin-bottom:12px; }
  .bio { font-size:15px; line-height:1.6; margin-bottom:10px; display:flex; justify-content:space-between; align-items:flex-start; }
  .bio-text { flex:1; }
  .bio-heart { color:#ff2442; font-size:18px; margin-left:8px; cursor:pointer; }
  .meta { display:flex; gap:16px; font-size:13px; color:var(--text-sub); margin-bottom:12px; }
  .stats { display:flex; gap:20px; font-size:14px; }
  .stats span { font-weight:600; color:var(--text); }
  .stats span:last-child { font-weight:400; color:var(--text-sub); margin-left:4px; }

  /* 选项卡 */
  .tabs { display:flex; border-bottom:1px solid var(--glass-border); }
  .tab { flex:1; text-align:center; padding:14px 0; font-size:14px; font-weight:500; color:var(--text-sub); cursor:pointer; border-bottom:2px solid transparent; transition:all 0.2s; }
  .tab.active { color:var(--text); border-bottom-color:var(--accent); }

  /* 动态列表 */
  .feed { padding:16px; }
  .post { margin-bottom:24px; padding:16px; background:var(--glass-bg); backdrop-filter:var(--glass-blur); -webkit-backdrop-filter:var(--glass-blur); border:1px solid var(--glass-border); border-radius:16px; }
  .post-header { display:flex; align-items:center; gap:10px; margin-bottom:10px; }
  .post-avatar { width:36px; height:36px; border-radius:6px; overflow:hidden; background:var(--border); }
  .post-avatar img { width:100%; height:100%; object-fit:cover; }
  .post-name { font-size:14px; font-weight:600; }
  .post-text { font-size:15px; line-height:1.6; margin-bottom:10px; color:var(--text); }
  .post-images { width:100%; border-radius:8px; overflow:hidden; margin-bottom:10px; }
  .post-images img { width:100%; display:block; object-fit:cover; }
  .post-footer { display:flex; justify-content:space-between; font-size:12px; color:var(--text-sub); }
  .post-actions { display:flex; gap:16px; font-size:14px; }
  .post-actions span { cursor:pointer; }

  /* 底部固定栏 */
  .bottom-bar {
    position:fixed; bottom:0; left:0; right:0; z-index:999;
    height:60px; display:flex; justify-content:space-around; align-items:center;
    background:var(--bar); backdrop-filter:var(--glass-blur); -webkit-backdrop-filter:var(--glass-blur);
    border-top:1px solid var(--glass-border);
  }
  .bottom-bar span { font-size:22px; cursor:pointer; color:var(--text); transition:transform 0.2s; }
  .bottom-bar span:active { transform:scale(0.9); }

  /* 版权信息 */
  .copyright { text-align:center; font-size:10px; color:var(--text-sub); padding:20px; font-family:monospace; letter-spacing:1px; }

  /* 音乐播放器 */
  .music-player {
    position: fixed; bottom: 80px; right: 20px; z-index: 1000;
    width: 52px; height: 52px; border-radius: 50%;
    background: var(--glass-bg); backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border);
    box-shadow: 0 8px 30px rgba(0,0,0,0.12);
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; transition: all .3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    color: var(--text); font-size: 22px;
  }
  .music-player:hover { transform: scale(1.1); }
  .music-player.playing {
    animation: spin 4s linear infinite;
    background: var(--accent); color: #fff; border-color: var(--accent);
  }
  @keyframes spin { 100% { transform: rotate(360deg); } }

  /* 编辑状态样式 */
  body.editing [data-edit] { outline:1px dashed var(--accent); outline-offset:4px; border-radius:4px; cursor:text; }
  body.editing [data-edit]:focus { background:rgba(0,0,0,0.02); outline:1px solid var(--accent); }
  body.editing .cover-wrap:hover::after, body.editing .avatar-wrap:hover::after, body.editing .post-avatar:hover::after, body.editing .post-images:hover::after {
    content:"点击更换图片"; position:absolute; inset:0; display:flex; align-items:center; justify-content:center;
    background:rgba(0,0,0,0.5); color:#fff; font-size:12px; z-index:5;
  }
  body.editing .post-images { position:relative; }
  .toast { position:fixed; left:50%; bottom:80px; transform:translateX(-50%) translateY(20px); background:var(--accent); color:#fff; padding:10px 20px; border-radius:20px; font-size:13px; opacity:0; pointer-events:none; transition:all 0.3s; z-index:2000; }
  .toast.show { opacity:1; transform:translateX(-50%) translateY(0); }
</style>
</head>
<body>

<!-- 音乐播放器 -->
<audio id="bgMusic" src="" loop preload="none"></audio>
<div class="music-player" id="musicPlayer" title="播放/暂停音乐">
  <span id="musicIcon">♪</span>
</div>

<!-- 工具栏 -->
<div class="toolbar">
  <button class="tbtn" id="btnEdit"><span class="ico">✎</span><span class="label">编辑</span></button>
  <button class="tbtn edit-only" id="btnExport"><span class="ico">⬇</span><span class="label">导出</span></button>
  <button class="tbtn edit-only" id="btnReset"><span class="ico">⟲</span><span class="label">重置</span></button>
  <span class="spacer"></span>
  <div class="color-picker-wrap edit-only" title="修改主题颜色">
    <input type="color" id="colorPicker" value="#ff2442">
  </div>
  <button class="tbtn" id="btnTheme"><span class="ico" id="themeIcon">◐</span></button>
</div>

<!-- 顶部导航栏 -->
<div class="top-nav" id="topNav">
  <a class="nav-link active" data-target="panel-0">动态</a>
  <a class="nav-link" data-target="panel-1">照片</a>
  <a class="nav-link" data-target="panel-2">视频</a>
</div>

<!-- 页面主体 -->
<div class="container" id="app">
  
  <!-- 封面图 -->
  <div class="cover-wrap">
    <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Cdefs%3E%3ClinearGradient id='g' x1='0' y1='0' x2='1' y2='1'%3E%3Cstop offset='0' stop-color='%23333'/%3E%3Cstop offset='1' stop-color='%230c0d0e'/%3E%3C/linearGradient%3E%3C/defs%3E%3Crect width='160' height='90' fill='url(%23g)'/%3E%3Ctext x='80' y='49' font-size='7' fill='%23888' text-anchor='middle' font-family='monospace'%3E点击换封面图%3C/text%3E%3C/svg%3E" alt="封面">
  </div>

  <!-- 头像与操作 -->
  <div class="profile-header">
    <div class="avatar-wrap">
      <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23262626'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23ffffff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像">
    </div>
    <div class="header-actions">
      <span>个人资料</span>
      <span>更换封面</span>
    </div>
  </div>

  <!-- 个人信息 -->
  <div class="profile-info">
    <div class="name" data-edit>你的名字</div>
    <div class="handle" data-edit>@你的ID</div>
    <div class="bio">
      <div class="bio-text" data-edit>点击编辑，写下你的签名或心情。</div>
      <div class="bio-heart">❤️</div>
    </div>
    <div class="meta">
      <span data-edit>📍 你的位置</span>
      <span data-edit>🗓️ 2026年加入</span>
    </div>
    <div class="stats">
      <div><span data-edit>0</span> 正在关注</div>
      <div><span data-edit>0</span> 关注者</div>
    </div>
  </div>

  <!-- 内容面板 -->
  <div id="panel-0" class="panel active">
    <div class="feed" id="feed">
      <div class="post">
        <div class="post-header">
          <div class="post-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23555'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23fff' text-anchor='middle' font-family='monospace'%3EA%3C/text%3E%3C/svg%3E"></div>
          <div class="post-name" data-edit>你的昵称</div>
        </div>
        <div class="post-text" data-edit>点击这里输入动态内容。你可以在编辑模式下随意修改文字、更换图片。</div>
        <div class="post-images">
          <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23ddd'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🏞️%3C/text%3E%3C/svg%3E">
        </div>
        <div class="post-footer">
          <span data-edit>刚刚</span>
          <div class="post-actions">
            <span>♡ 点赞</span>
            <span>💬 评论</span>
            <span>↗ 分享</span>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div id="panel-1" class="panel" style="display:none;">
    <div class="feed">
      <div class="post">
        <div class="post-text" data-edit>照片面板（编辑模式下可直接修改文字和图片）</div>
      </div>
    </div>
  </div>

  <div id="panel-2" class="panel" style="display:none;">
    <div class="feed">
      <div class="post">
        <div class="post-text" data-edit>视频面板（编辑模式下可直接修改文字和图片）</div>
      </div>
    </div>
  </div>

  <!-- 版权信息 -->
  <div class="copyright" data-edit>MY PAGE © 2026</div>
</div>

<!-- 底部固定栏 -->
<div class="bottom-bar">
  <span>✉️</span>
  <span>❤️</span>
  <span>➡️</span>
</div>

<div class="toast" id="toast"></div>
<input type="file" id="fileInput" accept="image/*" hidden>

<script>
(function(){
  'use strict';
  var KEY_CONTENT = 'myPage.glass.v1';
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
    var savedTheme = localStorage.getItem(KEY_THEME) || 'light';
    var savedColor = localStorage.getItem(KEY_COLOR) || '#ff2442';
    applyTheme(savedTheme === 'dark');
    applyColor(savedColor);
    document.getElementById('colorPicker').value = savedColor;
  } catch(e){}

  // 主题切换
  function applyTheme(dark){
    document.documentElement.classList.toggle('dark', dark);
    document.getElementById('themeIcon').textContent = dark ? '☀' : '◐';
    localStorage.setItem(KEY_THEME, dark ? 'dark' : 'light');
  }
  document.getElementById('btnTheme').addEventListener('click', function(){
    applyTheme(!document.documentElement.classList.contains('dark'));
  });

  // 颜色切换
  function applyColor(color){
    document.documentElement.style.setProperty('--accent', color);
    localStorage.setItem(KEY_COLOR, color);
  }
  document.getElementById('colorPicker').addEventListener('input', function(e){
    applyColor(e.target.value);
  });

  // 提示
  var toastTimer;
  function toast(msg){
    toastEl.textContent = msg;
    toastEl.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(function(){ toastEl.classList.remove('show'); }, 1600);
  }

  // 保存与序列化
  function serialize(){
    var clone = app.cloneNode(true);
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    return clone.innerHTML;
  }
  var saveTimer;
  function scheduleSave(){
    clearTimeout(saveTimer);
    saveTimer = setTimeout(function(){
      try { localStorage.setItem(KEY_CONTENT, serialize()); } catch(err) { toast('存储空间不足'); }
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

  // 图片替换
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
        currentImg.src = canvas.toDataURL('image/jpeg', 0.8);
        scheduleSave(); toast('图片已更新');
      };
      img.src = ev.target.result;
    };
    reader.readAsDataURL(file);
  });

  // 顶部导航切换
  document.querySelectorAll('.nav-link').forEach(function(link){
    link.addEventListener('click', function(){
      document.querySelectorAll('.nav-link').forEach(function(l){ l.classList.remove('active'); });
      this.classList.add('active');
      var target = this.getAttribute('data-target');
      document.querySelectorAll('.panel').forEach(function(p){ p.style.display = 'none'; });
      var panel = document.getElementById(target);
      if(panel) panel.style.display = 'block';
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
        console.error('播放失败:', err);
      });
    } else {
      bgMusic.pause();
      musicPlayer.classList.remove('playing');
      musicIcon.textContent = '♪';
    }
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

  setEdit(false);
})();
</script>
</body>
</html>
