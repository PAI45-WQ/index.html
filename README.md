# index.html[game.html](https://github.com/user-attachments/files/33651880/game.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>异世轮回录 - 最终完美版</title>
    <style>
        /* ================= 全局与基础 ================= */
        * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; }
        body {
            background-color: #0d1b2a;
            display: flex; justify-content: center; align-items: center;
            height: 100vh; font-family: "KaiTi", "STKaiti", "楷体", serif;
            overflow: hidden;
        }

        .game-container {
            width: 100%; max-width: 450px; height: 100%; max-height: 850px;
            background: linear-gradient(to bottom, #f2c3a7 0%, #e8a285 30%, #d9aa90 60%, #e5dfd8 100%);
            position: relative; overflow: hidden;
            box-shadow: 0 0 20px rgba(0,0,0,0.8);
            display: flex; flex-direction: column;
        }

        /* ================= 背景层 ================= */
        .bg-layer { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 1; transition: filter 0.3s, opacity 0.3s; }
        .bg-blur { filter: blur(5px) brightness(0.6); }
        .clouds { position: absolute; width: 100%; height: 50%; top: 0; background: radial-gradient(circle at 50% 0%, rgba(255,255,255,0.8) 0%, transparent 60%); opacity: 0.6; }
        .crystal { position: absolute; background: linear-gradient(135deg, #fff5cc, #f6c23e); opacity: 0.8; box-shadow: 0 0 20px #f6c23e; clip-path: polygon(50% 0%, 100% 50%, 50% 100%, 0% 50%); animation: float 4s infinite alternate; }
        .c1 { width: 60px; height: 120px; top: 10%; left: 10%; transform: rotate(-30deg); }
        .c2 { width: 40px; height: 80px; top: 15%; right: 15%; transform: rotate(20deg); animation-delay: 1s; }
        .c3 { width: 80px; height: 160px; top: 5%; right: 30%; transform: rotate(45deg); opacity: 0.5; animation-delay: 2s; }
        .clock { position: absolute; border-radius: 50%; border: 3px solid rgba(255,255,255,0.7); background: rgba(240, 230, 220, 0.4); backdrop-filter: blur(2px); box-shadow: 0 0 15px rgba(255,255,255,0.5); }
        .clock-top { width: 90px; height: 90px; top: -15px; left: 50%; transform: translateX(-50%); border-radius: 0 0 45px 45px; }
        .clock-left { width: 70px; height: 70px; top: 15%; left: 10%; transform: rotate(-20deg); }
        .pillar { position: absolute; width: 70px; height: 30%; background: linear-gradient(to right, #6b625b, #9e938a, #6b625b); top: 20%; clip-path: polygon(20% 0, 80% 0, 100% 100%, 0 100%); }
        .pillar.left { left: -10px; } .pillar.right { right: -10px; transform: scaleX(-1); }
        .girl { position: absolute; top: 28%; left: 50%; transform: translateX(-50%); width: 120px; height: 200px; display: flex; flex-direction: column; align-items: center; filter: drop-shadow(0 0 10px rgba(255,255,255,0.8)); animation: breathing 3s infinite alternate; }
        .girl-head { width: 40px; height: 45px; background: #fffaf0; border-radius: 45% 45% 40% 40%; z-index: 2; }
        .girl-hair { position: absolute; width: 60px; height: 80px; background: #fde08b; border-radius: 50% 50% 20% 20%; top: -5px; left: -10px; z-index: 1; }
        .girl-body { width: 60px; height: 100px; background: #ffffff; border-radius: 20px 20px 0 0; margin-top: -10px; z-index: 1; }
        .girl-skirt { width: 90px; height: 80px; background: #ffffff; margin-top: -5px; clip-path: polygon(20% 0, 80% 0, 100% 100%, 0 100%); }
        .stairs { position: absolute; bottom: 15%; left: 50%; transform: translateX(-50%); width: 100%; height: 35%; display: flex; flex-direction: column; align-items: center; justify-content: flex-end; }
        .step { background: linear-gradient(to bottom, #d9cfc5, #a89c91); border-top: 2px solid #fff; border-radius: 4px; margin-bottom: -2px; }
        .step:nth-child(1) { width: 20%; height: 15px; } .step:nth-child(2) { width: 35%; height: 20px; } .step:nth-child(3) { width: 50%; height: 25px; } .step:nth-child(4) { width: 65%; height: 30px; } .step:nth-child(5) { width: 80%; height: 35px; } .step:nth-child(6) { width: 95%; height: 40px; }
        .neon-line { position: absolute; width: 4px; height: 80px; background: #5ffbf1; box-shadow: 0 0 10px #5ffbf1; }
        .neon-l { bottom: 25%; left: 15%; transform: rotate(-35deg); } .neon-r { bottom: 25%; right: 15%; transform: rotate(35deg); }
        .fog { position: absolute; bottom: 0; left: 0; width: 100%; height: 30%; background: linear-gradient(to top, #f2ede9 0%, #f2ede9 40%, transparent 100%); filter: blur(10px); }

        /* ================= 顶部固定UI (层级提高!) ================= */
        .top-ui { 
            position: absolute; top: 20px; left: 0; width: 100%; 
            display: flex; justify-content: space-between; padding: 0 15px; 
            z-index: 999; /* 🔴 关键修复：把层级提到最高，确保能点到 */
        }
        
        /* 自定义音乐按钮（纯 JS 控制） */
        .audio-btn-wrap {
            display: flex;
            flex-direction: column;
            gap: 4px;
            cursor: pointer;
        }
        .audio-btn-inner {
            display: flex; align-items: center; gap: 8px;
            font-family: sans-serif; font-size: 14px; font-weight: bold; color: #333;
        }
        
        /* 手绘方块 */
        .checkmark-box {
            width: 26px; height: 26px;
            border: 2px solid #d9534f;
            border-radius: 6px;
            background-color: transparent; /* 默认透明空心 */
            position: relative;
            transition: all 0.2s ease;
        }
        
        /* 勾选后的状态 */
        .checkmark-box.active {
            background-color: #d9534f; /* 红底 */
        }
        /* 白色对勾 */
        .checkmark-box.active::after {
            content: ''; position: absolute;
            left: 8px; top: 3px;
            width: 6px; height: 12px;
            border: solid white; border-width: 0 3px 3px 0;
            transform: rotate(45deg);
        }

        .audio-status { 
            font-size: 12px; font-weight: bold; font-family: sans-serif; 
            color: #d9534f; margin-left: 34px;
            background: rgba(255,255,255,0.5); padding: 1px 5px; border-radius: 4px;
            width: fit-content;
        }

        .currency { display: flex; align-items: center; background: rgba(0,0,0,0.4); padding: 4px 15px; border-radius: 20px; color: #fff; font-family: sans-serif; font-weight: bold; font-size: 18px; gap: 8px; }
        .soul-icon { width: 24px; height: 24px; border-radius: 50% 50% 20% 20%; box-shadow: inset 0 0 5px rgba(255,255,255,0.8); }
        .soul-blue { background: linear-gradient(to bottom, #a8e6cf, #55efc4); }
        .soul-green { background: linear-gradient(to bottom, #c8d6e5, #48dbfb); }
        .right-btns { display: flex; gap: 10px; }
        .btn-small { background: rgba(255,255,255,0.8); border: 2px solid #333; border-radius: 50%; width: 32px; height: 32px; display: flex; justify-content: center; align-items: center; font-weight: bold; cursor: pointer; }
        .btn-plus-soul { display: flex; align-items: center; background: #fff; border: 2px solid #333; padding: 2px 8px; border-radius: 10px; gap: 5px; }

        /* ================= 界面 1：转生文字区 (层级降低) ================= */
        .intro-ui { 
            position: absolute; top: 0; left: 0; width: 100%; height: 100%; 
            z-index: 5; /* 🔴 关键修复：层级比 top-ui 低 */
            transition: opacity 0.3s; 
        }
        .vertical-text-container { position: absolute; top: 15%; left: 50%; transform: translateX(-50%); display: flex; flex-direction: row-reverse; gap: 15px; text-shadow: 1px 1px 3px rgba(0,0,0,0.4); }
        .v-text { writing-mode: vertical-rl; text-orientation: upright; font-size: 18px; color: #2b1d12; font-weight: bold; letter-spacing: 2px; line-height: 1.5; }
        .bottom-btn-container { position: absolute; bottom: 10%; width: 100%; display: flex; justify-content: center; }
        .choice-btn { background: rgba(90, 85, 80, 0.7); color: #fff; border: none; width: 60%; padding: 15px 0; font-size: 22px; font-weight: bold; cursor: pointer; clip-path: polygon(5% 0, 95% 0, 100% 50%, 95% 100%, 5% 100%, 0% 50%); transition: all 0.2s; text-shadow: 1px 1px 2px #000; }
        .choice-btn:hover { background: rgba(120, 110, 100, 0.9); transform: scale(1.02); }
        .choice-btn:active { transform: scale(0.95); }

        /* ================= 界面 2：卷轴弹窗 ================= */
        .scroll-modal { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 20; display: none; flex-direction: column; justify-content: center; align-items: center; }
        .scroll-body { width: 90%; background-color: #f6ebd8; background-image: radial-gradient(#e6d4b8 1px, transparent 1px); background-size: 20px 20px; border-radius: 10px; position: relative; padding: 30px 15px; box-shadow: 0 10px 30px rgba(0,0,0,0.5), inset 0 0 20px rgba(180, 140, 100, 0.3); transform: scale(0.8); opacity: 0; transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .scroll-modal.active .scroll-body { transform: scale(1); opacity: 1; }
        .scroll-rod-top, .scroll-rod-bottom { position: absolute; left: -5%; width: 110%; height: 25px; background: linear-gradient(to bottom, #c4a381, #8c6c4c, #5c4331); border-radius: 12px; box-shadow: 0 3px 5px rgba(0,0,0,0.4); z-index: 2; }
        .scroll-rod-top { top: -12px; } .scroll-rod-bottom { bottom: -12px; }
        .section-title { display: flex; justify-content: center; align-items: center; font-size: 22px; color: #7a4225; font-weight: bold; margin: 10px 0 20px 0; }
        .section-title::before, .section-title::after { content: ''; flex: 1; height: 2px; background: linear-gradient(to right, transparent, #c2a281, transparent); margin: 0 15px; }
        .origins-container { display: flex; justify-content: space-around; margin-bottom: 25px; }
        .origin-item { display: flex; flex-direction: column; align-items: center; gap: 5px; }
        .origin-shield { width: 65px; height: 75px; display: flex; justify-content: center; align-items: center; font-size: 24px; font-weight: bold; color: #fff; text-shadow: 1px 1px 2px #000; border-radius: 10px 10px 50% 50% / 10px 10px 30% 30%; border: 2px solid #fff; }
        .o-farmer { background: linear-gradient(135deg, #f2a1b3, #d96583); box-shadow: 0 0 15px #f2a1b3; }
        .o-hunter { background: #4a5a6a; border-color: #666; color: #888; box-shadow: none; filter: grayscale(1); }
        .o-noble { background: #8b7d3c; border-color: #666; color: #888; box-shadow: none; filter: grayscale(1); }
        .o-label { font-size: 15px; color: #5c4331; font-weight: bold; }
        .o-locked { font-size: 13px; color: #7a6250; }
        .talents-container { display: flex; justify-content: space-between; margin-bottom: 20px; gap: 10px; }
        .talent-card { flex: 1; background: #fff; border: 2px solid #ccc; border-radius: 5px; padding: 10px 5px; text-align: center; position: relative; cursor: pointer; clip-path: polygon(10px 0, calc(100% - 10px) 0, 100% 10px, 100% calc(100% - 10px), calc(100% - 10px) 100%, 10px 100%, 0 calc(100% - 10px), 0 10px); box-shadow: 0 3px 5px rgba(0,0,0,0.1); transition: transform 0.1s; }
        .talent-card:hover { transform: scale(1.05); }
        .talent-card:active { transform: scale(0.95); }
        .talent-name { font-size: 15px; font-weight: bold; color: #333; margin-bottom: 5px; }
        .talent-divider { font-size: 10px; color: #999; margin-bottom: 5px; }
        .talent-rarity { font-size: 13px; font-weight: bold; }
        .r-common { border-color: #a0a0a0; } .r-common .talent-rarity { color: #a0a0a0; }
        .r-rare { border-color: #4a90e2; box-shadow: 0 0 8px rgba(74,144,226,0.3); } .r-rare .talent-rarity { color: #4a90e2; }
        .r-epic { border-color: #b05ce6; box-shadow: 0 0 8px rgba(176,92,230,0.4); } .r-epic .talent-rarity { color: #b05ce6; }
        .r-legendary { border-color: #e6a23c; box-shadow: 0 0 10px rgba(230,162,60,0.5); } .r-legendary .talent-rarity { color: #e6a23c; }

        .roll-section { display: flex; justify-content: space-evenly; align-items: center; width: 100%; margin-bottom: 15px; padding: 0 10px; }
        .btn-roll, .btn-free-roll { position: relative; display: flex; flex-direction: row; align-items: center; gap: 8px; cursor: pointer; transition: transform 0.1s; background: rgba(255,255,255,0.4); padding: 8px 12px; border-radius: 8px; border: 1px solid rgba(200, 180, 150, 0.5); }
        .btn-roll:hover, .btn-free-roll:hover { transform: scale(1.05); background: rgba(255,255,255,0.7); }
        .btn-roll:active, .btn-free-roll:active { transform: scale(0.95); }
        .btn-roll .dice-icon, .btn-free-roll .dice-red { width: 36px; height: 36px; border-radius: 8px; display: flex; justify-content: center; align-items: center; color: #fff; font-size: 20px; font-weight: bold; box-shadow: inset 0 -3px 0 rgba(0,0,0,0.2); }
        .btn-roll .dice-icon { background: #a66a51; }
        .btn-free-roll .dice-red { background: #b5453d; }
        .btn-roll .roll-text, .btn-free-roll .free-text { font-size: 18px; font-weight: bold; color: #5c4331; font-family: Arial; }
        .btn-roll-cost { display: flex; align-items: center; font-size: 16px; font-weight: bold; color: #333; gap: 3px; }
        .ad-badge { position: absolute; top: -10px; right: -10px; background: #e74c3c; color: #fff; font-size: 10px; font-weight: bold; padding: 2px 6px; border-radius: 10px; border: 1px solid #fff; box-shadow: 0 2px 4px rgba(0,0,0,0.3); font-family: Arial; animation: pulse 1.5s infinite; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.1); } 100% { transform: scale(1); } }
        .roll-desc { text-align: center; font-size: 12px; color: #b05ce6; font-weight: bold; line-height: 1.4; margin-bottom: 15px; padding: 0 10px; }
        .roll-desc span { color: #7a6250; }
        .modal-actions { display: flex; flex-direction: column; gap: 10px; margin-top: 10px; }
        .modal-actions .choice-btn { width: 80%; margin: 0 auto; padding: 10px 0; font-size: 18px; background: rgba(120, 110, 100, 0.8); }

        #talent-detail-overlay { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 30; display: none; justify-content: center; align-items: center; background: rgba(0,0,0,0.3); }
        .detail-box { width: 85%; background: #fdfaf5; border-radius: 4px; position: relative; padding: 40px 20px; text-align: center; box-shadow: 0 10px 25px rgba(0,0,0,0.4); border: 4px solid #f2e8d5; outline: 1px solid #d4c5b0; animation: popIn 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        .detail-box::before { content: ''; position: absolute; top: 6px; left: 6px; right: 6px; bottom: 6px; border: 1px solid #c98b8b; border-radius: 2px; pointer-events: none; }
        .corner-deco { position: absolute; width: 20px; height: 20px; border: 2px solid #d4a3a3; border-radius: 50%; opacity: 0.6; }
        .corner-deco::after { content: ''; position: absolute; width: 8px; height: 8px; border: 1px solid #d4a3a3; border-radius: 50%; top: 50%; left: 50%; transform: translate(-50%, -50%); }
        .c-tl { top: 8px; left: 8px; border-bottom-color: transparent; border-right-color: transparent; transform: rotate(-45deg);}
        .c-tr { top: 8px; right: 8px; border-bottom-color: transparent; border-left-color: transparent; transform: rotate(45deg);}
        .c-bl { bottom: 8px; left: 8px; border-top-color: transparent; border-right-color: transparent; transform: rotate(-135deg);}
        .c-br { bottom: 8px; right: 8px; border-top-color: transparent; border-left-color: transparent; transform: rotate(135deg);}
        .detail-text { font-size: 18px; color: #3a2c20; font-weight: bold; line-height: 1.6; letter-spacing: 1px; font-family: "KaiTi", "STKaiti", serif; }
        @keyframes popIn { 0% { transform: scale(0.8); opacity: 0; } 100% { transform: scale(1); opacity: 1; } }

        #ad-overlay { position: absolute; top: 0; left: 0; width: 100%; height: 100%; z-index: 50; display: none; flex-direction: column; justify-content: center; align-items: center; background: rgba(0,0,0,0.9); color: #fff; }
        .ad-content { text-align: center; }
        .ad-icon { width: 60px; height: 60px; border: 3px solid #fff; border-radius: 50%; display: flex; justify-content: center; align-items: center; margin: 0 auto 20px auto; font-size: 24px; animation: rotate 2s linear infinite; }
        @keyframes rotate { 100% { transform: rotate(360deg); } }
        .ad-text { font-size: 18px; font-family: sans-serif; margin-bottom: 10px; }
        .ad-timer { font-size: 24px; font-weight: bold; color: #f6c23e; font-family: monospace; }

        .footer-text { position: absolute; bottom: 2%; width: 100%; text-align: center; font-size: 11px; color: #444; z-index: 10; font-family: sans-serif; }
        .footer-text .version { float: left; margin-left: 20px; }

        @keyframes float { 0% { transform: translateY(0) rotate(inherit); } 100% { transform: translateY(-15px) rotate(inherit); } }
        @keyframes breathing { 0% { transform: translateX(-50%) translateY(0); } 100% { transform: translateX(-50%) translateY(-5px); } }
    </style>
</head>
<body>

<audio id="bgm-audio" loop preload="auto">
    <source src="https://imagetourl.im/api/storage/file?key=anon%2F-%2F8207650169118-d622411b-c306-43c7-97e5-bd5e3b0fb522-maou_bgm_fantasy13.mp3" type="audio/mpeg">
</audio>

<div class="game-container">
    
    <div class="bg-layer" id="bg-layer">
        <div class="clouds"></div>
        <div class="crystal c1"></div><div class="crystal c2"></div><div class="crystal c3"></div>
        <div class="clock clock-top"></div><div class="clock clock-left"></div>
        <div class="pillar left"></div><div class="pillar right"></div>
        <div class="girl">
            <div class="girl-hair"></div><div class="girl-head"></div><div class="girl-body"></div><div class="girl-skirt"></div>
        </div>
        <div class="stairs">
            <div class="step"></div><div class="step"></div><div class="step"></div><div class="step"></div><div class="step"></div><div class="step"></div>
        </div>
        <div class="neon-line neon-l"></div><div class="neon-line neon-r"></div>
        <div class="fog"></div>
    </div>

    <!-- 🔴 顶部UI，已经增加 z-index 999 -->
    <div class="top-ui">
        <!-- 纯 JS 控制的音乐按钮 -->
        <div class="audio-btn-wrap" onclick="toggleAudio()">
            <div class="audio-btn-inner">
                <div class="checkmark-box active" id="audio-checkmark"></div>
                音乐开关
            </div>
            <div class="audio-status" id="audio-status">播放中</div>
        </div>
        
        <div class="currency">
            <div class="soul-icon soul-blue"></div><span>0</span>
            <div class="soul-icon soul-green"></div><span>0</span>
        </div>
        <div class="right-btns">
            <div class="btn-small">···</div>
            <div class="btn-small">−</div>
            <div class="btn-plus-soul"><span class="plus">+</span><div class="soul-icon soul-blue" style="width:16px; height:16px;"></div></div>
        </div>
    </div>

    <!-- 🔴 转生界面，z-index 降低为 5，确保不会遮挡上方的 UI -->
    <div class="intro-ui" id="intro-ui">
        <div class="vertical-text-container">
            <div class="v-text">选择你的出身吧</div>
            <div class="v-text">转生之门已经开启</div>
            <div class="v-text">很好，异世之人。</div>
        </div>
        <div class="bottom-btn-container">
            <button class="choice-btn" onclick="startGame()">进入转生</button>
        </div>
    </div>

    <div class="scroll-modal" id="scroll-modal">
        <div class="scroll-body">
            <div class="scroll-rod-top"></div>
            
            <div class="section-title">❖ 出身 ❖</div>
            <div class="origins-container">
                <div class="origin-item">
                    <div class="origin-shield o-farmer">✝</div><div class="o-label">农民</div>
                </div>
                <div class="origin-item">
                    <div class="origin-shield o-hunter">⚒</div><div class="o-label" style="color:#666">猎人</div><div class="o-locked">未解锁</div>
                </div>
                <div class="origin-item">
                    <div class="origin-shield o-noble">⚔</div><div class="o-label" style="color:#666">贵族</div><div class="o-locked">未解锁</div>
                </div>
            </div>

            <div class="section-title">❖ 天赋 ❖</div>
            <div class="talents-container" id="talents-box"></div>

            <div class="roll-section">
                <div class="btn-roll" onclick="rollTalents(false)">
                    <div class="dice-icon">🎲</div>
                    <span class="roll-text">Roll</span>
                    <div class="btn-roll-cost">
                        <div class="soul-icon soul-blue" style="width: 18px; height: 18px;"></div> 30
                    </div>
                </div>
                
                <div class="btn-free-roll" onclick="showAdForRoll()">
                    <div class="ad-badge">AD</div>
                    <div class="dice-red">🎲</div>
                    <span class="free-text">FreeRoll</span>
                </div>
            </div>

            <div class="roll-desc">
                <span style="color:#e74c3c;">看广告FreeRoll保底稀有，大概率出史诗/传说！</span><br>
                <span style="font-size: 10px; color: #999;">(付费Roll大概率普通)</span>
            </div>

            <div class="modal-actions">
                <button class="choice-btn" onclick="alert('天赋已锁定，准备进入异世界！')">确定</button>
                <button class="choice-btn" onclick="closeScroll()">还要再想想</button>
            </div>

            <div class="scroll-rod-bottom"></div>
        </div>
    </div>

    <div id="talent-detail-overlay" onclick="hideTalentDetail()">
        <div class="detail-box" onclick="event.stopPropagation()">
            <div class="corner-deco c-tl"></div><div class="corner-deco c-tr"></div>
            <div class="corner-deco c-bl"></div><div class="corner-deco c-br"></div>
            <div class="detail-text" id="detail-text-content"></div>
        </div>
    </div>

    <div id="ad-overlay">
        <div class="ad-content">
            <div class="ad-icon">🎬</div>
            <div class="ad-text">正在加载激励视频广告...</div>
            <div class="ad-timer" id="ad-timer">3s</div>
        </div>
    </div>

    <div class="footer-text">
        <span class="version">v3.26</span>
        <span>本游戏为单机游戏，删除游戏会导致存档消失。</span>
    </div>

</div>

<script>
    // ================= 纯 JS 音乐系统 =================
    const bgmAudio = document.getElementById('bgm-audio');
    const audioCheckmark = document.getElementById('audio-checkmark');
    const audioStatus = document.getElementById('audio-status');
    
    // 初始状态：开启
    let isAudioOn = true;

    function toggleAudio() {
        isAudioOn = !isAudioOn; // 切换状态
        
        if (isAudioOn) {
            // 打开音乐
            audioCheckmark.classList.add('active'); // 变红底打勾
            audioStatus.innerText = "播放中";
            audioStatus.style.color = "#d9534f";
            
            let playPromise = bgmAudio.play();
            if (playPromise !== undefined) {
                playPromise.catch(e => {
                    audioStatus.innerText = "等待点击屏幕";
                    audioStatus.style.color = "#856404";
                });
            }
        } else {
            // 关闭音乐
            audioCheckmark.classList.remove('active'); // 变白底红框
            audioStatus.innerText = "已关闭";
            audioStatus.style.color = "#666";
            bgmAudio.pause();
        }
    }

    // ================= 游戏逻辑 =================
    const bgLayer = document.getElementById('bg-layer');
    const introUI = document.getElementById('intro-ui');
    const scrollModal = document.getElementById('scroll-modal');
    const talentsBox = document.getElementById('talents-box');
    const detailOverlay = document.getElementById('talent-detail-overlay');
    const detailTextContent = document.getElementById('detail-text-content');
    const adOverlay = document.getElementById('ad-overlay');
    const adTimerText = document.getElementById('ad-timer');

    window.onload = () => {
        generateTalents(false); 
    };

    function startGame() {
        if (isAudioOn) {
            bgmAudio.volume = 0.5;
            bgmAudio.play().then(() => {
                audioStatus.innerText = "播放中";
                audioStatus.style.color = "#d9534f";
            }).catch(e => console.log("播放被拦截"));
        }

        bgLayer.classList.add('bg-blur'); 
        introUI.style.opacity = '0'; 
        setTimeout(() => introUI.style.display = 'none', 300); 
        scrollModal.style.display = 'flex';
        setTimeout(() => { scrollModal.classList.add('active'); }, 10);
    }

    function closeScroll() {
        scrollModal.classList.remove('active');
        setTimeout(() => {
            scrollModal.style.display = 'none';
            introUI.style.display = 'block';
            setTimeout(() => introUI.style.opacity = '1', 10);
            bgLayer.classList.remove('bg-blur');
        }, 300);
    }

    function showTalentDetail(cardElement) {
        const desc = cardElement.getAttribute('data-desc');
        detailTextContent.innerText = desc;
        detailOverlay.style.display = 'flex';
    }

    function hideTalentDetail() {
        detailOverlay.style.display = 'none';
    }

    function showAdForRoll() {
        adOverlay.style.display = 'flex';
        let timeLeft = 3;
        adTimerText.innerText = timeLeft + 's';

        let timer = setInterval(() => {
            timeLeft--;
            adTimerText.innerText = timeLeft + 's';
            
            if (timeLeft <= 0) {
                clearInterval(timer);
                adOverlay.style.display = 'none'; 
                triggerAdSDK(); 
            }
        }, 1000);
    }

    function triggerAdSDK() {
        alert("广告观看完毕，获得3个极品天赋！");
        rollTalents(true);
    }

    function rollTalents(isFree) {
        talentsBox.style.opacity = '0.3';
        setTimeout(() => {
            generateTalents(isFree);
            talentsBox.style.opacity = '1';
        }, 150);
    }

    const rarities = [
        { name: '普通', class: 'r-common' },
        { name: '稀有', class: 'r-rare' },
        { name: '史诗', class: 'r-epic' },
        { name: '传说', class: 'r-legendary' }
    ];

    const talentPool = [
        { name: '强壮的', desc: '力大无穷，力量+4。' },
        { name: '聪明的', desc: '天赋异禀，智力+4。' },
        { name: '幸运的', desc: '命运眷顾，遭遇奇遇概率提升。' },
        { name: '过目不忘', desc: '记忆力超群，学习效率翻倍。' },
        { name: '天选之人', desc: '你就是世界的主角，全属性+2。' },
        { name: '武神降世', desc: '万中无一的战斗天才，力量+10。' },
        { name: '先知', desc: '预知未来，必定触发完美选项。' }
    ];

    function generateTalents(isFree) {
        let html = '';
        for(let i=0; i<3; i++) {
            let rand = Math.random();
            let rIndex = 0;

            if (isFree) {
                if (rand > 0.70) rIndex = 3;
                else if (rand > 0.20) rIndex = 2;
                else rIndex = 1;
            } else {
                if (rand > 0.98) rIndex = 3;
                else if (rand > 0.90) rIndex = 2;
                else if (rand > 0.60) rIndex = 1;
                else rIndex = 0;
            }

            let rarity = rarities[rIndex];
            let talent = talentPool[Math.floor(Math.random() * talentPool.length)];

            html += `
            <div class="talent-card ${rarity.class}" data-desc="${talent.desc}" onclick="showTalentDetail(this)">
                <div class="talent-name">${talent.name}</div>
                <div class="talent-divider">◈</div>
                <div class="talent-rarity">${rarity.name}</div>
            </div>
            `;
        }
        talentsBox.innerHTML = html;
    }
</script>
</body>
</html>
