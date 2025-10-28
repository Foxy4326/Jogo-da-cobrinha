<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake 3D - Jogo da Cobrinha</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: linear-gradient(135deg, #1a1a2e, #16213e, #0f3460);
            font-family: 'Arial', sans-serif;
            color: white;
            overflow: hidden;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            touch-action: none;
        }

        #gameContainer {
            position: relative;
            width: 100%;
            max-width: 800px;
            height: 600px;
            border: 3px solid #00ff00;
            border-radius: 15px;
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.4);
            overflow: hidden;
            display: none;
        }

        #gameCanvas {
            width: 100%;
            height: 100%;
            display: block;
            background: #000000;
        }

        #loginScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            background: rgba(0, 0, 0, 0.9);
            padding: 40px;
            border-radius: 20px;
            border: 3px solid #00ff00;
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.5);
            width: 90%;
            max-width: 400px;
        }

        #loginScreen h1 {
            color: #00ff00;
            font-size: 36px;
            margin-bottom: 20px;
            text-shadow: 0 0 20px #00ff00;
        }

        #loginScreen p {
            font-size: 16px;
            margin: 15px 0;
            color: #cccccc;
        }

        #userInfo {
            margin: 20px 0;
            padding: 15px;
            background: rgba(0, 255, 0, 0.1);
            border-radius: 10px;
            border: 1px solid #00ff00;
            display: none;
        }

        #userAvatar {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            margin: 10px auto;
            border: 2px solid #00ff00;
        }

        #userName {
            font-size: 18px;
            color: #00ff00;
            margin: 10px 0;
        }

        #userEmail {
            font-size: 14px;
            color: #cccccc;
        }

        .login-btn {
            background: #4285f4;
            color: white;
            border: none;
            padding: 12px 24px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 8px;
            cursor: pointer;
            margin: 10px 0;
            width: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            transition: all 0.3s ease;
        }

        .login-btn:hover {
            background: #3367d6;
            transform: scale(1.05);
        }

        .login-btn img {
            width: 20px;
            height: 20px;
        }

        #logoutBtn {
            background: #ea4335;
            margin-top: 10px;
        }

        #logoutBtn:hover {
            background: #d33426;
        }

        #uiOverlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 10;
        }

        #score {
            position: absolute;
            top: 20px;
            left: 20px;
            font-size: 24px;
            color: #00ff00;
            text-shadow: 0 0 10px #00ff00;
            background: rgba(0, 0, 0, 0.7);
            padding: 10px 20px;
            border-radius: 10px;
            border: 2px solid #00ff00;
        }

        #userDisplay {
            position: absolute;
            top: 20px;
            right: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
            background: rgba(0, 0, 0, 0.7);
            padding: 8px 15px;
            border-radius: 10px;
            border: 2px solid #00ff00;
        }

        #userDisplay img {
            width: 30px;
            height: 30px;
            border-radius: 50%;
        }

        #userDisplay span {
            color: #00ff00;
            font-size: 14px;
        }

        #gameTitle {
            position: absolute;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 28px;
            color: #00ff00;
            text-shadow: 0 0 15px #00ff00;
            background: rgba(0, 0, 0, 0.8);
            padding: 10px 25px;
            border-radius: 15px;
            border: 2px solid #00ff00;
        }

        #startScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            background: rgba(0, 0, 0, 0.9);
            padding: 40px;
            border-radius: 20px;
            border: 3px solid #00ff00;
            box-shadow: 0 0 30px rgba(0, 255, 0, 0.5);
        }

        #startScreen h2 {
            color: #00ff00;
            font-size: 32px;
            margin-bottom: 20px;
            text-shadow: 0 0 20px #00ff00;
        }

        #startScreen p {
            font-size: 16px;
            margin: 10px 0;
            color: #cccccc;
        }

        #startButton {
            background: linear-gradient(45deg, #00ff00, #00cc00);
            color: black;
            border: none;
            padding: 15px 40px;
            font-size: 18px;
            font-weight: bold;
            border-radius: 10px;
            cursor: pointer;
            margin-top: 20px;
            pointer-events: auto;
            transition: all 0.3s ease;
        }

        #startButton:hover {
            transform: scale(1.1);
            box-shadow: 0 0 20px #00ff00;
        }

        #gameOverScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            text-align: center;
            background: rgba(0, 0, 0, 0.95);
            padding: 40px;
            border-radius: 20px;
            border: 3px solid #ff0000;
            box-shadow: 0 0 30px rgba(255, 0, 0, 0.5);
            display: none;
        }

        #gameOverScreen h2 {
            color: #ff0000;
            font-size: 36px;
            margin-bottom: 20px;
            text-shadow: 0 0 15px #ff0000;
        }

        #finalScore {
            color: #00ff00;
            font-size: 28px;
            margin: 20px 0;
        }

        #restartButton {
            background: linear-gradient(45deg, #ff0000, #cc0000);
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 18px;
            font-weight: bold;
            border-radius: 10px;
            cursor: pointer;
            margin-top: 20px;
            pointer-events: auto;
            transition: all 0.3s ease;
        }

        #restartButton:hover {
            transform: scale(1.1);
            box-shadow: 0 0 20px #ff0000;
        }

        #controls {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            text-align: center;
            background: rgba(0, 0, 0, 0.7);
            padding: 15px 30px;
            border-radius: 10px;
            border: 2px solid #00ff00;
            color: #00ff00;
            font-size: 14px;
        }

        .mobile-controls {
            position: absolute;
            bottom: 100px;
            width: 100%;
            display: none;
            justify-content: space-between;
            padding: 0 50px;
            pointer-events: auto;
        }

        .control-btn {
            width: 70px;
            height: 70px;
            background: rgba(0, 255, 0, 0.3);
            border: 2px solid #00ff00;
            border-radius: 50%;
            color: white;
            font-size: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            user-select: none;
            touch-action: manipulation;
        }

        @media (max-width: 768px) {
            #gameContainer {
                height: 100vh;
                border: none;
                border-radius: 0;
            }
            
            .mobile-controls {
                display: flex;
            }
            
            #controls {
                bottom: 180px;
                font-size: 12px;
            }
            
            #gameTitle {
                font-size: 20px;
                padding: 8px 16px;
            }
            
            #score, #userDisplay {
                font-size: 14px;
                padding: 6px 12px;
            }
        }
    </style>
