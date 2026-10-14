<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>个人主页</title>
<style>
  :root{
    /* 玻璃质感核心 */
    --glass-blur: blur(24px) saturate(180%);
    --glass-border: rgba(255, 255, 255, 0.12);
    
    /* 背景与文字 */
    --bg-color: #0d0d10;
    --bg-image: none;
    --text: #f0f0f0;
    --text-sub: #999;
    
    /* 全局主色调 */
    --accent: #4a9eff;
    --accent-text: #ffffff;
    
    /* 卡片属性（通过滑块控制） */
    --card-bg-color: rgba(25, 25, 30, 0.65);
    --card-opacity: 1;
    --card-gradient: linear-gradient(135deg, #1c1c22 0%, #0d0d10 100%);
    --card-gradient-angle: 135deg;
  }
  html.light{
    --glass-border: rgba(0, 0, 0, 0.08);
    --bg-color: #f2f2f5;
    --text: #111;
    --text-sub: #666;
    --card-bg-color: rgba(255, 255, 255, 0.75);
    --card-gradient: linear-gradient(135deg, #ffffff 0%, #f0f0f5 100%);
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html{-webkit-text-size-adjust:100%;}
  body{
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    background-color: var(--bg-color);
    background-image: var(--bg-image);
    background-size: cover; background-position: center; background-attachment: fixed;
    color: var(--text); min-height: 100vh;
    padding-top: 60px; padding-bottom: 90px;
    transition: background 0.3s, color 0.3s;
  }

  /* ============ 工具栏 ============ */
  .toolbar{
    position: fixed; top: 0; left: 0; right: 0; z-index: 999;
    height: 50px; display: flex; align-items: center; gap: 8px; padding: 0 16px;
    background: rgba(20, 20, 25, 0.7); backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border-bottom: 1px solid var(--glass-border);
  }
  html.light .toolbar { background: rgba(255, 255, 255, 0.7); }
  .toolbar .spacer{flex:1;}
  .tbtn{
    display: flex; align-items: center; gap: 5px; padding: 6px 12px; border-radius: 20px;
    border: 1px solid var(--glass-border); background: rgba(255,255,255,0.05);
    color: var(--text); cursor: pointer; font-size: 12px; font-family: monospace;
    transition: all 0.2s; backdrop-filter: blur(8px);
  }
  .tbtn:active{transform: scale(0.95);}
  .tbtn.on{background: var(--accent); color: var(--accent-text); border-color: var(--accent);}
  .edit-only{display: none !important;}
  body.editing .edit-only{display: flex !important;}
  
  /* 颜色选择器与滑块面板 */
  .color-picker-wrap { position: relative; overflow: hidden; width: 32px; height: 32px; border-radius: 50%; border: 1px solid var(--glass-border); display: flex; align-items: center; justify-content: center; background: rgba(255,255,255,0.05); }
  .color-picker-wrap input { position: absolute; width: 50px; height: 50px; top: -10px; left: -10px; opacity: 0; cursor: pointer; }
  .bg-image-btn { cursor: pointer; padding: 6px 10px; border-radius: 8px; border: 1px solid var(--glass-border); font-size: 12px; }

  /* 侧边样式面板（用于滑块调节） */
  .style-panel {
    position: fixed; top: 60px; right: 16px; z-index: 1000;
    width: 260px; padding: 16px; border-radius: 16px;
    background: rgba(20, 20, 25, 0.9); backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); box-shadow: 0 10px 40px rgba(0,0,0,0.5);
    display: none; flex-direction: column; gap: 12px;
    color: #fff; font-size: 12px;
  }
  .style-panel.show { display: flex; }
  .style-item { display: flex; justify-content: space-between; align-items: center; gap: 8px; }
  .style-item input[type="range"] { flex: 1; cursor: pointer; }
  .style-item input[type="color"] { width: 30px; height: 30px; border: none; border-radius: 6px; cursor: pointer; background: transparent; }
  .style-panel h4 { font-size: 13px; margin-bottom: 4px; border-bottom: 1px solid var(--glass-border); padding-bottom: 8px; }

  /* ============ 顶部导航栏 ============ */
  .top-nav {
    max-width: 640px; margin: 0 auto; display: flex; gap: 6px; padding: 12px 16px;
    position: sticky; top: 50px; z-index: 998;
    background: rgba(20, 20, 25, 0.7); backdrop-filter: var(--glass-blur);
    border-bottom: 1px solid var(--glass-border);
    overflow-x: auto; scrollbar-width: none;
  }
  html.light .top-nav { background: rgba(255, 255, 255, 0.7); }
  .top-nav::-webkit-scrollbar { display: none; }
  .nav-link {
    flex: 1 0 auto; text-align: center; padding: 8px 16px; border-radius: 20px;
    background: rgba(255,255,255,0.05); border: 1px solid var(--glass-border);
    color: var(--text-sub); text-decoration: none; font-size: 12px; font-family: monospace; letter-spacing: 0.1em;
    transition: all 0.2s ease; cursor: pointer; white-space: nowrap;
  }
  .nav-link.active { background: var(--accent); color: var(--accent-text); border-color: var(--accent); }

  /* ============ 主容器 ============ */
  .container { max-width: 640px; margin: 0 auto; padding: 16px; }

  /* 卡片统一玻璃质感 */
  .glass-card {
    background-color: var(--card-bg-color);
    background-image: var(--card-gradient);
    background-size: cover;
    backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 24px; overflow: hidden;
    box-shadow: 0 15px 35px rgba(0,0,0,0.3); margin-bottom: 16px; position: relative;
  }

  /* 主页顶部卡片（封面 + 头像 + 资料） */
  .main-card {
    background-color: var(--card-bg-color);
    background-image: var(--card-gradient);
    backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 24px; padding: 0 0 24px 0; overflow: hidden;
    box-shadow: 0 15px 35px rgba(0,0,0,0.3); margin-bottom: 16px; position: relative;
  }
  
  /* 封面图 */
  .cover-wrap { width: 100%; height: 220px; background: #1a1a1a; position: relative; overflow: hidden; }
  .cover-wrap img { width: 100%; height: 100%; object-fit: cover; display: block; filter: grayscale(10%); }
  
  /* 头像位置（按照第5张图，左下角下沉） */
  .avatar-wrap { 
    width: 80px; height: 80px; border-radius: 50%; border: 3px solid var(--bg-color); 
    position: absolute; bottom: -40px; left: 24px; background: var(--glass-bg); 
    overflow: hidden; box-shadow: 0 8px 25px rgba(0,0,0,0.6); z-index: 2;
  }
  .avatar-wrap img { width: 100%; height: 100%; object-fit: cover; }

  /* 个人信息区 */
  .profile-info { padding: 48px 24px 0; }
  .name-row { display: flex; align-items: center; gap: 12px; margin-bottom: 12px; }
  .name { font-size: 26px; font-weight: 700; letter-spacing: 0.02em; }
  .handle { font-size: 14px; color: var(--text-sub); font-family: monospace; margin-bottom: 16px; }
  
  .stats { display: flex; gap: 20px; font-size: 14px; margin-bottom: 24px; }
  .stats span { font-weight: 600; color: var(--text); }
  .stats span:last-child { font-weight: 400; color: var(--text-sub); margin-left: 4px; }

  /* 三图网格 */
  .image-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin: 0 24px 24px 24px; }
  .grid-item { aspect-ratio: 1; border-radius: 12px; overflow: hidden; background: rgba(255,255,255,0.05); position: relative; }
  .grid-item img { width: 100%; height: 100%; object-fit: cover; display: block; }

  /* 引言块 */
  .quote-block { margin: 0 24px 24px 24px; padding: 16px; border: 1px solid var(--glass-border); border-radius: 16px; background: rgba(255,255,255,0.02); }
  .quote-block p { font-size: 14px; line-height: 1.8; color: var(--text-sub); font-style: italic; }

  /* 发帖框 */
  .post-box { margin: 0 24px 16px 24px; background: rgba(255,255,255,0.03); border: 1px solid var(--glass-border); border-radius: 20px; padding: 16px; display: flex; flex-direction: column; gap: 12px; }
  .post-input { width: 100%; background: transparent; border: none; color: var(--text); font-size: 15px; outline: none; resize: none; font-family: inherit; }
  .post-tools { display: flex; justify-content: space-between; align-items: center; }
  .tool-icons { display: flex; gap: 12px; color: var(--text-sub); font-size: 18px; cursor: pointer; }
  .publish-btn { background: var(--accent); color: var(--accent-text); border: none; padding: 8px 20px; border-radius: 20px; font-size: 13px; font-weight: 600; cursor: pointer; display: flex; align-items: center; gap: 6px; }

  /* 动态卡片 */
  .feed-card { 
    background-color: var(--card-bg-color);
    background-image: var(--card-gradient);
    backdrop-filter: var(--glass-blur); 
    border: 1px solid var(--glass-border); border-radius: 20px; padding: 16px; margin-bottom: 16px; position: relative; 
  }
  .feed-header { display: flex; align-items: center; gap: 10px; margin-bottom: 12px; }
  .feed-avatar { width: 40px; height: 40px; border-radius: 50%; overflow: hidden; background: #333; }
  .feed-avatar img { width: 100%; height: 100%; object-fit: cover; }
  .feed-name { font-size: 14px; font-weight: 600; }
  .feed-handle { font-size: 12px; color: var(--text-sub); font-family: monospace; }
  .feed-time { font-size: 12px; color: var(--text-sub); margin-left: auto; }
  .feed-text { font-size: 15px; line-height: 1.6; margin-bottom: 12px; }
  .feed-footer { display: flex; justify-content: flex-end; font-size: 13px; color: var(--text-sub); gap: 16px; }

  /* 底部导航（仿截图） */
  .bottom-nav {
    position: fixed; bottom: 0; left: 0; right: 0; z-index: 998; height: 70px;
    display: flex; justify-content: space-around; align-items: center;
    background: rgba(20, 20, 25, 0.8); backdrop-filter: var(--glass-blur);
    border-top: 1px solid var(--glass-border);
  }
  html.light .bottom-nav { background: rgba(255, 255, 255, 0.8); }
  .bottom-nav-item { display: flex; flex-direction: column; align-items: center; gap: 4px; font-size: 10px; color: var(--text-sub); cursor: pointer; transition: color 0.2s; }
  .bottom-nav-item.active { color: var(--accent); }
  .bottom-nav-item .icon { font-size: 20px; }

  /* ============ 卡片操作按钮 ============ */
  .card-del {
    display: none; position: absolute; top: 12px; right: 12px;
    width: 28px; height: 28px; border-radius: 8px; border: 1px solid var(--glass-border);
    background: rgba(0,0,0,0.5); color: #fff; cursor: pointer; font-size: 16px;
    align-items: center; justify-content: center; z-index: 10;
  }
  .card-del:hover { background: #e5484d; border-color: #e5484d; }
  body.editing .card-del { display: flex; }
  .add-card-btn {
    display: none; width: 100%; padding: 14px; border: 1px dashed var(--glass-border);
    border-radius: 16px; background: transparent; color: var(--text-sub); cursor: pointer;
    font-size: 13px; margin-bottom: 16px; transition: all 0.2s;
  }
  .add-card-btn:hover { background: rgba(255,255,255,0.05); }
  body.editing .add-card-btn { display: block; }

  /* ============ 音乐播放器 ============ */
  .music-player {
    position: fixed; bottom: 85px; right: 20px; z-index: 1000;
    width: 52px; height: 52px; border-radius: 50%;
    background: rgba(20, 20, 25, 0.8); backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border);
    box-shadow: 0 8px 30px rgba(0,0,0,0.4);
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; transition: all 0.3s;
    color: var(--text); font-size: 22px;
  }
  html.light .music-player { background: rgba(255, 255, 255, 0.8); }
  .music-player.playing { animation: spin 4s linear infinite; background: var(--accent); color: var(--accent-text); border-color: var(--accent); }
  @keyframes spin { 100% { transform: rotate(360deg); } }

  /* ============ 编辑状态 ============ */
  body.editing [data-edit] { outline: 1px dashed var(--accent); outline-offset: 4px; border-radius: 4px; cursor: text; }
  body.editing [data-edit]:focus { background: rgba(255,255,255,0.05); outline: 1px solid var(--accent); }
  body.editing .cover-wrap:hover::after, body.editing .avatar-wrap:hover::after, body.editing .grid-item:hover::after, body.editing .feed-avatar:hover::after {
    content: "点击换图"; position: absolute; inset: 0; display: flex; align-items: center; justify-content: center;
    background: rgba(0,0,0,0.6); color: #fff; font-size: 12px; z-index: 5;
  }
  .toast { position: fixed; left: 50%; bottom: 100px; transform: translateX(-50%) translateY(20px); background: var(--accent); color: var(--accent-text); padding: 10px 20px; border-radius: 20px; font-size: 13px; opacity: 0; pointer-events: none; transition: all 0.3s; z-index: 2000; }
  .toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }
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
  <button class="tbtn edit-only" id="btnStylePanel"><span class="ico">⚙️</span><span class="label">样式</span></button>
  <button class="tbtn edit-only" id="btnExport"><span class="ico">⬇</span><span class="label">导出</span></button>
  <button class="tbtn edit-only" id="btnReset"><span class="ico">⟲</span><span class="label">重置</span></button>
  <span class="spacer"></span>
  <button class="tbtn edit-only bg-image-btn" id="btnBgImage">🖼️ 背景</button>
  <button class="tbtn" id="btnTheme"><span class="ico" id="themeIcon">☀</span></button>
</div>

<!-- 样式面板（滑块控制） -->
<div class="style-panel" id="stylePanel">
  <h4>卡片样式调节</h4>
  <div class="style-item">
    <span>透明度</span>
    <input type="range" id="opacitySlider" min="0" max="100" value="65">
    <span id="opacityVal">65%</span>
  </div>
  <div class="style-item">
    <span>渐变起始色</span>
    <input type="color" id="gradColor1" value="#1c1c22">
  </div>
  <div class="style-item">
    <span>渐变结束色</span>
    <input type="color" id="gradColor2" value="#0d0d10">
  </div>
  <div class="style-item">
    <span>渐变角度</span>
    <input type="range" id="gradAngle" min="0" max="360" value="135">
    <span id="angleVal">135°</span>
  </div>
  <h4 style="margin-top: 10px;">主色调联动</h4>
  <div class="style-item">
    <span>全局主色</span>
    <input type="color" id="accentColor" value="#4a9eff">
  </div>
</div>

<!-- 顶部导航栏 -->
<div class="top-nav" id="topNav">
  <a class="nav-link active" data-target="panel-0">WELCOME</a>
  <a class="nav-link" data-target="panel-1">PERSONAL INFO</a>
  <a class="nav-link" data-target="panel-2">MY STYLE</a>
  <a class="nav-link" data-target="panel-3">CATEGORIES</a>
</div>

<!-- 主容器 -->
<div class="container" id="app">

  <!-- ===== 面板 0: WELCOME (主页) ===== -->
  <div id="panel-0" class="panel active">
    <!-- 个人卡片 -->
    <div class="main-card">
      <div class="cover-wrap">
        <!-- 初始占位图，请在编辑模式下点击替换为你的封面图 -->
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Crect width='160' height='90' fill='%23111'/%3E%3Ctext x='80' y='49' font-size='8' fill='%23444' text-anchor='middle' font-family='monospace'%3E点击更换封面图%3C/text%3E%3C/svg%3E" alt="封面">
      </div>
      <!-- 头像（位置按第5张图） -->
      <div class="avatar-wrap">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23222'/%3E%3Ctext x='50' y='69' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像">
      </div>
      <div class="profile-info">
        <div class="name-row">
          <div class="name" data-edit>水水</div>
        </div>
        <div class="handle" data-edit>@InfernalBeyond.com</div>
        <div class="stats">
          <div><span data-edit>520</span> 正在关注</div>
          <div><span data-edit>1,314</span> 关注者</div>
        </div>
      </div>
      
      <!-- 三图网格（点击可替换图片） -->
      <div class="image-grid">
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🏞️%3C/text%3E%3C/svg%3E"></div>
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E📸%3C/text%3E%3C/svg%3E"></div>
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🌌%3C/text%3E%3C/svg%3E"></div>
      </div>

      <!-- 引言块 -->
      <div class="quote-block">
        <p data-edit>——They say time devours all things but say nothing about what becomes of the things as time digest them.What if life is consummated only by the way that time consumes it?What if to live is to be devoured by time and recycled?</p>
      </div>

      <!-- 发帖框 -->
      <div class="post-box">
        <input class="post-input" placeholder="说点什么..." data-edit>
        <div class="post-tools">
          <div class="tool-icons">
            <span>🖼️</span><span>👥</span><span>📍</span>
          </div>
          <button class="publish-btn"><span>发布</span> <span>→</span></button>
        </div>
      </div>
    </div>

    <!-- 动态卡片流 -->
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-header">
        <div class="feed-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23555'/%3E%3Ctext x='50' y='69' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EW%3C/text%3E%3C/svg%3E"></div>
        <div>
          <div class="feed-name" data-edit>水水</div>
          <div class="feed-handle" data-edit>@InfernalBeyond.com</div>
        </div>
        <div class="feed-time" data-edit>7小时前</div>
      </div>
      <div class="feed-text" data-edit>这里是暗网吗</div>
      <div class="feed-footer">
        <span>💬 评论</span>
      </div>
    </div>

    <!-- 添加卡片按钮 -->
    <button class="add-card-btn" onclick="addCard('panel-0')">＋ 添加一张动态卡片</button>
  </div>

  <!-- ===== 面板 1: PERSONAL INFO ===== -->
  <div id="panel-1" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-text" data-edit>这里是个人信息区域，你可以在编辑模式下自由修改文字和图片。</div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-1')">＋ 添加一张卡片</button>
  </div>

  <!-- ===== 面板 2: MY STYLE ===== -->
  <div id="panel-2" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-text" data-edit>这里是个人风格展示区域，可以放你的作品或爱好。</div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-2')">＋ 添加一张卡片</button>
  </div>

  <!-- ===== 面板 3: CATEGORIES ===== -->
  <div id="panel-3" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-text" data-edit>这里是分类区域，可以放你的收藏或分类信息。</div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-3')">＋ 添加一张卡片</button>
  </div>

</div>

<!-- 底部导航（仿截图） -->
<div class="bottom-nav">
  <div class="bottom-nav-item active"><span class="icon">⊞</span><span>Desk</span></div>
  <div class="bottom-nav-item"><span class="icon">👤</span><span>Space</span></div>
  <div class="bottom-nav-item"><span class="icon">👥</span><span>Friends</span></div>
  <div class="bottom-nav-item"><span class="icon">⚙️</span><span>设置</span></div>
</div>

<div class="toast" id="toast"></div>
<input type="file" id="fileInput" accept="image/*" hidden>
<input type="file" id="bgInput" accept="image/*" hidden>

<script>
(function(){
  'use strict';
  var KEY_CONTENT = 'myPage.glass.v3';
  var KEY_THEME   = 'myPage.theme.v1';
  var KEY_COLOR   = 'myPage.color.v1';
  var KEY_BG_IMG  = 'myPage.bgImage.v1';
  var KEY_STYLE   = 'myPage.styleSettings.v1';
  
  var app = document.getElementById('app');
  var fileInput = document.getElementById('fileInput');
  var bgInput = document.getElementById('bgInput');
  var toastEl = document.getElementById('toast');
  var currentImg = null;
  var editMode = false;
  var activeCard = null;
  var DEFAULT_HTML = app.innerHTML;

  // 样式滑块默认值
  var styleSettings = {
    opacity: 65,
    gradColor1: '#1c1c22',
    gradColor2: '#0d0d10',
    angle: 135,
    accent: '#4a9eff'
  };

  // 初始化加载
  try {
    var saved = localStorage.getItem(KEY_CONTENT);
    if(saved) app.innerHTML = saved;
    
    var savedTheme = localStorage.getItem(KEY_THEME) || 'dark';
    var savedBgImg = localStorage.getItem(KEY_BG_IMG) || 'none';
    var savedStyle = localStorage.getItem(KEY_STYLE);
    if(savedStyle) styleSettings = JSON.parse(savedStyle);

    applyTheme(savedTheme === 'dark');
    applyColor(styleSettings.accent);
    applySliders(styleSettings);
    
    if(savedBgImg !== 'none'){
      document.body.style.backgroundImage = 'url(' + savedBgImg + ')';
    }
  } catch(e){}

  // 应用主题
  function applyTheme(dark){
    document.documentElement.classList.toggle('light', !dark);
    document.getElementById('themeIcon').textContent = dark ? '☀' : '◐';
    localStorage.setItem(KEY_THEME, dark ? 'dark' : 'light');
  }
  document.getElementById('btnTheme').addEventListener('click', function(){
    applyTheme(!document.documentElement.classList.contains('light'));
  });

  // 应用全局颜色
  function applyColor(color){
    document.documentElement.style.setProperty('--accent', color);
    var hex = color.replace('#', '');
    if (hex.length === 3) hex = hex.split('').map(function(c){return c+c}).join('');
    var r = parseInt(hex.substr(0,2), 16), g = parseInt(hex.substr(2,2), 16), b = parseInt(hex.substr(4,2), 16);
    var brightness = (r * 299 + g * 587 + b * 114) / 1000;
    document.documentElement.style.setProperty('--accent-text', brightness > 128 ? '#000000' : '#ffffff');
    localStorage.setItem(KEY_COLOR, color);
  }

  // 应用滑块设置（透明度、渐变、角度）
  function applySliders(settings) {
    var opacity = settings.opacity / 100;
    // 将十六进制颜色转换为带透明度的 rgba
    function hexToRgba(hex, alpha) {
      var r = parseInt(hex.slice(1, 3), 16), g = parseInt(hex.slice(3, 5), 16), b = parseInt(hex.slice(5, 7), 16);
      return `rgba(${r}, ${g}, ${b}, ${alpha})`;
    }
    
    var bgColor = hexToRgba(settings.gradColor1, opacity);
    var gradColor1 = hexToRgba(settings.gradColor1, opacity);
    var gradColor2 = hexToRgba(settings.gradColor2, opacity);

    document.documentElement.style.setProperty('--card-bg-color', bgColor);
    document.documentElement.style.setProperty('--card-gradient', `linear-gradient(${settings.angle}deg, ${gradColor1} 0%, ${gradColor2} 100%)`);
    
    // 同步 UI 控件
    document.getElementById('opacitySlider').value = settings.opacity;
    document.getElementById('opacityVal').textContent = settings.opacity + '%';
    document.getElementById('gradColor1').value = settings.gradColor1;
    document.getElementById('gradColor2').value = settings.gradColor2;
    document.getElementById('gradAngle').value = settings.angle;
    document.getElementById('angleVal').textContent = settings.angle + '°';
    document.getElementById('accentColor').value = settings.accent;
    
    localStorage.setItem(KEY_STYLE, JSON.stringify(settings));
  }

  // 绑定滑块与面板事件
  document.getElementById('btnStylePanel').addEventListener('click', function(){
    document.getElementById('stylePanel').classList.toggle('show');
  });

  document.getElementById('opacitySlider').addEventListener('input', function(e){
    styleSettings.opacity = e.target.value;
    applySliders(styleSettings);
  });
  document.getElementById('gradColor1').addEventListener('input', function(e){
    styleSettings.gradColor1 = e.target.value;
    applySliders(styleSettings);
  });
  document.getElementById('gradColor2').addEventListener('input', function(e){
    styleSettings.gradColor2 = e.target.value;
    applySliders(styleSettings);
  });
  document.getElementById('gradAngle').addEventListener('input', function(e){
    styleSettings.angle = e.target.value;
    applySliders(styleSettings);
  });
  document.getElementById('accentColor').addEventListener('input', function(e){
    styleSettings.accent = e.target.value;
    applyColor(styleSettings.accent);
    applySliders(styleSettings);
  });

  // 背景图更换
  document.getElementById('btnBgImage').addEventListener('click', function(){
    bgInput.value = ''; bgInput.click();
  });
  bgInput.addEventListener('change', function(){
    var file = bgInput.files && bgInput.files[0];
    if(!file) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      document.body.style.backgroundImage = 'url(' + ev.target.result + ')';
      try { localStorage.setItem(KEY_BG_IMG, ev.target.result); } catch(e){ toast('背景图太大，无法保存'); }
      toast('背景已更新');
    };
    reader.readAsDataURL(file);
  });

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
    
    if(on) { toast('编辑已开启，点击图片可更换'); }
    else { scheduleSave(); toast('已保存'); }
  }
  document.getElementById('btnEdit').addEventListener('click', function(){ setEdit(!editMode); });
  app.addEventListener('input', function(){ if(editMode) scheduleSave(); });

  // 点击卡片高亮（用于单独选中）
  app.addEventListener('click', function(e){
    if(!editMode) return;
    var card = e.target.closest('.main-card, .feed-card');
    if(card && !e.target.closest('button')){
      document.querySelectorAll('.main-card, .feed-card').forEach(function(c){ c.style.outline = 'none'; });
      card.style.outline = '2px solid var(--accent)';
      activeCard = card;
    }
  });

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

  // 顶部导航跳转
  document.querySelectorAll('.nav-link').forEach(function(link){
    link.addEventListener('click', function(){
      if(editMode) return;
      document.querySelectorAll('.nav-link').forEach(function(l){ l.classList.remove('active'); });
      this.classList.add('active');
      var target = this.getAttribute('data-target');
      document.querySelectorAll('.panel').forEach(function(p){ p.style.display = 'none'; });
      var panel = document.getElementById(target);
      if(panel) panel.style.display = 'block';
    });
  });

  // 添加卡片
  window.addCard = function(panelId){
    var panel = document.getElementById(panelId);
    if(!panel) return;
    var card = document.createElement('div');
    card.className = 'feed-card glass-card';
    card.innerHTML = `
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-header">
        <div class="feed-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23555'/%3E%3Ctext x='50' y='69' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EN%3C/text%3E%3C/svg%3E"></div>
        <div>
          <div class="feed-name" data-edit>新朋友</div>
          <div class="feed-handle" data-edit>@newuser</div>
        </div>
        <div class="feed-time" data-edit>刚刚</div>
      </div>
      <div class="feed-text" data-edit>点击这里输入内容...</div>
      <div class="feed-footer"><span>💬 评论</span></div>
    `;
    var addBtn = panel.querySelector('.add-card-btn');
    panel.insertBefore(card, addBtn);
    
    if(editMode){
      card.querySelectorAll('[data-edit]').forEach(function(el){
        el.setAttribute('contenteditable', 'true');
      });
    }
    scheduleSave();
    toast('已添加新卡片');
  };

  // 删除卡片
  app.addEventListener('click', function(e){
    var del = e.target.closest('.card-del');
    if(!del || !editMode) return;
    e.preventDefault();
    if(confirm('确定删除这张卡片吗？')){
      del.closest('.feed-card, .main-card').remove();
      scheduleSave();
      toast('卡片已删除');
    }
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
