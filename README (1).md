# formulario
formulario
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Formulário Interativo</title>
  <style>
    body {
      background-color: #87CEEB;
      font-family: Arial, sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
    }
    .form-container {
      text-align: center;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
      width: 90%;
      max-width: 400px;
    }
    .field {
      margin: 15px 0;
    }
    label {
      display: block;
      font-size: 16px;
      margin-bottom: 5px;
    }
    input[type="text"], input[type="email"], input[type="tel"], textarea {
      width: 100%;
      padding: 10px;
      font-size: 14px;
      margin-top: 5px;
    }
    button {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 16px;
      color: white;
      background-color: #007BFF;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    .finalizado {
      font-size: 24px;
      color: green;
      font-weight: bold;
    }
    canvas {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      pointer-events: none;
    }
  </style>
</head>
<body>
  <canvas id="fireworks"></canvas>
  <div class="form-container">
    <div id="form">
      <div class="field">
        <label for="name">Nome:</label>
        <input type="text" id="name" required>
      </div>
      <div class="field">
        <label for="phone">Telefone:</label>
        <input type="tel" id="phone" required>
      </div>
      <div class="field">
        <label for="email">E-mail:</label>
        <input type="email" id="email" required>
      </div>
      <div class="field" id="question-container">
        <label id="question">Você está bem?</label>
        <button onclick="selectAnswer('Sim')">Sim</button>
        <button onclick="selectAnswer('Não')">Não</button>
      </div>
    </div>
    <div id="final-container" style="display: none;">
      <label for="future">Como você se vê daqui a 5 anos?</label>
      <textarea id="future" rows="4" required></textarea>
      <button onclick="sendAnswers()">Enviar</button>
    </div>
    <div id="finalizado" class="finalizado" style="display: none;">FINALIZADO</div>
  </div>

  <script>
    const questions = [
      "Você está bem?",
      "Você dormiu bem?",
      "Está disposta hoje?"
    ];
    let answers = [];
    let currentQuestion = 0;

    function selectAnswer(answer) {
      answers.push({ question: questions[currentQuestion], answer: answer });
      currentQuestion++;
      if (currentQuestion < questions.length) {
        document.getElementById("question").innerText = questions[currentQuestion];
      } else {
        document.getElementById("question-container").style.display = "none";
        document.getElementById("final-container").style.display = "block";
      }
    }

    function sendAnswers() {
      const name = document.getElementById("name").value;
      const phone = document.getElementById("phone").value;
      const email = document.getElementById("email").value;
      const future = document.getElementById("future").value;

      if (!name || !phone || !email || !future) {
        alert("Por favor, preencha todos os campos.");
        return;
      }

      answers.push({ question: "Nome", answer: name });
      answers.push({ question: "Telefone", answer: phone });
      answers.push({ question: "E-mail", answer: email });
      answers.push({ question: "Como você se vê daqui a 5 anos?", answer: future });

      const emailTo = "chdsluna@gmail.com";
      const subject = "Respostas do Formulário";
      const body = answers.map(a => `${a.question}: ${a.answer}`).join("\n");
      const mailtoLink = `mailto:${emailTo}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`;

      window.location.href = mailtoLink;

      showFireworks();
      document.getElementById("form").style.display = "none";
      document.getElementById("final-container").style.display = "none";
      document.getElementById("finalizado").style.display = "block";
    }

    function showFireworks() {
      const canvas = document.getElementById('fireworks');
      const ctx = canvas.getContext('2d');
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;

      function random(min, max) {
        return Math.random() * (max - min) + min;
      }

      function Firework(x, y) {
        this.x = x;
        this.y = y;
        this.radius = random(2, 4);
        this.alpha = 1;
        this.decay = random(0.01, 0.03);
        this.dx = random(-2, 2);
        this.dy = random(-2, 2);
      }

      Firework.prototype.update = function () {
        this.x += this.dx;
        this.y += this.dy;
        this.alpha -= this.decay;
      };

      Firework.prototype.draw = function () {
        ctx.globalAlpha = this.alpha;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2);
        ctx.fillStyle = `rgb(${random(0, 255)}, ${random(0, 255)}, ${random(0, 255)})`;
        ctx.fill();
      };

      let fireworks = [];

      function animate() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        fireworks = fireworks.filter(f => f.alpha > 0);
        fireworks.forEach(f => {
          f.update();
          f.draw();
        });
        if (fireworks.length > 0) {
          requestAnimationFrame(animate);
        }
      }

      function launchFireworks() {
        for (let i = 0; i < 50; i++) {
          fireworks.push(new Firework(random(0, canvas.width), random(0, canvas.height)));
        }
        animate();
      }

      launchFireworks();
    }
  </script>
</body>
</html>
