<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no">
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
    background-color: var(--bg-color); background-image: var(--bg-image);
    background-size: cover; background-position: center; background-attachment: fixed;
    color: var(--text); min-height: 100vh;
    padding-top: 60px; padding-bottom: 90px;
    transition: background 0.3s, color 0.3s; position: relative; overflow-x: hidden;
  }

  @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
  @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
  @keyframes zoomIn { from { opacity: 0; transform: scale(0.9); } to { opacity: 1; transform: scale(1); } }
  @keyframes slideLeft { from { opacity: 0; transform: translateX(-20px); } to { opacity: 1; transform: translateX(0); } }
  .anim-fadeIn { animation: fadeIn 1s ease forwards; }
  .anim-slideUp { animation: slideUp 1s ease forwards; }
  .anim-zoomIn { animation: zoomIn 1s ease forwards; }
  .anim-slideLeft { animation: slideLeft 1s ease forwards; }

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
    flex-shrink: 0;
  }
  .tbtn:active{transform: scale(0.95);}
  .tbtn.on{background: var(--accent); color: var(--accent-text); border-color: var(--accent);}
  .edit-only{display: none !important;}
  body.editing .edit-only{display: flex !important;}
  .bg-image-btn { cursor: pointer; padding: 6px 10px; border-radius: 8px; border: 1px solid var(--glass-border); font-size: 12px; }

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
  .style-btn-group { display: flex; gap: 4px; flex-wrap: wrap; }
  .font-select { width: 100%; padding: 4px; border-radius: 4px; background: #333; color: #fff; border: 1px solid #555; font-size: 11px; }

  .shape-picker {
    position: fixed; top: 60px; left: 50%; transform: translateX(-50%);
    z-index: 10000; background: rgba(20, 20, 25, 0.98); backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 16px;
    padding: 14px; box-shadow: 0 10px 40px rgba(0,0,0,0.6);
    display: none; grid-template-columns: repeat(5, 1fr); gap: 8px;
  }
  .shape-picker.show { display: grid; }
  .shape-pick-item {
    width: 48px; height: 48px; display: flex; align-items: center; justify-content: center;
    border-radius: 10px; cursor: pointer; color: var(--text); transition: all 0.2s;
    background: rgba(255,255,255,0.03); border: 1px solid var(--glass-border);
  }
  .shape-pick-item:hover { background: var(--accent); color: var(--accent-text); border-color: var(--accent); transform: scale(1.05); }
  .shape-pick-item svg { width: 30px; height: 30px; }
  .shape-picker-title { grid-column: 1 / -1; text-align: center; font-size: 12px; color: var(--accent); padding-bottom: 8px; border-bottom: 1px solid var(--glass-border); }

  .top-nav {
    max-width: 640px; margin: 0 auto; display: flex; gap: 6px; padding: 12px 16px;
    position: sticky; top: 50px; z-index: 998;
    background: rgba(20, 20, 25, 0.75); backdrop-filter: var(--glass-blur);
    border-bottom: 1px solid var(--glass-border); overflow-x: auto; scrollbar-width: none;
    align-items: center;
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

  .nav-collection {
    flex: 0 0 auto !important; display: flex; align-items: center; justify-content: center;
    padding: 8px 12px !important; min-width: 40px;
    color: var(--text) !important;
  }
  .nav-collection.active { background: var(--accent) !important; color: var(--accent-text) !important; border-color: var(--accent) !important; }
  .nav-collection svg { display: block; }

  .collection-menu {
    position: fixed; top: 108px; left: 16px;
    z-index: 10001;
    background: rgba(20, 20, 25, 0.98); backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 14px;
    padding: 8px; box-shadow: 0 12px 40px rgba(0,0,0,0.6);
    display: none; min-width: 180px; max-width: 80vw; max-height: 60vh; overflow-y: auto;
  }
  html.light .collection-menu { background: rgba(255, 255, 255, 0.98); }
  .collection-menu.show { display: block; animation: fadeIn 0.2s ease; }
  .collection-menu-title {
    font-size: 10px; color: var(--accent); padding: 4px 10px 8px; 
    border-bottom: 1px solid var(--glass-border); margin-bottom: 4px;
    letter-spacing: 0.15em; text-transform: uppercase;
    font-family: monospace;
  }
  .collection-item {
    display: block; padding: 8px 12px; border-radius: 8px; color: var(--text);
    text-decoration: none; font-size: 13px; cursor: pointer; transition: all 0.2s;
    font-family: monospace;
    white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
  }
  .collection-item:hover { background: rgba(255,255,255,0.08); }
  .collection-item.active { background: var(--accent); color: var(--accent-text); }
  .collection-empty { padding: 8px 12px; font-size: 12px; color: var(--text-sub); text-align: center; }

  .container { max-width: 640px; margin: 0 auto; padding: 16px; position: relative; }

  .glass-card {
    background-color: var(--card-bg-color); background-image: var(--card-gradient);
    backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 24px; padding: 16px; 
    box-shadow: 0 15px 35px rgba(0,0,0,0.3); margin-bottom: 16px; position: relative;
    word-break: break-all; overflow-wrap: break-word;
  }
  .main-card {
    background-color: var(--card-bg-color); background-image: var(--card-gradient);
    backdrop-filter: var(--glass-blur); -webkit-backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); border-radius: 24px; 
    padding: 0 0 24px 0; overflow: hidden;
    box-shadow: 0 15px 35px rgba(0,0,0,0.3); margin-bottom: 16px; position: relative;
  }
  .cover-wrap { width: 100%; height: 240px; background: #1a1a1a; position: relative; overflow: hidden; }
  .cover-wrap img { width: 100%; height: 100%; object-fit: cover; display: block; filter: grayscale(10%); }
  .cover-wrap::after {
    content: ""; position: absolute; bottom: 0; left: 0; right: 0; height: 160px;
    background: linear-gradient(to bottom, transparent 0%, var(--card-bg-color) 100%); 
    z-index: 1; pointer-events: none;
    -webkit-mask-image: linear-gradient(to bottom, black 20%, transparent 100%);
    mask-image: linear-gradient(to bottom, black 20%, transparent 100%);
  }
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
  .stats { display: flex; justify-content: flex-start; gap: 20px; font-size: 14px; }
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
    background-color: var(--card-bg-color); background-image: var(--card-gradient);
    backdrop-filter: var(--glass-blur); 
    border: 1px solid var(--glass-border); border-radius: 20px; padding: 16px; margin-bottom: 16px; position: relative; 
    word-break: break-all; overflow-wrap: break-word;
  }
  .feed-header { display: flex; align-items: center; gap: 10px; margin-bottom: 12px; }
  .feed-avatar { width: 40px; height: 40px; border-radius: 50%; overflow: hidden; background: #333; }
  .feed-avatar img { width: 100%; height: 100%; object-fit: cover; }
  .feed-name { font-size: 14px; font-weight: 600; }
  .feed-handle { font-size: 12px; color: var(--text-sub); font-family: monospace; }
  .feed-time { font-size: 12px; color: var(--text-sub); margin-left: auto; }
  .feed-text { font-size: 15px; line-height: 1.6; margin-bottom: 12px; position: relative; padding-right: 20px; }
  .feed-footer { display: flex; justify-content: flex-end; font-size: 13px; color: var(--text-sub); gap: 16px; }

  .card-row { display: flex; gap: 10px; margin-top: 12px; width: 100%; flex-wrap: wrap; }

  /* ====== 修复：始终居中（包括编辑模式） ====== */
  .card-col {
    flex: 1; min-width: 40px; padding: 8px 22px 8px 8px; 
    border: 1px dashed var(--glass-border);
    border-radius: 8px; background: rgba(255,255,255,0.02); 
    word-break: break-all; overflow-wrap: break-word; position: relative;
    display: flex; align-items: center; justify-content: center;
    text-align: center; 
    overflow: hidden;
    line-height: 1.5;
  }
  .card-col:hover, .feed-text:hover { border-color: var(--accent); background: rgba(255,255,255,0.05); }

  .text-block {
    padding: 12px 22px 12px 12px; 
    border: 1px dashed var(--glass-border);
    border-radius: 12px; background: rgba(255,255,255,0.02); 
    word-break: break-all; overflow-wrap: break-word; position: relative;
    display: flex; align-items: center; justify-content: center;
    text-align: center; 
    overflow: hidden;
    line-height: 1.5;
  }
  .text-block:hover { border-color: var(--accent); background: rgba(255,255,255,0.05); }

  /* ====== 新增：分隔线 ====== */
  .divider-block {
    padding: 10px 0;
    position: relative;
    display: flex;
    align-items: center;
    width: 100%;
  }
  .divider-block .divider-line {
    height: 1px;
    background: var(--glass-border);
    width: 100%;
    border-radius: 1px;
    transition: all 0.2s;
  }
  body.editing .divider-block .divider-line { background: var(--accent); opacity: 0.7; }
  body.editing .divider-block:hover .divider-line { opacity: 1; height: 2px; }

  .shape-block, .image-frame-block {
    position: absolute; z-index: 998;
    box-sizing: border-box; display: none; cursor: move;
    overflow: visible;
    pointer-events: auto;
    touch-action: none;
    user-select: none;
  }
  body.editing .shape-block, body.editing .image-frame-block { display: block; }
  .shape-block {
    color: var(--accent);
    filter: drop-shadow(0 6px 16px rgba(0,0,0,0.35));
  }
  .shape-block.selected { outline: 2px solid var(--accent); outline-offset: 4px; }
  .shape-block svg { width: 100%; height: 100%; display: block; pointer-events: none; }
  .image-frame-block {
    overflow: hidden; background: #333;
    border: 2px solid var(--glass-border);
    box-shadow: 0 10px 30px rgba(0,0,0,0.5);
  }
  .image-frame-block.selected { border-color: var(--accent); box-shadow: 0 0 0 3px rgba(74,158,255,0.3); }
  .image-frame-block img { width: 100%; height: 100%; object-fit: cover; display: block; pointer-events: none; }

  .element-del {
    position: absolute; top: -12px; left: -12px; width: 24px; height: 24px;
    background: #e5484d; color: #fff; border-radius: 50%; border: none;
    font-size: 14px; cursor: pointer; display: none; align-items: center; justify-content: center;
    z-index: 5; line-height: 1; padding: 0;
  }
  body.editing .shape-block:hover .element-del,
  body.editing .image-frame-block:hover .element-del { display: flex; }

  .block-wrapper { position: relative; }
  .block-wrapper > .resize-handle { display: none; }
  body.editing .block-wrapper > .resize-handle { display: block; }
  
  .resize-handle {
    position: absolute; bottom: 0; right: 0; width: 24px; height: 24px;
    background: linear-gradient(135deg, transparent 48%, var(--accent) 48%);
    cursor: se-resize; z-index: 10; 
    border-bottom-right-radius: 8px; 
    opacity: 0.9;
    touch-action: none;
    user-select: none;
    pointer-events: auto;
  }
  .resize-handle:hover { opacity: 1; }
  
  .shape-block > .resize-handle,
  .image-frame-block > .resize-handle,
  .float-text-block > .resize-handle { display: none; }
  body.editing .shape-block > .resize-handle,
  body.editing .image-frame-block > .resize-handle,
  body.editing .float-text-block > .resize-handle { display: block; }

  #float-layer { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 997; }
  .float-text-block {
    position: absolute; z-index: 998; min-width: 100px; min-height: 40px;
    padding: 14px; border-radius: 12px; border: 2px dashed var(--accent);
    background: rgba(20, 20, 25, 0.85); backdrop-filter: blur(12px);
    color: var(--text); font-size: 15px; line-height: 1.5; word-break: break-all;
    cursor: move; outline: none; box-sizing: border-box; display: none;
    box-shadow: 0 10px 30px rgba(0,0,0,0.5); pointer-events: auto; touch-action: none;
  }
  body.editing .float-text-block { display: block; }
  .float-text-block:focus { cursor: text; border: 2px solid var(--accent); }
  .float-text-block .drag-handle {
    position: absolute; top: 2px; right: 2px; width: 26px; height: 26px;
    background: var(--accent); color: var(--accent-text); border-radius: 6px;
    display: flex; align-items: center; justify-content: center;
    font-size: 14px; cursor: move; opacity: 0.7; z-index: 2; touch-action: none;
  }
  .float-del {
    position: absolute; top: -12px; left: -12px; width: 24px; height: 24px;
    background: #e5484d; color: #fff; border-radius: 50%; border: none;
    font-size: 14px; cursor: pointer; display: none; align-items: center; justify-content: center; z-index: 4;
  }
  body.editing .float-text-block:hover .float-del { display: flex; }

  .bottom-nav {
    position: fixed; bottom: 0; left: 0; right: 0; z-index: 998; height: 70px;
    display: flex; justify-content: space-around; align-items: center;
    background: rgba(20, 20, 25, 0.8); backdrop-filter: var(--glass-blur);
    border-top: 1px solid var(--glass-border);
  }
  html.light .bottom-nav { background: rgba(255, 255, 255, 0.8); }
  .bottom-nav-item { display: flex; flex-direction: column; align-items: center; gap: 4px; font-size: 10px; color: var(--text-sub); cursor: pointer; }
  .bottom-nav-item.active { color: var(--accent); }
  .bottom-nav-item .icon { font-size: 20px; }

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

  .panel-nav {
    display: flex; justify-content: space-between; align-items: center;
    margin-top: 16px; padding: 8px 0; gap: 16px;
  }
  .panel-nav-btn {
    display: flex; align-items: center; justify-content: center;
    min-width: 48px; height: 48px; padding: 0 16px; border-radius: 24px;
    background: var(--card-bg-color); backdrop-filter: var(--glass-blur);
    border: 1px solid var(--glass-border); color: var(--text);
    font-size: 20px; cursor: pointer; transition: all 0.2s; 
    font-family: inherit; user-select: none;
  }
  .panel-nav-btn:hover { background: var(--accent); color: var(--accent-text); border-color: var(--accent); transform: translateY(-2px); }
  .panel-nav-spacer { min-width: 48px; }

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

  body.editing [data-edit] { outline: 1px dashed transparent; outline-offset: 4px; border-radius: 4px; cursor: text; transition: outline 0.2s, background 0.2s; position: relative; }
  body.editing [data-edit]:hover { outline: 1px dashed var(--accent); background: rgba(255,255,255,0.03); }
  body.editing [data-edit]:focus { outline: 2px solid var(--accent); background: rgba(74, 158, 255, 0.1); }
  body.editing .cover-wrap:hover::after, 
  body.editing .avatar-wrap:hover::after, 
  body.editing .grid-item:hover::after, 
  body.editing .feed-avatar:hover::after {
    content: "点击换图"; position: absolute; inset: 0; display: flex; align-items: center; justify-content: center;
    background: rgba(0,0,0,0.6); color: #fff; font-size: 12px; z-index: 5; pointer-events: none;
  }
  .toast { position: fixed; left: 50%; bottom: 100px; transform: translateX(-50%) translateY(20px); background: var(--accent); color: var(--accent-text); padding: 10px 20px; border-radius: 20px; font-size: 13px; opacity: 0; pointer-events: none; transition: all 0.3s; z-index: 2000; }
  .toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }

  .block-actions {
    position: absolute; top: 0; right: 0; display: flex; gap: 4px; z-index: 20;
    background: rgba(0,0,0,0.5); padding: 4px; border-radius: 6px;
    backdrop-filter: blur(4px); opacity: 0.85;
    transition: opacity 0.2s;
  }
  body.editing .block-actions { display: flex !important; }
  body:not(.editing) .block-actions { display: none !important; }
  .block-action-btn {
    width: 28px; height: 28px; border-radius: 4px; background: var(--accent); color: var(--accent-text);
    border: none; font-size: 14px; cursor: pointer; display: flex; align-items: center; justify-content: center;
    box-shadow: 0 2px 5px rgba(0,0,0,0.3); transition: all 0.2s;
    padding: 0; line-height: 1;
  }
  .block-action-btn:hover { transform: scale(1.1); }
  .block-action-btn.del { background: #e5484d; }
  .block-action-btn.divider { background: #7d90b8; font-size: 18px; }

  .link-text {
    cursor: pointer;
    text-decoration: underline;
    text-decoration-style: dotted;
    text-underline-offset: 4px;
    transition: color 0.2s, transform 0.2s;
  }
  .link-text:hover { color: var(--accent); transform: translateX(2px); }
  .link-text::after { content: " ↗"; font-size: 0.8em; opacity: 0.7; }

  /* ====== 手机自适应 ====== */
  @media (max-width: 640px) {
    body {
      padding-top: 54px;
      padding-bottom: 80px;
    }
    .toolbar {
      height: 46px; padding: 0 10px; gap: 5px;
    }
    .tbtn {
      padding: 5px 10px; font-size: 11px; border-radius: 16px;
    }
    .tbtn .label { display: none; }
    .tbtn .ico { font-size: 15px; }
    .bg-image-btn .label { display: none; }
    .top-nav {
      top: 46px; padding: 8px 10px; gap: 4px;
    }
    .nav-link {
      padding: 6px 12px; font-size: 11px; border-radius: 16px;
    }
    .nav-collection { padding: 6px 10px !important; min-width: 36px; }
    .container { padding: 12px; }
    .glass-card, .feed-card { border-radius: 18px; padding: 14px; margin-bottom: 12px; }
    .main-card { border-radius: 18px; padding-bottom: 18px; margin-bottom: 12px; }
    .cover-wrap { height: 170px; }
    .avatar-wrap { width: 64px; height: 64px; border-width: 2px; }
    .name { font-size: 21px; }
    .handle { font-size: 12px; }
    .stats { font-size: 13px; gap: 14px; }
    .profile-info { padding: 12px 16px 0; }
    .profile-header-row { gap: 12px; margin-bottom: 10px; }
    .image-grid { margin: 0 16px 16px 16px; gap: 6px; }
    .quote-block { margin: 0 16px 16px 16px; padding: 12px; }
    .quote-block p { font-size: 13px; line-height: 1.7; }
    .post-box { margin: 0 16px 12px 16px; padding: 12px; border-radius: 16px; }
    .post-input { font-size: 14px; }
    .publish-btn { padding: 6px 16px; font-size: 12px; }
    .tool-icons { font-size: 16px; gap: 10px; }
    .feed-text { font-size: 14px; padding-right: 16px; }
    .feed-header { gap: 8px; }
    .feed-avatar { width: 34px; height: 34px; }
    .feed-name { font-size: 13px; }
    .feed-handle, .feed-time { font-size: 11px; }
    .card-col, .text-block { padding: 8px 20px 8px 8px; font-size: 13px; }
    .divider-block { padding: 8px 0; }
    .panel-nav { margin-top: 12px; padding: 6px 0; gap: 12px; }
    .panel-nav-btn {
      min-width: 42px; height: 42px; font-size: 18px; padding: 0 12px;
      border-radius: 21px;
    }
    .panel-nav-spacer { min-width: 42px; }
    .bottom-nav { height: 58px; }
    .bottom-nav-item { font-size: 9px; }
    .bottom-nav-item .icon { font-size: 17px; }
    .music-player {
      width: 44px; height: 44px; font-size: 18px;
      bottom: 68px; right: 12px;
    }
    .style-panel {
      width: calc(100vw - 24px); max-width: 340px; right: 12px;
      padding: 12px; font-size: 11px; gap: 8px;
    }
    .style-panel h4 { font-size: 12px; }
    .style-btn { font-size: 10px; padding: 3px 6px; }
    .shape-picker { padding: 10px; gap: 6px; max-width: calc(100vw - 24px); }
    .shape-pick-item { width: 40px; height: 40px; }
    .shape-pick-item svg { width: 24px; height: 24px; }
    .collection-menu { top: 96px; left: 12px; min-width: 150px; }
    .block-actions { padding: 3px; gap: 3px; }
    .block-action-btn { width: 26px; height: 26px; font-size: 13px; }
    .block-action-btn.divider { font-size: 16px; }
    .resize-handle { width: 22px; height: 22px; }
    .add-card-btn { padding: 12px; font-size: 12px; }
    .card-del { width: 26px; height: 26px; font-size: 14px; }
  }

  @media (max-width: 380px) {
    .nav-link { padding: 5px 9px; font-size: 10px; letter-spacing: 0.05em; }
    .container { padding: 10px; }
    .glass-card, .feed-card { padding: 12px; }
    .name { font-size: 19px; }
  }
</style>
</head>
<body>

<audio id="bgMusic" src="" loop preload="none"></audio>
<div class="music-player" id="musicPlayer" title="播放/暂停音乐"><span id="musicIcon">♪</span></div>

<div class="toolbar">
  <button class="tbtn" id="btnEdit"><span class="ico">✎</span><span class="label">编辑</span></button>
  <button class="tbtn edit-only" id="btnStylePanel"><span class="ico">⚙️</span><span class="label">样式</span></button>
  <button class="tbtn edit-only" id="btnAddFloat"><span class="ico">＋</span><span class="label">文本</span></button>
  <button class="tbtn edit-only" id="btnAddShape"><span class="ico">◆</span><span class="label">图形</span></button>
  <button class="tbtn edit-only" id="btnAddImageFrame"><span class="ico">🖼️</span><span class="label">图片框</span></button>
  <button class="tbtn edit-only" id="btnAddPanel"><span class="ico">📄</span><span class="label">新页面</span></button>
  <button class="tbtn edit-only" id="btnExport"><span class="ico">⬇</span><span class="label">导出</span></button>
  <button class="tbtn edit-only" id="btnReset"><span class="ico">⟲</span><span class="label">重置</span></button>
  <span class="spacer"></span>
  <button class="tbtn edit-only bg-image-btn" id="btnBgImage">🖼️ <span class="label">背景</span></button>
  <button class="tbtn" id="btnTheme"><span class="ico" id="themeIcon">☀</span></button>
</div>

<div class="shape-picker" id="shapePicker">
  <div class="shape-picker-title">选择图形</div>
  <div class="shape-pick-item" data-shape="circle"><svg viewBox="0 0 100 100"><circle cx="50" cy="50" r="45" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="square"><svg viewBox="0 0 100 100"><rect x="5" y="5" width="90" height="90" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="rounded"><svg viewBox="0 0 100 100"><rect x="5" y="5" width="90" height="90" rx="18" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="triangle"><svg viewBox="0 0 100 100"><polygon points="50,5 95,95 5,95" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="star"><svg viewBox="0 0 100 100"><polygon points="50,5 61,38 96,38 68,59 79,92 50,72 21,92 32,59 4,38 39,38" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="heart"><svg viewBox="0 0 100 100"><path d="M50,88 C20,65 5,45 5,30 C5,15 18,5 32,5 C41,5 48,10 50,18 C52,10 59,5 68,5 C82,5 95,15 95,30 C95,45 80,65 50,88 Z" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="arrow"><svg viewBox="0 0 100 100"><path d="M5,40 L60,40 L60,15 L95,50 L60,85 L60,60 L5,60 Z" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="diamond"><svg viewBox="0 0 100 100"><polygon points="50,5 95,50 50,95 5,50" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="hexagon"><svg viewBox="0 0 100 100"><polygon points="50,5 93,27 93,73 50,95 7,73 7,27" fill="currentColor"/></svg></div>
  <div class="shape-pick-item" data-shape="cross"><svg viewBox="0 0 100 100"><path d="M35,5 L65,5 L65,35 L95,35 L95,65 L65,65 L65,95 L35,95 L35,65 L5,65 L5,35 L35,35 Z" fill="currentColor"/></svg></div>
</div>

<div class="shape-picker" id="framePicker" style="grid-template-columns: repeat(3, 1fr);">
  <div class="shape-picker-title">选择图片框形状</div>
  <div class="shape-pick-item" data-frame="rounded"><svg viewBox="0 0 100 100"><rect x="8" y="20" width="84" height="60" rx="10" fill="none" stroke="currentColor" stroke-width="6"/></svg></div>
  <div class="shape-pick-item" data-frame="square"><svg viewBox="0 0 100 100"><rect x="15" y="15" width="70" height="70" fill="none" stroke="currentColor" stroke-width="6"/></svg></div>
  <div class="shape-pick-item" data-frame="circle"><svg viewBox="0 0 100 100"><circle cx="50" cy="50" r="35" fill="none" stroke="currentColor" stroke-width="6"/></svg></div>
</div>

<div class="style-panel" id="stylePanel">
  <h4>卡片样式调节</h4>
  <div class="style-item"><span>透明度</span><input type="range" id="opacitySlider" min="0" max="100" value="65"><span id="opacityVal">65%</span></div>
  <div class="style-item"><span>渐变起始</span><input type="color" id="gradColor1" value="#1c1c22"></div>
  <div class="style-item"><span>渐变结束</span><input type="color" id="gradColor2" value="#0d0d10"></div>
  <div class="style-item"><span>渐变角度</span><input type="range" id="gradAngle" min="0" max="360" value="135"><span id="angleVal">135°</span></div>
  <div class="style-item"><span>全局主色</span><input type="color" id="accentColor" value="#4a9eff"></div>

  <div id="shapeStyleSection" style="display:none;">
    <h4>图形/图片框样式</h4>
    <div class="style-item" id="shapeColorRow"><span>图形颜色</span><input type="color" id="shapeColor" value="#4a9eff"></div>
    <div class="style-item"><span>宽度</span><input type="range" id="shapeWidth" min="20" max="400" value="90"><span id="shapeWidthVal">90px</span></div>
    <div class="style-item"><span>高度</span><input type="range" id="shapeHeight" min="20" max="400" value="90"><span id="shapeHeightVal">90px</span></div>
    <div class="style-item"><span>圆角</span><input type="range" id="shapeRadius" min="0" max="200" value="0"><span id="shapeRadiusVal">0px</span></div>
    <div class="style-item" id="shapeImgRow" style="display:none;"><span>图片</span><button class="style-btn" id="btnChangeShapeImg">更换图片</button></div>
  </div>

  <h4>文字样式（点击文字后激活）</h4>
  <div class="style-item"><span>字体大小</span><input type="range" id="fontSize" min="12" max="48" value="16"><span id="fontSizeVal">16px</span></div>
  <div class="style-item"><span>字体</span><select id="fontFamilySelect" class="font-select"><option value="default">系统默认</option></select></div>
  <div class="style-item"><span>导入字体</span><input type="file" id="fontInput" accept=".ttf,.woff,.woff2" style="width:100px; font-size:10px;"></div>
  <div class="style-item"><span>出场动画</span><select id="textAnimation" class="font-select" style="width:100px;"><option value="none">无</option><option value="fadeIn">淡入</option><option value="slideUp">上滑</option><option value="zoomIn">缩放</option><option value="slideLeft">左滑</option></select></div>
  <div class="style-item"><span>排列</span><select id="textDir" style="background:#333; color:#fff; border:1px solid #555; padding:2px 6px; border-radius:4px; font-size:11px;"><option value="horizontal-tb">横排</option><option value="vertical-rl">竖排</option></select></div>
  <div class="style-item"><span>对齐</span><div class="style-btn-group"><button class="style-btn" data-align="left">左</button><button class="style-btn" data-align="center">中</button><button class="style-btn" data-align="right">右</button></div></div>
  <div class="style-item"><span>样式</span><div class="style-btn-group"><button class="style-btn" id="btnBold">B</button><button class="style-btn" id="btnItalic">I</button><button class="style-btn" id="btnUnderline">U</button></div></div>
  <div class="style-item"><span>文字颜色</span><input type="color" id="textColor" value="#f0f0f0"></div>
  <div class="style-item"><span>文字背景</span><input type="color" id="textBg" value="#000000"><span id="bgOpacityVal">0%</span><input type="range" id="bgOpacity" min="0" max="100" value="0" style="width:60px;"></div>
  <div class="style-item"><span>边框颜色</span><input type="color" id="textBorderColor" value="#4a9eff"></div>
  <div class="style-item"><span>边框粗细</span><input type="range" id="borderWidth" min="1" max="5" value="1"><span id="borderWidthVal">1px</span></div>
  <div class="style-item"><span>边框圆角</span><input type="range" id="borderRadius" min="0" max="20" value="0"><span id="borderRadiusVal">0px</span></div>
  <div class="style-item"><span>边框</span><button class="style-btn" id="btnToggleBorder">添加边框</button></div>
  <div class="style-item"><span>链接目标</span><select id="linkTargetSelect" class="font-select" style="width:130px;"><option value="">无</option></select></div>
</div>

<div class="top-nav" id="topNav">
  <a class="nav-link nav-collection" id="navCollection" title="合集">
    <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.2" stroke-linecap="round">
      <line x1="3" y1="6" x2="21" y2="6"/>
      <line x1="3" y1="12" x2="21" y2="12"/>
      <line x1="3" y1="18" x2="21" y2="18"/>
    </svg>
  </a>
  <a class="nav-link active" data-target="panel-0">WELCOME</a>
  <a class="nav-link" data-target="panel-1">PERSONAL INFO</a>
  <a class="nav-link" data-target="panel-2">MY STYLE</a>
  <a class="nav-link" data-target="panel-3">CATEGORIES</a>
</div>

<div class="collection-menu" id="collectionMenu">
  <div class="collection-menu-title">合集</div>
  <div id="collectionList"></div>
</div>

<div class="container" id="app">
  <div id="float-layer"></div>

  <div id="panel-0" class="panel active">
    <div class="main-card">
      <div class="cover-wrap">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Crect width='160' height='90' fill='%23111'/%3E%3Ctext x='80' y='49' font-size='8' fill='%23444' text-anchor='middle' font-family='monospace'%3E点击更换封面图%3C/text%3E%3C/svg%3E" alt="封面">
      </div>
      <div class="profile-info">
        <div class="profile-header-row">
          <div class="avatar-wrap"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23222'/%3E%3Ctext x='50' y='69' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像"></div>
          <div class="name-handle">
            <div class="name" data-edit>水水</div>
            <div class="handle" data-edit>@InfernalBeyond.com</div>
          </div>
        </div>
        <div class="block-wrapper" style="margin-bottom: 12px;">
          <div class="stats">
            <div><span data-edit>520</span> 正在关注</div>
            <div><span data-edit>1,314</span> 关注者</div>
          </div>
          <div class="block-actions">
            <button class="block-action-btn" onclick="addTextBlockBelow(this)" title="在下方添加内容">＋</button>
            <button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>
          </div>
        </div>
      </div>
      <div class="image-grid">
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🏞️%3C/text%3E%3C/svg%3E"></div>
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E📸%3C/text%3E%3C/svg%3E"></div>
        <div class="grid-item"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='30' fill='%23999' text-anchor='middle'%3E🌌%3C/text%3E%3C/svg%3E"></div>
      </div>
      <div class="quote-block"><p data-edit>——They say time devours all things but say nothing about what becomes of the things as time digest them.What if life is consummated only by the way that time consumes it?What if to live is to be devoured by time and recycled?</p></div>
      <div class="post-box" id="postBoxTrigger">
        <input class="post-input" placeholder="说点什么..." data-edit>
        <div class="post-tools">
          <div class="tool-icons"><span>🖼️</span><span>👥</span><span>📍</span></div>
          <button class="publish-btn"><span data-edit>发布</span> <span>→</span></button>
        </div>
      </div>
    </div>
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="feed-header">
        <div class="feed-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23555'/%3E%3Ctext x='50' y='69' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EW%3C/text%3E%3C/svg%3E"></div>
        <div><div class="feed-name" data-edit>水水</div><div class="feed-handle" data-edit>@InfernalBeyond.com</div></div>
        <div class="feed-time" data-edit>7小时前</div>
      </div>
      <div class="block-wrapper">
        <div class="feed-text" data-edit>这里是暗网吗</div>
        <div class="block-actions">
          <button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>
          <button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>
          <button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>
          <button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>
        </div>
      </div>
      <div class="feed-footer"><span>💬 评论</span></div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-0')" style="margin-bottom:8px;">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-0')">＋ 添加自由文本块</button>
    <div class="panel-nav">
      <div class="panel-nav-spacer"></div>
      <button class="panel-nav-btn" onclick="window.switchTab('panel-1')" title="下一页">→</button>
    </div>
  </div>

  <div id="panel-1" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="block-wrapper">
        <div class="feed-text" data-edit>这里是个人信息区域，你可以在编辑模式下自由修改文字和图片。</div>
        <div class="block-actions">
          <button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>
          <button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>
          <button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>
          <button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>
        </div>
      </div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-1')">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-1')">＋ 添加自由文本块</button>
    <div class="panel-nav">
      <button class="panel-nav-btn" onclick="window.goBack()" title="上一页">←</button>
      <button class="panel-nav-btn" onclick="window.switchTab('panel-2')" title="下一页">→</button>
    </div>
  </div>

  <div id="panel-2" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="block-wrapper">
        <div class="feed-text" data-edit>这里是个人风格展示区域，可以放你的作品或爱好。</div>
        <div class="block-actions">
          <button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>
          <button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>
          <button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>
          <button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>
        </div>
      </div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-2')">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-2')">＋ 添加自由文本块</button>
    <div class="panel-nav">
      <button class="panel-nav-btn" onclick="window.goBack()" title="上一页">←</button>
      <button class="panel-nav-btn" onclick="window.switchTab('panel-3')" title="下一页">→</button>
    </div>
  </div>

  <div id="panel-3" class="panel" style="display:none;">
    <div class="feed-card glass-card">
      <button class="card-del" contenteditable="false" title="删除这张卡片">×</button>
      <div class="block-wrapper">
        <div class="feed-text" data-edit>这里是分类区域，可以放你的收藏或分类信息。</div>
        <div class="block-actions">
          <button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>
          <button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>
          <button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>
          <button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>
        </div>
      </div>
      <div class="block-wrapper">
        <div class="feed-text link-text" data-edit data-link="" style="margin-top:12px;">→ 点击跳转到我的新页面（示例）</div>
        <div class="block-actions">
          <button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>
          <button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>
          <button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>
          <button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>
        </div>
      </div>
    </div>
    <button class="add-card-btn" onclick="addCard('panel-3')">＋ 添加动态卡片</button>
    <button class="add-card-btn" onclick="addTextBlock('panel-3')">＋ 添加自由文本块</button>
    <div class="panel-nav">
      <button class="panel-nav-btn" onclick="window.goBack()" title="上一页">←</button>
      <div class="panel-nav-spacer"></div>
    </div>
  </div>

