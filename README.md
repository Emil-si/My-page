<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0,user-scalable=no,viewport-fit=cover">
<meta name="color-scheme" content="dark light">
<title>我的主页</title>
<style>
  :root{
    --border: rgba(255,255,255,0.12);
    --bg: #0d0d10;
    --text: #f0f0f0;
    --sub: #999;
    --accent: #4a9eff;
    --accent-text: #fff;
    --card-alpha: 0.55;
    --card: rgba(25,25,30,var(--card-alpha));
    --bar: rgba(20,20,25,0.75);
    --safe-top: env(safe-area-inset-top, 0px);
    --safe-bottom: env(safe-area-inset-bottom, 0px);
    color-scheme: dark;
  }
  html.light{
    --border: rgba(0,0,0,0.08);
    --bg: #f2f2f5;
    --text: #111;
    --sub: #666;
    --card: rgba(255,255,255,var(--card-alpha));
    --bar: rgba(255,255,255,0.78);
    color-scheme: light;
  }
  *{margin:0;padding:0;box-sizing:border-box;}
  html,body{max-width:100vw;overflow-x:hidden;background:transparent;}
  body{
    font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif;
    color:var(--text); min-height:100vh;
    padding-top:calc(58px + var(--safe-top));
    padding-bottom:calc(30px + var(--safe-bottom));
    padding-left:var(--safe-left); padding-right:var(--safe-right);
  }

  #bgLayer{
    position:fixed; inset:0; z-index:0;
    background-color:var(--bg);
    background-size:cover; background-position:center; background-repeat:no-repeat;
    pointer-events:none; transition:background-color .3s;
  }

  #toolbar{
    position:fixed; top:0; left:0; right:0; z-index:100;
    height:calc(48px + var(--safe-top));
    padding-top:var(--safe-top);
    padding-left:12px; padding-right:12px;
    display:flex; align-items:center; gap:6px;
    background:var(--bar);
    -webkit-backdrop-filter:blur(20px) saturate(180%);
    backdrop-filter:blur(20px) saturate(180%);
    border-bottom:1px solid var(--border);
    overflow-x:auto; scrollbar-width:none;
  }
  #toolbar::-webkit-scrollbar{display:none;}
  .btn{
    display:flex; align-items:center; gap:4px;
    padding:6px 12px; border-radius:18px;
    border:1px solid var(--border);
    background:rgba(127,127,127,0.06);
    color:var(--text); font-size:12px; font-family:monospace;
    cursor:pointer; white-space:nowrap; flex-shrink:0;
    transition:all .2s;
  }
  .btn:active{transform:scale(.95);}
  .btn.on{background:var(--accent); color:var(--accent-text); border-color:var(--accent);}
  .edit-only{display:none !important;}
  body.editing .edit-only{display:flex !important;}
  .spacer{flex:1;}

  #topnav{
    max-width:640px; margin:0 auto;
    padding:10px 14px; display:flex; gap:6px;
    position:sticky; top:calc(48px + var(--safe-top));
    z-index:90;
    background:var(--bar);
    -webkit-backdrop-filter:blur(20px) saturate(180%);
    backdrop-filter:blur(20px) saturate(180%);
    border-bottom:1px solid var(--border);
    overflow-x:auto; scrollbar-width:none;
    align-items:center;
  }
  #topnav::-webkit-scrollbar{display:none;}
  .navlink{
    flex:1 0 auto; text-align:center;
    padding:7px 14px; border-radius:18px;
    background:rgba(127,127,127,0.06);
    border:1px solid var(--border);
    color:var(--sub); text-decoration:none;
    font-size:11px; font-family:monospace; letter-spacing:.08em;
    cursor:pointer; white-space:nowrap; transition:all .2s;
  }
  .navlink.active{background:var(--accent); color:var(--accent-text); border-color:var(--accent);}
  .navlink.collection{
    flex:0 0 auto; min-width:38px; padding:7px 10px;
    display:flex; align-items:center; justify-content:center;
  }
  .navlink.collection.active{background:var(--accent); color:var(--accent-text);}

  #collectionMenu{
    position:fixed;
    top:calc(100px + var(--safe-top));
    left:calc(16px + var(--safe-left));
    z-index:200;
    background:var(--bar);
    -webkit-backdrop-filter:blur(20px) saturate(180%);
    backdrop-filter:blur(20px) saturate(180%);
    border:1px solid var(--border);
    border-radius:14px; padding:8px;
    box-shadow:0 12px 40px rgba(0,0,0,.4);
    min-width:160px; max-width:80vw;
    display:none;
  }
  #collectionMenu.show{display:block;}
  .citem{
    display:block; padding:8px 12px; border-radius:8px;
    color:var(--text); font-size:12px; font-family:monospace;
    cursor:pointer; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;
    transition:all .15s;
  }
  .citem:hover{background:rgba(127,127,127,0.15);}
  .citem.active{background:var(--accent); color:var(--accent-text);}
  .cempty{padding:8px 12px; font-size:11px; color:var(--sub); text-align:center;}

  #app{max-width:640px; margin:0 auto; padding:14px; position:relative; z-index:10;}
  .panel{display:none;}
  .panel.active{display:block;}

  .card{
    background:var(--card);
    -webkit-backdrop-filter:blur(20px) saturate(180%);
    backdrop-filter:blur(20px) saturate(180%);
    border:1px solid var(--border);
    border-radius:20px; padding:16px;
    margin-bottom:14px; position:relative;
    word-break:break-word;
  }

  .cover{
    width:calc(100% + 32px); height:180px;
    border-radius:20px 20px 0 0;
    background:#1a1a1a; position:relative; overflow:hidden;
    margin:-16px -16px 0 -16px;
  }
  .cover img{width:100%; height:100%; object-fit:cover; display:block;}
  .cover::after{
    content:""; position:absolute; inset:auto 0 0 0; height:120px;
    background:linear-gradient(to bottom, transparent, var(--card));
    pointer-events:none;
  }

  .profile{display:flex; align-items:flex-end; gap:12px; margin-top:-28px; position:relative; z-index:2; padding-bottom:10px;}
  .avatar{
    width:76px; height:76px; border-radius:50%;
    border:3px solid var(--card); overflow:hidden; flex-shrink:0;
    background:#222; box-shadow:0 6px 20px rgba(0,0,0,.4);
  }
  .avatar img{width:100%; height:100%; object-fit:cover; display:block;}
  .namebox{flex:1; padding-bottom:4px;}
  .name{font-size:22px; font-weight:700; margin-bottom:2px;}
  .handle{font-size:12px; color:var(--sub); font-family:monospace;}

  .stats{display:flex; gap:18px; font-size:13px; margin:10px 0 6px; color:var(--sub);}
  .stats b{color:var(--text); font-weight:600;}

  .grid{
    display:grid; grid-template-columns:repeat(3, 1fr);
    gap:6px; margin:12px 0;
  }
  .grid img{
    width:100%; aspect-ratio:1; object-fit:cover;
    border-radius:10px; display:block; cursor:pointer;
    background:rgba(127,127,127,0.1);
  }

  .quote{
    padding:14px; border:1px solid var(--border); border-radius:14px;
    background:rgba(127,127,127,0.04);
    font-size:13px; line-height:1.7; color:var(--sub); font-style:italic;
    margin:12px 0;
  }

  .post{
    background:rgba(127,127,127,0.05); border:1px solid var(--border);
    border-radius:16px; padding:14px; margin:12px 0 0;
  }
  .post input{
    width:100%; background:transparent; border:none; outline:none;
    color:var(--text); font-size:14px; font-family:inherit;
  }
  .post input::placeholder{color:var(--sub);}
  .postrow{display:flex; justify-content:space-between; align-items:center; margin-top:10px;}
  .postrow .icons{display:flex; gap:10px; color:var(--sub); font-size:16px;}
  .pubbtn{
    background:var(--accent); color:var(--accent-text);
    border:none; padding:7px 18px; border-radius:18px;
    font-size:12px; font-weight:600; cursor:pointer;
  }

  .feed{display:flex; gap:10px; margin-bottom:10px; align-items:center;}
  .feed .fav{width:36px; height:36px; border-radius:50%; overflow:hidden; background:#333; flex-shrink:0;}
  .feed .fav img{width:100%; height:100%; object-fit:cover; display:block;}
  .feed .meta{flex:1;}
  .feed .meta .n{font-size:13px; font-weight:600;}
  .feed .meta .h{font-size:11px; color:var(--sub); font-family:monospace;}
  .feed .time{font-size:11px; color:var(--sub);}

  .etext{
    font-size:14px; line-height:1.6;
    padding:4px 0; position:relative;
  }

  .divider{
    padding:8px 0; display:flex; align-items:center;
    position:relative;
  }
  .divider .line{
    height:1px; background:var(--border); width:100%;
    border-radius:1px; transition:all .2s;
  }
  body.editing .divider:hover .line{background:var(--accent);}

  .bwrap { position: relative; padding-right: 0; }
  body.editing .bwrap { padding-bottom: 42px; }

  .bwrap > .row-tools {
    position: absolute;
    bottom: 0;
    right: 0;
    display: none;
    flex-direction: row;
    gap: 8px;
    opacity: 0.8;
    z-index: 20;
  }
  body.editing .bwrap > .row-tools { 
    display: flex !important;
    opacity: 1; 
  }

  .row-tools button {
    width: 32px; 
    height: 32px; 
    border-radius: 8px;
    border: 1px solid var(--border);
    background: var(--accent); 
    color: var(--accent-text);
    font-size: 16px; 
    cursor: pointer; 
    padding: 0; 
    line-height: 1;
    display: flex; 
    align-items: center; 
    justify-content: center;
    transition: all .15s;
  }
  .row-tools button.dvd { background: #7d90b8; font-size: 18px; }
  .row-tools button.del { background: #e5484d; }
  .row-tools button:hover { transform: scale(1.1); }

  /* ===== 浮动元素样式 ===== */
  .shape{
    position: absolute;
    z-index: 999999;
    cursor: move;
    pointer-events: auto;
    touch-action: none;
    user-select: none;
    filter: drop-shadow(0 4px 12px rgba(0,0,0,.3));
    display: block;
  }
  .shape svg{width:100%; height:100%; display:block; pointer-events:none;}
  .frame{
    position: absolute;
    z-index: 999999;
    overflow:hidden; background:#333;
    border:2px solid var(--border);
    cursor:move;
    pointer-events:auto; touch-action:none; user-select:none;
    box-shadow:0 6px 24px rgba(0,0,0,.3);
    display: block;
  }
  .frame img{width:100%; height:100%; object-fit:cover; display:block; pointer-events:none;}
  .floater{
    position: absolute;
    z-index: 999999;
    min-width:100px; min-height:40px;
    padding:12px; border-radius:12px; border:2px dashed var(--accent);
    background:var(--card);
    -webkit-backdrop-filter:blur(12px); backdrop-filter:blur(12px);
    color:var(--text); font-size:14px; line-height:1.5;
    word-break:break-word; cursor:move; outline:none;
    pointer-events:auto; touch-action:none;
    display: block;
  }

  .xdel{
    position:absolute; top:-10px; left:-10px;
    width:22px; height:22px; border-radius:50%;
    background:#e5484d; color:#fff; border:none;
    font-size:12px; cursor:pointer; line-height:1;
    display:none; align-items:center; justify-content:center;
    padding:0; z-index:5;
  }
  
  .rz{
    position:absolute; bottom:0; right:0;
    width:28px; height:28px;
    background:linear-gradient(135deg, transparent 48%, var(--accent) 48%);
    cursor:se-resize; z-index:10;
    border-bottom-right-radius:10px;
    touch-action:none;
  }
  .rz::before{
    content:""; position:absolute;
    right:-6px; bottom:-6px; width:44px; height:44px;
    background:transparent; cursor:se-resize;
  }
  body:not(.editing) .rz{display:none;}

  #shapePicker, #framePicker{
    position:fixed;
    top:calc(110px + var(--safe-top));
    left:50%; transform:translateX(-50%);
    z-index:100001;
    background:var(--bar);
    -webkit-backdrop-filter:blur(24px) saturate(180%);
    backdrop-filter:blur(24px) saturate(180%);
    border:1px solid var(--border);
    border-radius:16px; padding:14px;
    box-shadow:0 12px 40px rgba(0,0,0,.5);
    display:none; grid-template-columns:repeat(5, 1fr); gap:8px;
    max-width:calc(100vw - 24px);
  }
  #shapePicker.show, #framePicker.show{display:grid;}
  .pick{
    width:44px; height:44px;
    display:flex; align-items:center; justify-content:center;
    border-radius:10px; cursor:pointer;
    background:rgba(127,127,127,0.06);
    border:1px solid var(--border);
    color:var(--text); transition:all .2s;
  }
  .pick:hover{background:var(--accent); color:var(--accent-text); border-color:var(--accent);}
  .pick svg{width:26px; height:26px;}
  .pick-title{
    grid-column:1/-1; text-align:center;
    font-size:11px; color:var(--accent);
    font-family:monospace; letter-spacing:.1em;
    padding-bottom:6px; border-bottom:1px solid var(--border); margin-bottom:4px;
  }

  .pagenav{display:flex; justify-content:space-between; align-items:center; padding:10px 0; margin-top:10px;}
  .navbtn{
    width:44px; height:44px; border-radius:50%;
    background:var(--card); border:1px solid var(--border);
    color:var(--text); font-size:18px; cursor:pointer;
    display:flex; align-items:center; justify-content:center;
    -webkit-backdrop-filter:blur(20px); backdrop-filter:blur(20px);
  }
  .navbtn:active{background:var(--accent); color:var(--accent-text);}
  .spacer30{width:44px;}

  .extras{display:none; gap:6px; flex-wrap:wrap; margin-bottom:10px;}
  body.editing .extras{display:flex;}
  .exbtn{
    padding:8px 14px; border-radius:12px;
    border:1px dashed var(--border); background:transparent;
    color:var(--sub); font-size:12px; font-family:monospace;
    cursor:pointer; transition:all .2s;
  }
  .exbtn:hover{background:rgba(127,127,127,0.08); color:var(--accent); border-color:var(--accent);}

  #stylePanel{
    position:fixed;
    right:14px; top:calc(110px + var(--safe-top));
    z-index:100000;
    width:260px;
    background:var(--bar);
    -webkit-backdrop-filter:blur(24px) saturate(180%);
    backdrop-filter:blur(24px) saturate(180%);
    border:1px solid var(--border);
    border-radius:16px;
    box-shadow:0 12px 40px rgba(0,0,0,.5);
    padding:14px;
    display:none;
    flex-direction:column;
    gap:8px;
    max-height:75vh;
    overflow-y:auto;
    font-size:12px;
  }
  #stylePanel.show{display:flex;}
  .sp-title{
    font-size:11px; color:var(--accent);
    font-family:monospace; letter-spacing:.1em;
    padding-bottom:6px; border-bottom:1px solid var(--border);
    margin-bottom:4px;
  }
  .sp-row{display:flex; align-items:center; justify-content:space-between; gap:8px; margin-bottom:6px;}
  .sp-row > span{flex-shrink:0; font-size:11px; color:var(--sub);}
  .sp-row input[type="range"]{flex:1; cursor:pointer; height:22px;}
  .sp-row input[type="color"]{width:30px; height:30px; border:none; border-radius:8px; cursor:pointer; background:transparent;}
  .sp-row select{
    flex:1; padding:5px 8px; border-radius:8px;
    background:rgba(127,127,127,0.08); color:var(--text);
    border:1px solid var(--border); font-size:11px; outline:none;
  }
  .sp-group{display:flex; gap:4px; flex-wrap:wrap;}
  .sp-btn{
    padding:5px 10px; border-radius:8px;
    border:1px solid var(--border);
    background:transparent; color:var(--text);
    font-size:11px; font-family:monospace; cursor:pointer;
    transition:all .15s;
  }
  .sp-btn:hover{background:rgba(127,127,127,0.12);}
  .sp-btn.on{background:var(--accent); color:var(--accent-text); border-color:var(--accent);}
  .sp-note{
    font-size:10px; color:var(--sub);
    line-height:1.5; padding:6px 8px;
    background:rgba(127,127,127,0.06);
    border-radius:8px; margin-top:4px;
  }

  /* ===== 音乐播放面板 ===== */
  #musicPanel {
    position: fixed;
    bottom: calc(20px + var(--safe-bottom));
    right: calc(14px + 48px + 12px);
    z-index: 210;
    background: var(--bar);
    -webkit-backdrop-filter: blur(24px) saturate(180%);
    backdrop-filter: blur(24px) saturate(180%);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: 999px;
    padding: 6px 14px;
    box-shadow: 0 12px 40px rgba(0, 0, 0, 0.4);
    display: none;
    flex-direction: row;
    align-items: center;
    gap: 10px;
  }
  #musicPanel.show { display: flex; }

  .mp-controls { display: flex; align-items: center; gap: 2px; }

  .mp-ctrl-btn {
    width: 34px; height: 34px; border-radius: 50%;
    border: none; background: transparent;
    color: var(--text); cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    padding: 0; transition: all 0.15s ease;
  }
  .mp-ctrl-btn svg { width: 16px; height: 16px; }
  .mp-ctrl-btn:hover { background: rgba(127, 127, 127, 0.2); }

  .mp-ctrl-btn.play {
    background: var(--accent); color: var(--accent-text); margin: 0 2px;
  }
  .mp-ctrl-btn.play:hover { transform: scale(1.05); }

  .mp-vol-container {
    display: flex; align-items: center; justify-content: center;
    height: 38px; width: 20px;
  }
  #mpVolume {
    writing-mode: vertical-lr; direction: rtl;
    -webkit-appearance: slider-vertical; appearance: slider-vertical;
    width: 4px; height: 38px;
    background: rgba(127, 127, 127, 0.3);
    border-radius: 2px; outline: none; cursor: pointer;
  }
  #mpVolume::-webkit-slider-thumb {
    -webkit-appearance: none;
    width: 12px; height: 12px; border-radius: 50%;
    background: var(--text); cursor: pointer;
  }

  .mp-add {
    width: 28px; height: 28px; border-radius: 50%;
    border: 1px solid var(--border); background: transparent;
    color: var(--sub); font-size: 16px;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; padding: 0; transition: all 0.15s;
  }
  .mp-add:hover { border-color: var(--accent); color: var(--accent); }

  /* 右下角悬浮球 */
  #musicBtn {
    position: fixed; 
    bottom: calc(20px + var(--safe-bottom)); 
    right: 14px;
    width: 48px; height: 48px; 
    border-radius: 50%;
    background: var(--card);
    border: 1px solid var(--border);
    -webkit-backdrop-filter: blur(20px); backdrop-filter: blur(20px);
    box-shadow: 0 6px 24px rgba(0,0,0,.35);
    display: flex; align-items: center; justify-content: center;
    color: var(--text); 
    font-size: 22px;
    cursor: pointer; z-index: 220;
    touch-action: none; user-select: none;
  }

  #musicBtn.playing {
    animation: spin 4s linear infinite;
  }

  body.editing [data-edit]{
    outline:1px dashed transparent; outline-offset:4px;
    border-radius:4px; cursor:text; transition:outline .2s;
  }
  body.editing [data-edit]:hover{outline-color:var(--accent);}
  body.editing [data-edit]:focus{
    outline:2px solid var(--accent);
    background:rgba(74,158,255,0.08);
  }

  .linktext{
    cursor:pointer; text-decoration:underline;
    text-decoration-style:dotted; text-underline-offset:4px;
  }
  .linktext:hover{color:var(--accent);}
  .linktext::after{content:" ↗"; font-size:.8em; opacity:.7;}

  #picker{
    position:fixed; left:0; right:0; bottom:0;
    height:52vh; z-index:100002;
    background:var(--bar);
    -webkit-backdrop-filter:blur(24px) saturate(180%);
    backdrop-filter:blur(24px) saturate(180%);
    border-top:1px solid var(--border);
    border-radius:20px 20px 0 0;
    transform:translateY(100%); transition:transform .3s ease;
    display:flex; flex-direction:column;
  }
  #picker.show{transform:translateY(0);}
  .ph{padding:12px 16px; border-bottom:1px solid var(--border); display:flex; align-items:center; justify-content:space-between; flex-shrink:0;}
  .ptitle{font-size:12px; font-family:monospace; color:var(--accent); letter-spacing:.1em;}
  .pclose{
    background:transparent; border:1px solid var(--border);
    color:var(--text); font-size:20px; width:30px; height:30px;
    border-radius:8px; cursor:pointer; line-height:1; padding:0;
  }
  .pgrid{
    flex:1; overflow-y:auto; padding:12px;
    display:grid; grid-template-columns:repeat(4, 1fr); gap:6px;
    padding-bottom:calc(12px + var(--safe-bottom));
  }
  .pgrid img{
    width:100%; aspect-ratio:1; object-fit:cover;
    border-radius:8px; cursor:pointer;
    border:2px solid transparent; transition:border-color .2s;
    background:rgba(127,127,127,0.1);
  }
  .pgrid img:hover{border-color:var(--accent);}

  #toast{
    position:fixed; left:50%; bottom:calc(80px + var(--safe-bottom));
    transform:translateX(-50%) translateY(20px);
    background:var(--accent); color:var(--accent-text);
    padding:9px 18px; border-radius:18px;
    font-size:12px; opacity:0; pointer-events:none;
    transition:all .3s; z-index:100003;
  }
  #toast.show{opacity:1; transform:translateX(-50%) translateY(0);}

  #musicDialog{
    position:fixed; inset:0; z-index:100004;
    background:rgba(0,0,0,.5);
    -webkit-backdrop-filter:blur(6px); backdrop-filter:blur(6px);
    display:none; align-items:center; justify-content:center; padding:20px;
  }
  #musicDialog.show{display:flex;}
  .mcard{
    width:100%; max-width:340px;
    background:var(--card); border:1px solid var(--border);
    border-radius:18px; padding:20px;
    -webkit-backdrop-filter:blur(24px); backdrop-filter:blur(24px);
  }
  .mcard h3{font-size:13px; color:var(--accent); margin-bottom:12px; font-family:monospace; letter-spacing:.1em; text-align:center;}
  .mcard input{
    width:100%; padding:11px 12px; border-radius:10px;
    border:1px solid var(--border); background:rgba(127,127,127,0.08);
    color:var(--text); font-size:13px; font-family:monospace; outline:none; margin-bottom:12px;
  }
  .mcard .row{display:flex; gap:8px;}
  .mcard .row button{
    flex:1; padding:10px; border-radius:10px;
    border:1px solid var(--border); background:transparent;
    color:var(--text); font-size:13px; font-family:monospace; cursor:pointer;
  }
  .mcard .row button.primary{background:var(--accent); color:var(--accent-text); border-color:var(--accent); font-weight:600;}

  /* ===== 卡片操作按钮 ===== */
  .card-actions { position: absolute; top: 8px; right: 8px; display: none; gap: 4px; z-index: 100; }
  body.editing .card-actions { display: flex !important; }
  .card-actions button { background: #e5484d; color: #fff; border: none; border-radius: 6px; padding: 4px 8px; font-size: 11px; cursor: pointer; font-family: monospace; }
  .card-actions button:hover { transform: scale(1.05); }

  /* ===== 添加新卡片按钮 ===== */
  .add-card-btn {
    display: none; width: 100%; padding: 12px; margin: 4px 0 14px 0;
    border: 1px dashed var(--border); background: rgba(127,127,127,0.05);
    color: var(--sub); border-radius: 14px; cursor: pointer;
    font-family: monospace; font-size: 12px; transition: all 0.2s;
  }
  body.editing .add-card-btn { display: block !important; }
  .add-card-btn:hover { border-color: var(--accent); color: var(--accent); background: rgba(74,158,255,0.08); }

  /* ==================== 开屏动画样式 ==================== */
  #splash {
    position: fixed; inset: 0; z-index: 99999;
    background: #1c1c1e;
    overflow: hidden;
  }

  .splash-card {
    position: absolute;
    top: 50%; left: 50%;
    width: 82%; max-width: 420px;
    aspect-ratio: 4 / 5;
    background: rgba(25,25,30,0.85);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 28px;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    padding: 30px; text-align: center; gap: 20px;
    
    transform: translate(-50%, -50%) scale(0.9);
    opacity: 0;
    transition: transform 0.6s cubic-bezier(0.16, 1, 0.3, 1), opacity 0.6s ease;
  }

  .splash-card.show {
    transform: translate(-50%, -50%) scale(1);
    opacity: 1;
  }

  .splash-title { font-size: 48px; font-weight: 800; color: #f0f0f0; }
  .splash-desc { font-size: 14px; color: #999; font-family: monospace; }

  .splash-enter-btn {
    position: absolute; bottom: 40px; right: 24px;
    padding: 12px 20px; background: rgba(25,25,30,0.85);
    border: 1px solid rgba(255,255,255,0.12); border-radius: 999px;
    color: #f0f0f0; font-size: 13px; font-family: monospace;
    cursor: pointer; opacity: 0; transition: opacity 0.5s ease 0.4s;
  }
  .splash-enter-btn.show { opacity: 1; }

  /* 编辑模式下，开屏文字可编辑 */
  body.editing .splash-title[data-splash-edit],
  body.editing .splash-desc[data-splash-edit] {
      outline: 2px dashed var(--accent);
      outline-offset: 4px;
      cursor: text;
  }
  body.editing .splash-title[data-splash-edit]:focus,
  body.editing .splash-desc[data-splash-edit]:focus {
      background: rgba(74,158,255,0.08);
  }

  /* ==================== 文字特效基础样式 ==================== */
  [data-edit].fx-hidden { opacity: 0; transform: translateY(20px); transition: opacity 0.8s ease, transform 0.8s ease; }
  [data-edit].fx-visible { opacity: 1; transform: translateY(0); }
  [data-edit].fx-slide-up { opacity: 0; transform: translateY(30px); transition: opacity 0.8s ease, transform 0.8s cubic-bezier(0.16, 1, 0.3, 1); }
  [data-edit].fx-slide-up.fx-visible { opacity: 1; transform: translateY(0); }
  [data-edit].fx-fade-in { opacity: 0; transition: opacity 1s ease; }
  [data-edit].fx-fade-in.fx-visible { opacity: 1; }
  [data-edit].fx-scale-in { opacity: 0; transform: scale(0.9); transition: opacity 0.8s ease, transform 0.8s ease; }
  [data-edit].fx-scale-in.fx-visible { opacity: 1; transform: scale(1); }

  @supports not ((backdrop-filter:blur(1px)) or (-webkit-backdrop-filter:blur(1px))){
    #toolbar, #topnav, .card, #musicBtn, #picker, .mcard, .navbtn, #collectionMenu, #shapePicker, #framePicker, #stylePanel, #musicPanel { background-color:rgba(20,20,25,0.96) !important; background-image:none !important; }
    html.light #toolbar, html.light #topnav, html.light .card, html.light #musicBtn, html.light #picker, html.light .mcard, html.light .navbtn, html.light #collectionMenu, html.light #shapePicker, html.light #framePicker, html.light #stylePanel, html.light #musicPanel { background-color:rgba(255,255,255,0.97) !important; background-image:none !important; }
  }

  @media (max-width:640px){
    body{padding-top:calc(52px + var(--safe-top));}
    #toolbar{height:calc(44px + var(--safe-top)); padding:0 8px; gap:5px;}
    .btn{padding:5px 9px; font-size:11px;}
    .btn .lbl{display:none;}
    .btn .ico{font-size:15px;}
    #topnav{top:calc(44px + var(--safe-top)); padding:8px 10px; gap:4px;}
    .navlink{padding:6px 11px; font-size:10px;}
    #app{padding:10px;}
    .card{padding:14px; border-radius:16px; margin-bottom:10px;}
    .cover{height:150px; margin:-14px -14px 0 -14px; width:calc(100% + 28px);}
    .avatar{width:64px; height:64px; border-width:2px;}
    .name{font-size:18px;}
    .profile{margin-top:-22px;}
    .pgrid{grid-template-columns:repeat(3, 1fr);}
    .pick{width:38px; height:38px;}
    .pick svg{width:22px; height:22px;}
    #stylePanel{ top:auto !important; bottom:0 !important; left:0 !important; right:0 !important; width:100vw !important; max-width:100vw !important; max-height:38vh; border-radius:20px 20px 0 0 !important; padding:10px 14px calc(10px + var(--safe-bottom)) 14px; transform:translateY(100%); transition:transform .3s cubic-bezier(.2,.8,.2,1); display:flex !important; visibility:hidden; font-size:11px; }
    #stylePanel.show{transform:translateY(0); visibility:visible;}
    #stylePanel .sp-title{margin-top:4px;padding-bottom:3px;}
    #stylePanel .sp-row{margin-bottom:4px;gap:6px;}
    #stylePanel .sp-row > span{font-size:10px; min-width:48px;}
    #stylePanel .sp-btn{padding:4px 8px; font-size:10px;}
    #stylePanel .sp-note{display:none;}
    .sp-row > span{font-size:11px; min-width:52px;}
    .sp-btn{padding:6px 12px; font-size:11px;}
    .bwrap > .row-tools { gap: 6px; }
    .row-tools button { width: 30px; height: 30px; font-size: 15px; }
  }
  /* ===== 开屏专用工具栏 ===== */
  #splashToolbar {
    position: fixed;
    top: calc(48px + var(--safe-top));
    left: 0; right: 0;
    z-index: 101;
    height: 44px;
    padding: 0 12px;
    display: none; /* 默认隐藏 */
    align-items: center;
    gap: 8px;
    background: var(--bar);
    -webkit-backdrop-filter: blur(20px) saturate(180%);
    backdrop-filter: blur(20px) saturate(180%);
    border-bottom: 1px solid var(--border);
    overflow-x: auto;
    scrollbar-width: none;
  }
  #splashToolbar::-webkit-scrollbar { display: none; }
  #splashToolbar.show { display: flex; }

  /* 开屏浮动元素 */
  .splash-floater {
    position: absolute;
    z-index: 100005;
    min-width: 80px; min-height: 30px;
    padding: 8px 12px;
    border-radius: 10px;
    border: 2px dashed var(--accent);
    background: rgba(25,25,30,0.7);
    -webkit-backdrop-filter: blur(12px);
    backdrop-filter: blur(12px);
    color: #f0f0f0;
    font-size: 14px; line-height: 1.5;
    word-break: break-word;
    cursor: move; outline: none;
    pointer-events: auto;
    touch-action: none;
    user-select: none;
  }
  body:not(.editing) .splash-floater {
    border-color: transparent;
    background: transparent;
    -webkit-backdrop-filter: none;
    backdrop-filter: none;
    cursor: default;
  }

  .splash-floater .splash-fdel {
    position: absolute;
    top: -10px; left: -10px;
    width: 20px; height: 20px;
    border-radius: 50%;
    background: #e5484d;
    color: #fff;
    border: none;
    font-size: 11px;
    line-height: 1;
    cursor: pointer;
    display: none;
    align-items: center;
    justify-content: center;
    padding: 0;
    z-index: 5;
  }
  body.editing .splash-floater:hover .splash-fdel,
  body.editing .splash-floater:focus-within .splash-fdel {
    display: flex;
  }
</style>
</head>
<body>

<!-- ==================== 开屏动画 ==================== -->
<div id="splash">
  <div class="splash-card" id="splashCard">
    <div class="splash-title" data-splash-edit="title">肆</div>
    <div class="splash-desc" data-splash-edit="desc">双击此文字可修改</div>
  </div>
  <button class="splash-enter-btn" id="splashEnterBtn">
    <span class="splash-btn-text">进入主页</span>
  </button>
</div>

<div id="bgLayer"></div>

<div id="toolbar">
  <button class="btn" id="btnEdit"><span class="ico">✎</span><span class="lbl">编辑</span></button>
  <button class="btn edit-only" id="btnStyle"><span class="ico">⚙️</span><span class="lbl">样式</span></button>
  <button class="btn edit-only" id="btnBg"><span class="ico">🖼️</span><span class="lbl">背景</span></button>
  <button class="btn edit-only" id="btnAddFloat"><span class="ico">＋</span><span class="lbl">浮动文本</span></button>
  <button class="btn edit-only" id="btnAddShape"><span class="ico">◆</span><span class="lbl">图形</span></button>
  <button class="btn edit-only" id="btnAddFrame"><span class="ico">🖼️</span><span class="lbl">图片框</span></button>
  <button class="btn edit-only" id="btnOpenSplash"><span class="ico">🎬</span><span class="lbl">开屏</span></button>
  <button class="btn edit-only" id="btnAddPanel"><span class="ico">📄</span><span class="lbl">新页面</span></button>
  <button class="btn edit-only" id="btnExport"><span class="ico">⬇</span><span class="lbl">导出</span></button>
  <button class="btn edit-only" id="btnImport"><span class="ico">⬆</span><span class="lbl">导入</span></button>
  <button class="btn edit-only" id="btnReset"><span class="ico">⟲</span><span class="lbl">重置</span></button>
  <span class="spacer"></span>
  <button class="btn" id="btnTheme"><span class="ico" id="themeIcon">☀</span></button>
</div>

<!-- 开屏专用工具栏：只在开屏编辑模式下显示 -->
<div id="splashToolbar">
  <button class="btn" id="btnSplashAddFloat"><span class="ico">＋</span><span class="lbl">开屏浮动文本</span></button>
  <span class="spacer"></span>
  <span style="font-size:11px;color:var(--sub);font-family:monospace;flex-shrink:0;">字号</span>
  <input type="range" id="splashFontSize" min="10" max="96" value="14" style="width:90px;flex-shrink:0;">
  <span style="font-size:11px;color:var(--sub);font-family:monospace;flex-shrink:0;">特效</span>
  <select id="splashFxType" style="flex-shrink:0;padding:4px 6px;border-radius:8px;background:rgba(127,127,127,0.08);color:var(--text);border:1px solid var(--border);font-size:11px;outline:none;">
    <option value="none">无</option>
    <option value="fx-slide-up">上滑</option>
    <option value="fx-fade-in">淡入</option>
    <option value="fx-scale-in">缩放</option>
  </select>
  <button class="btn" id="btnSplashClose" style="flex-shrink:0;"><span class="ico">✓</span><span class="lbl">完成</span></button>
</div>

<div id="topnav">
  <a class="navlink collection" id="navCollection" title="合集">
    <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.4" stroke-linecap="round">
      <line x1="3" y1="6" x2="21" y2="6"/>
      <line x1="3" y1="12" x2="21" y2="12"/>
      <line x1="3" y1="18" x2="21" y2="18"/>
    </svg>
  </a>
  <a class="navlink active" data-panel="panel-0">WELCOME</a>
  <a class="navlink" data-panel="panel-1">PERSONAL INFO</a>
  <a class="navlink" data-panel="panel-2">MY STYLE</a>
  <a class="navlink" data-panel="panel-3">CATEGORIES</a>
</div>

<div id="collectionMenu">
  <div id="collectionList"></div>
</div>

<div id="stylePanel">
  <div class="sp-title">卡片透明度</div>
  <div class="sp-row"><span>不透明</span><input type="range" id="spCardAlpha" min="0" max="100" value="55"><span id="spCardAlphaVal" style="min-width:34px;text-align:right;">55%</span></div>
  <div class="sp-title" style="margin-top:6px;">文字样式</div>
  <div class="sp-row"><span>字号</span><input type="range" id="spFontSize" min="10" max="48" value="14"><span id="spFontSizeVal" style="min-width:34px;text-align:right;">14px</span></div>
  <div class="sp-row"><span>字体</span>
    <select id="spFontFamily">
      <option value="default">系统默认</option>
      <option value="'Courier New',monospace">等宽</option>
      <option value="Georgia,serif">衬线</option>
      <option value="'Comic Sans MS',cursive">手写</option>
    </select>
  </div>
  <div class="sp-row"><span>对齐</span>
    <div class="sp-group">
      <button class="sp-btn" data-align="left">左</button>
      <button class="sp-btn" data-align="center">中</button>
      <button class="sp-btn" data-align="right">右</button>
    </div>
  </div>
  <div class="sp-row"><span>颜色</span><input type="color" id="spTextColor" value="#f0f0f0"></div>
  <div class="sp-row"><span>样式</span>
    <div class="sp-group">
      <button class="sp-btn" id="spBold">B</button>
      <button class="sp-btn" id="spItalic">I</button>
      <button class="sp-btn" id="spUnderline">U</button>
    </div>
  </div>
  <div class="sp-row"><span>链接到</span>
    <select id="spLink"><option value="">无</option></select>
  </div>
  <div class="sp-row"><span>文字特效</span>
    <select id="spFxType">
      <option value="none">无特效</option>
      <option value="fx-slide-up">上滑出现</option>
      <option value="fx-fade-in">淡入出现</option>
      <option value="fx-scale-in">缩放出现</option>
    </select>
  </div>
  <div class="sp-row"><span>出场顺序</span>
    <select id="spFxOrder">
      <option value="0">第 1 个出现</option>
      <option value="1">第 2 个出现</option>
      <option value="2">第 3 个出现</option>
      <option value="3">第 4 个出现</option>
      <option value="4">第 5 个出现</option>
    </select>
  </div>
  <div class="sp-row" id="spShapeColorRow" style="display:none;">
    <span>图形颜色</span>
    <input type="color" id="spShapeColor" value="#4a9eff">
  </div>
  <div class="sp-row" id="spShapeOpacityRow" style="display:none;">
    <span>透明度</span>
    <input type="range" id="spShapeOpacity" min="0" max="100" value="100">
    <span id="spShapeOpacityVal" style="min-width:34px;text-align:right;">100%</span>
  </div>
  <!-- 新增：开屏标题字号 -->
  <div class="sp-title" style="margin-top:6px;">开屏动画</div>
  <div class="sp-row">
    <span>标题字号</span>
    <input type="range" id="spSplashTitleSize" min="20" max="96" value="48">
    <span id="spSplashTitleSizeVal" style="min-width:34px;text-align:right;">48px</span>
  </div>
  <div class="sp-note">💡 想分享给朋友看：编辑完 → 点「⬇ 导出」下载 HTML → 上传到 GitHub → 分享新链接。</div>
</div>

<div id="app">
  <section class="panel active" id="panel-0">
    <div class="card">
      <div class="card-actions"><button onclick="deleteCard(this)">× 删除卡片</button></div>
      <div class="cover">
        <img data-edit-img id="coverImg" src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Crect width='160' height='90' fill='%23111'/%3E%3Ctext x='80' y='49' font-size='7' fill='%23555' text-anchor='middle' font-family='monospace'%3E点击更换封面%3C/text%3E%3C/svg%3E" alt="封面">
      </div>
      <div class="profile">
        <div class="avatar"><img data-edit-img id="avatarImg" src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23222'/%3E%3Ctext x='50' y='68' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像"></div>
        <div class="namebox">
          <div class="name bwrap"><span class="etext" data-edit>肆</span></div>
          <div class="handle bwrap"><span class="etext" data-edit>@InfernalBeyond.com</span></div>
        </div>
      </div>
      <div class="bwrap">
        <div class="stats">
          <div><b data-edit>520</b> 正在关注</div>
          <div><b data-edit>1314</b> 关注者</div>
        </div>
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
      <div class="grid" id="grid0">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='26' fill='%23888' text-anchor='middle'%3E1%3C/text%3E%3C/svg%3E" alt="图1">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='26' fill='%23888' text-anchor='middle'%3E2%3C/text%3E%3C/svg%3E" alt="图2">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='26' fill='%23888' text-anchor='middle'%3E3%3C/text%3E%3C/svg%3E" alt="图3">
      </div>
      <div class="bwrap">
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
      <div class="bwrap">
        <div class="quote"><span class="etext" data-edit>——They say time devours all things but say nothing about what becomes of the things as time digest them.</span></div>
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
      <div class="post">
        <input type="text" placeholder="说点什么..." data-edit>
        <div class="postrow">
          <div class="icons"><span>🖼️</span><span>👥</span><span>📍</span></div>
          <button class="pubbtn">发布 →</button>
        </div>
      </div>
    </div>
    <div class="card">
      <div class="card-actions"><button onclick="deleteCard(this)">× 删除卡片</button></div>
      <div class="bwrap">
        <div class="feed">
          <div class="fav"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23555'/%3E%3Ctext x='50' y='68' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EW%3C/text%3E%3C/svg%3E" alt="动态头像"></div>
          <div class="meta">
            <div class="n"><span class="etext" data-edit>肆</span></div>
            <div class="h"><span class="etext" data-edit>@may be you love me</span></div>
          </div>
          <div class="time"><span class="etext" data-edit>7小时前</span></div>
        </div>
        <div class="etext" data-edit>我要搓死了</div>
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
    </div>
    <div class="extras">
      <button class="exbtn" onclick="addTextEnd(this, 'panel-0')">＋ 末尾加文字</button>
      <button class="exbtn" onclick="addDividerEnd(this, 'panel-0')">— 末尾加分隔线</button>
    </div>
    <button class="add-card-btn" onclick="addCard(this, 'panel-0')">＋ 添加新卡片</button>
    <div class="pagenav">
      <div class="spacer30"></div>
      <button class="navbtn" onclick="switchTab('panel-1')">→</button>
    </div>
  </section>

  <section class="panel" id="panel-1">
    <div class="card">
      <div class="card-actions"><button onclick="deleteCard(this)">× 删除卡片</button></div>
      <div class="bwrap">
        <div class="etext" data-edit>这里是个人信息区域，可以在编辑模式下自由修改文字和图片。</div>
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
    </div>
    <div class="extras">
      <button class="exbtn" onclick="addTextEnd(this, 'panel-1')">＋ 末尾加文字</button>
      <button class="exbtn" onclick="addDividerEnd(this, 'panel-1')">— 末尾加分隔线</button>
    </div>
    <button class="add-card-btn" onclick="addCard(this, 'panel-1')">＋ 添加新卡片</button>
    <div class="pagenav">
      <button class="navbtn" onclick="switchTab('panel-0')">←</button>
      <button class="navbtn" onclick="switchTab('panel-2')">→</button>
    </div>
  </section>

  <section class="panel" id="panel-2">
    <div class="card">
      <div class="card-actions"><button onclick="deleteCard(this)">× 删除卡片</button></div>
      <div class="bwrap">
        <div class="etext" data-edit>这里是个人风格展示区域，可以放你的作品或爱好。</div>
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
    </div>
    <div class="extras">
      <button class="exbtn" onclick="addTextEnd(this, 'panel-2')">＋ 末尾加文字</button>
      <button class="exbtn" onclick="addDividerEnd(this, 'panel-2')">— 末尾加分隔线</button>
    </div>
    <button class="add-card-btn" onclick="addCard(this, 'panel-2')">＋ 添加新卡片</button>
    <div class="pagenav">
      <button class="navbtn" onclick="switchTab('panel-1')">←</button>
      <button class="navbtn" onclick="switchTab('panel-3')">→</button>
    </div>
  </section>

  <section class="panel" id="panel-3">
    <div class="card">
      <div class="card-actions"><button onclick="deleteCard(this)">× 删除卡片</button></div>
      <div class="bwrap">
        <div class="etext" data-edit>这里是分类区域，可以放你的收藏或分类信息。</div>
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
      <div class="bwrap" style="margin-top:8px;">
        <div class="etext linktext" data-edit data-link="panel-0">→ 点击返回 WELCOME</div>
        <div class="row-tools">
          <button title="加文字" onclick="addTextAfter(this)">＋</button>
          <button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button>
          <button class="del" onclick="deleteRow(this)">×</button>
        </div>
      </div>
    </div>
    <div class="extras">
      <button class="exbtn" onclick="addTextEnd(this, 'panel-3')">＋ 末尾加文字</button>
      <button class="exbtn" onclick="addDividerEnd(this, 'panel-3')">— 末尾加分隔线</button>
    </div>
    <button class="add-card-btn" onclick="addCard(this, 'panel-3')">＋ 添加新卡片</button>
    <div class="pagenav">
      <button class="navbtn" onclick="switchTab('panel-2')">←</button>
      <div class="spacer30"></div>
    </div>
  </section>
</div>

<div id="musicBtn">♪</div>

<div id="musicPanel">
  <div class="mp-controls">
    <button class="mp-ctrl-btn" id="mpPrev" title="上一首"><svg viewBox="0 0 24 24" fill="currentColor"><path d="M6 6h2v12H6zm3.5 6l8.5 6V6z"/></svg></button>
    <button class="mp-ctrl-btn play" id="mpPlay" title="播放/暂停"><svg viewBox="0 0 24 24" fill="currentColor"><path d="M8 5v14l11-7z"/></svg></button>
    <button class="mp-ctrl-btn" id="mpNext" title="下一首"><svg viewBox="0 0 24 24" fill="currentColor"><path d="M16 6h2v12h-2zM6 18l8.5-6L6 6z"/></svg></button>
  </div>
  <div class="mp-vol-container"><input type="range" id="mpVolume" min="0" max="100" value="100"></div>
  <button class="mp-add" id="mpAdd" title="添加音乐">＋</button>
</div>

<div id="shapePicker">
  <div class="pick-title">选择图形</div>
  <div class="pick" data-shape="circle"><svg viewBox="0 0 100 100"><circle cx="50" cy="50" r="45" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="square"><svg viewBox="0 0 100 100"><rect x="5" y="5" width="90" height="90" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="rounded"><svg viewBox="0 0 100 100"><rect x="5" y="5" width="90" height="90" rx="20" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="triangle"><svg viewBox="0 0 100 100"><polygon points="50,5 95,95 5,95" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="star"><svg viewBox="0 0 100 100"><polygon points="50,5 61,38 96,38 68,59 79,92 50,72 21,92 32,59 4,38 39,38" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="heart"><svg viewBox="0 0 100 100"><path d="M50,88 C20,65 5,45 5,30 C5,15 18,5 32,5 C41,5 48,10 50,18 C52,10 59,5 68,5 C82,5 95,15 95,30 C95,45 80,65 50,88 Z" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="arrow"><svg viewBox="0 0 100 100"><path d="M5,40 L60,40 L60,15 L95,50 L60,85 L60,60 L5,60 Z" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="diamond"><svg viewBox="0 0 100 100"><polygon points="50,5 95,50 50,95 5,50" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="hexagon"><svg viewBox="0 0 100 100"><polygon points="50,5 93,27 93,73 50,95 7,73 7,27" fill="currentColor"/></svg></div>
  <div class="pick" data-shape="cross"><svg viewBox="0 0 100 100"><path d="M35,5 L65,5 L65,35 L95,35 L95,65 L65,65 L65,95 L35,95 L35,65 L5,65 L5,35 L35,35 Z" fill="currentColor"/></svg></div>
</div>

<div id="framePicker" style="grid-template-columns:repeat(3,1fr);">
  <div class="pick-title">选择图片框形状</div>
  <div class="pick" data-frame="rounded"><svg viewBox="0 0 100 100"><rect x="8" y="20" width="84" height="60" rx="10" fill="none" stroke="currentColor" stroke-width="6"/></svg></div>
  <div class="pick" data-frame="square"><svg viewBox="0 0 100 100"><rect x="15" y="15" width="70" height="70" fill="none" stroke="currentColor" stroke-width="6"/></svg></div>
  <div class="pick" data-frame="circle"><svg viewBox="0 0 100 100"><circle cx="50" cy="50" r="35" fill="none" stroke="currentColor" stroke-width="6"/></svg></div>
</div>

<div id="picker">
  <div class="ph"><div class="ptitle">选择图片</div><button class="pclose" id="pickerClose">×</button></div>
  <div class="pgrid" id="pickerGrid"></div>
</div>

<div id="musicDialog">
  <div class="mcard">
    <h3>更换音乐</h3>
    <input type="text" id="musicInput" placeholder="music.mp3 或 https://.../song.mp3">
    <div class="row"><button id="musicCancel">取消</button><button class="primary" id="musicOk">确定</button></div>
  </div>
</div>

<div id="toast"></div>
<audio id="bgMusic" loop preload="none"></audio>

<script>
(function(){
  'use strict';

  // ==================== 1. 最高优先级：开屏兜底与双击逻辑修复 ====================
  (function() {
      var splash = document.getElementById('splash');
      if (!splash) return;
      
      var splashCard = document.getElementById('splashCard');
      var splashBtn = document.getElementById('splashEnterBtn');
      
      // 1.1 显示卡片
      setTimeout(function() {
          try {
              if (splashCard) splashCard.classList.add('show');
              if (splashBtn) splashBtn.classList.add('show');
          } catch(e) {}
      }, 100);

      // 1.2 强制进入主页函数
      function forceEnter() {
          // 如果处于编辑模式，点击空白不跳转，避免误触
          if (document.body.classList.contains('editing')) return;
          
          try {
              if (splash.parentNode) splash.parentNode.removeChild(splash);
          } catch(e) {
              try { splash.remove(); } catch(err) {}
          }
          setTimeout(function() {
              try {
                  if (typeof playMusic === 'function') { playMusic(); }
                  else if (typeof music !== 'undefined' && music.src) { music.play().catch(function(){}); }
              } catch(err) {}
          }, 100);
      }

      // 1.3 延迟点击计时器，用于区分单击跳转与双击编辑
      var clickTimer = null;

      // 1.4 双击编辑逻辑（直接绑定 dblclick，兼容移动端）
      var editEls = splash.querySelectorAll('[data-splash-edit]');
      for (var i = 0; i < editEls.length; i++) {
          (function(el) {
              el.addEventListener('dblclick', function(e) {
                  e.stopPropagation();
                  clearTimeout(clickTimer);
                  el.setAttribute('contenteditable', 'true');
                  el.focus();
                  var range = document.createRange(); range.selectNodeContents(el);
                  var sel = window.getSelection(); sel.removeAllRanges(); sel.addRange(range);
              });
              el.addEventListener('blur', function() { el.removeAttribute('contenteditable'); });
              el.addEventListener('keydown', function(e) { if (e.key === 'Enter') { e.preventDefault(); el.blur(); } });
          })(editEls[i]);
      }

      // 1.5 全局点击逻辑
      splash.addEventListener('click', function(e) {
          var target = e.target;
          
          // 如果点击的是正在编辑的文字，不处理
          if (target.getAttribute('contenteditable') === 'true') return;

          // 如果点击的是可编辑文字，不触发跳转（等待用户双击或编辑模式）
          if (target.hasAttribute('data-splash-edit')) {
              return;
          }

          // 如果点击的是“进入主页”按钮
          if (target.closest('#splashEnterBtn')) {
              clearTimeout(clickTimer);
              forceEnter();
              return;
          }

          // 如果不在编辑模式，点击空白处延迟 300ms 跳转
          if (!document.body.classList.contains('editing')) {
              clearTimeout(clickTimer);
              clickTimer = setTimeout(forceEnter, 300);
          }
      });

      // 1.6 按钮单击直接进入（作为双重保险）
      if (splashBtn) {
          splashBtn.addEventListener('click', function(e) {
              e.stopPropagation();
              clearTimeout(clickTimer);
              forceEnter();
          });
      }
  })();

  // ==================== 2. 全局变量定义 ====================
  var IMG_BASE = 'https://cdn.jsdelivr.net/gh/emil-si/My-page@main/';
  var IMAGES = ['IMG_6273.jpeg','IMG_6334.jpeg','IMG_6394.jpeg','IMG_6395.jpeg','IMG_6396.jpeg','IMG_7871.jpeg','IMG_7872.jpeg','IMG_7873.jpeg','IMG_7874.jpeg','IMG_8450.jpeg','IMG_8451.jpeg','IMG_8452.jpeg','IMG_8453.jpeg','IMG_8454.jpeg','IMG_8455.jpeg','IMG_8456.jpeg','IMG_9006.png','IMG_9007.png','IMG_9008.png','IMG_9009.png','IMG_9010.png','IMG_9011.png','IMG_9012.png','IMG_9013.png','IMG_9014.png','IMG_9016.png'];

  var isReadonly = false;
  try { var qp = new URLSearchParams(location.search); isReadonly = qp.has('view') || qp.has('readonly'); } catch(e){}
  if(isReadonly && document.body) document.body.classList.add('readonly');

  var K_THEME = 'myPage.theme.v5', K_BG = 'myPage.bg.v5', K_CONTENT = 'myPage.content.v6', K_MUSIC = 'myPage.music.v5', K_ALPHA = 'myPage.alpha.v2', K_SPLASH = 'myPage.splash.v1';
  
  var app = document.getElementById('app');
  var bgLayer = document.getElementById('bgLayer');
  var toastEl = document.getElementById('toast');
  
  var editMode = false, activeImg = null, bgMode = false, activeTextEl = null, activeFloater = null;
  var DEFAULT_HTML = app ? app.innerHTML : '';

  // ==================== 3. 核心工具函数 ====================
  function resolveTheme(){ var s = null; try { s = localStorage.getItem(K_THEME); } catch(e){} if(s === 'dark' || s === 'light') return s; try { if(window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) return 'dark'; } catch(e){} return 'light'; }
  function applyTheme(t){ document.documentElement.classList.toggle('light', t === 'light'); var icon = document.getElementById('themeIcon'); if(icon) icon.textContent = (t === 'dark') ? '☀' : '◐'; }
  try { applyTheme(resolveTheme()); } catch(e) {}
  
  function toast(msg){ if(!toastEl) return; toastEl.textContent = msg; toastEl.classList.add('show'); clearTimeout(window.__toastTimer); window.__toastTimer = setTimeout(function(){ toastEl.classList.remove('show'); }, 1400); }
  
  function applyAlpha(v){ var a = Math.max(0, Math.min(1, v / 100)); document.documentElement.style.setProperty('--card-alpha', a); var el = document.getElementById('spCardAlphaVal'); if(el) el.textContent = Math.round(v) + '%'; try { localStorage.setItem(K_ALPHA, v); } catch(e){} }
  try { var savedAlpha = localStorage.getItem(K_ALPHA); if(savedAlpha !== null){ applyAlpha(savedAlpha); var el = document.getElementById('spCardAlpha'); if(el) el.value = savedAlpha; } } catch(e){}

  function setBg(url){ if(bgLayer) { if(url) bgLayer.style.backgroundImage = 'url(' + url + ')'; else bgLayer.style.backgroundImage = 'none'; } }
  try { var savedBg = localStorage.getItem(K_BG); if(savedBg) setBg(savedBg); } catch(e){}

  // ==================== 4. 开屏内容恢复 ====================
  try {
      var savedSplash = localStorage.getItem(K_SPLASH);
      if (savedSplash) {
          var sData = JSON.parse(savedSplash);
          var splashTitle = document.querySelector('.splash-title');
          var splashDesc = document.querySelector('.splash-desc');
          if (splashTitle && sData.title) splashTitle.textContent = sData.title;
          if (splashDesc && sData.desc) splashDesc.textContent = sData.desc;
          if (sData.titleSize) {
              if (splashTitle) splashTitle.style.fontSize = sData.titleSize + 'px';
              var el = document.getElementById('spSplashTitleSize'); if(el) el.value = sData.titleSize;
              var v = document.getElementById('spSplashTitleSizeVal'); if(v) v.textContent = sData.titleSize + 'px';
          }
      }
  } catch(e) { console.log('开屏内容恢复失败:', e); }

  function saveSplash() {
      try {
          var splashTitle = document.querySelector('.splash-title');
          var splashDesc = document.querySelector('.splash-desc');
          var sizeInput = document.getElementById('spSplashTitleSize');
          var data = {
              title: splashTitle ? splashTitle.textContent : '',
              desc: splashDesc ? splashDesc.textContent : '',
              titleSize: sizeInput ? parseInt(sizeInput.value) : 48
          };
          localStorage.setItem(K_SPLASH, JSON.stringify(data));
      } catch(e) {}
  }

  // ==================== 5. 内容恢复（安全注入） ====================
  try {
      var savedContent = localStorage.getItem(K_CONTENT);
      if(savedContent && app) {
          var tempDiv = document.createElement('div');
          tempDiv.innerHTML = savedContent;
          var oldSplash = tempDiv.querySelector('#splash');
          if(oldSplash) oldSplash.remove();
          var savedApp = tempDiv.querySelector('#app');
          if(savedApp) {
              app.innerHTML = savedApp.innerHTML;
          } else {
              app.innerHTML = savedContent;
          }
      }
  } catch(e) { console.log('内容恢复失败:', e); }

  function serialize(){ if(!app) return ''; var clone = app.cloneNode(true); clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); }); return clone.innerHTML; }
  var saveTimer;
  function save(){ clearTimeout(saveTimer); saveTimer = setTimeout(function(){ if(!app) return; try { localStorage.setItem(K_CONTENT, serialize()); } catch(e){ alert('保存失败：浏览器存储空间已满'); } }, 300); }
  if (app) app.addEventListener('input', function(){ if(editMode) save(); });

  // ==================== 6. 编辑模式（同步开屏编辑） ====================
  function setEdit(on){
    if(isReadonly) return; 
    editMode = on; 
    document.body.classList.toggle('editing', on);
    
    // 主体内容的编辑状态
    document.querySelectorAll('#app [data-edit]').forEach(function(el){ 
        if(on){ el.setAttribute('contenteditable', 'true'); el.setAttribute('spellcheck', 'false'); } 
        else el.removeAttribute('contenteditable'); 
    });
    
    // 开屏文字的编辑状态
    var splashTitle = document.querySelector('.splash-title');
    var splashDesc = document.querySelector('.splash-desc');
    if (splashTitle) {
        if (on) { splashTitle.setAttribute('contenteditable', 'true'); splashTitle.setAttribute('spellcheck', 'false'); } 
        else { splashTitle.removeAttribute('contenteditable'); }
    }
    if (splashDesc) {
        if (on) { splashDesc.setAttribute('contenteditable', 'true'); splashDesc.setAttribute('spellcheck', 'false'); } 
        else { splashDesc.removeAttribute('contenteditable'); }
    }
    
    // 按钮状态
    var btn = document.getElementById('btnEdit'); 
    if(btn) { 
        btn.classList.toggle('on', on); 
        var ico = btn.querySelector('.ico'); if(ico) ico.textContent = on ? '✓' : '✎'; 
        var lbl = btn.querySelector('.lbl'); if(lbl) lbl.textContent = on ? '完成' : '编辑'; 
    }
    
    if(!on) { 
        var sp = document.getElementById('stylePanel'); if(sp) sp.classList.remove('show'); 
        saveSplash();
    }
    if(on) { 
        try { bindAll(); } catch(e){} 
        toast('编辑已开启'); 
    } else { 
        save(); 
        toast('已保存'); 
    }
    updateFloatersVisibility();
  }
  var btnEdit = document.getElementById('btnEdit');
  if (btnEdit) btnEdit.addEventListener('click', function(){ setEdit(!editMode); });

  // ==================== 7. 浮动元素页面隔离 ====================
  function updateFloatersVisibility() {
    var activePanel = document.querySelector('.panel.active');
    if (!activePanel) return;
    var activePanelId = activePanel.id;
    document.querySelectorAll('.shape, .frame, .floater').forEach(function(el) {
      if (!el.dataset.panelId) el.dataset.panelId = 'panel-0';
      if (el.dataset.panelId === activePanelId) {
        el.style.display = 'block'; el.style.pointerEvents = 'auto';
      } else {
        el.style.display = 'none'; el.style.pointerEvents = 'none';
      }
    });
  }

  // ==================== 8. 图片点击与替换 ====================
  var __imgPressStart = 0, __imgPressX = 0, __imgPressY = 0, __imgPressTarget = null, __lastImgOpen = 0;
  function __isEditableImg(el){ if(!el) return false; if(el.tagName === 'IMG' && el.hasAttribute('data-edit-img')) return true; if(el.tagName === 'IMG' && el.closest && el.closest('.frame')) return true; return false; }
  document.addEventListener('touchstart', function(e){ if(!editMode || isReadonly) return; var t = e.target; var img = t.tagName === 'IMG' ? t : (t.closest ? t.closest('img') : null); if(!__isEditableImg(img)) return; __imgPressTarget = img; __imgPressStart = Date.now(); var p = e.touches[0]; __imgPressX = p.clientX; __imgPressY = p.clientY; }, true);
  document.addEventListener('mousedown', function(e){ if(!editMode || isReadonly) return; if(e.button !== 0) return; var t = e.target; var img = t.tagName === 'IMG' ? t : (t.closest ? t.closest('img') : null); if(!__isEditableImg(img)) return; __imgPressTarget = img; __imgPressStart = Date.now(); __imgPressX = e.clientX; __imgPressY = e.clientY; }, true);
  document.addEventListener('touchmove', function(e){ if(!__imgPressTarget) return; var p = e.touches[0]; if(Math.abs(p.clientX - __imgPressX) > 15 || Math.abs(p.clientY - __imgPressY) > 15){ __imgPressTarget = null; } }, true);
  document.addEventListener('mousemove', function(e){ if(!__imgPressTarget) return; if(Math.abs(e.clientX - __imgPressX) > 15 || Math.abs(e.clientY - __imgPressY) > 15){ __imgPressTarget = null; } }, true);
  function __finishImgPress(upX, upY){ if(!__imgPressTarget) return; var target = __imgPressTarget; __imgPressTarget = null; if(!editMode || isReadonly) return; if(Date.now() - __imgPressStart > 900) return; var now = Date.now(); if(now - __lastImgOpen < 500) return; __lastImgOpen = now; openPicker(target, false); }
  document.addEventListener('touchend', function(e){ var p = e.changedTouches && e.changedTouches[0]; if(p) __finishImgPress(p.clientX, p.clientY); }, true);
  document.addEventListener('mouseup', function(e){ __finishImgPress(e.clientX, e.clientY); }, true);
  document.addEventListener('click', function(e){ if(!editMode || isReadonly) return; var t = e.target; var img = t.tagName === 'IMG' ? t : (t.closest ? t.closest('img') : null); if(!__isEditableImg(img)) return; if(__imgPressTarget) return; var now = Date.now(); if(now - __lastImgOpen < 500) return; __lastImgOpen = now; e.preventDefault(); openPicker(img, false); }, true);
  function bindImg(img){ /* 空壳 */ }

  var picker = document.getElementById('picker'), pickerGrid = document.getElementById('pickerGrid');
  function openPicker(img, isBg){
    if (!picker || !pickerGrid) return;
    activeImg = img || null; bgMode = !!isBg; pickerGrid.innerHTML = '';
    var observer = new IntersectionObserver(function(entries){ entries.forEach(function(entry){ if(entry.isIntersecting){ var el = entry.target; if(el.dataset.src){ el.src = el.dataset.src; el.removeAttribute('data-src'); } observer.unobserve(el); } }); }, { root: pickerGrid, rootMargin: '150px' });
    IMAGES.forEach(function(name){ var url = IMG_BASE + name; var el = document.createElement('img'); el.dataset.src = url; el.alt = name; el.style.background = 'rgba(127,127,127,0.15)'; el.addEventListener('click', function(e){ e.preventDefault(); e.stopPropagation(); if(bgMode){ setBg(url); try { localStorage.setItem(K_BG, url); } catch(e){} toast('背景已更新'); } else if(activeImg){ activeImg.src = url; save(); toast('图片已更新'); } closePicker(); }); pickerGrid.appendChild(el); observer.observe(el); });
    picker.classList.add('show');
  }
  function closePicker(){ if(picker) picker.classList.remove('show'); activeImg = null; bgMode = false; }
  var pickerClose = document.getElementById('pickerClose'); if(pickerClose) pickerClose.addEventListener('click', closePicker);
  var btnBg = document.getElementById('btnBg'); if(btnBg) btnBg.addEventListener('click', function(e){ e.preventDefault(); e.stopPropagation(); openPicker(null, true); });

  // ==================== 9. 增删行与卡片 ====================
  function makeTextRow(content){ var wrap = document.createElement('div'); wrap.className = 'bwrap'; wrap.style.marginTop = '8px'; wrap.innerHTML = '<div class="etext" data-edit>' + (content || '点击输入新内容...') + '</div><div class="row-tools"><button title="加文字" onclick="addTextAfter(this)">＋</button><button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button><button class="del" onclick="deleteRow(this)">×</button></div>'; return wrap; }
  function makeDividerRow(){ var wrap = document.createElement('div'); wrap.className = 'bwrap'; wrap.style.marginTop = '6px'; wrap.innerHTML = '<div class="divider"><div class="line"></div></div><div class="row-tools"><button title="加文字" onclick="addTextAfter(this)">＋</button><button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button><button class="del" onclick="deleteRow(this)">×</button></div>'; return wrap; }
  window.addTextAfter = function(btn){ var wrap = btn.closest('.bwrap'); if(!wrap) return; var newRow = makeTextRow(); wrap.parentNode.insertBefore(newRow, wrap.nextSibling); if(editMode){ var tb = newRow.querySelector('.etext'); tb.setAttribute('contenteditable', 'true'); tb.focus(); selectText(tb); } save(); toast('已添加文字'); };
  window.addDividerAfter = function(btn){ var wrap = btn.closest('.bwrap'); if(!wrap) return; var newRow = makeDividerRow(); wrap.parentNode.insertBefore(newRow, wrap.nextSibling); save(); toast('已添加分隔线'); };
  window.deleteRow = function(btn){ var wrap = btn.closest('.bwrap'); if(wrap && confirm('删除这一行？')){ wrap.remove(); save(); toast('已删除'); } };
  window.addTextEnd = function(btn, panelId){ var panel = document.getElementById(panelId); if(!panel) return; var card = panel.querySelector('.card'); if(!card) return; var newRow = makeTextRow(); card.appendChild(newRow); if(editMode){ var tb = newRow.querySelector('.etext'); tb.setAttribute('contenteditable', 'true'); tb.focus(); } save(); toast('已添加文字'); };
  window.addDividerEnd = function(btn, panelId){ var panel = document.getElementById(panelId); if(!panel) return; var card = panel.querySelector('.card'); if(!card) return; card.appendChild(makeDividerRow()); save(); toast('已添加分隔线'); };

  window.deleteCard = function(btn){ var card = btn.closest('.card'); if(card && confirm('确定删除这张卡片吗？')){ card.remove(); save(); toast('卡片已删除'); } };
  window.addCard = function(btn, panelId){
    var panel = document.getElementById(panelId); if(!panel) return;
    var newCard = document.createElement('div'); newCard.className = 'card';
    newCard.innerHTML = '<div class="card-actions"><button onclick="deleteCard(this)">× 删除卡片</button></div><div class="bwrap"><div class="etext" data-edit>新卡片内容...</div><div class="row-tools"><button title="加文字" onclick="addTextAfter(this)">＋</button><button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button><button class="del" onclick="deleteRow(this)">×</button></div></div>';
    panel.insertBefore(newCard, btn); if(editMode) newCard.querySelectorAll('[data-edit]').forEach(function(el){ el.setAttribute('contenteditable', 'true'); });
    save(); toast('已添加新卡片');
  };

  function refreshLinkOptions(){ var sel = document.getElementById('spLink'); if(!sel) return; var currentVal = sel.value; sel.innerHTML = '<option value="">无</option>'; document.querySelectorAll('#app .panel').forEach(function(p){ var opt = document.createElement('option'); opt.value = p.id; opt.textContent = p.dataset.name || p.id; sel.appendChild(opt); }); if(currentVal) sel.value = currentVal; }

  // ==================== 10. 样式面板与浮动元素编辑 ====================
  var stylePanel = document.getElementById('stylePanel');
  var btnStyle = document.getElementById('btnStyle'); if(btnStyle) btnStyle.addEventListener('click', function(e){ e.stopPropagation(); if(stylePanel) stylePanel.classList.toggle('show'); });

  function selectText(el){
    activeTextEl = el; activeFloater = null;
    var r1 = document.getElementById('spShapeColorRow'); if(r1) r1.style.display = 'none';
    var r2 = document.getElementById('spShapeOpacityRow'); if(r2) r2.style.display = 'none';
    var cs = getComputedStyle(el);
    var elSize = document.getElementById('spFontSize'); if(elSize){ elSize.value = parseInt(cs.fontSize) || 14; var v = document.getElementById('spFontSizeVal'); if(v) v.textContent = (parseInt(cs.fontSize) || 14) + 'px'; }
    var elColor = document.getElementById('spTextColor'); if(elColor) elColor.value = rgbToHex(cs.color);
  }
  function selectFloater(el) {
    activeFloater = el; activeTextEl = null;
    var r1 = document.getElementById('spShapeColorRow'); if(r1) r1.style.display = 'flex';
    var r2 = document.getElementById('spShapeOpacityRow'); if(r2) r2.style.display = 'flex';
    var currentColor = el.style.color || '#4a9eff'; if (currentColor.startsWith('rgb')) currentColor = rgbToHex(currentColor);
    var elColor = document.getElementById('spShapeColor'); if(elColor) elColor.value = currentColor;
    var currentOpacity = el.style.opacity !== '' ? Math.round(parseFloat(el.style.opacity) * 100) : 100;
    var elOp = document.getElementById('spShapeOpacity'); if(elOp) elOp.value = currentOpacity;
    var elOpVal = document.getElementById('spShapeOpacityVal'); if(elOpVal) elOpVal.textContent = currentOpacity + '%';
  }
  function rgbToHex(c){ if(!c) return '#f0f0f0'; var m = c.match(/\d+/g); if(!m) return '#f0f0f0'; return '#' + m.slice(0,3).map(function(x){ return ('0'+(+x).toString(16)).slice(-2); }).join(''); }

  var spFontSize = document.getElementById('spFontSize'); if(spFontSize) spFontSize.addEventListener('input', function(e){ if(!activeTextEl) return; activeTextEl.style.fontSize = e.target.value + 'px'; var v = document.getElementById('spFontSizeVal'); if(v) v.textContent = e.target.value + 'px'; save(); });
  var spTextColor = document.getElementById('spTextColor'); if(spTextColor) spTextColor.addEventListener('input', function(e){ if(!activeTextEl) return; activeTextEl.style.color = e.target.value; save(); });
  var spShapeColor = document.getElementById('spShapeColor'); if(spShapeColor) spShapeColor.addEventListener('input', function(e){ if(!activeFloater) return; activeFloater.style.color = e.target.value; save(); });
  var spShapeOpacity = document.getElementById('spShapeOpacity'); if(spShapeOpacity) spShapeOpacity.addEventListener('input', function(e){ if(!activeFloater) return; activeFloater.style.opacity = e.target.value / 100; var v = document.getElementById('spShapeOpacityVal'); if(v) v.textContent = e.target.value + '%'; save(); });

  // 开屏标题字号修改
  var spSplashTitleSize = document.getElementById('spSplashTitleSize');
  if (spSplashTitleSize) spSplashTitleSize.addEventListener('input', function(e) {
      var size = e.target.value;
      var splashTitle = document.querySelector('.splash-title');
      if (splashTitle) splashTitle.style.fontSize = size + 'px';
      var v = document.getElementById('spSplashTitleSizeVal');
      if (v) v.textContent = size + 'px';
      saveSplash();
  });

  document.addEventListener('focusin', function(e){ if(!editMode || isReadonly) return; var el = e.target.closest('#app [contenteditable="true"]'); if(el){ selectText(el); if(stylePanel) stylePanel.classList.add('show'); } });

  // ==================== 11. 图形创建与拖拽 ====================
  function shapeSVG(type){ var s = { circle:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><circle cx="50" cy="50" r="45" fill="currentColor"/></svg>', square:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" fill="currentColor"/></svg>', rounded:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" rx="20" fill="currentColor"/></svg>', triangle:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,95 5,95" fill="currentColor"/></svg>', star:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 61,38 96,38 68,59 79,92 50,72 21,92 32,59 4,38 39,38" fill="currentColor"/></svg>', heart:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M50,88 C20,65 5,45 5,30 C5,15 18,5 32,5 C41,5 48,10 50,18 C52,10 59,5 68,5 C82,5 95,15 95,30 C95,45 80,65 50,88 Z" fill="currentColor"/></svg>', arrow:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M5,40 L60,40 L60,15 L95,50 L60,85 L60,60 L5,60 Z" fill="currentColor"/></svg>', diamond:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,50 50,95 5,50" fill="currentColor"/></svg>', hexagon:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 93,27 93,73 50,95 7,73 7,27" fill="currentColor"/></svg>', cross:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M35,5 L65,5 L65,35 L95,35 L95,65 L65,65 L65,95 L35,95 L35,65 L5,65 L5,35 L35,35 Z" fill="currentColor"/></svg>' }; return s[type] || s.circle; }
  function getCenterPos(elWidth, elHeight) { var left = (window.innerWidth / 2) - (elWidth / 2) + window.scrollX; var top = (window.innerHeight / 2) - (elHeight / 2) + window.scrollY; return { left: left, top: top }; }
  function createFloatingElement(className, innerHTML, isContentEditable) {
      var el = document.createElement('div'); el.className = className; if (isContentEditable) el.contentEditable = 'true'; el.innerHTML = innerHTML;
      var pos = getCenterPos(100, 100); el.style.position = 'absolute'; el.style.left = pos.left + 'px'; el.style.top = pos.top + 'px'; el.style.zIndex = '999999'; el.style.display = 'block'; el.style.pointerEvents = 'auto';
      var activePanel = document.querySelector('.panel.active'); if (activePanel) el.dataset.panelId = activePanel.id;
      document.body.appendChild(el); return el;
  }
  function makeShape(type){ try { var el = createFloatingElement('shape', shapeSVG(type) + '<div class="rz"></div><button class="xdel">×</button>', false); el.style.width = '80px'; el.style.height = '80px'; el.style.color = getComputedStyle(document.documentElement).getPropertyValue('--accent').trim() || '#4a9eff'; el.addEventListener('click', function(e) { if(editMode && !e.target.classList.contains('rz') && !e.target.classList.contains('xdel')) { e.stopPropagation(); selectFloater(el); if(stylePanel) stylePanel.classList.add('show'); } }); makeMovable(el); bindXDel(el); bindResize(el); save(); toast('已添加图形'); } catch(e) { alert('添加图形失败: ' + e.message); } }
  function makeFrame(shape){ try { var imgHtml = '<img data-edit-img src="data:image/svg+xml,%3Csvg xmlns=\'http://www.w3.org/2000/svg\' viewBox=\'0 0 100 100\'%3E%3Crect width=\'100\' height=\'100\' fill=\'%23333\'/%3E%3Ctext x=\'50\' y=\'55\' font-size=\'12\' fill=\'%23999\' text-anchor=\'middle\' font-family=\'sans-serif\'%3E点击换图%3C/text%3E%3C/svg%3E" alt="图片">'; var el = createFloatingElement('frame', imgHtml + '<div class="rz"></div><button class="xdel">×</button>', false); el.style.width = '120px'; el.style.height = '120px'; if(shape === 'circle') el.style.borderRadius = '50%'; else if(shape === 'rounded') el.style.borderRadius = '16px'; var img = el.querySelector('img'); makeMovable(el); bindXDel(el); bindResize(el); bindImg(img); save(); toast('已添加图片框'); } catch(e) { alert('添加图片框失败: ' + e.message); } }
  function makeFloater(){ try { var el = createFloatingElement('floater', '自由文本<div class="rz"></div><button class="xdel">×</button>', true); el.style.width = '160px'; makeMovable(el); bindXDel(el); bindResize(el); setTimeout(function(){ el.focus(); }, 150); save(); toast('已添加浮动文本'); } catch(e) { alert('添加浮动文本失败: ' + e.message); } }

  var btnAddShape = document.getElementById('btnAddShape'); if (btnAddShape) btnAddShape.addEventListener('click', function(e){ e.stopPropagation(); var fp = document.getElementById('framePicker'); if(fp) fp.classList.remove('show'); var sp = document.getElementById('shapePicker'); if(sp) sp.classList.toggle('show'); });
  var btnAddFrame = document.getElementById('btnAddFrame'); if (btnAddFrame) btnAddFrame.addEventListener('click', function(e){ e.stopPropagation(); var sp = document.getElementById('shapePicker'); if(sp) sp.classList.remove('show'); var fp = document.getElementById('framePicker'); if(fp) fp.classList.toggle('show'); });
  var btnAddFloat = document.getElementById('btnAddFloat'); if (btnAddFloat) btnAddFloat.addEventListener('click', makeFloater);
  document.querySelectorAll('#shapePicker .pick').forEach(function(p){ p.addEventListener('click', function(){ makeShape(this.dataset.shape); var sp = document.getElementById('shapePicker'); if(sp) sp.classList.remove('show'); }); });
  document.querySelectorAll('#framePicker .pick').forEach(function(p){ p.addEventListener('click', function(){ makeFrame(this.dataset.frame); var fp = document.getElementById('framePicker'); if(fp) fp.classList.remove('show'); }); });

  function makeMovable(el){ var dragging = false, sx = 0, sy = 0, sl = 0, st = 0, moved = false; function down(e){ if(!editMode) return; if(e.target.classList.contains('rz')) return; if(e.target.classList.contains('xdel')) return; e.preventDefault(); dragging = true; moved = false; var p = e.touches ? e.touches[0] : e; sx = p.clientX; sy = p.clientY; sl = parseFloat(el.style.left) || 0; st = parseFloat(el.style.top) || 0; document.addEventListener('mousemove', move); document.addEventListener('touchmove', move, {passive:false}); document.addEventListener('mouseup', up); document.addEventListener('touchend', up); } function move(e){ if(!dragging) return; e.preventDefault(); var p = e.touches ? e.touches[0] : e; var dx = p.clientX - sx, dy = p.clientY - sy; if(Math.abs(dx) > 3 || Math.abs(dy) > 3) moved = true; el.style.left = (sl + dx) + 'px'; el.style.top = (st + dy) + 'px'; } function up(){ dragging = false; document.removeEventListener('mousemove', move); document.removeEventListener('touchmove', move); document.removeEventListener('mouseup', up); document.removeEventListener('touchend', up); if(moved) save(); } el.addEventListener('mousedown', down); el.addEventListener('touchstart', down, {passive:false}); }
  function bindResize(el){ var rz = el.querySelector('.rz'); if(!rz) return; var rs = false, sw = 0, sh = 0, sx = 0, sy = 0; function down(e){ e.preventDefault(); e.stopPropagation(); rs = true; var p = e.touches ? e.touches[0] : e; sx = p.clientX; sy = p.clientY; sw = el.offsetWidth; sh = el.offsetHeight; document.addEventListener('mousemove', move); document.addEventListener('touchmove', move, {passive:false}); document.addEventListener('mouseup', up); document.addEventListener('touchend', up); } function move(e){ if(!rs) return; e.preventDefault(); var p = e.touches ? e.touches[0] : e; el.style.width = Math.max(30, sw + (p.clientX - sx)) + 'px'; el.style.height = Math.max(30, sh + (p.clientY - sy)) + 'px'; } function up(){ rs = false; document.removeEventListener('mousemove', move); document.removeEventListener('touchmove', move); document.removeEventListener('mouseup', up); document.removeEventListener('touchend', up); save(); } rz.addEventListener('mousedown', down); rz.addEventListener('touchstart', down, {passive:false}); }
  function bindXDel(el){ var d = el.querySelector('.xdel'); if(!d) return; d.addEventListener('click', function(e){ e.stopPropagation(); if(confirm('确定删除吗？')){ el.remove(); save(); toast('已删除'); } }); }

  function initCardTools(){ document.querySelectorAll('#app .card').forEach(function(card){ if(!card.querySelector('.card-actions')){ var actions = document.createElement('div'); actions.className = 'card-actions'; actions.innerHTML = '<button onclick="deleteCard(this)">× 删除卡片</button>'; card.insertBefore(actions, card.firstChild); } }); }
  function bindAll(){ try { initCardTools(); } catch(e){} document.querySelectorAll('img[data-edit-img]').forEach(bindImg); document.querySelectorAll('.shape, .frame, .floater').forEach(function(el){ if(!el.dataset.moveBound){ el.dataset.moveBound = '1'; makeMovable(el); bindXDel(el); bindResize(el); var img = el.querySelector('img'); if(img) bindImg(img); } }); }

  // ==================== 12. 音乐播放器 ====================
  var music = document.getElementById('bgMusic'), musicBtn = document.getElementById('musicBtn'), musicPanel = document.getElementById('musicPanel'), mpPrev = document.getElementById('mpPrev'), mpPlay = document.getElementById('mpPlay'), mpNext = document.getElementById('mpNext'), mpVolume = document.getElementById('mpVolume'), mpAdd = document.getElementById('mpAdd');
  var playlist = [], currentIndex = 0, currentVolume = 1;
  try { var savedList = localStorage.getItem(K_MUSIC); if(savedList){ playlist = savedList.split('\n').filter(function(s){ return s.trim(); }); } var savedIdx = parseInt(localStorage.getItem('myPage.musicIndex') || '0'); if(!isNaN(savedIdx) && savedIdx >= 0 && savedIdx < playlist.length){ currentIndex = savedIdx; } var savedVol = localStorage.getItem('myPage.musicVolume'); if(savedVol !== null) currentVolume = parseFloat(savedVol); } catch(e){}
  if(music) music.volume = Math.max(0, Math.min(1, currentVolume)); if(mpVolume) mpVolume.value = Math.round(currentVolume * 100);
  function loadCurrent(){ if(!music) return; if(playlist.length === 0){ music.removeAttribute('src'); return; } music.src = playlist[currentIndex]; music.load(); }
  function saveMusic(){ try { localStorage.setItem(K_MUSIC, playlist.join('\n')); localStorage.setItem('myPage.musicIndex', currentIndex); } catch(e){} }
  function playMusic(){ if(!music) return; if(playlist.length === 0){ toast('请先添加音乐'); return; } if(!music.src) loadCurrent(); music.play().then(function(){ if(musicBtn) musicBtn.classList.add('playing'); if(mpPlay) mpPlay.innerHTML = '<svg viewBox="0 0 24 24" fill="currentColor" style="width:16px;height:16px;"><rect x="6" y="4" width="4" height="16" rx="1"></rect><rect x="14" y="4" width="4" height="16" rx="1"></rect></svg>'; }).catch(function(){ toast('播放失败'); }); }
  function pauseMusic(){ if(!music) return; music.pause(); if(musicBtn) musicBtn.classList.remove('playing'); if(mpPlay) mpPlay.innerHTML = '<svg viewBox="0 0 24 24" fill="currentColor" style="width:16px;height:16px;"><path d="M8 5v14l11-7z"/></svg>'; }
  function prevMusic(){ if(playlist.length === 0) return; var wasPlaying = !music.paused; currentIndex = (currentIndex - 1 + playlist.length) % playlist.length; loadCurrent(); saveMusic(); if(wasPlaying) setTimeout(playMusic, 100); }
  function nextMusic(){ if(playlist.length === 0) return; var wasPlaying = !music.paused; currentIndex = (currentIndex + 1) % playlist.length; loadCurrent(); saveMusic(); if(wasPlaying) setTimeout(playMusic, 100); }
  if(musicBtn) musicBtn.addEventListener('click', function(e){ e.stopPropagation(); if(musicPanel) musicPanel.classList.toggle('show'); });
  if(mpPlay) mpPlay.addEventListener('click', function(){ if(music && music.paused) playMusic(); else pauseMusic(); });
  if(mpPrev) mpPrev.addEventListener('click', prevMusic); if(mpNext) mpNext.addEventListener('click', nextMusic);
  if(mpVolume) mpVolume.addEventListener('input', function(){ currentVolume = mpVolume.value / 100; if(music) music.volume = currentVolume; try { localStorage.setItem('myPage.musicVolume', currentVolume); } catch(e){} });
  if(mpAdd) mpAdd.addEventListener('click', function(){ var url = prompt('输入音乐链接（以 .mp3 结尾）\n\n可以多次添加，之后用上一首/下一首切换', ''); if(url === null) return; url = url.trim(); if(!url) return; playlist.push(url); currentIndex = playlist.length - 1; loadCurrent(); saveMusic(); toast('已添加，点 ▶ 播放'); playMusic(); });
  var pressTimer = null, longPressed = false;
  function startPress(){ longPressed = false; clearTimeout(pressTimer); pressTimer = setTimeout(function(){ longPressed = true; var cur = playlist[currentIndex] || ''; var url = prompt('修改当前音乐链接：\n（留空 = 删除这首）', cur); if(url === null) return; url = url.trim(); if(!url){ playlist.splice(currentIndex, 1); if(currentIndex >= playlist.length) currentIndex = Math.max(0, playlist.length - 1); loadCurrent(); } else { playlist[currentIndex] = url; loadCurrent(); } saveMusic(); toast('已更新'); }, 700); }
  function cancelPress(){ clearTimeout(pressTimer); }
  if(musicBtn){ musicBtn.addEventListener('mousedown', function(e){ e.preventDefault(); startPress(); }); musicBtn.addEventListener('mouseup', function(e){ cancelPress(); if(!longPressed){ e.preventDefault(); if(musicPanel) musicPanel.classList.toggle('show'); } }); musicBtn.addEventListener('mouseleave', cancelPress); musicBtn.addEventListener('touchstart', startPress, {passive:true}); musicBtn.addEventListener('touchend', function(e){ cancelPress(); if(!longPressed){ e.preventDefault(); if(musicPanel) musicPanel.classList.toggle('show'); } }); musicBtn.addEventListener('touchcancel', cancelPress); musicBtn.addEventListener('contextmenu', function(e){ e.preventDefault(); }); }
  document.addEventListener('click', function(e){ if(musicPanel && musicPanel.classList.contains('show') && !musicPanel.contains(e.target) && !musicBtn.contains(e.target)){ musicPanel.classList.remove('show'); } });
  loadCurrent();

  // ==================== 13. 页面切换与集合（恢复链接跳转） ====================
  var navHistory = [];
  window.switchTab = function(id){
    var cur = document.querySelector('.panel.active'); if(cur && cur.id !== id){ navHistory.push(cur.id); if(navHistory.length > 30) navHistory.shift(); }
    document.querySelectorAll('.panel').forEach(function(p){ p.classList.toggle('active', p.id === id); });
    document.querySelectorAll('.navlink').forEach(function(n){ if(n.getAttribute('data-panel')) n.classList.toggle('active', n.getAttribute('data-panel') === id); });
    refreshCollectionActive(); window.scrollTo(0, 0); setTimeout(playPageEffects, 150);
    updateFloatersVisibility();
  };
  document.querySelectorAll('.navlink[data-panel]').forEach(function(n){ n.addEventListener('click', function(){ window.switchTab(this.getAttribute('data-panel')); }); });

  // 恢复链接跳转功能（非编辑模式下）
  document.addEventListener('click', function(e){
      if (editMode) return;
      var el = e.target.closest('#app [data-link]');
      if (el && el.getAttribute('data-link')) {
          var t = el.getAttribute('data-link');
          if (document.getElementById(t)) {
              e.preventDefault();
              window.switchTab(t);
          }
      }
  });

  var navCollection = document.getElementById('navCollection'), collectionMenu = document.getElementById('collectionMenu'), collectionList = document.getElementById('collectionList');
  function refreshCollectionMenu(){ if(!collectionList) return; collectionList.innerHTML = ''; var items = document.querySelectorAll('#app .panel[data-collection="true"]'); if(items.length === 0){ collectionList.innerHTML = '<div class="cempty">暂无页面</div>'; return; } items.forEach(function(p){ var d = document.createElement('div'); d.className = 'citem'; d.setAttribute('data-panel', p.id); d.textContent = p.dataset.name || p.id; d.addEventListener('click', function(){ window.switchTab(p.id); if(collectionMenu) collectionMenu.classList.remove('show'); }); collectionList.appendChild(d); }); refreshCollectionActive(); }
  function refreshCollectionActive(){ var cur = document.querySelector('.panel.active'); if(!cur || !navCollection) return; var isC = cur.getAttribute('data-collection') === 'true'; navCollection.classList.toggle('active', isC); if(collectionList) collectionList.querySelectorAll('.citem').forEach(function(c){ c.classList.toggle('active', c.getAttribute('data-panel') === cur.id); }); }
  if(navCollection) navCollection.addEventListener('click', function(e){ e.stopPropagation(); if(collectionMenu.classList.contains('show')) collectionMenu.classList.remove('show'); else { refreshCollectionMenu(); collectionMenu.classList.add('show'); } });
  document.addEventListener('click', function(e){ if(collectionMenu && collectionMenu.classList.contains('show') && !collectionMenu.contains(e.target) && !navCollection.contains(e.target)){ collectionMenu.classList.remove('show'); } var sp = document.getElementById('stylePanel'); var spBtn = document.getElementById('btnStyle'); if(sp && sp.classList.contains('show') && !sp.contains(e.target) && !spBtn.contains(e.target) && !e.target.closest('[contenteditable="true"]')){ sp.classList.remove('show'); } });

  var btnAddPanel = document.getElementById('btnAddPanel'); if (btnAddPanel) btnAddPanel.addEventListener('click', function(){ var name = prompt('新页面名称（会加入合集）:', '新页面'); if(!name) return; name = name.trim(); if(!name) return; var panels = document.querySelectorAll('#app .panel'); var used = {}; panels.forEach(function(p){ used[p.id] = true; }); var idx = 0; while(used['panel-' + idx]) idx++; var newId = 'panel-' + idx; var sec = document.createElement('section'); sec.className = 'panel'; sec.id = newId; sec.setAttribute('data-collection', 'true'); sec.dataset.name = name; sec.innerHTML = '<div class="card"><div class="card-actions"><button onclick="deleteCard(this)">× 删除卡片</button></div><div class="bwrap"><div class="etext" data-edit>新页面内容...</div><div class="row-tools"><button title="加文字" onclick="addTextAfter(this)">＋</button><button class="dvd" title="加分隔线" onclick="addDividerAfter(this)">—</button><button class="del" onclick="deleteRow(this)">×</button></div></div></div><div class="extras"><button class="exbtn" onclick="addTextEnd(this, \'' + newId + '\')">＋ 末尾加文字</button><button class="exbtn" onclick="addDividerEnd(this, \'' + newId + '\')">— 末尾加分隔线</button></div><button class="add-card-btn" onclick="addCard(this, \'' + newId + '\')">＋ 添加新卡片</button><div class="pagenav"><button class="navbtn" onclick="window.goBack()">←</button><div class="spacer30"></div></div>'; app.appendChild(sec); if(editMode){ sec.querySelectorAll('[data-edit]').forEach(function(el){ el.setAttribute('contenteditable', 'true'); }); } refreshCollectionMenu(); refreshLinkOptions(); save(); toast('已添加：' + name); });
  window.goBack = function(){ var prev = navHistory.pop(); if(prev) window.switchTab(prev); else window.switchTab('panel-0'); };

  // ==================== 14. 重置、导出、导入 ====================
  var btnReset = document.getElementById('btnReset'); if (btnReset) btnReset.addEventListener('click', function(){ if(!confirm('确定恢复初始内容吗？所有修改将丢失！')) return; if(app) app.innerHTML = DEFAULT_HTML; bindAll(); setEdit(false); save(); refreshCollectionMenu(); refreshLinkOptions(); toast('已重置'); });
  var btnExport = document.getElementById('btnExport'); if (btnExport) btnExport.addEventListener('click', function(){ save(); saveSplash(); var clone = document.documentElement.cloneNode(true); clone.querySelector('body').classList.remove('editing'); clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); }); clone.querySelectorAll('.style-panel').forEach(function(el){ el.classList.remove('show'); }); clone.querySelectorAll('#picker').forEach(function(el){ el.classList.remove('show'); }); clone.querySelectorAll('#collectionMenu').forEach(function(el){ el.classList.remove('show'); }); clone.querySelectorAll('#shapePicker, #framePicker').forEach(function(el){ el.classList.remove('show'); }); clone.querySelectorAll('#musicDialog').forEach(function(el){ el.classList.remove('show'); }); clone.querySelectorAll('.panel').forEach(function(p, i){ p.classList.toggle('active', i === 0); }); clone.querySelectorAll('.navlink[data-panel]').forEach(function(n, i){ n.classList.toggle('active', i === 0); }); var currentContent = serialize(), currentBg = '', currentAlpha = '', currentTheme = '', currentMusic = '', currentSplash = ''; try { currentBg = localStorage.getItem(K_BG) || ''; currentAlpha = localStorage.getItem(K_ALPHA) || '55'; currentTheme = localStorage.getItem(K_THEME) || 'light'; currentMusic = localStorage.getItem(K_MUSIC) || ''; currentSplash = localStorage.getItem(K_SPLASH) || ''; } catch(e){} var injectScript = document.createElement('script'); injectScript.textContent = '(function(){try{var _c=' + JSON.stringify(currentContent) + ';var _bg=' + JSON.stringify(currentBg) + ';var _a=' + JSON.stringify(currentAlpha) + ';var _t=' + JSON.stringify(currentTheme) + ';var _m=' + JSON.stringify(currentMusic) + ';var _s=' + JSON.stringify(currentSplash) + ';localStorage.setItem("myPage.content.v6",_c);if(_bg)localStorage.setItem("myPage.bg.v5",_bg);if(_a)localStorage.setItem("myPage.alpha.v2",_a);if(_t)localStorage.setItem("myPage.theme.v5",_t);if(_m)localStorage.setItem("myPage.music.v5",_m);if(_s)localStorage.setItem("myPage.splash.v1",_s);}catch(e){}})();'; clone.querySelector('head').insertBefore(injectScript, clone.querySelector('head').firstChild); var html = '<!DOCTYPE html>\n' + clone.outerHTML; var d = new Date(); var pad = function(n){ return String(n).padStart(2, '0'); }; var stamp = d.getFullYear() + '-' + pad(d.getMonth()+1) + '-' + pad(d.getDate()) + '-' + pad(d.getHours()) + '-' + pad(d.getMinutes()) + '-' + pad(d.getSeconds()); var fileName = 'p' + stamp.replace(/[-:]/g,'').slice(2) + '.html'; var blob = new Blob([html], {type:'text/html;charset=utf-8'}); var a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = fileName; document.body.appendChild(a); a.click(); document.body.removeChild(a); setTimeout(function(){ URL.revokeObjectURL(a.href); }, 2000); toast('已导出 ' + fileName); setTimeout(function(){ alert('已导出：' + fileName + '\n\n下一步：\n1. 打开 GitHub 仓库\n2. Add file → Upload files\n3. 上传这个文件\n4. 分享链接：\n   https://emil-si.github.io/My-page/' + fileName); }, 400); });
  var btnImport = document.getElementById('btnImport'); if (btnImport) btnImport.addEventListener('click', function(){ var inp = document.createElement('input'); inp.type = 'file'; inp.accept = '.html,.htm,text/html'; inp.onchange = function(){ var f = inp.files && inp.files[0]; if(!f) return; var reader = new FileReader(); reader.onload = function(ev){ try { var doc = new DOMParser().parseFromString(ev.target.result, 'text/html'); var imported = doc.getElementById('app'); if(!imported) throw new Error('no app'); if(app) app.innerHTML = imported.innerHTML; bindAll(); setEdit(false); save(); refreshCollectionMenu(); refreshLinkOptions(); toast('导入成功'); } catch(err){ alert('导入失败：文件里没有识别到内容'); } }; reader.readAsText(f); }; inp.click(); });

  // ==================== 15. 文字特效初始化 ====================
  function playPageEffects() {
    var currentPanel = document.querySelector('.panel.active'); if (!currentPanel) return;
    var elements = currentPanel.querySelectorAll('[data-edit][class*="fx-"]');
    var sorted = Array.from(elements).sort(function(a, b) { return parseInt(a.dataset.fxOrder || '0') - parseInt(b.dataset.fxOrder || '0'); });
    if (window.__fxTimers) { window.__fxTimers.forEach(function(t) { clearTimeout(t); }); }
    window.__fxTimers = [];
    sorted.forEach(function(el, index) { var delay = index * 400; var timer = setTimeout(function() { el.classList.remove('fx-hidden'); el.classList.add('fx-visible'); }, 300 + delay); window.__fxTimers.push(timer); });
  }

 // ==================== 重新打开开屏（供编辑） ====================
  var btnOpenSplash = document.getElementById('btnOpenSplash');
  if (btnOpenSplash) btnOpenSplash.addEventListener('click', function() {
      // 如果已经存在，直接返回
      if (document.getElementById('splash')) { toast('开屏已经打开'); return; }
      
      // 读取已保存的开屏数据
      var sData = { title: '肆', desc: '双击此文字可修改', titleSize: 48 };
      try {
          var savedSplash = localStorage.getItem(K_SPLASH);
          if (savedSplash) {
              var parsed = JSON.parse(savedSplash);
              if (parsed.title) sData.title = parsed.title;
              if (parsed.desc) sData.desc = parsed.desc;
              if (parsed.titleSize) sData.titleSize = parsed.titleSize;
          }
      } catch(e) {}
      
      // 动态创建开屏层
      var splash = document.createElement('div');
      splash.id = 'splash';
      splash.innerHTML = '<div class="splash-card" id="splashCard">' +
          '<div class="splash-title" data-splash-edit="title">' + sData.title + '</div>' +
          '<div class="splash-desc" data-splash-edit="desc">' + sData.desc + '</div>' +
          '</div>' +
          '<button class="splash-enter-btn" id="splashEnterBtn">' +
          '<span class="splash-btn-text">进入主页</span>' +
          '</button>';
      
      document.body.appendChild(splash);
      
      // 应用标题字号
      var titleEl = splash.querySelector('.splash-title');
      if (titleEl) titleEl.style.fontSize = sData.titleSize + 'px';
      
      // 同步样式面板滑块
      var sizeInput = document.getElementById('spSplashTitleSize');
      if (sizeInput) sizeInput.value = sData.titleSize;
      var sizeVal = document.getElementById('spSplashTitleSizeVal');
      if (sizeVal) sizeVal.textContent = sData.titleSize + 'px';
      
      // 显示卡片
      setTimeout(function() {
          var card = document.getElementById('splashCard');
          var btn = document.getElementById('splashEnterBtn');
          if (card) card.classList.add('show');
          if (btn) btn.classList.add('show');
      }, 50);
      
      // 绑定双击编辑（编辑模式下）
      var editEls = splash.querySelectorAll('[data-splash-edit]');
      for (var i = 0; i < editEls.length; i++) {
          (function(el) {
              el.addEventListener('dblclick', function(e) {
                  e.stopPropagation();
                  el.setAttribute('contenteditable', 'true');
                  el.focus();
                  var range = document.createRange(); range.selectNodeContents(el);
                  var sel = window.getSelection(); sel.removeAllRanges(); sel.addRange(range);
              });
              el.addEventListener('blur', function() { el.removeAttribute('contenteditable'); saveSplash(); });
              el.addEventListener('keydown', function(e) { if (e.key === 'Enter') { e.preventDefault(); el.blur(); } });
          })(editEls[i]);
      }
      
      // 绑定关闭逻辑
      splash.addEventListener('click', function(e) {
          var target = e.target;
          if (target.getAttribute('contenteditable') === 'true') return;
          if (target.hasAttribute('data-splash-edit')) return;
          if (target.closest('#splashEnterBtn')) {
              if (splash.parentNode) splash.parentNode.removeChild(splash);
              saveSplash();
              return;
          }
          // 编辑模式下点击空白不关闭
          if (document.body.classList.contains('editing')) return;
          if (splash.parentNode) splash.parentNode.removeChild(splash);
          saveSplash();
      });
      
      toast('已打开开屏，可直接编辑');
  });
   // ==================== 16. 初始化 ====================
  try { bindAll(); } catch(e) { console.log('bindAll 出错:', e); }
  try { refreshCollectionMenu(); } catch(e) {}
  try { refreshLinkOptions(); } catch(e) {}
  try { setEdit(false); } catch(e) {}
  setTimeout(function(){ try { updateFloatersVisibility(); } catch(e) {} }, 100);
