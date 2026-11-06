# index.html<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Интерактивная песочница</title>
    <!-- Подключаем Telegram Web App JS для правильной интеграции -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        :root {
            --bg-color: var(--tg-theme-bg-color, #121212);
            --text-color: var(--tg-theme-text-color, #ffffff);
            --hint-color: var(--tg-theme-hint-color, #aaaaaa);
            --button-color: var(--tg-theme-button-color, #6366f1);
            --button-text-color: var(--tg-theme-button-text-color, #ffffff);
            --card-bg: rgba(255, 255, 255, 0.05);
            --border-color: rgba(255, 255, 255, 0.1);
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            padding: 16px;
            overflow-x: hidden;
        }

        header {
            text-align: center;
            margin-bottom: 20px;
        }

        header h1 {
            font-size: 1.5rem;
            font-weight: 700;
            background: linear-gradient(45deg, #6366f1, #a855f7, #ec4899);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            margin-bottom: 4px;
        }

        header p {
            font-size: 0.85rem;
            color: var(--hint-color);
        }

        /* Навигация по вкладкам */
        .tabs {
            display: flex;
            background: var(--card-bg);
            border-radius: 12px;
            padding: 4px;
            margin-bottom: 20px;
            border: 1px solid var(--border-color);
        }

        .tab-btn {
            flex: 1;
            background: transparent;
            border: none;
            color: var(--hint-color);
            padding: 10px;
            font-size: 0.9rem;
            font-weight: 600;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .tab-btn.active {
            background: var(--button-color);
            color: var(--button-text-color);
            box-shadow: 0 4px 12px rgba(99, 102, 241, 0.3);
        }

        /* Контент вкладок */
        .tab-content {
            display: none;
            flex-direction: column;
            flex: 1;
            animation: fadeIn 0.4s ease forwards;
        }

        .tab-content.active {
            display: flex;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(6px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Стили для генератора фактов */
        .fact-card {
            background: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 16px;
            padding: 24px 20px;
            text-align: center;
            margin-bottom: 20px;
            min-height: 180px;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
            position: relative;
            overflow: hidden;
        }

        .fact-text {
            font-size: 1.05rem;
            line-height: 1.5;
            transition: opacity 0.3s ease, transform 0.3s ease;
        }

        .action-btn {
            background: var(--button-color);
            color: var(--button-text-color);
            border: none;
            border-radius: 14px;
            padding: 14px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            width: 100%;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
            transition: transform 0.1s ease, opacity 0.2s;
        }

        .action-btn:active {
            transform: scale(0.97);
        }

        /* Стили для песочницы (пузыри-антистресс) */
        .sandbox-info {
            text-align: center;
            margin-bottom: 15px;
            font-size: 0.9rem;
            color: var(--hint-color);
        }

        .bubbles-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
            margin-bottom: 20px;
        }

        .bubble {
            aspect-ratio: 1;
            background: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            cursor: pointer;
            transition: transform 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275), background 0.3s, border-color 0.3s;
            user-select: none;
        }

        .bubble.popped {
            background: rgba(99, 102, 241, 0.15);
            border-color: var(--button-color);
            transform: scale(0.85);
            color: var(--button-color);
        }

        .reset-sandbox {
            background: transparent;
            border: 1px solid var(--border-color);
            color: var(--text-color);
            border-radius: 12px;
            padding: 10px;
            font-size: 0.9rem;
            cursor: pointer;
            margin-top: auto;
        }
    </style>
</head>
<body>

    <header>
        <h1>Зона Залипания</h1>
        <p>Необычные факты и интерактив</p>
    </header>

    <!-- Переключатель вкладок -->
    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab('facts')">🌌 Факты</button>
        <button class="tab-btn" onclick="switchTab('sandbox')">🫧 Антистресс</button>
    </div>

    <!-- Вкладка 1: Генератор фактов -->
    <div id="facts-tab" class="tab-content active">
        <div class="fact-card">
            <div id="factText" class="fact-text">Нажмите кнопку ниже, чтобы узнать первый удивительный факт!</div>
        </div>
        <button class="action-btn" onclick="getRandomFact()">Следующий факт 🚀</button>
    </div>

    <!-- Вкладка 2: Песочница (Антистресс пупырка) -->
    <div id="sandbox-tab" class="tab-content">
        <div class="sandbox-info">Лопайте пузырьки, чтобы снять напряжение: <span id="poppedCount">0</span>/16</div>
        <div class="bubbles-grid" id="bubblesGrid">
            <!-- Пузырьки генерируются через JS -->
        </div>
        <button class="reset-sandbox" onclick="resetBubbles()">Надуть пузыри заново</button>
    </div>

    <script>
        // Инициализация Telegram Web App
        const tg = window.Telegram.WebApp;
        tg.expand();

        // Данные для генератора фактов
        const facts = [
            "Улитки могут спать до 3 лет подряд, если условия окружающей среды им не подходят.",
            "На Плутоне год длится почти 248 земных лет. С момента открытия Плутона он еще не сделал ни одного полного оборота вокруг Солнца!",
            "Медузы вида Turritopsis dohrnii биологически бессмертны: они умеют возвращаться к фазе полипа и молодеть заново.",
            "Общий вес всех муравьев на Земле примерно равен общему весу всех людей.",
            "В космосе пахнет горячим металлом, сварочным дымом и жареным мясом (со слов астронавтов после выхода в открытый космос).",
            "У осьминогов три сердца, девять мозгов, а их кровь имеет голубой цвет из-за высокого содержания меди.",
            "Самый громкий звук живого существа на планете издает кашалот — его щелканье громче реактивного двигателя.",
            "На Венере сутки длятся дольше, чем целый год (полный оборот вокруг Солнца занимает меньше времени, чем вокруг своей оси).",
            "У тигров полосатая не только шерсть, но и сама кожа под ней!"
        ];

        let lastIndex = -1;

        function getRandomFact() {
            const factElement = document.getElementById('factText');
            factElement.style.opacity = 0;
            factElement.style.transform = 'translateY(5px)';

            setTimeout(() => {
                let randomIndex;
                do {
                    randomIndex = Math.floor(Math.random() * facts.length);
                } while (randomIndex === lastIndex && facts.length > 1);
                
                lastIndex = randomIndex;
                factElement.textContent = facts[randomIndex];
                
                factElement.style.opacity = 1;
                factElement.style.transform = 'translateY(0)';
            }, 200);

            // Легкая вибрация телефона, если поддерживается Telegram
            if (tg.HapticFeedback) {
                tg.HapticFeedback.impactOccurred('light');
            }
        }

        // Переключение вкладок
        function switchTab(tabName) {
            const tabs = document.querySelectorAll('.tab-content');
            const buttons = document.querySelectorAll('.tab-btn');

            tabs.forEach(tab => tab.classList.remove('active'));
            buttons.forEach(btn => btn.classList.remove('active'));

            if (tabName === 'facts') {
                document.getElementById('facts-tab').classList.add('active');
                buttons[0].classList.add('active');
            } else {
                document.getElementById('sandbox-tab').classList.add('active');
                buttons[1].classList.add('active');
            }

            if (tg.HapticFeedback) {
                tg.HapticFeedback.selectionChanged();
            }
        }

        // Инициализация пузырьков антистресса
        let poppedCount = 0;
        const totalBubbles = 16;

        function initBubbles() {
            const grid = document.getElementById('bubblesGrid');
            grid.innerHTML = '';
            poppedCount = 0;
            document.getElementById('poppedCount').textContent = poppedCount;

            for (let i = 0; i < totalBubbles; i++) {
                const bubble = document.createElement('div');
                bubble.className = 'bubble';
                bubble.textContent = '✨';
                bubble.onclick = () => popBubble(bubble);
                grid.appendChild(bubble);
            }
        }

        function popBubble(bubble) {
            if (!bubble.classList.contains('popped')) {
                bubble.classList.add('popped');
                bubble.textContent = '✔';
                poppedCount++;
                document.getElementById('poppedCount').textContent = poppedCount;

                if (tg.HapticFeedback) {
                    tg.HapticFeedback.impactOccurred('medium');
                }
            }
        }

        function resetBubbles() {
            initBubbles();
            if (tg.HapticFeedback) {
                tg.HapticFeedback.notificationOccurred('success');
            }
        }

        // Запуск генерации пузырьков при старте
        initBubbles();
    </script>
</body>
</html>
