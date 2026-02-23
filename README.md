<!DOCTYPE html>
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
        }

        .clear-all-btn:active {
            opacity: 0.8;
        }

        /* Анимации */
        @keyframes slideIn {
            from {
                transform: translateX(-20px);
                opacity: 0;
            }
            to {
                transform: translateX(0);
                opacity: 1;
            }
        }

        .todo-item {
            animation: slideIn 0.3s ease;
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
    <div class="header">
        <h1>📝 Задачи</h1>
        <div class="stats">
            <span id="completed-count">0</span>/<span id="total-count">0</span>
        </div>
    </div>

    <div class="container">
        <div class="input-wrapper">
            <input type="text" id="todo-input" placeholder="Новая задача..." maxlength="200">
            <button class="add-btn" id="add-btn">+</button>
        </div>

        <ul class="todo-list" id="todo-list"></ul>

        <div class="empty-state" id="empty-state" style="display: none;">
            <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                <path d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            <p>Пока нет задач</p>
        </div>

        <button class="clear-all-btn" id="clear-all-btn" style="display: none;">Очистить все</button>
    </div>

    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script>
        // Инициализация Telegram Web App
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.ready();

        // Цветовая схема из Telegram
        if (tg.themeParams) {
            const root = document.documentElement;
            if (tg.themeParams.bg_color) root.style.setProperty('--tg-theme-bg-color', tg.themeParams.bg_color);
            if (tg.themeParams.secondary_bg_color) root.style.setProperty('--tg-theme-secondary-bg-color', tg.themeParams.secondary_bg_color);
            if (tg.themeParams.text_color) root.style.setProperty('--tg-theme-text-color', tg.themeParams.text_color);
            if (tg.themeParams.hint_color) root.style.setProperty('--tg-theme-hint-color', tg.themeParams.hint_color);
            if (tg.themeParams.button_color) root.style.setProperty('--tg-theme-button-color', tg.themeParams.button_color);
            if (tg.themeParams.button_text_color) root.style.setProperty('--tg-theme-button-text-color', tg.themeParams.button_text_color);
            if (tg.themeParams.destructive_text_color) root.style.setProperty('--tg-theme-destructive-bg-color', tg.themeParams.destructive_text_color);
        }

        // DOM элементы
        const todoInput = document.getElementById('todo-input');
        const addBtn = document.getElementById('add-btn');
        const todoList = document.getElementById('todo-list');
        const emptyState = document.getElementById('empty-state');
        const clearAllBtn = document.getElementById('clear-all-btn');
        const completedCountEl = document.getElementById('completed-count');
        const totalCountEl = document.getElementById('total-count');

        // Загрузка задач из localStorage
        let todos = JSON.parse(localStorage.getItem('telegram-todo-list') || '[]');

        // Сохранение задач
        function saveTodos() {
            localStorage.setItem('telegram-todo-list', JSON.stringify(todos));
            updateStats();
        }

        // Обновление счётчиков
        function updateStats() {
            const completed = todos.filter(t => t.completed).length;
            completedCountEl.textContent = completed;
            totalCountEl.textContent = todos.length;
        }

        // Рендер списка задач
        function renderTodos() {
            todoList.innerHTML = '';
            
            if (todos.length === 0) {
                emptyState.style.display = 'block';
                clearAllBtn.style.display = 'none';
            } else {
                emptyState.style.display = 'none';
                clearAllBtn.style.display = todos.some(t => t.completed) ? 'block' : 'none';
            }

            todos.forEach((todo, index) => {
                const li = document.createElement('li');
                li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
                li.innerHTML = `
                    <div class="checkbox" data-index="${index}"></div>
                    <span class="todo-text">${escapeHtml(todo.text)}</span>
                    <button class="delete-btn" data-index="${index}">×</button>
                `;
                todoList.appendChild(li);
            });

            updateStats();
        }

        // Экранирование HTML
        function escapeHtml(text) {
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }

        // Добавление задачи
        function addTodo() {
            const text = todoInput.value.trim();
            if (!text) return;

            todos.push({ text, completed: false });
            todoInput.value = '';
            saveTodos();
            renderTodos();
            tg.HapticFeedback.impactOccurred('light');
        }

        // Переключение статуса задачи
        function toggleTodo(index) {
            todos[index].completed = !todos[index].completed;
            saveTodos();
            renderTodos();
            tg.HapticFeedback.impactOccurred('light');
        }

        // Удаление задачи
        function deleteTodo(index) {
            const item = todoList.children[index];
            item.classList.add('removing');
            
            setTimeout(() => {
                todos.splice(index, 1);
                saveTodos();
                renderTodos();
                tg.HapticFeedback.impactOccurred('medium');
            }, 150);
        }

        // Очистка выполненных
        function clearCompleted() {
            todos = todos.filter(t => !t.completed);
            saveTodos();
            renderTodos();
            tg.HapticFeedback.impactOccurred('medium');
        }

        // Обработчики событий
        addBtn.addEventListener('click', addTodo);
        
        todoInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') addTodo();
        });

        todoList.addEventListener('click', (e) => {
            const checkbox = e.target.closest('.checkbox');
            const deleteBtn = e.target.closest('.delete-btn');
            
            if (checkbox) {
                toggleTodo(parseInt(checkbox.dataset.index));
            } else if (deleteBtn) {
                deleteTodo(parseInt(deleteBtn.dataset.index));
            }
        });

        clearAllBtn.addEventListener('click', clearCompleted);

        // Первичный рендер
        renderTodos();

        // Отправка данных в бота при изменении
        tg.onEvent('viewportChanged', () => {
            tg.ready();
        });
    </script>
</body>
</html>
