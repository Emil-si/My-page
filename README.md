<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>我的朋友圈</title>
<style>
  :root{
    --bg: #ededed;
    --card: #ffffff;
    --text: #1a1a1a;
    --text-sub: #999999;
    --border: #e5e5e5;
    --accent: #07c160; /* 默认微信绿 */
    --shadow: 0 4px 12px rgba(0,0,0,0.05);
    --bar: rgba(255,255,255,0.9);
  }
  html.dark{
    --bg: #111111;
    --card: #1c1c1e;
    --text: #f5f5f5;
    --text-sub: #888888;
    --border: #2c2c2e;
    --bar: rgba(28,28,30,0.9);
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html{-webkit-text-size-adjust:100%;}
  body{
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    padding-top: 50px;
    padding-bottom: 80px;
    transition: background .3s, color .3s;
  }

  /* ============ 工具栏 ============ */
  .toolbar{
    position:fixed; top:0; left:0; right:0; z-index:999;
    height:50px; display:flex; align-items:center; gap:6px;
    padding:0 12px;
    background: var(--bar);
    -webkit-backdrop-filter:blur(14px); backdrop-filter:blur(14px);
    border-bottom:1px solid var(--border);
  }
  .toolbar .spacer{flex:1;}
  .tbtn{
    display:flex; align-items:center; gap:5px;
    padding:6px 10px; border-radius:8px;
    border:1px solid var(--border); background:transparent;
    color:var(--text); cursor:pointer;
    font-size:12px; font-family:monospace;
    transition:all .2s;
  }
  .tbtn:active{transform:scale(.95);}
  .tbtn.on{background:var(--accent); color:#fff; border-color:var(--accent);}
  .tbtn .ico{font-size:14px; line-height:1;}
  .edit-only{display:none !important;}
  body.editing .edit-only{display:flex !important;}
  .color-picker-wrap { position: relative; overflow: hidden; width: 32px; height: 32px; border-radius: 8px; border: 1px solid var(--border); display: flex; align-items: center; justify-content: center; }
  .color-picker-wrap input { position: absolute; width: 50px; height: 50px; top: -10px; left: -10px; opacity: 0; cursor: pointer; }

  /* ============ 页面主体 ============ */
  .container { max-width: 640px; margin: 0 auto; background: var(--card); min-height: 100vh; box-shadow: 0 0 20px rgba(0,0,0,0.05); }
  
  /* 封面图 */
  .cover-wrap { width: 100%; aspect-ratio: 16/9; background: #333; position: relative; overflow: hidden; }
  .cover-wrap img { width: 100%; height: 100%; object-fit: cover; display: block; }
  
  /* 头像与名字 */
  .profile-area { padding: 0 16px 16px; display: flex; justify-content: flex-end; position: relative; }
  .avatar-wrap { 
    width: 70px; height: 70px; border-radius: 8px; border: 3px solid var(--card); 
    position: absolute; top: -35px; right: 16px; background: var(--card); 
    overflow: hidden; box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  }
  .avatar-wrap img { width: 100%; height: 100%; object-fit: cover; }
  .name { font-size: 18px; font-weight: 600; margin-top: 40px; }
  .sub { font-size: 12px; color: var(--text-sub); margin-top: 2px; }

  /* 动态流（朋友圈内容） */
  .feed { padding: 0 16px 20px; }
  .post { margin-bottom: 24px; border-bottom: 1px solid var(--border); padding-bottom: 16px; }
  .post:last-child { border-bottom: none; }
  .post-header { display: flex; gap: 10px; margin-bottom: 8px; }
  .post-avatar { width: 40px; height: 40px; border-radius: 6px; background: var(--border); overflow: hidden; flex: none; }
  .post-avatar img { width: 100%; height: 100%; object-fit: cover; }
  .post-name { font-size: 14px; font-weight: 600; color: #576b95; }
  html.dark .post-name { color: #7d90b8; }
  .post-text { font-size: 15px; line-height: 1.6; margin-bottom: 8px; word-wrap: break-word; }
  .post-images { display: grid; grid-template-columns: repeat(3, 1fr); gap: 4px; margin-top: 8px; }
  .post-images img { width: 100%; aspect-ratio: 1; object-fit: cover; border-radius: 4px; cursor: pointer; }
  .post-footer { display: flex; justify-content: space-between; align-items: center; margin-top: 10px; font-size: 12px; color: var(--text-sub); }
  .post-actions { display: flex; gap: 12px; }
  .post-actions span { cursor: pointer; transition: color 0.2s; }
  .post-actions span:hover { color: var(--accent); }

  /* 编辑模式特殊样式 */
  body.editing [data-edit] { outline: 1px dashed var(--accent); outline-offset: 4px; border-radius: 4px; cursor: text; }
  body.editing [data-edit]:focus { background: rgba(0,0,0,0.03); outline: 1px solid var(--accent); }
  body.editing .cover-wrap:hover::after, body.editing .avatar-wrap:hover::after, body.editing .post-avatar:hover::after { content: "换图"; position: absolute; inset: 0; display: flex; align-items: center; justify-content: center; background: rgba(0,0,0,0.5); color: #fff; font-size: 12px; z-index: 5; }
  
  /* 添加按钮 */
  .add-btn { width: 100%; padding: 14px; margin-top: 10px; border: 1px dashed var(--border); border-radius: 8px; background: transparent; color: var(--text-sub); font-size: 14px; cursor: pointer; display: none; }
  body.editing .add-btn { display: block; }
  
  /* 提示框 */
  .toast { position: fixed; left: 50%; bottom: 30px; transform: translateX(-50%) translateY(20px); background: var(--accent); color: #fff; padding: 10px 20px; border-radius: 20px; font-size: 13px; opacity: 0; pointer-events: none; transition: all 0.3s; z-index: 2000; }
  .toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }
</style>
</head>
<body>

<!-- 工具栏 -->
<div class="toolbar">
  <button class="tbtn" id="btnEdit"><span class="ico">✎</span><span class="label">编辑</span></button>
  <button class="tbtn edit-only" id="btnExport"><span class="ico">⬇</span><span class="label">导出</span></button>
  <button class="tbtn edit-only" id="btnReset"><span class="ico">⟲</span><span class="label">重置</span></button>
  <span class="spacer"></span>
  
  <!-- 颜色选择器（只在编辑模式显示） -->
  <div class="color-picker-wrap edit-only" title="修改主题颜色">
    <input type="color" id="colorPicker" value="#07c160">
  </div>
  
  <button class="tbtn" id="btnTheme"><span class="ico" id="themeIcon">◐</span></button>
</div>

<!-- 页面主体 -->
<div class="container" id="app">
  
  <!-- 封面图 -->
  <div class="cover-wrap">
    <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Cdefs%3E%3ClinearGradient id='g' x1='0' y1='0' x2='1' y2='1'%3E%3Cstop offset='0' stop-color='%23333'/%3E%3Cstop offset='1' stop-color='%230c0d0e'/%3E%3C/linearGradient%3E%3C/defs%3E%3Crect width='160' height='90' fill='url(%23g)'/%3E%3Ctext x='80' y='49' font-size='7' fill='%23888' text-anchor='middle' font-family='monospace'%3E点击换封面图%3C/text%3E%3C/svg%3E" alt="封面">
  </div>

  <!-- 个人信息 -->
  <div class="profile-area">
    <div class="avatar-wrap">
      <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23262626'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23ffffff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像">
    </div>
    <div style="text-align: right;">
      <div class="name" data-edit>你的名字</div>
      <div class="sub" data-edit>你的副标题 / 签名</div>
    </div>
  </div>

  <!-- 朋友圈动态列表 -->
  <div class="feed" id="feed">
    
    <!-- 动态 1 -->
    <div class="post">
      <div class="post-header">
        <div class="post-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23555'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23fff' text-anchor='middle' font-family='monospace'%3EA%3C/text%3E%3C/svg%3E"></div>
        <div class="post-name" data-edit>我的小号</div>
      </div>
      <div class="post-text" data-edit>今天天气真好，出来散散步！☀️ 这个页面用手机就能直接改，点一下“编辑”就能写新动态啦～</div>
      <div class="post-images">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23ddd'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🏞️%3C/text%3E%3C/svg%3E">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23ddd'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E📸%3C/text%3E%3C/svg%3E">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23ddd'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🎵%3C/text%3E%3C/svg%3E">
      </div>
      <div class="post-footer">
        <span data-edit>刚刚</span>
        <div class="post-actions">
          <span>♡ 赞</span>
          <span>💬 评论</span>
        </div>
      </div>
    </div>

    <!-- 动态 2 -->
    <div class="post">
      <div class="post-header">
        <div class="post-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23555'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23fff' text-anchor='middle' font-family='monospace'%3EB%3C/text%3E%3C/svg%3E"></div>
        <div class="post-name" data-edit>另一个朋友</div>
      </div>
      <div class="post-text" data-edit>这是一条纯文字的动态，多写点字也没关系，它会自动换行。</div>
      <div class="post-footer">
        <span data-edit>1小时前</span>
        <div class="post-actions">
          <span>♡ 赞</span>
          <span>💬 评论</span>
        </div>
      </div>
    </div>

  </div>

  <button class="add-btn" onclick="addPost()">＋ 发一条新动态</button>
</div>

<div class="toast" id="toast"></div>
<input type="file" id="fileInput" accept="image/*" hidden>

<script>
(function(){
  'use strict';
  var KEY_CONTENT = 'myPage.friendCircle.v1';
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
    var savedColor = localStorage.getItem(KEY_COLOR) || '#07c160';
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
      img.onload = function
(){
        var canvas = document.createElement('canvas');
         var MAX = 1000; var window w = img.width; var h = img.height;
        var scale = Math.min(1.add, MAX / Math.max(w, h)); w *= scale; h *= scale;
Post        canvas.width = w; canvas =.height = h;
        canvas.getContext('2d').drawImage(img, 0, 0, w, h);
        currentImg.src = canvas.toDataURL('image/jpeg', 0.8);
        scheduleSave(); toast('图片已更新');
      };
      img.src = ev.target.result;
    };
    reader.readAsDataURL(file);
  });

  // 添加动态 function(){
    var newPost = document.createElement('div');
    newPost.className = 'post';
    newPost.innerHTML = `
      <div class="post-header">
        <div class="post-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23999'/%3E%3Ctext x='50' y='69' font-size='46' fill='%23fff' text-anchor='middle' font-family='monospace'%3EN%3C/text%3E%3C/svg%3E"></div>
        <div class="post-name" data-edit>新朋友</div>
      </div>
      <div class="post-text" data-edit>点击这里输入动态内容...</div>
      <div class="post-footer">
        <span data-edit>刚刚</span>
        <div class="post-actions"><span>♡ 赞</span><span>💬 评论</span></div>
      </div>`;
    document.getElementById('feed').appendChild(newPost);
    if(editMode) setEdit(false); setTimeout(function(){ setEdit(true); }, 50);
    scheduleSave(); toast('已添加新动态');
  };

  // 导出与重置
  document.getElementById('btnExport').addEventListener('click', function(){
    var clone = document.documentElement.cloneNode(true);
    clone.querySelector('body').classList.remove('editing');
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    var blob = new Blob(['<!DOCTYPE html>\n' + clone.outerHTML], {type:'text/html;charset=utf-8'});
    var a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'my-friend-circle.html';
    document.body.appendChild(a); a.click(); document.body.removeChild(a); toast('已导出');
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
