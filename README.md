<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>我的主页</title>
<style>
  :root{
    --bg: #f7f8fa;
    --card: #ffffff;
    --text: #111111;
    --text-sub: #888888;
    --border: #eeeeee;
    --accent: #ff2442; /* 小红书红 */
    --bar: rgba(255,255,255,0.95);
  }
  html.dark{
    --bg: #121212;
    --card: #1c1c1e;
    --text: #f5f5f5;
    --text-sub: #888888;
    --border: #2c2c2e;
    --bar: rgba(28,28,30,0.95);
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html{-webkit-text-size-adjust:100%;}
  body{
    font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,sans-serif;
    background:var(--bg); color:var(--text); min-height:100vh;
    padding-top:50px; padding-bottom:80px; transition:background .3s,color .3s;
  }

  /* 工具栏 */
  .toolbar{
    position:fixed; top:0; left:0; right:0; z-index:999;
    height:50px; display:flex; align-items:center; gap:8px; padding:0 12px;
    background:var(--bar); backdrop-filter:blur(14px); border-bottom:1px solid var(--border);
  }
  .toolbar .spacer{flex:1;}
  .tbtn{
    display:flex; align-items:center; gap:5px; padding:6px 10px; border-radius:8px;
    border:1px solid var(--border); background:transparent; color:var(--text);
    cursor:pointer; font-size:12px; font-family:monospace; transition:all .2s;
  }
  .tbtn:active{transform:scale(.95);}
  .tbtn.on{background:var(--accent); color:#fff; border-color:var(--accent);}
  .tbtn .ico{font-size:14px; line-height:1;}
  .edit-only{display:none !important;}
  body.editing .edit-only{display:flex !important;}
  .color-picker-wrap { position:relative; overflow:hidden; width:32px; height:32px; border-radius:8px; border:1px solid var(--border); display:flex; align-items:center; justify-content:center; }
  .color-picker-wrap input { position:absolute; width:50px; height:50px; top:-10px; left:-10px; opacity:0; cursor:pointer; }

  /* 页面主体 */
  .container { max-width:640px; margin:0 auto; background:var(--card); min-height:100vh; box-shadow:0 0 20px rgba(0,0,0,0.03); }

  /* 封面和头像 */
  .cover-wrap { width:100%; height:240px; background:#e0e0e0; position:relative; overflow:hidden; }
  .cover-wrap img { width:100%; height:100%; object-fit:cover; display:block; }
  .profile-header { padding:0 16px; display:flex; justify-content:flex-end; position:relative; }
  .avatar-wrap { width:80px; height:80px; border-radius:12px; border:4px solid var(--card); position:absolute; top:-40px; left:16px; background:var(--card); overflow:hidden; box-shadow:0 4px 12px rgba(0,0,0,0.1); }
  .avatar-wrap img { width:100%; height:100%; object-fit:cover; }
  .header-actions { display:flex; gap:16px; margin-top:10px; font-size:14px; color:var(--text-sub); font-weight:500; }
  .header-actions span { cursor:pointer; }

  /* 个人信息 */
  .profile-info { padding:16px; border-bottom:1px solid var(--border); }
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
  .tabs { display:flex; border-bottom:1px solid var(--border); }
  .tab { flex:1; text-align:center; padding:14px 0; font-size:14px; font-weight:500; color:var(--text-sub); cursor:pointer; border-bottom:2px solid transparent; transition:all 0.2s; }
  .tab.active { color:var(--text); border-bottom-color:var(--accent); }

  /* 动态列表 */
  .feed { padding:16px; }
  .post { margin-bottom:24px; }
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
    background:var(--bar); backdrop-filter:blur(14px); border-top:1px solid var(--border);
  }
  .bottom-bar span { font-size:22px; cursor:pointer; color:var(--text); transition:transform 0.2s; }
  .bottom-bar span:active { transform:scale(0.9); }

  /* 版权信息 */
  .copyright { text-align:center; font-size:10px; color:var(--text-sub); padding:20px; font-family:monospace; letter-spacing:1px; }

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
    <div class="name" data-edit>颂舰</div>
    <div class="handle" data-edit>@TuTEnYn</div>
    <div class="bio">
      <div class="bio-text" data-edit>你说幸福有链接 我打开→看是404 Not Found</div>
      <div class="bio-heart">❤️</div>
    </div>
    <div class="meta">
      <span data-edit>📍 恩岛</span>
      <span data-edit>🗓️ 2026年9月 加入 &gt;</span>
    </div>
    <div class="stats">
      <div><span data-edit>520</span> 正在关注</div>
      <div><span data-edit>1,314</span> 关注者</div>
    </div>
  </div>

  <!-- 选项卡 -->
  <div class="tabs">
    <div class="tab active">动态</div>
    <div class="tab">照片</div>
    <div class="tab">视频</div>
  </div>

  <!-- 动态列表 -->
  <div class="feed" id="feed">
    
    <!-- 动态 1 -->
    <div class="post">
      <div class="post-header">
        <div class="post-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23555'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23fff' text-anchor='middle' font-family='monospace'%3EA%3C/text%3E%3C/svg%3E"></div>
        <div class="post-name" data-edit>颂舰</div>
      </div>
      <div class="post-text" data-edit>泪是无法临摹的雨季</div>
      <div class="post-images">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23ddd'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🏞️%3C/text%3E%3C/svg%3E">
      </div>
      <div class="post-footer">
        <span data-edit>1分钟前</span>
        <div class="post-actions">
          <span>♡ 点赞</span>
          <span>💬 评论</span>
          <span>↗ 分享</span>
        </div>
      </div>
    </div>

  </div>

  <!-- 版权信息 -->
  <div class="copyright" data-edit>XIUTU BY @TuTEnYn DO NOT USE WITHOUT PERMISSION</div>
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
  var KEY_CONTENT = 'myPage.social.v1';
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
