<div id="game" style="font-family: 'Poppins', sans-serif; max-width: 750px; margin: auto; text-align: center; background-color: #fffafc; padding: 25px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.15);">
  <p id="question" style="font-size: 1.1em; font-weight: 600; white-space: pre-line;"></p>

  <p id="lyric" style="font-size: 1.4em; font-weight: 700; margin-top: 10px; color:#333;"></p>

  <div id="options" style="margin-top: 15px;"></div>

  <textarea id="reflection" placeholder="Write your reflection here..." style="display: none; margin-top: 10px; width: 90%; height: 70px; padding: 8px; border-radius: 8px; border: 1px solid #aaa;"></textarea>
  <button id="submitReflection" style="display: none; margin-top: 10px; padding: 8px 20px; border: none; border-radius: 8px; background-color: #0078D7; color: white; cursor: pointer;">Submit</button>

  <p id="result" style="font-size: 1.1em; margin-top: 15px;"></p>
  <button onclick="nextQuestion()" id="nextBtn" style="margin-top: 20px; padding: 10px 20px; border: none; border-radius: 8px; background-color: #FF5A5F; color: white; cursor: pointer;">Next</button>
</div>

<script>
// ------------------------------
// TIMER
// ------------------------------
let startTime = Date.now(); // Inicia en cuanto carga el juego

// ------------------------------
// PREGUNTAS
// ------------------------------
const questions = [
  {
    type: "info",
    question: "✨ Welcome to *Guess the Disney Movie from the Song!* \nRead the lyric and choose which movie it comes from."
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"Let it go, let it go\"",
    options: ["Moana", "Frozen", "Tangled", "Encanto"],
    correct: 1
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"A whole new world\"",
    options: ["Aladdin", "The Lion King", "Mulan", "Beauty and the Beast"],
    correct: 0
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"Hakuna Matata\"",
    options: ["Finding Nemo", "Tarzan", "Coco", "The Lion King"],
    correct: 3
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"Under the sea\"",
    options: [ "Frozen", "Moana","The Little Mermaid", "Luca"],
    correct: 2
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"Remember me, though I have to say goodbye\"",
    options: ["Coco", "Raya and the Last Dragon", "Brave", "Big Hero 6"],
    correct: 0
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"You'll be in my heart, always\"",
    options: ["Tarzan", "Hercules", "Up", "Toy Story"],
    correct: 0
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"I can go the distance\"",
    options: ["Mulan", "Hercules", "Pocahontas", "Encanto"],
    correct: 1
  },
  {
    type: "lyricChoice",
    question: "🎶 From which movie is this song?",
    lyric: "\"When you wish upon a star\"",
    options: ["Pinocchio", "Sleeping Beauty", "Snow White", "Cinderella"],
    correct: 0
  },
  {
    type: "reflect",
    question: "💫 Final reflection: What is *your* favorite Disney song, and why do you think it connects with people so deeply?"
  }
];

let index = 0;
let score = 0;

// ------------------------------
// MOSTRAR PREGUNTA
// ------------------------------
function showQuestion() {
  const q = questions[index];
  const qEl = document.getElementById("question");
  const lyricEl = document.getElementById("lyric");
  const optionsDiv = document.getElementById("options");
  const reflection = document.getElementById("reflection");
  const submitRef = document.getElementById("submitReflection");
  const result = document.getElementById("result");

  qEl.innerText = q.question;
  lyricEl.innerText = q.lyric ? q.lyric : "";
  optionsDiv.innerHTML = "";
  reflection.style.display = "none";
  submitRef.style.display = "none";
  result.innerText = "";
  result.style.color = "black";

  if (q.type === "info") {
    result.innerHTML = "💡 Click 'Next' to continue!";
  }

  if (q.type === "lyricChoice") {
    q.options.forEach((op, i) => {
      const btn = document.createElement("button");
      btn.innerText = op;
      btn.style.margin = "8px";
      btn.style.padding = "8px 15px";
      btn.style.border = "1px solid #888";
      btn.style.borderRadius = "8px";
      btn.style.backgroundColor = "#f9f9f9";
      btn.style.cursor = "pointer";
      btn.onclick = () => checkAnswer(i);
      optionsDiv.appendChild(btn);
    });
  }

  if (q.type === "reflect") {
    reflection.style.display = "block";
    submitRef.style.display = "inline-block";
    submitRef.onclick = () => {
      const text = reflection.value.trim();
      if (text.length > 5) {
        result.innerHTML = "💭 Nice! Your reflection was saved.";
        result.style.color = "#009688";
      } else {
        result.innerHTML = "✏️ Try to write a bit more.";
        result.style.color = "red";
      }
    };
  }
}

function checkAnswer(i) {
  const q = questions[index];
  const result = document.getElementById("result");

  if (i === q.correct) {
    result.innerHTML = "🎉 Correct!";
    result.style.color = "green";
    score++;
  } else {
    result.innerHTML = "❌ Not quite! The right answer was: " + q.options[q.correct];
    result.style.color = "red";
  }

  Array.from(document.getElementById("options").children).forEach(b => b.disabled = true);
}

// ------------------------------
// SIGUIENTE PREGUNTA
// ------------------------------
function nextQuestion() {
  index++;

  if (index < questions.length) {
    showQuestion();
  } else {

    // CALCULAR TIEMPO TOTAL
    let endTime = Date.now();
    let elapsedMs = endTime - startTime;
    let seconds = Math.round(elapsedMs / 1000);
    let minutes = (seconds / 60).toFixed(1);

    document.getElementById("game").innerHTML = `
      <h2>🎵 You finished the Disney Lyrics Quiz!</h2>
      <p style="font-size:1.2em;">Your score: ${score} / ${questions.length - 2}</p>

      <h3>⏱️ Total time:</h3>
      <p style="font-size:1.3em; font-weight: bold;">
        ${seconds} seconds (${minutes} minutes)
      </p>

      <p>✨ Music makes the stories unforgettable. Thanks for playing!</p>
    `;
  }
}

showQuestion();
</script>
