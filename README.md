<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Spacebar Timer</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
        }
        #timer {
            font-size: 2rem;
            padding: 20px;
            background-color: #333;
            color: white;
            border-radius: 10px;
        }
    </style>
</head>
<body>

    <div id="timer">0.00</div>

    <script>
        let timer;
        let isTimerRunning = false;
        let hasStopped = false;
        let waitingToStart = true;
        let timerStartTime = 0;
        let elapsedTime = 0;
        let stopTimes = [];
        let resetCount = 0;
        let resetTimeout;
        let spacePressed = false;

        const timerElement = document.getElementById("timer");

        function startTimer() {
            timerStartTime = Date.now() - elapsedTime;
            timer = setInterval(() => {
                elapsedTime = Date.now() - timerStartTime;
                timerElement.textContent = (elapsedTime / 1000).toFixed(2);
            }, 10);
            isTimerRunning = true;
            hasStopped = false;
            clearTimeout(resetTimeout);
        }

        function stopTimer() {
            clearInterval(timer);
            isTimerRunning = false;
            hasStopped = true;

            const currentStop = elapsedTime / 1000;
            stopTimes.push(currentStop);
            resetCount++;

            if (resetCount === 25) {
                const average = stopTimes.reduce((a, b) => a + b, 0) / stopTimes.length;
                timerElement.textContent = `Average: ${average.toFixed(2)}s`;
                resetCount = 0;
                stopTimes = [];
            } else {
                resetTimeout = setTimeout(resetTimer, 1000);
            }
        }

        function resetTimer() {
            elapsedTime = 0;
            timerElement.textContent = "0.00";
            hasStopped = false;
            waitingToStart = true;
        }

        document.addEventListener("keydown", function (event) {
            if (event.code === "Space" && !spacePressed) {
                spacePressed = true;

                if (isTimerRunning) {
                    stopTimer(); // stop immediately when space is pressed
                }
            }
        });

        document.addEventListener("keyup", function (event) {
            if (event.code === "Space") {
                spacePressed = false;

                // Only start on spacebar release *if* we're waiting to start
                if (waitingToStart && !isTimerRunning && elapsedTime === 0) {
                    waitingToStart = false;
                    startTimer();
                }
            }
        });
    </script>

</body>
</html>
