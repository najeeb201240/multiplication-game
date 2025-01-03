<!DOCTYPE html>
<html lang="ar" dir="rtl">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>الإصابة في جدول الضرب</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 0;
            text-align: center;
            direction: rtl;
        }

        .game-container {
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
            width: 90%;
            max-width: 600px;
            margin: 20px auto;
            position: relative;
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .header div {
            font-size: 18px;
            font-weight: bold;
        }

        #game-title {
            font-size: 28px;
            font-weight: bold;
            margin-bottom: 10px;
        }

        #game-subtitle {
            font-size: 18px;
            color: #555;
            margin-bottom: 20px;
        }

        #question-box {
            font-size: 24px;
            background-color: #fffbcc;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        #falling-answers {
            position: relative;
            height: 300px;
            overflow: hidden;
            background-color: #e0f7fa;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        .circle {
            position: absolute;
            width: 50px;
            height: 50px;
            background-color: #f0f0f0;
            border-radius: 50%;
            text-align: center;
            line-height: 50px;
            font-size: 18px;
            cursor: pointer;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
        }

        .circle.correct {
            background-color: #a8f0a1;
        }

        .circle.wrong {
            background-color: #f0a1a1;
        }

        .buttons {
            display: flex;
            justify-content: space-around;
            margin-top: 20px;
        }

        button {
            padding: 10px 20px;
            font-size: 16px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            background-color: #2196f3;
            color: white;
        }

        button:hover {
            background-color: #1976d2;
        }

        #result {
            display: none;
            font-size: 20px;
            margin-top: 20px;
        }

        .footer {
            margin-top: 30px;
            font-size: 14px;
            color: #888;
        }

        .modal {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: #fff;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
            border-radius: 10px;
            padding: 20px;
            text-align: center;
            z-index: 1000;
        }

        .modal.show {
            display: block;
        }

        .modal h1 {
            font-size: 24px;
            margin-bottom: 15px;
        }

        .modal p {
            font-size: 18px;
            margin-bottom: 10px;
        }

        .modal .icon {
            font-size: 48px;
            margin-bottom: 15px;
        }

        #feedback {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            display: none;
            background: rgba(255, 255, 255, 0.9);
            border-radius: 10px;
            padding: 20px;
            text-align: center;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }

        #feedback.correct {
            color: green;
        }

        #feedback.wrong {
            color: red;
        }

        #feedback .icon {
            font-size: 48px;
        }
    </style>
</head>

