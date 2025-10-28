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
        }

        #gameCanvas {
            width: 100%;
            height: 100%;
            display: block;
            background: #000000;
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

        #gameTitle {
            position: absolute;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 32px;
            color: #00ff00;
            text-shadow: 0 0 15px #00ff00;
            background: rgba(0, 0, 0, 0.8);
            padding: 15px 30px;
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

        #startScreen h1 {
            color: #00ff00;
            font-size: 48px;
            margin-bottom: 20px;
            text-shadow: 0 0 20px #00ff00;
        }

        #startScreen p {
            font-size: 18px;
            margin: 10px 0;
            color: #cccccc;
        }

        #startButton {
            background: linear-gradient(45deg, #00ff00, #00cc00);
            color: black;
            border: none;
            padding: 15px 40px;
            font-size: 20px;
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
            font-size: 20px;
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
            font-size: 16px;
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
            width: 80px;
            height: 80px;
            background: rgba(0, 255, 0, 0.3);
            border: 2px solid #00ff00;
            border-radius: 50%;
            color: white;
            font-size: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            user-select: none;
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
                bottom: 200px;
            }
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <canvas id="gameCanvas"></canvas>
        
        <div id="uiOverlay">
            <div id="score">Score: 0</div>
            <div id="gameTitle">🐍 SNAKE 3D 🐍</div>
            
            <div id="startScreen">
                <h1>SNAKE 3D</h1>
                <p>🎮 Controle a cobra e colete a comida!</p>
                <p>⬅️➡️ Use A/D ou setas para girar</p>
                <p>🚫 Evite bater no próprio corpo!</p>
                <button id="startButton">JOGAR</button>
            </div>
            
            <div id="gameOverScreen">
                <h2>GAME OVER</h2>
                <div id="finalScore">Score: 0</div>
                <p>Tente novamente!</p>
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

    <script>
        // Configurações do jogo
        const config = {
            snakeSpeed: 0.08,
            rotationSpeed: 3,
            segmentDistance: 0.8,
            gameAreaSize: 30,
            initialSegments: 3
        };

        // Variáveis do jogo
        let canvas, gl;
        let snake = [];
        let food = { x: 0, y: 0.5, z: 0 };
        let direction = { x: 0, y: 0, z: 1 };
        let score = 0;
        let gameStarted = false;
        let gameOver = false;
        let rotation = 0;
        let lastTime = 0;
        let moveCounter = 0;

        // Shaders WebGL
        const vertexShaderSource = `
            attribute vec3 aPosition;
            attribute vec3 aColor;
            uniform mat4 uModelViewMatrix;
            uniform mat4 uProjectionMatrix;
            varying vec3 vColor;
            
            void main() {
                gl_Position = uProjectionMatrix * uModelViewMatrix * vec4(aPosition, 1.0);
                vColor = aColor;
            }
        `;

        const fragmentShaderSource = `
            precision mediump float;
            varying vec3 vColor;
            
            void main() {
                gl_FragColor = vec4(vColor, 1.0);
            }
        `;

        // Inicialização
        function init() {
            canvas = document.getElementById('gameCanvas');
            gl = canvas.getContext('webgl');
            
            if (!gl) {
                alert('WebGL não suportado neste navegador!');
                return;
            }

            // Configura tamanho do canvas
            canvas.width = canvas.clientWidth;
            canvas.height = canvas.clientHeight;
            gl.viewport(0, 0, canvas.width, canvas.height);

            // Compila shaders
            const vertexShader = compileShader(gl.VERTEX_SHADER, vertexShaderSource);
            const fragmentShader = compileShader(gl.FRAGMENT_SHADER, fragmentShaderSource);
            const program = createProgram(vertexShader, fragmentShader);
            gl.useProgram(program);

            // Configura projeção
            setupProjection(program);

            // Event listeners
            setupEventListeners();

            // Inicia game loop
            requestAnimationFrame(gameLoop);
        }

        function compileShader(type, source) {
            const shader = gl.createShader(type);
            gl.shaderSource(shader, source);
            gl.compileShader(shader);
            
            if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
                console.error('Erro no shader:', gl.getShaderInfoLog(shader));
                gl.deleteShader(shader);
                return null;
            }
            return shader;
        }

        function createProgram(vertexShader, fragmentShader) {
            const program = gl.createProgram();
            gl.attachShader(program, vertexShader);
            gl.attachShader(program, fragmentShader);
            gl.linkProgram(program);
            
            if (!gl.getProgramParameter(program, gl.LINK_STATUS)) {
                console.error('Erro no programa:', gl.getProgramInfoLog(program));
                return null;
            }
            return program;
        }

        function setupProjection(program) {
            const fieldOfView = 45 * Math.PI / 180;
            const aspect = canvas.width / canvas.height;
            const zNear = 0.1;
            const zFar = 100.0;
            const projectionMatrix = new Float32Array(16);
            
            const f = 1.0 / Math.tan(fieldOfView / 2);
            projectionMatrix[0] = f / aspect;
            projectionMatrix[5] = f;
            projectionMatrix[10] = (zFar + zNear) / (zNear - zFar);
            projectionMatrix[11] = -1;
            projectionMatrix[14] = (2 * zFar * zNear) / (zNear - zFar);
            
            const uProjectionMatrix = gl.getUniformLocation(program, 'uProjectionMatrix');
            gl.uniformMatrix4fv(uProjectionMatrix, false, projectionMatrix);
        }

        function setupEventListeners() {
            // Teclado
            document.addEventListener('keydown', (e) => {
                if (!gameStarted || gameOver) return;
                
                switch(e.key) {
                    case 'a':
                    case 'ArrowLeft':
                        rotation = config.rotationSpeed;
                        break;
                    case 'd':
                    case 'ArrowRight':
                        rotation = -config.rotationSpeed;
                        break;
                }
            });

            document.addEventListener('keyup', (e) => {
                if (e.key === 'a' || e.key === 'd' || e.key === 'ArrowLeft' || e.key === 'ArrowRight') {
                    rotation = 0;
                }
            });

            // Botões mobile
            document.getElementById('leftBtn').addEventListener('touchstart', () => rotation = config.rotationSpeed);
            document.getElementById('leftBtn').addEventListener('touchend', () => rotation = 0);
            document.getElementById('rightBtn').addEventListener('touchstart', () => rotation = -config.rotationSpeed);
            document.getElementById('rightBtn').addEventListener('touchend', () => rotation = 0);

            // Botões UI
            document.getElementById('startButton').addEventListener('click', startGame);
            document.getElementById('restartButton').addEventListener('click', restartGame);
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
                snake.push({ x: 0, y: 0.5, z: -i * config.segmentDistance });
            }
            
            // Posição inicial da comida
            spawnFood();
            
            // Configura câmera
            setupCamera();
        }

        function restartGame() {
            document.getElementById('gameOverScreen').style.display = 'none';
            startGame();
        }

        function spawnFood() {
            // Gera comida em posição aleatória
            const halfSize = config.gameAreaSize / 2 - 2;
            food.x = Math.random() * halfSize * 2 - halfSize;
            food.z = Math.random() * halfSize * 2 - halfSize;
            food.y = 0.5;
        }

        function setupCamera() {
            const program = gl.getParameter(gl.CURRENT_PROGRAM);
            const uModelViewMatrix = gl.getUniformLocation(program, 'uModelViewMatrix');
            
            // Configura câmera em perspectiva
            const modelViewMatrix = new Float32Array(16);
            mat4.lookAt(modelViewMatrix, 
                [0, 10, 15],  // Posição da câmera
                [0, 0, 0],    // Para onde olha
                [0, 1, 0]     // Vetor up
            );
            
            gl.uniformMatrix4fv(uModelViewMatrix, false, modelViewMatrix);
        }

        function gameLoop(currentTime) {
            const deltaTime = Math.min((currentTime - lastTime) / 16, 2); // Limita delta time
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
            if (moveCounter >= config.snakeSpeed * 10) {
                moveCounter = 0;
                
                // Nova posição da cabeça
                const head = snake[0];
                const newHead = {
                    x: head.x + direction.x,
                    y: head.y,
                    z: head.z + direction.z
                };

                // Verifica colisão com as paredes
                if (Math.abs(newHead.x) > config.gameAreaSize / 2 || 
                    Math.abs(newHead.z) > config.gameAreaSize / 2) {
                    endGame();
                    return;
                }

                // Verifica colisão com o próprio corpo
                for (let i = 1; i < snake.length; i++) {
                    if (distance(newHead, snake[i]) < 0.8) {
                        endGame();
                        return;
                    }
                }

                // Verifica se comeu a comida
                if (distance(newHead, food) < 1.5) {
                    score += 10;
                    updateScore();
                    spawnFood();
                    // Adiciona novo segmento (não remove a cauda)
                } else {
                    // Remove a cauda se não comeu
                    snake.pop();
                }

                // Adiciona nova cabeça
                snake.unshift(newHead);
            }
        }

        function distance(a, b) {
            return Math.sqrt((a.x - b.x) ** 2 + (a.y - b.y) ** 2 + (a.z - b.z) ** 2);
        }

        function endGame() {
            gameOver = true;
            document.getElementById('finalScore').textContent = `Score: ${score}`;
            document.getElementById('gameOverScreen').style.display = 'block';
        }

        function updateScore() {
            document.getElementById('score').textContent = `Score: ${score}`;
        }

        function render() {
            // Limpa o canvas
            gl.clearColor(0.0, 0.0, 0.0, 1.0);
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);
            gl.enable(gl.DEPTH_TEST);

            // Renderiza a cobra
            snake.forEach((segment, index) => {
                const isHead = index === 0;
                const color = isHead ? [0.0, 1.0, 0.0] : [0.2, 0.8, 0.2]; // Verde
                drawCube(segment.x, segment.y, segment.z, 0.5, color);
            });

            // Renderiza a comida
            drawCube(food.x, food.y, food.z, 0.4, [1.0, 0.0, 0.0]); // Vermelho

            // Renderiza o chão
            drawGround();
        }

        function drawCube(x, y, z, size, color) {
            const program = gl.getParameter(gl.CURRENT_PROGRAM);
            const vertices = [
                // Front face
                -size + x, -size + y,  size + z,
                 size + x, -size + y,  size + z,
                 size + x,  size + y,  size + z,
                -size + x,  size + y,  size + z,
                
                // Back face
                -size + x, -size + y, -size + z,
                -size + x,  size + y, -size + z,
                 size + x,  size + y, -size + z,
                 size + x, -size + y, -size + z,
                
                // Top face
                -size + x,  size + y, -size + z,
                -size + x,  size + y,  size + z,
                 size + x,  size + y,  size + z,
                 size + x,  size + y, -size + z,
                
                // Bottom face
                -size + x, -size + y, -size + z,
                 size + x, -size + y, -size + z,
                 size + x, -size + y,  size + z,
                -size + x, -size + y,  size + z,
                
                // Right face
                 size + x, -size + y, -size + z,
                 size + x,  size + y, -size + z,
                 size + x,  size + y,  size + z,
                 size + x, -size + y,  size + z,
                
                // Left face
                -size + x, -size + y, -size + z,
                -size + x, -size + y,  size + z,
                -size + x,  size + y,  size + z,
                -size + x,  size + y, -size + z,
            ];

            const colors = [];
            for (let i = 0; i < 6; i++) {
                for (let j = 0; j < 4; j++) {
                    colors.push(...color);
                }
            }

            const indices = [
                0, 1, 2, 0, 2, 3,    // front
                4, 5, 6, 4, 6, 7,    // back
                8, 9, 10, 8, 10, 11,  // top
                12, 13, 14, 12, 14, 15, // bottom
                16, 17, 18, 16, 18, 19, // right
                20, 21, 22, 20, 22, 23  // left
            ];

            drawGeometry(vertices, colors, indices);
        }

        function drawGround() {
            const size = config.gameAreaSize;
            const vertices = [
                -size, 0, -size,
                 size, 0, -size,
                 size, 0,  size,
                -size, 0,  size,
            ];

            const colors = [
                0.3, 0.3, 0.3,
                0.3, 0.3, 0.3,
                0.3, 0.3, 0.3,
                0.3, 0.3, 0.3,
            ];

            const indices = [0, 1, 2, 0, 2, 3];

            drawGeometry(vertices, colors, indices);
        }

        function drawGeometry(vertices, colors, indices) {
            const program = gl.getParameter(gl.CURRENT_PROGRAM);
            
            // Buffer de vértices
            const vertexBuffer = gl.createBuffer();
            gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(vertices), gl.STATIC_DRAW);
            
            const aPosition = gl.getAttribLocation(program, 'aPosition');
            gl.enableVertexAttribArray(aPosition);
            gl.vertexAttribPointer(aPosition, 3, gl.FLOAT, false, 0, 0);

            // Buffer de cores
            const colorBuffer = gl.createBuffer();
            gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer);
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(colors), gl.STATIC_DRAW);
            
            const aColor = gl.getAttribLocation(program, 'aColor');
            gl.enableVertexAttribArray(aColor);
            gl.vertexAttribPointer(aColor, 3, gl.FLOAT, false, 0, 0);

            // Buffer de índices
            const indexBuffer = gl.createBuffer();
            gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER, indexBuffer);
            gl.bufferData(gl.ELEMENT_ARRAY_BUFFER, new Uint16Array(indices), gl.STATIC_DRAW);

            // Desenha
            gl.drawElements(gl.TRIANGLES, indices.length, gl.UNSIGNED_SHORT, 0);
        }

        // Biblioteca de matrizes simples
        const mat4 = {
            lookAt: function(out, eye, center, up) {
                let x0, x1, x2, y0, y1, y2, z0, z1, z2, len;
                let eyex = eye[0], eyey = eye[1], eyez = eye[2];
                let upx = up[0], upy = up[1], upz = up[2];
                let centerx = center[0], centery = center[1], centerz = center[2];

                z0 = eyex - centerx;
                z1 = eyey - centery;
                z2 = eyez - centerz;

                len = 1 / Math.sqrt(z0 * z0 + z1 * z1 + z2 * z2);
                z0 *= len;
                z1 *= len;
                z2 *= len;

                x0 = upy * z2 - upz * z1;
                x1 = upz * z0 - upx * z2;
                x2 = upx * z1 - upy * z0;
                
                len = Math.sqrt(x0 * x0 + x1 * x1 + x2 * x2);
                if (!len) {
                    x0 = 0; x1 = 0; x2 = 0;
                } else {
                    len = 1 / len;
                    x0 *= len; x1 *= len; x2 *= len;
                }

                y0 = z1 * x2 - z2 * x1;
                y1 = z2 * x0 - z0 * x2;
                y2 = z0 * x1 - z1 * x0;

                len = Math.sqrt(y0 * y0 + y1 * y1 + y2 * y2);
                if (!len) {
                    y0 = 0; y1 = 0; y2 = 0;
                } else {
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
        };

        // Inicia o jogo quando a página carregar
        window.addEventListener('load', init);
        
        // Redimensiona o canvas quando a janela mudar de tamanho
        window.addEventListener('resize', () => {
            if (canvas) {
                canvas.width = canvas.clientWidth;
                canvas.height = canvas.clientHeight;
                gl.viewport(0, 0, canvas.width, canvas.height);
                setupProjection(gl.getParameter(gl.CURRENT_PROGRAM));
            }
        });
    </script>
</body>
</html>