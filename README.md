# <!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>羊了個羊 · 三消挑战</title>
    <style>
        * {
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            box-sizing: border-box;
        }

        body {
            margin: 0;
            min-height: 100vh;
            background: linear-gradient(145deg, #1b5e2a 0%, #0a3b1a 100%);
            font-family: 'Segoe UI', 'Poppins', 'Noto Sans TC', system-ui, -apple-system, 'Helvetica Neue', sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            touch-action: manipulation;
        }

        /* 游戏主容器 - 手机友好尺寸 */
        .game-container {
            width: 100%;
            max-width: 450px;
            margin: 12px 12px;
            background: rgba(255,248,205,0.85);
            border-radius: 56px 56px 48px 48px;
            box-shadow: 0 20px 35px rgba(0,0,0,0.4), inset 0 1px 4px rgba(255,255,200,0.8);
            backdrop-filter: blur(2px);
            padding: 16px 12px 24px;
            transition: all 0.2s;
        }

        /* 顶部状态栏 */
        .game-header {
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            background: #fdf8e7;
            padding: 12px 18px;
            border-radius: 60px;
            margin-bottom: 20px;
            box-shadow: inset 0 1px 4px #0001, 0 5px 10px rgba(0,0,0,0.2);
        }

        .score-box, .remaining-box {
            background: #2c2b28;
            padding: 6px 18px;
            border-radius: 40px;
            color: #ffeaac;
            font-weight: bold;
            font-size: 1.4rem;
            text-shadow: 0 1px 0 #5f3f00;
            letter-spacing: 1px;
        }

        .score-box span, .remaining-box span {
            font-size: 0.8rem;
            color: #ddb76e;
            margin-right: 6px;
        }

        /* 卡槽区 (类似羊了个羊底部槽位) */
        .slot-area {
            background: #e7dbb6;
            border-radius: 48px;
            padding: 12px 12px;
            margin-bottom: 20px;
            box-shadow: inset 0 0 0 3px #fff9e8, inset 0 0 0 6px #b9975a, 0 8px 18px black;
        }

        .slot-title {
            text-align: center;
            font-size: 0.7rem;
            font-weight: bold;
            color: #7a5610;
            background: #fffaec;
            display: inline-block;
            width: auto;
            margin: 0 auto 8px;
            padding: 2px 16px;
            border-radius: 20px;
            letter-spacing: 1px;
        }

        .slots {
            display: flex;
            flex-wrap: nowrap;
            justify-content: center;
            gap: 10px;
            overflow-x: auto;
            padding: 8px 4px;
            min-height: 85px;
        }

        .slot-card {
            width: 70px;
            height: 85px;
            background: #fef1ca;
            border-radius: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.6rem;
            font-weight: bold;
            box-shadow: 0 6px 0 #aa7c3c;
            transition: all 0.1s ease;
            flex-shrink: 0;
            position: relative;
            background: #fff3df;
            border: 1px solid #ecc48b;
        }

        /* 卡片槽位7个, 超出预警红色闪烁 (视觉提示) */
        .slot-card.warning {
            background: #ffb4a2;
            box-shadow: 0 0 0 2px #ff4444;
            transform: scale(0.98);
        }

        /* 主牌堆区域 (层叠卡片) */
        .deck-area {
            background: #ccaa75;
            border-radius: 48px;
            padding: 16px 8px;
            margin-bottom: 16px;
            box-shadow: inset 0 0 0 3px #fef0cf, inset 0 0 0 6px #b47c44, 0 6px 12px black;
        }

        .cards-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px 10px;
            justify-items: center;
            align-items: center;
        }

        /* 卡片样式: 圆润可爱 */
        .card {
            background: #fff7e8;
            width: 100%;
            aspect-ratio: 3 / 4;
            max-width: 85px;
            border-radius: 18px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2.2rem;
            font-weight: bold;
            box-shadow: 0 8px 0 #a86f2e;
            transition: 0.05s linear;
            cursor: pointer;
            touch-action: manipulation;
            transform: translateY(-2px);
            border: 1px solid #fce6b4;
        }

        .card:active {
            transform: translateY(4px);
            box-shadow: 0 3px 0 #a86f2e;
        }

        /* 不可见/占位 */
        .card.removed {
            visibility: hidden;
            pointer-events: none;
            box-shadow: none;
            background: transparent;
            transform: scale(0.9);
        }

        /* 重置按钮 + 提示 */
        .control-bar {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-top: 15px;
        }

        button {
            background: #ffdd99;
            border: none;
            font-size: 1.1rem;
            font-weight: bold;
            padding: 10px 28px;
            border-radius: 60px;
            color: #4f2d0a;
            box-shadow: 0 5px 0 #b16224;
            transition: 0.07s linear;
            cursor: pointer;
            font-family: inherit;
            touch-action: manipulation;
        }

        button:active {
            transform: translateY(3px);
            box-shadow: 0 2px 0 #b16224;
        }

        .message-area {
            text-align: center;
            margin-top: 16px;
            font-size: 0.9rem;
            font-weight: bold;
            background: #2b2b1faa;
            backdrop-filter: blur(4px);
            border-radius: 40px;
            padding: 6px;
            color: #ffefb9;
        }

        /* 手机窄屏适配 */
        @media (max-width: 480px) {
            .game-container {
                margin: 8px;
                padding: 12px 8px 20px;
            }
            .slot-card {
                width: 60px;
                height: 75px;
                font-size: 2rem;
            }
            .card {
                font-size: 1.8rem;
            }
            .cards-grid {
                gap: 8px;
            }
            .score-box, .remaining-box {
                padding: 4px 12px;
                font-size: 1.1rem;
            }
        }
        @media (max-width: 380px) {
            .slot-card {
                width: 55px;
                height: 70px;
                font-size: 1.7rem;
            }
            .card {
                font-size: 1.5rem;
            }
        }
    </style>
