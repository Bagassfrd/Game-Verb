<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>English Verb Game</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #ff7a18, #af002d);
            color: white;
            text-align: center;
            padding: 20px;
            overflow: hidden;
        }

        h1 {
            font-size: 3em;
            text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.6);
        }

        .game-container {
            background-color: rgba(0, 0, 0, 0.5);
            border-radius: 10px;
            padding: 30px;
            margin: 50px auto;
            width: 80%;
            max-width: 600px;
        }

        .question {
            font-size: 1.5em;
            margin-bottom: 20px;
        }

        .answer-btn {
            padding: 15px 30px;
            background-color: #28a745;
            border: none;
            color: white;
            font-size: 1.2em;
            border-radius: 8px;
            margin: 10px;
            width: 80%;
            transition: transform 0.3s, background-color 0.3s;
            cursor: pointer;
        }

        .answer-btn:hover {
            transform: scale(1.1);
            background-color: #218838;
        }

        .answer-btn:active {
            transform: scale(0.9);
        }

        .answer-btn.correct {
            background-color: #28a745 !important;
        }

        .answer-btn.incorrect {
            background-color: #dc3545 !important;
        }

        .game-info {
            margin-top: 20px;
            font-size: 1.2em;
        }

        .lives, .score {
            font-weight: bold;
            color: yellow;
        }

        .animation {
            display: block;
            margin: 10px auto;
            font-size: 1.5em;
        }

        .hidden {
            display: none;
        }

        .game-over {
            font-size: 2em;
            color: red;
            text-shadow: 3px 3px 5px rgba(0, 0, 0, 0.7);
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.1); }
            100% { transform: scale(1); }
        }

        .button-animation {
            animation: pulse 1.5s infinite;
        }
    </style>
</head>
<body>
    <h1>Verb Learning Challenge</h1>
    <div class="game-container">
        <div id="game-over" class="game-over hidden">Game Over! Try again!</div>
        <div id="question" class="question"></div>
        <button id="answer1" class="answer-btn button-animation"></button>
        <button id="answer2" class="answer-btn button-animation"></button>
        <button id="answer3" class="answer-btn button-animation"></button>
        <button id="answer4" class="answer-btn button-animation"></button>
        <div class="game-info">
            <p>Lives: <span id="lives" class="lives">5</span></p>
            <p>Score: <span id="score" class="score">0</span></p>
        </div>
    </div>

    <audio id="bg-audio" loop autoplay>
        <source src="background-music.mp3" type="audio/mp3">
    </audio>

    <audio id="correct-sound">
        <source src="correct-answer.mp3" type="audio/mp3">
    </audio>

    <audio id="incorrect-sound">
        <source src="incorrect-answer.mp3" type="audio/mp3">
    </audio>

    <script>
        const questions = [
            { question: "What is the past tense of 'eat'?", answers: ["ate", "eats", "eating", "eaten"], correct: 0 },
            { question: "What is the past tense of 'go'?", answers: ["went", "gone", "goes", "going"], correct: 0 },
            { question: "What is the past tense of 'write'?", answers: ["writes", "wrote", "written", "writing"], correct: 1 },
            { question: "What is the past tense of 'speak'?", answers: ["spoken", "speaked", "spoke", "speaks"], correct: 2 },
            { question: "What is the past tense of 'run'?", answers: ["ran", "run", "running", "runned"], correct: 0 },
            { question: "What is the past tense of 'play'?", answers: ["played", "plays", "playing", "play"], correct: 0 },
            { question: "What is the past tense of 'drink'?", answers: ["drinks", "drank", "drunk", "drinking"], correct: 1 },
            { question: "What is the past tense of 'come'?", answers: ["came", "come", "coming", "comed"], correct: 0 },
            { question: "What is the past tense of 'do'?", answers: ["did", "done", "do", "doing"], correct: 0 },
            { question: "What is the past tense of 'see'?", answers: ["sees", "saw", "seen", "seeing"], correct: 1 },
            { question: "What is the past tense of 'take'?", answers: ["takes", "took", "taken", "taking"], correct: 1 },
            { question: "What is the past tense of 'buy'?", answers: ["bought", "buyed", "buying", "boughted"], correct: 0 },
            { question: "What is the past tense of 'begin'?", answers: ["begins", "began", "begun", "beginning"], correct: 1 },
            { question: "What is the past tense of 'teach'?", answers: ["taught", "teach", "teached", "teaching"], correct: 0 },
            { question: "What is the past tense of 'sang'?", answers: ["sung", "sing", "sang", "sunged"], correct: 0 },
            { question: "What is the past tense of 'have'?", answers: ["had", "haved", "has", "having"], correct: 0 },
            { question: "What is the past tense of 'give'?", answers: ["gives", "gave", "given", "giving"], correct: 1 },
            { question: "What is the past tense of 'bring'?", answers: ["brang", "brought", "bringed", "bringing"], correct: 1 },
            { question: "What is the past tense of 'fall'?", answers: ["fell", "fallen", "falls", "falling"], correct: 0 },
            { question: "What is the past tense of 'catch'?", answers: ["caught", "catch", "catched", "catching"], correct: 0 }
        ];

        let currentQuestionIndex = 0;
        let score = 0;
        let lives = 5;

        const questionElement = document.getElementById("question");
        const answerButtons = [
            document.getElementById("answer1"),
            document.getElementById("answer2"),
            document.getElementById("answer3"),
            document.getElementById("answer4")
        ];
        const livesElement = document.getElementById("lives");
        const scoreElement = document.getElementById("score");
        const gameOverElement = document.getElementById("game-over");
        const correctSound = document.getElementById("correct-sound");
        const incorrectSound = document.getElementById("incorrect-sound");

        function loadQuestion() {
            if (currentQuestionIndex < questions.length) {
                const currentQuestion = questions[currentQuestionIndex];
                questionElement.textContent = currentQuestion.question;
                answerButtons.forEach((button, index) => {
                    button.textContent = currentQuestion.answers[index];
                    button.classList.remove("correct", "incorrect");
                    button.onclick = () => checkAnswer(index);
                });
            } else {
                gameOver();
            }
        }

        function checkAnswer(selectedIndex) {
            const correctAnswerIndex = questions[currentQuestionIndex].correct;
            if (selectedIndex === correctAnswerIndex) {
                score++;
                scoreElement.textContent = score;
                correctSound.play();
                answerButtons[selectedIndex].classList.add("correct");
            } else {
                lives--;
                livesElement.textContent = lives;
                incorrectSound.play();
                answerButtons[selectedIndex].classList.add("incorrect");
                if (lives === 0) {
                    gameOver();
                }
            }
            setTimeout(() => {
                currentQuestionIndex++;
                loadQuestion();
            }, 1000);
        }

        function gameOver() {
            gameOverElement.classList.remove("hidden");
            setTimeout(() => {
                location.reload(); // Reload the page to restart the game
            }, 3000); // Restart game after 3 seconds
        }

        // Initialize the game
        loadQuestion();
    </script>
</body>
</html>
