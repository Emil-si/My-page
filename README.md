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
    --card: rgba(25,25,30,0.55);
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
    --card: rgba(255,255,255,0.7);
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

  /* 背景层 */
  #bgLayer{
    position:fixed; inset:0; z-index:0;
    background-color:var(--bg);
    background-size:cover; background-position:center; background-repeat:no-repeat;
    pointer-events:none; transition:background-color .3s;
  }

  /* 顶部工具栏 */
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

  /* 顶部导航 */
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

  /* 合集下拉 */
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

  /* 主体 */
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

  /* 封面 */
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

  /* 头像 */
  .profile{display:flex; align-items:flex-end; gap:12px; margin-top:-40px; position:relative; z-index:2; padding-bottom:10px;}
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

  /* 图片网格 */
  .grid{
    display:grid; grid-template-columns:repeat(3, 1fr);
    gap:6px; margin:12px 0;
  }
  .grid img{
    width:100%; aspect-ratio:1; object-fit:cover;
    border-radius:10px; display:block; cursor:pointer;
    background:rgba(127,127,127,0.1);
  }

  /* 引文 */
  .quote{
    padding:14px; border:1px solid var(--border); border-radius:14px;
    background:rgba(127,127,127,0.04);
    font-size:13px; line-height:1.7; color:var(--sub); font-style:italic;
    margin:12px 0;
  }

  /* 发帖框 */
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

  /* 动态卡片 */
  .feed{display:flex; gap:10px; margin-bottom:10px;}
  .feed .fav{width:36px; height:36px; border-radius:50%; overflow:hidden; background:#333; flex-shrink:0;}
  .feed .fav img{width:100%; height:100%; object-fit:cover; display:block;}
  .feed .meta{flex:1;}
  .feed .meta .n{font-size:13px; font-weight:600;}
  .feed .meta .h{font-size:11px; color:var(--sub); font-family:monospace;}
  .feed .time{font-size:11px; color:var(--sub);}
  .feedtext{font-size:14px; line-height:1.6; margin-bottom:8px;}

  /* 自由文本块 */
  .textblock{
    padding:10px 30px 10px 12px; border:1px dashed var(--border);
    border-radius:12px; background:rgba(127,127,127,0.04);
    position:relative; font-size:14px; line-height:1.6;
    margin-bottom:8px; display:flex; align-items:center; justify-content:center;
    text-align:center; min-height:40px;
  }
  .textblock:focus{outline:2px solid var(--accent); outline-offset:2px;}

  /* 分隔线 */
  .divider{
    padding:10px 0; display:flex; align-items:center;
    position:relative; margin-bottom:4px;
  }
  .divider .line{
    height:1px; background:var(--border); width:100%;
    border-radius:1px; transition:all .2s;
  }
  body.editing .divider:hover .line{background:var(--accent);}

  /* 卡片内块工具条 */
  .bwrap{position:relative;}
  .btools{
    position:absolute; top:-10px; right:0; z-index:20;
    display:none; gap:3px;
    background:rgba(0,0,0,0.55);
    padding:3px; border-radius:6px;
  }
  html.light .btools{background:rgba(255,255,255,0.85);}
  body.editing .bwrap:hover .btools{display:flex;}
  .btools button{
    width:24px; height:24px; border-radius:4px;
    border:none; background:var(--accent); color:var(--accent-text);
    font-size:12px; cursor:pointer; padding:0; line-height:1;
    display:flex; align-items:center; justify-content:center;
  }
  .btools button.del{background:#e5484d;}
  .btools button.dvd{background:#7d90b8; font-size:15px;}

  /* 图形 / 图片框 / 浮动文本 */
  .shape{
    position:absolute; z-index:998;
    display:none; cursor:move;
    pointer-events:auto; touch-action:none; user-select:none;
    filter:drop-shadow(0 4px 12px rgba(0,0,0,.3));
  }
  .shape svg{width:100%; height:100%; display:block; pointer-events:none;}
  .frame{
    position:absolute; z-index:998;
    overflow:hidden; background:#333;
    border:2px solid var(--border);
    display:none; cursor:move;
    pointer-events:auto; touch-action:none; user-select:none;
    box-shadow:0 6px 24px rgba(0,0,0,.3);
  }
  .frame img{width:100%; height:100%; object-fit:cover; display:block; pointer-events:none;}
  .floater{
    position:absolute; z-index:998; min-width:100px; min-height:40px;
    padding:12px; border-radius:12px; border:2px dashed var(--accent);
    background:var(--card);
    -webkit-backdrop-filter:blur(12px); backdrop-filter:blur(12px);
    color:var(--text); font-size:14px; line-height:1.5;
    word-break:break-word; cursor:move; outline:none;
    display:none; pointer-events:auto; touch-action:none;
  }
  body.editing .shape, body.editing .frame, body.editing .floater{display:block;}

  /* 元素删除按钮 */
  .xdel{
    position:absolute; top:-10px; left:-10px;
    width:22px; height:22px; border-radius:50%;
    background:#e5484d; color:#fff; border:none;
    font-size:12px; cursor:pointer; line-height:1;
    display:none; align-items:center; justify-content:center;
    padding:0; z-index:5;
  }
  body.editing .shape:hover .xdel,
  body.editing .frame:hover .xdel,
  body.editing .floater:hover .xdel{display:flex;}

  /* 拖拽缩放手柄 */
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

  /* 选择器面板 */
  #shapePicker, #framePicker{
    position:fixed;
    top:calc(110px + var(--safe-top));
    left:50%; transform:translateX(-50%);
    z-index:300;
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

  /* 底部翻页 */
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

  /* 底部额外按钮（加卡片/加文字） */
  .extras{display:none; gap:6px; flex-wrap:wrap; margin-bottom:10px;}
  body.editing .extras{display:flex;}
  .exbtn{
    padding:8px 14px; border-radius:12px;
    border:1px dashed var(--border); background:transparent;
    color:var(--sub); font-size:12px; font-family:monospace;
    cursor:pointer; transition:all .2s;
  }
  .exbtn:hover{background:rgba(127,127,127,0.08); color:var(--accent); border-color:var(--accent);}

  /* 音乐按钮 */
  #musicBtn{
    position:fixed; bottom:calc(20px + var(--safe-bottom)); right:14px;
    width:48px; height:48px; border-radius:50%;
    background:var(--card); border:1px solid var(--border);
    -webkit-backdrop-filter:blur(20px); backdrop-filter:blur(20px);
    box-shadow:0 6px 24px rgba(0,0,0,.35);
    display:flex; align-items:center; justify-content:center;
    color:var(--text); font-size:20px; cursor:pointer; z-index:200;
    touch-action:none; user-select:none;
  }
  #musicBtn.playing{animation:spin 4s linear infinite; background:var(--accent); color:var(--accent-text);}
  @keyframes spin{100%{transform:rotate(360deg);}}

  /* 编辑提示 */
  body.editing [data-edit]{
    outline:1px dashed transparent; outline-offset:4px;
    border-radius:4px; cursor:text; transition:outline .2s;
  }
  body.editing [data-edit]:hover{outline-color:var(--accent);}
  body.editing [data-edit]:focus{
    outline:2px solid var(--accent);
    background:rgba(74,158,255,0.08);
  }

  /* 图片库 */
  #picker{
    position:fixed; left:0; right:0; bottom:0;
    height:52vh; z-index:500;
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
    background-bottom:transparent; border:1px solid var(--border));
);
    color:var(--text); font-size: 20px; width:30px; height:30px;
    border-radius:8px; cursor:pointer; line-height:1; padding:0;
  }
  .pgrid{
    flex:1; overflow-y:auto; padding:12px;
    display:grid; grid-template-columns:repeat(4, 1fr); gap:6px;
    padding-bottom:calc(12px + var(--safe }
  .pgrid img{
    width:100%; aspect-ratio:1; object-fit:cover;
    border-radius:8px; cursor:pointer;
    border:2px solid transparent; transition:border-color .2s;
    background:rgba(127,127,127,0.1);
  }
  .pgrid img:hover{border-color:var(--accent);}

  /* 提示 */
  #toast{
    position:fixed; left:50%; bottom:calc(80px + var(--safe-bottom));
    transform:translateX(-50%) translateY(20px);
    background:var(--accent); color:var(--accent-text);
    padding:9px 18px; border-radius:18px;
    font-size:12px; opacity:0; pointer-events:none;
    transition:all .3s; z-index:900;
  }
  #toast.show{opacity:1; transform:translateX(-50%) translateY(0);}

  /* 音乐弹窗 */
  #musicDialog{
    position:fixed; inset:0; z-index:1000;
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

  /* 微信/老浏览器兜底 */
  @supports not ((backdrop-filter:blur(1px)) or (-webkit-backdrop-filter:blur(1px))){
    #toolbar, #topnav, .card, #musicBtn, #picker, .mcard, .navbtn, #collectionMenu,
    #shapePicker, #framePicker{
      background-color:rgba(20,20,25,0.96) !important;
      background-image:none !important;
    }
    html.light #toolbar, html.light #topnav, html.light .card,
    html.light #musicBtn, html.light #picker, html.light .mcard, html.light .navbtn,
    html.light #collectionMenu, html.light #shapePicker, html.light #framePicker{
      background-color:rgba(255,255,255,0.97) !important;
      background-image:none !important;
    }
  }

  /* 手机优化 */
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
    .profile{margin-top:-32px;}
    .feedtext{font-size:13px;}
    .pgrid{grid-template-columns:repeat(3, 1fr);}
    .pick{width:38px; height:38px;}
    .pick svg{width:22px; height:22px;}
  }
