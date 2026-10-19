<!DOCTYPE html>
<html lang="kk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Математикадан онлайн тест</title>
  <style>
    :root {
      --primary-color: #4f46e5;
      --primary-hover: #4338ca;
      --bg-color: #f3f4f6;
      --card-bg: #ffffff;
      --text-color: #1f2937;
      --border-color: #e5e7eb;
      --correct-color: #10b981;
      --wrong-color: #ef4444;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: var(--bg-color);
      color: var(--text-color);
      margin: 0;
      padding: 20px;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }

    .quiz-container {
      background-color: var(--card-bg);
      border-radius: 12px;
      box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
      width: 100%;
      max-width: 650px;
      padding: 30px;
      box-sizing: border-box;
    }

    h1 {
      text-align: center;
      color: var(--primary-color);
      margin-top: 0;
      font-size: 1.5rem;
    }

    .progress-bar {
      width: 100%;
      background-color: var(--border-color);
      height: 8px;
      border-radius: 4px;
      margin-bottom: 20px;
      overflow: hidden;
    }

    .progress {
      height: 100%;
      background-color: var(--primary-color);
      width: 0%;
      transition: width 0.3s ease;
    }

    .question-number {
      font-size: 0.9rem;
      color: #6b7280;
      font-weight: 600;
      margin-bottom: 8px;
    }

    .question-text {
      font-size: 1.15rem;
      font-weight: 600;
      margin-bottom: 20px;
      line-height: 1.4;
    }

    .formula-badge {
      display: inline-block;
      background-color: #e0e7ff;
      color: #3730a3;
      padding: 4px 8px;
      border-radius: 4px;
      font-family: 'Courier New', Courier, monospace;
      font-weight: bold;
    }

    .options-list {
      list-style: none;
      padding: 0;
      margin: 0 0 20px 0;
    }

    .option-item {
      border: 2px solid var(--border-color);
      border-radius: 8px;
      padding: 12px 16px;
      margin-bottom: 10px;
      cursor: pointer;
      transition: all 0.2s ease;
      display: flex;
      align-items: center;
    }

    .option-item:hover {
      border-color: var(--primary-color);
      background-color: #f8fafc;
    }

    .option-item.selected {
      border-color: var(--primary-color);
      background-color: #eef2ff;
    }

    .btn {
      display: block;
      width: 100%;
      padding: 12px;
      background-color: var(--primary-color);
      color: white;
      border: none;
      border-radius: 8px;
      font-size: 1rem;
      font-weight: 600;
      cursor: pointer;
      transition: background-color 0.2s ease;
    }

    .btn:hover {
      background-color: var(--primary-hover);
    }

    .btn:disabled {
      background-color: #9ca3af;
      cursor: not-allowed;
    }

    /* Результаты */
    .result-container {
      text-align: center;
      display: none;
    }

    .score-circle {
      width: 120px;
      height: 120px;
      border-radius: 50%;
      background-color: #e0e7ff;
      color: var(--primary-color);
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 2rem;
      font-weight: bold;
      margin: 20px auto;
    }

    .result-text {
      font-size: 1.2rem;
      margin-bottom: 20px;
    }

    .review-list {
      text-align: left;
      margin-top: 20px;
      max-height: 250px;
      overflow-y: auto;
      padding-right: 5px;
    }

    .review-item {
      padding: 10px;
      border-bottom: 1px solid var(--border-color);
      font-size: 0.9rem;
    }

    .review-item.correct {
      border-left: 4px solid var(--correct-color);
    }

    .review-item.wrong {
      border-left: 4px solid var(--wrong-color);
    }
  </style>
