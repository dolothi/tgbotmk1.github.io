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
            display: flex;
            flex-direction: column;
            width: 100vw;
            height: 100vh;
            overflow: hidden; /* Предотвращаем скролл на body */
        }

        .main-wrapper {
            flex: 1;
            display: flex;
            flex-direction: column;
            width: 100%;
            height: 100%;
            position: relative;
        }

        /* Контейнер для ПК */
        @media (min-width: 768px) {
            body {
                align-items: center;
                justify-content: center;
                overflow: auto;
            }
            .main-wrapper {
                width: 400px; /* Ширина мобильного устройства */
                max-width: 90vw;
                /* Соотношение 16:9 при ширине 400px = 225px (высота) * 16/9. Или 400px * 16/9 = 711px*/
                height: calc(400px * 16 / 9); /* Пробую динамическую высоту для 16:9 */
                max-height: 90vh; /* Ограничиваем высоту */
                border-radius: 20px;
                overflow: hidden;
                box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            }
        }
        
        .header {
            background: var(--tg-theme-header-bg-color);
            padding: 16px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 1px solid rgba(0,0,0,0.08);
            position: sticky; /* Sticky header */
            top: 0;
            z-index: 10;
            flex-shrink: 0;
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
            padding-bottom: 16px;
            overflow-y: auto;
        }

        .input-wrapper {
            display: flex;
            gap: 12px;
            margin-bottom: 20px;
            flex-shrink: 0;
        }

        .input-wrapper input[type="text"] {
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
        .time-select-btn {
            width: 50px;
            height: 50px;
            border: none;
            border-radius: 12px;
            background: var(--tg-theme-secondary-bg-color);
            color: var(--tg-theme-text-color);
            font-size: 20px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: transform 0.15s, opacity 0.15s;
            flex-shrink: 0;
        }
        .time-select-btn:active {
            transform: scale(0.95);
            opacity: 0.8;
        }
        .time-select-btn.active {
            box-shadow: 0 0 0 2px var(--tg-theme-button-color); /* Выделение активной кнопки */
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
            flex-shrink: 0;
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
            transition: transform 0.15s, opacity 0.15s, background 0.2s;
            position: relative;
            will-change: transform, opacity; /* Оптимизация анимации */
        }
        .todo-item.overdue {
            background-color: var(--tg-theme-destructive-bg-color);
        }
        .todo-item.overdue .todo-text, .todo-item.overdue .todo-due-date {
            color: white;
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
           flex-grow: 1; /* Занимает доступное пространство */
           display: flex;
           flex-direction: column;
           align-items: center;
           justify-content: center;
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
            margin-top: auto;
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
            flex-shrink: 0;
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

        /* Анимация конфетти (КРУГЛЫЕ) */
        @keyframes confetti-fall-round {
            0% { transform: scale(0) translate(0, 0); opacity: 1; }
            50% { transform: scale(1) translate(var(--travelX), var(--travelY)); opacity: 1; }
            100% { transform: scale(0.2) translate(var(--travelX), var(--travelY-end)); opacity: 0; }
        }

        .confetti {
            position: absolute;
            width: 8px; /* Размер кружка */
            height: 8px;
            background-color: var(--color);
            border-radius: 50%;
            animation: confetti-fall-round 1.5s forwards ease-out; /* Увеличил время для плавности */
            pointer-events: none;
            z-index: 20; /* Выше других элементов */
            will-change: transform, opacity; /* Оптимизация анимации */
        }
        .confetti-praise {
            position: absolute;
            font-size: 16px;
            font-weight: 600;
            color: var(--tg-theme-button-color);
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            animation: praise-fade-out 1.5s forwards ease-out;
            pointer-events: none;
            white-space: nowrap;
            z-index: 20; /* Выше других элементов */
            text-shadow: 0 0 5px rgba(0,0,0,0.1);
            will-change: transform, opacity; /* Оптимизация анимации */
        }
        @keyframes praise-fade-out {
            0% { opacity: 0; transform: translate(-50%, 0px) scale(0.8); }
            10% { opacity: 1; transform: translate(-50%, -20px) scale(1.1); }
            100% { opacity: 0; transform: translate(-50%, -70px) scale(1); }
        }

        /* Эффект жидкого стекла */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.4);
            backdrop-filter: blur(8px);
            -webkit-backdrop-filter: blur(8px);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 100; /* Самый высокий z-index */
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.2s ease-out;
            will-change: opacity; /* Оптимизация анимации */
        }
        .modal-overlay.active {
            opacity: 1;
            pointer-events: all;
        }
        .modal-content {
            background: var(--tg-theme-secondary-bg-color);
            border-radius: 14px;
            padding: 20px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
            width: 300px;
            max-width: 90vw;
            transform: translateY(20px);
            opacity: 0;
            transition: transform 0.2s ease-out, opacity 0.2s ease-out;
            will-change: transform, opacity; /* Оптимизация анимации */
        }
        .modal-overlay.active .modal-content {
            transform: translateY(0);
            opacity: 1;
        }
        .modal-content h3 {
            margin-top: 0;
            margin-bottom: 15px;
            font-size: 18px;
            color: var(--tg-theme-text-color);
        }
        .modal-content input[type="time"] {
            width: 100%;
            padding: 10px;
            border: 1px solid var(--tg-theme-hint-color);
            border-radius: 8px;
            background: var(--tg-theme-bg-color);
            color: var(--tg-theme-text-color);
            font-size: 16px;
            margin-bottom: 20px;
            -webkit-appearance: none;
            outline: none;
        }
        @supports (-webkit-overflow-scrolling: touch) {
            .modal-content input[type="time"] {
                padding: 12px;
                border: none;
                border-radius: 10px;
                background: var(--tg-theme-secondary-bg-color);
            }
        }
        .modal-buttons {
            display: flex;
            justify-content: space-between;
            gap: 10px;
        }
        .modal-buttons button {
            flex: 1;
            padding: 10px 15px;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            cursor: pointer;
            transition: opacity 0.15s;
        }
        .modal-buttons button:active {
            opacity: 0.8;
        }
        .modal-ok-btn {
            background: var(--tg-theme-button-color);
            color: var(--tg-theme-button-text-color);
        }
        .modal-cancel-btn {
            background: var(--tg-theme-secondary-bg-color);
            color: var(--tg-theme-text-color);
        }
        .modal-clear-btn {
            background: var(--tg-theme-destructive-bg-color);
            color: white;
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
                <button class="time-select-btn" id="time-select-btn">🕒</button>
                <button class="add-btn" id="add-btn">+</button>
            </div>

            <ul class="todo-list" id="todo-list"></ul>

            <div class="empty-state" id="empty-state" style="display: none;">
                <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                    <path d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-6 9l2 2 4-4" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
                <p>Ваш список дел пуст. Добавьте задачу!</p>
            </div>

            <button class="clear-all-btn" id="clear-all-btn" style="display: none;">Очистить завершенные</button>
        </div>
    </div>

    <!-- Модальное окно для выбора времени -->
    <div class="modal-overlay" id="time-modal-overlay">
        <div class="modal-content">
            <h3>Установите время</h3>
            <input type="time" id="modal-time-input">
            <div class="modal-buttons">
                <button class="modal-ok-btn" id="modal-ok-btn">ОК</button>
                <button class="modal-cancel-btn" id="modal-cancel-btn">Отмена</button>
                <button class="modal-clear-btn" id="modal-clear-btn">Очистить</button>
            </div>
        </div>
    </div>

    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();
        tg.ready();

        tg.setHeaderColor(tg.themeParams.header_bg_color || '#f5f5f5');
        tg.setBackgroundColor(tg.themeParams.bg_color || '#ffffff');
        tg.MainButton.textColor = tg.themeParams.button_text_color || '#ffffff';
        tg.MainButton.color = tg.themeParams.button_color || '#2481cc';

        tg.viewport.onEvent('viewportChanged', () => {
            if (!tg.viewport.isExpanded) {
                tg.viewport.expand();
            }
        });

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
        const timeSelectBtn = document.getElementById('time-select-btn');
        const addBtn = document.getElementById('add-btn');
        const todoList = document.getElementById('todo-list');
        const emptyState = document.getElementById('empty-state');
        const clearAllBtn = document.getElementById('clear-all-btn');
        const completedCountEl = document.getElementById('completed-count');
        const totalCountEl = document.getElementById('total-count');

        const timeModalOverlay = document.getElementById('time-modal-overlay');
        const modalTimeInput = document.getElementById('modal-time-input');
        const modalOkBtn = document.getElementById('modal-ok-btn');
        const modalCancelBtn = document.getElementById('modal-cancel-btn');
        const modalClearBtn = document.getElementById('modal-clear-btn');
        let selectedTime = null;

        const praises = ["Так держать!", "Молодец!", "Отличная работа!", "Супер!", "Продолжай в том же духе!", "Браво!"];

        let todos = JSON.parse(localStorage.getItem('telegram-todo-list') || '[]');

        function saveTodos() {
            localStorage.setItem('telegram-todo-list', JSON.stringify(todos));
            updateStats();
            updateClearAllButtonVisibility();
            updateMiniAppTitle();
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
                tg.MainButton.setText(`Выполнено: ${completed}/${total}`); // Убрал "задач"
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
        
        function isOverdue(dueDate) {
            if (!dueDate) return false;
            const now = new Date();
            const due = new Date(dueDate);
            // Если задача не выполнена и время просрочено
            return !this.completed && due <= now;
        }

        function createTodoElement(todo, index) {
            const li = document.createElement('li');
            li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdue.call(todo, todo.dueDate) ? 'overdue' : ''}`;
            li.dataset.index = index;
            li.innerHTML = `
                <div class="checkbox" data-index="${index}"></div>
                <span class="todo-text">${escapeHtml(todo.text)}</span>
                ${todo.dueDate ? `<span class="todo-due-date">${formatTime(todo.dueDate)}</span>` : ''}
                <button class="delete-btn" data-index="${index}">×</button>
            `;
            return li;
        }

        function renderTodos() {
            todoList.innerHTML = '';
            
            if (todos.length === 0) {
                emptyState.style.display = 'flex'; // empty-state display flex для центрирования
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
            if (!text) return;

            const newTodo = { text, completed: false, dueDate: selectedTime };
            todos.push(newTodo);
            todoInput.value = '';
            selectedTime = null;
            timeSelectBtn.classList.remove('active');
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
                todoItem.classList.remove('overdue');
            }
            
            if (todos[index].completed) {
                triggerConfetti(todoItem.querySelector('.checkbox'));
                triggerPraise(todoItem.querySelector('.checkbox'));
                tg.HapticFeedback.notificationOccurred('success');
            } else {
                tg.HapticFeedback.impactOccurred('light');
                if (isOverdue.call(todos[index], todos[index].dueDate)) {
                    todoItem.classList.add('overdue');
                }
            }

            saveTodos();
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

        function formatTime(dateTimeString) {
            if (!dateTimeString) return '';
            const date = new Date(dateTimeString);
            return date.toLocaleTimeString('ru-RU', { hour: '2-digit', minute: '2-digit' });
        }

        function triggerConfetti(targetElement) {
            const colors = ['#f44336', '#e91e63', '#9c27b0', '#673ab7', '#3f51b5', '#2196f3', '#03a9f4', '#00bcd4', '#009688', '#4CAF50', '#8BC34A', '#CDDC39', '#FFEB3B', '#FFC107', '#FF9800', '#FF5722'];
            const numberOfConfetti = 15; // Уменьшил количество конфетти

            const rect = targetElement.getBoundingClientRect();
            const startX = rect.left + rect.width / 2;
            const startY = rect.top + rect.height / 2;

            for (let i = 0; i < numberOfConfetti; i++) {
                const confetti = document.createElement('div');
                confetti.classList.add('confetti');
                confetti.style.setProperty('--color', colors[Math.floor(Math.random() * colors.length)]);
                
                const offsetX = (Math.random() - 0.5) * 10; 
                const offsetY = (Math.random() - 0.5) * 10;

                confetti.style.left = `${startX + offsetX}px`;
                confetti.style.top = `${startY + offsetY}px`;

                confetti.style.animationDelay = `${Math.random() * 0.2}s`;
                
                const angle = Math.random() * Math.PI * 2;
                const distance = Math.random() * 60 + 20; // Меньшая дальность разлета
                confetti.style.setProperty('--travelX', `${Math.cos(angle) * distance}px`);
                confetti.style.setProperty('--travelY', `${Math.sin(angle) * distance}px`);
                // Добавим чтобы конфетти не просто разлетались, но и опускались вниз
                confetti.style.setProperty('--travelY-end', `${(Math.sin(angle) * distance) + 50}px`);


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

        timeSelectBtn.addEventListener('click', () => {
            timeModalOverlay.classList.add('active');
            modalTimeInput.value = selectedTime ? new Date(selectedTime).toLocaleTimeString('ru', {hour: '2-digit', minute: '2-digit'}) : '';
            tg.HapticFeedback.impactOccurred('light');
        });

        modalOkBtn.addEventListener('click', () => {
            if (modalTimeInput.value) {
                const now = new Date();
                const [hours, minutes] = modalTimeInput.value.split(':').map(Number);
                now.setHours(hours, minutes, 0, 0);
                selectedTime = now.toISOString();
                timeSelectBtn.classList.add('active');
            } else {
                selectedTime = null;
                timeSelectBtn.classList.remove('active');
            }
            timeModalOverlay.classList.remove('active');
            tg.HapticFeedback.impactOccurred('light');
        });

        modalCancelBtn.addEventListener('click', () => {
            timeModalOverlay.classList.remove('active');
            tg.HapticFeedback.impactOccurred('light');
        });

        modalClearBtn.addEventListener('click', () => {
            selectedTime = null;
            modalTimeInput.value = '';
            timeSelectBtn.classList.remove('active');
            timeModalOverlay.classList.remove('active');
            tg.HapticFeedback.impactOccurred('light');
        });

        timeModalOverlay.addEventListener('click', (e) => {
            if (e.target === timeModalOverlay) {
                timeModalOverlay.classList.remove('active');
                tg.HapticFeedback.impactOccurred('light');
            }
        });
        
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
        
        setInterval(() => {
            todos.forEach((todo, index) => {
                const li = todoList.children[index];
                if (li && !todo.completed) {
                    if (isOverdue.call(todo, todo.dueDate)) {
                        li.classList.add('overdue');
                    } else {
                        li.classList.remove('overdue');
                    }
                }
            });
        }, 60 * 1000);
    </script>
</body>
</html>
