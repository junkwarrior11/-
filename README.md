<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>暗算アプリ</title>
  <style>
    body {
      font-family: 'Comic Sans MS', sans-serif;
      margin: 0;
      padding: 0;
      background-color: #f7f7f7;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      background-image: linear-gradient(to right, #a8c0ff, #c9d9ff);
    }
    .container {
      background-color: #ffffff;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
      width: 700px;
      text-align: center;
      border: 5px solid #4fa3d1;
    }
    h1 {
      color: #4fa3d1;
      font-size: 32px;
      margin-bottom: 20px;
    }
    #problem {
      font-size: 28px;
      margin: 20px 0;
      font-weight: bold;
      color: #333;
      background-color: #a8c0ff;
      padding: 10px;
      border-radius: 8px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
    }
    input {
      font-size: 24px;
      padding: 12px;
      margin: 10px 0;
      width: 100%;
      box-sizing: border-box;
      border-radius: 8px;
      border: 2px solid #4fa3d1;
      outline: none;
      transition: border-color 0.3s ease;
    }
    input:focus {
      border-color: #0077cc;
    }
    button {
      padding: 12px 25px;
      font-size: 20px;
      background-color: #4fa3d1;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      margin-top: 15px;
      transition: background-color 0.3s ease;
    }
    button:disabled {
      background-color: #ccc;
      cursor: not-allowed;
    }
    button:hover {
      background-color: #0077cc;
    }
    #timer {
      font-size: 20px;
      margin: 15px 0;
      font-weight: bold;
      color: #0077cc;
    }
    #progress {
      font-size: 20px;
      font-weight: bold;
      color: #4fa3d1;
      margin-bottom: 10px;
    }
    #results {
      margin-top: 20px;
      font-size: 20px;
      text-align: left;
      color: #333;
      max-height: 400px;
      overflow-y: auto;
      padding: 10px;
      border: 2px solid #4fa3d1;
      border-radius: 8px;
      background-color: #f7f7f7;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    }
    table {
      width: 100%;
      border-collapse: collapse;
      font-size: 16px;
    }
    th, td {
      padding: 8px;
      border: 1px solid #ccc;
      text-align: center;
    }
    th {
      background-color: #4fa3d1;
      color: white;
    }
    tr:nth-child(even) {
      background-color: #f2f2f2;
    }
    tr:hover {
      background-color: #d8e9ff;
    }
    #retryButton {
      padding: 10px 20px;
      font-size: 16px;
      background-color: #f45c42;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      margin-top: 10px;
    }
    #retryButton:hover {
      background-color: #e63946;
    }
    select {
      font-size: 18px;
      padding: 8px;
      margin: 10px 0;
      border-radius: 8px;
      border: 2px solid #4fa3d1;
    }
  </style>
</head>
<body>

<div class="container">
  <h1>暗算アプリ</h1>
  
  <div id="startScreen">
    <button id="startButton">スタート</button>
    <div>
      <label for="problemType">問題の種類:</label>
      <select id="problemType">
        <option value="addition">たし算</option>
        <option value="subtraction">ひき算</option>
        <option value="multiplication">かけ算</option>
        <option value="division">わり算</option>
      </select>
    </div>
    <div>
      <label for="difficulty">難易度:</label>
      <select id="difficulty">
        <option value="easy">簡単</option>
        <option value="medium">中級</option>
        <option value="hard">難しい</option>
      </select>
    </div>
    <div>
      <label for="problemCount">問題数:</label>
      <select id="problemCount">
        <option value="10">10問</option>
        <option value="50">50問</option>
        <option value="100">100問</option>
      </select>
    </div>
  </div>

  <div id="questionScreen" style="display: none;">
    <div id="progress">進行状況: 0問中0問正解</div>
    <div id="problem">問題が表示されます</div>
    <input type="text" id="answer" placeholder="答えを入力" />
    <div id="timer">タイマー: 0秒</div>
  </div>

  <div id="results" style="display: none;"></div>
  <button id="retryButton" style="display:none;" onclick="retryGame()">再挑戦</button>
</div>