</head>
<body>
    <!-- Tela de Login -->
    <div id="loginScreen">
        <h1>🐍 SNAKE 3D 🐍</h1>
        <p>Faça login para jogar e salvar sua pontuação!</p>
        
        <div id="userInfo">
            <img id="userAvatar" src="" alt="Avatar">
            <div id="userName"></div>
            <div id="userEmail"></div>
        </div>
        
        <button id="googleLogin" class="login-btn">
            <img src="data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTgiIGhlaWdodD0iMTgiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PGcgZmlsbD0ibm9uZSIgZmlsbC1ydWxlPSJldmVub2RkIj48cGF0aCBkPSJNMTcuNiA5LjJsLS4xLTEuOEg5djMuNGg0LjhDMTMuNiAxMiAxMyAxMyAxMiAxMy42djIuMmgzYTguOCA4LjggMCAwIDAgMi42LTYuNnoiIGZpbGw9IiM0Mjg1RjQiIGZpbGwtcnVsZT0ibm9uemVybyIvPjxwYXRoIGQ9Ik05IDE4YzIuNCAwIDQuNS0uOCA2LTIuMmwtMy0yLjJhNS40IDUuNCAwIDAgMS04LTIuOUgxVjEzYTkgOSAwIDAgMCA4IDV6IiBmaWxsPSIjMzRBODUzIiBmaWxsLXJ1bGU9Im5vbnplcm8iLz48cGF0aCBkPSJNNCAxMC43YTUuNCA1LjQgMCAwIDEgMC0zLjRWNUgxYTkgOSAwIDAgMCAwIDhsMy0yLjN6IiBmaWxsPSIjRkJCQzA1IiBmaWxsLXJ1bGU9Im5vbnplcm8iLz48cGF0aCBkPSJNOSAzLjZjMS4zIDAgMi41LjQgMy40IDEuM0wxNSAyLjNBOSA5IDAgMCAwIDEgNWwzIDIuNGE1LjQgNS40IDAgMCAxIDUtMy43eiIgZmlsbD0iI0VBNDMzNSIgZmlsbC1ydWxlPSJub256ZXJvIi8+PHBhdGggZD0iTTAgMGgxOHYxOEgweiIvPjwvZz48L3N2Zz4=" alt="Google">
            Entrar com Google
        </button>
        
        <button id="logoutBtn" class="login-btn" style="display: none;">
            Sair
        </button>
        
        <p style="margin-top: 20px; font-size: 12px; color: #888;">
            Sua pontuação será salva automaticamente
        </p>
    </div>

    <!-- Container do Jogo -->
    <div id="gameContainer">
        <canvas id="gameCanvas"></canvas>
        
        <div id="uiOverlay">
            <div id="score">Score: 0</div>
            <div id="userDisplay" style="display: none;">
                <img id="displayAvatar" src="" alt="Avatar">
                <span id="displayName"></span>
            </div>
            <div id="gameTitle">🐍 SNAKE 3D 🐍</div>
            
            <div id="startScreen">
                <h2>Bem-vindo, <span id="welcomeName"></span>!</h2>
                <p>🎮 Controle a cobra e colete a comida!</p>
                <p>⬅️➡️ Use A/D ou setas para girar</p>
                <p>🚫 Evite bater no próprio corpo!</p>
                <button id="startButton">JOGAR</button>
            </div>
            
            <div id="gameOverScreen">
                <h2>GAME OVER</h2>
                <div id="finalScore">Score: 0</div>
                <p id="highScoreMessage"></p>
                <button id="restartButton">JOGAR NOVAMENTE</button>
            </div>
            
            <div id="controls">
                Use <strong>A/D</strong> ou <strong>←/→</strong> para girar a cobra
            </div>
            
            <div class="mobile-controls">
                <div class="control-btn" id="leftBtn">←</div>
                <div class="control-btn" id="rightBtn">→</div>
            </div>
        </div>
    </div>

    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-firestore-compat.js"></script>

    <script>
        // Configuração do Firebase (substitua com suas credenciais)
        const firebaseConfig = {
            apiKey: "AIzaSyC4mJ6g7V6X6X6X6X6X6X6X6X6X6X6X6X6X6X6",
            authDomain: "snake-3d-game.firebaseapp.com",
            projectId: "snake-3d-game",
            storageBucket: "snake-3d-game.appspot.com",
            messagingSenderId: "123456789012",
            appId: "1:123456789012:web:abcdef123456"
        };

        // Inicializa Firebase
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        // Configurações do jogo
        const config = {
            snakeSpeed: 0.1,
            rotationSpeed: 2.5,
            segmentDistance: 1.0,
            gameAreaSize: 25,
            initialSegments: 3
        };

        // Variáveis do jogo
        let canvas, gl;
        let snake = [];
        let food = { x: 0, y: 0.5, z: 0 };
        let direction = { x: 0, y: 0, z: 1 };
        let score = 0;
        let highScore = 0;
        let gameStarted = false;
        let gameOver = false;
        let rotation = 0;
        let lastTime = 0;
        let moveCounter = 0;
        let program;
        let currentUser = null;

        // Sistema de Autenticação
        function initAuth() {
            // Observador de estado de autenticação
            auth.onAuthStateChanged((user) => {
                if (user) {
                    // Usuário logado
                    currentUser = user;
                    showUserInfo(user);
                    showGameInterface();
                    loadUserHighScore(user.uid);
                } else {
                    // Usuário não logado
                    currentUser = null;
                    showLoginScreen();
                }
            });

            // Event listeners para botões de login/logout
            document.getElementById('googleLogin').addEventListener('click', signInWithGoogle);
            document.getElementById('logoutBtn').addEventListener('click', signOut);
        }

        async function signInWithGoogle() {
            const provider = new firebase.auth.GoogleAuthProvider();
            provider.addScope('profile');
            provider.addScope('email');
            
            try {
                await auth.signInWithPopup(provider);
            } catch (error) {
                console.error('Erro no login:', error);
                alert('Erro ao fazer login: ' + error.message);
            }
        }

        function signOut() {
            auth.signOut();
        }

        function showUserInfo(user) {
            const userInfo = document.getElementById('userInfo');
            const userName = document.getElementById('userName');
            const userEmail = document.getElementById('userEmail');
            const userAvatar = document.getElementById('userAvatar');
            const logoutBtn = document.getElementById('logoutBtn');

            userName.textContent = user.displayName || 'Usuário';
            userEmail.textContent = user.email;
            userAvatar.src = user.photoURL || 'https://via.placeholder.com/60';
            
            userInfo.style.display = 'block';
            logoutBtn.style.display = 'block';
            document.getElementById('googleLogin').style.display = 'none';
        }

        function showLoginScreen() {
            document.getElementById('loginScreen').style.display = 'block';
            document.getElementById('gameContainer').style.display = 'none';
            document.getElementById('userInfo').style.display = 'none';
            document.getElementById('logoutBtn').style.display = 'none';
            document.getElementById('googleLogin').style.display = 'block';
        }

        function showGameInterface() {
            document.getElementById('loginScreen').style.display = 'none';
            document.getElementById('gameContainer').style.display = 'block';
            
            // Atualiza informações do usuário no jogo
            if (currentUser) {
                document.getElementById('welcomeName').textContent = currentUser.displayName || 'Jogador';
                document.getElementById('displayName').textContent = currentUser.displayName || 'Jogador';
                document.getElementById('displayAvatar').src = currentUser.photoURL || 'https://via.placeholder.com/30';
                document.getElementById('userDisplay').style.display = 'flex';
            }
        }

        async function loadUserHighScore(userId) {
            try {
                const doc = await db.collection('highscores').doc(userId).get();
                if (doc.exists) {
                    highScore = doc.data().score || 0;
                }
            } catch (error) {
                console.error('Erro ao carregar high score:', error);
            }
        }

        async function saveUserHighScore(userId, score) {
            if (score <= highScore) return;
            
            try {
                highScore = score;
                await db.collection('highscores').doc(userId).set({
                    score: score,
                    name: currentUser.displayName || 'Anônimo',
                    email: currentUser.email,
                    timestamp: firebase.firestore.FieldValue.serverTimestamp()
                });
                console.log('High score salvo:', score);
            } catch (error) {
                console.error('Erro ao salvar high score:', error);
            }
        }

        // Inicialização do Jogo
        function initGame() {
            canvas = document.getElementById('gameCanvas');
            
            // Tenta obter contexto WebGL
            gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl');
            
            if (!gl) {
                alert('WebGL não é suportado neste navegador! Tente usar Chrome, Firefox ou Edge.');
                return;
            }

            // Configura tamanho do canvas
            resizeCanvas();
            
            // Compila shaders e cria programa
            setupShaders();
            
            // Configura WebGL
            gl.enable(gl.DEPTH_TEST);
            gl.clearColor(0.0, 0.0, 0.0, 1.0);
            
            // Event listeners do jogo
            setupGameEventListeners();

            // Inicia game loop
            requestAnimationFrame(gameLoop);
        }

        function resizeCanvas() {
            const container = document.getElementById('gameContainer');
            canvas.width = container.clientWidth;
            canvas.height = container.clientHeight;
            gl.viewport(0, 0, canvas.width, canvas.height);
            
            if (program) {
                setupProjection();
            }
        }

        function setupShaders() {
            // Vertex Shader
            const vsSource = `
                attribute vec4 aVertexPosition;
                attribute vec4 aVertexColor;
                uniform mat4 uModelViewMatrix;
                uniform mat4 uProjectionMatrix;
                varying lowp vec4 vColor;
                
                void main(void) {
                    gl_Position = uProjectionMatrix * uModelViewMatrix * aVertexPosition;
                    vColor = aVertexColor;
                }
            `;
            
            // Fragment Shader
            const fsSource = `
                varying lowp vec4 vColor;
                
                void main(void) {
                    gl_FragColor = vColor;
                }
            `;
            
            // Compilar shaders
            const vertexShader = compileShader(gl.VERTEX_SHADER, vsSource);
            const fragmentShader = compileShader(gl.FRAGMENT_SHADER, fsSource);
            
            // Criar programa
            program = gl.createProgram();
            gl.attachShader(program, vertexShader);
            gl.attachShader(program, fragmentShader);
            gl.linkProgram(program);
            
            if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
                alert('Erro ao inicializar programa shader: ' + gl.getProgramInfoLog(program));
                return null;
            }
            
            gl.useProgram(program);
            setupProjection();
        }

        function compileShader(type, source) {
            const shader = gl.createShader(type);
            gl.shaderSource(shader, source);
            gl.compileShader(shader);
            
            if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
                alert('Erro ao compilar shader: ' + gl.getShaderInfoLog(shader));
                gl.deleteShader(shader);
                return null;
            }
            return shader;
        }

        function setupProjection() {
            const fieldOfView = 45 * Math.PI / 180;
            const aspect = canvas.width / canvas.height;
            const zNear = 0.1;
            const zFar = 100.0;
            
            const projectionMatrix = new Float32Array(16);
            const f = Math.tan(Math.PI * 0.5 - 0.5 * fieldOfView);
            const rangeInv = 1.0 / (zNear - zFar);
            
            projectionMatrix[0] = f / aspect;
            projectionMatrix[5] = f;
            projectionMatrix[10] = (zNear + zFar) * rangeInv;
            projectionMatrix[11] = -1;
            projectionMatrix[14] = zNear * zFar * rangeInv * 2;
            
            const uProjectionMatrix = gl.getUniformLocation(program, 'uProjectionMatrix');
            gl.uniformMatrix4fv(uProjectionMatrix, false, projectionMatrix);
        }

        function setupGameEventListeners() {
            // Teclado
            document.addEventListener('keydown', (e) => {
                if (!gameStarted || gameOver) return;
                
                switch(e.key.toLowerCase()) {
                    case 'a':
                    case 'arrowleft':
                        rotation = config.rotationSpeed;
                        break;
                    case 'd':
                    case 'arrowright':
                        rotation = -config.rotationSpeed;
                        break;
                }
            });

            document.addEventListener('keyup', (e) => {
                const key = e.key.toLowerCase();
                if (key === 'a' || key === 'd' || key === 'arrowleft' || key === 'arrowright') {
                    rotation = 0;
                }
            });

            // Botões mobile
            const leftBtn = document.getElementById('leftBtn');
            const rightBtn = document.getElementById('rightBtn');
            
            leftBtn.addEventListener('mousedown', () => rotation = config.rotationSpeed);
            leftBtn.addEventListener('mouseup', () => rotation = 0);
            leftBtn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                rotation = config.rotationSpeed;
            });
            leftBtn.addEventListener('touchend', () => rotation = 0);
            
            rightBtn.addEventListener('mousedown', () => rotation = -config.rotationSpeed);
            rightBtn.addEventListener('mouseup', () => rotation = 0);
            rightBtn.addEventListener('touchstart', (e) => {
                e.preventDefault();
                rotation = -config.rotationSpeed;
            });
            rightBtn.addEventListener('touchend', () => rotation = 0);

            // Botões UI do jogo
            document.getElementById('startButton').addEventListener('click', startGame);
            document.getElementById('restartButton').addEventListener('click', restartGame);
            
            // Redimensionamento
            window.addEventListener('resize', resizeCanvas);
        }

        function startGame() {
            document.getElementById('startScreen').style.display = 'none';
            gameStarted = true;
            gameOver = false;
            score = 0;
            updateScore();
            
            // Inicializa cobra
            snake = [];
            for (let i = 0; i < config.initialSegments; i++) {
                snake.push({ 
                    x: 0, 
                    y: 0.5, 
                    z: -i * config.segmentDistance 
                });
            }
            
            // Posição inicial da comida
            spawnFood();
        }

        function restartGame() {
            document.getElementById('gameOverScreen').style.display = 'none';
            startGame();
        }

        function spawnFood() {
            const halfSize = config.gameAreaSize / 2 - 3;
            let validPosition = false;
            let attempts = 0;
            
            while (!validPosition && attempts < 20) {
                food.x = Math.random() * halfSize * 2 - halfSize;
                food.z = Math.random() * halfSize * 2 - halfSize;
                food.y = 0.5;
                
                // Verifica se não está em cima da cobra
                validPosition = true;
                for (const segment of snake) {
                    if (distance(food, segment) < 2.0) {
                        validPosition = false;
                        break;
                    }
                }
                attempts++;
            }
        }

        function gameLoop(currentTime) {
            const deltaTime = Math.min((currentTime - lastTime) / 1000, 0.1);
            lastTime = currentTime;

            if (gameStarted && !gameOver) {
                updateGame(deltaTime);
            }

            render();
            requestAnimationFrame(gameLoop);
        }

        function updateGame(deltaTime) {
            // Atualiza rotação
            if (rotation !== 0) {
                const angle = rotation * deltaTime;
                const newX = direction.x * Math.cos(angle) - direction.z * Math.sin(angle);
                const newZ = direction.x * Math.sin(angle) + direction.z * Math.cos(angle);
                direction.x = newX;
                direction.z = newZ;
            }

            // Move a cobra
            moveCounter += deltaTime;
            if (moveCounter >= config.snakeSpeed) {
                moveCounter = 0;
                
                const head = snake[0];
                const newHead = {
                    x: head.x + direction.x,
                    y: head.y,
                    z: head.z + direction.z
                };

                // Verifica colisão com as paredes
                const boundary = config.gameAreaSize / 2;
                if (Math.abs(newHead.x) > boundary || Math.abs(newHead.z) > boundary) {
                    endGame();
                    return;
                }

                // Verifica colisão com o próprio corpo
                for (let i = 4; i < snake.length; i++) {
                    if (distance(newHead, snake[i]) < 0.8) {
                        endGame();
                        return;
                    }
                }

                // Verifica se comeu a comida
                if (distance(newHead, food) < 1.2) {
                    score += 10;
                    updateScore();
                    spawnFood();
                    // Adiciona segmento sem remover a cauda
                } else {
                    snake.pop();
                }

                snake.unshift(newHead);
            }
        }

        function distance(a, b) {
            const dx = a.x - b.x;
            const dz = a.z - b.z;
            return Math.sqrt(dx * dx + dz * dz);
        }

        function endGame() {
            gameOver = true;
            
            // Salva high score se for maior
            if (currentUser && score > highScore) {
                saveUserHighScore(currentUser.uid, score);
            }
            
            document.getElementById('finalScore').textContent = `Score: ${score}`;
            
            // Mensagem de high score
            const highScoreMessage = document.getElementById('highScoreMessage');
            if (score > highScore) {
                highScoreMessage.textContent = `🎉 Novo Recorde! High Score: ${score}`;
                highScoreMessage.style.color = '#00ff00';
            } else {
                highScoreMessage.textContent = `High Score: ${highScore}`;
                highScoreMessage.style.color = '#cccccc';
            }
            
            document.getElementById('gameOverScreen').style.display = 'block';
        }

        function updateScore() {
            document.getElementById('score').textContent = `Score: ${score}`;
        }

        function render() {
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
            
            // Configura view matrix (câmera)
            const modelViewMatrix = new Float32Array(16);
            this.lookAt(modelViewMatrix, [0, 8, 12], [0, 0, 0], [0, 1, 0]);
            
            const uModelViewMatrix = gl.getUniformLocation(program, 'uModelViewMatrix');
            gl.uniformMatrix4fv(uModelViewMatrix, false, modelViewMatrix);

            // Renderiza a cobra
            snake.forEach((segment, index) => {
                const isHead = index === 0;
                const color = isHead ? [0.0, 1.0, 0.0, 1.0] : [0.2, 0.8, 0.2, 1.0];
                drawCube(segment.x, segment.y, segment.z, 0.4, color);
            });

            // Renderiza a comida
            drawCube(food.x, food.y, food.z, 0.3, [1.0, 0.0, 0.0, 1.0]);

            // Renderiza o chão
            drawGround();
        }

        function drawCube(x, y, z, size, color) {
            const vertices = [
                // Front face
                -size + x, -size + y,  size + z,  size + x, -size + y,  size + z,  size + x,  size + y,  size + z, -size + x,  size + y,  size + z,
                // Back face
                -size + x, -size + y, -size + z, -size + x,  size + y, -size + z,  size + x,  size + y, -size + z,  size + x, -size + y, -size + z,
                // Top face
                -size + x,  size + y, -size + z, -size + x,  size + y,  size + z,  size + x,  size + y,  size + z,  size + x,  size + y, -size + z,
                // Bottom face
                -size + x, -size + y, -size + z,  size + x, -size + y, -size + z,  size + x, -size + y,  size + z, -size + x, -size + y,  size + z,
                // Right face
                 size + x, -size + y, -size + z,  size + x,  size + y, -size + z,  size + x,  size + y,  size + z,  size + x, -size + y,  size + z,
                // Left face
                -size + x, -size + y, -size + z, -size + x, -size + y,  size + z, -size + x,  size + y,  size + z, -size + x,  size + y, -size + z,
            ];

            const colors = [];
            for (let i = 0; i < 6; i++) {
                for (let j = 0; j < 4; j++) {
                    colors.push(...color);
                }
            }

            const indices = [
                0, 1, 2, 0, 2, 3,       // front
                4, 5, 6, 4, 6, 7,       // back
                8, 9, 10, 8, 10, 11,    // top
                12, 13, 14, 12, 14, 15, // bottom
                16, 17, 18, 16, 18, 19, // right
                20, 21, 22, 20, 22, 23  // left
            ];

            drawGeometry(vertices, colors, indices);
        }

        function drawGround() {
            const size = config.gameAreaSize;
            const vertices = [
                -size, 0, -size,  size, 0, -size,  size, 0,  size, -size, 0,  size
            ];

            const colors = [
                0.1, 0.1, 0.1, 1.0,
                0.1, 0.1, 0.1, 1.0,
                0.1, 0.1, 0.1, 1.0,
                0.1, 0.1, 0.1, 1.0
            ];

            const indices = [0, 1, 2, 0, 2, 3];
            drawGeometry(vertices, colors, indices);
        }

        function drawGeometry(vertices, colors, indices) {
            // Buffer de vértices
            const vertexBuffer = gl.createBuffer();
            gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(vertices), gl.STATIC_DRAW);
            
            const positionAttributeLocation = gl.getAttribLocation(program, "aVertexPosition");
            gl.enableVertexAttribArray(positionAttributeLocation);
            gl.vertexAttribPointer(positionAttributeLocation, 3, gl.FLOAT, false, 0, 0);

            // Buffer de cores
            const colorBuffer = gl.createBuffer();
            gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer);
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(colors), gl.STATIC_DRAW);
            
            const colorAttributeLocation = gl.getAttribLocation(program, "aVertexColor");
            gl.enableVertexAttribArray(colorAttributeLocation);
            gl.vertexAttribPointer(colorAttributeLocation, 4, gl.FLOAT, false, 0, 0);

            // Buffer de índices
            const indexBuffer = gl.createBuffer();
            gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
            gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, new Uint16Array(indices), gl.STATIC_DRAW);

            // Desenha
            gl.drawElements(gl.TRIANGLES, indices.length, gl.UNSIGNED_SHORT, 0);
        }

        // Função lookAt simplificada
        function lookAt(out, eye, center, up) {
            const eyex = eye[0], eyey = eye[1], eyez = eye[2];
            const upx = up[0], upy = up[1], upz = up[2];
            const centerx = center[0], centery = center[1], centerz = center[2];

            let z0 = eyex - centerx, z1 = eyey - centery, z2 = eyez - centerz;
            let len = 1 / Math.sqrt(z0 * z0 + z1 * z1 + z2 * z2);
            z0 *= len; z1 *= len; z2 *= len;

            let x0 = upy * z2 - upz * z1, x1 = upz * z0 - upx * z2, x2 = upx * z1 - upy * z0;
            len = Math.sqrt(x0 * x0 + x1 * x1 + x2 * x2);
            if (len) {
                len = 1 / len;
                x0 *= len; x1 *= len; x2 *= len;
            }

            let y0 = z1 * x2 - z2 * x1, y1 = z2 * x0 - z0 * x2, y2 = z0 * x1 - z1 * x0;
            len = Math.sqrt(y0 * y0 + y1 * y1 + y2 * y2);
            if (len) {
                len = 1 / len;
                y0 *= len; y1 *= len; y2 *= len;
            }

            out[0] = x0; out[1] = y0; out[2] = z0; out[3] = 0;
            out[4] = x1; out[5] = y1; out[6] = z1; out[7] = 0;
            out[8] = x2; out[9] = y2; out[10] = z2; out[11] = 0;
            out[12] = -(x0 * eyex + x1 * eyey + x2 * eyez);
            out[13] = -(y0 * eyex + y1 * eyey + y2 * eyez);
            out[14] = -(z0 * eyex + z1 * eyey + z2 * eyez);
            out[15] = 1;
        }

        // Inicialização quando a página carrega
        window.addEventListener('load', () => {
            initAuth();
            initGame();
        });
    </script>
</body>
</html>