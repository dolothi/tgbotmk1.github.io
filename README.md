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
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(180deg, #1c1c1e 0%, #2c2c2e 100%);
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
            font-size: 24px;
            font-weight: 600;
            margin-bottom: 8px;
            background: linear-gradient(90deg, #6c5ce7, #a29bfe);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        .subtitle {
            text-align: center;
            font-size: 14px;
            color: #8e8e93;
            margin-bottom: 32px;
        }

        .options {
            display: flex;
            flex-direction: column;
            gap: 16px;
        }

        .option {
            background: #3a3a3c;
            border-radius: 16px;
            padding: 20px 24px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            border: 2px solid transparent;
        }

        .option.active {
            background: rgba(108, 92, 231, 0.15);
            border-color: #6c5ce7;
        }

        .option-label {
            font-size: 18px;
            font-weight: 500;
            letter-spacing: 0.5px;
        }

        .toggle {
            position: relative;
            width: 56px;
            height: 32px;
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
            background: #5a5a5e;
            border-radius: 16px;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .toggle-slider::before {
            content: '';
            position: absolute;
            width: 26px;
            height: 26px;
            left: 3px;
            top: 3px;
            background: #ffffff;
            border-radius: 50%;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
        }

        .toggle input:checked + .toggle-slider {
            background: #6c5ce7;
        }

        .toggle input:checked + .toggle-slider::before {
            transform: translateX(24px);
        }

        .status {
            text-align: center;
            margin-top: 32px;
            padding: 12px 20px;
            background: rgba(108, 92, 231, 0.1);
            border-radius: 12px;
            border: 1px solid rgba(108, 92, 231, 0.3);
        }

        .status-text {
            font-size: 14px;
            color: #a29bfe;
        }

        .status-count {
            font-weight: 700;
            color: #6c5ce7;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(0.95); }
        }

        .option.deactivating {
            animation: pulse 0.3s ease;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="title">Выбери два</h1>
        <p class="subtitle">Можно активировать только 2 опции</p>
        
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
                <span class="option-label">Маленькая залупа</span>
                <label class="toggle">
                    <input type="checkbox" id="toggle3">
                    <span class="toggle-slider"></span>
                </label>
            </div>
        </div>

        <div class="status">
            <p class="status-text">Активно: <span class="status-count" id="count">0</span> / 2</p>
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

        function getActiveIndices() {
            return toggles
                .map((t, i) => t.checked ? i : -1)
                .filter(i => i !== -1);
        }

        function updateUI() {
            const active = getActiveIndices();
            
            options.forEach((opt, i) => {
                if (toggles[i].checked) {
                    opt.classList.add('active');
                } else {
                    opt.classList.remove('active');
                }
            });

            countEl.textContent = active.length;
        }

        toggles.forEach((toggle, index) => {
            toggle.addEventListener('change', (e) => {
                const active = getActiveIndices();
                
                if (e.target.checked) {
                    if (active.length > 2) {
                        const firstActive = active[0];
                        
                        options[firstActive].classList.add('deactivating');
                        setTimeout(() => {
                            options[firstActive].classList.remove('deactivating');
                        }, 300);
                        
                        toggles[firstActive].checked = false;
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

