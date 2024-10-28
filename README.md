# SCT_Trackcode_Task3<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Tic-Tac-Toe</title>
    <!-- Bootstrap CSS -->
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            background-color: #222831;
            color: #eeeeee;
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            font-family: Arial, sans-serif;
        }

        .container {
            max-width: 500px;
            text-align: center;
        }

        .game-board {
            display: grid;
            grid-template-columns: repeat(3, 120px);
            gap: 15px;
            margin-top: 20px;
        }

        .cell {
            width: 120px;
            height: 120px;
            background-color: #393e46;
            color: #00adb5;
            font-size: 2.5em;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: background-color 0.3s;
            border-radius: 12px;
        }

        .cell:hover {
            background-color: #007bff;
        }

        .cell.active {
            pointer-events: none;
        }

        .game-info {
            text-align: center;
            font-size: 1.5em;
            font-weight: bold;
            margin-top: 10px;
        }

        .btn-group .btn {
            font-size: 1.2em;
            border-radius: 8px;
            margin: 5px;
        }

        .btn-primary {
            background-color: #00adb5;
            border-color: #00adb5;
        }

        .btn-warning {
            background-color: #ff5722;
            border-color: #ff5722;
        }

        .btn-danger {
            background-color: #d9534f;
            border-color: #d9534f;
        }

        /* New styles for player mode icons and result messages */
        .player-icon {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            margin-right: 10px;
            object-fit: cover;
            vertical-align: middle;
        }

        .result-message {
            background-color: #333;
            color: #00adb5;
            padding: 10px;
            border-radius: 8px;
            margin-top: 20px;
        }

        .result-win {
            color: #28a745;
        }

        .result-draw {
            color: #ffc107;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="mb-4">Advanced Tic-Tac-Toe</h1>

        <!-- Mode Selection -->
        <div class="btn-group mb-3">
            <button id="two-player" class="btn btn-primary">
                <img src="https://via.placeholder.com/50?text=F" class="player-icon" alt="Friend">Play with Friend
            </button>
            <button id="computer-player" class="btn btn-warning">
                <img src="https://via.placeholder.com/50?text=C" class="player-icon" alt="Computer">Play with Computer
            </button>
            <button id="reset" class="btn btn-danger">Reset</button>
        </div>

        <!-- Status Message -->
        <p id="game-status" class="game-info mb-3">Choose an opponent to start the game!</p>

        <!-- Game Board -->
        <div class="game-board">
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
            <div class="cell" data-cell></div>
        </div>

        <!-- Result Message -->
        <div id="result-message" class="result-message" style="display: none;"></div>
    </div>

    <!-- Bootstrap JS and jQuery -->
    <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.5.2/dist/js/bootstrap.bundle.min.js"></script>

    <script>
        const cells = document.querySelectorAll('[data-cell]');
        const statusText = document.getElementById('game-status');
        const resultMessage = document.getElementById('result-message');
        const resetBtn = document.getElementById('reset');
        const twoPlayerBtn = document.getElementById('two-player');
        const computerPlayerBtn = document.getElementById('computer-player');

        let board = ['', '', '', '', '', '', '', '', ''];
        let currentPlayer = 'X';
        let isGameActive = true;
        let isComputerMode = false;

        const winningCombinations = [
            [0, 1, 2],
            [3, 4, 5],
            [6, 7, 8],
            [0, 3, 6],
            [1, 4, 7],
            [2, 5, 8],
            [0, 4, 8],
            [2, 4, 6]
        ];

        twoPlayerBtn.addEventListener('click', () => startGame(false));
        computerPlayerBtn.addEventListener('click', () => startGame(true));
        cells.forEach(cell => cell.addEventListener('click', handleCellClick));
        resetBtn.addEventListener('click', resetGame);

        function startGame(computerMode) {
            isComputerMode = computerMode;
            currentPlayer = 'X';
            isGameActive = true;
            board = ['', '', '', '', '', '', '', '', ''];
            resultMessage.style.display = 'none';
            cells.forEach(cell => {
                cell.classList.remove('active');
                cell.textContent = '';
            });
            updateStatus(`Player ${currentPlayer}'s turn`);
        }

        function handleCellClick(e) {
            const cell = e.target;
            const cellIndex = Array.from(cells).indexOf(cell);

            if (board[cellIndex] !== '' || !isGameActive) return;

            updateCell(cell, cellIndex);
            checkResult();

            if (isGameActive && isComputerMode && currentPlayer === 'O') {
                setTimeout(makeComputerMove, 500);
            }
        }

        function updateCell(cell, index) {
            board[index] = currentPlayer;
            cell.textContent = currentPlayer;
            cell.classList.add('active');
        }

        function changePlayer() {
            currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
            updateStatus(`Player ${currentPlayer}'s turn`);
        }

        function updateStatus(message) {
            statusText.textContent = message;
        }

        function showResultMessage(message, type) {
            resultMessage.style.display = 'block';
            resultMessage.textContent = message;
            resultMessage.className = `result-message result-${type}`;
        }

        function checkResult() {
            let roundWon = false;
            for (let i = 0; i < winningCombinations.length; i++) {
                const [a, b, c] = winningCombinations[i];
                if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                    roundWon = true;
                    break;
                }
            }

            if (roundWon) {
                showResultMessage(`Player ${currentPlayer} wins! 🎉`, 'win');
                isGameActive = false;
                return;
            }

            if (!board.includes('')) {
                showResultMessage("It's a draw! 🤝", 'draw');
                isGameActive = false;
                return;
            }

            changePlayer();
        }

        function makeComputerMove() {
            let bestScore = -Infinity;
            let move;
            for (let i = 0; i < board.length; i++) {
                if (board[i] === '') {
                    board[i] = 'O';
                    let score = minimax(board, 0, false);
                    board[i] = '';
                    if (score > bestScore) {
                        bestScore = score;
                        move = i;
                    }
                }
            }
            cells[move].click();
        }

        function minimax(board, depth, isMaximizing) {
            const scores = { 'X': -1, 'O': 1, 'tie': 0 };
            const result = getWinner();
            if (result !== null) return scores[result];

            if (isMaximizing) {
                let bestScore = -Infinity;
                for (let i = 0; i < board.length; i++) {
                    if (board[i] === '') {
                        board[i] = 'O';
                        let score = minimax(board, depth + 1, false);
                        board[i] = '';
                        bestScore = Math.max(score, bestScore);
                    }
                }
                return bestScore;
            } else {
                let bestScore = Infinity;
                for (let i = 0; i < board.length; i++) {
                    if (board[i] === '') {
                        board[i] = 'X';
                        let score = minimax(board, depth + 1, true);
                        board[i] = '';
                        bestScore = Math.min(score, bestScore);
                    }
                }
                return bestScore;
            }
        }

        function getWinner() {
            for (let i = 0; i < winningCombinations.length; i++) {
                const [a, b, c] = winningCombinations[i];
                if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                    return board[a];
                }
            }
            return board.includes('') ? null : 'tie';
        }

        function resetGame() {
            startGame(isComputerMode);
        }
    </script>
</body>
</html>