</div>

<div class="bottom-nav">
  <div class="bottom-nav-item active"><span class="icon">⊞</span><span>Desk</span></div>
  <div class="bottom-nav-item"><span class="icon">👤</span><span>Space</span></div>
  <div class="bottom-nav-item"><span class="icon">👥</span><span>Friends</span></div>
  <div class="bottom-nav-item"><span class="icon">⚙️</span><span>设置</span></div>
</div>

<div class="toast" id="toast"></div>
<input type="file" id="fileInput" accept="image/*" hidden>
<input type="file" id="bgInput" accept="image/*" hidden>
<input type="file" id="elementImageInput" accept="image/*" hidden>

<script>
(function(){
  'use strict';
  var KEY_CONTENT = 'myPage.glass.v21';
  var KEY_THEME   = 'myPage.theme.v1';
  var KEY_COLOR   = 'myPage.color.v1';
  var KEY_BG_IMG  = 'myPage.bgImage.v1';
  var KEY_STYLE   = 'myPage.styleSettings.v1';
  
  var app = document.getElementById('app');
  var fileInput = document.getElementById('fileInput');
  var bgInput = document.getElementById('bgInput');
  var elementImageInput = document.getElementById('elementImageInput');
  var toastEl = document.getElementById('toast');
  var currentImg = null;
  var currentElementImg = null;
  var editMode = false;
  var activeTextEl = null;
  var activeShapeEl = null;
  var DEFAULT_HTML = app.innerHTML;
  var customFonts = [];

  var styleSettings = {
    opacity: 65, gradColor1: '#1c1c22', gradColor2: '#0d0d10',
    angle: 135, accent: '#4a9eff'
  };

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
    if(savedBgImg !== 'none'){ document.body.style.backgroundImage = 'url(' + savedBgImg + ')'; }
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

  function applySliders(settings){
    var opacity = settings.opacity / 100;
    function hexToRgba(hex, alpha){
      var r = parseInt(hex.slice(1,3),16), g = parseInt(hex.slice(3,5),16), b = parseInt(hex.slice(5,7),16);
      return 'rgba(' + r + ',' + g + ',' + b + ',' + alpha + ')';
    }
    var bgColor = hexToRgba(settings.gradColor1, opacity);
    var gradColor1 = hexToRgba(settings.gradColor1, opacity);
    var gradColor2 = hexToRgba(settings.gradColor2, opacity);
    document.documentElement.style.setProperty('--card-bg-color', bgColor);
    document.documentElement.style.setProperty('--card-gradient', 'linear-gradient(' + settings.angle + 'deg, ' + gradColor1 + ' 0%, ' + gradColor2 + ' 100%)');
    document.getElementById('opacitySlider').value = settings.opacity;
    document.getElementById('opacityVal').textContent = settings.opacity + '%';
    document.getElementById('gradColor1').value = settings.gradColor1;
    document.getElementById('gradColor2').value = settings.gradColor2;
    document.getElementById('gradAngle').value = settings.angle;
    document.getElementById('angleVal').textContent = settings.angle + '°';
    document.getElementById('accentColor').value = settings.accent;
    localStorage.setItem(KEY_STYLE, JSON.stringify(settings));
  }

  document.getElementById('btnStylePanel').addEventListener('click', function(e){
    e.stopPropagation();
    document.getElementById('stylePanel').classList.toggle('show');
  });
  document.getElementById('opacitySlider').addEventListener('input', function(e){ styleSettings.opacity = e.target.value; applySliders(styleSettings); });
  document.getElementById('gradColor1').addEventListener('input', function(e){ styleSettings.gradColor1 = e.target.value; applySliders(styleSettings); });
  document.getElementById('gradColor2').addEventListener('input', function(e){ styleSettings.gradColor2 = e.target.value; applySliders(styleSettings); });
  document.getElementById('gradAngle').addEventListener('input', function(e){ styleSettings.angle = e.target.value; applySliders(styleSettings); });
  document.getElementById('accentColor').addEventListener('input', function(e){ styleSettings.accent = e.target.value; applyColor(styleSettings.accent); applySliders(styleSettings); });

  function updateShapeStyleUI(){
    var section = document.getElementById('shapeStyleSection');
    if(!activeShapeEl){ section.style.display = 'none'; return; }
    section.style.display = 'block';
    var isShape = activeShapeEl.classList.contains('shape-block');
    var isFrame = activeShapeEl.classList.contains('image-frame-block');
    document.getElementById('shapeColorRow').style.display = isShape ? 'flex' : 'none';
    document.getElementById('shapeImgRow').style.display = isFrame ? 'flex' : 'none';
    var w = activeShapeEl.offsetWidth, h = activeShapeEl.offsetHeight;
    document.getElementById('shapeWidth').value = w;
    document.getElementById('shapeWidthVal').textContent = w + 'px';
    document.getElementById('shapeHeight').value = h;
    document.getElementById('shapeHeightVal').textContent = h + 'px';
    var radius = parseInt(activeShapeEl.style.borderRadius) || 0;
    document.getElementById('shapeRadius').value = radius;
    document.getElementById('shapeRadiusVal').textContent = radius + 'px';
    if(isShape){
      document.getElementById('shapeColor').value = rgbToHex(window.getComputedStyle(activeShapeEl).color);
    }
  }
  function rgbToHex(rgb){
    if(!rgb) return '#4a9eff';
    var m = rgb.match(/\d+/g);
    if(!m) return '#4a9eff';
    return '#' + m.slice(0,3).map(function(x){ return ('0'+(+x).toString(16)).slice(-2); }).join('');
  }
  document.getElementById('shapeColor').addEventListener('input', function(e){
    if(activeShapeEl && activeShapeEl.classList.contains('shape-block')){
      activeShapeEl.style.color = e.target.value;
      scheduleSave();
    }
  });
  document.getElementById('shapeWidth').addEventListener('input', function(e){
    if(activeShapeEl){
      activeShapeEl.style.width = e.target.value + 'px';
      document.getElementById('shapeWidthVal').textContent = e.target.value + 'px';
      scheduleSave();
    }
  });
  document.getElementById('shapeHeight').addEventListener('input', function(e){
    if(activeShapeEl){
      activeShapeEl.style.height = e.target.value + 'px';
      document.getElementById('shapeHeightVal').textContent = e.target.value + 'px';
      scheduleSave();
    }
  });
  document.getElementById('shapeRadius').addEventListener('input', function(e){
    if(activeShapeEl){
      activeShapeEl.style.borderRadius = e.target.value + 'px';
      document.getElementById('shapeRadiusVal').textContent = e.target.value + 'px';
      scheduleSave();
    }
  });
  document.getElementById('btnChangeShapeImg').addEventListener('click', function(){
    if(activeShapeEl && activeShapeEl.classList.contains('image-frame-block')){
      currentElementImg = activeShapeEl.querySelector('img');
      elementImageInput.value = '';
      elementImageInput.click();
    }
  });

  function selectShape(el){
    if(activeShapeEl) activeShapeEl.classList.remove('selected');
    activeShapeEl = el;
    if(el) el.classList.add('selected');
    updateShapeStyleUI();
    if(el) document.getElementById('stylePanel').classList.add('show');
  }

  document.getElementById('fontInput').addEventListener('change', function(e){
    var file = e.target.files[0]; if(!file) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      var fontName = 'CustomFont_' + Date.now();
      var font = new FontFace(fontName, ev.target.result);
      font.load().then(function(loadedFont){
        document.fonts.add(loadedFont);
        customFonts.push(fontName);
        var select = document.getElementById('fontFamilySelect');
        var option = document.createElement('option');
        option.value = fontName;
        option.textContent = '自定义字体 ' + customFonts.length;
        select.appendChild(option);
        toast('字体导入成功');
        if(activeTextEl){ activeTextEl.style.fontFamily = fontName; select.value = fontName; }
      }).catch(function(){ toast('字体导入失败'); });
    };
    reader.readAsArrayBuffer(file);
  });

  function updateTextStyleUI(){
    if(!activeTextEl) return;
    var style = window.getComputedStyle(activeTextEl);
    document.getElementById('fontSize').value = parseInt(style.fontSize) || 16;
    document.getElementById('fontSizeVal').textContent = (parseInt(style.fontSize) || 16) + 'px';
    document.getElementById('textColor').value = rgbToHex(style.color);
    document.getElementById('textDir').value = style.writingMode && style.writingMode.indexOf('vertical') === 0 ? 'vertical-rl' : 'horizontal-tb';
    document.getElementById('borderWidth').value = parseInt(style.borderWidth) || 1;
    document.getElementById('borderWidthVal').textContent = (parseInt(style.borderWidth) || 1) + 'px';
    document.getElementById('borderRadius').value = parseInt(style.borderRadius) || 0;
    document.getElementById('borderRadiusVal').textContent = (parseInt(style.borderRadius) || 0) + 'px';
    var select = document.getElementById('fontFamilySelect');
    var currentFont = style.fontFamily.split(',')[0].replace(/['"]/g, '');
    for(var i=0; i<select.options.length; i++){
      if(select.options[i].value === currentFont || (currentFont === 'default' && select.options[i].value === 'default')) { select.value = select.options[i].value; break; }
    }
    var bgColor = style.backgroundColor;
    if(bgColor && bgColor !== 'rgba(0, 0, 0, 0)'){
      document.getElementById('textBg').value = rgbToHex(bgColor);
      document.getElementById('bgOpacity').value = 100;
      document.getElementById('bgOpacityVal').textContent = '100%';
    } else {
      document.getElementById('bgOpacity').value = 0;
      document.getElementById('bgOpacityVal').textContent = '0%';
    }
    var linkSelect = document.getElementById('linkTargetSelect');
    if(linkSelect){
      linkSelect.value = activeTextEl.getAttribute('data-link') || '';
    }
  }

  document.getElementById('fontSize').addEventListener('input', function(e){ if(activeTextEl){ activeTextEl.style.fontSize = e.target.value + 'px'; document.getElementById('fontSizeVal').textContent = e.target.value + 'px'; } });
  document.getElementById('fontFamilySelect').addEventListener('change', function(e){ if(activeTextEl){ activeTextEl.style.fontFamily = e.target.value === 'default' ? 'inherit' : e.target.value; } });
  document.getElementById('textAnimation').addEventListener('change', function(e){
    if(activeTextEl){
      var anim = e.target.value;
      activeTextEl.classList.remove('anim-fadeIn','anim-slideUp','anim-zoomIn','anim-slideLeft');
      void activeTextEl.offsetWidth;
      if(anim !== 'none') activeTextEl.classList.add('anim-' + anim);
    }
  });
  document.getElementById('textDir').addEventListener('change', function(e){
    if(!activeTextEl) return;
    if(e.target.value === 'vertical-rl'){
      activeTextEl.style.writingMode = 'vertical-rl';
      activeTextEl.style.textOrientation = 'upright';
    } else {
      activeTextEl.style.writingMode = 'horizontal-tb';
      activeTextEl.style.textOrientation = '';
    }
  });
  document.querySelectorAll('.style-btn[data-align]').forEach(function(btn){
    btn.addEventListener('click', function(){ if(activeTextEl) activeTextEl.style.textAlign = this.getAttribute('data-align'); });
  });
  document.getElementById('btnBold').addEventListener('click', function(){ if(activeTextEl){ var w = activeTextEl.style.fontWeight === 'bold' ? 'normal' : 'bold'; activeTextEl.style.fontWeight = w; this.classList.toggle('active', w === 'bold'); } });
  document.getElementById('btnItalic').addEventListener('click', function(){ if(activeTextEl){ var s = activeTextEl.style.fontStyle === 'italic' ? 'normal' : 'italic'; activeTextEl.style.fontStyle = s; this.classList.toggle('active', s === 'italic'); } });
  document.getElementById('btnUnderline').addEventListener('click', function(){ if(activeTextEl){ var d = activeTextEl.style.textDecoration === 'underline' ? 'none' : 'underline'; activeTextEl.style.textDecoration = d; this.classList.toggle('active', d === 'underline'); } });
  document.getElementById('textColor').addEventListener('input', function(e){ if(activeTextEl) activeTextEl.style.color = e.target.value; });
  document.getElementById('textBg').addEventListener('input', function(e){
    if(activeTextEl){
      activeTextEl.dataset.bgColor = e.target.value;
      var alpha = document.getElementById('bgOpacity').value / 100;
      var hex = e.target.value.replace('#', '');
      var r = parseInt(hex.substr(0,2),16), g = parseInt(hex.substr(2,2),16), b = parseInt(hex.substr(4,2),16);
      activeTextEl.style.backgroundColor = 'rgba(' + r + ',' + g + ',' + b + ',' + alpha + ')';
    }
  });
  document.getElementById('bgOpacity').addEventListener('input', function(e){
    if(activeTextEl){
      var color = activeTextEl.dataset.bgColor || document.getElementById('textBg').value;
      var hex = color.replace('#', '');
      var r = parseInt(hex.substr(0,2),16), g = parseInt(hex.substr(2,2),16), b = parseInt(hex.substr(4,2),16);
      activeTextEl.style.backgroundColor = 'rgba(' + r + ',' + g + ',' + b + ',' + (e.target.value / 100) + ')';
      document.getElementById('bgOpacityVal').textContent = e.target.value + '%';
    }
  });
  document.getElementById('borderWidth').addEventListener('input', function(e){ if(activeTextEl){ activeTextEl.style.borderWidth = e.target.value + 'px'; activeTextEl.style.borderStyle = 'solid'; activeTextEl.style.borderColor = document.getElementById('textBorderColor').value; document.getElementById('borderWidthVal').textContent = e.target.value + 'px'; } });
  document.getElementById('borderRadius').addEventListener('input', function(e){ if(activeTextEl){ activeTextEl.style.borderRadius = e.target.value + 'px'; document.getElementById('borderRadiusVal').textContent = e.target.value + 'px'; } });
  document.getElementById('textBorderColor').addEventListener('input', function(e){ if(activeTextEl && activeTextEl.style.borderStyle === 'solid') activeTextEl.style.borderColor = e.target.value; });
  document.getElementById('btnToggleBorder').addEventListener('click', function(){
    if(activeTextEl){
      if(activeTextEl.style.borderStyle === 'solid'){ activeTextEl.style.borderStyle = 'none'; this.textContent = '添加边框'; }
      else { activeTextEl.style.borderStyle = 'solid'; activeTextEl.style.borderWidth = document.getElementById('borderWidth').value + 'px'; activeTextEl.style.borderColor = document.getElementById('textBorderColor').value; this.textContent = '去除边框'; }
    }
  });

  document.getElementById('linkTargetSelect').addEventListener('change', function(e){
    if(!activeTextEl) return;
    var target = e.target.value;
    if(target){
      activeTextEl.setAttribute('data-link', target);
      activeTextEl.classList.add('link-text');
    } else {
      activeTextEl.removeAttribute('data-link');
      activeTextEl.classList.remove('link-text');
    }
    scheduleSave();
    toast(target ? '已链接到 ' + target : '已取消链接');
  });

  document.addEventListener('focusin', function(e){
    if(!editMode) return;
    var el = e.target.closest('[contenteditable="true"], .float-text-block');
    if(el && !el.classList.contains('shape-block') && !el.classList.contains('image-frame-block')){
      activeTextEl = el;
      updateTextStyleUI();
      document.getElementById('stylePanel').classList.add('show');
      el.style.transition = 'box-shadow 0.3s';
      el.style.boxShadow = '0 0 0 4px var(--accent)';
      setTimeout(function(){ el.style.boxShadow = ''; }, 600);
    }
  });

  // ========= 增删行列 =========
  window.addRowHere = function(btn){
    var wrapper = btn.closest('.block-wrapper');
    var nw = document.createElement('div'); nw.className = 'block-wrapper'; nw.style.marginTop = '12px';
    nw.innerHTML = '<div class="card-col" contenteditable="true" style="width:100%;">新行内容</div>' +
      '<div class="resize-handle"></div>' +
      '<div class="block-actions">' +
        '<button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>' +
        '<button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>' +
        '<button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>' +
        '<button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>' +
      '</div>';
    wrapper.parentNode.insertBefore(nw, wrapper.nextSibling);
    if(editMode) nw.querySelector('.card-col').focus();
    bindResizeEvents(nw); scheduleSave(); toast('已添加行');
  };
  window.addColHere = function(btn){
    var wrapper = btn.closest('.block-wrapper');
    var targetBlock = wrapper.querySelector('.feed-text, .card-col, .text-block');
    var row = targetBlock.closest('.card-row');
    if(!row){ row = document.createElement('div'); row.className = 'card-row'; wrapper.parentNode.insertBefore(row, wrapper); row.appendChild(wrapper); }
    var nw = document.createElement('div'); nw.className = 'block-wrapper'; nw.style.flex = '1';
    nw.innerHTML = '<div class="card-col" contenteditable="true">新列内容</div>' +
      '<div class="resize-handle"></div>' +
      '<div class="block-actions">' +
        '<button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>' +
        '<button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>' +
        '<button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>' +
        '<button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>' +
      '</div>';
    wrapper.parentNode.insertBefore(nw, wrapper.nextSibling);
    if(editMode) nw.querySelector('.card-col').focus();
    bindResizeEvents(nw); scheduleSave(); toast('已添加列');
  };
  window.addTextBlockBelow = function(btn){
    var wrapper = btn.closest('.block-wrapper');
    var nw = document.createElement('div'); nw.className = 'block-wrapper'; nw.style.marginTop = '12px';
    nw.innerHTML = '<div class="text-block" contenteditable="true">点击输入新内容...</div>' +
      '<div class="resize-handle"></div>' +
      '<div class="block-actions">' +
        '<button class="block-action-btn" onclick="addTextBlockBelow(this)" title="在下方添加内容">＋</button>' +
        '<button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>' +
        '<button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>' +
        '<button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>' +
      '</div>';
    wrapper.parentNode.insertBefore(nw, wrapper.nextSibling);
    if(editMode) nw.querySelector('.text-block').focus();
    bindResizeEvents(nw); scheduleSave(); toast('已添加内容');
  };

  // ========= 新增：分隔线 =========
  window.addDivider = function(btn){
    var wrapper = btn.closest('.block-wrapper');
    var nw = document.createElement('div');
    nw.className = 'block-wrapper';
    nw.style.marginTop = '8px';
    nw.style.marginBottom = '8px';
    nw.innerHTML =
      '<div class="divider-block">' +
        '<div class="divider-line"></div>' +
      '</div>' +
      '<div class="block-actions">' +
        '<button class="block-action-btn divider" onclick="addDivider(this)" title="再添加一条分隔线">—</button>' +
        '<button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>' +
      '</div>';
    wrapper.parentNode.insertBefore(nw, wrapper.nextSibling);
    scheduleSave();
    toast('已添加分隔线');
  };

  window.deleteBlock = function(btn){
    var wrapper = btn.closest('.block-wrapper'); var row = wrapper.closest('.card-row');
    if(row){ wrapper.remove(); if(row.querySelectorAll('.block-wrapper').length === 0) row.remove(); }
    else wrapper.remove();
    scheduleSave(); toast('已删除');
  };

  function bindResizeEvents(host){
    if(!host) return;
    var block, handle;
    if(host.classList && (host.classList.contains('card-col') || host.classList.contains('text-block') || host.classList.contains('shape-block') || host.classList.contains('image-frame-block') || host.classList.contains('float-text-block'))){
      block = host;
      handle = host.querySelector(':scope > .resize-handle');
    } else {
      block = host.querySelector('.card-col, .text-block');
      handle = host.querySelector(':scope > .resize-handle');
    }
    if(!block || !handle) return;
    if(handle.dataset.bound) return;
    handle.dataset.bound = 'true';

    var isResizing = false, startW, startH, startResX, startResY;
    function startResize(e){
      if(!editMode) return;
      e.preventDefault(); e.stopPropagation();
      isResizing = true;
      var p = e.touches ? e.touches[0] : e;
      startResX = p.clientX; startResY = p.clientY;
      startW = block.offsetWidth; startH = block.offsetHeight;
      if(block.closest('.card-row')) block.style.flex = 'none';
      document.addEventListener('mousemove', onResize);
      document.addEventListener('touchmove', onResize, {passive: false});
      document.addEventListener('mouseup', stopResize);
      document.addEventListener('touchend', stopResize);
    }
    function onResize(e){
      if(!isResizing) return;
      e.preventDefault();
      var p = e.touches ? e.touches[0] : e;
      var newW = Math.max(16, startW + (p.clientX - startResX));
      var newH = Math.max(16, startH + (p.clientY - startResY));
      block.style.width = newW + 'px';
      block.style.height = newH + 'px';
      if(activeShapeEl === block){
        document.getElementById('shapeWidth').value = newW;
        document.getElementById('shapeWidthVal').textContent = newW + 'px';
        document.getElementById('shapeHeight').value = newH;
        document.getElementById('shapeHeightVal').textContent = newH + 'px';
      }
    }
    function stopResize(){
      isResizing = false;
      document.removeEventListener('mousemove', onResize);
      document.removeEventListener('touchmove', onResize);
      document.removeEventListener('mouseup', stopResize);
      document.removeEventListener('touchend', stopResize);
      scheduleSave();
    }
    handle.addEventListener('mousedown', startResize);
    handle.addEventListener('touchstart', startResize, {passive: false});
  }

  function bindAllResizeEvents(){
    document.querySelectorAll('.block-wrapper').forEach(function(w){
      var block = w.querySelector('.card-col, .text-block');
      if(!block) return;
      if(!w.querySelector(':scope > .resize-handle')){
        var h = document.createElement('div'); h.className = 'resize-handle'; w.appendChild(h);
      }
      bindResizeEvents(w);
    });
    document.querySelectorAll('.shape-block, .image-frame-block, .float-text-block').forEach(function(block){
      if(!block.querySelector('.resize-handle')){
        var h = document.createElement('div'); h.className = 'resize-handle'; block.appendChild(h);
      }
      bindResizeEvents(block);
    });
  }

  function makeDraggable(el){
    var isDragging = false, startX, startY, startLeft, startTop;
    function onStart(e){
      if(!editMode) return;
      if(e.target.classList.contains('resize-handle')) return;
      if(e.target.classList.contains('element-del')) return;
      if(e.target.classList.contains('float-del')) return;
      e.preventDefault();
      isDragging = true;
      var p = e.touches ? e.touches[0] : e;
      startX = p.clientX; startY = p.clientY;
      startLeft = el.offsetLeft; startTop = el.offsetTop;
      if(el.classList.contains('shape-block') || el.classList.contains('image-frame-block')){
        selectShape(el);
      }
      document.addEventListener('mousemove', onMove);
      document.addEventListener('touchmove', onMove, {passive: false});
      document.addEventListener('mouseup', onEnd);
      document.addEventListener('touchend', onEnd);
    }
    function onMove(e){
      if(!isDragging) return;
      e.preventDefault();
      var p = e.touches ? e.touches[0] : e;
      el.style.left = (startLeft + (p.clientX - startX)) + 'px';
      el.style.top = (startTop + (p.clientY - startY)) + 'px';
    }
    function onEnd(){
      isDragging = false;
      document.removeEventListener('mousemove', onMove);
      document.removeEventListener('touchmove', onMove);
      document.removeEventListener('mouseup', onEnd);
      document.removeEventListener('touchend', onEnd);
      scheduleSave();
    }
    el.addEventListener('mousedown', onStart);
    el.addEventListener('touchstart', onStart, {passive: false});
  }

  function getShapeSVG(type){
    var svgs = {
      circle:   '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><circle cx="50" cy="50" r="45" fill="currentColor"/></svg>',
      square:   '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" fill="currentColor"/></svg>',
      rounded:  '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" rx="20" fill="currentColor"/></svg>',
      triangle: '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,95 5,95" fill="currentColor"/></svg>',
      star:     '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 61,38 96,38 68,59 79,92 50,72 21,92 32,59 4,38 39,38" fill="currentColor"/></svg>',
      heart:    '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M50,88 C20,65 5,45 5,30 C5,15 18,5 32,5 C41,5 48,10 50,18 C52,10 59,5 68,5 C82,5 95,15 95,30 C95,45 80,65 50,88 Z" fill="currentColor"/></svg>',
      arrow:    '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M5,40 L60,40 L60,15 L95,50 L60,85 L60,60 L5,60 Z" fill="currentColor"/></svg>',
      diamond:  '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,50 50,95 5,50" fill="currentColor"/></svg>',
      hexagon:  '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 93,27 93,73 50,95 7,73 7,27" fill="currentColor"/></svg>',
      cross:    '<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M35,5 L65,5 L65,35 L95,35 L95,65 L65,65 L65,95 L35,95 L35,65 L5,65 L5,35 L35,35 Z" fill="currentColor"/></svg>'
    };
    return svgs[type] || svgs.circle;
  }

  function createShape(type){
    var shape = document.createElement('div');
    shape.className = 'shape-block';
    shape.dataset.shapeType = type;
    shape.style.left = '40px';
    shape.style.top = (window.scrollY + 200) + 'px';
    shape.style.width = '90px';
    shape.style.height = '90px';
    shape.style.color = styleSettings.accent;
    shape.innerHTML = getShapeSVG(type) + '<div class="resize-handle"></div><button class="element-del" contenteditable="false">×</button>';
    var del = shape.querySelector('.element-del');
    del.onclick = function(e){ e.stopPropagation(); if(confirm('删除这个图形吗？')){ shape.remove(); if(activeShapeEl === shape){ activeShapeEl = null; updateShapeStyleUI(); } scheduleSave(); } };
    document.getElementById('float-layer').appendChild(shape);
    makeDraggable(shape);
    bindResizeEvents(shape);
    selectShape(shape);
    scheduleSave();
    toast('已添加图形');
  }

  function createImageFrame(shapeType){
    var frame = document.createElement('div');
    frame.className = 'image-frame-block';
    frame.style.left = '40px';
    frame.style.top = (window.scrollY + 300) + 'px';
    frame.style.width = '120px';
    frame.style.height = '120px';
    if(shapeType === 'circle') frame.style.borderRadius = '50%';
    else if(shapeType === 'rounded') frame.style.borderRadius = '16px';
    else frame.style.borderRadius = '0px';
    var img = document.createElement('img');
    img.src = 'data:image/svg+xml,%3Csvg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"%3E%3Crect width="100" height="100" fill="%23333"/%3E%3Ctext x="50" y="55" font-size="14" fill="%23999" text-anchor="middle" font-family="sans-serif"%3E点击换图%3C/text%3E%3C/svg%3E';
    frame.appendChild(img);
    var handle = document.createElement('div'); handle.className = 'resize-handle'; frame.appendChild(handle);
    var del = document.createElement('button'); del.className = 'element-del'; del.textContent = '×';
    del.onclick = function(e){ e.stopPropagation(); if(confirm('删除这个图片框吗？')){ frame.remove(); if(activeShapeEl === frame){ activeShapeEl = null; updateShapeStyleUI(); } scheduleSave(); } };
    frame.appendChild(del);
    document.getElementById('float-layer').appendChild(frame);
    makeDraggable(frame);
    bindResizeEvents(frame);
    selectShape(frame);
    scheduleSave();
    toast('已添加图片框');
  }

  document.getElementById('btnAddShape').addEventListener('click', function(e){
    e.stopPropagation();
    document.getElementById('framePicker').classList.remove('show');
    document.getElementById('shapePicker').classList.toggle('show');
  });
  document.getElementById('btnAddImageFrame').addEventListener('click', function(e){
    e.stopPropagation();
    document.getElementById('shapePicker').classList.remove('show');
    document.getElementById('framePicker').classList.toggle('show');
  });
  document.querySelectorAll('#shapePicker .shape-pick-item').forEach(function(item){
    item.addEventListener('click', function(){
      createShape(this.dataset.shape);
      document.getElementById('shapePicker').classList.remove('show');
    });
  });
  document.querySelectorAll('#framePicker .shape-pick-item').forEach(function(item){
    item.addEventListener('click', function(){
      createImageFrame(this.dataset.frame);
      document.getElementById('framePicker').classList.remove('show');
    });
  });

  elementImageInput.addEventListener('change', function(){
    var file = elementImageInput.files && elementImageInput.files[0];
    if(!file || !currentElementImg) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      var img = new Image();
      img.onload = function(){
        var canvas = document.createElement('canvas');
        var MAX = 800; var w = img.width, h = img.height;
        var scale = Math.min(1, MAX / Math.max(w, h)); w *= scale; h *= scale;
        canvas.width = w; canvas.height = h;
        canvas.getContext('2d').drawImage(img, 0, 0, w, h);
        currentElementImg.src = canvas.toDataURL('image/jpeg', 0.8);
        scheduleSave(); toast('图片已更新');
      };
      img.src = ev.target.result;
    };
    reader.readAsDataURL(file);
  });

  document.getElementById('btnAddFloat').addEventListener('click', function(){
    var floatBlock = document.createElement('div');
    floatBlock.className = 'float-text-block';
    floatBlock.style.left = '40px';
    floatBlock.style.top = (window.scrollY + 150) + 'px';
    floatBlock.style.width = '150px';
    floatBlock.style.height = 'auto';
    floatBlock.setAttribute('contenteditable', 'true');
    floatBlock.innerHTML = '自由文本框';
    var dh = document.createElement('div'); dh.className = 'drag-handle'; dh.innerHTML = '≡'; floatBlock.appendChild(dh);
    var rh = document.createElement('div'); rh.className = 'resize-handle'; floatBlock.appendChild(rh);
    var del = document.createElement('button'); del.className = 'float-del'; del.textContent = '×';
    del.onclick = function(e){ e.stopPropagation(); if(confirm('删除这个浮动文本框吗？')){ floatBlock.remove(); scheduleSave(); } };
    floatBlock.appendChild(del);
    floatBlock.addEventListener('focusin', function(){ activeTextEl = floatBlock; updateTextStyleUI(); });
    document.getElementById('float-layer').appendChild(floatBlock);
    makeDraggable(floatBlock);
    bindResizeEvents(floatBlock);
    floatBlock.focus();
    scheduleSave(); toast('浮动文本已添加');
  });

  document.getElementById('btnAddPanel').addEventListener('click', function(){
    var name = prompt('请输入新页面的名称（将保存在「合集」中）：', '新页面');
    if(!name) return;
    name = name.trim();
    if(!name) return;

    var panels = document.querySelectorAll('#app .panel');
    var usedIds = {};
    panels.forEach(function(p){ usedIds[p.id] = true; });
    var newIndex = 0;
    while(usedIds['panel-' + newIndex]) newIndex++;
    var newPanelId = 'panel-' + newIndex;

    var newPanel = document.createElement('div');
    newPanel.id = newPanelId;
    newPanel.className = 'panel';
    newPanel.dataset.collection = 'true';
    newPanel.dataset.name = name;
    newPanel.style.display = 'none';
    newPanel.innerHTML =
      '<div class="feed-card glass-card">' +
        '<button class="card-del" contenteditable="false" title="删除这张卡片">×</button>' +
        '<div class="block-wrapper">' +
          '<div class="feed-text" data-edit>在此输入新页面内容...</div>' +
          '<div class="block-actions">' +
            '<button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>' +
            '<button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>' +
            '<button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>' +
            '<button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>' +
          '</div>' +
        '</div>' +
      '</div>' +
      '<button class="add-card-btn" onclick="addCard(\'' + newPanelId + '\')" style="margin-bottom:8px;">＋ 添加动态卡片</button>' +
      '<button class="add-card-btn" onclick="addTextBlock(\'' + newPanelId + '\')">＋ 添加自由文本块</button>' +
      '<div class="panel-nav">' +
        '<button class="panel-nav-btn" onclick="window.goBack()" title="上一页">←</button>' +
        '<div class="panel-nav-spacer"></div>' +
      '</div>';
    document.getElementById('app').appendChild(newPanel);

    if(editMode){
      newPanel.querySelectorAll('[data-edit]').forEach(function(el){
        el.setAttribute('contenteditable', 'true');
        el.setAttribute('spellcheck', 'false');
      });
    }

    refreshCollectionMenu();
    refreshLinkTargets();
    scheduleSave();
    toast('已添加到合集：' + name);
  });

  function refreshCollectionMenu(){
    var list = document.getElementById('collectionList');
    list.innerHTML = '';
    var items = document.querySelectorAll('#app .panel[data-collection="true"]');
    if(items.length === 0){
      list.innerHTML = '<div class="collection-empty">暂无页面</div>';
      return;
    }
    items.forEach(function(p){
      var item = document.createElement('a');
      item.className = 'collection-item';
      item.setAttribute('data-target', p.id);
      item.textContent = p.dataset.name || p.id;
      item.addEventListener('click', function(){
        switchTab(p.id);
        document.getElementById('collectionMenu').classList.remove('show');
      });
      list.appendChild(item);
    });
  }

  function refreshLinkTargets(){
    var select = document.getElementById('linkTargetSelect');
    if(!select) return;
    var cur = activeTextEl ? (activeTextEl.getAttribute('data-link') || '') : '';
    select.innerHTML = '<option value="">无</option>';
    document.querySelectorAll('#app .panel').forEach(function(p){
      var opt = document.createElement('option');
      opt.value = p.id;
      var name = p.dataset.name || p.id;
      var navEl = document.querySelector('.nav-link[data-target="' + p.id + '"]');
      if(navEl && !p.dataset.name) name = navEl.textContent.trim();
      if(p.dataset.collection === 'true') name = '合集 · ' + name;
      opt.textContent = name;
      select.appendChild(opt);
    });
    select.value = cur;
  }

  var navCollection = document.getElementById('navCollection');
  var collectionMenu = document.getElementById('collectionMenu');

  navCollection.addEventListener('click', function(e){
    e.stopPropagation();
    document.getElementById('shapePicker').classList.remove('show');
    document.getElementById('framePicker').classList.remove('show');
    document.getElementById('stylePanel').classList.remove('show');
    if(collectionMenu.classList.contains('show')){
      collectionMenu.classList.remove('show');
    } else {
      refreshCollectionMenu();
      collectionMenu.classList.add('show');
    }
  });

  function setEdit(on){
    editMode = on;
    document.body.classList.toggle('editing', on);
    if(!on){
      document.getElementById('stylePanel').classList.remove('show');
      document.getElementById('shapePicker').classList.remove('show');
      document.getElementById('framePicker').classList.remove('show');
      document.getElementById('collectionMenu').classList.remove('show');
      if(activeShapeEl){ activeShapeEl.classList.remove('selected'); activeShapeEl = null; updateShapeStyleUI(); }
    }
    document.querySelectorAll('[data-edit], .text-block, .float-text-block, .card-col').forEach(function(el){
      if(on){ el.setAttribute('contenteditable', 'true'); el.setAttribute('spellcheck', 'false'); }
      else el.removeAttribute('contenteditable');
    });
    var btn = document.getElementById('btnEdit');
    btn.classList.toggle('on', on);
    btn.querySelector('.ico').textContent = on ? '✓' : '✎';
    btn.querySelector('.label').textContent = on ? '完成' : '编辑';
    if(on){ bindAllResizeEvents(); refreshLinkTargets(); toast('编辑已开启'); }
    else { scheduleSave(); toast('已保存'); }
  }
  document.getElementById('btnEdit').addEventListener('click', function(){ setEdit(!editMode); });
  app.addEventListener('input', function(){ if(editMode) scheduleSave(); });

  function serialize(){
    var clone = app.cloneNode(true);
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    clone.querySelectorAll('.shape-block.selected, .image-frame-block.selected').forEach(function(el){ el.classList.remove('selected'); });
    return clone.innerHTML;
  }
  var saveTimer;
  function scheduleSave(){
    clearTimeout(saveTimer);
    saveTimer = setTimeout(function(){
      try { localStorage.setItem(KEY_CONTENT, serialize()); } catch(err) { toast('存储空间不足'); }
    }, 400);
  }

  var navHistory = [];

  var originalSwitchTab = function(targetPanelId){
    document.querySelectorAll('.nav-link').forEach(function(l){ l.classList.remove('active'); });
    document.querySelectorAll('#collectionList .collection-item').forEach(function(l){ l.classList.remove('active'); });
    
    var panel = document.getElementById(targetPanelId);
    if(!panel) return;
    var isCollection = panel.getAttribute('data-collection') === 'true';
    
    if(isCollection){
      document.getElementById('navCollection').classList.add('active');
      var collectionItem = document.querySelector('#collectionList .collection-item[data-target="' + targetPanelId + '"]');
      if(collectionItem) collectionItem.classList.add('active');
    } else {
      var targetNav = document.querySelector('.nav-link[data-target="' + targetPanelId + '"]');
      if(targetNav) targetNav.classList.add('active');
    }
    
    document.querySelectorAll('.panel').forEach(function(p){ p.style.display = 'none'; });
    panel.style.display = 'block';
    if(editMode) toast('已跳转');
  };

  window.switchTab = function(targetPanelId){
    var currentVisible = document.querySelector('.panel[style*="display: block"]');
    if(currentVisible && currentVisible.id !== targetPanelId){
      navHistory.push(currentVisible.id);
      if(navHistory.length > 50) navHistory.shift();
    }
    originalSwitchTab(targetPanelId);
    refreshLinkTargets();
  };

  window.goBack = function(){
    if(navHistory.length > 0){
      var prev = navHistory.pop();
      originalSwitchTab(prev);
    } else {
      originalSwitchTab('panel-0');
    }
  };

  document.addEventListener('click', function(e){
    if(editMode) return;
    var linkEl = e.target.closest('[data-link]');
    if(linkEl){
      var target = linkEl.getAttribute('data-link');
      if(target && document.getElementById(target)){
        e.preventDefault();
        e.stopPropagation();
        window.switchTab(target);
      }
    }
  });

  document.getElementById('topNav').addEventListener('click', function(e){
    var link = e.target.closest('.nav-link');
    if(!link) return;
    if(link.id === 'navCollection') return;
    window.switchTab(link.getAttribute('data-target'));
  });

  document.getElementById('postBoxTrigger').addEventListener('click', function(e){
    if(editMode && e.target.closest('[data-edit]')) return;
    window.switchTab('panel-1'); toast('已跳转到个人信息页');
  });

  window.addCard = function(panelId){
    var panel = document.getElementById(panelId); if(!panel) return;
    var card = document.createElement('div'); card.className = 'feed-card glass-card';
    card.innerHTML = '<button class="card-del" contenteditable="false" title="删除这张卡片">×</button>' +
      '<div class="feed-header"><div class="feed-avatar"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns=\'http://www.w3.org/2000/svg\' viewBox=\'0 0 100 100\'%3E%3Ccircle cx=\'50\' cy=\'50\' r=\'50\' fill=\'%23555\'/%3E%3Ctext x=\'50\' y=\'69\' font-size=\'40\' fill=\'%23fff\' text-anchor=\'middle\' font-family=\'monospace\'%3EN%3C/text%3E%3C/svg%3E"></div>' +
      '<div><div class="feed-name" data-edit>新朋友</div><div class="feed-handle" data-edit>@newuser</div></div>' +
      '<div class="feed-time" data-edit>刚刚</div></div>' +
      '<div class="block-wrapper"><div class="feed-text" data-edit>点击这里输入内容...</div>' +
      '<div class="resize-handle"></div>' +
      '<div class="block-actions">' +
        '<button class="block-action-btn" onclick="addRowHere(this)" title="添加行">＋</button>' +
        '<button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>' +
        '<button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>' +
        '<button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>' +
      '</div></div>' +
      '<div class="feed-footer"><span>💬 评论</span></div>';
    var addBtn = panel.querySelector('.add-card-btn'); panel.insertBefore(card, addBtn);
    if(editMode) card.querySelectorAll('[data-edit]').forEach(function(el){ el.setAttribute('contenteditable', 'true'); });
    bindResizeEvents(card.querySelector('.block-wrapper'));
    scheduleSave(); toast('已添加新卡片');
  };
  window.addTextBlock = function(panelId){
    var panel = document.getElementById(panelId); if(!panel) return;
    var wrapper = document.createElement('div'); wrapper.className = 'block-wrapper';
    wrapper.innerHTML = '<div class="text-block" contenteditable="true">点击输入自由文本内容...</div>' +
      '<div class="resize-handle"></div>' +
      '<div class="block-actions">' +
        '<button class="block-action-btn" onclick="addTextBlockBelow(this)" title="在下方添加文本">＋</button>' +
        '<button class="block-action-btn" onclick="addColHere(this)" title="添加列">▥</button>' +
        '<button class="block-action-btn divider" onclick="addDivider(this)" title="添加分隔线">—</button>' +
        '<button class="block-action-btn del" onclick="deleteBlock(this)" title="删除">×</button>' +
      '</div>';
    var addBtn = panel.querySelectorAll('.add-card-btn')[1]; panel.insertBefore(wrapper, addBtn);
    if(editMode) wrapper.querySelector('.text-block').focus();
    bindResizeEvents(wrapper); scheduleSave(); toast('已添加自由文本块');
  };

  document.getElementById('btnBgImage').addEventListener('click', function(){ bgInput.value = ''; bgInput.click(); });
  bgInput.addEventListener('change', function(){
    var file = bgInput.files && bgInput.files[0]; if(!file) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      document.body.style.backgroundImage = 'url(' + ev.target.result + ')';
      try { localStorage.setItem(KEY_BG_IMG, ev.target.result); } catch(e){ toast('背景图太大'); }
      toast('背景已更新');
    };
    reader.readAsDataURL(file);
  });

  app.addEventListener('click', function(e){
    if(!editMode) return;
    var img = e.target.closest('img[data-edit-img]');
    if(img){ e.preventDefault(); e.stopPropagation(); currentImg = img; fileInput.value = ''; fileInput.click(); }
  });
  fileInput.addEventListener('change', function(){
    var file = fileInput.files && fileInput.files[0]; if(!file || !currentImg) return;
    var reader = new FileReader();
    reader.onload = function(ev){
      var img = new Image();
      img.onload = function(){
        var canvas = document.createElement('canvas');
        var MAX = 800; var w = img.width, h = img.height;
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

  app.addEventListener('click', function(e){
    var del = e.target.closest('.card-del');
    if(!del || !editMode) return; e.preventDefault();
    if(confirm('确定删除这张卡片吗？')){ del.closest('.feed-card').remove(); scheduleSave(); toast('卡片已删除'); }
  });

  document.getElementById('btnExport').addEventListener('click', function(){
    var clone = document.documentElement.cloneNode(true);
    clone.querySelector('body').classList.remove('editing');
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    clone.querySelectorAll('.shape-block.selected, .image-frame-block.selected').forEach(function(el){ el.classList.remove('selected'); });
    clone.querySelectorAll('.collection-menu').forEach(function(el){ el.classList.remove('show'); });
    var blob = new Blob(['<!DOCTYPE html>\n' + clone.outerHTML], {type:'text/html;charset=utf-8'});
    var a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'my-profile.html';
    document.body.appendChild(a); a.click(); document.body.removeChild(a); toast('已导出文件');
  });
  document.getElementById('btnReset').addEventListener('click', function(){
    if(!confirm('确定恢复初始内容吗？所有修改将丢失！')) return;
    app.innerHTML = DEFAULT_HTML; setEdit(false); scheduleSave(); refreshCollectionMenu(); refreshLinkTargets(); toast('已重置');
  });

  var bgMusic = document.getElementById('bgMusic'); var musicPlayer = document.getElementById('musicPlayer'); var musicIcon = document.getElementById('musicIcon');
  musicPlayer.addEventListener('click', function(){
    if(bgMusic.paused){
      if(!bgMusic.src || bgMusic.src === window.location.href){ toast('请先设置音乐链接'); return; }
      bgMusic.play().then(function(){ musicPlayer.classList.add('playing'); musicIcon.textContent = '❚❚'; }).catch(function(){ toast('播放失败'); });
    } else {
      bgMusic.pause(); musicPlayer.classList.remove('playing'); musicIcon.textContent = '♪';
    }
  });

  document.addEventListener('click', function(e){
    var sp = document.getElementById('stylePanel');
    var spBtn = document.getElementById('btnStylePanel');
    if(sp.classList.contains('show') && !sp.contains(e.target) && !spBtn.contains(e.target)){
      if(e.target.closest('.shape-block') || e.target.closest('.image-frame-block')) return;
      if(e.target.closest('[contenteditable="true"]')) return;
      sp.classList.remove('show');
    }
    var shapeP = document.getElementById('shapePicker');
    var shapeBtn = document.getElementById('btnAddShape');
    if(shapeP.classList.contains('show') && !shapeP.contains(e.target) && !shapeBtn.contains(e.target)) shapeP.classList.remove('show');
    var frameP = document.getElementById('framePicker');
    var frameBtn = document.getElementById('btnAddImageFrame');
    if(frameP.classList.contains('show') && !frameP.contains(e.target) && !frameBtn.contains(e.target)) frameP.classList.remove('show');
    if(collectionMenu.classList.contains('show') && !collectionMenu.contains(e.target) && !navCollection.contains(e.target)){
      collectionMenu.classList.remove('show');
    }
  });

  setEdit(false);
  bindAllResizeEvents();
  refreshCollectionMenu();
  refreshLinkTargets();
})();
</script>
</body>
</html>
