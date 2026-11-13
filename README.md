# rainforest-game
<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>熱帶雨林地理大探險</title>
    <style>
        :root {
            --primary: #1b5e20;
            --secondary: #2e7d32;
            --accent: #fbc02d;
            --bg-gradient: linear-gradient(135deg, #0d3b1e 0%, #1b5e20 50%, #2e7d32 100%);
            --card-bg: rgba(255, 255, 255, 0.96);
            --text-dark: #1c2822;
            --correct: #2e7d32;
            --wrong: #c62828;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'PingFang HK', 'Microsoft JhengHei', sans-serif;
        }

        body {
            background: var(--bg-gradient);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
            color: var(--text-dark);
        }

        .game-container {
            background: var(--card-bg);
            width: 100%;
            max-width: 680px;
            border-radius: 20px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.4);
            overflow: hidden;
            animation: fadeIn 0.5s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(15px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .header {
            background: #104218;
            color: white;
            padding: 25px 30px;
            text-align: center;
            border-bottom: 4px solid var(--accent);
        }

        .header h1 {
            font-size: 1.8rem;
            margin-bottom: 8px;
            letter-spacing: 1px;
        }

        .header p {
            font-size: 0.95rem;
            color: #c8e6c9;
        }

        .status-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 30px;
            background: #e8f5e9;
            font-weight: bold;
            color: var(--primary);
            border-bottom: 1px solid #c8e6c9;
        }

        .progress-container {
            width: 100%;
            height: 8px;
            background: #c8e6c9;
        }

        .progress-bar {
            height: 100%;
            background: var(--accent);
            width: 0%;
            transition: width 0.4s ease;
        }

        .content {
            padding: 30px;
        }

        .start-screen, .end-screen {
            text-align: center;
            padding: 20px 10px;
        }

        .icon-large {
            font-size: 4.5rem;
            margin-bottom: 15px;
        }

        .question-tag {
            display: inline-block;
            background: #e8f5e9;
            color: var(--primary);
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 0.85rem;
            font-weight: bold;
            margin-bottom: 12px;
        }

        .question-text {
            font-size: 1.25rem;
            font-weight: bold;
            line-height: 1.5;
            margin-bottom: 22px;
        }

        .options-grid {
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .option-btn {
            background: white;
            border: 2px solid #cfd8dc;
            border-radius: 12px;
            padding: 15px 20px;
            font-size: 1rem;
            text-align: left;
            cursor: pointer;
            transition: all 0.2s ease;
            color: var(--text-dark);
            font-weight: 500;
        }

        .option-btn:hover:not(:disabled) {
            border-color: var(--secondary);
            background: #f1f8e9;
            transform: translateX(5px);
        }

        .option-btn.correct {
            background: #e8f5e9;
            border-color: var(--correct);
            color: var(--correct);
            font-weight: bold;
        }

        .option-btn.wrong {
            background: #ffebee;
            border-color: var(--wrong);
            color: var(--wrong);
        }

        .option-btn:disabled {
            cursor: default;
        }

        .feedback-box {
            margin-top: 20px;
            padding: 18px;
            border-radius: 12px;
            display: none;
            line-height: 1.6;
            font-size: 0.95rem;
        }

        .feedback-box.correct {
            background: #e8f5e9;
            border-left: 5px solid var(--correct);
            color: #1b5e20;
        }

        .feedback-box.wrong {
            background: #ffebee;
            border-left: 5px solid var(--wrong);
            color: #b71c1c;
        }

        .btn-main {
            background: var(--secondary);
            color: white;
            border: none;
            padding: 14px 32px;
            font-size: 1.1rem;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            margin-top: 25px;
            box-shadow: 0 4px 12px rgba(46, 125, 50, 0.3);
            transition: background 0.2s, transform 0.2s;
        }

        .btn-main:hover {
            background: var(--primary);
            transform: translateY(-2px);
        }

        .next-btn {
            display: none;
            width: 100%;
        }

        .hidden {
            display: none;
        }

        .rank-badge {
            display: inline-block;
            background: var(--accent);
            color: #3e2723;
            padding: 8px 20px;
            border-radius: 25px;
            font-weight: bold;
            font-size: 1.2rem;
            margin: 15px 0;
        }
    </style>
</head>
<body>

<div class="game-container">
    <div class="header">
        <h1>🌿 熱帶雨林地理大探險</h1>
        <p>探索地球之肺的氣候、養分循環與生態奧秘</p>
    </div>

    <!-- 狀態列 -->
    <div class="status-bar hidden" id="statusBar">
        <span id="questionCounter">第 1 / 6 題</span>
        <span id="scoreDisplay">得分：0</span>
    </div>
    <div class="progress-container hidden" id="progressContainer">
        <div class="progress-bar" id="progressBar"></div>
    </div>

    <div class="content">
        <!-- 開始畫面 -->
        <div class="start-screen" id="startScreen">
            <div class="icon-large">🦜🌴</div>
            <h2>準備好進入熱帶雨林了嗎？</h2>
            <p style="margin: 15px 0; line-height: 1.6; color: #455a64;">
                你將化身地理生態學家，深入亞馬遜與剛果盆地，解答 6 個關於雨林氣候、植被結構與養分循環的核心問題！
            </p>
            <button class="btn-main" onclick="startGame()">開始探險</button>
        </div>

        <!-- 遊戲問答畫面 -->
        <div class="quiz-screen hidden" id="quizScreen">
            <span class="question-tag" id="topicTag">主題標籤</span>
            <div class="question-text" id="questionText">問題內容載入中...</div>
            <div class="options-grid" id="optionsGrid"></div>
            <div class="feedback-box" id="feedbackBox"></div>
            <button class="btn-main next-btn" id="nextBtn" onclick="nextQuestion()">下一題 ➔</button>
        </div>

        <!-- 結束畫面 -->
        <div class="end-screen hidden" id="endScreen">
            <div class="icon-large" id="endIcon">🏆</div>
            <h2>探險完成！</h2>
            <div class="rank-badge" id="rankBadge">雨林大師</div>
            <p style="font-size: 1.2rem; margin: 10px 0;">
                你的最終得分：<strong id="finalScore" style="color: var(--primary); font-size: 1.5rem;">0</strong> / 600 分
            </p>
            <p id="endMessage" style="color: #546e7a; line-height: 1.6; margin-top: 10px;"></p>
            <button class="btn-main" onclick="startGame()">再次挑戰 🔄</button>
        </div>
    </div>
</div>

<script>
    const quizData = [
        {
            topic: "氣候特徵",
            question: "熱帶雨林氣候（如赤道地帶）最主要的氣候特徵是什麼？",
            options: [
                "全年高溫多雨，年溫差小，日溫差較大",
                "全年乾燥少雨，日夜溫差極大",
                "夏季高溫多雨，冬季寒冷乾燥",
                "全年嚴寒，降雪頻繁"
            ],
            correct: 0,
            explanation: "赤道地區全年太陽高度角大，終年高溫且對流雨旺盛；因日夜變化比四季變化明顯，呈現「日溫差大於年溫差」的特徵。"
        },
        {
            topic: "植被垂直結構",
            question: "在熱帶雨林的垂直結構中，哪一層形成密集的「綠色傘蓋」，截留約 90% 的陽光並孕育最多生物？",
            options: [
                "林底層",
                "幼樹層",
                "林冠層",
                "突出層"
            ],
            correct: 2,
            explanation: "林冠層高度約 30 至 40 米，枝葉緊密交織，吸收了絕大部分陽光，是雨林中光合作用最旺盛與物種最豐富的「空中走廊」。"
        },
        {
            topic: "植物適應構造",
            question: "熱帶雨林許多高大喬木基部都長有巨大的「板根」（Buttress roots），其主要功能是什麼？",
            options: [
                "儲存大量水分以備乾旱季節使用",
                "提供額外機械支撐力，穩定淺根系統以支撐高大樹幹",
                "直接從空氣中吸收懸浮養分",
                "抵抗地底森林大火的熱能"
            ],
            correct: 1,
            explanation: "熱帶雨林土壤表層薄且貧瘠，樹木根系較淺，板根能擴大受力面積，支撐高達數十米的巨木不致傾倒。"
        },
        {
            topic: "養分循環 (Gersmehl 模型)",
            question: "在熱帶雨林生態系統中，絕大部分的養分主要儲存在哪一個庫（Store）中？",
            options: [
                "土壤中",
                "枯枝落葉層",
                "地下深層岩石層",
                "活體生物量（樹木與植物體內）"
            ],
            correct: 3,
            explanation: "雨林超過 70% 以上的養分儲存在龐大的活體生物量中；枯枝落葉分解極快且迅速被植物吸收，因此枯枝落葉層儲存量最小。"
        },
        {
            topic: "土壤與淋溶作用",
            question: "熱帶雨林常見的磚紅壤（Latosol）通常呈酸性且養分儲存量小，主要原因是什麼？",
            options: [
                "強烈的降水導致劇烈的「淋溶作用」，將可溶性養分沖刷至深層",
                "氣溫太低導致微生物無法分解落葉",
                "雨林植物不從土壤中吸收任何礦物質",
                "頻繁的火山灰完全覆蓋了土壤"
            ],
            correct: 0,
            explanation: "大量雨水下滲引發強烈的淋溶作用（Leaching），把鈣、鉀等養分沖走，加上植物大量吸收，使土壤中的養分儲存量變得相當小。"
        },
        {
            topic: "環境保育與砍伐影響",
            question: "當大面積熱帶雨林遭到砍伐後，為什麼該地區難以在短時間內自然恢復原貌？",
            options: [
                "砍伐後當地氣溫會驟降至冰點以下",
                "失去樹木遮蔽後陽光再也無法照射到地面",
                "失去植被覆蓋後表土易被暴雨沖刷，養分迅速流失，破壞封閉的養分循環",