<body>
    <!-- عنوان اللعبة -->
    <div id="game-title">الإصابة في جدول الضرب</div>
    <div id="game-subtitle">ركز وأصب الهدف</div>

    <div class="game-container">
        <div class="header">
            <div>الدرجة: <span id="score">٠</span> / <span id="total">٠</span></div>
        </div>

        <!-- تحديد عدد الأسئلة -->
        <label for="numQuestions">اختر عدد الأسئلة:</label>
        <input type="number" id="numQuestions" min="1" max="20" value="10" style="width: 60px; text-align: center;">

        <div id="question-box">؟ × ؟ = ؟</div>

        <div id="falling-answers"></div>

        <div class="buttons">
            <button id="restart-btn" onclick="restartGame()">بدء من جديد</button>
            <button id="pause-btn" onclick="pauseGame()">إيقاف مؤقت</button>
            <button id="end-btn" onclick="endGame()">إنهاء</button>
        </div>
    </div>

    <!-- نافذة النتيجة -->
    <div id="resultModal" class="modal">
        <div class="icon" id="resultIcon">🎉</div>
        <h1 id="resultMessage">ممتاز!</h1>
        <p id="resultDetails"></p>
    </div>

    <!-- نافذة التغذية الراجعة -->
    <div id="feedback">
        <div class="icon" id="feedbackIcon">✔️</div>
        <p id="feedbackMessage"></p>
    </div>

    <div class="footer">
        تم تصميم هذا التطبيق لتعزيز التعلم الممتع باستخدام الذكاء الاصطناعي.
    </div>

    <audio id="correctSound" src="correct.mp3"></audio>
    <audio id="wrongSound" src="wrong.mp3"></audio>

    <script>
        const scoreEl = document.getElementById("score");
        const totalEl = document.getElementById("total");
        const questionBox = document.getElementById("question-box");
        const fallingAnswers = document.getElementById("falling-answers");
        const resultModal = document.getElementById("resultModal");
        const resultIcon = document.getElementById("resultIcon");
        const resultMessage = document.getElementById("resultMessage");
        const resultDetails = document.getElementById("resultDetails");
        const feedback = document.getElementById("feedback");
        const feedbackIcon = document.getElementById("feedbackIcon");
        const feedbackMessage = document.getElementById("feedbackMessage");
        const numQuestionsInput = document.getElementById("numQuestions");
        const correctSound = document.getElementById("correctSound");
        const wrongSound = document.getElementById("wrongSound");

        let score = 0;
        let totalQuestions = 0;
        let maxQuestions = 10;
        let isPaused = false;
        let isGameEnded = false;

        function randomInt(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
        }

        function generateQuestion() {
            const factor1 = randomInt(1, 10);
            const factor2 = randomInt(1, 10);
            const correctAnswer = factor1 * factor2;

            questionBox.textContent = `${factor1} × ${factor2} = ؟`;

            const answers = new Set();
            answers.add(correctAnswer);

            while (answers.size < 3) {
                const wrongAnswer = randomInt(1, 100);
                if (!answers.has(wrongAnswer)) {
                    answers.add(wrongAnswer);
                }
            }

            return {
                correctAnswer: correctAnswer,
                answers: Array.from(answers),
            };
        }

        function showFeedback(isCorrect) {
            feedback.style.display = "block";
            if (isCorrect) {
                feedback.className = "correct";
                feedbackIcon.textContent = "✔️";
                feedbackMessage.textContent = "إجابة صحيحة! أحسنت!";
                correctSound.play();
            } else {
                feedback.className = "wrong";
                feedbackIcon.textContent = "❌";
                feedbackMessage.textContent = "إجابة خاطئة! حاول مرة أخرى!";
                wrongSound.play();
            }

            setTimeout(() => {
                feedback.style.display = "none";
            }, 2000);
        }

        function dropAnswers(questionData) {
            if (isGameEnded || totalQuestions >= maxQuestions) {
                endGame();
                return;
            }

            fallingAnswers.innerHTML = "";
            totalQuestions++;
            totalEl.textContent = totalQuestions;

            const shuffledAnswers = questionData.answers.sort(() => Math.random() - 0.5);

            shuffledAnswers.forEach((answer, index) => {
                const circle = document.createElement("div");
                circle.className = "circle";
                circle.textContent = answer;
                circle.style.left = `${index * 33 + 10}%`;
                circle.style.top = "-50px";

                circle.onclick = () => {
                    if (answer === questionData.correctAnswer) {
                        circle.classList.add("correct");
                        score++;
                        scoreEl.textContent = score;
                        showFeedback(true);
                    } else {
                        circle.classList.add("wrong");
                        showFeedback(false);
                    }
                    setTimeout(() => {
                        if (!isGameEnded) startGame();
                    }, 1000);
                };

                fallingAnswers.appendChild(circle);
                animateCircle(circle);
            });
        }

        function animateCircle(circle) {
            let top = parseInt(circle.style.top.replace("px", "")) || -50;
            const fallInterval = setInterval(() => {
                if (isPaused || isGameEnded) {
                    clearInterval(fallInterval);
                    return;
                }

                top += 1;
                circle.style.top = `${top}px`;

                if (top >= fallingAnswers.offsetHeight - circle.offsetHeight) {
                    clearInterval(fallInterval);
                }
            }, 10);
        }

        function restartGame() {
            score = 0;
            totalQuestions = 0;
            maxQuestions = parseInt(numQuestionsInput.value) || 10; // تحديث عدد الأسئلة عند بدء اللعبة
            scoreEl.textContent = score;
            totalEl.textContent = totalQuestions;
            isGameEnded = false;
            resultModal.classList.remove("show");
            startGame();
        }

        function endGame() {
            isGameEnded = true;

            const percentage = (score / maxQuestions) * 100;
            let message = "";
            let icon = "";

            if (percentage >= 90) {
                message = "ممتاز! استمر في العمل الرائع.";
                icon = "🎉";
            } else if (percentage >= 75) {
                message = "جيد جدًا! أداء رائع، يمكنك تحقيق المزيد.";
                icon = "😊";
            } else if (percentage >= 50) {
                message = "جيد! حاول تحسين مستواك.";
                icon = "🙂";
            } else {
                message = "راسب. حاول مجددًا وستتحسن بالتدريب!";
                icon = "😢";
            }

            resultIcon.textContent = icon;
            resultMessage.textContent = message;
            resultDetails.textContent = `حصلت على ${score} من ${maxQuestions} (${Math.round(percentage)}%)`;
            resultModal.classList.add("show");
        }

        function startGame() {
            const questionData = generateQuestion();
            dropAnswers(questionData);
        }

        startGame();
    </script>
</body>

</html>