// ==================== 16. 开屏浮动元素编辑 ====================
  var splashActiveFloater = null;  // 当前选中的开屏浮动元素

  function getSplashCard() {
      return document.querySelector('#splash .splash-card');
  }

  // 添加开屏浮动文本
  function addSplashFloater() {
      var card = getSplashCard();
      if (!card) { toast('请先打开开屏'); return; }
      
      var el = document.createElement('div');
      el.className = 'splash-floater';
      el.contentEditable = 'true';
      el.textContent = '开屏文字';
      el.innerHTML += '<button class="splash-fdel">×</button>';
      
      el.style.left = '50%';
      el.style.top = '50%';
      el.style.transform = 'translate(-50%, -50%)';
      el.style.fontSize = '16px';
      
      card.appendChild(el);
      
      // 删除按钮
      var delBtn = el.querySelector('.splash-fdel');
      delBtn.addEventListener('click', function(e) {
          e.stopPropagation();
          if (confirm('删除这个开屏浮动文本？')) {
              el.remove();
              saveSplashFloaters();
          }
      });
      
      // 拖动
      makeSplashFloaterMovable(el);
      
      // 选中时同步到样式面板
      el.addEventListener('focusin', function() {
          splashActiveFloater = el;
          var cs = getComputedStyle(el);
          var fontSize = parseInt(cs.fontSize) || 16;
          var sizeInput = document.getElementById('splashFontSize');
          if (sizeInput) sizeInput.value = fontSize;
      });
      
      el.addEventListener('click', function(e) {
          if (!editMode) return;
          e.stopPropagation();
          splashActiveFloater = el;
          var cs = getComputedStyle(el);
          var sizeInput = document.getElementById('splashFontSize');
          if (sizeInput) sizeInput.value = parseInt(cs.fontSize) || 16;
      });
      
      el.focus();
      selectSplashText(el);
      saveSplashFloaters();
      toast('已添加开屏浮动文本');
  }

  // 选中开屏元素，同步样式面板
  function selectSplashText(el) {
      splashActiveFloater = el;
      var cs = getComputedStyle(el);
      var sizeInput = document.getElementById('splashFontSize');
      if (sizeInput) sizeInput.value = parseInt(cs.fontSize) || 14;
  }

  // 拖动逻辑
  function makeSplashFloaterMovable(el) {
      var dragging = false, sx = 0, sy = 0, sl = 0, st = 0, moved = false;
      function down(e) {
          if (!editMode) return;
          if (e.target.classList.contains('splash-fdel')) return;
          e.preventDefault();
          dragging = true; moved = false;
          
          // 去掉 transform，改用绝对定位
          var rect = el.getBoundingClientRect();
          var parentRect = el.parentNode.getBoundingClientRect();
          el.style.transform = 'none';
          el.style.left = (rect.left - parentRect.left) + 'px';
          el.style.top = (rect.top - parentRect.top) + 'px';
          
          var p = e.touches ? e.touches[0] : e;
          sx = p.clientX; sy = p.clientY;
          sl = parseFloat(el.style.left) || 0;
          st = parseFloat(el.style.top) || 0;
          
          document.addEventListener('mousemove', move);
          document.addEventListener('touchmove', move, {passive:false});
          document.addEventListener('mouseup', up);
          document.addEventListener('touchend', up);
      }
      function move(e) {
          if (!dragging) return;
          e.preventDefault();
          var p = e.touches ? e.touches[0] : e;
          var dx = p.clientX - sx, dy = p.clientY - sy;
          if (Math.abs(dx) > 3 || Math.abs(dy) > 3) moved = true;
          el.style.left = (sl + dx) + 'px';
          el.style.top = (st + dy) + 'px';
      }
      function up() {
          dragging = false;
          document.removeEventListener('mousemove', move);
          document.removeEventListener('touchmove', move);
          document.removeEventListener('mouseup', up);
          document.removeEventListener('touchend', up);
          if (moved) saveSplashFloaters();
      }
      el.addEventListener('mousedown', down);
      el.addEventListener('touchstart', down, {passive:false});
  }

  // 保存开屏浮动元素
  function saveSplashFloaters() {
      try {
          var card = getSplashCard();
          if (!card) return;
          var floaters = card.querySelectorAll('.splash-floater');
          var data = [];
          floaters.forEach(function(el) {
              data.push({
                  text: el.textContent.replace('×','').trim(),
                  left: el.style.left,
                  top: el.style.top,
                  fontSize: el.style.fontSize,
                  opacity: el.style.opacity,
                  color: el.style.color
              });
          });
          localStorage.setItem('myPage.splashFloaters.v1', JSON.stringify(data));
      } catch(e) {}
  }

  // 恢复开屏浮动元素
  function restoreSplashFloaters() {
      try {
          var data = localStorage.getItem('myPage.splashFloaters.v1');
          if (!data) return;
          var arr = JSON.parse(data);
          var card = getSplashCard();
          if (!card) return;
          arr.forEach(function(item) {
              var el = document.createElement('div');
              el.className = 'splash-floater';
              el.contentEditable = 'true';
              el.textContent = item.text || '开屏文字';
              el.innerHTML += '<button class="splash-fdel">×</button>';
              el.style.left = item.left || '50%';
              el.style.top = item.top || '50%';
              el.style.fontSize = item.fontSize || '16px';
              if (item.opacity) el.style.opacity = item.opacity;
              if (item.color) el.style.color = item.color;
              card.appendChild(el);
              
              var delBtn = el.querySelector('.splash-fdel');
              delBtn.addEventListener('click', function(e) {
                  e.stopPropagation();
                  if (confirm('删除这个开屏浮动文本？')) {
                      el.remove();
                      saveSplashFloaters();
                  }
              });
              
              makeSplashFloaterMovable(el);
              el.addEventListener('focusin', function() { selectSplashText(el); });
              el.addEventListener('click', function(e) {
                  if (!editMode) return;
                  e.stopPropagation();
                  selectSplashText(el);
              });
          });
      } catch(e) { console.log('恢复开屏浮动元素失败:', e); }
  }

  // 打开/关闭开屏编辑
  var btnOpenSplash = document.getElementById('btnOpenSplash');
  var splashToolbar = document.getElementById('splashToolbar');
  
  function openSplashEditor() {
      // 如果已经存在，直接返回
      if (document.getElementById('splash')) { toast('开屏已经打开'); return; }
      
      // 读取已保存的开屏数据
      var sData = { title: '肆', desc: '双击此文字可修改', titleSize: 48 };
      try {
          var savedSplash = localStorage.getItem(K_SPLASH);
          if (savedSplash) {
              var parsed = JSON.parse(savedSplash);
              if (parsed.title) sData.title = parsed.title;
              if (parsed.desc) sData.desc = parsed.desc;
              if (parsed.titleSize) sData.titleSize = parsed.titleSize;
          }
      } catch(e) {}
      
      // 动态创建开屏层
      var splash = document.createElement('div');
      splash.id = 'splash';
      splash.innerHTML = '<div class="splash-card" id="splashCard">' +
          '<div class="splash-title" data-splash-edit="title">' + sData.title + '</div>' +
          '<div class="splash-desc" data-splash-edit="desc">' + sData.desc + '</div>' +
          '</div>' +
          '<button class="splash-enter-btn" id="splashEnterBtn">' +
          '<span class="splash-btn-text">进入主页</span>' +
          '</button>';
      
      document.body.appendChild(splash);
      
      // 应用标题字号
      var titleEl = splash.querySelector('.splash-title');
      if (titleEl) titleEl.style.fontSize = sData.titleSize + 'px';
      
      // 同步样式面板滑块
      var sizeInput = document.getElementById('spSplashTitleSize');
      if (sizeInput) sizeInput.value = sData.titleSize;
      var sizeVal = document.getElementById('spSplashTitleSizeVal');
      if (sizeVal) sizeVal.textContent = sData.titleSize + 'px';
      
      // 显示卡片
      setTimeout(function() {
          var card = document.getElementById('splashCard');
          var btn = document.getElementById('splashEnterBtn');
          if (card) card.classList.add('show');
          if (btn) btn.classList.add('show');
      }, 50);
      
      // 绑定双击编辑（编辑模式下）
      var editEls = splash.querySelectorAll('[data-splash-edit]');
      for (var i = 0; i < editEls.length; i++) {
          (function(el) {
              el.addEventListener('dblclick', function(e) {
                  e.stopPropagation();
                  el.setAttribute('contenteditable', 'true');
                  el.focus();
                  selectSplashText(el);
                  var range = document.createRange(); range.selectNodeContents(el);
                  var sel = window.getSelection(); sel.removeAllRanges(); sel.addRange(range);
              });
              el.addEventListener('focusin', function() { selectSplashText(el); });
              el.addEventListener('click', function(e) {
                  if (!editMode) return;
                  e.stopPropagation();
                  selectSplashText(el);
              });
              el.addEventListener('blur', function() { el.removeAttribute('contenteditable'); saveSplash(); });
              el.addEventListener('keydown', function(e) { if (e.key === 'Enter') { e.preventDefault(); el.blur(); } });
          })(editEls[i]);
      }
      
      // 绑定关闭逻辑
      splash.addEventListener('click', function(e) {
          var target = e.target;
          if (target.getAttribute('contenteditable') === 'true') return;
          if (target.hasAttribute('data-splash-edit')) return;
          if (target.classList.contains('splash-floater')) return;
          if (target.closest('.splash-floater')) return;
          if (target.closest('#splashEnterBtn')) {
              if (splash.parentNode) splash.parentNode.removeChild(splash);
              if (splashToolbar) splashToolbar.classList.remove('show');
              saveSplash();
              saveSplashFloaters();
              return;
          }
          if (document.body.classList.contains('editing')) return;
          if (splash.parentNode) splash.parentNode.removeChild(splash);
          saveSplash();
          saveSplashFloaters();
      });
      
      // 打开开屏工具栏
      if (splashToolbar) splashToolbar.classList.add('show');
      
      // 恢复已保存的开屏浮动元素
      setTimeout(restoreSplashFloaters, 100);
      
      toast('已打开开屏编辑');
  }
  
  if (btnOpenSplash) btnOpenSplash.addEventListener('click', openSplashEditor);
  
  // 开屏工具栏按钮
  var btnSplashAddFloat = document.getElementById('btnSplashAddFloat');
  if (btnSplashAddFloat) btnSplashAddFloat.addEventListener('click', addSplashFloater);
  
  var btnSplashClose = document.getElementById('btnSplashClose');
  if (btnSplashClose) btnSplashClose.addEventListener('click', function() {
      var splash = document.getElementById('splash');
      if (splash) {
          if (splash.parentNode) splash.parentNode.removeChild(splash);
      }
      if (splashToolbar) splashToolbar.classList.remove('show');
      saveSplash();
      saveSplashFloaters();
      toast('已保存开屏');
  });
  
  var splashFontSize = document.getElementById('splashFontSize');
  if (splashFontSize) splashFontSize.addEventListener('input', function(e) {
      var size = e.target.value + 'px';
      // 优先应用到当前选中的开屏浮动元素
      if (splashActiveFloater) {
          splashActiveFloater.style.fontSize = size;
          saveSplashFloaters();
      } else {
          // 否则应用到开屏标题
          var splashTitle = document.querySelector('#splash .splash-title');
          if (splashTitle) {
              splashTitle.style.fontSize = size;
              saveSplash();
          }
      }
  });
  
  var splashFxType = document.getElementById('splashFxType');
  if (splashFxType) splashFxType.addEventListener('change', function(e) {
      var val = e.target.value;
      if (!splashActiveFloater) {
          toast('请先选中一个开屏文字');
          return;
      }
      splashActiveFloater.classList.remove('fx-slide-up', 'fx-fade-in', 'fx-scale-in', 'fx-visible', 'fx-hidden');
      if (val !== 'none') {
          splashActiveFloater.classList.add(val);
          splashActiveFloater.classList.add('fx-hidden');
          // 立即演示
          setTimeout(function() {
              splashActiveFloater.classList.remove('fx-hidden');
              splashActiveFloater.classList.add('fx-visible');
          }, 200);
      }
      saveSplashFloaters();
      toast(val === 'none' ? '已取消特效' : '已应用特效');
  });

  // 关闭编辑模式时，自动关闭开屏编辑
  var _originalSetEdit = setEdit;
  window.setEdit = function(on) {
      _originalSetEdit(on);
      if (!on) {
          var splash = document.getElementById('splash');
          if (splash && splashToolbar && splashToolbar.classList.contains('show')) {
              // 如果开屏是编辑状态下打开的，退出编辑模式时保留开屏，但隐藏工具栏
              if (splashToolbar) splashToolbar.classList.remove('show');
          }
      }
  };
})();

