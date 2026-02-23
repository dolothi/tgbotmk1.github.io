<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Todo List</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=SF+Pro+Display:wght@400;500;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --tg-theme-bg-color: #ffffff;
            --tg-theme-secondary-bg-color: #f5f5f5;
            --tg-theme-text-color: #000000;
            --tg-theme-hint-color: #999999;
            --tg-theme-button-color: #2481cc;
            --tg-theme-button-text-color: #ffffff;
            --tg-theme-destructive-bg-color: #ff3b30;
            --tg-theme-header-bg-color: #f5f5f5;
            --tg-viewport-height: 100vh;
            --tg-viewport-width: 100vw;
        }

        /* Общие сбросы и стили */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Segoe UI', Roboto, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background: var(--tg-theme-bg-color);
            color: var(--tg-theme-text-color);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }

        /* Контейнер для ПК */
        @media (min-width: 768px) {
            body {
                align-items: center;
                justify-content: center;
            }
            .main-wrapper {
                width: 400px; /* Увеличил ширину на ПК */
                max-width: 90vw;
                height: 90vh; /* Увеличил высоту */
                border-radius: 20px;
                overflow: hidden;
                box-shadow: 0 10px 30px rgba(0,0,0,0.1);
                display: flex;
                flex-direction: column;
                position: relative;
            }
        }
        /* Стили существующего кода (оставлены почти без изменений) */
        .header {
            background: var(--tg-theme-header-bg-color);
            padding: 16px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid rgba(0,0,0,0.08);
        }

        .header h1 {
            font-size: 20px;
            font-weight: 600;
        }

        .stats {
            font-size: 13px;
            color: var(--tg-theme-hint-color);
        }

        .container {
            flex: 1;
            display: flex;
            flex-direction: column;
            padding: 16px;
            padding-bottom: 100px;
            overflow-y: auto; /* Добавлено для прокрутки контента */
        }

        .input-wrapper {
            display: flex;
            gap: 12px;
            margin-bottom: 20px;
        }

        .input-wrapper input {
            flex: 1;
            padding: 14px 16px;
            border: none;
            border-radius: 12px;
            background: var(--tg-theme-secondary-bg-color);
            color: var(--tg-theme-text-color);
            font-size: 16px;
            outline: none;
            transition: box-shadow 0.2s;
        }

        .input-wrapper input::placeholder {
            color: var(--tg-theme-hint-color);
        }

        .input-wrapper input:focus {
            box-shadow: 0 0 0 2px var(--tg-theme-button-color);
        }

        .add-btn {
            width: 50px;
            height: 50px;
            border: none;
            border-radius: 12px;
            background: var(--tg-theme-button-color);
            color: var(--tg-theme-button-text-color);
            font-size: 24px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: transform 0.15s, opacity 0.15s;
        }

        .add-btn:active {
            transform: scale(0.95);
            opacity: 0.8;
        }

        .todo-list {
            list-style: none;
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .todo-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 14px 16px;
            background: var(--tg-theme-secondary-bg-color);
            border-radius: 12px;
            transition: transform 0.15s, opacity 0.15s;
            position: relative; /* Для конфетти и похвалы */
        }

        .todo-item.completed .todo-text {
            text-decoration: line-through;
            color: var(--tg-theme-hint-color);
        }

        .todo-item.completed .checkbox {
            background: var(--tg-theme-button-color);
            border-color: var(--tg-theme-button-color);
        }

        .todo-item.completed .checkbox::after {
            opacity: 1;
        }

        .todo-item.removing {
            transform: translateX(-100%);
            opacity: 0;
        }

        .checkbox {
            width: 24px;
            height: 24px;
            border: 2px solid var(--tg-theme-hint-color);
            border-radius: 6px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-shrink: 0;
            transition: all 0.2s;
            position: relative;
            z-index: 1;
        }

        .checkbox::after {
            content: '✓';
            color: white;
            font-size: 14px;
            font-weight: bold;
            opacity: 0;
            transition: opacity 0.2s;
        }

        .todo-text {
            flex: 1;
            font-size: 16px;
            word-break: break-word;
        }

        .todo-due-date {
            font-size: 13px;
            color: var(--tg-theme-hint-color);
            margin-left: auto;
            flex-shrink: 0;
            padding-left: 8px;
        }

        .delete-btn {
            width: 32px;
            height: 32px;
            border: none;
            border-radius: 8px;
            background: transparent;
            color: var(--tg-theme-destructive-bg-color);
            font-size: 18px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            opacity: 0.6;
            transition: opacity 0.15s;
        }

        .delete-btn:hover {
            opacity: 1;
        }

        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: var(--tg-theme-hint-color);
        }

        .empty-state svg {
            width: 80px;
            height: 80px;
            margin-bottom: 16px;
            opacity: 0.5;
        }

        .empty-state p {
            font-size: 15px;
        }

        .clear-all-btn {
            margin-top: 20px;
            padding: 12px 24px;
            border: none;
            border-radius: 10px;
            background: var(--tg-theme-destructive-bg-color);
            color: white;
            font-size: 14px;
            font-weight: 500;
            cursor: pointer;
            transition: opacity 0.15s;
            align-self: center;
        }

        .clear-all-btn:active {
            opacity: 0.8;
        }

        /* Анимации */
        @keyframes slideIn {
            from {
                transform: translateY(-10px);
                opacity: 0;
            }
            to {
                transform: translateY(0);
                opacity: 1;
            }
        }

        .todo-item.new-item {
            animation: slideIn 0.3s ease-out;
        }

        /* Более резкое конфетти */
        @keyframes confetti-fade-out {
            0% { opacity: 1; transform: translate(-50%, -50%) scale(1) rotate(0deg); }
            50% { opacity: 1; transform: translate(-50%, -150%) scale(1.2) rotate(180deg); } /* Подлетает вверх */
            100% { opacity: 0; transform: translate(-50%, -250%) scale(0.5) rotate(360deg); } /* Исчезает вверху */
        }
        @keyframes confetti-fall-fast {
            0% { transform: translateY(0) rotate(0deg); opacity: 1; }
            100% { transform: translateY(150px) rotate(360deg); opacity: 0; }
        }

        .confetti {
            position: absolute;
            width: 8px; /* Увеличил размер */
            height: 8px; /* Увеличил размер */
            background-color: var(--color);
            border-radius: 50%;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            animation: confetti-fall-fast 0.8s forwards ease-out; /* Более быстрая анимация */
            pointer-events: none;
            z-index: 2; /* Над элементами */
            will-change: transform, opacity; /* Для оптимизации анимации */
        }
        .confetti-praise {
            position: absolute;
            font-size: 16px;
            font-weight: 600;
            color: var(--tg-theme-button-color); /* Или другой яркий цвет */
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            animation: praise-fade-out 1.5s forwards ease-out;
            pointer-events: none;
            white-space: nowrap;
            z-index: 2;
            text-shadow: 0 0 5px rgba(0,0,0,0.1);
        }
        @keyframes praise-fade-out {
            0% { opacity: 0; transform: translate(-50%, 0px) scale(0.8); }
            10% { opacity: 1; transform: translate(-50%, -20px) scale(1.1); }
            100% { opacity: 0; transform: translate(-50%, -70px) scale(1); }
        }


        /* Тёмная тема */
        @media (prefers-color-scheme: dark) {
            :root {
                --tg-theme-bg-color: #1c1c1e;
                --tg-theme-secondary-bg-color: #2c2c2e;
                --tg-theme-text-color: #ffffff;
                --tg-theme-hint-color: #8e8e93;
                --tg-theme-button-color: #0a84ff;
                --tg-theme-button-text-color: #ffffff;
                --tg-theme-destructive-bg-color: #ff453a;
                --tg-theme-header-bg-color: #1c1c1e;
            }
        }
    </style>
