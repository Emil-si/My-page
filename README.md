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
  #bgLayer{position:fixed;inset:0;z-index:0;background-color:var(--bg);background-size:cover;background-position:center;background-repeat:no-repeat;pointer-events:none;transition:background-color .3s;}
  #toolbar{position:fixed;top:0;left:0;right:0;z-index:100;height:calc(48px + var(--safe-top));padding-top:var(--safe-top);padding-left:12px;padding-right:12px;display:flex;align-items:center;gap:6px;background:var(--bar);-webkit-backdrop-filter:blur(20px) saturate(180%);backdrop-filter:blur(20px) saturate(180%);border-bottom:1px solid var(--border);overflow-x:auto;scrollbar-width:none;}
  #toolbar::-webkit-scrollbar{display:none;}
  .btn{display:flex;align-items:center;gap:4px;padding:6px 12px;border-radius:18px;border:1px solid var(--border);background:rgba(127,127,127,0.06);color:var(--text);font-size:12px;font-family:monospace;cursor:pointer;white-space:nowrap;flex-shrink:0;transition:all .2s;}
  .btn:active{transform:scale(.95);}
  .btn.on{background:var(--accent);color:var(--accent-text);border-color:var(--accent);}
  .edit-only{display:none !important;}
  body.editing .edit-only{display:flex !important;}
  .spacer{flex:1;}
  #topnav{max-width:640px;margin:0 auto;padding:10px 14px;display:flex;gap:6px;position:sticky;top:calc(48px + var(--safe-top));z-index:90;background:var(--bar);-webkit-backdrop-filter:blur(20px) saturate(180%);backdrop-filter:blur(20px) saturate(180%);border-bottom:1px solid var(--border);overflow-x:auto;scrollbar-width:none;align-items:center;}
  #topnav::-webkit-scrollbar{display:none;}
  .navlink{flex:1 0 auto;text-align:center;padding:7px 14px;border-radius:18px;background:rgba(127,127,127,0.06);border:1px solid var(--border);color:var(--sub);text-decoration:none;font-size:11px;font-family:monospace;letter-spacing:.08em;cursor:pointer;white-space:nowrap;transition:all .2s;}
  .navlink.active{background:var(--accent);color:var(--accent-text);border-color:var(--accent);}
  .navlink.collection{flex:0 0 auto;min-width:38px;padding:7px 10px;display:flex;align-items:center;justify-content:center;}
  .navlink.collection.active{background:var(--accent);color:var(--accent-text);}
  #collectionMenu{position:fixed;top:calc(100px + var(--safe-top));left:calc(16px + var(--safe-left));z-index:200;background:var(--bar);-webkit-backdrop-filter:blur(20px) saturate(180%);backdrop-filter:blur(20px) saturate(180%);border:1px solid var(--border);border-radius:14px;padding:8px;box-shadow:0 12px 40px rgba(0,0,0,.4);min-width:160px;max-width:80vw;display:none;}
  #collectionMenu.show{display:block;}
  .citem{display:block;padding:8px 12px;border-radius:8px;color:var(--text);font-size:12px;font-family:monospace;cursor:pointer;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;transition:all .15s;}
  .citem:hover{background:rgba(127,127,127,0.15);}
  .citem.active{background:var(--accent);color:var(--accent-text);}
  .cempty{padding:8px 12px;font-size:11px;color:var(--sub);text-align:center;}
  #app{max-width:640px;margin:0 auto;padding:14px;position:relative;z-index:10;}
  .panel{display:none;}
  .panel.active{display:block;}
  .card{background:var(--card);-webkit-backdrop-filter:blur(20px) saturate(180%);backdrop-filter:blur(20px) saturate(180%);border:1px solid var(--border);border-radius:20px;padding:16px;margin-bottom:14px;position:relative;word-break:break-word;}
  .cover{width:calc(100% + 32px);height:180px;border-radius:20px 20px 0 0;background:#1a1a1a;position:relative;overflow:hidden;margin:-16px -16px 0 -16px;}
  .cover img{width:100%;height:100%;object-fit:cover;display:block;}
  .cover::after{content:"";position:absolute;inset:auto 0 0 0;height:120px;background:linear-gradient(to bottom, transparent, var(--card));pointer-events:none;}
  .profile{display:flex;align-items:flex-end;gap:12px;margin-top:-28px;position:relative;z-index:2;padding-bottom:10px;}
  .avatar{width:76px;height:76px;border-radius:50%;border:3px solid var(--card);overflow:hidden;flex-shrink:0;background:#222;box-shadow:0 6px 20px rgba(0,0,0,.4);}
  .avatar img{width:100%;height:100%;object-fit:cover;display:block;}
  .namebox{flex:1;padding-bottom:4px;}
  .name{font-size:22px;font-weight:700;margin-bottom:2px;}
  .handle{font-size:12px;color:var(--sub);font-family:monospace;}
  .stats{display:flex;gap:18px;font-size:13px;margin:10px 0 6px;color:var(--sub);}
  .stats b{color:var(--text);font-weight:600;}
  .grid{display:grid;grid-template-columns:repeat(3, 1fr);gap:6px;margin:12px 0;}
  .grid img{width:100%;aspect-ratio:1;object-fit:cover;border-radius:10px;display:block;cursor:pointer;background:rgba(127,127,127,0.1);}
  .quote{padding:14px;border:1px solid var(--border);border-radius:14px;background:rgba(127,127,127,0.04);font-size:13px;line-height:1.7;color:var(--sub);font-style:italic;margin:12px 0;}
  .post{background:rgba(127,127,127,0.05);border:1px solid var(--border);border-radius:16px;padding:14px;margin:12px 0 0;}
  .post input{width:100%;background:transparent;border:none;outline:none;color:var(--text);font-size:14px;font-family:inherit;}
  .post input::placeholder{color:var(--sub);}
  .postrow{display:flex;justify-content:space-between;align-items:center;margin-top:10px;}
  .postrow .icons{display:flex;gap:10px;color:var(--sub);font-size:16px;}
  .pubbtn{background:var(--accent);color:var(--accent-text);border:none;padding:7px 18px;border-radius:18px;font-size:12px;font-weight:600;cursor:pointer;}
  .feed{display:flex;gap:10px;margin-bottom:10px;align-items:center;}
  .feed .fav{width:36px;height:36px;border-radius:50%;overflow:hidden;background:#333;flex-shrink:0;}
  .feed .fav img{width:100%;height:100%;object-fit:cover;display:block;}
  .feed .meta{flex:1;}
  .feed .meta .n{font-size:13px;font-weight:600;}
  .feed .meta .h{font-size:11px;color:var(--sub);font-family:monospace;}
  .feed .time{font-size:11px;color:var(--sub);}
  .etext{font-size:14px;line-height:1.6;padding:4px 0;position:relative;}
  .divider{padding:8px 0;display:flex;align-items:center;position:relative;}
  .divider .line{height:1px;background:var(--border);width:100%;border-radius:1px;transition:all .2s;}
  body.editing .divider:hover .line{background:var(--accent);}
  
  /* ===== 修复：编辑态/保存态偏移下沉 ===== */
  .bwrap { position: relative; padding-right: 0; }
  .bwrap > .row-tools {
    position: absolute; 
    top: -28px; 
    right: 0; 
    display: none; 
    flex-direction: row; 
    gap: 6px; 
    opacity: 0.9; 
    z-index: 100; 
    background: var(--bar); 
    padding: 2px 4px; 
    border-radius: 6px; 
    box-shadow: 0 4px 12px rgba(0,0,0,0.2);
  }
  body.editing .bwrap > .row-tools { display: flex !important; opacity: 1; }
  
  /* ===== 修复：浮动文本框删除按钮 ===== */
  body.editing .xdel { display: flex; }
  body:not(.editing) .floater { border-color: transparent; }

  .row-tools button{width:24px;height:24px;border-radius:6px;border:1px solid var(--border);background:var(--accent);color:var(--accent-text);font-size:13px;cursor:pointer;padding:0;line-height:1;display:flex;align-items:center;justify-content:center;transition:all .15s;}
  .row-tools button.dvd { background: #7d90b8; font-size: 14px; }
  .row-tools button.del { background: #e5484d; }
  .row-tools button:hover { transform: scale(1.1); }
  .shape{position:absolute;z-index:999999;cursor:move;pointer-events:auto;touch-action:none;user-select:none;filter:drop-shadow(0 4px 12px rgba(0,0,0,.3));display:block;}
  .shape svg{width:100%;height:100%;display:block;pointer-events:none;}
  .frame{position:absolute;z-index:999999;overflow:hidden;background:#333;border:2px solid var(--border);cursor:move;pointer-events:auto;touch-action:none;user-select:none;box-shadow:0 6px 24px rgba(0,0,0,.3);display:block;}
  .frame img{width:100%;height:100%;object-fit:cover;display:block;pointer-events:auto;}
  .floater{position:absolute;z-index:999999;min-width:100px;min-height:40px;padding:12px;border-radius:12px;border:2px dashed var(--accent);background:var(--card);-webkit-backdrop-filter:blur(12px);backdrop-filter:blur(12px);color:var(--text);font-size:14px;line-height:1.5;word-break:break-word;cursor:text;outline:none;pointer-events:auto;touch-action:none;display:block;}
  
  /* 新增：主页浮动文本框无背景样式 */
  .floater.no-bg {
    border-color: transparent !important;
    background: transparent !important;
    -webkit-backdrop-filter: none !important;
    backdrop-filter: none !important;
  }
  body:not(.editing) .floater.no-bg {
    border-color: transparent !important;
  }

  .xdel{position:absolute;top:-10px;left:-10px;width:22px;height:22px;border-radius:50%;background:#e5484d;color:#fff;border:none;font-size:12px;cursor:pointer;line-height:1;display:none;align-items:center;justify-content:center;padding:0;z-index:5;}
  .rz{position:absolute;bottom:0;right:0;width:28px;height:28px;background:linear-gradient(135deg, transparent 48%, var(--accent) 48%);cursor:se-resize;z-index:10;border-bottom-right-radius:10px;touch-action:none;}
  .rz::before{content:"";position:absolute;right:-6px;bottom:-6px;width:44px;height:44px;background:transparent;cursor:se-resize;}
  body:not(.editing) .rz{display:none;}
  #shapePicker, #framePicker{position:fixed;top:calc(110px + var(--safe-top));left:50%;transform:translateX(-50%);z-index:100001;background:var(--bar);-webkit-backdrop-filter:blur(24px) saturate(180%);backdrop-filter:blur(24px) saturate(180%);border:1px solid var(--border);border-radius:16px;padding:14px;box-shadow:0 12px 40px rgba(0,0,0,.5);display:none;grid-template-columns:repeat(5, 1fr);gap:8px;max-width:calc(100vw - 24px);}
  #shapePicker.show, #framePicker.show{display:grid;}
  .pick{width:44px;height:44px;display:flex;align-items:center;justify-content:center;border-radius:10px;cursor:pointer;background:rgba(127,127,127,0.06);border:1px solid var(--border);color:var(--text);transition:all .2s;}
  .pick:hover{background:var(--accent);color:var(--accent-text);border-color:var(--accent);}
  .pick svg{width:26px;height:26px;}
  .pick-title{grid-column:1/-1;text-align:center;font-size:11px;color:var(--accent);font-family:monospace;letter-spacing:.1em;padding-bottom:6px;border-bottom:1px solid var(--border);margin-bottom:4px;}
  .pagenav{display:flex;justify-content:space-between;align-items:center;padding:10px 0;margin-top:10px;}
  .navbtn{width:44px;height:44px;border-radius:50%;background:var(--card);border:1px solid var(--border);color:var(--text);font-size:18px;cursor:pointer;display:flex;align-items:center;justify-content:center;-webkit-backdrop-filter:blur(20px);backdrop-filter:blur(20px);}
  .navbtn:active{background:var(--accent);color:var(--accent-text);}
  .spacer30{width:44px;}
  .extras{display:none;gap:6px;flex-wrap:wrap;margin-bottom:10px;}
  body.editing .extras{display:flex;}
  .exbtn{padding:8px 14px;border-radius:12px;border:1px dashed var(--border);background:transparent;color:var(--sub);font-size:12px;font-family:monospace;cursor:pointer;transition:all .2s;}
  .exbtn:hover{background:rgba(127,127,127,0.08);color:var(--accent);border-color:var(--accent);}
  #stylePanel{position:fixed;right:14px;top:calc(110px + var(--safe-top));z-index:100000;width:260px;background:var(--bar);-webkit-backdrop-filter:blur(24px) saturate(180%);backdrop-filter:blur(24px) saturate(180%);border:1px solid var(--border);border-radius:16px;box-shadow:0 12px 40px rgba(0,0,0,.5);padding:14px;display:none;flex-direction:column;gap:8px;max-height:75vh;overflow-y:auto;font-size:12px;}
  #stylePanel.show{display:flex;}
  .sp-title{font-size:11px;color:var(--accent);font-family:monospace;letter-spacing:.1em;padding-bottom:6px;border-bottom:1px solid var(--border);margin-bottom:4px;}
  .sp-row{display:flex;align-items:center;justify-content:space-between;gap:8px;margin-bottom:6px;}
  .sp-row > span{flex-shrink:0;font-size:11px;color:var(--sub);}
  .sp-row input[type="range"]{flex:1;cursor:pointer;height:22px;}
  .sp-row input[type="color"]{width:30px;height:30px;border:none;border-radius:8px;cursor:pointer;background:transparent;}
  .sp-row select{flex:1;padding:5px 8px;border-radius:8px;background:rgba(127,127,127,0.08);color:var(--text);border:1px solid var(--border);font-size:11px;outline:none;}
  .sp-group{display:flex;gap:4px;flex-wrap:wrap;}
  .sp-btn{padding:5px 10px;border-radius:8px;border:1px solid var(--border);background:transparent;color:var(--text);font-size:11px;font-family:monospace;cursor:pointer;transition:all .15s;}
  .sp-btn:hover{background:rgba(127,127,127,0.12);}
  .sp-btn.on{background:var(--accent);color:var(--accent-text);border-color:var(--accent);}
  .sp-note{font-size:10px;color:var(--sub);line-height:1.5;padding:6px 8px;background:rgba(127,127,127,0.06);border-radius:8px;margin-top:4px;}
  #musicPanel{position:fixed;bottom:calc(20px + var(--safe-bottom));right:calc(14px + 48px + 12px);z-index:210;background:var(--bar);-webkit-backdrop-filter:blur(24px) saturate(180%);backdrop-filter:blur(24px) saturate(180%);border:1px solid rgba(255, 255, 255, 0.08);border-radius:999px;padding:6px 14px;box-shadow:0 12px 40px rgba(0, 0, 0, 0.4);display:none;flex-direction:row;align-items:center;gap:10px;}
  #musicPanel.show { display: flex; }
  .mp-controls { display: flex; align-items: center; gap: 2px; }
  .mp-ctrl-btn{width:34px;height:34px;border-radius:50%;border:none;background:transparent;color:var(--text);cursor:pointer;display:flex;align-items:center;justify-content:center;padding:0;transition:all 0.15s ease;}
  .mp-ctrl-btn svg { width: 16px; height: 16px; }
  .mp-ctrl-btn:hover { background: rgba(127, 127, 127, 0.2); }
  .mp-ctrl-btn.play{background:var(--accent);color:var(--accent-text);margin:0 2px;}
  .mp-ctrl-btn.play:hover { transform: scale(1.05); }
  .mp-vol-container{display:flex;align-items:center;justify-content:center;height:38px;width:20px;}
  #mpVolume{writing-mode:vertical-lr;direction:rtl;-webkit-appearance:slider-vertical;appearance:slider-vertical;width:4px;height:38px;background:rgba(127, 127, 127, 0.3);border-radius:2px;outline:none;cursor:pointer;}
  #mpVolume::-webkit-slider-thumb{-webkit-appearance:none;width:12px;height:12px;border-radius:50%;background:var(--text);cursor:pointer;}
  .mp-add{width:28px;height:28px;border-radius:50%;border:1px solid var(--border);background:transparent;color:var(--sub);font-size:16px;display:flex;align-items:center;justify-content:center;cursor:pointer;padding:0;transition:all 0.15s;}
  .mp-add:hover { border-color: var(--accent); color: var(--accent); }
  #musicBtn{position:fixed;bottom:calc(20px + var(--safe-bottom));right:14px;width:48px;height:48px;border-radius:50%;background:var(--card);border:1px solid var(--border);-webkit-backdrop-filter:blur(20px);backdrop-filter:blur(20px);box-shadow:0 6px 24px rgba(0,0,0,.35);display:flex;align-items:center;justify-content:center;color:var(--text);font-size:22px;cursor:pointer;z-index:220;touch-action:none;user-select:none;}
  #musicBtn.playing { animation: spin 4s linear infinite; }
  body.editing [data-edit]{outline:1px dashed transparent;outline-offset:4px;border-radius:4px;cursor:text;transition:outline .2s;}
  body.editing [data-edit]:hover{outline-color:var(--accent);}
  body.editing [data-edit]:focus{outline:2px solid var(--accent);background:rgba(74,158,255,0.08);}
  
  /* ===== 增强：链接提示样式（支持普通文本、浮动文本） ===== */
  [data-link] {
    cursor: pointer;
    text-decoration: underline;
    text-decoration-style: dotted;
    text-underline-offset: 4px;
  }
  [data-link]:hover {
    color: var(--accent);
  }
  [data-link]::after {
    content: " ↗";
    font-size: 0.8em;
    opacity: 0.7;
  }
  /* 兼容原版 linktext 类 */
  .linktext { cursor: pointer; text-decoration: underline; text-decoration-style: dotted; text-underline-offset: 4px; }
  .linktext:hover { color: var(--accent); }
  .linktext::after { content: " ↗"; font-size: .8em; opacity: .7; }

  #picker{position:fixed;left:0;right:0;bottom:0;height:52vh;z-index:100002;background:var(--bar);-webkit-backdrop-filter:blur(24px) saturate(180%);backdrop-filter:blur(24px) saturate(180%);border-top:1px solid var(--border);border-radius:20px 20px 0 0;transform:translateY(100%);transition:transform .3s ease;display:flex;flex-direction:column;}
  #picker.show{transform:translateY(0);}
  .ph{padding:12px 16px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between;flex-shrink:0;}
  .ptitle{font-size:12px;font-family:monospace;color:var(--accent);letter-spacing:.1em;}
  .pclose{background:transparent;border:1px solid var(--border);color:var(--text);font-size:20px;width:30px;height:30px;border-radius:8px;cursor:pointer;line-height:1;padding:0;}
  .pgrid{flex:1;overflow-y:auto;padding:12px;display:grid;grid-template-columns:repeat(4, 1fr);gap:6px;padding-bottom:calc(12px + var(--safe-bottom));}
  .pgrid img{width:100%;aspect-ratio:1;object-fit:cover;border-radius:8px;cursor:pointer;border:2px solid transparent;transition:border-color .2s;background:rgba(127,127,127,0.1);}
  .pgrid img:hover{border-color:var(--accent);}
  #toast{position:fixed;left:50%;bottom:calc(80px + var(--safe-bottom));transform:translateX(-50%) translateY(20px);background:var(--accent);color:var(--accent-text);padding:9px 18px;border-radius:18px;font-size:12px;opacity:0;pointer-events:none;transition:all .3s;z-index:100003;}
  #toast.show{opacity:1;transform:translateX(-50%) translateY(0);}
  #musicDialog{position:fixed;inset:0;z-index:100004;background:rgba(0,0,0,.5);-webkit-backdrop-filter:blur(6px);backdrop-filter:blur(6px);display:none;align-items:center;justify-content:center;padding:20px;}
  #musicDialog.show{display:flex;}
  .mcard{width:100%;max-width:340px;background:var(--card);border:1px solid var(--border);border-radius:18px;padding:20px;-webkit-backdrop-filter:blur(24px);backdrop-filter:blur(24px);}
  .mcard h3{font-size:13px;color:var(--accent);margin-bottom:12px;font-family:monospace;letter-spacing:.1em;text-align:center;}
  .mcard input{width:100%;padding:11px 12px;border-radius:10px;border:1px solid var(--border);background:rgba(127,127,127,0.08);color:var(--text);font-size:13px;font-family:monospace;outline:none;margin-bottom:12px;}
  .mcard .row{display:flex;gap:8px;}
  .mcard .row button{flex:1;padding:10px;border-radius:10px;border:1px solid var(--border);background:transparent;color:var(--text);font-size:13px;font-family:monospace;cursor:pointer;}
  .mcard .row button.primary{background:var(--accent);color:var(--accent-text);border-color:var(--accent);font-weight:600;}
  .card-actions { position: absolute; top: 8px; right: 8px; display: none; gap: 4px; z-index: 100; }
  body.editing .card-actions { display: flex !important; }
  .card-actions button { background: #e5484d; color: #fff; border: none; border-radius: 6px; padding: 4px 8px; font-size: 11px; cursor: pointer; font-family: monospace; }
  .card-actions button:hover { transform: scale(1.05); }
  .add-card-btn{display:none;width:100%;padding:12px;margin:4px 0 14px 0;border:1px dashed var(--border);background:rgba(127,127,127,0.05);color:var(--sub);border-radius:14px;cursor:pointer;font-family:monospace;font-size:12px;transition:all 0.2s;}
  body.editing .add-card-btn { display: block !important; }
  .add-card-btn:hover { border-color: var(--accent); color: var(--accent); background: rgba(74,158,255,0.08); }

  /* ==================== 开屏动画重做 ==================== */
  #splash {
    position: fixed;
    inset: 0;
    z-index: 99999;
    background-color: #1c1c1e;
    /* 替换为您真实的图片地址 */
    background-image: url('https://emil-si.github.io/My-page/IMG_2138.png'); 
    background-size: cover;
    background-position: center;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }
  /* 暗色遮罩增强文字可读性 */
  #splash::before {
    content: "";
    position: absolute;
    inset: 0;
    background: rgba(0, 0, 0, 0.45);
    backdrop-filter: blur(8px);
    -webkit-backdrop-filter: blur(8px);
    z-index: 1;
  }

  .splash-card {
    position: relative;
    z-index: 2;
    width: 85%;
    max-width: 380px;
    padding: 32px 24px;
    /* 玻璃质感 */
    background: rgba(255, 255, 255, 0.06);
    border: 1px solid rgba(255, 255, 255, 0.15);
    border-radius: 24px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5), inset 0 0 20px rgba(255, 255, 255, 0.05);
    -webkit-backdrop-filter: blur(24px) saturate(180%);
    backdrop-filter: blur(24px) saturate(180%);
    text-align: center;
    /* 缓入动画 */
    transform: translateY(30px) scale(0.95);
    opacity: 0;
    transition: transform 1.2s cubic-bezier(0.16, 1, 0.3, 1), opacity 1.2s ease;
  }
  .splash-card.show {
    transform: translateY(0) scale(1);
    opacity: 1;
  }

  .splash-text {
    font-size: 15px;
    line-height: 1.8;
    color: #f0f0f0;
    letter-spacing: 0.05em;
    font-weight: 300;
    text-shadow: 0 2px 10px rgba(0,0,0,0.5);
  }

  .splash-enter-btn {
    position: relative;
    z-index: 2;
    margin-top: 40px;
    padding: 14px 32px;
    background: var(--accent);
    color: var(--accent-text);
    border: none;
    border-radius: 999px; /* 胶囊形状 */
    font-size: 15px;
    font-family: inherit;
    letter-spacing: 2px;
    cursor: pointer;
    box-shadow: 0 8px 24px rgba(74, 158, 255, 0.4);
    /* 延迟出现动画 */
    opacity: 0;
    transform: translateY(20px);
    transition: opacity 0.8s ease 0.6s, transform 0.8s cubic-bezier(0.16, 1, 0.3, 1) 0.6s;
  }
  .splash-enter-btn.show {
    opacity: 1;
    transform: translateY(0);
  }
  .splash-enter-btn:active {
    transform: scale(0.96);
  }

  /* 开屏浮动文本编辑样式 */
  body.editing .splash-floater {
    border: 2px dashed var(--accent);
    background: rgba(25,25,30,0.7);
    padding: 8px 12px;
    border-radius: 10px;
  }
  .splash-floater {
    position: absolute;
    z-index: 100005;
    min-width: 80px; min-height: 30px;
    border: 2px dashed transparent;
    background: transparent;
    -webkit-backdrop-filter: none;
    backdrop-filter: none;
    color: #f0f0f0;
    font-size: 14px; line-height: 1.5;
    word-break: break-word;
    outline: none;
    pointer-events: auto;
    user-select: text;
    -webkit-user-select: text;
  }
  .splash-text {
    outline: none;
    display: block;
    min-width: 40px;
    min-height: 20px;
    cursor: text;
    user-select: text;
    -webkit-user-select: text;
    padding-right: 4px;
  }
  .splash-fmove {
    position: absolute;
    top: -10px; right: -10px;
    width: 24px; height: 24px;
    border-radius: 50%;
    background: var(--accent);
    color: var(--accent-text);
    border: none;
    font-size: 12px;
    line-height: 1;
    cursor: move;
    display: none;
    align-items: center;
    justify-content: center;
    padding: 0;
    z-index: 100;
    touch-action: none;
    user-select: none;
    -webkit-user-select: none;
  }
  body.editing .splash-fmove { display: flex; }
  .splash-floater .splash-fdel {
    position: absolute;
    top: -10px; left: -10px;
    width: 24px; height: 24px;
    border-radius: 50%;
    background: #e5484d;
    color: #fff;
    border: none;
    font-size: 14px;
    line-height: 1;
    cursor: pointer;
    display: none;
    align-items: center;
    justify-content: center;
    padding: 0;
    z-index: 100;
    touch-action: none;
    user-select: none;
    -webkit-user-select: none;
  }
  body.editing .splash-floater .splash-fdel { display: flex; }
</style>
</head>
<body>

<div id="splash">
  <div class="splash-card" id="splashCard">
    <div class="splash-text" id="splashMainText">口头的爱太缥缈了，所以要恨，要抹不去的执念，在幸福千里之遥的时候，触手可及的恨才是漫天的委屈。</div>
  </div>
  <button class="splash-enter-btn" id="splashEnterBtn">
    <span class="splash-btn-text">认识我 ⟶</span>
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
  
  <!-- 主页浮动文本框的背景控制 -->
  <div class="sp-row" id="spFloaterBgRow" style="display:none;">
    <span>文本背景</span>
    <button class="sp-btn" id="spFloaterBgToggle" style="width:100%;">显示</button>
  </div>
  
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

  // ==================== 1. 开屏兜底与双击逻辑 ====================
  (function() {
      var splash = document.getElementById('splash');
      if (!splash) return;
      var splashCard = document.getElementById('splashCard');
      var splashBtn = document.getElementById('splashEnterBtn');
      
      // 延迟出现玻璃卡片和按钮
      setTimeout(function() {
          try {
              if (splashCard) splashCard.classList.add('show');
              if (splashBtn) splashBtn.classList.add('show');
          } catch(e) {}
      }, 100);

      function forceEnter() {
          if (document.body.classList.contains('editing')) return;
          
          // 保存开屏主文本
          try {
              var mainTextEl = document.getElementById('splashMainText');
              if (mainTextEl) {
                  localStorage.setItem('myPage.splash.mainText.v1', mainTextEl.textContent.trim());
              }
          } catch(e) {}

          try {
              if (splash.parentNode) splash.parentNode.removeChild(splash);
          } catch(e) {
              try { splash.remove(); } catch(err) {}
          }
          // 恢复主页浮动元素
          document.querySelectorAll('body > .shape, body > .frame, body > .floater').forEach(function(el) { el.style.display = ''; });
          updateFloatersVisibility();
          
          setTimeout(function() {
              try {
                  if (typeof playMusic === 'function') { playMusic(); }
                  else if (typeof music !== 'undefined' && music.src) { music.play().catch(function(){}); }
              } catch(err) {}
          }, 100);
      }

      var clickTimer = null;
      var mainTextEl = document.getElementById('splashMainText');
      
      // 恢复主文本内容
      try {
          var savedMainText = localStorage.getItem('myPage.splash.mainText.v1');
          if (savedMainText && mainTextEl) mainTextEl.textContent = savedMainText;
      } catch(e) {}

      if (mainTextEl) {
          mainTextEl.addEventListener('dblclick', function(e) {
              e.stopPropagation();
              clearTimeout(clickTimer);
              mainTextEl.setAttribute('contenteditable', 'true');
              mainTextEl.focus();
              var range = document.createRange(); range.selectNodeContents(mainTextEl);
              var sel = window.getSelection(); sel.removeAllRanges(); sel.addRange(range);
          });
          mainTextEl.addEventListener('blur', function() { 
              mainTextEl.removeAttribute('contenteditable');
              try { localStorage.setItem('myPage.splash.mainText.v1', mainTextEl.textContent.trim()); } catch(e) {}
          });
          mainTextEl.addEventListener('keydown', function(e) { if (e.key === 'Enter') { e.preventDefault(); mainTextEl.blur(); } });
      }

      splash.addEventListener('click', function(e) {
          var target = e.target;
          if (target.getAttribute('contenteditable') === 'true') return;
          if (target.closest('.splash-floater')) return; 
          if (target.closest('#splashEnterBtn')) {
              clearTimeout(clickTimer);
              forceEnter();
              return;
          }
          // 点击背景不跳转，只有点击按钮才跳转
      });
      
      if (splashBtn) {
          splashBtn.addEventListener('click', function(e) {
              e.stopPropagation();
              clearTimeout(clickTimer);
              forceEnter();
          });
      }
  })();

  // ==================== 2. 全局变量 ====================
  var IMG_BASE = 'https://cdn.jsdelivr.net/gh/emil-si/My-page@main/';
  var IMAGES = ['IMG_6273.jpeg','IMG_6334.jpeg','IMG_6394.jpeg','IMG_6395.jpeg','IMG_6396.jpeg','IMG_7871.jpeg','IMG_7872.jpeg','IMG_7873.jpeg','IMG_7874.jpeg','IMG_8450.jpeg','IMG_8451.jpeg','IMG_8452.jpeg','IMG_8453.jpeg','IMG_8454.jpeg','IMG_8455.jpeg','IMG_8456.jpeg','IMG_9006.png','IMG_9007.png','IMG_9008.png','IMG_9009.png','IMG_9010.png','IMG_9011.png','IMG_9012.png','IMG_9013.png','IMG_9014.png','IMG_9016.png'];
  var isReadonly = false;
  try { var qp = new URLSearchParams(location.search); isReadonly = qp.has('view') || qp.has('readonly'); } catch(e){}
  if(isReadonly && document.body) document.body.classList.add('readonly');
  var K_THEME = 'myPage.theme.v5', K_BG = 'myPage.bg.v5', K_CONTENT = 'myPage.content.v6', K_MUSIC = 'myPage.music.v5', K_ALPHA = 'myPage.alpha.v2', K_SPLASH = 'myPage.splash.v1', K_FLOATERS = 'myPage.floaters.v1';
  var app = document.getElementById('app');
  var bgLayer = document.getElementById('bgLayer');
  var toastEl = document.getElementById('toast');
  var editMode = false, activeImg = null, bgMode = false, activeTextEl = null, activeFloater = null;
  var DEFAULT_HTML = app ? app.innerHTML : '';

  function resolveTheme(){ var s = null; try { s = localStorage.getItem(K_THEME); } catch(e){} if(s === 'dark' || s === 'light') return s; try { if(window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) return 'dark'; } catch(e){} return 'light'; }
  function applyTheme(t){ document.documentElement.classList.toggle('light', t === 'light'); var icon = document.getElementById('themeIcon'); if(icon) icon.textContent = (t === 'dark') ? '☀' : '◐'; }
  try { applyTheme(resolveTheme()); } catch(e) {}
  function toast(msg){ if(!toastEl) return; toastEl.textContent = msg; toastEl.classList.add('show'); clearTimeout(window.__toastTimer); window.__toastTimer = setTimeout(function(){ toastEl.classList.remove('show'); }, 1400); }
  function applyAlpha(v){ var a = Math.max(0, Math.min(1, v / 100)); document.documentElement.style.setProperty('--card-alpha', a); var el = document.getElementById('spCardAlphaVal'); if(el) el.textContent = Math.round(v) + '%'; try { localStorage.setItem(K_ALPHA, v); } catch(e){} }
  try { var savedAlpha = localStorage.getItem(K_ALPHA); if(savedAlpha !== null){ applyAlpha(savedAlpha); var el = document.getElementById('spCardAlpha'); if(el) el.value = savedAlpha; } } catch(e){}
  function setBg(url){ if(bgLayer) { if(url) bgLayer.style.backgroundImage = 'url(' + url + ')'; else bgLayer.style.backgroundImage = 'none'; } }
  try { var savedBg = localStorage.getItem(K_BG); if(savedBg) setBg(savedBg); } catch(e){}

  // ==================== 4. 内容恢复 ====================
  try {
      var savedContent = localStorage.getItem(K_CONTENT);
      if(savedContent && app) {
          var tempDiv = document.createElement('div');
          tempDiv.innerHTML = savedContent;
          var oldSplash = tempDiv.querySelector('#splash');
          if(oldSplash) oldSplash.remove();
          var savedApp = tempDiv.querySelector('#app');
          if(savedApp) { app.innerHTML = savedApp.innerHTML; } else { app.innerHTML = savedContent; }
      }
  } catch(e) {}
  function serialize(){ if(!app) return ''; var clone = app.cloneNode(true); clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); }); return clone.innerHTML; }

  // 保存浮动元素到 localStorage
  function saveFloaters() {
      try {
          var arr = [];
          document.querySelectorAll('body > .shape, body > .frame, body > .floater').forEach(function(el) {
              var item = {
                  type: el.classList.contains('shape') ? 'shape' : (el.classList.contains('frame') ? 'frame' : 'floater'),
                  panelId: el.dataset.panelId || 'panel-0',
                  left: el.style.left || '',
                  top: el.style.top || '',
                  width: el.style.width || '',
                  height: el.style.height || '',
                  color: el.style.color || '',
                  opacity: el.style.opacity || '',
                  borderRadius: el.style.borderRadius || '',
                  fontSize: el.style.fontSize || '',
                  fontFamily: el.style.fontFamily || '',
                  textAlign: el.style.textAlign || '',
                  fontWeight: el.style.fontWeight || '',
                  fontStyle: el.style.fontStyle || '',
                  textDecoration: el.style.textDecoration || '',
                  link: el.dataset.link || '',
                  fx: el.dataset.fx || '',
                  fxOrder: el.dataset.fxOrder || '0',
                  noBg: el.classList.contains('no-bg') ? true : false
              };
              if (el.classList.contains('shape')) {
                  item.shapeType = el.dataset.shapeType || 'circle';
              }
              if (el.classList.contains('frame')) {
                  var img = el.querySelector('img');
                  item.imgSrc = img ? img.src : '';
              }
              if (el.classList.contains('floater')) {
                  var clone = el.cloneNode(true);
                  var rz = clone.querySelector('.rz'); if (rz) rz.remove();
                  var xdel = clone.querySelector('.xdel'); if (xdel) xdel.remove();
                  item.text = clone.textContent.trim();
              }
              arr.push(item);
          });
          localStorage.setItem(K_FLOATERS, JSON.stringify(arr));
      } catch(e) {}
  }

  // 恢复浮动元素
  function restoreFloaters() {
      try {
          document.querySelectorAll('body > .shape, body > .frame, body > .floater').forEach(function(el) { el.remove(); });
          
          var data = localStorage.getItem(K_FLOATERS);
          if (!data) return;
          var arr = JSON.parse(data);
          if (!arr || !arr.length) return;
          
          arr.forEach(function(item) {
              if (item.type === 'shape') {
                  var el = document.createElement('div');
                  el.className = 'shape';
                  el.dataset.shapeType = item.shapeType;
                  el.style.position = 'absolute';
                  el.style.left = item.left;
                  el.style.top = item.top;
                  el.style.width = item.width;
                  el.style.height = item.height;
                  el.style.zIndex = '999999';
                  el.style.display = 'none';
                  el.style.pointerEvents = 'auto';
                  if (item.color) el.style.color = item.color;
                  if (item.opacity) el.style.opacity = item.opacity;
                  if (item.fontSize) el.style.fontSize = item.fontSize;
                  if (item.fx) el.dataset.fx = item.fx;
                  if (item.fxOrder) el.dataset.fxOrder = item.fxOrder;
                  el.dataset.panelId = item.panelId;
                  el.innerHTML = shapeSVG(item.shapeType) + '<div class="rz"></div><button class="xdel">×</button>';
                  document.body.appendChild(el);
                  el.addEventListener('click', function(e) {
                      if (editMode && !e.target.classList.contains('rz') && !e.target.classList.contains('xdel')) {
                          e.stopPropagation(); selectFloater(el);
                          if(stylePanel) stylePanel.classList.add('show');
                      }
                  });
                  makeMovable(el); bindXDel(el); bindResize(el);
              }
              else if (item.type === 'frame') {
                  var el = document.createElement('div');
                  el.className = 'frame';
                  el.style.position = 'absolute';
                  el.style.left = item.left;
                  el.style.top = item.top;
                  el.style.width = item.width;
                  el.style.height = item.height;
                  el.style.zIndex = '999999';
                  el.style.display = 'none';
                  el.style.pointerEvents = 'auto';
                  if (item.borderRadius) el.style.borderRadius = item.borderRadius;
                  if (item.fontSize) el.style.fontSize = item.fontSize;
                  if (item.fx) el.dataset.fx = item.fx;
                  if (item.fxOrder) el.dataset.fxOrder = item.fxOrder;
                  el.dataset.panelId = item.panelId;
                  var img = document.createElement('img');
                  img.setAttribute('data-edit-img', '');
                  img.src = item.imgSrc || 'data:image/svg+xml,%3Csvg xmlns=%27http://www.w3.org/2000/svg%27 viewBox=%270 0 100 100%27%3E%3Crect width=%27100%27 height=%27100%27 fill=%27%23333%27/%3E%3Ctext x=%2750%27 y=%2755%27 font-size=%2712%27 fill=%27%23999%27 text-anchor=%27middle%27%3E点击换图%3C/text%3E%3C/svg%3E';
                  var rz = document.createElement('div'); rz.className = 'rz';
                  var xdel = document.createElement('button'); xdel.className = 'xdel'; xdel.textContent = '×';
                  el.appendChild(img); el.appendChild(rz); el.appendChild(xdel);
                  document.body.appendChild(el);
                  makeMovable(el); bindXDel(el); bindResize(el); bindImg(img);
              }
              else if (item.type === 'floater') {
                  var el = document.createElement('div');
                  el.className = 'floater';
                  el.contentEditable = 'true';
                  el.style.position = 'absolute';
                  el.style.left = item.left;
                  el.style.top = item.top;
                  el.style.width = item.width;
                  el.style.zIndex = '999999';
                  el.style.display = 'none';
                  el.style.pointerEvents = 'auto';
                  if (item.fontSize) el.style.fontSize = item.fontSize;
                  if (item.fontFamily) el.style.fontFamily = item.fontFamily;
                  if (item.textAlign) el.style.textAlign = item.textAlign;
                  if (item.fontWeight) el.style.fontWeight = item.fontWeight;
                  if (item.fontStyle) el.style.fontStyle = item.fontStyle;
                  if (item.textDecoration) el.style.textDecoration = item.textDecoration;
                  if (item.link) el.dataset.link = item.link;
                  if (item.fx) el.dataset.fx = item.fx;
                  if (item.fxOrder) el.dataset.fxOrder = item.fxOrder;
                  if (item.noBg) el.classList.add('no-bg'); 
                  el.dataset.panelId = item.panelId;
                  el.textContent = item.text || '自由文本';
                  var rz = document.createElement('div'); rz.className = 'rz';
                  var xdel = document.createElement('button'); xdel.className = 'xdel'; xdel.textContent = '×';
                  el.appendChild(rz); el.appendChild(xdel);
                  document.body.appendChild(el);
                  makeMovable(el); bindXDel(el); bindResize(el);
                  // 新增：点击或聚焦时选中浮动文本
                  el.addEventListener('focusin', function() {
                      if(editMode) { selectFloater(el); if(stylePanel) stylePanel.classList.add('show'); }
                  });
                  el.addEventListener('click', function(e) {
                      if(editMode && !e.target.classList.contains('rz') && !e.target.classList.contains('xdel')) {
                          e.stopPropagation(); selectFloater(el); if(stylePanel) stylePanel.classList.add('show');
                      }
                  });
              }
          });
          updateFloatersVisibility();
      } catch(e) { console.log('恢复浮动元素失败:', e); }
  }

  var saveTimer;
  function save(){
      clearTimeout(saveTimer);
      saveTimer = setTimeout(function(){
          if(!app) return;
          try {
              localStorage.setItem(K_CONTENT, serialize());
              saveFloaters();
          } catch(e){ alert('保存失败：浏览器存储空间已满'); }
      }, 300);
  }
  if (app) app.addEventListener('input', function(){ if(editMode) save(); });

  // ==================== 6. 编辑模式 ====================
  function setEdit(on){
    if(isReadonly) return;
    editMode = on;
    document.body.classList.toggle('editing', on);
    document.querySelectorAll('#app [data-edit]').forEach(function(el){
        if(on){ el.setAttribute('contenteditable', 'true'); el.setAttribute('spellcheck', 'false'); }
        else el.removeAttribute('contenteditable');
    });
    
    var mainTextEl = document.getElementById('splashMainText');
    if (mainTextEl) {
        if (on) { mainTextEl.setAttribute('contenteditable', 'true'); mainTextEl.setAttribute('spellcheck', 'false'); }
        else { mainTextEl.removeAttribute('contenteditable'); }
    }

    var btn = document.getElementById('btnEdit');
    if(btn) {
        btn.classList.toggle('on', on);
        var ico = btn.querySelector('.ico'); if(ico) ico.textContent = on ? '✓' : '✎';
        var lbl = btn.querySelector('.lbl'); if(lbl) lbl.textContent = on ? '完成' : '编辑';
    }
    if(!on) {
        var sp = document.getElementById('stylePanel'); if(sp) sp.classList.remove('show');
    }
    if(on) { try { bindAll(); } catch(e){} toast('编辑已开启'); }
    else { save(); toast('已保存'); }
    updateFloatersVisibility();
  }
  var btnEdit = document.getElementById('btnEdit');
  if (btnEdit) btnEdit.addEventListener('click', function(){ setEdit(!editMode); });

  function updateFloatersVisibility() {
    var activePanel = document.querySelector('.panel.active');
    if (!activePanel) return;
    var activePanelId = activePanel.id;
    var splashEl = document.getElementById('splash');
    if (splashEl && splashEl.parentNode) {
        document.querySelectorAll('body > .shape, body > .frame, body > .floater').forEach(function(el) {
            el.style.display = 'none';
        });
        return;
    }
    document.querySelectorAll('body > .shape, body > .frame, body > .floater').forEach(function(el) {
      if (!el.dataset.panelId) el.dataset.panelId = 'panel-0';
      if (el.dataset.panelId === activePanelId) { el.style.display = 'block'; el.style.pointerEvents = 'auto'; }
      else { el.style.display = 'none'; el.style.pointerEvents = 'none'; }
    });
  }

  // ==================== 8. 图片点击 ====================
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
  function bindImg(img){ }

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

  // ==================== 9. 增删行 ====================
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

  // ==================== 10. 样式面板 ====================
  var stylePanel = document.getElementById('stylePanel');
  var btnStyle = document.getElementById('btnStyle'); if(btnStyle) btnStyle.addEventListener('click', function(e){ e.stopPropagation(); if(stylePanel) stylePanel.classList.toggle('show'); });

  // 状态同步：选中普通文本
  function selectText(el){
    activeTextEl = el; activeFloater = null;
    var r1 = document.getElementById('spShapeColorRow'); if(r1) r1.style.display = 'none';
    var r2 = document.getElementById('spShapeOpacityRow'); if(r2) r2.style.display = 'none';
    var r3 = document.getElementById('spFloaterBgRow'); if(r3) r3.style.display = 'none';
    
    var cs = getComputedStyle(el);
    var elSize = document.getElementById('spFontSize'); if(elSize){ elSize.value = parseInt(cs.fontSize) || 14; var v = document.getElementById('spFontSizeVal'); if(v) v.textContent = (parseInt(cs.fontSize) || 14) + 'px'; }
    var elColor = document.getElementById('spTextColor'); if(elColor) elColor.value = rgbToHex(cs.color);
    
    // 同步字体
    var fontSelect = document.getElementById('spFontFamily');
    if(fontSelect) {
        if (cs.fontFamily.includes('Courier')) fontSelect.value = "'Courier New',monospace";
        else if (cs.fontFamily.includes('Georgia')) fontSelect.value = "Georgia,serif";
        else if (cs.fontFamily.includes('Comic')) fontSelect.value = "'Comic Sans MS',cursive";
        else fontSelect.value = 'default';
    }
    // 同步对齐
    document.querySelectorAll('[data-align]').forEach(btn => btn.classList.toggle('on', cs.textAlign === btn.dataset.align));
    // 同步粗体/斜体/下划线
    document.getElementById('spBold').classList.toggle('on', cs.fontWeight === '700' || cs.fontWeight === 'bold');
    document.getElementById('spItalic').classList.toggle('on', cs.fontStyle === 'italic');
    document.getElementById('spUnderline').classList.toggle('on', cs.textDecoration.includes('underline'));
    // 同步链接
    var linkSelect = document.getElementById('spLink');
    if(linkSelect) linkSelect.value = el.dataset.link || '';
    // 同步特效
    var fxSelect = document.getElementById('spFxType');
    if(fxSelect) {
        if (el.classList.contains('fx-slide-up')) fxSelect.value = 'fx-slide-up';
        else if (el.classList.contains('fx-fade-in')) fxSelect.value = 'fx-fade-in';
        else if (el.classList.contains('fx-scale-in')) fxSelect.value = 'fx-scale-in';
        else fxSelect.value = 'none';
    }
    var orderSelect = document.getElementById('spFxOrder');
    if(orderSelect) orderSelect.value = el.dataset.fxOrder || '0';
  }

  // 状态同步：选中浮动元素
  function selectFloater(el) {
    activeFloater = el; activeTextEl = null;
    var isShape = el.classList.contains('shape');
    var isFloater = el.classList.contains('floater');
    
    // 图形颜色 (仅图形显示)
    var r1 = document.getElementById('spShapeColorRow'); if(r1) r1.style.display = isShape ? 'flex' : 'none';
    // 透明度 (图形和浮动文本均显示)
    var r2 = document.getElementById('spShapeOpacityRow'); if(r2) r2.style.display = 'flex';
    // 文本背景 (仅浮动文本显示)
    var r3 = document.getElementById('spFloaterBgRow'); if(r3) r3.style.display = isFloater ? 'flex' : 'none';
    
    var bgToggle = document.getElementById('spFloaterBgToggle');
    if (bgToggle) bgToggle.textContent = !el.classList.contains('no-bg') ? '显示' : '隐藏';
    
    var currentColor = el.style.color || '#4a9eff'; if (currentColor.startsWith('rgb')) currentColor = rgbToHex(currentColor);
    var elColorShape = document.getElementById('spShapeColor'); if(elColorShape && isShape) elColorShape.value = currentColor;
    
    var currentOpacity = el.style.opacity !== '' ? Math.round(parseFloat(el.style.opacity) * 100) : 100;
    var elOp = document.getElementById('spShapeOpacity'); if(elOp) elOp.value = currentOpacity;
    var elOpVal = document.getElementById('spShapeOpacityVal'); if(elOpVal) elOpVal.textContent = currentOpacity + '%';

    var cs = getComputedStyle(el);
    var elSize = document.getElementById('spFontSize'); if(elSize){ elSize.value = parseInt(cs.fontSize) || 14; var v = document.getElementById('spFontSizeVal'); if(v) v.textContent = (parseInt(cs.fontSize) || 14) + 'px'; }
    
    // 同步文本颜色 (针对floater)
    var elColorText = document.getElementById('spTextColor'); if(elColorText) elColorText.value = rgbToHex(cs.color);
    
    // 同步字体
    var fontSelect = document.getElementById('spFontFamily');
    if(fontSelect) {
        if (cs.fontFamily.includes('Courier')) fontSelect.value = "'Courier New',monospace";
        else if (cs.fontFamily.includes('Georgia')) fontSelect.value = "Georgia,serif";
        else if (cs.fontFamily.includes('Comic')) fontSelect.value = "'Comic Sans MS',cursive";
        else fontSelect.value = 'default';
    }
    // 同步对齐、粗体、斜体、下划线
    document.querySelectorAll('[data-align]').forEach(btn => btn.classList.toggle('on', cs.textAlign === btn.dataset.align));
    document.getElementById('spBold').classList.toggle('on', cs.fontWeight === '700' || cs.fontWeight === 'bold');
    document.getElementById('spItalic').classList.toggle('on', cs.fontStyle === 'italic');
    document.getElementById('spUnderline').classList.toggle('on', cs.textDecoration.includes('underline'));
    
    var linkSelect = document.getElementById('spLink');
    if(linkSelect) linkSelect.value = el.dataset.link || '';
    
    var fxSelect = document.getElementById('spFxType');
    if(fxSelect) {
        if (el.classList.contains('fx-slide-up')) fxSelect.value = 'fx-slide-up';
        else if (el.classList.contains('fx-fade-in')) fxSelect.value = 'fx-fade-in';
        else if (el.classList.contains('fx-scale-in')) fxSelect.value = 'fx-scale-in';
        else fxSelect.value = 'none';
    }
    var orderSelect = document.getElementById('spFxOrder');
    if(orderSelect) orderSelect.value = el.dataset.fxOrder || '0';
  }

  function rgbToHex(c){ if(!c) return '#f0f0f0'; var m = c.match(/\d+/g); if(!m) return '#f0f0f0'; return '#' + m.slice(0,3).map(function(x){ return ('0'+(+x).toString(16)).slice(-2); }).join(''); }

  var spFontSize = document.getElementById('spFontSize'); 
  if(spFontSize) spFontSize.addEventListener('input', function(e){ 
      var size = e.target.value + 'px';
      var v = document.getElementById('spFontSizeVal'); if(v) v.textContent = size;
      if(activeTextEl) activeTextEl.style.fontSize = size; 
      if(activeFloater) activeFloater.style.fontSize = size; 
      save(); 
  });
  
  var spTextColor = document.getElementById('spTextColor'); 
  if(spTextColor) spTextColor.addEventListener('input', function(e){ 
      if(activeTextEl) activeTextEl.style.color = e.target.value; 
      if(activeFloater) activeFloater.style.color = e.target.value;
      save(); 
  });

  var spFontFamily = document.getElementById('spFontFamily');
  if (spFontFamily) spFontFamily.addEventListener('change', function(e) {
      var font = e.target.value === 'default' ? '' : e.target.value;
      if (activeTextEl) activeTextEl.style.fontFamily = font;
      if (activeFloater) activeFloater.style.fontFamily = font;
      save();
  });

  document.querySelectorAll('.sp-btn[data-align]').forEach(function(btn) {
      btn.addEventListener('click', function() {
          var align = this.dataset.align;
          document.querySelectorAll('.sp-btn[data-align]').forEach(b => b.classList.remove('on'));
          this.classList.add('on');
          if (activeTextEl) activeTextEl.style.textAlign = align;
          if (activeFloater) activeFloater.style.textAlign = align;
          save();
      });
  });

  var spBold = document.getElementById('spBold');
  if (spBold) spBold.addEventListener('click', function() {
      if (!activeTextEl && !activeFloater) return;
      var el = activeTextEl || activeFloater;
      var isBold = el.style.fontWeight === 'bold' || el.style.fontWeight === '700';
      el.style.fontWeight = isBold ? 'normal' : 'bold';
      this.classList.toggle('on', !isBold);
      save();
  });
  var spItalic = document.getElementById('spItalic');
  if (spItalic) spItalic.addEventListener('click', function() {
      if (!activeTextEl && !activeFloater) return;
      var el = activeTextEl || activeFloater;
      var isItalic = el.style.fontStyle === 'italic';
      el.style.fontStyle = isItalic ? 'normal' : 'italic';
      this.classList.toggle('on', !isItalic);
      save();
  });
  var spUnderline = document.getElementById('spUnderline');
  if (spUnderline) spUnderline.addEventListener('click', function() {
      if (!activeTextEl && !activeFloater) return;
      var el = activeTextEl || activeFloater;
      var isUnderline = el.style.textDecoration.includes('underline');
      el.style.textDecoration = isUnderline ? 'none' : 'underline';
      this.classList.toggle('on', !isUnderline);
      save();
  });

  var spLink = document.getElementById('spLink');
  if (spLink) spLink.addEventListener('change', function(e) {
      var val = e.target.value;
      if (activeTextEl) {
          if (val) activeTextEl.dataset.link = val;
          else activeTextEl.removeAttribute('data-link');
      }
      if (activeFloater) {
          if (val) activeFloater.dataset.link = val;
          else activeFloater.removeAttribute('data-link');
      }
      save();
  });

  var spFxType = document.getElementById('spFxType');
  if (spFxType) spFxType.addEventListener('change', function(e) {
      var val = e.target.value;
      if (activeTextEl) {
          activeTextEl.classList.remove('fx-slide-up', 'fx-fade-in', 'fx-scale-in', 'fx-visible', 'fx-hidden');
          if (val !== 'none') { activeTextEl.classList.add(val); activeTextEl.classList.add('fx-hidden'); }
      }
      if (activeFloater) {
          activeFloater.classList.remove('fx-slide-up', 'fx-fade-in', 'fx-scale-in', 'fx-visible', 'fx-hidden');
          if (val !== 'none') { activeFloater.classList.add(val); activeFloater.classList.add('fx-hidden'); }
      }
      save();
  });
  var spFxOrder = document.getElementById('spFxOrder');
  if (spFxOrder) spFxOrder.addEventListener('change', function(e) {
      if (activeTextEl) activeTextEl.dataset.fxOrder = e.target.value;
      if (activeFloater) activeFloater.dataset.fxOrder = e.target.value;
      save();
  });

  var spShapeColor = document.getElementById('spShapeColor'); if(spShapeColor) spShapeColor.addEventListener('input', function(e){ if(!activeFloater) return; activeFloater.style.color = e.target.value; save(); });
  var spShapeOpacity = document.getElementById('spShapeOpacity'); if(spShapeOpacity) spShapeOpacity.addEventListener('input', function(e){ if(!activeFloater) return; activeFloater.style.opacity = e.target.value / 100; var v = document.getElementById('spShapeOpacityVal'); if(v) v.textContent = e.target.value + '%'; save(); });

  var spFloaterBgToggle = document.getElementById('spFloaterBgToggle');
  if (spFloaterBgToggle) {
      spFloaterBgToggle.addEventListener('click', function() {
          if (!activeFloater) return;
          activeFloater.classList.toggle('no-bg');
          var hasBg = !activeFloater.classList.contains('no-bg');
          this.textContent = hasBg ? '显示' : '隐藏';
          save();
          toast(hasBg ? '已显示背景' : '已隐藏背景');
      });
  }

  var spSplashTitleSize = document.getElementById('spSplashTitleSize');
  if (spSplashTitleSize) spSplashTitleSize.addEventListener('input', function(e) {
      var size = e.target.value;
      var mainTextEl = document.getElementById('splashMainText');
      if (mainTextEl) mainTextEl.style.fontSize = size + 'px';
      var v = document.getElementById('spSplashTitleSizeVal');
      if (v) v.textContent = size + 'px';
  });

  document.addEventListener('focusin', function(e){ if(!editMode || isReadonly) return; var el = e.target.closest('#app [contenteditable="true"]'); if(el){ selectText(el); if(stylePanel) stylePanel.classList.add('show'); } });

  // ==================== 11. 图形创建 ====================
  function shapeSVG(type){ var s = { circle:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><circle cx="50" cy="50" r="45" fill="currentColor"/></svg>', square:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" fill="currentColor"/></svg>', rounded:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" rx="20" fill="currentColor"/></svg>', triangle:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,95 5,95" fill="currentColor"/></svg>', star:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 61,38 96,38 68,59 79,92 50,72 21,92 32,59 4,38 39,38" fill="currentColor"/></svg>', heart:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M50,88 C20,65 5,45 5,30 C5,15 18,5 32,5 C41,5 48,10 50,18 C52,10 59,5 68,5 C82,5 95,15 95,30 C95,45 80,65 50,88 Z" fill="currentColor"/></svg>', arrow:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M5,40 L60,40 L60,15 L95,50 L60,85 L60,60 L5,60 Z" fill="currentColor"/></svg>', diamond:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,50 50,95 5,50" fill="currentColor"/></svg>', hexagon:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 93,27 93,73 50,95 7,73 7,27" fill="currentColor"/></svg>', cross:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M35,5 L65,5 L65,35 L95,35 L95,65 L65,65 L65,95 L35,95 L35,65 L5,65 L5,35 L35,35 Z" fill="currentColor"/></svg>' }; return s[type] || s.circle; }
  function getCenterPos(elWidth, elHeight) { var left = (window.innerWidth / 2) - (elWidth / 2) + window.scrollX; var top = (window.innerHeight / 2) - (elHeight / 2) + window.scrollY; return { left: left, top: top }; }
  function createFloatingElement(className, innerHTML, isContentEditable) {
      var el = document.createElement('div'); el.className = className; if (isContentEditable) el.contentEditable = 'true'; el.innerHTML = innerHTML;
      var pos = getCenterPos(100, 100); el.style.position = 'absolute'; el.style.left = pos.left + 'px'; el.style.top = pos.top + 'px'; el.style.zIndex = '999999'; el.style.display = 'block'; el.style.pointerEvents = 'auto';
      var activePanel = document.querySelector('.panel.active'); if (activePanel) el.dataset.panelId = activePanel.id;
      document.body.appendChild(el); return el;
  }
  function makeShape(type){ try { var el = createFloatingElement('shape', shapeSVG(type) + '<div class="rz"></div><button class="xdel">×</button>', false); el.dataset.shapeType = type; el.style.width = '80px'; el.style.height = '80px'; el.style.color = getComputedStyle(document.documentElement).getPropertyValue('--accent').trim() || '#4a9eff'; el.addEventListener('click', function(e) { if(editMode && !e.target.classList.contains('rz') && !e.target.classList.contains('xdel')) { e.stopPropagation(); selectFloater(el); if(stylePanel) stylePanel.classList.add('show'); } }); makeMovable(el); bindXDel(el); bindResize(el); save(); toast('已添加图形'); } catch(e) { alert('添加图形失败: ' + e.message); } }
  function makeFrame(shape){ try { var imgHtml = '<img data-edit-img src="data:image/svg+xml,%3Csvg xmlns=\'http://www.w3.org/2000/svg\' viewBox=\'0 0 100 100\'%3E%3Crect width=\'100\' height=\'100\' fill=\'%23333\'/%3E%3Ctext x=\'50\' y=\'55\' font-size=\'12\' fill=\'%23999\' text-anchor=\'middle\' font-family=\'sans-serif\'%3E点击换图%3C/text%3E%3C/svg%3E" alt="图片">'; var el = createFloatingElement('frame', imgHtml