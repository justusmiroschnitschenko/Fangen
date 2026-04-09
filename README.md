<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fangen Pure Timer</title>
    <style>
        :root {
            --bg-color: #000000;
            --accent-run: #2ecc71;
            --accent-seek: #e74c3c;
        }

        body {
            font-family: 'Inter', -apple-system, sans-serif;
            background-color: var(--bg-color);
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            overflow: hidden;
            transition: background-color 1s ease;
        }

        .container {
            text-align: center;
            width: 100%;
        }

        #active-role {
            font-size: 1.5rem;
            text-transform: uppercase;
            letter-spacing: 5px;
            margin-bottom: 10px;
            opacity: 0.6;
        }

        #status-text {
            font-size: 4rem;
            font-weight: 900;
            margin-bottom: 20px;
        }

        .timer-display {
            font-size: 28vw;
            font-weight: 800;
            font-variant-numeric: tabular-nums;
            line-height: 1;
        }

        /* Phasen-Farben */
        .phase-run { background-color: #062006; color: var(--accent-run); }
        .phase-seek { background-color: #200606; color: var(--accent-seek); }
        .phase-start { background-color: #111; color: #fff; }
    </style>
</head>
<body onclick="startAppOnce()">

<div class="container" id="app-container">
    <div id="active-role">Bereit?</div>
    <div id="status-text">Tippen zum Starten</div>
    <div class="timer-display" id="timer">07:00</div>
</div>

<script>
    let timeLeft;
    let isRunning = false;
    let currentPhase = 'RUN'; 
    let roundCount = 1;

    const TIMES = {
        RUN_FIRST: 7 * 60,
        RUN_LATER: 5 * 60,
        SEEK_FIRST: 12 * 60,
        SEEK_LATER: 10 * 60
    };

    const timerDisplay = document.getElementById('timer');
    const statusText = document.getElementById('status-text');
    const roleText = document.getElementById('active-role');
    const container = document.body;

    function getTargetTime() {
        if (currentPhase === 'RUN') {
            return (roundCount === 1) ? TIMES.RUN_FIRST : TIMES.RUN_LATER;
        } else {
            return (roundCount === 1) ? TIMES.SEEK_FIRST : TIMES.SEEK_LATER;
        }
    }

    function updateUI() {
        const minutes = Math.floor(timeLeft / 60);
        const seconds = timeLeft % 60;
        timerDisplay.textContent = `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
        
        if (currentPhase === 'RUN') {
            roleText.textContent = "Läufer";
            statusText.textContent = "LAUFT!";
            container.className = "phase-run";
        } else {
            roleText.textContent = "Sucher";
            statusText.textContent = "SUCHT!";
            container.className = "phase-seek";
        }
    }

    function startAppOnce() {
        if (!isRunning) {
            isRunning = true;
            timeLeft = TIMES.RUN_FIRST;
            updateUI();
            
            setInterval(() => {
                timeLeft--;
                if (timeLeft <= 0) {
                    autoSwitch();
                }
                updateUI();
            }, 1000);
        }
    }

    function autoSwitch() {
        if (currentPhase === 'RUN') {
            currentPhase = 'SEEK';
        } else {
            currentPhase = 'RUN';
            roundCount++;
        }
        timeLeft = getTargetTime();
        if (navigator.vibrate) navigator.vibrate([1000, 200, 1000]); // Lange Vibration beim Wechsel
    }

    // Initialer Zustand
    container.className = "phase-start";
</script>

</body>
</html>
