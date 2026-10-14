<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0">
<title>个人主页</title>
<style>
  :root{
    --glass-blur: blur(24px) saturate(180%);
    --glass-border: rgba(255, 255, 255, 0.12);
    --bg-color: #0d0d10;
    --bg-image: none;
    --text: #f0f0f0;
    --text-sub: #999;
    --accent: #4a9eff;
    --accent-text: #ffffff;
    --card-bg-color: rgba(25, 25, 30, 0.65);
    --card-gradient: linear-gradient(135deg, #1c1c22 0%, #0d0d10 100%);
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
    position: relative; overflow-x: hidden;
  }

  /* ============ 工具栏 ============ */
  .toolbar{
    position: fixed; top: 0; left: 0; right: 0; z-index: 999;
    height: 50px; display: flex; align-items: center; gap: 8px; padding: 0 16px;
    background: rgba(20, 20, 25, 0.75); backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border-bottom: 1px solid var(--glass-border); overflow-x: auto; scrollbar-width: none;
  }
  html.light .toolbar { background: rgba(255, 255, 255, 0.75); }
  .toolbar::-webkit-scrollbar { display: none; }
  .toolbar .spacer{flex:1;}
  .tbtn{
    display: flex; align-items: center; gap: 5px; padding: 6px 12px; border-radius: 20px;
    border: 1px solid var(--glass-border); background: rgba(255,255,255,0.05);
    color: var(--text); cursor: pointer; font-size: 12px; font-family: monospace;
    transition: all 0.2s; backdrop-filter: blur(8px); white-space: nowrap;
  }
  .tbtn:active{transform: scale(0.95);}
  .tbtn.on{background: var(--accent); color: var(--accent-text); border-color: var(--accent);}
  .edit-only{display: none !important;}
  body.editing .edit-only{display: flex !important;}
  
  .bg-image-btn { cursor: pointer; padding: 6px 10px; border-radius: 8px; border: 1px solid var(--glass-border); font-size: 12px; }

  /* ============ 侧边样式面板 ============ */
  .style-panel {
    position: fixed; top: 60px; right: 16px; z-index: 9999; 
    width: 280px; padding: 16px; border-radius: 16px;
    background: rgba(20, 20, 25, 0.95); backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); box-shadow: 0 10px 40px rgba(0,0,0,0.5);
    display: none; flex-direction: column; gap: 10px;
    color: #fff; font-size: 12px; max-height: 80vh; overflow-y: auto;
  }
  .style-panel.show { display: flex; }
  .style-item { display: flex; justify-content: space-between; align-items: center; gap: 8px; margin-bottom: 4px; }
  .style-item input[type="range"] { flex: 1; cursor: pointer; }
  .style-item input[type="color"] { width: 28px; height: 28px; border: none; border-radius: 6px; cursor: pointer; background: transparent; }
  .style-panel h4 { font-size: 13px; margin: 6px 0 2px 0; border-bottom: 1px solid var(--glass-border); padding-bottom: 4px; color: var(--accent); }
  .style-btn { padding: 4px 8px; border-radius: 4px; border: 1px solid var(--glass-border); background: transparent; color: var(--text); cursor: pointer; font-size: 11px; }
  .style-btn.active { background: var(--accent); color: var(--accent-text); }
  .style-btn-group { display: flex; gap: 4px; }

  /* ============ 顶部导航栏 ============ */
  .top-nav {
    max-width: 640px; margin: 0 auto; display: flex; gap: 6px; padding: 12px 16px;
    position: sticky; top: 50px; z-index: 998;
    background: rgba(20, 20, 25, 0.75); backdrop-filter: var(--glass-blur);
    border-bottom: 1px solid var(--glass-border);
    overflow-x: auto; scrollbar-width: none;
  }
  html.light .top-nav { background: rgba(255, 255, 255, 0.75); }
  .top-nav::-webkit-scrollbar { display: none; }
  .nav-link {
    flex: 1 0 auto; text-align: center; padding: 8px 16px; border-radius: 20px;
    background: rgba(255,255,255,0.05); border: 1px solid var(--glass-border);
    color: var(--text-sub); text-decoration: none; font-size: 12px; font-family: monospace; letter-spacing: 0.1em;
    transition: all 0.2s ease; cursor: pointer; white-space: nowrap;
  }
  .nav-link.active { background: var(--accent); color: var(--accent-text); border-color: var(--accent); }

  /* ============ 主容器 ============ */
  .container { max-width: 640px; margin: 0 auto; padding: 16px; position: relative; }

  .glass-card {
    background-color: var(--card-bg-color);
    background-image: var(--card-gradient);
    backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 24px; overflow: hidden;
    box-shadow: 0 15px 35px rgba(0,0,0,0.3); margin-bottom: 16px; position: relative;
  }

  .main-card {
    background-color: var(--card-bg-color);
    background-image: var(--card-gradient);
    backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 24px; padding: 0 0 24px 0; overflow: hidden;
    box-shadow: 0 15px 35px rgba(0,0,0,0.3); margin-bottom: 16px; position: relative;
  }
  
  /* 封面图：边缘渐变融合 */
  .cover-wrap { width: 100%; height: 240px; background: #1a1a1a; position: relative; overflow: hidden; }
  .cover-wrap img { width: 100%; height: 100%; object-fit: cover; display: block; filter: grayscale(10%); }
  .cover-wrap::after {
    content: ""; position: absolute; bottom: 0; left: 0; right: 0; height: 160px;
    background: linear-gradient(to bottom, transparent 0%, var(--card-bg-color) 100%); 
    z-index: 1; pointer-events: none;
    -webkit-mask-image: linear-gradient(to bottom, black 20%, transparent 100%);
    mask-image: linear-gradient(to bottom, black 20%, transparent 100%);
  }

  /* 个人信息区 */
  .profile-info { padding: 16px 24px 0; text-align: left; position: relative; z-index: 2; }
  .profile-header-row { display: flex; align-items: flex-end; gap: 16px; margin-bottom: 12px; }
  
  .avatar-wrap { 
    width: 80px; height: 80px; border-radius: 50%; border: 3px solid var(--glass-border); 
    background: var(--glass-bg); overflow: hidden; box-shadow: 0 8px 25px rgba(0,0,0,0.6); flex: none;
  }
  .avatar-wrap img { width: 100%; height: 100%; object-fit: cover; }

  .name-handle { flex: 1; }
  .name { font-size: 26px; font-weight: 700; letter-spacing: 0.02em; margin-bottom: 4px; }
  .handle { font-size: 14px; color: var(--text-sub); font-family: monospace; }
  
  .stats { display: flex; justify-content: flex-start; gap: 20px; font-size: 14px; margin-bottom: 24px; }
  .stats span { font-weight: 600; color: var(--text); }
  .stats span:last-child { font-weight: 400; color: var(--text-sub); margin-left: 4px; }

  .image-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 8px; margin: 0 24px 24px 24px; }
  .grid-item { aspect-ratio: 1; border-radius: 12px; overflow: hidden; background: rgba(255,255,255,0.05); position: relative; }
  .grid-item img { width: 100%; height: 100%; object-fit: cover; display: block; }

  .quote-block { margin: 0 24px 24px 24px; padding: 16px; border: 1px solid var(--glass-border); border-radius: 16px; background: rgba(255,255,255,0.02); }
  .quote-block p { font-size: 14px; line-height: 1.8; color: var(--text-sub); font-style: italic; }

  .post-box { margin: 0 24px 16px 24px; background: rgba(255,255,255,0.03); border: 1px solid var(--glass-border); border-radius: 20px; padding: 16px; display: flex; flex-direction: column; gap: 12px; cursor: pointer; transition: all 0.2s; }
  .post-box:hover { background: rgba(255,255,255,0.08); border-color: var(--accent); }
  .post-input { width: 100%; background: transparent; border: none; color: var(--text); font-size: 15px; outline: none; resize: none; font-family: inherit; cursor: pointer; }
  .post-tools { display: flex; justify-content: space-between; align-items: center; }
  .tool-icons { display: flex; gap: 12px; color: var(--text-sub); font-size: 18px; cursor: pointer; }
  .publish-btn { background: var(--accent); color: var(--accent-text); border: none; padding: 8px 20px; border-radius: 20px; font-size: 13px; font-weight: 600; cursor: pointer; display: flex; align-items: center; gap: 6px; }

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

  /* ============ 浮动文本框层 ============ */
  #float-layer {
    position: absolute; top: 0; left: 0; width: 100%; height: 100%;
    pointer-events: none; z-index: 997;
  }
  .float-text-block {
    position: absolute; z-index: 998; min-width: 100px; min-height: 40px;
    padding: 14px; border-radius: 12px; border: 2px dashed var(--accent);
    background: rgba(20, 20, 25, 0.85); backdrop-filter: blur(12px);
    color: var(--text); font-size: 15px; line-height: 1.5; word-break: break-all;
    cursor: move; outline: none; box-sizing: border-box; display: none;
    box-shadow: 0 10px 30px rgba(0,0,0,0.5); pointer-events: auto;
    overflow: hidden; /* 防止内容溢出 */
  }
  body.editing .float-text-block { display: block; }
  .float-text-block:focus { cursor: text; border: 2px solid var(--accent); box-shadow: 0 0 20px rgba(74, 158, 255, 0.6); }
  
  .float-text-block .drag-handle {
    position: absolute; top: 2px; right: 2px; width: 26px; height: 26px;
    background: var(--accent); color: var(--accent-text); border-radius: 6px;
    display: flex; align-items: center; justify-content: center;
    font-size: 14px; cursor: move; opacity: 0.7; z-index: 2;
  }
  .float-text-block .drag-handle:hover { opacity: 1; }
  
  /* 右下角调整大小手柄 */
  .float-text-block .resize-handle {
    position: absolute; bottom: 0; right: 0; width: 20px; height: 20px;
    background: linear-gradient(135deg, transparent 50%, var(--accent) 50%);
    cursor: se-resize; z-index: 3; border-bottom-right-radius: 10px;
  }

  .float-del {
    position: absolute; top: -12px; left: -12px; width: 24px; height: 24px;
    background: #e5484d; color: #fff; border-radius: 50%; border: none;
    font-size: 14px; cursor: pointer; display: none; align-items: center; justify-content: center;
    z-index: 4;
  }
  body.editing .float-text-block:hover .float-del { display: flex; }

  /* ============ 底部导航 ============ */
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
  body.editing .feed-card .card-del { display: flex; }
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
  body.editing [data-edit] { outline: 1px dashed transparent; outline-offset: 4px; border-radius: 4px; cursor: text; transition: outline 0.2s, background 0.2s; position: relative; }
  body.editing [data-edit]:hover { outline: 1px dashed var(--accent); background: rgba(255,255,255,0.03); }
  body.editing [data-edit]:focus { outline: 2px solid var(--accent); background: rgba(74, 158, 255, 0.1); box-shadow: 0 0 10px rgba(74, 158, 255, 0.3); }
  
  body.editing .cover-wrap:hover::after, 
  body.editing .avatar-wrap:hover::after, 
  body.editing .grid-item:hover::after, 
  body.editing .feed-avatar:hover::after {
    content: "点击换图"; position: absolute; inset: 0; display: flex; align-items: center; justify-content: center;
    background: rgba(0,0,0,0.6); color: #fff; font-size: 12px; z-index: 5;
    pointer-events: none;
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
  <button class="tbtn edit-only" id="btnAddFloat"><span class="ico">＋</span><span class="label">浮动文本</span></button>
  <button class="tbtn edit-only" id="btnExport"><span class="ico">⬇</span><span class="label">导出</span></button>
  <button class="tbtn edit-only" id="btnReset"><span class="ico">⟲</span><span class="label">重置</span></button>
  <span class="spacer"></span>
  <button class="tbtn edit-only bg-image-btn" id="btnBgImage">🖼️ 背景</button>
  <button class="tbtn" id="btnTheme"><span class="ico" id="themeIcon">☀</span></button>
</div>

<!-- 样式面板 -->
<div class="style-panel" id="stylePanel">
  <div style="position:relative;">
    <h4>卡片样式调节</h4>
  </div>
  <div class="style-item">
    <span>透明度</span>
    <input type="range" id="opacitySlider" min="0" max="100" value="65">
    <span id="opacityVal">65%</span>
  </div>
  <div class="style-item">
    <span>渐变起始</span>
    <input type="color" id="gradColor1" value="#1c1c22">
  </div>
  <div class="style-item">
    <span>渐变结束</span>
    <input type="color" id="gradColor2" value="#0d0d10">
  </div>
  <div class="style-item">
    <span>渐变角度</span>
    <input type="range" id="gradAngle" min="0" max="360" value="135">
    <span id="angleVal">135°</span>
  </div>
  <div class="style-item">
    <span>全局主色</span>
    <input type="color" id="accentColor" value="#4a9eff">
  </div>

  <h4>文字样式（点击文字后激活）</h4>
  <div class="style-item">
    <span>字体大小</span>
    <input type="range" id="fontSize" min="12" max="48" value="16">
    <span id="fontSizeVal">16px</span>
  </div>
  <div class="style-item">
    <span>方向</span>
    <select id="textDir" style="background:#333; color:#fff; border:1px solid #555; padding:2px 6px; border-radius:4px; font-size:11px;">
      <option value="horizontal-tb">横排</option>
      <option value="vertical-rl">竖排</option>
    </select>
  </div>
  <div class="style-item">
    <span>对齐</span>
    <div class="style-btn-group">
      <button class="style-btn" data-align="left">左</button>
      <button class="style-btn" data-align="center">中</button>
      <button class="style-btn" data-align="right">右</button>
    </div>
  </div>
  <div class="style-item">
    <span>样式</span>
    <div class="style-btn-group">
      <button class="style-btn" id="btnBold">B</button>
      <button class="style-btn" id="btnItalic">I</button>
      <button class="style-btn" id="btnUnderline">U</button>
    </div>
  </div>
  <div class="style-item">
    <span>文字颜色</span>
    <input type="color" id="textColor" value="#f0f0f0">
  </div>
  <div class="style-item">
    <span>文字背景</span>
    <input type="color" id="textBg" value="#000000">
    <span id="bgOpacityVal">0%</span>
    <input type="range" id="bgOpacity" min="0" max="100" value="0" style="width:60px;">
  </div>
  <div class="style-item">
    <span>边框颜色</span>
    <input type="color" id="textBorderColor" value="#4a9eff">
  </div>
  <div class="style-item">
    <span>边框粗细</span>
    <input type="range" id="borderWidth" min="1" max="5" value="1">
    <span id="borderWidthVal">1px</span>
  </div>
  <div class="style-item">
    <span>边框圆角</span>
    <input type="range" id="borderRadius" min="0" max="20" value="0">
    <span id="borderRadiusVal">0px</span>
  </div>
  <div class="style-item">
    <span>边框</span>
    <button class="style-btn" id="btnToggleBorder">添加边框</button>
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
  
  <!-- 浮动文本框图层（修复：将浮动元素存入此层，确保能被保存） -->
  <div id="float-layer"></div>

  <!-- ===== 面板 0 ===== -->
  <div id="panel-0" class="panel active">
    <div class="main-card">
      <div class="cover-wrap">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Crect width='160' height='90' fill='%23111'/%3E%3Ctext x='80' y='49' font-size='8' fill='%23444' text-anchor='middle' font-family='monospace'%3E点击更换封面图%3C/text%3E%3C/svg%3E" alt="封面">
      </div>
      <div class="profile-info">
        <div class="profile-header-row">
          <div class="avatar-wrap">
            <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23222'/%3E%3Ctext x='50' y='69' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像">
          </div>
          <div class="name-handle">
            <div class="name" data-edit>水水</div>
            <div class="handle" data-edit>@InfernalBeyond.com</div>
          </div>
        </div>
        <div class="stats">
          <div><span data-edit>520</span> 正在关注</div>
          <div><span data-edit>1,314</span> 关注者</div>
        </div>
      </div>
      
      <div class="image-grid">
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🏞️%3C/text%3E%3C/svg%3E"></div>
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E📸%3C/text%3E%3C/svg%3E"></div>
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🌌%3C/text%3E%3C/svg%3E"></div>
      </div>

      <div class="quote-block">
        <p data-edit>——They say time devours all things but say nothing about what becomes of the things as time digest them.What if life is consummated only by the way that time consumes it?What if to live is to be devoured by time and recycled?</p>
      </div>

      <!-- 发帖框：点击触发跳转 -->
      <div class="post-box" id="postBoxTrigger">
        <input class="post-input" placeholder="说点什么..." data-edit>
        <div class="post-tools">
          <div class="tool-icons">
            <span>🖼️</span><span>👥</span><span>📍</span>
          </div>
          <button class="publish-btn"><span data-edit>发布</span> <span>→</span></button>
        </div>
      </div>
    </div>

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

    <button class="add-card-btn" onclick="addCard('panel-0')" style="margin-bottom:8px;">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-0')">＋ 添加自由文本块</button>
  </div>

  <!-- ===== 面板 1 ===== -->
  <div id="panel-1" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-text" data-edit>这里是个人信息区域，你可以在编辑模式下自由修改文字和图片。</div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-1')">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-1')">＋ 添加自由文本块</button>
  </div>

  <!-- ===== 面板 2 ===== -->
  <div id="panel-2" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-text" data-edit>这里是个人风格展示区域，可以放你的作品或爱好。</div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-2')">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-2')">＋ 添加自由文本块</button>
  </div>

  <!-- ===== 面板 3 ===== -->
  <div id="panel-3" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-text" data-edit>这里是分类区域，可以放你的收藏或分类信息。</div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-3')">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-3')">＋ 添加自由文本块</button>
  </div>

</div>

<!-- 底部导航 -->
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
  var KEY_CONTENT = 'myPage.glass.v8';
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
  var activeTextEl = null; 
  var DEFAULT_HTML = app.innerHTML;

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

  function applyTheme(dark){
    document.documentElement.classList.toggle('light', !dark);
    document.getElementById('themeIcon').textContent = dark ? '☀' : '◐';
    localStorage.setItem(KEY_THEME, dark ? 'dark' : 'light');
  }
  document.getElementById('btnTheme').addEventListener('click', function(){
    applyTheme(!document.documentElement.classList.contains('light'));
  });

  function applyColor(color){
    document.documentElement.style.setProperty('--accent', color);
    var hex = color.replace('#', '');
    if (hex.length === 3) hex = hex.split('').map(function(c){return c+c}).join('');
    var r = parseInt(hex.substr(0,2), 16), g = parseInt(hex.substr(2,2), 16), b = parseInt(hex.substr(4,2), 16);
    var brightness = (r * 299 + g * 587 + b * 114) / 1000;
    document.documentElement.style.setProperty('--accent-text', brightness > 128 ? '#000000' : '#ffffff');
    localStorage.setItem(KEY_COLOR, color);
  }

  function applySliders(settings) {
    var opacity = settings.opacity / 100;
    function hexToRgba(hex, alpha) {
      var r = parseInt(hex.slice(1, 3), 16), g = parseInt(hex.slice(3, 5), 16), b = parseInt(hex.slice(5, 7), 16);
      return `rgba(${r}, ${g}, ${b}, ${alpha})`;
    }
    
    var bgColor = hexToRgba(settings.gradColor1, opacity);
    var gradColor1 = hexToRgba(settings.gradColor1, opacity);
    var gradColor2 = hexToRgba(settings.gradColor2, opacity);

    document.documentElement.style.setProperty('--card-bg-color', bgColor);
    document.documentElement.style.setProperty('--card-gradient', `linear-gradient(${settings.angle}deg, ${gradColor1} 0%, ${gradColor2} 100%)`);
    
    document.getElementById('opacitySlider').value = settings.opacity;
    document.getElementById('opacityVal').textContent = settings.opacity + '%';
    document.getElementById('gradColor1').value = settings.gradColor1;
    document.getElementById('gradColor2').value = settings.gradColor2;
    document.getElementById('gradAngle').value = settings.angle;
    document.getElementById('angleVal').textContent = settings.angle + '°';
    document.getElementById('accentColor').value = settings.accent;
    
    localStorage.setItem(KEY_STYLE, JSON.stringify(settings));
  }

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

  // ============ 文字样式绑定 ============
  function updateTextStyleUI() {
    if(!activeTextEl) return;
    var style = window.getComputedStyle(activeTextEl);
    document.getElementById('fontSize').value = parseInt(style.fontSize) || 16;
    document.getElementById('fontSizeVal').textContent = (parseInt(style.fontSize) || 16) + 'px';
    document.getElementById('textColor').value = style.color || '#f0f0f0';
    document.getElementById('textDir').value = style.writingMode || 'horizontal-tb';
    document.getElementById('borderWidth').value = parseInt(style.borderWidth) || 1;
    document.getElementById('borderWidthVal').textContent = (parseInt(style.borderWidth) || 1) + 'px';
    document.getElementById('borderRadius').value = parseInt(style.borderRadius) || 0;
    document.getElementById('borderRadiusVal').textContent = (parseInt(style.borderRadius) || 0) + 'px';
    
    var bgColor = style.backgroundColor;
    if(bgColor && bgColor !== 'rgba(0, 0, 0, 0)') {
      document.getElementById('textBg').value = bgColor;
      document.getElementById('bgOpacity').value = 100;
      document.getElementById('bgOpacityVal').textContent = '100%';
    } else {
      document.getElementById('bgOpacity').value = 0;
      document.getElementById('bgOpacityVal').textContent = '0%';
    }
  }

  document.getElementById('fontSize').addEventListener('input', function(e){
    if(activeTextEl) { activeTextEl.style.fontSize = e.target.value + 'px'; document.getElementById('fontSizeVal').textContent = e.target.value + 'px'; }
  });
  document.getElementById('textDir').addEventListener('change', function(e){
    if(activeTextEl) activeTextEl.style.writingMode = e.target.value;
  });
  document.querySelectorAll('.style-btn[data-align]').forEach(function(btn){
    btn.addEventListener('click', function(){
      if(activeTextEl) activeTextEl.style.textAlign = this.getAttribute('data-align');
    });
  });
  document.getElementById('btnBold').addEventListener('click', function(){
    if(activeTextEl) { var w = activeTextEl.style.fontWeight === 'bold' ? 'normal' : 'bold'; activeTextEl.style.fontWeight = w; this.classList.toggle('active', w === 'bold'); }
  });
  document.getElementById('btnItalic').addEventListener('click', function(){
    if(activeTextEl) { var s = activeTextEl.style.fontStyle === 'italic' ? 'normal' : 'italic'; activeTextEl.style.fontStyle = s; this.classList.toggle('active', s === 'italic'); }
  });
  document.getElementById('btnUnderline').addEventListener('click', function(){
    if(activeTextEl) { var d = activeTextEl.style.textDecoration === 'underline' ? 'none' : 'underline'; activeTextEl.style.textDecoration = d; this.classList.toggle('active', d === 'underline'); }
  });
  document.getElementById('textColor').addEventListener('input', function(e){
    if(activeTextEl) activeTextEl.style.color = e.target.value;
  });
  
  document.getElementById('textBg').addEventListener('input', function(e){
    if(activeTextEl) {
      activeTextEl.dataset.bgColor = e.target.value;
      var alpha = document.getElementById('bgOpacity').value / 100;
      var hex = e.target.value.replace('#', '');
      var r = parseInt(hex.substr(0,2), 16), g = parseInt(hex.substr(2,2), 16), b = parseInt(hex.substr(4,2), 16);
      activeTextEl.style.backgroundColor = `rgba(${r}, ${g}, ${b}, ${alpha})`;
    }
  });
  document.getElementById('bgOpacity').addEventListener('input', function(e){
    if(activeTextEl) {
      var color = activeTextEl.dataset.bgColor || document.getElementById('textBg').value;
      var hex = color.replace('#', '');
      var r = parseInt(hex.substr(0,2), 16), g = parseInt(hex.substr(2,2), 16), b = parseInt(hex.substr(4,2), 16);
      activeTextEl.style.backgroundColor = `rgba(${r}, ${g}, ${b}, ${e.target.value / 100})`;
      document.getElementById('bgOpacityVal').textContent = e.target.value + '%';
    }
  });

  document.getElementById('borderWidth').addEventListener('input', function(e){
    if(activeTextEl) {
      activeTextEl.style.borderWidth = e.target.value + 'px';
      activeTextEl.style.borderStyle = 'solid';
      activeTextEl.style.borderColor = document.getElementById('textBorderColor').value;
      document.getElementById('borderWidthVal').textContent = e.target.value + 'px';
    }
  });
  document.getElementById('borderRadius').addEventListener('input', function(e){
    if(activeTextEl) {
      activeTextEl.style.borderRadius = e.target.value + 'px';
      document.getElementById('borderRadiusVal').textContent = e.target.value + 'px';
    }
  });
  document.getElementById('textBorderColor').addEventListener('input', function(e){
    if(activeTextEl && activeTextEl.style.borderStyle === 'solid') activeTextEl.style.borderColor = e.target.value;
  });
  document.getElementById('btnToggleBorder').addEventListener('click', function(){
    if(activeTextEl) {
      if(activeTextEl.style.borderStyle === 'solid') {
        activeTextEl.style.borderStyle = 'none';
        this.textContent = '添加边框';
      } else {
        activeTextEl.style.borderStyle = 'solid';
        activeTextEl.style.borderWidth = document.getElementById('borderWidth').value + 'px';
        activeTextEl.style.borderColor = document.getElementById('textBorderColor').value;
        this.textContent = '去除边框';
      }
    }
  });

  // ============ 浮动文本框（修复保存、位置、增加大小调整） ============
  var floatIdCounter = 0;
  document.getElementById('btnAddFloat').addEventListener('click', function(){
    var floatBlock = document.createElement('div');
    floatBlock.className = 'float-text-block';
    floatBlock.id = 'float-' + (++floatIdCounter);
    // 默认出现在页面可视区域中央
    floatBlock.style.left = '50px';
    floatBlock.style.top = (window.scrollY + 150) + 'px'; // 相对于文档的位置
    floatBlock.style.width = '150px';
    floatBlock.style.height = 'auto';
    floatBlock.setAttribute('contenteditable', 'true');
    floatBlock.innerHTML = '自由文本框<br>拖拽移动 · 右下角缩放';
    
    // 拖拽手柄
    var dragHandle = document.createElement('div');
    dragHandle.className = 'drag-handle';
    dragHandle.innerHTML = '≡';
    floatBlock.appendChild(dragHandle);

    // 缩放手柄
    var resizeHandle = document.createElement('div');
    resizeHandle.className = 'resize-handle';
    floatBlock.appendChild(resizeHandle);

    // 删除按钮
    var delBtn = document.createElement('button');
    delBtn.className = 'float-del';
    delBtn.innerHTML = '×';
    delBtn.onclick = function(e) {
      e.stopPropagation();
      if(confirm('删除这个浮动文本框吗？')) {
        floatBlock.remove();
        scheduleSave();
      }
    };
    floatBlock.appendChild(delBtn);

    // 拖拽逻辑
    dragHandle.addEventListener('mousedown', startDrag);
    dragHandle.addEventListener('touchstart', startDrag, {passive: false});

    var isDragging = false;
    var startX, startY, startLeft, startTop;

    function startDrag(e) {
      if(!editMode) return;
      e.preventDefault();
      isDragging = true;
      var clientX = e.clientX || e.touches[0].clientX;
      var clientY = e.clientY || e.touches[0].clientY;
      startX = clientX;
      startY = clientY;
      
      startLeft = floatBlock.offsetLeft;
      startTop = floatBlock.offsetTop;
      
      document.addEventListener('mousemove', onDrag);
      document.addEventListener('touchmove', onDrag, {passive: false});
      document.addEventListener('mouseup', stopDrag);
      document.addEventListener('touchend', stopDrag);
    }

    function onDrag(e) {
      if(!isDragging) return;
      e.preventDefault();
      var clientX = e.clientX || e.touches[0].clientX;
      var clientY = e.clientY || e.touches[0].clientY;
      var dx = clientX - startX;
      var dy = clientY - startY;
      
      floatBlock.style.left = (startLeft + dx) + 'px';
      floatBlock.style.top = (startTop + dy) + 'px';
    }

    function stopDrag() {
      isDragging = false;
      document.removeEventListener('mousemove', onDrag);
      document.removeEventListener('touchmove', onDrag);
      document.removeEventListener('mouseup', stopDrag);
      document.removeEventListener('touchend', stopDrag);
      scheduleSave();
    }

    // 缩放逻辑
    resizeHandle.addEventListener('mousedown', startResize);
    resizeHandle.addEventListener('touchstart', startResize, {passive: false});

    var isResizing = false;
    var startW, startH, startResX, startResY;

    function startResize(e) {
      if(!editMode) return;
      e.preventDefault();
      e.stopPropagation(); // 防止触发拖拽
      isResizing = true;
      var clientX = e.clientX || e.touches[0].clientX;
      var clientY = e.clientY || e.touches[0].clientY;
      startResX = clientX;
      startResY = clientY;
      startW = floatBlock.offsetWidth;
      startH = floatBlock.offsetHeight;
      
      document.addEventListener('mousemove', onResize);
      document.addEventListener('touchmove', onResize, {passive: false});
      document.addEventListener('mouseup', stopResize);
      document.addEventListener('touchend', stopResize);
    }

    function onResize(e) {
      if(!isResizing) return;
      e.preventDefault();
      var clientX = e.clientX || e.touches[0].clientX;
      var clientY = e.clientY || e.touches[0].clientY;
      var dx = clientX - startResX;
      var dy = clientY - startResY;
      
      floatBlock.style.width = Math.max(100, startW + dx) + 'px';
      floatBlock.style.height = Math.max(40, startH + dy) + 'px';
    }

    function stopResize() {
      isResizing = false;
      document.removeEventListener('mousemove', onResize);
      document.removeEventListener('touchmove', onResize);
      document.removeEventListener('mouseup', stopResize);
      document.removeEventListener('touchend', stopResize);
      scheduleSave();
    }

    floatBlock.addEventListener('focusin', function() {
      activeTextEl = floatBlock;
      updateTextStyleUI();
    });

    // 追加到图层
    document.getElementById('float-layer').appendChild(floatBlock);
    floatBlock.focus();
    
    toast('浮动文本框已添加，可拖拽移动和缩放');
    scheduleSave();
  });

  // ============ 文本编辑状态跟踪 ============
  document.addEventListener('focusin', function(e){
    if(!editMode) return;
    var el = e.target.closest('[contenteditable="true"], .float-text-block');
    if(el) {
      activeTextEl = el;
      updateTextStyleUI();
      document.getElementById('stylePanel').classList.add('show');
      el.style.transition = 'box-shadow 0.3s';
      el.style.boxShadow = '0 0 0 4px var(--accent)';
      setTimeout(function(){ el.style.boxShadow = 'none'; }, 500);
    }
  });

  // 点击卡片高亮
  app.addEventListener('click', function(e){
    if(!editMode) return;
    var card = e.target.closest('.main-card, .feed-card, .text-block');
    if(card && !e.target.closest('button') && !e.target.closest('img[data-edit-img]')){
      document.querySelectorAll('.main-card, .feed-card, .text-block').forEach(function(c){ c.style.outline = 'none'; });
      card.style.outline = '2px solid var(--accent)';
      activeCard = card;
    }
  });

  app.addEventListener('click', function(e){
    if(!editMode) return;
    var img = e.target.closest('img[data-edit-img]');
    if(img){ 
      e.preventDefault(); 
      e.stopPropagation(); 
      currentImg = img; 
      fileInput.value = ''; 
      fileInput.click(); 
    }
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

  // ============ 导航跳转逻辑 ============
  function switchTab(targetPanelId) {
    document.querySelectorAll('.nav-link').forEach(function(l){ l.classList.remove('active'); });
    var targetNav = document.querySelector('.nav-link[data-target="' + targetPanelId + '"]');
    if(targetNav) targetNav.classList.add('active');
    
    document.querySelectorAll('.panel').forEach(function(p){ p.style.display = 'none'; });
    var panel = document.getElementById(targetPanelId);
    if(panel) panel.style.display = 'block';
    
    if(editMode) toast('已跳转至 ' + (targetNav ? targetNav.textContent : targetPanelId));
  }

  document.querySelectorAll('.nav-link').forEach(function(link){
    link.addEventListener('click', function(){
      var target = this.getAttribute('data-target');
      switchTab(target);
    });
  });

  // ============ 点击发布框跳转到第二个界面 ============
  document.getElementById('postBoxTrigger').addEventListener('click', function(e) {
    if(editMode && e.target.closest('[data-edit]')) return; 
    switchTab('panel-1');
    toast('已跳转到个人信息页');
  });

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

  window.addTextBlock = function(panelId){
    var panel = document.getElementById(panelId);
    if(!panel) return;
    var block = document.createElement('div');
    block.className = 'text-block';
    block.setAttribute('contenteditable', 'true');
    block.innerHTML = '点击输入自由文本内容...';
    var addBtn = panel.querySelectorAll('.add-card-btn')[1]; 
    panel.insertBefore(block, addBtn);
    if(editMode) block.focus();
    scheduleSave();
    toast('已添加自由文本块');
  };

  app.addEventListener('click', function(e){
    var del = e.target.closest('.card-del');
    if(!del || !editMode) return;
    e.preventDefault();
    if(confirm('确定删除这个内容吗？')){
      del.closest('.feed-card, .text-block').remove();
      scheduleSave();
      toast('已删除');
    }
  });

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

  function setEdit(on){
    editMode = on;
    document.body.classList.toggle('editing', on);
    document.querySelectorAll('[data-edit], .text-block, .float-text-block').forEach(function(el){
      if(on) { el.setAttribute('contenteditable', 'true'); el.setAttribute('spellcheck', 'false'); }
      else el.removeAttribute('contenteditable');
    });
    var btn = document.getElementById('btnEdit');
    btn.classList.toggle('on', on);
    btn.querySelector('.ico').textContent = on ? '✓' : '✎';
    btn.querySelector('.label').textContent = on ? '完成' : '编辑';
    
    if(on) { toast('编辑已开启，点击文字可调样式'); }
    else { scheduleSave(); toast('已保存'); }
  }
  document.getElementById('btnEdit').addEventListener('click', function(){ setEdit(!editMode); });
  app.addEventListener('input', function(){ if(editMode) scheduleSave(); });

  setEdit(false);
})();
</script>
</body>
</html>
