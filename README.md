# tasktracker
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>計画書タスク管理ツール</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: #f4f4f4;
            color: #333;
        }

        .container {
            max-width: 1000px;
            margin: 0 auto;
            background-color: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        h1, h2 {
            color: #0056b3;
        }

        .input-panel, .task-list-panel, .action-panel {
            margin-bottom: 20px;
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 5px;
            background-color: #f9f9f9;
        }

        .form-group {
            margin-bottom: 10px;
        }

        .form-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }

        .form-group input[type="text"],
        .form-group input[type="datetime-local"] {
            width: 100%; /* 幅を100%に */
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }

        button {
            background-color: #007bff;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            margin-right: 10px;
            width: auto; /* ボタンの幅を自動に */
            display: inline-block; /* インラインブロックで並べる */
            margin-top: 5px; /* 上部に少し余白 */
        }

        button:hover {
            background-color: #0056b3;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }

        table th, table td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }

        table th {
            background-color: #007bff;
            color: white;
        }

        table tr:nth-child(even) {
            background-color: #f2f2f2;
        }

        .action-panel input[type="text"] {
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
            margin-right: 10px;
            width: calc(100% - 200px); /* 幅を調整 */
            max-width: 200px; /* 最大幅を設定 */
            box-sizing: border-box;
        }

        @media (max-width: 768px) {
            .container {
                margin: 10px;
                padding: 10px;
            }
            .input-panel .form-group,
            .action-panel {
                flex-direction: column;
                align-items: stretch;
            }
            .action-panel button,
            .action-panel input[type="text"] {
                width: 100%;
                margin-top: 10px;
            }
            .form-group label {
                margin-bottom: 2px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>計画書タスク管理ツール</h1>

        <div class="input-panel">
            <h2>新しいタスクの追加</h2>
            <div class="form-group">
                <label for="when">いつ (例: 2025/01/06 18:00):</label>
                <input type="datetime-local" id="when" value="">
            </div>
            <div class="form-group">
                <label for="who">だれが:</label>
                <input type="text" id="who">
            </div>
            <div class="form-group">
                <label for="where">どこで:</label>
                <input type="text" id="where">
            </div>
            <div class="form-group">
                <label for="what">何を:</label>
                <input type="text" id="what">
            </div>
            <div class="form-group">
                <label for="why">何故:</label>
                <input type="text" id="why">
            </div>
            <div class="form-group">
                <label for="how">どうするか:</label>
                <input type="text" id="how">
            </div>
            <button id="addTaskButton">タスクを追加</button>
        </div>

        <div class="task-list-panel">
            <h2>現在のタスク</h2>
            <table id="taskTable">
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>いつ</th>
                        <th>だれが</th>
                        <th>どこで</th>
                        <th>何を</th>
                        <th>何故</th>
                        <th>どうするか</th>
                        <th>完了</th>
                    </tr>
                </thead>
                <tbody>
                    <!-- タスクがここに追加されます -->
                </tbody>
            </table>
        </div>

        <div class="action-panel">
            <label for="taskIdField">タスクID:</label>
            <input type="text" id="taskIdField">
            <button id="markCompleteButton">タスクを完了にする</button>
            <button id="deleteTaskButton">タスクを削除</button>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // TaskManagerクラス
            class TaskManager {
                constructor() {
                    this.tasks = [];
                    this.nextId = 1;
                }

                addTask(task) {
                    this.tasks.push(task);
                }

                getAllTasks() {
                    return [...this.tasks];
                }

                getIncompleteTasks() {
                    return this.tasks.filter(task => !task.completed);
                }

                markTaskAsCompleted(id) {
                    const task = this.tasks.find(t => t.id === id);
                    if (task) {
                        task.completed = true;
                        return true;
                    }
                    return false;
                }

                deleteTask(id) {
                    const initialLength = this.tasks.length;
                    this.tasks = this.tasks.filter(t => t.id !== id);
                    return this.tasks.length < initialLength;
                }

                generateNextId() {
                    return String(this.nextId++);
                }
            }

            // Taskクラス
            class Task {
                constructor(id, when, who, where, what, why, how) {
                    this.id = id;
                    this.when = when; // DateオブジェクトまたはISO文字列
                    this.who = who;
                    this.where = where;
                    this.what = what;
                    this.why = why;
                    this.how = how;
                    this.completed = false;
                }

                // JavaのDateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm")に相当
                formatWhen() {
                    const date = new Date(this.when);
                    const year = date.getFullYear();
                    const month = String(date.getMonth() + 1).padStart(2, '0');
                    const day = String(date.getDate()).padStart(2, '0');
                    const hours = String(date.getHours()).padStart(2, '0');
                    const minutes = String(date.getMinutes()).padStart(2, '0');
                    return `${year}/${month}/${day} ${hours}:${minutes}`;
                }

                toArray() {
                    return [
                        this.id,
                        this.formatWhen(),
                        this.who,
                        this.where,
                        this.what,
                        this.why,
                        this.how,
                        this.completed ? 'はい' : 'いいえ'
                    ];
                }
            }

            const taskManager = new TaskManager();

            const whenField = document.getElementById('when');
            const whoField = document.getElementById('who');
            const whereField = document.getElementById('where');
            const whatField = document.getElementById('what');
            const whyField = document.getElementById('why');
            const howField = document.getElementById('how');
            const addTaskButton = document.getElementById('addTaskButton');
            const taskTableBody = document.querySelector('#taskTable tbody');
            const taskIdField = document.getElementById('taskIdField');
            const markCompleteButton = document.getElementById('markCompleteButton');
            const deleteTaskButton = document.getElementById('deleteTaskButton');

            function updateTaskTable() {
                taskTableBody.innerHTML = ''; // テーブルをクリア
                const incompleteTasks = taskManager.getIncompleteTasks();
                incompleteTasks.forEach(task => {
                    const row = taskTableBody.insertRow();
                    task.toArray().forEach(text => {
                        const cell = row.insertCell();
                        cell.textContent = text;
                    });
                });
            }

            addTaskButton.addEventListener('click', () => {
                const id = taskManager.generateNextId();
                let when;
                try {
                    // datetime-local の値は "YYYY-MM-DDTHH:MM" 形式なので、そのままDateオブジェクトに渡せる
                    if (!whenField.value) {
                        throw new Error("日時が入力されていません。");
                    }
                    when = new Date(whenField.value);
                    if (isNaN(when.getTime())) { // 無効な日付をチェック
                        throw new Error("日時フォーマットが不正です。");
                    }
                } catch (ex) {
                    alert(`入力エラー: ${ex.message} 指定された形式で入力してください。`);
                    return;
                }

                const who = whoField.value;
                const where = whereField.value;
                const what = whatField.value;
                const why = whyField.value;
                const how = howField.value;

                if (!who || !where || !what || !why || !how) {
                    alert("入力エラー: すべての項目を入力してください。");
                    return;
                }

                const newTask = new Task(id, when, who, where, what, why, how);
                taskManager.addTask(newTask);
                alert("タスクが追加されました。");

                // フォームをクリア
                whenField.value = '';
                whoField.value = '';
                whereField.value = '';
                whatField.value = '';
                whyField.value = '';
                howField.value = '';

                updateTaskTable();
            });

            markCompleteButton.addEventListener('click', () => {
                const id = taskIdField.value;
                if (!id) {
                    alert("入力エラー: 完了にするタスクのIDを入力してください。");
                    return;
                }
                if (taskManager.markTaskAsCompleted(id)) {
                    alert(`タスクID: ${id} を完了にしました。`);
                    taskIdField.value = '';
                    updateTaskTable();
                } else {
                    alert("エラー: 指定されたIDのタスクは見つかりませんでした。");
                }
            });

            deleteTaskButton.addEventListener('click', () => {
                const id = taskIdField.value;
                if (!id) {
                    alert("入力エラー: 削除するタスクのIDを入力してください。");
                    return;
                }
                if (taskManager.deleteTask(id)) {
                    alert(`タスクID: ${id} を削除しました。`);
                    taskIdField.value = '';
                    updateTaskTable();
                } else {
                    alert("エラー: 指定されたIDのタスクは見つかりませんでした。");
                }
            });

            // 初期表示
            updateTaskTable();
        });
    </script>
</body>
</html>
