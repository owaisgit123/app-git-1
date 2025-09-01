# app-git-1
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Todo App</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        
        .header {
            background: #4a5568;
            color: white;
            padding: 30px;
            text-align: center;
        }
        
        .header h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
        }
        
        .input-section {
            padding: 30px;
            background: #f7fafc;
            border-bottom: 1px solid #e2e8f0;
        }
        
        .input-container {
            display: flex;
            gap: 10px;
        }
        
        #todoInput {
            flex: 1;
            padding: 15px;
            border: 2px solid #e2e8f0;
            border-radius: 8px;
            font-size: 16px;
            outline: none;
            transition: border-color 0.3s;
        }
        
        #todoInput:focus {
            border-color: #667eea;
        }
        
        #addBtn {
            padding: 15px 25px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            font-weight: 600;
            transition: background 0.3s;
        }
        
        #addBtn:hover {
            background: #5a67d8;
        }
        
        .todos-container {
            padding: 20px 30px 30px;
            min-height: 300px;
        }
        
        .todo-item {
            display: flex;
            align-items: center;
            padding: 15px;
            margin-bottom: 10px;
            background: #f8f9fa;
            border-radius: 8px;
            border-left: 4px solid #667eea;
            transition: all 0.3s;
        }
        
        .todo-item:hover {
            transform: translateX(5px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
        }
        
        .todo-text {
            flex: 1;
            font-size: 16px;
            color: #2d3748;
            margin-right: 15px;
        }
        
        .todo-text.editing {
            background: white;
            border: 2px solid #667eea;
            border-radius: 4px;
            padding: 8px;
            outline: none;
        }
        
        .todo-actions {
            display: flex;
            gap: 8px;
        }
        
        .btn {
            padding: 8px 12px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 14px;
            font-weight: 500;
            transition: all 0.3s;
        }
        
        .edit-btn {
            background: #48bb78;
            color: white;
        }
        
        .edit-btn:hover {
            background: #38a169;
        }
        
        .delete-btn {
            background: #f56565;
            color: white;
        }
        
        .delete-btn:hover {
            background: #e53e3e;
        }
        
        .save-btn {
            background: #4299e1;
            color: white;
        }
        
        .save-btn:hover {
            background: #3182ce;
        }
        
        .cancel-btn {
            background: #a0aec0;
            color: white;
        }
        
        .cancel-btn:hover {
            background: #718096;
        }
        
        .empty-state {
            text-align: center;
            color: #a0aec0;
            font-size: 18px;
            margin-top: 50px;
        }
        
        .todo-counter {
            text-align: center;
            color: #718096;
            margin-bottom: 20px;
            font-size: 14px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📝 Todo App</h1>
            <p>Stay organized and get things done!</p>
        </div>
        
        <div class="input-section">
            <div class="input-container">
                <input type="text" id="todoInput" placeholder="What needs to be done?" maxlength="100">
                <button id="addBtn">Add Todo</button>
            </div>
        </div>
        
        <div class="todos-container">
            <div class="todo-counter" id="todoCounter">0 todos</div>
            <div id="todosList"></div>
            <div class="empty-state" id="emptyState">
                No todos yet. Add one above to get started! 🚀
            </div>
        </div>
    </div>

    <script>
        class TodoApp {
            constructor() {
                this.todos = [];
                this.todoIdCounter = 1;
                this.init();
            }
            
            init() {
                this.todoInput = document.getElementById('todoInput');
                this.addBtn = document.getElementById('addBtn');
                this.todosList = document.getElementById('todosList');
                this.emptyState = document.getElementById('emptyState');
                this.todoCounter = document.getElementById('todoCounter');
                
                // Event listeners
                this.addBtn.addEventListener('click', () => this.addTodo());
                this.todoInput.addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') this.addTodo();
                });
                
                this.updateDisplay();
            }
            
            addTodo() {
                const text = this.todoInput.value.trim();
                if (!text) {
                    alert('Please enter a todo item!');
                    return;
                }
                
                const todo = {
                    id: this.todoIdCounter++,
                    text: text,
                    createdAt: new Date().toLocaleString()
                };
                
                this.todos.push(todo);
                this.todoInput.value = '';
                this.updateDisplay();
            }
            
            updateTodo(id, newText) {
                const todoIndex = this.todos.findIndex(todo => todo.id === id);
                if (todoIndex !== -1) {
                    this.todos[todoIndex].text = newText;
                    this.todos[todoIndex].updatedAt = new Date().toLocaleString();
                    // Re-append the element as requested
                    this.updateDisplay();
                }
            }
            
            deleteTodo(id) {
                this.todos = this.todos.filter(todo => todo.id !== id);
                // Remove element from DOM and update display
                this.updateDisplay();
            }
            
            updateDisplay() {
                // Clear the todos list
                this.todosList.innerHTML = '';
                
                // Update counter
                this.todoCounter.textContent = `${this.todos.length} todo${this.todos.length !== 1 ? 's' : ''}`;
                
                if (this.todos.length === 0) {
                    this.emptyState.style.display = 'block';
                    return;
                }
                
                this.emptyState.style.display = 'none';
                
                // Re-append all elements to the div
                this.todos.forEach(todo => {
                    const todoElement = this.createTodoElement(todo);
                    this.todosList.appendChild(todoElement);
                });
            }
            
            createTodoElement(todo) {
                const todoDiv = document.createElement('div');
                todoDiv.className = 'todo-item';
                todoDiv.dataset.id = todo.id;
                
                todoDiv.innerHTML = `
                    <div class="todo-text" data-original-text="${todo.text}">${todo.text}</div>
                    <div class="todo-actions">
                        <button class="btn edit-btn" onclick="todoApp.startEdit(${todo.id})">Edit</button>
                        <button class="btn delete-btn" onclick="todoApp.confirmDelete(${todo.id})">Delete</button>
                    </div>
                `;
                
                return todoDiv;
            }
            
            startEdit(id) {
                const todoElement = document.querySelector(`[data-id="${id}"]`);
                const textElement = todoElement.querySelector('.todo-text');
                const actionsElement = todoElement.querySelector('.todo-actions');
                const originalText = textElement.dataset.originalText;
                
                // Convert text to input
                textElement.innerHTML = `<input type="text" class="todo-text editing" value="${originalText}" maxlength="100">`;
                const input = textElement.querySelector('input');
                
                // Update actions
                actionsElement.innerHTML = `
                    <button class="btn save-btn" onclick="todoApp.saveEdit(${id})">Save</button>
                    <button class="btn cancel-btn" onclick="todoApp.cancelEdit(${id})">Cancel</button>
                `;
                
                input.focus();
                input.select();
                
                // Allow saving with Enter key
                input.addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') this.saveEdit(id);
                    if (e.key === 'Escape') this.cancelEdit(id);
                });
            }
            
            saveEdit(id) {
                const todoElement = document.querySelector(`[data-id="${id}"]`);
                const input = todoElement.querySelector('input.editing');
                const newText = input.value.trim();
                
                if (!newText) {
                    alert('Todo cannot be empty!');
                    return;
                }
                
                this.updateTodo(id, newText);
            }
            
            cancelEdit(id) {
                // Simply re-render to cancel edit
                this.updateDisplay();
            }
            
            confirmDelete(id) {
                const todo = this.todos.find(t => t.id === id);
                if (confirm(`Are you sure you want to delete "${todo.text}"?`)) {
                    this.deleteTodo(id);
                }
            }
        }
        
        // Initialize the app
        const todoApp = new TodoApp();
    </script>
</body>
</html>
