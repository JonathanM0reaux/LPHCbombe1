<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Désamorçage de Bombe</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap');

        body {
            text-align: center;
            font-family: 'Orbitron', sans-serif;
            background-color: #000;
            color: #00ffcc;
            overflow: hidden;
            position: relative;
        }

        /* Fond animé */
        .background-animation {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: url('https://cdn.pixabay.com/animation/2022/10/26/05/44/05-44-47-979_512.gif') no-repeat center center fixed;
            background-size: cover;
            opacity: 0.3;
            z-index: -1;
        }

        #timer {
            font-size: 50px;
            color: #ff3300;
            margin: 20px;
            transition: color 0.5s ease-in-out;
        }

        .code-container {
            margin: 20px;
        }

        .code-input {
            font-size: 40px;
            width: 60px;
            text-align: center;
            margin: 5px;
            pointer-events: none;
            background-color: #222;
            color: #00ffcc;
            border: 2px solid #00ffcc;
        }

        #defuse-btn, #start-btn {
            font-size: 24px;
            padding: 15px;
            margin-top: 20px;
            cursor: pointer;
            background-color: #ff3300;
            color: white;
            border: none;
            transition: transform 0.3s, background-color 0.3s;
        }

        #defuse-btn:hover, #start-btn:hover {
            background-color: #cc2900;
            transform: scale(1.1);
        }

        .keypad {
            margin-top: 20px;
        }

        .keypad button {
            font-size: 28px;
            width: 70px;
            height: 70px;
            margin: 5px;
            cursor: pointer;
            background-color: #111;
            color: #00ffcc;
            border: 2px solid #00ffcc;
            transition: background-color 0.3s, transform 0.2s;
        }

        .keypad button:hover {
            background-color: #444;
        }

        /* Effet tremblement (plus vif) */
        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            10% { transform: translateX(-20px); }
            20% { transform: translateX(20px); }
            30% { transform: translateX(-20px); }
            40% { transform: translateX(20px); }
            50% { transform: translateX(-20px); }
            60% { transform: translateX(20px); }
            70% { transform: translateX(-20px); }
            80% { transform: translateX(20px); }
            90% { transform: translateX(-20px); }
        }

        .shake {
            animation: shake 0.4s ease-in-out;
        }

        /* Message de succès en grand */
        .success-message {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 255, 100, 0.9);
            color: black;
            padding: 30px;
            font-size: 50px;
            font-weight: bold;
            text-align: center;
            border-radius: 20px;
            display: none;
            z-index: 1000;
        }

    </style>

    <!-- Sons -->
    <audio id="error-sound" src="/mnt/data/error-call-to-attention-129258.mp3" preload="auto"></audio> <!-- Son d'erreur -->
    <audio id="success-sound" src="/mnt/data/game_sounds/Game-level-up-sound-effect.mp3" preload="auto"></audio> <!-- Son succès -->
    <audio id="tick-sound" src="https://www.fesliyanstudios.com/play-mp3/699" preload="auto"></audio> <!-- Tic-tac -->

</head>
<body>
    <div class="background-animation"></div>

    <h1>Désamorçage de la Bombe</h1>
    <div id="timer">25:00</div>
    <button id="start-btn" onclick="startCountdown()">Démarrer</button>

    <div class="code-container">
        <input type="text" class="code-input" maxlength="1" id="digit1">
        <input type="text" class="code-input" maxlength="1" id="digit2">
        <input type="text" class="code-input" maxlength="1" id="digit3">
        <input type="text" class="code-input" maxlength="1" id="digit4">
    </div>

    <div class="keypad">
        <button onclick="enterDigit(1)">1</button>
        <button onclick="enterDigit(2)">2</button>
        <button onclick="enterDigit(3)">3</button><br>
        <button onclick="enterDigit(4)">4</button>
        <button onclick="enterDigit(5)">5</button>
        <button onclick="enterDigit(6)">6</button><br>
        <button onclick="enterDigit(7)">7</button>
        <button onclick="enterDigit(8)">8</button>
        <button onclick="enterDigit(9)">9</button><br>
        <button onclick="clearDigits()">C</button>
        <button onclick="enterDigit(0)">0</button>
    </div>

    <button id="defuse-btn" onclick="checkCode()">Désamorcer</button>

    <div class="success-message" id="success-message">🎉 Bombe désamorcée !</div>

    <script>
        let timeLeft = 1500;
        let countdownInterval;
        const correctCode = "1234";
        const digits = document.querySelectorAll(".code-input");
        let currentInputIndex = 0;

        function startCountdown() {
            document.getElementById("start-btn").style.display = 'none';
            countdownInterval = setInterval(() => {
                if (timeLeft > 0) {
                    timeLeft--;
                    let minutes = Math.floor(timeLeft / 60);
                    let seconds = timeLeft % 60;
                    document.getElementById("timer").textContent = `${minutes}:${seconds.toString().padStart(2, '0')}`;
                    
                    if (timeLeft <= 10) {
                        document.getElementById("timer").style.color = "red";
                    }

                    document.getElementById("tick-sound").play();
                } else {
                    clearInterval(countdownInterval);
                    alert("💥 BOOM ! La bombe a explosé !");
                }
            }, 1000);
        }

        function enterDigit(digit) {
            if (currentInputIndex < 4) {
                digits[currentInputIndex].value = digit;
                currentInputIndex++;
            }
        }

        function clearDigits() {
            digits.forEach(input => input.value = "");
            currentInputIndex = 0;
        }

        function checkCode() {
            let enteredCode = [...digits].map(input => input.value).join("");
            if (enteredCode === correctCode) {
                clearInterval(countdownInterval);
                document.getElementById("success-sound").play();
                document.getElementById("success-message").style.display = "block";
            } else {
                document.getElementById("error-sound").play();
                document.body.classList.add("shake");
                setTimeout(() => document.body.classList.remove("shake"), 400);
            }
        }
    </script>
</body>
</html>