<script>
  let problemIndex = 0;
  let correctAnswers = 0;
  let problems = [];
  let userAnswers = [];
  let timerInterval;
  let time = 0;

  const startButton = document.getElementById('startButton');
  const questionScreen = document.getElementById('questionScreen');
  const startScreen = document.getElementById('startScreen');
  const problemElement = document.getElementById('problem');
  const answerInput = document.getElementById('answer');
  const resultsDiv = document.getElementById('results');
  const retryButton = document.getElementById('retryButton');
  const progressText = document.getElementById('progress');
  const timerText = document.getElementById('timer');

  startButton.addEventListener('click', startGame);

  function startGame() {
    const problemType = document.getElementById('problemType').value;
    const difficulty = document.getElementById('difficulty').value;
    const problemCount = parseInt(document.getElementById('problemCount').value);

    problems = generateProblems(problemType, difficulty, problemCount);
    problemIndex = 0;
    correctAnswers = 0;
    userAnswers = [];
    time = 0;

    startScreen.style.display = 'none';
    questionScreen.style.display = 'block';
    progressText.style.display = 'block';
    resultsDiv.style.display = 'none';
    retryButton.style.display = 'none';

    updateProgress();
    nextProblem();

    timerInterval = setInterval(() => {
      time++;
      timerText.textContent = `タイマー: ${time}秒`;
    }, 1000);
  }

  function generateProblems(type, difficulty, count) {
    const ranges = {
      easy: { a: [1, 10], b: [1, 10] },
      medium: { a: [10, 50], b: [1, 9] },
      hard: { a: [1, 100], b: [1, 100] },
    };

    const problems = [];
    const range = ranges[difficulty];

    for (let i = 0; i < count; i++) {
      let a = randomInRange(...range.a);
      let b = randomInRange(...range.b);

      if (type === 'division') {
        a = a * b; // 割り切れる数に調整
      }

      let problem = {};
      switch (type) {
        case 'addition':
          problem = { question: `${a} + ${b}`, answer: a + b };
          break;
        case 'subtraction':
          if (a < b) [a, b] = [b, a]; // aがbより小さい場合、入れ替える
          problem = { question: `${a} - ${b}`, answer: a - b };
          break;
        case 'multiplication':
          problem = { question: `${a} × ${b}`, answer: a * b };
          break;
        case 'division':
          problem = { question: `${a} ÷ ${b}`, answer: a / b };
          break;
      }
      problems.push(problem);
    }

    return problems;
  }

  function randomInRange(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }

  function nextProblem() {
    if (problemIndex >= problems.length) {
      endGame();
      return;
    }
    const currentProblem = problems[problemIndex];
    problemElement.textContent = currentProblem.question;
    answerInput.value = '';
    answerInput.focus();
    answerInput.addEventListener('keydown', handleAnswer);
  }

  function handleAnswer(event) {
    if (event.key !== 'Enter') return;
    const userAnswer = parseInt(answerInput.value.replace(/[０-９]/g, (c) => String.fromCharCode(c.charCodeAt(0) - 65248)));
    const correctAnswer = problems[problemIndex].answer;

    userAnswers.push(userAnswer);

    if (userAnswer === correctAnswer) {
      correctAnswers++;
    }
    problemIndex++;
    updateProgress();
    nextProblem();
  }

  function updateProgress() {
    progressText.textContent = `進行状況: ${problems.length}問中${correctAnswers}問正解`;
  }

  function endGame() {
    clearInterval(timerInterval);
    questionScreen.style.display = 'none';
    resultsDiv.style.display = 'block';
    retryButton.style.display = 'block';

    let resultsHTML = '<table><tr><th>問題</th><th>入力した答え</th><th>正解の答え</th><th>正誤</th></tr>';
    problems.forEach((p, i) => {
      const userAnswer = userAnswers[i] || '';
      const isCorrect = userAnswer === p.answer;
      resultsHTML += `<tr><td>${p.question}</td><td>${userAnswer}</td><td>${p.answer}</td><td>${isCorrect ? '○' : '×'}</td></tr>`;
    });
    resultsHTML += `</table><p>正解数: ${correctAnswers}/${problems.length} 問</p><p>タイム: ${time} 秒</p>`;
    resultsDiv.innerHTML = resultsHTML;
  }

  function retryGame() {
    startScreen.style.display = 'block';
    questionScreen.style.display = 'none';
    resultsDiv.style.display = 'none';
    retryButton.style.display = 'none';
  }
</script>

</body>
</html>