</head>
<body>

  <div class="quiz-container">
    <div id="quiz-screen">
      <h1>Жоғары математика: Тест</h1>
      <div class="progress-bar">
        <div class="progress" id="progress"></div>
      </div>
      <div class="question-number" id="question-number">Сұрақ 1 / 10</div>
      <div class="question-text" id="question-text"></div>
      <ul class="options-list" id="options-list"></ul>
      <button class="btn" id="next-btn" onclick="nextQuestion()" disabled>Келесі сұрақ</button>
    </div>

    <div id="result-screen" class="result-container">
      <h1>Тест аяқталды!</h1>
      <div class="score-circle" id="score-circle">0%</div>
      <p class="result-text" id="result-message"></p>
      
      <h3>Жауаптарды шолу:</h3>
      <div class="review-list" id="review-list"></div>

      <button class="btn" onclick="restartQuiz()" style="margin-top: 20px;">Тесті қайта өту</button>
    </div>
  </div>

  <script>
    // Массив вопросов на основе предоставленных таблиц
    const quizData = [
      {
        question: "Екінші ретті анықтауыш формуласы қайсысы?",
        options: [
          "det(A) = ad - bc",
          "A_ij = (-1)^(i+j) * M_ij",
          "r(A) = r(A^T)",
          "AX = B"
        ],
        correct: 0
      },
      {
        question: "A_ij = (-1)^(i+j) * M_ij формуласы нені білдіреді?",
        options: [
          "Кері матрицаны табу",
          "Алгебралық толықтауыш",
          "Матрица рангін табу",
          "Скалярлық көбейтінді"
        ],
        correct: 1
      },
      {
        question: "Крамер формуласы арқылы белгісізді табу формуласы қандай?",
        options: [
          "x_i = Δ_i / Δ",
          "A * A^-1 = E",
          "A + B = (a_ij + b_ij)",
          "y = kx + b"
        ],
        correct: 0
      },
      {
        question: "Нүктеден түзуге дейінгі қашықтық формуласы қайсысы?",
        options: [
          "d = |Ax_0 + By_0 + C| / √(A² + B²)",
          "cos φ = (a · b) / (|a| |b|)",
          "S = |a × b|",
          "lim sin(x)/x = 1"
        ],
        correct: 0
      },
      {
        question: "Векторлық көбейтінді модулі арқылы параллелограмм ауданын табу формуласы:",
        options: [
          "S = |a × b|",
          "S_Δ = 1/2 |a × b|",
          "V = |(a, b, c)|",
          "|a| = √(x² + y² + z²)"
        ],
        correct: 0
      },
      {
        question: "Кеңістіктегі жазықтықтың жалпы теңдеуі қандай?",
        options: [
          "Ax + By + Cz + D = 0",
          "x/a + y/b = 1",
          "y - y_1 = k(x - x_1)",
          "r(A) = r(A|B)"
        ],
        correct: 0
      },
      {
        question: "Бөлшектеп интегралдау формуласын көрсетіңіз:",
        options: [
          "∫ u dv = uv - ∫ v du",
          "∫ f(x)dx = F(b) - F(a)",
          "dy = f'(x)dx",
          "lim (1 + 1/x)^x = e"
        ],
        correct: 0
      },
      {
        question: "Екі вектордың ортогональдық (перпендикулярлық) шарты қандай?",
        options: [
          "a · b = 0",
          "a × b = 0",
          "r(A) = r(A^T)",
          "det(A) ≠ 0"
        ],
        correct: 0
      },
      {
        question: "Ньютон-Лейбниц формуласы қайсысы?",
        options: [
          "∫_a^b f(x)dx = F(b) - F(a)",
          "lim_(x→0) sin(x)/x = 1",
          "lim f/g = lim f'/g'",
          "f(-x) = -f(x)"
        ],
        correct: 0
      },
      {
        question: "Лопиталь ережесі қашан қолданылады (туынды арқылы шек табу)?",
        options: [
          "lim f/g = lim f'/g'",
          "dy/dx = y'_t / x'_t",
          "A = A_0 (1 + p/100)^t",
          "A_1/A_2 = B_1/B_2"
        ],
        correct: 0
      }
    ];

    let currentQuestion = 0;
    let score = 0;
    let selectedOption = null;
    let userAnswers = [];

    const questionNumberEl = document.getElementById('question-number');
    const questionTextEl = document.getElementById('question-text');
    const optionsListEl = document.getElementById('options-list');
    const nextBtn = document.getElementById('next-btn');
    const progressBar = document.getElementById('progress');

    function loadQuestion() {
      selectedOption = null;
      nextBtn.disabled = true;

      const currentQuiz = quizData[currentQuestion];
      
      questionNumberEl.textContent = `Сұрақ ${currentQuestion + 1} / ${quizData.length}`;
      questionTextEl.textContent = currentQuiz.question;
      
      // Обновляем шкалу прогресса
      progressBar.style.width = `${((currentQuestion) / quizData.length) * 100}%`;

      optionsListEl.innerHTML = '';
      currentQuiz.options.forEach((option, index) => {
        const li = document.createElement('li');
        li.className = 'option-item';
        li.textContent = option;
        li.onclick = () => selectOption(index, li);
        optionsListEl.appendChild(li);
      });
    }

    function selectOption(index, element) {
      const allOptions = optionsListEl.querySelectorAll('.option-item');
      allOptions.forEach(opt => opt.classList.remove('selected'));
      
      element.classList.add('selected');
      selectedOption = index;
      nextBtn.disabled = false;
    }

    function nextQuestion() {
      if (selectedOption === null) return;

      const isCorrect = selectedOption === quizData[currentQuestion].correct;
      if (isCorrect) {
        score++;
      }

      userAnswers.push({
        question: quizData[currentQuestion].question,
        userAnswer: quizData[currentQuestion].options[selectedOption],
        correctAnswer: quizData[currentQuestion].options[quizData[currentQuestion].correct],
        isCorrect: isCorrect
      });

      currentQuestion++;

      if (currentQuestion < quizData.length) {
        loadQuestion();
      } else {
        showResults();
      }
    }

    function showResults() {
      document.getElementById('quiz-screen').style.display = 'none';
      document.getElementById('result-screen').style.display = 'block';

      const percentage = Math.round((score / quizData.length) * 100);
      document.getElementById('score-circle').textContent = `${percentage}%`;

      let message = '';
      if (percentage >= 90) {
        message = 'Керемет! Сіз жоғары математика формулаларын өте жақсы білесіз!';
      } else if (percentage >= 60) {
        message = 'Жақсы нәтиже! Бірақ кейбір формулаларды қайталау керек.';
      } else {
        message = 'Формулаларды қайтадан мұқият оқып шығуды ұсынамыз.';
      }
      document.getElementById('result-message').textContent = message;

      // Вывод обзора ответов
      const reviewList = document.getElementById('review-list');
      reviewList.innerHTML = '';
      userAnswers.forEach((ans, idx) => {
        const div = document.createElement('div');
        div.className = `review-item ${ans.isCorrect ? 'correct' : 'wrong'}`;
        div.innerHTML = `
          <strong>${idx + 1}. ${ans.question}</strong><br>
          Сіздің жауабыңыз: ${ans.userAnswer} ${ans.isCorrect ? '✓' : '✗'}<br>
          ${!ans.isCorrect ? `<span style="color:#10b981;">Дұрыс жауап: ${ans.correctAnswer}</span>` : ''}
        `;
        reviewList.appendChild(div);
      });
    }

    function restartQuiz() {
      currentQuestion = 0;
      score = 0;
      userAnswers = [];
      document.getElementById('quiz-screen').style.display = 'block';
      document.getElementById('result-screen').style.display = 'none';
      loadQuestion();
    }

    // Запуск теста при загрузке страницы
    loadQuestion();
  </script>
</body>
</html>