</style>
</head>
<body>

<div id="bgLayer"></div>

<div id="toolbar">
  <button class="btn" id="btnEdit"><span class="ico">✎</span><span class="lbl">编辑</span></button>
  <button class="btn edit-only" id="btnBg"><span class="ico">🖼️</span><span class="lbl">背景</span></button>
  <button class="btn edit-only" id="btnAddFloat"><span class="ico">＋</span><span class="lbl">文本</span></button>
  <button class="btn edit-only" id="btnAddShape"><span class="ico">◆</span><span class="lbl">图形</span></button>
  <button class="btn edit-only" id="btnAddFrame"><span class="ico">🖼️</span><span class="lbl">图片框</span></button>
  <button class="btn edit-only" id="btnAddPanel"><span class="ico">📄</span><span class="lbl">新页面</span></button>
  <button class="btn edit-only" id="btnReset"><span class="ico">⟲</span><span class="lbl">重置</span></button>
  <span class="spacer"></span>
  <button class="btn" id="btnTheme"><span class="ico" id="themeIcon">☀</span></button>
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

<div id="app">
  <div id="floatLayer" style="position:absolute; inset:0; pointer-events:none; z-index:997;"></div>

  <!-- ========== 面板 0 ========== -->
  <section class="panel active" id="panel-0">
    <div class="card">
      <div class="cover">
        <img data-edit-img id="coverImg" src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 160 90'%3E%3Crect width='160' height='90' fill='%23111'/%3E%3Ctext x='80' y='49' font-size='7' fill='%23555' text-anchor='middle' font-family='monospace'%3E点击更换封面%3C/text%3E%3C/svg%3E" alt="封面">
      </div>
      <div class="profile">
        <div class="avatar">
          <img data-edit-img id="avatarImg" src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23222'/%3E%3Ctext x='50' y='68' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EH%3C/text%3E%3C/svg%3E" alt="头像">
        </div>
        <div class="namebox">
          <div class="name" data-edit>肆</div>
          <div class="handle" data-edit>@InfernalBeyond.com</div>
        </div>
      </div>
      <div class="bwrap">
        <div class="stats">
          <div><b data-edit>520</b> 正在关注</div>
          <div><b data-edit>1314</b> 关注者</div>
        </div>
        <div class="btools">
          <button onclick="addTextBlock(this)" title="加文字块">＋</button>
          <button class="dvd" onclick="addDivider(this)" title="加分隔线">—</button>
        </div>
      </div>
      <div class="grid" id="grid0">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='26' fill='%23888' text-anchor='middle'%3E1%3C/text%3E%3C/svg%3E" alt="图1">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='26' fill='%23888' text-anchor='middle'%3E2%3C/text%3E%3C/svg%3E" alt="图2">
        <img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect width='100' height='100' fill='%23333'/%3E%3Ctext x='50' y='55' font-size='26' fill='%23888' text-anchor='middle'%3E3%3C/text%3E%3C/svg%3E" alt="图3">
      </div>
      <div class="bwrap">
        <div class="quote" data-edit>——They say time devours all things but say nothing about what becomes of the things as time digest them.</div>
        <div class="btools">
          <button onclick="addTextBlock(this)" title="加文字块">＋</button>
          <button class="dvd" onclick="addDivider(this)" title="加分隔线">—</button>
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
      <div class="bwrap">
        <div class="feed">
          <div class="fav"><img data-edit-img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='50' fill='%23555'/%3E%3Ctext x='50' y='68' font-size='40' fill='%23fff' text-anchor='middle' font-family='monospace'%3EW%3C/text%3E%3C/svg%3E" alt="动态头像"></div>
          <div class="meta">
            <div class="n" data-edit>肆</div>
            <div class="h" data-edit>@InfernalBeyond.com</div>
          </div>
          <div class="time" data-edit>7小时前</div>
        </div>
        <div class="feedtext" data-edit>我要搓死了</div>
        <div class="btools">
          <button onclick="addTextBlock(this)" title="加文字块">＋</button>
          <button class="dvd" onclick="addDivider(this)" title="加分隔线">—</button>
        </div>
      </div>
    </div>

    <div class="extras">
      <button class="exbtn" onclick="addTextBlock(this, 'panel-0')">＋ 自由文本块</button>
      <button class="exbtn" onclick="addDivider(this, 'panel-0')">— 分隔线</button>
    </div>

    <div class="pagenav">
      <div class="spacer30"></div>
      <button class="navbtn" onclick="switchTab('panel-1')">→</button>
    </div>
  </section>

  <!-- ========== 面板 1 ========== -->
  <section class="panel" id="panel-1">
    <div class="card">
      <div class="bwrap">
        <div class="feedtext" data-edit>这里是个人信息区域，可以在编辑模式下自由修改文字和图片。</div>
        <div class="btools">
          <button onclick="addTextBlock(this)" title="加文字块">＋</button>
          <button class="dvd" onclick="addDivider(this)" title="加分隔线">—</button>
        </div>
      </div>
    </div>
    <div class="extras">
      <button class="exbtn" onclick="addTextBlock(this, 'panel-1')">＋ 自由文本块</button>
      <button class="exbtn" onclick="addDivider(this, 'panel-1')">— 分隔线</button>
    </div>
    <div class="pagenav">
      <button class="navbtn" onclick="switchTab('panel-0')">←</button>
      <button class="navbtn" onclick="switchTab('panel-2')">→</button>
    </div>
  </section>

  <!-- ========== 面板 2 ========== -->
  <section class="panel" id="panel-2">
    <div class="card">
      <div class="bwrap">
        <div class="feedtext" data-edit>这里是个人风格展示区域，可以放你的作品或爱好。</div>
        <div class="btools">
          <button onclick="addTextBlock(this)" title="加文字块">＋</button>
          <button class="dvd" onclick="addDivider(this)" title="加分隔线">—</button>
        </div>
      </div>
    </div>
    <div class="extras">
      <button class="exbtn" onclick="addTextBlock(this, 'panel-2')">＋ 自由文本块</button>
      <button class="exbtn" onclick="addDivider(this, 'panel-2')">— 分隔线</button>
    </div>
    <div class="pagenav">
      <button class="navbtn" onclick="switchTab('panel-1')">←</button>
      <button class="navbtn" onclick="switchTab('panel-3')">→</button>
    </div>
  </section>

  <!-- ========== 面板 3 ========== -->
  <section class="panel" id="panel-3">
    <div class="card">
      <div class="bwrap">
        <div class="feedtext" data-edit>这里是分类区域，可以放你的收藏或分类信息。</div>
        <div class="btools">
          <button onclick="addTextBlock(this)" title="加文字块">＋</button>
          <button class="dvd" onclick="addDivider(this)" title="加分隔线">—</button>
        </div>
      </div>
      <div class="bwrap">
        <div class="feedtext linktext" data-edit data-link="" style="margin-top:10px;cursor:pointer;text-decoration:underline;">→ 点击跳转到指定页面（在样式面板选择）</div>
        <div class="btools">
          <button onclick="addTextBlock(this)" title="加文字块">＋</button>
          <button class="dvd" onclick="addDivider(this)" title="加分隔线">—</button>
        </div>
      </div>
    </div>
    <div class="extras">
      <button class="exbtn" onclick="addTextBlock(this, 'panel-3')">＋ 自由文本块</button>
      <button class="exbtn" onclick="addDivider(this, 'panel-3')">— 分隔线</button>
    </div>
    <div class="pagenav">
      <button class="navbtn" onclick="switchTab('panel-2')">←</button>
      <div class="spacer30"></div>
    </div>
  </section>

