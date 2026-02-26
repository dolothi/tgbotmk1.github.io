<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Выбери два</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Segoe UI', Roboto, sans-serif;
            background: 
                radial-gradient(ellipse at 20% 20%, rgba(120, 119, 198, 0.3) 0%, transparent 50%),
                radial-gradient(ellipse at 80% 80%, rgba(74, 78, 200, 0.25) 0%, transparent 50%),
                radial-gradient(ellipse at 40% 40%, rgba(139, 92, 246, 0.15) 0%, transparent 60%),
                linear-gradient(180deg, #0f0f1a 0%, #1a1a2e 50%, #16213e 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
            color: #ffffff;
        }

        .container {
            width: 100%;
            max-width: 400px;
        }

        .title {
            text-align: center;
            font-size: 28px;
            font-weight: 700;
            margin-bottom: 8px;
            background: linear-gradient(135deg, #fff 0%, #a78bfa 50%, #f472b6 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            text-shadow: 0 0 40px rgba(167, 139, 250, 0.3);
        }

        .subtitle {
            text-align: center;
            font-size: 14px;
            color: rgba(255, 255, 255, 0.5);
            margin-bottom: 32px;
            font-weight: 400;
        }

        .options {
            display: flex;
            flex-direction: column;
            gap: 16px;
        }

        .option {
            /* Glassmorphism эффект */
            background: rgba(255, 255, 255, 0.08);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-radius: 20px;
            padding: 22px 26px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 
                0 8px 32px rgba(0, 0, 0, 0.3),
                inset 0 1px 0 rgba(255, 255, 255, 0.1);
        }

        .option:hover {
            background: rgba(255, 255, 255, 0.12);
            transform: translateY(-2px);
            box-shadow: 
                0 12px 40px rgba(0, 0, 0, 0.4),
                inset 0 1px 0 rgba(255, 255, 255, 0.15);
        }

        .option.active {
            background: rgba(167, 139, 250, 0.2);
            border: 1px solid rgba(167, 139, 250, 0.5);
            box-shadow: 
                0 8px 32px rgba(167, 139, 250, 0.3),
                inset 0 1px 0 rgba(255, 255, 255, 0.15),
                0 0 20px rgba(167, 139, 250, 0.2);
        }

        .option-label {
            font-size: 18px;
            font-weight: 500;
            letter-spacing: 0.5px;
            color: rgba(255, 255, 255, 0.9);
        }

        .toggle {
            position: relative;
            width: 60px;
            height: 34px;
            cursor: pointer;
        }

        .toggle input {
            opacity: 0;
            width: 0;
            height: 0;
        }

        .toggle-slider {
            position: absolute;
            inset: 0;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 17px;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            border: 1px solid rgba(255, 255, 255, 0.15);
            box-shadow: inset 0 2px 8px rgba(0, 0, 0, 0.3);
        }

        .toggle-slider::before {
            content: '';
            position: absolute;
            width: 28px;
            height: 28px;
            left: 3px;
            top: 2px;
            background: linear-gradient(135deg, #ffffff 0%, #e0e0e0 100%);
            border-radius: 50%;
            transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 
                0 2px 8px rgba(0, 0, 0, 0.3),
                0 1px 3px rgba(0, 0, 0, 0.2);
        }

        .toggle input:checked + .toggle-slider {
            background: linear-gradient(135deg, #8b5cf6 0%, #a855f7 50%, #c084fc 100%);
            box-shadow: 
                0 0 20px rgba(139, 92, 246, 0.5),
                inset 0 2px 8px rgba(0, 0, 0, 0.2);
        }

        .toggle input:checked + .toggle-slider::before {
            transform: translateX(26px);
            background: linear-gradient(135deg, #ffffff 0%, #f0f0f0 100%);
            box-shadow: 
                0 2px 12px rgba(139, 92, 246, 0.5),
                0 1px 3px rgba(0, 0, 0, 0.2);
        }

        .status {
            text-align: center;
            margin-top: 32px;
            padding: 16px 24px;
            background: rgba(255, 255, 255, 0.08);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-radius: 16px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
        }

        .status-text {
            font-size: 16px;
            color: rgba(255, 255, 255, 0.7);
        }

        .status-count {
            font-weight: 700;
            background: linear-gradient(135deg, #a78bfa 0%, #f472b6 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            font-size: 22px;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(0.97); }
        }

        .option.deactivating {
            animation: pulse 0.3s ease;
        }

        /* Декоративные плавающие элементы */
        .orb {
            position: fixed;
            border-radius: 50%;
            filter: blur(60px);
            pointer-events: none;
            z-index: -1;
        }

        .orb-1 {
            width: 300px;
            height: 300px;
            background: rgba(139, 92, 246, 0.3);
            top: -100px;
            left: -100px;
            animation: float 8s ease-in-out infinite;
        }

        .orb-2 {
            width: 250px;
            height: 250px;
            background: rgba(244, 114, 182, 0.25);
            bottom: -80px;
            right: -80px;
            animation: float 10s ease-in-out infinite reverse;
        }

        @keyframes float {
            0%, 100% { transform: translate(0, 0); }
            50% { transform: translate(30px, 20px); }
        }
    </style>
</head>
<body>
    <!-- Декоративные элементы -->
    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>

    <div class="container">
        <h1 class="title">Выбери два</h1>
        <p class="subtitle">Последние 2 выбранных остаются активными</p>
        
        <div class="options">
            <div class="option" id="option1">
                <span class="option-label">Натурал</span>
                <label class="toggle">
                    <input type="checkbox" id="toggle1">
                    <span class="toggle-slider"></span>
                </label>
            </div>
            
            <div class="option" id="option2">
                <span class="option-label">Имя Сережа</span>
                <label class="toggle">
                    <input type="checkbox" id="toggle2">
                    <span class="toggle-slider"></span>
                </label>
            </div>
            
            <div class="option" id="option3">
                <span class="option-label">Темнокожий</span>
                <label class="toggle">
                    <input type="checkbox" id="toggle3">
                    <span class="toggle-slider"></span>
                </label>
            </div>
        </div>

        <div class="status">
            <p class="status-text">Выбрано: <span class="status-count" id="count">0</span> / 3</p>
        </div>
    </div>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();

        const toggles = [
            document.getElementById('toggle1'),
            document.getElementById('toggle2'),
            document.getElementById('toggle3')
        ];

        const options = [
            document.getElementById('option1'),
            document.getElementById('option2'),
            document.getElementById('option3')
        ];

        const countEl = document.getElementById('count');

        // Хранит порядок активации ползунков
        let activeOrder = [];

        function updateUI() {
            // Показываем только последние 2 активированных
            const active = activeOrder.slice(-2);
            
            options.forEach((opt, i) => {
                if (active.includes(i)) {
                    opt.classList.add('active');
                    toggles[i].checked = true;
                } else {
                    opt.classList.remove('active');
                    toggles[i].checked = false;
                }
            });

            countEl.textContent = active.length;
        }

        toggles.forEach((toggle, index) => {
            toggle.addEventListener('change', (e) => {
                if (e.target.checked) {
                    // Добавляем в конец очереди
                    activeOrder.push(index);
                    
                    // Если активировано больше 2, убираем первый (предпоследний выбранный)
                    if (activeOrder.length > 2) {
                        const removedIndex = activeOrder[0];
                        
                        options[removedIndex].classList.add('deactivating');
                        setTimeout(() => {
                            options[removedIndex].classList.remove('deactivating');
                        }, 300);
                        
                        // Убираем первый элемент
                        activeOrder.shift();
                    }
                } else {
                    // При ручном выключении - убираем из очереди
                    const idx = activeOrder.indexOf(index);
                    if (idx > -1) {
                        activeOrder.splice(idx, 1);
                    }
                }
                
                updateUI();
                tg.ready();
            });
        });

        updateUI();
    </script>
</body>
</html>