</head>
<body>
<div class="game-container">
    <div class="game-header">
        <div class="score-box"><span>🐏 积分</span> <span id="scoreValue">0</span></div>
        <div class="remaining-box"><span>📦 剩余</span> <span id="remainingCount">0</span></div>
    </div>

    <div class="slot-area">
        <div style="text-align: center;"><span class="slot-title">📥 卡槽 (最多7张)</span></div>
        <div class="slots" id="slotContainer"></div>
    </div>

    <div class="deck-area">
        <div class="cards-grid" id="cardsGrid"></div>
    </div>

    <div class="control-bar">
        <button id="resetBtn">🔄 重来</button>
        <button id="shuffleBtn">♻️ 洗牌</button>
    </div>
    <div class="message-area" id="gameMessage">点击卡片，放入卡槽，三个相同消除！</div>
</div>

<script>
    // ---------- 游戏配置 ----------
    // 定义可用图标 (羊了个羊风格，可爱emoji/符号)
    const ICONS = ['🌿', '🍂', '🔥', '💧', '⭐', '❤️', '🍎', '🐑', '🐏', '🌸', '🌻', '🍀'];
    // 难度设定: 生成多少对? 我们生成6种图案，每种出现6次 => 共36张，排成4x9? 但网格最好排4x? 动态
    // 为了更丰富且过关有挑战，卡片总数: 32张 (8种图案各4张) -> 可消除分数 = 32/3 ≈ 10组有余数，但不一定完全消光，随机布局加上洗牌可玩性高
    // 但羊了个羊核心难点是槽位限制且多层，此处简化但机制一致: 点击卡片进入槽位，槽位满7且没有三消则失败。消除后得分并移除槽内三张相同。
    // 为了更有趣，牌堆所有卡片都是可见的，无层级，但可以洗牌并重新挑战，像"羊了个羊"轻量版。
    let currentCards = [];        // 存储卡片对象 { id, icon, visible (始终true，移除就删除) }
    let slotCards = [];           // 存储当前槽位中的图标 (字符串数组)
    let score = 0;               // 得分 (每消除一组+10)
    let gameActive = true;       // 是否游戏结束(胜利/失败)
    
    // DOM 元素
    const cardsGrid = document.getElementById('cardsGrid');
    const slotContainer = document.getElementById('slotContainer');
    const scoreSpan = document.getElementById('scoreValue');
    const remainingSpan = document.getElementById('remainingCount');
    const resetBtn = document.getElementById('resetBtn');
    const shuffleBtn = document.getElementById('shuffleBtn');
    const messageDiv = document.getElementById('gameMessage');
    
    // 辅助函数: 更新UI (卡片网格, 槽位, 剩余牌数)
    function updateUI() {
        // 更新分数显示
        scoreSpan.innerText = score;
        // 更新剩余未使用卡片数量
        const remaining = currentCards.filter(c => !c.removed).length;
        remainingSpan.innerText = remaining;
        
        // 渲染牌堆
        cardsGrid.innerHTML = '';
        currentCards.forEach(card => {
            if (!card.removed) {
                const cardDiv = document.createElement('div');
                cardDiv.className = 'card';
                cardDiv.innerText = card.icon;
                cardDiv.setAttribute('data-id', card.id);
                cardDiv.addEventListener('click', (e) => {
                    e.stopPropagation();
                    if (!gameActive) {
                        messageDiv.innerText = gameActive === false && remaining === 0 && slotCards.length === 0 ? "🎉 恭喜通关！按重来继续 🎉" : "游戏已结束，请按重来";
                        return;
                    }
                    onCardClick(card.id);
                });
                cardsGrid.appendChild(cardDiv);
            }
        });
        
        // 渲染槽位 (最多7个，显示警告样式)
        slotContainer.innerHTML = '';
        for (let i = 0; i < slotCards.length; i++) {
            const slotDiv = document.createElement('div');
            slotDiv.className = 'slot-card';
            if (slotCards.length >= 7) slotDiv.classList.add('warning');
            slotDiv.innerText = slotCards[i];
            slotContainer.appendChild(slotDiv);
        }
        // 补齐空白槽位占位（保持样式）
        for (let i = slotCards.length; i < 7; i++) {
            const emptyDiv = document.createElement('div');
            emptyDiv.className = 'slot-card';
            emptyDiv.style.background = '#eeddbb';
            emptyDiv.style.boxShadow = 'inset 0 0 0 1px #bba46c';
            emptyDiv.style.opacity = '0.5';
            emptyDiv.innerText = '?';
            slotContainer.appendChild(emptyDiv);
        }
        
        // 检查失败情况 (槽位>=7 且无三消可能, 但实际上每次添加卡片后调用eliminateAndCheck)
    }
    
    // 消除槽位中所有能消除的组 (递归消除直到没有三连)
    function eliminateFromSlot() {
        let eliminated = false;
        // 统计槽位中每个图标的出现次数
        const countMap = new Map();
        for (let icon of slotCards) {
            countMap.set(icon, (countMap.get(icon) || 0) + 1);
        }
        let toEliminateIcon = null;
        for (let [icon, cnt] of countMap.entries()) {
            if (cnt >= 3) {
                toEliminateIcon = icon;
                break;
            }
        }
        if (toEliminateIcon) {
            // 移除槽位中该图标的前三个 (实际移除所有该图标中三次，羊了个羊经典消三张相同)
            let removedCount = 0;
            const newSlot = [];
            for (let icon of slotCards) {
                if (icon === toEliminateIcon && removedCount < 3) {
                    removedCount++;
                    continue;
                }
                newSlot.push(icon);
            }
            slotCards = newSlot;
            score += 10;
            eliminated = true;
            messageDiv.innerText = `✨ 消除 ${toEliminateIcon} ✨ +10分 !`;
            // 递归继续消除 (可能剩下的还有三连)
            if (eliminated) {
                eliminateFromSlot(); // 递归直到无消除
            }
        }
        // 如果没有任何消除，但槽位已经大于等于7张并且游戏未结束 => 游戏失败
        if (!eliminated && slotCards.length >= 7 && gameActive) {
            gameActive = false;
            messageDiv.innerText = '😭 槽位已满，无法消除... 游戏失败！点击重来 😭';
            updateUI();
            return false;
        }
        // 胜利检测: 牌堆无剩余 & 槽位为空
        const remainingCards = currentCards.filter(c => !c.removed).length;
        if (remainingCards === 0 && slotCards.length === 0 && gameActive === true) {
            gameActive = false;
            messageDiv.innerText = '🎉 恭喜通关！ 你是真正的羊了个羊大师！ 🎉';
            updateUI();
            return true;
        }
        updateUI();
        return eliminated;
    }
    
    // 处理点击卡片
    function onCardClick(cardId) {
        if (!gameActive) return;
        const cardIndex = currentCards.findIndex(c => c.id === cardId && !c.removed);
        if (cardIndex === -1) return;
        const card = currentCards[cardIndex];
        // 将卡片图标加入到槽位
        slotCards.push(card.icon);
        // 标记卡片为已移除
        card.removed = true;
        
        // 更新UI再消除
        updateUI();
        
        // 执行消除逻辑 (同时会检测失败/胜利)
        const hadEliminate = eliminateFromSlot(); // 内部会更新UI并检测失败
        
        // 如果游戏失败gameActive被设为false则不用额外处理
        if (!gameActive) return;
        
        // 额外胜利判定如果刚刚消除后空槽且无牌了，eliminateFromSlot已经设置胜利
        const remainingAfter = currentCards.filter(c => !c.removed).length;
        if (remainingAfter === 0 && slotCards.length === 0 && gameActive) {
            gameActive = false;
            messageDiv.innerText = '🎉 完美通关！ 🎉';
            updateUI();
        } else if (slotCards.length >= 7 && !hadEliminate) {
            // 安全兜底: 如果槽位7张且消除函数没有消除(无三连)那么失败
            gameActive = false;
            messageDiv.innerText = '💥 槽位爆炸了... 游戏结束 💥';
            updateUI();
        } else {
            // 一切正常， 刷新提示
            if (gameActive) messageDiv.innerText = '继续点击卡片，凑三消～';
        }
    }
    
    // 重置/初始化游戏
    function initGame() {
        // 生成卡片组: 使用前8种图标, 每种出现4次 => 共32张 (可玩性不错)
        // 为了更有挑战，也可随机多几种，但保持总数在30~36之间手机操作舒适
        const usedIcons = ICONS.slice(0, 8);  // 8种图案
        let deck = [];
        let idCounter = 0;
        // 每个图标出现4张 -> 32张
        for (let icon of usedIcons) {
            for (let i = 0; i < 4; i++) {
                deck.push({ id: idCounter++, icon: icon, removed: false });
            }
        }
        // 随机打乱
        deck = shuffleArray(deck);
        currentCards = deck;
        slotCards = [];
        score = 0;
        gameActive = true;
        messageDiv.innerText = '🐏 点击卡片，放入卡槽，三个相同消除！槽位最多7张，注意策略~';
        updateUI();
    }
    
    // Fisher-Yates 洗牌
    function shuffleArray(arr) {
        for (let i = arr.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [arr[i], arr[j]] = [arr[j], arr[i]];
        }
        return arr;
    }
    
    // 洗牌功能 (仅洗剩余未移除的卡片，保留移除状态)
    function shuffleRemainingCards() {
        if (!gameActive) {
            // 游戏结束后洗牌没有意义, 可以重置, 但为了体验可直接重置? 要么提示重置，为了交互, 如果游戏结束, 调用重置？
            messageDiv.innerText = '游戏已结束，请按"重来"开始新游戏~';
            return;
        }
        // 获取未移除卡片
        const activeCards = currentCards.filter(c => !c.removed);
        if (activeCards.length === 0) {
            messageDiv.innerText = '已经没有卡片了！太厉害了，按重来继续挑战';
            if (slotCards.length === 0) gameActive = false;
            return;
        }
        // 暂存当前槽位里的内容，并且不能影响槽位吗？羊了个羊洗牌通常只重置场上牌的顺序，保留槽位
        // 但为了避免洗牌后槽位不匹配导致失去平衡，洗牌只改变剩余卡片的顺序，不会影响槽位现有卡片。
        // 为了玩家体验，洗牌前需要先判断当前牌桌状态，不能清空槽位
        const remainingIds = activeCards.map(c => c.id);
        // 提取这些对象的引用
        const remainingObjs = activeCards;
        // 重新随机排序
        const shuffledRemaining = shuffleArray([...remainingObjs]);
        // 按照新顺序重新构建currentCards中未移除卡片的顺序（保留移除卡的位置）
        let newCurrentCards = [];
        let shuffledIndex = 0;
        for (let card of currentCards) {
            if (card.removed) {
                newCurrentCards.push(card);
            } else {
                // 从洗好的数组中取
                const newCard = shuffledRemaining[shuffledIndex++];
                // 保证id不变保留引用，但图标不变，只是顺序变化
                newCurrentCards.push(newCard);
            }
        }
        currentCards = newCurrentCards;
        // 洗牌后刷新，不清除槽位，可能帮助玩家形成三消
        updateUI();
        messageDiv.innerText = '♻️ 牌堆已重排，继续挑战！注意卡槽容量~';
        // 洗牌后再次自动检测槽位如果有三消直接消除? 为了公平起见，调用eliminateFromSlot触发自动消除（防止槽位已经有三个相同但是之前未自动消除? 之前逻辑每次添加卡片都有消除，洗牌不改变槽位，但依然需要检查是否存在槽位三消）
        eliminateFromSlot();
        // 二次胜利判定
        const remainingAfter = currentCards.filter(c => !c.removed).length;
        if (remainingAfter === 0 && slotCards.length === 0 && gameActive) {
            gameActive = false;
            messageDiv.innerText = '🎉 洗牌后通关！太神啦 🎉';
            updateUI();
        }
    }
    
    // 重置游戏（完整重置）
    function resetGame() {
        initGame();
    }
    
    // 绑定事件
    resetBtn.addEventListener('click', () => {
        resetGame();
    });
    shuffleBtn.addEventListener('click', () => {
        if (!gameActive) {
            // 如果游戏结束了但玩家还想挣扎，重置更方便? 不过保留重置按钮够用，也可以给个提示
            if (currentCards.filter(c=>!c.removed).length === 0 && slotCards.length === 0) {
                messageDiv.innerText = '游戏已通关！点击重来继续挑战新牌局';
            } else {
                messageDiv.innerText = '游戏已经结束，请按重来按钮吧';
            }
            return;
        }
        shuffleRemainingCards();
    });
    
    // 手机触摸优化 & 初始化
    initGame();
    
    // 额外确保卡片点击无延迟
    document.querySelectorAll('button, .card').forEach(el => {
        el.addEventListener('touchstart', (e) => {
            // 只是为了提高移动端响应，不用preventDefault以免影响滚动
        });
    });
    
    // 增加一个简单工具，当槽位太多时显示友好
    // 也监听胜利失败后，重新生成
    
    // 补充：因为洗牌时可能当前的card grid数量很多，显示良好。整体设计符合手机竖直滑动体验
</script>
</body>
</html>