<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Math Tug - Play Math, Win the Tug</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .game-container {
            background: white;
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            max-width: 1200px;
            width: 100%;
        }

        /* Header Styles */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            flex-wrap: wrap;
            gap: 15px;
        }

        .game-title {
            font-size: 32px;
            font-weight: bold;
            background: linear-gradient(45deg, #667eea, #764ba2);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .level-info {
            background: #f0f0f0;
            padding: 10px 20px;
            border-radius: 10px;
            font-weight: bold;
            font-size: 18px;
        }

        .score-board {
            display: flex;
            gap: 20px;
            background: #333;
            color: white;
            padding: 10px 20px;
            border-radius: 10px;
        }

        .player-scores {
            display: flex;
            gap: 30px;
            background: #4a5568;
            color: white;
            padding: 10px 25px;
            border-radius: 10px;
            font-size: 18px;
        }

        .player1-score, .player2-score {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .player1-score span, .player2-score span {
            font-weight: bold;
            font-size: 24px;
        }

        .player1-color {
            color: #4299e1;
        }

        .player2-color {
            color: #f56565;
        }

        /* Game Modes */
        .game-modes {
            display: flex;
            gap: 15px;
            margin-bottom: 20px;
            flex-wrap: wrap;
        }

        .mode-btn {
            padding: 12px 25px;
            border: none;
            border-radius: 10px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            background: #f0f0f0;
        }

        .mode-btn.active {
            background: #667eea;
            color: white;
            transform: scale(1.05);
        }

        .mode-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        /* Player Name Input */
        .player-names {
            display: flex;
            gap: 20px;
            margin-bottom: 20px;
            padding: 20px;
            background: #f7fafc;
            border-radius: 10px;
        }

        .player-names.hidden {
            display: none;
        }

        .name-input {
            flex: 1;
        }

        .name-input label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }

        .name-input input {
            width: 100%;
            padding: 10px;
            border: 2px solid #e2e8f0;
            border-radius: 5px;
            font-size: 16px;
            transition: all 0.3s;
        }

        .name-input input:focus {
            outline: none;
            border-color: #4299e1;
        }

        .name-input.player1 input:focus {
            border-color: #4299e1;
        }

        .name-input.player2 input:focus {
            border-color: #f56565;
        }

        .save-names-btn {
            padding: 10px 20px;
            background: #48bb78;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            align-self: flex-end;
        }

        .save-names-btn:hover {
            background: #38a169;
        }

        /* Player Turn Indicator */
        .turn-indicator {
            text-align: center;
            margin-bottom: 20px;
            padding: 15px;
            border-radius: 10px;
            font-size: 20px;
            font-weight: bold;
            transition: all 0.3s;
        }

        .turn-indicator.player1-turn {
            background: #4299e1;
            color: white;
            box-shadow: 0 0 20px #4299e1;
        }

        .turn-indicator.player2-turn {
            background: #f56565;
            color: white;
            box-shadow: 0 0 20px #f56565;
        }

        /* Tug of War Arena */
        .tug-arena {
            background: #87CEEB;
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 30px;
            position: relative;
            overflow: hidden;
        }

        .field {
            background: #90EE90;
            height: 200px;
            border-radius: 10px;
            position: relative;
            border: 3px solid white;
        }

        .center-line {
            position: absolute;
            left: 50%;
            top: 0;
            width: 4px;
            height: 100%;
            background: white;
            transform: translateX(-50%);
            z-index: 2;
        }

        .rope-container {
            position: relative;
            height: 100%;
            width: 100%;
        }

        .rope {
            position: absolute;
            top: 50%;
            left: 0;
            width: 100%;
            height: 8px;
            background: #8B4513;
            transform: translateY(-50%);
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
        }

        .team {
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            font-size: 40px;
            z-index: 3;
            transition: left 0.3s ease-out;
        }

        .team-left {
            left: 20%;
        }

        .team-right {
            right: 20%;
        }

        .team-left.pulling {
            animation: pullLeft 0.3s ease-out;
        }

        .team-right.pulling {
            animation: pullRight 0.3s ease-out;
        }

        @keyframes pullLeft {
            0%, 100% { transform: translateY(-50%) rotate(0deg); }
            50% { transform: translateY(-50%) rotate(-10deg); }
        }

        @keyframes pullRight {
            0%, 100% { transform: translateY(-50%) rotate(0deg); }
            50% { transform: translateY(-50%) rotate(10deg); }
        }

        .knot {
            position: absolute;
            top: 50%;
            width: 20px;
            height: 20px;
            background: #D2691E;
            border-radius: 50%;
            transform: translate(-50%, -50%);
            transition: left 0.3s ease-out;
            z-index: 4;
            box-shadow: 0 0 10px rgba(0,0,0,0.3);
        }

        .win-line {
            position: absolute;
            top: 0;
            width: 4px;
            height: 100%;
            background: gold;
            z-index: 2;
        }

        .win-line-left {
            left: 10%;
        }

        .win-line-right {
            right: 10%;
        }

        /* Player Labels with Names */
        .player-label {
            position: absolute;
            bottom: -25px;
            font-size: 16px;
            font-weight: bold;
            padding: 8px 20px;
            border-radius: 20px;
            color: white;
            white-space: nowrap;
            max-width: 150px;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .player1-label {
            left: 15%;
            background: #4299e1;
            box-shadow: 0 2px 10px rgba(66, 153, 225, 0.5);
        }

        .player2-label {
            right: 15%;
            background: #f56565;
            box-shadow: 0 2px 10px rgba(245, 101, 101, 0.5);
        }

        .player-label i {
            margin-right: 5px;
            font-size: 12px;
        }

        /* Team Names on Rope */
        .team-name {
            position: absolute;
            top: -30px;
            font-size: 14px;
            font-weight: bold;
            padding: 4px 12px;
            border-radius: 12px;
            color: white;
            white-space: nowrap;
        }

        .team-name-left {
            left: 10%;
            background: #4299e1;
        }

        .team-name-right {
            right: 10%;
            background: #f56565;
        }

        /* Math Question Area */
        .question-area {
            background: white;
            border-radius: 15px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }

        .question {
            text-align: center;
            font-size: 48px;
            font-weight: bold;
            margin-bottom: 30px;
            color: #333;
        }

        .options {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
        }

        .option-btn {
            padding: 20px;
            font-size: 24px;
            font-weight: bold;
            border: 3px solid #e0e0e0;
            border-radius: 15px;
            background: white;
            cursor: pointer;
            transition: all 0.3s;
        }

        .option-btn:hover:not(:disabled) {
            transform: translateY(-3px);
            box-shadow: 0 10px 20px rgba(0,0,0,0.1);
            border-color: #667eea;
        }

        .option-btn.correct {
            background: #4CAF50;
            color: white;
            border-color: #45a049;
            animation: correctPulse 0.5s;
        }

        .option-btn.wrong {
            background: #f44336;
            color: white;
            border-color: #da190b;
            animation: wrongShake 0.5s;
        }

        .option-btn:disabled {
            opacity: 0.7;
            cursor: not-allowed;
        }

        @keyframes correctPulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        @keyframes wrongShake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-10px); }
            75% { transform: translateX(10px); }
        }

        /* Timer */
        .timer-container {
            width: 100%;
            height: 10px;
            background: #e0e0e0;
            border-radius: 5px;
            margin: 20px 0;
            overflow: hidden;
        }

        .timer-bar {
            height: 100%;
            background: linear-gradient(90deg, #4CAF50, #FFC107, #f44336);
            transition: width 1s linear;
        }

        /* Daily Challenge */
        .daily-challenge {
            background: linear-gradient(135deg, #FFD700, #FFA500);
            border-radius: 15px;
            padding: 20px;
            margin-top: 20px;
            text-align: center;
            color: white;
        }

        .daily-challenge.hidden {
            display: none;
        }

        /* Leaderboard */
        .leaderboard {
            background: #f8f9fa;
            border-radius: 15px;
            padding: 20px;
            margin-top: 20px;
        }

        .leaderboard.hidden {
            display: none;
        }

        .leaderboard h3 {
            margin-bottom: 15px;
            color: #333;
        }

        .leaderboard-item {
            display: flex;
            justify-content: space-between;
            padding: 10px;
            border-bottom: 1px solid #dee2e6;
        }

        /* Multiplayer Controls */
        .multiplayer-controls {
            display: flex;
            gap: 20px;
            justify-content: center;
            margin: 20px 0;
        }

        .multiplayer-controls.hidden {
            display: none;
        }

        .player-btn {
            padding: 15px 30px;
            font-size: 18px;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s;
            font-weight: bold;
        }

        .player1-btn {
            background: #4299e1;
            color: white;
        }

        .player1-btn:hover:not(:disabled) {
            background: #3182ce;
            transform: scale(1.05);
        }

        .player2-btn {
            background: #f56565;
            color: white;
        }

        .player2-btn:hover:not(:disabled) {
            background: #e53e3e;
            transform: scale(1.05);
        }

        .player-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .player-btn.active {
            box-shadow: 0 0 20px currentColor;
        }

        /* Responsive Design */
        @media (max-width: 768px) {
            .game-container {
                padding: 15px;
            }

            .question {
                font-size: 36px;
            }

            .options {
                grid-template-columns: 1fr;
            }

            .team {
                font-size: 30px;
            }

            .player-scores {
                flex-direction: column;
                gap: 10px;
            }

            .player-names {
                flex-direction: column;
            }

            .player-label {
                font-size: 12px;
                padding: 4px 10px;
            }
        }

        /* Victory/Defeat Modal */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal.hidden {
            display: none;
        }

        .modal-content {
            background: white;
            padding: 40px;
            border-radius: 20px;
            text-align: center;
            animation: modalPop 0.5s;
        }

        @keyframes modalPop {
            0% { transform: scale(0.8); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }

        .modal-content h2 {
            font-size: 36px;
            margin-bottom: 20px;
        }

        .modal-content.win h2 {
            color: #4CAF50;
        }

        .modal-content.lose h2 {
            color: #f44336;
        }

        .modal-content.player1-win h2 {
            color: #4299e1;
        }

        .modal-content.player2-win h2 {
            color: #f56565;
        }

        .modal-btn {
            padding: 15px 30px;
            font-size: 18px;
            border: none;
            border-radius: 10px;
            background: #667eea;
            color: white;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s;
        }

        .modal-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        .modal-btn.reset {
            background: #718096;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <!-- Header -->
        <div class="header">
            <div class="game-title">Math Tug</div>
            <div class="level-info">Level <span id="currentLevel">1</span>/<span id="totalLevels">15000</span></div>
            <div class="score-board" id="singlePlayerScore">
                <div>Wins: <span id="wins">0</span></div>
                <div>Streak: <span id="streak">0</span></div>
            </div>
            <div class="player-scores hidden" id="multiPlayerScore">
                <div class="player1-score">
                    <span class="player1-color">●</span> <span id="player1Name">Player 1</span>: <span id="player1Wins">0</span>
                </div>
                <div class="player2-score">
                    <span class="player2-color">●</span> <span id="player2Name">Player 2</span>: <span id="player2Wins">0</span>
                </div>
            </div>
        </div>

        <!-- Game Modes -->
        <div class="game-modes">
            <button class="mode-btn active" onclick="changeMode('single')">Single Player</button>
            <button class="mode-btn" onclick="changeMode('multi')">Multiplayer</button>
            <button class="mode-btn" onclick="toggleDailyChallenge()">Daily Challenge</button>
            <button class="mode-btn" onclick="toggleLeaderboard()">Leaderboard</button>
            <button class="mode-btn" onclick="resetGame()">New Game</button>
        </div>

        <!-- Player Name Input (Multiplayer) -->
        <div class="player-names hidden" id="playerNames">
            <div class="name-input player1">
                <label for="player1NameInput">Player 1 Name:</label>
                <input type="text" id="player1NameInput" value="Champion" maxlength="15">
            </div>
            <div class="name-input player2">
                <label for="player2NameInput">Player 2 Name:</label>
                <input type="text" id="player2NameInput" value="Challenger" maxlength="15">
            </div>
            <button class="save-names-btn" onclick="savePlayerNames()">Save Names</button>
        </div>

        <!-- Player Turn Indicator (Multiplayer) -->
        <div class="turn-indicator hidden" id="turnIndicator">
            Player 1's Turn
        </div>

        <!-- Tug of War Arena -->
        <div class="tug-arena">
            <div class="field">
                <div class="center-line"></div>
                <div class="win-line win-line-left"></div>
                <div class="win-line win-line-right"></div>
                <div class="rope-container">
                    <div class="rope"></div>
                    <div class="team team-left" id="teamLeft">💪</div>
                    <div class="team team-right" id="teamRight">💪</div>
                    <div class="knot" id="knot" style="left: 50%"></div>
                </div>
                <div class="player-label player1-label" id="player1Label">Champion</div>
                <div class="player-label player2-label" id="player2Label">Challenger</div>
            </div>
        </div>

        <!-- Math Question Area -->
        <div class="question-area">
            <div class="question" id="question">25 × 4 = ?</div>
            <div class="options" id="options">
                <button class="option-btn" onclick="checkAnswer(0)">90</button>
                <button class="option-btn" onclick="checkAnswer(1)">100</button>
                <button class="option-btn" onclick="checkAnswer(2)">110</button>
                <button class="option-btn" onclick="checkAnswer(3)">95</button>
            </div>
        </div>

        <!-- Timer -->
        <div class="timer-container">
            <div class="timer-bar" id="timerBar" style="width: 100%"></div>
        </div>

        <!-- Multiplayer Controls -->
        <div class="multiplayer-controls hidden" id="multiplayerControls">
            <button class="player-btn player1-btn active" id="player1Btn" onclick="setActivePlayer(1)"><span id="player1BtnName">Champion</span>'s Turn</button>
            <button class="player-btn player2-btn" id="player2Btn" onclick="setActivePlayer(2)"><span id="player2BtnName">Challenger</span>'s Turn</button>
        </div>

        <!-- Daily Challenge -->
        <div class="daily-challenge hidden" id="dailyChallenge">
            <h3>Daily Challenge</h3>
            <p>Complete 10 questions in 60 seconds!</p>
            <p>Progress: <span id="challengeProgress">0</span>/10</p>
            <p>Time: <span id="challengeTime">60</span>s</p>
        </div>

        <!-- Leaderboard -->
        <div class="leaderboard hidden" id="leaderboard">
            <h3>🏆 Leaderboard 🏆</h3>
            <div id="leaderboardEntries"></div>
        </div>
    </div>

    <!-- Victory/Defeat Modal -->
    <div class="modal hidden" id="gameOverModal">
        <div class="modal-content" id="modalContent">
            <h2 id="modalTitle">Victory!</h2>
            <p id="modalMessage">You won the level!</p>
            <button class="modal-btn" id="nextLevelBtn" onclick="nextLevel()">Next Level</button>
            <button class="modal-btn reset" onclick="closeModal()">Play Again</button>
        </div>
    </div>

    <script>
        // Game State
        let currentLevel = 1;
        let wins = 0;
        let streak = 0;
        let knotPosition = 50;
        let gameMode = 'single';
        let timerInterval;
        let timeLeft = 30;
        let dailyChallengeActive = false;
        let challengeProgress = 0;
        let leaderboardActive = false;
        
        // Multiplayer State
        let activePlayer = 1; // 1 for Player 1, 2 for Player 2
        let player1Wins = 0;
        let player2Wins = 0;
        let player1Name = "Champion";
        let player2Name = "Challenger";
        let player1Answered = false;
        let player2Answered = false;
        let currentQuestionAnswer = 0;
        let bothPlayersAnswered = false;
        
        // Total levels (scalable to 15000)
        const TOTAL_LEVELS = 15000;
        
        // Level generation system
        class LevelGenerator {
            constructor() {
                this.operations = ['+', '-', '×', '÷'];
            }

            generateLevel(level) {
                // Difficulty progression based on level
                let difficulty = Math.min(level / 100, 10); // Max difficulty at level 1000
                
                // Determine operation based on level
                let operation;
                if (level <= 1000) {
                    // Easy levels: addition and subtraction
                    operation = level % 2 === 0 ? '+' : '-';
                } else if (level <= 3000) {
                    // Medium levels: multiplication
                    operation = '×';
                } else if (level <= 6000) {
                    // Medium-hard: division
                    operation = '÷';
                } else {
                    // Hard levels: mix of all
                    operation = this.operations[Math.floor(Math.random() * this.operations.length)];
                }

                let num1, num2, answer, question;
                
                switch(operation) {
                    case '+':
                        num1 = Math.floor(Math.random() * (10 + difficulty * 20)) + 1;
                        num2 = Math.floor(Math.random() * (10 + difficulty * 20)) + 1;
                        answer = num1 + num2;
                        question = `${num1} + ${num2} = ?`;
                        break;
                        
                    case '-':
                        num1 = Math.floor(Math.random() * (20 + difficulty * 30)) + 10;
                        num2 = Math.floor(Math.random() * num1) + 1;
                        answer = num1 - num2;
                        question = `${num1} - ${num2} = ?`;
                        break;
                        
                    case '×':
                        num1 = Math.floor(Math.random() * (5 + difficulty * 5)) + 2;
                        num2 = Math.floor(Math.random() * (5 + difficulty * 5)) + 2;
                        answer = num1 * num2;
                        question = `${num1} × ${num2} = ?`;
                        break;
                        
                    case '÷':
                        num2 = Math.floor(Math.random() * (5 + difficulty * 3)) + 2;
                        answer = Math.floor(Math.random() * (5 + difficulty * 3)) + 2;
                        num1 = num2 * answer;
                        question = `${num1} ÷ ${num2} = ?`;
                        break;
                }

                // Generate wrong options
                let options = [answer];
                while(options.length < 4) {
                    let offset = Math.floor(Math.random() * 10) + 1;
                    let wrong = answer + (Math.random() > 0.5 ? offset : -offset);
                    if (!options.includes(wrong) && wrong > 0) {
                        options.push(wrong);
                    }
                }

                // Shuffle options
                options = this.shuffleArray(options);

                return {
                    question,
                    options,
                    answer
                };
            }

            shuffleArray(array) {
                for (let i = array.length - 1; i > 0; i--) {
                    const j = Math.floor(Math.random() * (i + 1));
                    [array[i], array[j]] = [array[j], array[i]];
                }
                return array;
            }
        }

        const levelGenerator = new LevelGenerator();

        // Initialize game
        function initGame() {
            loadLevel(currentLevel);
            document.getElementById('currentLevel').textContent = currentLevel;
            document.getElementById('totalLevels').textContent = TOTAL_LEVELS;
            startTimer();
            updateUIForMode();
            updatePlayerNames();
        }

        // Update player names throughout UI
        function updatePlayerNames() {
            // Update labels
            document.getElementById('player1Label').textContent = player1Name;
            document.getElementById('player2Label').textContent = player2Name;
            
            // Update scoreboard
            document.getElementById('player1Name').textContent = player1Name;
            document.getElementById('player2Name').textContent = player2Name;
            
            // Update buttons
            document.getElementById('player1BtnName').textContent = player1Name;
            document.getElementById('player2BtnName').textContent = player2Name;
            
            // Update turn indicator
            updateTurnIndicator();
        }

        // Save player names
        function savePlayerNames() {
            const newPlayer1Name = document.getElementById('player1NameInput').value.trim();
            const newPlayer2Name = document.getElementById('player2NameInput').value.trim();
            
            if (newPlayer1Name) player1Name = newPlayer1Name;
            if (newPlayer2Name) player2Name = newPlayer2Name;
            
            updatePlayerNames();
        }

        // Load level
        function loadLevel(level) {
            const levelData = levelGenerator.generateLevel(level);
            document.getElementById('question').textContent = levelData.question;
            
            const optionButtons = document.querySelectorAll('.option-btn');
            levelData.options.forEach((option, index) => {
                optionButtons[index].textContent = option;
            });

            // Store answer
            currentQuestionAnswer = levelData.answer;
            
            // Reset answered flags for multiplayer
            resetMultiplayerQuestion();
        }

        // Reset multiplayer question state
        function resetMultiplayerQuestion() {
            player1Answered = false;
            player2Answered = false;
            bothPlayersAnswered = false;
            
            // Enable all option buttons
            document.querySelectorAll('.option-btn').forEach(btn => {
                btn.disabled = false;
            });
            
            // Update turn indicator
            updateTurnIndicator();
        }

        // Update turn indicator
        function updateTurnIndicator() {
            const turnIndicator = document.getElementById('turnIndicator');
            const player1Btn = document.getElementById('player1Btn');
            const player2Btn = document.getElementById('player2Btn');
            
            if (gameMode === 'multi') {
                if (activePlayer === 1) {
                    turnIndicator.textContent = `${player1Name}'s Turn`;
                    turnIndicator.className = 'turn-indicator player1-turn';
                    player1Btn.classList.add('active');
                    player2Btn.classList.remove('active');
                } else {
                    turnIndicator.textContent = `${player2Name}'s Turn`;
                    turnIndicator.className = 'turn-indicator player2-turn';
                    player2Btn.classList.add('active');
                    player1Btn.classList.remove('active');
                }
            }
        }

        // Set active player
        function setActivePlayer(player) {
            if (gameMode === 'multi') {
                activePlayer = player;
                updateTurnIndicator();
                
                // Enable/disable buttons based on who has answered
                const optionButtons = document.querySelectorAll('.option-btn');
                if ((player === 1 && player1Answered) || (player === 2 && player2Answered)) {
                    optionButtons.forEach(btn => btn.disabled = true);
                } else {
                    optionButtons.forEach(btn => btn.disabled = false);
                }
            }
        }

        // Check answer
        function checkAnswer(selectedIndex) {
            const selectedOption = parseInt(document.querySelectorAll('.option-btn')[selectedIndex].textContent);
            const optionButtons = document.querySelectorAll('.option-btn');

            if (gameMode === 'single') {
                // Single player mode
                optionButtons.forEach(btn => btn.disabled = true);

                if (selectedOption === currentQuestionAnswer) {
                    // Correct answer
                    optionButtons[selectedIndex].classList.add('correct');
                    pullRope('player');
                    streak++;
                    document.getElementById('streak').textContent = streak;
                    
                    // Daily challenge progress
                    if (dailyChallengeActive) {
                        challengeProgress++;
                        document.getElementById('challengeProgress').textContent = challengeProgress;
                        if (challengeProgress >= 10) {
                            completeDailyChallenge();
                        }
                    }
                } else {
                    // Wrong answer
                    optionButtons[selectedIndex].classList.add('wrong');
                    pullRope('ai');
                    streak = 0;
                    document.getElementById('streak').textContent = streak;
                }

                // Show correct answer
                optionButtons.forEach((btn, index) => {
                    if (parseInt(btn.textContent) === currentQuestionAnswer) {
                        btn.classList.add('correct');
                    }
                });

                // Load next question after delay
                setTimeout(() => {
                    optionButtons.forEach(btn => {
                        btn.classList.remove('correct', 'wrong');
                        btn.disabled = false;
                    });
                    
                    if (knotPosition > 10 && knotPosition < 90) {
                        loadLevel(currentLevel);
                        resetTimer();
                    }
                }, 1000);
            } else {
                // Multiplayer mode
                // Check if it's the active player's turn and they haven't answered
                if ((activePlayer === 1 && !player1Answered) || (activePlayer === 2 && !player2Answered)) {
                    
                    // Mark that this player has answered
                    if (activePlayer === 1) {
                        player1Answered = true;
                    } else {
                        player2Answered = true;
                    }

                    // Show selected answer effect
                    if (selectedOption === currentQuestionAnswer) {
                        optionButtons[selectedIndex].classList.add('correct');
                        
                        // Pull rope based on who answered correctly
                        if (activePlayer === 1) {
                            pullRope('player1');
                        } else {
                            pullRope('player2');
                        }
                    } else {
                        optionButtons[selectedIndex].classList.add('wrong');
                        
                        // Pull rope against the player who answered wrong
                        if (activePlayer === 1) {
                            pullRope('player2'); // Player 2 gets advantage
                        } else {
                            pullRope('player1'); // Player 1 gets advantage
                        }
                    }

                    // Show correct answer
                    optionButtons.forEach((btn, index) => {
                        if (parseInt(btn.textContent) === currentQuestionAnswer) {
                            btn.classList.add('correct');
                        }
                    });

                    // Check if both players have answered
                    if (player1Answered && player2Answered) {
                        bothPlayersAnswered = true;
                        
                        // Load next question after delay
                        setTimeout(() => {
                            optionButtons.forEach(btn => {
                                btn.classList.remove('correct', 'wrong');
                                btn.disabled = false;
                            });
                            
                            if (knotPosition > 10 && knotPosition < 90) {
                                loadLevel(currentLevel);
                                resetTimer();
                            }
                            
                            // Reset for next question
                            resetMultiplayerQuestion();
                        }, 1500);
                    } else {
                        // Switch to other player
                        optionButtons.forEach(btn => btn.disabled = true);
                        
                        setTimeout(() => {
                            activePlayer = activePlayer === 1 ? 2 : 1;
                            updateTurnIndicator();
                            
                            // Enable buttons for new active player if they haven't answered
                            if ((activePlayer === 1 && !player1Answered) || (activePlayer === 2 && !player2Answered)) {
                                optionButtons.forEach(btn => btn.disabled = false);
                            }
                        }, 1000);
                    }
                }
            }
        }

        // Pull rope
        function pullRope(team) {
            const teamLeft = document.getElementById('teamLeft');
            const teamRight = document.getElementById('teamRight');
            const knot = document.getElementById('knot');
            
            if (team === 'player' || team === 'player1') {
                knotPosition -= 3;
                teamLeft.classList.add('pulling');
                setTimeout(() => teamLeft.classList.remove('pulling'), 300);
            } else if (team === 'ai' || team === 'player2') {
                knotPosition += 3;
                teamRight.classList.add('pulling');
                setTimeout(() => teamRight.classList.remove('pulling'), 300);
            }

            // Update knot position
            knot.style.left = knotPosition + '%';

            // Check for win
            if (knotPosition <= 10) {
                if (gameMode === 'single') {
                    gameOver('win');
                } else {
                    gameOver('player1');
                }
            } else if (knotPosition >= 90) {
                if (gameMode === 'single') {
                    gameOver('lose');
                } else {
                    gameOver('player2');
                }
            }
        }

        // Game over
        function gameOver(result) {
            const modal = document.getElementById('gameOverModal');
            const modalContent = document.getElementById('modalContent');
            const modalTitle = document.getElementById('modalTitle');
            const modalMessage = document.getElementById('modalMessage');
            const nextLevelBtn = document.getElementById('nextLevelBtn');

            // Clear existing classes
            modalContent.className = 'modal-content';

            if (gameMode === 'single') {
                if (result === 'win') {
                    modalContent.classList.add('win');
                    modalTitle.textContent = 'Victory!';
                    modalMessage.textContent = 'You won the level!';
                    wins++;
                    document.getElementById('wins').textContent = wins;
                    
                    // Level up
                    if (currentLevel < TOTAL_LEVELS) {
                        currentLevel++;
                        document.getElementById('currentLevel').textContent = currentLevel;
                    }
                    nextLevelBtn.style.display = 'inline-block';
                } else {
                    modalContent.classList.add('lose');
                    modalTitle.textContent = 'Defeat!';
                    modalMessage.textContent = 'Better luck next time!';
                    nextLevelBtn.style.display = 'none';
                }
            } else {
                // Multiplayer game over
                if (result === 'player1') {
                    modalContent.classList.add('player1-win');
                    modalTitle.textContent = `${player1Name} Wins!`;
                    modalMessage.textContent = `Congratulations ${player1Name}!`;
                    player1Wins++;
                    document.getElementById('player1Wins').textContent = player1Wins;
                } else {
                    modalContent.classList.add('player2-win');
                    modalTitle.textContent = `${player2Name} Wins!`;
                    modalMessage.textContent = `Congratulations ${player2Name}!`;
                    player2Wins++;
                    document.getElementById('player2Wins').textContent = player2Wins;
                }
                nextLevelBtn.style.display = 'none';
            }

            modal.classList.remove('hidden');
            clearInterval(timerInterval);
        }

        // Next level
        function nextLevel() {
            closeModal();
            knotPosition = 50;
            document.getElementById('knot').style.left = '50%';
            loadLevel(currentLevel);
            resetTimer();
        }

        // Close modal
        function closeModal() {
            document.getElementById('gameOverModal').classList.add('hidden');
            if (gameMode === 'single') {
                resetGame();
            } else {
                resetMultiplayerGame();
            }
        }

        // Reset multiplayer game
        function resetMultiplayerGame() {
            knotPosition = 50;
            document.getElementById('knot').style.left = '50%';
            resetMultiplayerQuestion();
            resetTimer();
        }

        // Change game mode
        function changeMode(mode) {
            gameMode = mode;
            document.querySelectorAll('.mode-btn').forEach(btn => {
                btn.classList.remove('active');
            });
            event.target.classList.add('active');
            
            updateUIForMode();
            
            if (mode === 'multi') {
                resetMultiplayerGame();
            } else {
                resetGame();
            }
        }

        // Update UI based on game mode
        function updateUIForMode() {
            const singlePlayerScore = document.getElementById('singlePlayerScore');
            const multiPlayerScore = document.getElementById('multiPlayerScore');
            const playerNames = document.getElementById('playerNames');
            const turnIndicator = document.getElementById('turnIndicator');
            const multiplayerControls = document.getElementById('multiplayerControls');
            
            if (gameMode === 'single') {
                singlePlayerScore.classList.remove('hidden');
                multiPlayerScore.classList.add('hidden');
                playerNames.classList.add('hidden');
                turnIndicator.classList.add('hidden');
                multiplayerControls.classList.add('hidden');
            } else {
                singlePlayerScore.classList.add('hidden');
                multiPlayerScore.classList.remove('hidden');
                playerNames.classList.remove('hidden');
                turnIndicator.classList.remove('hidden');
                multiplayerControls.classList.remove('hidden');
                updateTurnIndicator();
            }
        }

        // Timer functions
        function startTimer() {
            timeLeft = 30;
            updateTimerBar();
            
            timerInterval = setInterval(() => {
                timeLeft--;
                updateTimerBar();
                
                if (timeLeft <= 0) {
                    timeOut();
                }
            }, 1000);
        }

        function resetTimer() {
            clearInterval(timerInterval);
            timeLeft = 30;
            updateTimerBar();
            startTimer();
        }

        function updateTimerBar() {
            const percentage = (timeLeft / 30) * 100;
            document.getElementById('timerBar').style.width = percentage + '%';
        }

        function timeOut() {
            if (gameMode === 'single') {
                pullRope('ai');
            } else {
                // In multiplayer, time out penalizes the active player
                if (activePlayer === 1) {
                    pullRope('player2');
                } else {
                    pullRope('player1');
                }
            }
            resetTimer();
        }

        // Daily challenge
        function toggleDailyChallenge() {
            dailyChallengeActive = !dailyChallengeActive;
            const challengeDiv = document.getElementById('dailyChallenge');
            
            if (dailyChallengeActive) {
                challengeDiv.classList.remove('hidden');
                challengeProgress = 0;
                document.getElementById('challengeProgress').textContent = '0';
                startChallengeTimer();
            } else {
                challengeDiv.classList.add('hidden');
            }
        }

        function startChallengeTimer() {
            let challengeTime = 60;
            const timerDisplay = document.getElementById('challengeTime');
            
            const challengeInterval = setInterval(() => {
                challengeTime--;
                timerDisplay.textContent = challengeTime;
                
                if (challengeTime <= 0 || !dailyChallengeActive) {
                    clearInterval(challengeInterval);
                    if (dailyChallengeActive) {
                        alert('Daily challenge time expired!');
                        toggleDailyChallenge();
                    }
                }
            }, 1000);
        }

        function completeDailyChallenge() {
            alert('Congratulations! You completed the daily challenge!');
            toggleDailyChallenge();
        }

        // Leaderboard
        function toggleLeaderboard() {
            leaderboardActive = !leaderboardActive;
            const leaderboardDiv = document.getElementById('leaderboard');
            
            if (leaderboardActive) {
                leaderboardDiv.classList.remove('hidden');
                updateLeaderboard();
            } else {
                leaderboardDiv.classList.add('hidden');
            }
        }

        function updateLeaderboard() {
            const leaderboardEntries = document.getElementById('leaderboardEntries');
            let html = '';
            
            // Sample leaderboard data
            const scores = [
                { name: 'Math Wizard', level: 1500, wins: 250 },
                { name: 'Number Ninja', level: 1200, wins: 180 },
                { name: 'Calc King', level: 900, wins: 120 },
                { name: player1Name, level: currentLevel, wins: player1Wins },
                { name: player2Name, level: currentLevel, wins: player2Wins }
            ];
            
            scores.sort((a, b) => b.level - a.level).forEach((score, index) => {
                html += `
                    <div class="leaderboard-item">
                        <span>${index + 1}. ${score.name}</span>
                        <span>Level ${score.level} (${score.wins} wins)</span>
                    </div>
                `;
            });
            
            leaderboardEntries.innerHTML = html;
        }

        // Reset game
        function resetGame() {
            currentLevel = 1;
            wins = 0;
            streak = 0;
            knotPosition = 50;
            
            document.getElementById('currentLevel').textContent = currentLevel;
            document.getElementById('wins').textContent = wins;
            document.getElementById('streak').textContent = streak;
            document.getElementById('knot').style.left = '50%';
            
            loadLevel(currentLevel);
            resetTimer();
        }

        // Initialize game on load
        window.onload = initGame;
    </script>
</body>
</html>