// ============================================================
// ========== 开屏编辑器（独立模块，无需修改原有代码）==========
// ============================================================
(function(){
  'use strict';
  
  var TOOLBAR_ID = 'splashToolbar';
  var STYLE_ID = 'splash-toolbar-style';
  var FLOATERS_KEY = 'myPage.splashFloaters.v2';
  var activeEl = null;
  
  function injectStyle() {
    if (document.getElementById(STYLE_ID)) return;
    var style = document.createElement('style');
    style.id = STYLE_ID;
    style.textContent = 
      '#splashToolbar{position:fixed;top:calc(48px + var(--safe-top));left:0;right:0;z-index:50000;height:44px;padding:0 12px;display:none;align-items:center;gap:8px;background:var(--bar);-webkit-backdrop-filter:blur(20px) saturate(180%);backdrop-filter:blur(20px) saturate(180%);border-bottom:1px solid var(--border);overflow-x:auto;scrollbar-width:none;}' +
      '#splashToolbar::-webkit-scrollbar{display:none;}' +
      '#splashToolbar.show{display:flex !important;}' +
      '.splash-tool-btn{padding:6px 12px;border-radius:18px;border:1px solid var(--border);background:rgba(127,127,127,0.06);color:var(--text);font-size:12px;font-family:monospace;cursor:pointer;white-space:nowrap;flex-shrink:0;}' +
      '.splash-tool-btn:active{transform:scale(0.95);}' +
      '.splash-tool-label{font-size:11px;color:var(--sub);font-family:monospace;flex-shrink:0;}' +
      '#splashFontSize{width:80px;flex-shrink:0;}' +
      '#splashFxType{flex-shrink:0;padding:4px 6px;border-radius:8px;background:rgba(127,127,127,0.08);color:var(--text);border:1px solid var(--border);font-size:11px;outline:none;}' +
      '.splash-floater{position:absolute;z-index:100005;min-width:80px;min-height:30px;padding:8px 12px;border-radius:10px;border:2px dashed var(--accent);background:rgba(25,25,30,0.7);-webkit-backdrop-filter:blur(12px);backdrop-filter:blur(12px);color:#f0f0f0;font-size:16px;line-height:1.5;word-break:break-word;cursor:move;outline:none;pointer-events:auto;touch-action:none;user-select:none;}' +
      'body:not(.editing) .splash-floater{border-color:transparent;background:transparent;-webkit-backdrop-filter:none;backdrop-filter:none;cursor:default;}' +
      '.splash-fdel{position:absolute;top:-10px;left:-10px;width:20px;height:20px;border-radius:50%;background:#e5484d;color:#fff;border:none;font-size:11px;line-height:1;cursor:pointer;display:none;align-items:center;justify-content:center;padding:0;z-index:5;}' +
      'body.editing .splash-floater .splash-fdel{display:flex;}' +
      '.splash-floater.fx-hidden{opacity:0;transform:translateY(20px);transition:opacity .8s ease,transform .8s ease;}' +
      '.splash-floater.fx-visible{opacity:1;transform:translateY(0);}' +
      '.splash-floater.fx-slide-up{opacity:0;transform:translateY(30px);transition:opacity .8s ease,transform .8s cubic-bezier(0.16,1,0.3,1);}' +
      '.splash-floater.fx-slide-up.fx-visible{opacity:1;transform:translateY(0);}' +
      '.splash-floater.fx-fade-in{opacity:0;transition:opacity 1s ease;}' +
      '.splash-floater.fx-fade-in.fx-visible{opacity:1;}' +
      '.splash-floater.fx-scale-in{opacity:0;transform:scale(0.9);transition:opacity .8s ease,transform .8s ease;}' +
      '.splash-floater.fx-scale-in.fx-visible{opacity:1;transform:scale(1);}';
    document.head.appendChild(style);
  }
  
  function createToolbar() {
    if (document.getElementById(TOOLBAR_ID)) return;
    var bar = document.createElement('div');
    bar.id = TOOLBAR_ID;
    bar.innerHTML = 
      '<button class="splash-tool-btn" id="splashAddFloat">＋ 浮动文本</button>' +
      '<span style="flex:1;min-width:8px;"></span>' +
      '<span class="splash-tool-label">字号</span>' +
      '<input type="range" id="splashFontSize" min="10" max="120" value="16">' +
      '<span class="splash-tool-label" id="splashFontSizeVal" style="min-width:36px;text-align:right;">16px</span>' +
      '<span class="splash-tool-label">特效</span>' +
      '<select id="splashFxType">' +
        '<option value="none">无</option>' +
        '<option value="fx-slide-up">上滑</option>' +
        '<option value="fx-fade-in">淡入</option>' +
        '<option value="fx-scale-in">缩放</option>' +
      '</select>' +
      '<button class="splash-tool-btn" id="splashDone">✓ 完成</button>';
    document.body.appendChild(bar);
    return bar;
  }
  
  function saveFloaters() {
    try {
      var card = document.querySelector('#splash .splash-card');
      if (!card) return;
      var arr = [];
      card.querySelectorAll('.splash-floater').forEach(function(el) {
        var txt = el.cloneNode(true);
        var del = txt.querySelector('.splash-fdel');
        if (del) del.remove();
        arr.push({
          text: txt.textContent.trim(),
          left: el.style.left,
          top: el.style.top,
          fontSize: el.style.fontSize,
          color: el.style.color,
          fx: el.dataset.fx || 'none'
        });
      });
      localStorage.setItem(FLOATERS_KEY, JSON.stringify(arr));
    } catch(e) {}
  }
  
  function restoreFloaters() {
    try {
      var data = localStorage.getItem(FLOATERS_KEY);
      if (!data) return;
      var arr = JSON.parse(data);
      var card = document.querySelector('#splash .splash-card');
      if (!card) return;
      arr.forEach(function(item) {
        createFloater(card, item.text, item.left, item.top, item.fontSize, item.color, item.fx);
      });
    } catch(e) {}
  }
  
  function createFloater(card, text, left, top, fontSize, color, fx) {
    var el = document.createElement('div');
    el.className = 'splash-floater';
    el.contentEditable = 'true';
    el.textContent = text || '开屏文字';
    el.dataset.fx = fx || 'none';
    el.style.left = left || '50%';
    el.style.top = top || '50%';
    el.style.fontSize = fontSize || '16px';
    if (color) el.style.color = color;
    if (left === '50%' && top === '50%') el.style.transform = 'translate(-50%, -50%)';
    
    var del = document.createElement('button');
    del.className = 'splash-fdel';
    del.textContent = '×';
    del.addEventListener('click', function(e) {
      e.stopPropagation();
      if (confirm('删除这个开屏文字？')) { el.remove(); saveFloaters(); }
    });
    el.appendChild(del);
    
    el.addEventListener('click', function(e) {
      if (!document.body.classList.contains('editing')) return;
      e.stopPropagation(); selectEl(el);
    });
    el.addEventListener('focusin', function() { selectEl(el); });
    el.addEventListener('dblclick', function(e) {
      e.stopPropagation();
      el.setAttribute('contenteditable', 'true');
      el.focus();
      var range = document.createRange(); range.selectNodeContents(el);
      var sel = window.getSelection(); sel.removeAllRanges(); sel.addRange(range);
    });
    el.addEventListener('blur', function() { el.removeAttribute('contenteditable'); saveFloaters(); });
    el.addEventListener('keydown', function(e) { if (e.key === 'Enter') { e.preventDefault(); el.blur(); } });
    
    makeDraggable(el);
    card.appendChild(el);
    return el;
  }
  
  function makeDraggable(el) {
    var dragging = false, sx = 0, sy = 0, sl = 0, st = 0, moved = false;
    function down(e) {
      if (!document.body.classList.contains('editing')) return;
      if (e.target.classList.contains('splash-fdel')) return;
      e.preventDefault();
      dragging = true; moved = false;
      if (el.style.transform) {
        var rect = el.getBoundingClientRect();
        var pr = el.parentNode.getBoundingClientRect();
        el.style.transform = 'none';
        el.style.left = (rect.left - pr.left) + 'px';
        el.style.top = (rect.top - pr.top) + 'px';
      }
      var p = e.touches ? e.touches[0] : e;
      sx = p.clientX; sy = p.clientY;
      sl = parseFloat(el.style.left) || 0;
      st = parseFloat(el.style.top) || 0;
      document.addEventListener('mousemove', move);
      document.addEventListener('touchmove', move, {passive:false});
      document.addEventListener('mouseup', up);
      document.addEventListener('touchend', up);
    }
    function move(e) {
      if (!dragging) return;
      e.preventDefault();
      var p = e.touches ? e.touches[0] : e;
      var dx = p.clientX - sx, dy = p.clientY - sy;
      if (Math.abs(dx) > 3 || Math.abs(dy) > 3) moved = true;
      el.style.left = (sl + dx) + 'px';
      el.style.top = (st + dy) + 'px';
    }
    function up() {
      dragging = false;
      document.removeEventListener('mousemove', move);
      document.removeEventListener('touchmove', move);
      document.removeEventListener('mouseup', up);
      document.removeEventListener('touchend', up);
      if (moved) saveFloaters();
    }
    el.addEventListener('mousedown', down);
    el.addEventListener('touchstart', down, {passive:false});
  }
  
  function selectEl(el) {
    activeEl = el;
    var cs = getComputedStyle(el);
    var size = parseInt(cs.fontSize) || 16;
    var sizeInput = document.getElementById('splashFontSize');
    if (sizeInput) sizeInput.value = size;
    var sizeVal = document.getElementById('splashFontSizeVal');
    if (sizeVal) sizeVal.textContent = size + 'px';
    var fx = document.getElementById('splashFxType');
    if (fx) fx.value = el.dataset.fx || 'none';
  }
  
  function bindEvents() {
    var addBtn = document.getElementById('splashAddFloat');
    if (addBtn) addBtn.addEventListener('click', function() {
      var card = document.querySelector('#splash .splash-card');
      if (!card) { alert('请先点顶部"🎬 开屏"打开开屏'); return; }
      var el = createFloater(card);
      el.setAttribute('contenteditable', 'true');
      el.focus();
      selectEl(el);
      saveFloaters();
    });
    
    var sizeInput = document.getElementById('splashFontSize');
    if (sizeInput) sizeInput.addEventListener('input', function(e) {
      var v = e.target.value;
      var sizeVal = document.getElementById('splashFontSizeVal');
      if (sizeVal) sizeVal.textContent = v + 'px';
      if (activeEl) {
        activeEl.style.fontSize = v + 'px';
        saveFloaters();
      } else {
        var title = document.querySelector('#splash .splash-title');
        if (title) {
          title.style.fontSize = v + 'px';
          try {
            var sData = JSON.parse(localStorage.getItem('myPage.splash.v1') || '{}');
            sData.titleSize = parseInt(v);
            localStorage.setItem('myPage.splash.v1', JSON.stringify(sData));
          } catch(err) {}
        }
      }
    });
    
    var fxSelect = document.getElementById('splashFxType');
    if (fxSelect) fxSelect.addEventListener('change', function(e) {
      var val = e.target.value;
      if (!activeEl) { alert('请先点击选中一个开屏文字'); return; }
      activeEl.dataset.fx = val;
      activeEl.classList.remove('fx-slide-up', 'fx-fade-in', 'fx-scale-in', 'fx-visible', 'fx-hidden');
      if (val !== 'none') {
        activeEl.classList.add(val);
        activeEl.classList.add('fx-hidden');
        setTimeout(function() {
          activeEl.classList.remove('fx-hidden');
          activeEl.classList.add('fx-visible');
        }, 200);
      }
      saveFloaters();
    });
    
    var doneBtn = document.getElementById('splashDone');
    if (doneBtn) doneBtn.addEventListener('click', function() {
      var splash = document.getElementById('splash');
      if (splash) splash.remove();
      var bar = document.getElementById(TOOLBAR_ID);
      if (bar) bar.classList.remove('show');
      saveFloaters();
    });
  }
  
  function watchSplash() {
    var hasSplash = false;
    setInterval(function() {
      var splash = document.getElementById('splash');
      var bar = document.getElementById(TOOLBAR_ID);
      if (!bar) return;
      if (splash && !hasSplash) {
        hasSplash = true;
        if (document.body.classList.contains('editing')) {
          bar.classList.add('show');
          setTimeout(restoreFloaters, 100);
        }
      } else if (!splash && hasSplash) {
        hasSplash = false;
        bar.classList.remove('show');
        saveFloaters();
      }
    }, 300);
  }
  
  function init() {
    injectStyle();
    createToolbar();
    bindEvents();
    watchSplash();
  }
  
  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', init);
  } else {
    init();
  }
})();

</script>
</body>
</html>