</head>
<body>
    <div class="main-wrapper">
        <div class="header">
            <div class="stats">
                Завершено: <span id="completed-count">0</span> из <span id="total-count">0</span>
            </div>
        </div>

        <div class="container">
            <div class="input-wrapper">
                <input type="text" id="todo-input" placeholder="Новая задача..." maxlength="200">
                <input type="datetime-local" id="due-date-input"> <!-- Сделал видимым -->
                <button class="add-btn" id="add-btn">+</button>
            </div>

            <ul class="todo-list" id="todo-list"></ul>

            <div class="empty-state" id="empty-state" style="display: none;">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                    <path d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
                <p>Ещё нет задач. Самое время добавить первую!</p>
            </div>

            <button class="clear-all-btn" id="clear-all-btn" style="display: none;">Очистить завершенные</button>
        </div>
    </div>

    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.ready();

        // Устанавливаем заголовок страницы (будет отображаться в адресной строке)
        tg.setHeaderColor(tg.themeParams.bg_color || '#ffffff'); // Применяем цвет фона к заголовку
        tg.setBackgroundColor(tg.themeParams.bg_color || '#ffffff'); // Устанавливаем цвет фона

        if (tg.themeParams) {
            const root = document.documentElement;
            if (tg.themeParams.bg_color) root.style.setProperty('--tg-theme-bg-color', tg.themeParams.bg_color);
            if (tg.themeParams.secondary_bg_color) root.style.setProperty('--tg-theme-secondary-bg-color', tg.themeParams.secondary_bg_color);
            if (tg.themeParams.text_color) root.style.setProperty('--tg-theme-text-color', tg.themeParams.text_color);
            if (tg.themeParams.hint_color) root.style.setProperty('--tg-theme-hint-color', tg.themeParams.hint_color);
            if (tg.themeParams.button_color) root.style.setProperty('--tg-theme-button-color', tg.themeParams.button_color);
            if (tg.themeParams.button_text_color) root.style.setProperty('--tg-theme-button-text-color', tg.themeParams.button_text_color);
            if (tg.themeParams.destructive_text_color) root.style.setProperty('--tg-theme-destructive-bg-color', tg.themeParams.destructive_text_color);
            if (tg.themeParams.header_bg_color) root.style.setProperty('--tg-theme-header-bg-color', tg.themeParams.header_bg_color);
        }

        const todoInput = document.getElementById('todo-input');
        const dueDateInput = document.getElementById('due-date-input');
        const addBtn = document.getElementById('add-btn');
        const todoList = document.getElementById('todo-list');
        const emptyState = document.getElementById('empty-state');
        const clearAllBtn = document.getElementById('clear-all-btn');
        const completedCountEl = document.getElementById('completed-count');
        const totalCountEl = document.getElementById('total-count');

        const praises = ["Так держать!", "Молодец!", "Отличная работа!", "Супер!", "Продолжай в том же духе!"];

        let todos = JSON.parse(localStorage.getItem('telegram-todo-list') || '[]');

        function saveTodos() {
            localStorage.setItem('telegram-todo-list', JSON.stringify(todos));
            updateStats();
            updateClearAllButtonVisibility();
            updateMiniAppTitle(); // Обновляем заголовок Mini App
        }

        function updateStats() {
            const completed = todos.filter(t => t.completed).length;
            completedCountEl.textContent = completed;
            totalCountEl.textContent = todos.length;
        }

        function updateMiniAppTitle() {
            const completed = todos.filter(t => t.completed).length;
            const total = todos.length;
            if (total > 0) {
                tg.MainButton.text = `Выполнено: ${completed}/${total}`;
                tg.MainButton.show();
            } else {
                tg.MainButton.hide();
            }
        }


        function updateClearAllButtonVisibility() {
            clearAllBtn.style.display = todos.some(t => t.completed) ? 'block' : 'none';
        }

        function escapeHtml(text) {
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }

        function createTodoElement(todo, index) {
            const li = document.createElement('li');
            li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
            li.dataset.index = index;
            li.innerHTML = `
                <div class="checkbox" data-index="${index}"></div>
                <span class="todo-text">${escapeHtml(todo.text)}</span>
                ${todo.dueDate ? `<span class="todo-due-date">${formatDate(todo.dueDate)}</span>` : ''}
                <button class="delete-btn" data-index="${index}">×</button>
            `;
            return li;
        }

        function renderTodos() {
            todoList.innerHTML = '';
            
            if (todos.length === 0) {
                emptyState.style.display = 'block';
            } else {
                emptyState.style.display = 'none';
                todos.forEach((todo, index) => {
                    todoList.appendChild(createTodoElement(todo, index));
                });
            }
            updateStats();
            updateClearAllButtonVisibility();
            updateMiniAppTitle();
        }

        function addTodo() {
            const text = todoInput.value.trim();
            const dueDate = dueDateInput.value;
            if (!text) return;

            const newTodo = { text, completed: false, dueDate };
            todos.push(newTodo);
            todoInput.value = '';
            dueDateInput.value = '';
            saveTodos();
            
            renderTodos();

            const newItem = todoList.lastElementChild;
            if (newItem) {
                newItem.classList.add('new-item');
                newItem.addEventListener('animationend', () => {
                    newItem.classList.remove('new-item');
                }, { once: true });
            }
            tg.HapticFeedback.impactOccurred('light');
        }

        function toggleTodo(index) {
            todos[index].completed = !todos[index].completed;
            
            const todoItem = todoList.children[index];
            if (todoItem) {
                todoItem.classList.toggle('completed', todos[index].completed);
            }
            
            if (todos[index].completed) {
                triggerConfetti(todoItem.querySelector('.checkbox'));
                triggerPraise(todoItem.querySelector('.checkbox'));
                tg.HapticFeedback.notificationOccurred('success');
            } else {
                tg.HapticFeedback.impactOccurred('light');
            }

            saveTodos();
            updateClearAllButtonVisibility();
        }

        function deleteTodo(index) {
            const item = todoList.children[index];
            if (item) {
                item.classList.add('removing');
                setTimeout(() => {
                    todos.splice(index, 1);
                    saveTodos();
                    renderTodos();
                    tg.HapticFeedback.impactOccurred('medium');
                }, 300);
            }
        }

        function clearCompleted() {
            todos = todos.filter(t => !t.completed);
            saveTodos();
            renderTodos();
            tg.HapticFeedback.impactOccurred('medium');
        }

        function formatDate(dateString) {
            const options = { year: 'numeric', month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' };
            return new Date(dateString).toLocaleDateString('ru-RU', options);
        }

        function triggerConfetti(targetElement) {
            const colors = ['#f44336', '#e91e63', '#9c27b0', '#673ab7', '#3f51b5', '#2196f3', '#03a9f4', '#00bcd4', '#009688', '#4CAF50', '#8BC34A', '#CDDC39', '#FFEB3B', '#FFC107', '#FF9800', '#FF5722'];
            const numberOfConfetti = 20; // Уменьшил количество для лучшей производительности
            
            const rect = targetElement.getBoundingClientRect();
            const startX = rect.left + rect.width / 2;
            const startY = rect.top + rect.height / 2;

            for (let i = 0; i < numberOfConfetti; i++) {
                const confetti = document.createElement('div');
                confetti.classList.add('confetti');
                confetti.style.setProperty('--color', colors[Math.floor(Math.random() * colors.length)]);
                confetti.style.left = `${startX}px`;
                confetti.style.top = `${startY}px`;
                confetti.style.animationDelay = `${Math.random() * 0.2}s`; // Уменьшил задержку
                // Задаем случайное направление для разлета
                const angle = Math.random() * Math.PI * 2; // От 0 до 2*PI
                const distance = Math.random() * 50 + 20; // От 20 до 70px
                confetti.style.setProperty('--travelX', `${Math.cos(angle) * distance}px`);
                confetti.style.setProperty('--travelY', `${Math.sin(angle) * distance}px`);

                document.body.appendChild(confetti);

                confetti.addEventListener('animationend', () => {
                    confetti.remove();
                });
            }
        }
        function triggerPraise(targetElement) {
            const praiseText = praises[Math.floor(Math.random() * praises.length)];
            const praiseElement = document.createElement('div');
            praiseElement.classList.add('confetti-praise');
            praiseElement.textContent = praiseText;
            
            const rect = targetElement.getBoundingClientRect();
            praiseElement.style.left = `${rect.left + rect.width / 2}px`;
            praiseElement.style.top = `${rect.top + rect.height / 2}px`;
            
            document.body.appendChild(praiseElement);

            praiseElement.addEventListener('animationend', () => {
                praiseElement.remove();
            });
        }


        addBtn.addEventListener('click', addTodo);
        
        todoInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') addTodo();
        });

        todoList.addEventListener('click', (e) => {
            const checkbox = e.target.closest('.checkbox');
            const deleteBtn = e.target.closest('.delete-btn');
            
            if (checkbox) {
                const index = parseInt(checkbox.dataset.index);
                toggleTodo(index);
            } else if (deleteBtn) {
                const index = parseInt(deleteBtn.dataset.index);
                deleteTodo(index);
            }
        });

        clearAllBtn.addEventListener('click', clearCompleted);

        renderTodos();

        tg.onEvent('viewportChanged', () => {
            tg.ready();
            // Эти параметры уже должны быть установлены при инициализации.
            //tg.setHeaderColor(tg.themeParams.bg_color || '#ffffff');
            //tg.setBackgroundColor(tg.themeParams.bg_color || '#ffffff');
        });
        
        // Добавьте это, чтобы кнопка Telegram MainButton не появлялась дважды
        tg.onEvent('mainButtonClicked', () => {
            // В нашем случае, MainButton используется как индикатор,
            // но если бы она была интерактивной, здесь была бы логика.
        });
    </script>
</body>
</html>
