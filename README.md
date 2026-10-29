<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>UNO - لعبة جماعية</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Arial, sans-serif;
            user-select: none;
        }

        body {
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            min-height: 100vh;
            color: white;
            overflow-x: hidden;
        }

        .screen {
            display: none;
            min-height: 100vh;
            padding: 20px;
            animation: fadeIn 0.5s;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .screen.active {
            display: flex;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* شاشة الاسم */
        #nameScreen {
            background: radial-gradient(circle, #2d1b69 0%, #0f0f1e 100%);
        }

        .game-logo {
            font-size: 5rem;
            font-weight: bold;
            margin-bottom: 10px;
            transform: rotate(-5deg);
            text-shadow: 0 0 30px rgba(255,255,255,0.5);
        }

        .red { color: #ff4757; }
        .blue { color: #3742fa; }
        .green { color: #2ed573; }
        .yellow { color: #ffa502; }

        .input-group {
            background: rgba(255, 255, 255, 0.1);
            padding: 30px;
            border-radius: 20px;
            backdrop-filter: blur(10px);
            border: 2px solid rgba(255, 255, 255, 0.2);
            width: 90%;
            max-width: 400px;
            text-align: center;
        }

        input[type="text"] {
            width: 100%;
            padding: 15px;
            font-size: 1.2rem;
            border: none;
            border-radius: 10px;
            background: rgba(255, 255, 255, 0.9);
            color: #333;
            margin-bottom: 20px;
            text-align: center;
        }

        button {
            padding: 15px 30px;
            font-size: 1.2rem;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s;
            margin: 10px;
        }

        .btn-primary { background: linear-gradient(45deg, #ff6b9d, #c44569); color: white; }
        .btn-secondary { background: linear-gradient(45deg, #4834d4, #686de0); color: white; }
        .btn-success { background: linear-gradient(45deg, #6ab04c, #badc58); color: white; }
        .btn-warning { background: linear-gradient(45deg, #ffa502, #ff6348); color: white; }

        button:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(0,0,0,0.3); }
        button:disabled { opacity: 0.5; cursor: not-allowed; }

        /* شاشة الروم */
        .room-info {
            background: rgba(0,0,0,0.3);
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 20px;
            text-align: center;
            border: 2px solid #ffd700;
            width: 90%;
            max-width: 500px;
        }

        .room-number {
            font-size: 3rem;
            color: #ffd700;
            font-weight: bold;
            letter-spacing: 5px;
        }

        .players-list {
            background: rgba(255,255,255,0.05);
            border-radius: 15px;
            padding: 20px;
            width: 90%;
            max-width: 500px;
            max-height: 40vh;
            overflow-y: auto;
            margin-bottom: 20px;
        }

        .player-item {
            background: rgba(255,255,255,0.1);
            padding: 15px;
            margin: 10px 0;
            border-radius: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-right: 4px solid #2ed573;
            animation: slideIn 0.3s;
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateX(-20px); }
            to { opacity: 1; transform: translateX(0); }
        }

        .player-number {
            background: #ffd700;
            color: #000;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
        }

        /* منطقة اللعب */
        #gameScreen {
            justify-content: flex-start;
            padding-top: 20px;
        }

        .game-header {
            width: 100%;
            max-width: 800px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            background: rgba(0,0,0,0.3);
            padding: 15px;
            border-radius: 15px;
        }

        .current-player {
            font-size: 1.3rem;
            color: #ffd700;
        }

        .game-table {
            width: 100%;
            max-width: 800px;
            background: rgba(0,0,0,0.2);
            border-radius: 20px;
            padding: 30px;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 400px;
        }

        .center-cards {
            display: flex;
            gap: 30px;
            margin: 30px 0;
            align-items: center;
        }

        .deck, .discard-pile {
            width: 100px;
            height: 150px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
        }

        .deck {
            background: linear-gradient(135deg, #1e3799, #0c2461);
            border: 3px solid white;
        }

        .deck:hover { transform: scale(1.05); }

        .discard-pile {
            background: white;
            color: #333;
        }

        .player-hand {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            justify-content: center;
            margin-top: 30px;
            min-height: 150px;
        }

        .card {
            width: 80px;
            height: 120px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
            box-shadow: 0 4px 8px rgba(0,0,0,0.3);
            border: 3px solid white;
        }

        .card:hover {
            transform: translateY(-20px);
            z-index: 10;
        }

        .card.red { background: #ff4757; }
        .card.blue { background: #3742fa; }
        .card.green { background: #2ed573; }
        .card.yellow { background: #ffa502; }
        .card.wild { background: linear-gradient(135deg, #ff4757, #3742fa, #2ed573, #ffa502); }
        .card.draw4 { background: linear-gradient(135deg, #000, #333); }

        .card.disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

        .card.disabled:hover {
            transform: none;
        }

        .color-picker {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0,0,0,0.9);
            padding: 30px;
            border-radius: 20px;
            display: none;
            z-index: 1000;
            text-align: center;
        }

        .color-picker.active { display: block; }

        .color-options {
            display: flex;
            gap: 15px;
            margin-top: 20px;
        }

        .color-option {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            cursor: pointer;
            border: 4px solid white;
            transition: all 0.3s;
        }

        .color-option:hover { transform: scale(1.2); }

        /* شاشة النتائج */
        #resultScreen {
            background: radial-gradient(circle, #2d1b69 0%, #000 100%);
        }

        .winner-card {
            background: linear-gradient(135deg, #ffd700, #ff6b9d);
            padding: 40px;
            border-radius: 20px;
            text-align: center;
            margin-bottom: 30px;
            box-shadow: 0 0 40px rgba(255, 215, 0, 0.5);
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        .winner-name { font-size: 3rem; color: #000; font-weight: bold; margin-bottom: 10px; }
        .winner-label { font-size: 2rem; color: #fff; text-shadow: 2px 2px 4px rgba(0,0,0,0.5); }

        .losers-container {
            display: flex;
            flex-wrap: wrap;
            gap: 15px;
            justify-content: center;
            margin-bottom: 20px;
        }

        .loser-card {
            background: rgba(255,255,255,0.1);
            padding: 20px;
            border-radius: 10px;
            border: 2px solid #ff4757;
            min-width: 120px;
            text-align: center;
        }

        .big-loser-label {
            font-size: 3rem;
            color: #ff4757;
            font-weight: bold;
            text-shadow: 0 0 20px rgba(255, 71, 87, 0.5);
            margin-top: 20px;
        }

        .score-board {
            background: rgba(255,255,255,0.1);
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 20px;
            width: 90%;
            max-width: 400px;
        }

        .score-item {
            display: flex;
            justify-content: space-between;
            padding: 10px;
            border-bottom: 1px solid rgba(255,255,255,0.2);
        }

        .direction-indicator {
            font-size: 1.5rem;
            margin: 10px 0;
        }
    </style>
</head>
<body>

    <!-- شاشة الاسم -->
    <div id="nameScreen" class="screen active">
        <div class="game-logo">
            <span class="red">U</span><span class="blue">N</span><span class="green">O</span>
        </div>
        <div class="input-group">
            <h2>اكتب اسمك</h2>
            <input type="text" id="playerName" placeholder="اسمك هنا..." maxlength="20">
            <button class="btn-primary" onclick="saveName()">ابدأ اللعب</button>
        </div>
    </div>

    <!-- الشاشة الرئيسية -->
    <div id="mainScreen" class="screen">
        <div class="game-logo">
            <span class="red">U</span><span class="blue">N</span><span class="green">O</span>
        </div>
        <h2 style="margin-bottom: 30px;">أهلاً <span id="displayName"></span></h2>
        <div style="display: flex; flex-direction: column; gap: 20px; width: 90%; max-width: 400px;">
            <button class="btn-primary" style="padding: 25px; font-size: 1.5rem;" onclick="createRoom()">🏠 صناعة روم</button>
            <button class="btn-secondary" style="padding: 25px; font-size: 1.5rem;" onclick="showJoinScreen()">🚪 انضمام إلى روم</button>
        </div>
    </div>

    <!-- شاشة الانضمام -->
    <div id="joinScreen" class="screen">
        <div class="input-group">
            <h2>اكتب رقم الروم</h2>
            <input type="text" id="roomNumberInput" placeholder="مثال: 1234" maxlength="6">
            <button class="btn-success" onclick="joinRoom()">انضمام</button>
            <button class="btn-secondary" onclick="goBack()">رجوع</button>
        </div>
    </div>

    <!-- شاشة الروم -->
    <div id="roomScreen" class="screen">
        <div class="room-info">
            <div>رقم الروم:</div>
            <div class="room-number" id="displayRoomNumber">----</div>
            <div style="margin-top: 10px; color: #2ed573;">في انتظار اللاعبين...</div>
        </div>

        <div class="players-list">
            <h3 style="margin-bottom: 15px; text-align: center;">اللاعبين (<span id="playerCount">0</span>)</h3>
            <div id="playersContainer"></div>
        </div>

        <div style="margin-top: 20px;" id="startGameBtn">
            <button class="btn-success" style="padding: 20px 40px; font-size: 1.3rem;" onclick="startGame()">🎮 ابدأ اللعبة</button>
        </div>

        <button class="btn-secondary" onclick="leaveRoom()" style="margin-top: 20px;">مغادرة الروم</button>
    </div>

    <!-- شاشة اللعب -->
    <div id="gameScreen" class="screen">
        <div class="game-header">
            <div class="current-player">الدور على: <span id="currentPlayerName">-</span></div>
            <div class="direction-indicator" id="directionIcon">🔄 اتجاه عقارب الساعة</div>
            <div>الروم: <span id="gameRoomNumber" style="color: #ffd700;"></span></div>
        </div>

        <div class="game-table">
            <div style="font-size: 1.2rem; margin-bottom: 10px;">
                الكرت الملقى: <span id="topCardInfo" style="color: #ffd700;">-</span>
            </div>
            
            <div class="center-cards">
                <div class="deck" onclick="drawCard()" title="اسحب كارت">
                    🂠
                    <div style="position: absolute; bottom: 5px; font-size: 0.9rem;">اسحب</div>
                </div>
                <div class="discard-pile" id="discardPile">
                    ابدأ
                </div>
            </div>

            <div style="margin: 20px 0; font-size: 1.1rem;">
                كروتك (<span id="myCardCount">0</span>):
            </div>
            
            <div class="player-hand" id="playerHand">
                <!-- الكروت هتتولد هنا -->
            </div>
        </div>

        <button class="btn-warning" onclick="endGame()" style="margin-top: 20px;">🏁 إنهاء اللعبة</button>
    </div>

    <!-- اختيار اللون -->
    <div class="color-picker" id="colorPicker">
        <h2>اختر اللون</h2>
        <div class="color-options">
            <div class="color-option" style="background: #ff4757;" onclick="chooseColor('red')"></div>
            <div class="color-option" style="background: #3742fa;" onclick="chooseColor('blue')"></div>
            <div class="color-option" style="background: #2ed573;" onclick="chooseColor('green')"></div>
            <div class="color-option" style="background: #ffa502;" onclick="chooseColor('yellow')"></div>
        </div>
    </div>

    <!-- شاشة النتائج -->
    <div id="resultScreen" class="screen">
        <div class="winner-card">
            <div class="winner-name" id="winnerName">الفائز</div>
            <div class="winner-label">Winner 🏆</div>
        </div>

        <div class="score-board">
            <h3 style="text-align: center; margin-bottom: 15px;">النتائج</h3>
            <div id="scoresList"></div>
        </div>

        <div class="losers-container" id="losersContainer"></div>

        <div class="big-loser-label">الانتان 💀</div>

        <button class="btn-primary" onclick="location.reload()" style="margin-top: 30px;">🔄 العب من جديد</button>
    </div>

    <script>
        // المتغيرات العامة
        let currentPlayer = '';
        let currentRoom = null;
        let isHost = false;
        let roomInterval = null;
        
        // متغيرات اللعبة
        let gamePlayers = [];
        let currentPlayerIndex = 0;
        let playerHands = {};
        let deck = [];
        let discardPile = [];
        let currentColor = '';
        let currentValue = '';
        let direction = 1; // 1 = مع عقارب الساعة، -1 = عكس
        let pendingColorChoice = null;

        // ألوان UNO
        const colors = ['red', 'blue', 'green', 'yellow'];
        const values = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'skip', 'reverse', 'draw2'];

        window.onload = function() {
            const savedName = localStorage.getItem('playerName');
            if (savedName) {
                document.getElementById('playerName').value = savedName;
            }
        };

        function saveName() {
            const name = document.getElementById('playerName').value.trim();
            if (!name) {
                alert('اكتب اسمك الأول!');
                return;
            }
            currentPlayer = name;
            localStorage.setItem('playerName', name);
            document.getElementById('displayName').innerText = name;
            showScreen('mainScreen');
        }

        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
        }

        function goBack() {
            showScreen('mainScreen');
        }

        // إنشاء روم
        function createRoom() {
            const roomNum = Math.floor(1000 + Math.random() * 9000).toString();
            currentRoom = roomNum;
            isHost = true;
            
            const room = {
                number: roomNum,
                players: [currentPlayer],
                created: Date.now(),
                gameStarted: false
            };
            
            localStorage.setItem('room_' + roomNum, JSON.stringify(room));
            localStorage.setItem('currentRoom', roomNum);
            
            document.getElementById('displayRoomNumber').innerText = roomNum;
            document.getElementById('gameRoomNumber').innerText = roomNum;
            updatePlayersList(room.players);
            showScreen('roomScreen');
            
            startRoomMonitoring();
        }

        // الانضمام لروم
        function showJoinScreen() {
            showScreen('joinScreen');
        }

        function joinRoom() {
            const roomNum = document.getElementById('roomNumberInput').value.trim();
            if (!roomNum) {
                alert('اكتب رقم الروم!');
                return;
            }

            const roomData = localStorage.getItem('room_' + roomNum);
            if (!roomData) {
                alert('الروم ده مش موجود!');
                return;
            }

            const room = JSON.parse(roomData);
            if (room.gameStarted) {
                alert('اللعبة بدأت بالفعل!');
                return;
            }

            currentRoom = roomNum;
            isHost = false;

            if (!room.players.includes(currentPlayer)) {
                room.players.push(currentPlayer);
                localStorage.setItem('room_' + roomNum, JSON.stringify(room));
            }

            localStorage.setItem('currentRoom', roomNum);
            document.getElementById('displayRoomNumber').innerText = roomNum;
            document.getElementById('gameRoomNumber').innerText = roomNum;
            updatePlayersList(room.players);
            showScreen('roomScreen');
            
            startRoomMonitoring();
        }

        function startRoomMonitoring() {
            if (roomInterval) clearInterval(roomInterval);
            
            roomInterval = setInterval(() => {
                const roomData = localStorage.getItem('room_' + currentRoom);
                if (!roomData) {
                    alert('الروم اتقفل!');
                    leaveRoom();
                    return;
                }
                
                const room = JSON.parse(roomData);
                updatePlayersList(room.players);
                
                if (room.gameStarted && !isHost) {
                    clearInterval(roomInterval);
                    initGame(room.players);
                    showScreen('gameScreen');
                }
            }, 1000);
        }

        function updatePlayersList(players) {
            document.getElementById('playerCount').innerTe