</div>

<div id="musicBtn">♪</div>

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
  <div class="ph">
    <div class="ptitle">选择图片</div>
    <button class="pclose" id="pickerClose">×</button>
  </div>
  <div class="pgrid" id="pickerGrid"></div>
</div>

<div id="musicDialog">
  <div class="mcard">
    <h3>更换音乐</h3>
    <input type="text" id="musicInput" placeholder="music.mp3 或 https://.../song.mp3">
    <div class="row">
      <button id="musicCancel">取消</button>
      <button class="primary" id="musicOk">确定</button>
    </div>
  </div>
</div>

<div id="toast"></div>

<audio id="bgMusic" loop preload="none"></audio>

<script>
(function(){
  'use strict';

  /* ==================== 配置 ==================== */
  var IMG_BASE = 'https://emil-si.github.io/My-page/';
  var IMAGES = [
    'IMG_6273.jpeg','IMG_6334.jpeg','IMG_6394.jpeg','IMG_6395.jpeg',
    'IMG_6396.jpeg','IMG_7871.jpeg','IMG_7872.jpeg','IMG_7873.jpeg',
    'IMG_7874.jpeg','IMG_8450.jpeg','IMG_8451.jpeg','IMG_8452.jpeg',
    'IMG_8453.jpeg','IMG_8454.jpeg','IMG_8455.jpeg','IMG_8456.jpeg',
    'IMG_9006.png','IMG_9007.png','IMG_9008.png','IMG_9009.png',
    'IMG_9010.png','IMG_9011.png','IMG_9012.png','IMG_9013.png',
    'IMG_9014.png','IMG_9016.png'
  ];
  /* ================================================= */

  var isReadonly = false;
  try {
    var qp = new URLSearchParams(location.search);
    isReadonly = qp.has('view') || qp.has('readonly');
  } catch(e){}
  if(isReadonly) document.body.classList.add('readonly');

  var K_THEME = 'myPage.theme.v2';
  var K_BG    = 'myPage.bg.v2';
  var K_CONTENT = 'myPage.content.v3';
  var K_MUSIC = 'myPage.music.v2';

  var app = document.getElementById('app');
  var bgLayer = document.getElementById('bgLayer');
  var floatLayer = document.getElementById('floatLayer');
  var toastEl = document.getElementById('toast');
  var editMode = false;
  var activeImg = null;
  var bgMode = false;
  var activeTextEl = null;
  var DEFAULT_HTML = app.innerHTML;

  // ==================== 主题 ====================
  function resolveTheme(){
    var s = null;
    try { s = localStorage.getItem(K_THEME); } catch(e){}
    if(s === 'dark' || s === 'light') return s;
    try {
      if(window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) return 'dark';
    } catch(e){}
    return 'light';
  }
  function applyTheme(t){
    document.documentElement.classList.toggle('light', t === 'light');
    document.getElementById('themeIcon').textContent = (t === 'dark') ? '☀' : '◐';
  }
  applyTheme(resolveTheme());
  document.getElementById('btnTheme').addEventListener('click', function(){
    var nowDark = !document.documentElement.classList.contains('light');
    var next = nowDark ? 'light' : 'dark';
    applyTheme(next);
    try { localStorage.setItem(K_THEME, next); } catch(e){}
    toast(next === 'dark' ? '已切换至暗夜' : '已切换至白天');
  });

  // ==================== 提示 ====================
  var toastTimer;
  function toast(msg){
    toastEl.textContent = msg;
    toastEl.classList.add('show');
    clearTimeout(toastTimer);
    toastTimer = setTimeout(function(){ toastEl.classList.remove('show'); }, 1500);
  }

  // ==================== 背景 ====================
  function setBg(url){
    if(url) bgLayer.style.backgroundImage = 'url(' + url + ')';
    else bgLayer.style.backgroundImage = 'none';
  }
  try {
    var savedBg = localStorage.getItem(K_BG);
    if(savedBg) setBg(savedBg);
  } catch(e){}

  // ==================== 内容恢复 ====================
  try {
    var savedContent = localStorage.getItem(K_CONTENT);
    if(savedContent) app.innerHTML = savedContent;
  } catch(e){}

  // 恢复后重新绑定所有内容
  bindAll();

  function serialize(){
    var clone = app.cloneNode(true);
    clone.querySelectorAll('[contenteditable]').forEach(function(el){ el.removeAttribute('contenteditable'); });
    clone.querySelectorAll('.shape.selected').forEach(function(el){ el.classList.remove('selected'); });
    return clone.innerHTML;
  }

  var saveTimer;
  function save(){
    clearTimeout(saveTimer);
    saveTimer = setTimeout(function(){
      try {
        localStorage.setItem(K_CONTENT, serialize());
      } catch(e){
        alert('保存失败：浏览器存储空间已满\n\n解决方法：\n1. 点「重置」清空\n2. 使用 GitHub 图片库（不占空间）');
      }
    }, 300);
  }

  app.addEventListener('input', function(){ if(editMode) save(); });

  // ==================== 编辑模式 ====================
  function setEdit(on){
    if(isReadonly) return;
    editMode = on;
    document.body.classList.toggle('editing', on);
    document.querySelectorAll('[data-edit]').forEach(function(el){
      if(on){ el.setAttribute('contenteditable', 'true'); el.setAttribute('spellcheck', 'false'); }
      else el.removeAttribute('contenteditable');
    });
    document.querySelectorAll('.shape, .frame, .floater').forEach(function(el){
      if(on){ el.style.display = ''; } else { el.style.display = 'none'; }
    });
    var btn = document.getElementById('btnEdit');
    btn.classList.toggle('on', on);
    btn.querySelector('.ico').textContent = on ? '✓' : '✎';
    btn.querySelector('.lbl').textContent = on ? '完成' : '编辑';
    if(on) { bindAll(); toast('编辑已开启'); }
    else { save(); toast('已保存'); }
  }
  document.getElementById('btnEdit').addEventListener('click', function(){ setEdit(!editMode); });

  // ==================== 图片绑定 + 点击打开图片库 ====================
  function bindImg(img){
    if(!img || img.dataset.bound === '1') return;
    img.dataset.bound = '1';

    var t0 = 0, x0 = 0, y0 = 0, moved = false;

    function down(e){
      t0 = Date.now(); moved = false;
      var p = e.touches ? e.touches[0] : e;
      x0 = p.clientX; y0 = p.clientY;
    }
    function move(e){
      var p = e.touches ? e.touches[0] : e;
      if(Math.abs(p.clientX - x0) > 10 || Math.abs(p.clientY - y0) > 10) moved = true;
    }
    function up(e){
      if(!editMode || isReadonly) return;
      if(moved) return;
      if(Date.now() - t0 > 700) return;
      e.preventDefault();
      e.stopPropagation();
      openPicker(img, false);
    }

    img.addEventListener('mousedown', down, true);
    img.addEventListener('touchstart', down, true);
    img.addEventListener('mousemove', move, true);
    img.addEventListener('touchmove', move, true);
    img.addEventListener('click', up, true);
  }

  // ==================== 图片库 ====================
  var picker = document.getElementById('picker');
  var pickerGrid = document.getElementById('pickerGrid');

  function openPicker(img, isBg){
    activeImg = img || null;
    bgMode = !!isBg;
    pickerGrid.innerHTML = '';
    IMAGES.forEach(function(name){
      var url = IMG_BASE + name;
      var el = document.createElement('img');
      el.src = url;
      el.alt = name;
      el.loading = 'lazy';
      el.addEventListener('click', function(e){
        e.preventDefault();
        e.stopPropagation();
        if(bgMode){
          setBg(url);
          try { localStorage.setItem(K_BG, url); } catch(e){}
          toast('背景已更新');
        } else if(activeImg){
          activeImg.src = url;
          save();
          toast('图片已更新');
        }
        closePicker();
      });
      pickerGrid.appendChild(el);
    });
    picker.classList.add('show');
  }
  function closePicker(){
    picker.classList.remove('show');
    activeImg = null;
    bgMode = false;
  }
  document.getElementById('pickerClose').addEventListener('click', closePicker);
  document.getElementById('btnBg').addEventListener('click', function(e){
    e.preventDefault(); e.stopPropagation();
    openPicker(null, true);
  });

  // ==================== 文字块 & 分隔线 ====================
  window.addTextBlock = function(btn, panelId){
    var wrap = btn.closest('.bwrap') || btn.closest('.extras');
    var container;
    if(panelId){
      container = document.getElementById(panelId).querySelector('.card') || document.getElementById(panelId);
    } else if(wrap && wrap.classList.contains('bwrap')){
      container = wrap.parentNode;
    } else {
      container = btn.closest('.card') || btn.closest('.panel');
    }
    if(!container) return;

    var newWrap = document.createElement('div');
    newWrap.className = 'bwrap';
    newWrap.style.marginTop = '8px';
    newWrap.innerHTML = '<div class="textblock" data-edit>点击输入新内容...</div>' +
      '<div class="btools">' +
        '<button onclick="addTextBlock(this)">＋</button>' +
        '<button class="dvd" onclick="addDivider(this)">—</button>' +
        '<button class="del" onclick="deleteBlock(this)">×</button>' +
      '</div>';

    if(wrap && wrap.parentNode){
      wrap.parentNode.insertBefore(newWrap, wrap.nextSibling);
    } else {
      container.appendChild(newWrap);
    }

    if(editMode){
      var tb = newWrap.querySelector('.textblock');
      tb.setAttribute('contenteditable', 'true');
      activeTextEl = tb;
      tb.focus();
    }
    save();
    toast('已添加文字块');
  };

  window.addDivider = function(btn, panelId){
    var wrap = btn.closest('.bwrap') || btn.closest('.extras');
    var container;
    if(panelId){
      container = document.getElementById(panelId).querySelector('.card') || document.getElementById(panelId);
    } else if(wrap && wrap.classList.contains('bwrap')){
      container = wrap.parentNode;
    } else {
      container = btn.closest('.card') || btn.closest('.panel');
    }
    if(!container) return;

    var newWrap = document.createElement('div');
    newWrap.className = 'bwrap';
    newWrap.style.marginTop = '6px';
    newWrap.innerHTML = '<div class="divider"><div class="line"></div></div>' +
      '<div class="btools">' +
        '<button class="dvd" onclick="addDivider(this)">—</button>' +
        '<button class="del" onclick="deleteBlock(this)">×</button>' +
      '</div>';

    if(wrap && wrap.parentNode){
      wrap.parentNode.insertBefore(newWrap, wrap.nextSibling);
    } else {
      container.appendChild(newWrap);
    }
    save();
    toast('已添加分隔线');
  };

  window.deleteBlock = function(btn){
    var wrap = btn.closest('.bwrap');
    if(wrap) wrap.remove();
    save();
    toast('已删除');
  };

  // ==================== 链接跳转 ====================
  document.addEventListener('click', function(e){
    if(editMode) return;
    var el = e.target.closest('[data-link]');
    if(el && el.getAttribute('data-link')){
      var target = el.getAttribute('data-link');
      if(target && document.getElementById(target)){
        e.preventDefault();
        window.switchTab(target);
      }
    }
  });

  // ==================== 顶部导航 ====================
  var navHistory = [];

  window.switchTab = function(id){
    var cur = document.querySelector('.panel.active');
    if(cur && cur.id !== id){
      navHistory.push(cur.id);
      if(navHistory.length > 30) navHistory.shift();
    }
    document.querySelectorAll('.panel').forEach(function(p){ p.classList.toggle('active', p.id === id); });
    document.querySelectorAll('.navlink').forEach(function(n){
      if(n.getAttribute('data-panel')) n.classList.toggle('active', n.getAttribute('data-panel') === id);
    });
    refreshCollectionActive();
    window.scrollTo(0, 0);
  };

  document.querySelectorAll('.navlink[data-panel]').forEach(function(n){
    n.addEventListener('click', function(){ window.switchTab(this.getAttribute('data-panel')); });
  });

  // ==================== 合集菜单 ====================
  var navCollection = document.getElementById('navCollection');
  var collectionMenu = document.getElementById('collectionMenu');
  var collectionList = document.getElementById('collectionList');

  function refreshCollectionMenu(){
    collectionList.innerHTML = '';
    var items = document.querySelectorAll('#app .panel[data-collection="true"]');
    if(items.length === 0){
      collectionList.innerHTML = '<div class="cempty">暂无页面</div>';
      return;
    }
    items.forEach(function(p){
      var d = document.createElement('div');
      d.className = 'citem';
      d.setAttribute('data-panel', p.id);
      d.textContent = p.dataset.name || p.id;
      d.addEventListener('click', function(){
        window.switchTab(p.id);
        collectionMenu.classList.remove('show');
      });
      collectionList.appendChild(d);
    });
    refreshCollectionActive();
  }

  function refreshCollectionActive(){
    var cur = document.querySelector('.panel.active');
    if(!cur) return;
    var isCollection = cur.getAttribute('data-collection') === 'true';
    navCollection.classList.toggle('active', isCollection);
    collectionList.querySelectorAll('.citem').forEach(function(c){
      c.classList.toggle('active', c.getAttribute('data-panel') === cur.id);
    });
  }

  navCollection.addEventListener('click', function(e){
    e.stopPropagation();
    if(collectionMenu.classList.contains('show')) collectionMenu.classList.remove('show');
    else { refreshCollectionMenu(); collectionMenu.classList.add('show'); }
  });

  document.addEventListener('click', function(e){
    if(collectionMenu.classList.contains('show') && !collectionMenu.contains(e.target) && !navCollection.contains(e.target)){
      collectionMenu.classList.remove('show');
    }
  });

  // ==================== 新页面 ====================
  document.getElementById('btnAddPanel').addEventListener('click', function(){
    var name = prompt('新页面名称（会加入合集）:', '新页面');
    if(!name) return;
    name = name.trim(); if(!name) return;

    var panels = document.querySelectorAll('#app .panel');
    var used = {}; panels.forEach(function(p){ used[p.id] = true; });
    var idx = 0; while(used['panel-' + idx]) idx++;
    var newId = 'panel-' + idx;

    var sec = document.createElement('section');
    sec.className = 'panel';
    sec.id = newId;
    sec.setAttribute('data-collection', 'true');
    sec.dataset.name = name;
    sec.innerHTML = '<div class="card">' +
      '<div class="bwrap">' +
        '<div class="feedtext" data-edit>新页面内容...</div>' +
        '<div class="btools">' +
          '<button onclick="addTextBlock(this)">＋</button>' +
          '<button class="dvd" onclick="addDivider(this)">—</button>' +
          '<button class="del" onclick="deleteBlock(this)">×</button>' +
        '</div>' +
      '</div>' +
      '</div>' +
      '<div class="extras">' +
        '<button class="exbtn" onclick="addTextBlock(this, \'' + newId + '\')">＋ 自由文本块</button>' +
        '<button class="exbtn" onclick="addDivider(this, \'' + newId + '\')">— 分隔线</button>' +
      '</div>';

    app.appendChild(sec);
    if(editMode){
      sec.querySelectorAll('[data-edit]').forEach(function(el){
        el.setAttribute('contenteditable', 'true');
      });
    }
    refreshCollectionMenu();
    save();
    toast('已添加：' + name);
  });

  // ==================== 重置 ====================
  document.getElementById('btnReset').addEventListener('click', function(){
    if(!confirm('确定恢复初始内容吗？所有修改将丢失！')) return;
    app.innerHTML = DEFAULT_HTML;
    bindAll();
    setEdit(false);
    save();
    refreshCollectionMenu();
    toast('已重置');
  });

  // ==================== 图形 ====================
  function shapeSVG(type){
    var s = {
      circle:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><circle cx="50" cy="50" r="45" fill="currentColor"/></svg>',
      square:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" fill="currentColor"/></svg>',
      rounded:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><rect x="5" y="5" width="90" height="90" rx="20" fill="currentColor"/></svg>',
      triangle:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,95 5,95" fill="currentColor"/></svg>',
      star:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 61,38 96,38 68,59 79,92 50,72 21,92 32,59 4,38 39,38" fill="currentColor"/></svg>',
      heart:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M50,88 C20,65 5,45 5,30 C5,15 18,5 32,5 C41,5 48,10 50,18 C52,10 59,5 68,5 C82,5 95,15 95,30 C95,45 80,65 50,88 Z" fill="currentColor"/></svg>',
      arrow:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M5,40 L60,40 L60,15 L95,50 L60,85 L60,60 L5,60 Z" fill="currentColor"/></svg>',
      diamond:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 95,50 50,95 5,50" fill="currentColor"/></svg>',
      hexagon:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><polygon points="50,5 93,27 93,73 50,95 7,73 7,27" fill="currentColor"/></svg>',
      cross:'<svg viewBox="0 0 100 100" preserveAspectRatio="none"><path d="M35,5 L65,5 L65,35 L95,35 L95,65 L65,65 L65,95 L35,95 L35,65 L5,65 L5,35 L35,35 Z" fill="currentColor"/></svg>'
    };
    return s[type] || s.circle;
  }

  function makeShape(type){
    var el = document.createElement('div');
    el.className = 'shape';
    el.dataset.type = type;
    el.style.left = '40px';
    el.style.top = (window.scrollY + 200) + 'px';
    el.style.width = '80px';
    el.style.height = '80px';
    el.style.color = getComputedStyle(document.documentElement).getPropertyValue('--accent').trim() || '#4a9eff';
    el.innerHTML = shapeSVG(type) +
      '<div class="rz"></div>' +
      '<button class="xdel">×</button>';
    floatLayer.appendChild(el);
    makeMovable(el);
    bindXDel(el);
    bindResize(el);
    save();
    toast('已添加图形');
  }

  function makeFrame(shape){
    var el = document.createElement('div');
    el.className = 'frame';
    el.style.left = '40px';
    el.style.top = (window.scrollY + 300) + 'px';
    el.style.width = '120px';
    el.style.height = '120px';
    if(shape === 'circle') el.style.borderRadius = '50%';
    else if(shape === 'rounded') el.style.borderRadius = '16px';
    else el.style.borderRadius = '0';

    var img = document.createElement('img');
    img.setAttribute('data-edit-img', '');
    img.src = 'data:image/svg+xml,%3Csvg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"%3E%3Crect width="100" height="100" fill="%23333"/%3E%3Ctext x="50" y="55" font-size="12" fill="%23999" text-anchor="middle" font-family="sans-serif"%3E点击换图%3C/text%3E%3C/svg%3E';
    el.appendChild(img);
    el.innerHTML += '<div class="rz"></div><button class="xdel">×</button>';

    floatLayer.appendChild(el);
    makeMovable(el);
    bindXDel(el);
    bindResize(el);
    bindImg(img);
    save();
    toast('已添加图片框');
  }

  function makeFloater(){
    var el = document.createElement('div');
    el.className = 'floater';
    el.contentEditable = 'true';
    el.style.left = '40px';
    el.style.top = (window.scrollY + 150) + 'px';
    el.style.width = '160px';
    el.innerHTML = '自由文本';
    el.innerHTML += '<div class="rz"></div><button class="xdel">×</button>';
    floatLayer.appendChild(el);
    makeMovable(el);
    bindXDel(el);
    bindResize(el);
    setTimeout(function(){ el.focus(); }, 50);
    save();
    toast('已添加浮动文本');
  }

  document.getElementById('btnAddShape').addEventListener('click', function(e){
    e.stopPropagation();
    document.getElementById('framePicker').classList.remove('show');
    document.getElementById('shapePicker').classList.toggle('show');
  });
  document.getElementById('btnAddFrame').addEventListener('click', function(e){
    e.stopPropagation();
    document.getElementById('shapePicker').classList.remove('show');
    document.getElementById('framePicker').classList.toggle('show');
  });
  document.getElementById('btnAddFloat').addEventListener('click', function(){
    makeFloater();
  });

  document.querySelectorAll('#shapePicker .pick').forEach(function(p){
    p.addEventListener('click', function(){
      makeShape(this.dataset.shape);
      document.getElementById('shapePicker').classList.remove('show');
    });
  });
  document.querySelectorAll('#framePicker .pick').forEach(function(p){
    p.addEventListener('click', function(){
      makeFrame(this.dataset.frame);
      document.getElementById('framePicker').classList.remove('show');
    });
  });

  // ==================== 拖拽 / 缩放 / 删除 ====================
  function makeMovable(el){
    var dragging = false, sx = 0, sy = 0, sl = 0, st = 0, moved = false;

    function down(e){
      if(!editMode) return;
      if(e.target.classList.contains('rz')) return;
      if(e.target.classList.contains('xdel')) return;
      e.preventDefault();
      dragging = true; moved = false;
      var p = e.touches ? e.touches[0] : e;
      sx = p.clientX; sy = p.clientY;
      sl = el.offsetLeft; st = el.offsetTop;
      document.addEventListener('mousemove', move);
      document.addEventListener('touchmove', move, {passive:false});
      document.addEventListener('mouseup', up);
      document.addEventListener('touchend', up);
    }
    function move(e){
      if(!dragging) return;
      e.preventDefault();
      var p = e.touches ? e.touches[0] : e;
      var dx = p.clientX - sx, dy = p.clientY - sy;
      if(Math.abs(dx) > 3 || Math.abs(dy) > 3) moved = true;
      el.style.left = (sl + dx) + 'px';
      el.style.top = (st + dy) + 'px';
    }
    function up(){
      dragging = false;
      document.removeEventListener('mousemove', move);
      document.removeEventListener('touchmove', move);
      document.removeEventListener('mouseup', up);
      document.removeEventListener('touchend', up);
      if(moved) save();
    }
    el.addEventListener('mousedown', down);
    el.addEventListener('touchstart', down, {passive:false});
  }

  function bindResize(el){
    var rz = el.querySelector('.rz');
    if(!rz) return;
    var rs = false, sw = 0, sh = 0, sx = 0, sy = 0;
    function down(e){
      e.preventDefault(); e.stopPropagation();
      rs = true;
      var p = e.touches ? e.touches[0] : e;
      sx = p.clientX; sy = p.clientY;
      sw = el.offsetWidth; sh = el.offsetHeight;
      document.addEventListener('mousemove', move);
      document.addEventListener('touchmove', move, {passive:false});
      document.addEventListener('mouseup', up);
      document.addEventListener('touchend', up);
    }
    function move(e){
      if(!rs) return;
      e.preventDefault();
      var p = e.touches ? e.touches[0] : e;
      el.style.width = Math.max(30, sw + (p.clientX - sx)) + 'px';
      el.style.height = Math.max(30, sh + (p.clientY - sy)) + 'px';
    }
    function up(){
      rs = false;
      document.removeEventListener('mousemove', move);
      document.removeEventListener('touchmove', move);
      document.removeEventListener('mouseup', up);
      document.removeEventListener('touchend', up);
      save();
    }
    rz.addEventListener('mousedown', down);
    rz.addEventListener('touchstart', down, {passive:false});
  }

  function bindXDel(el){
    var d = el.querySelector('.xdel');
    if(!d) return;
    d.addEventListener('click', function(e){
      e.stopPropagation();
      if(confirm('确定删除吗？')){ el.remove(); save(); toast('已删除'); }
    });
  }

  // ==================== 初始化绑定 ====================
  function bindAll(){
    document.querySelectorAll('img[data-edit-img]').forEach(bindImg);
    document.querySelectorAll('.shape, .frame, .floater').forEach(function(el){
      if(!el.dataset.moveBound){
        el.dataset.moveBound = '1';
        makeMovable(el);
        bindXDel(el);
        bindResize(el);
        var img = el.querySelector('img');
        if(img) bindImg(img);
      }
    });
  }

  // ==================== 音乐 ====================
  var music = document.getElementById('bgMusic');
  var musicBtn = document.getElementById('musicBtn');
  var dialog = document.getElementById('musicDialog');
  var musicInput = document.getElementById('musicInput');

  try {
    var savedMusic = localStorage.getItem(K_MUSIC);
    if(savedMusic) music.src = savedMusic;
  } catch(e){}

  var pressTimer = null, longPressed = false;
  function startPress(){
    longPressed = false;
    clearTimeout(pressTimer);
    pressTimer = setTimeout(function(){
      longPressed = true;
      try { musicInput.value = localStorage.getItem(K_MUSIC) || ''; } catch(e){ musicInput.value=''; }
      dialog.classList.add('show');
      setTimeout(function(){ musicInput.focus(); }, 200);
    }, 600);
  }
  function cancelPress(){ clearTimeout(pressTimer); }
  function playPause(){
    if(!music.src || music.src === location.href){ toast('请长按按钮设置音乐'); return; }
    if(music.paused){
      music.play().then(function(){
        musicBtn.classList.add('playing'); musicBtn.textContent = '❚❚';
      }).catch(function(){ toast('播放失败'); });
    } else {
      music.pause(); musicBtn.classList.remove('playing'); musicBtn.textContent = '♪';
    }
  }
  musicBtn.addEventListener('mousedown', function(e){ e.preventDefault(); startPress(); });
  musicBtn.addEventListener('mouseup', function(e){
    cancelPress();
    if(!longPressed){ e.preventDefault(); playPause(); }
  });
  musicBtn.addEventListener('mouseleave', cancelPress);
  musicBtn.addEventListener('touchstart', function(){ startPress(); }, {passive:true});
  musicBtn.addEventListener('touchend', function(e){
    cancelPress();
    if(!longPressed){ e.preventDefault(); playPause(); }
  });
  musicBtn.addEventListener('touchcancel', cancelPress);
  musicBtn.addEventListener('contextmenu', function(e){ e.preventDefault(); });

  document.getElementById('musicCancel').addEventListener('click', function(){
    dialog.classList.remove('show');
  });
  document.getElementById('musicOk').addEventListener('click', function(){
    var url = musicInput.value.trim();
    if(!url){ toast('请输入链接'); return; }
    try { localStorage.setItem(K_MUSIC, url); } catch(e){}
    music.src = url; music.load();
    dialog.classList.remove('show');
    music.play().then(function(){
      musicBtn.classList.add('playing'); musicBtn.textContent = '❚❚';
      toast('音乐播放中');
    }).catch(function(){ toast('播放失败'); });
  });

  // ==================== 初始化 ====================
  bindAll();
  refreshCollectionMenu();
  setEdit(false);

})();
</script>
</body>
</html>