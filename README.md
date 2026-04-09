<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Experimento de Percepción Auditiva</title>
<style>
:root{
  --bg:#0f1115;
  --card:#171a21;
  --text:#e8ecf1;
  --muted:#aab2bf;
  --accent:#6ee7b7;
  --accent-2:#60a5fa;
  --border:#242938;
}
*{box-sizing:border-box}
body{
  margin:0; font-family: Helvetica, Arial, sans-serif; background:var(--bg); color:var(--text);
  display:flex; justify-content:center; padding:24px;
}
.container{width:100%; max-width:720px}
h1{font-weight:600; letter-spacing:.3px}
h2{font-weight:600}
p{color:var(--muted)}
.card{
  background:var(--card); border:1px solid var(--border); border-radius:16px; padding:22px; margin-top:18px;
  box-shadow: 0 10px 30px rgba(0,0,0,.35);
}
label{display:block; margin:10px 0; color:var(--muted)}
input, select{
  width:100%; padding:12px 14px; margin-top:6px; border-radius:12px; border:1px solid var(--border);
  background:#0c0f14; color:var(--text); outline:none;
}
input:focus, select:focus{border-color:var(--accent-2)}
.row{display:flex; gap:10px; flex-wrap:wrap; justify-content:center}
button{
  border:none; border-radius:999px; padding:12px 18px; font-size:15px; cursor:pointer;
  background:#1f2430; color:var(--text); border:1px solid var(--border);
  transition: transform .05s ease, box-shadow .15s ease, background .15s ease;
}
button.primary{background:linear-gradient(135deg, var(--accent), var(--accent-2)); color:#08120f; border:none}
button:hover{transform: translateY(-1px); box-shadow:0 8px 18px rgba(0,0,0,.35)}
button:active{transform: translateY(0)}
.hidden{display:none}
.bar{height:10px; background:#0c0f14; border-radius:999px; overflow:hidden; border:1px solid var(--border); margin:12px 0 16px}
.progress{height:100%; width:0%; background:linear-gradient(90deg, var(--accent), var(--accent-2))}
.status{font-size:14px; color:var(--muted)}
.question{font-size:18px; margin-top:10px}
.center{display:flex; justify-content:center; align-items:center}
</style>
</head>
<body>
<div class="container">
<h1>Experimento de Percepción Auditiva</h1>

<div id="intro" class="card">
  <p>Escucharás pares de sonidos y deberás indicar cuál suena más fuerte o si no percibes diferencia.</p>

  <label>Edad
    <input type="number" id="edad" placeholder="Introduce tu edad" />
  </label>

  <label>Conocimientos musicales
    <select id="musica">
      <option value="ninguno">Sin conocimientos</option>
      <option value="basico">Estudios básicos</option>
      <option value="medio">Estudios medios</option>
      <option value="superior">Estudios superiores</option>
    </select>
  </label>

  <div class="center">
    <button class="primary" onclick="goCalibration()">Continuar</button>
  </div>
</div>

<div id="calibration" class="card hidden">
  <h2>Calibración</h2>
  <p>Ajusta el volumen hasta que el sonido sea cómodo (ni muy bajo ni molesto).</p>
  <div class="row">
    <button onclick="playCalibration()">Reproducir sonido</button>
    <button class="primary" onclick="startExperiment()">Comenzar experimento</button>
  </div>
</div>

<div id="experiment" class="card hidden">
  <div class="status" id="status"></div>
  <div class="bar"><div class="progress" id="progress"></div></div>

  <div class="center">
    <button onclick="playTrial()">Reproducir sonidos</button>
  </div>

  <div id="question" class="hidden">
    <p class="question">¿Cuál suena más fuerte?</p>
    <div class="row">
      <button onclick="answer(1)">Primero</button>
      <button onclick="answer(2)">Segundo</button>
      <button onclick="answer(0)">No hay diferencia</button>
    </div>
  </div>
</div>
</div>

<script>
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
const freqs = [100, 500, 2000];
const types = ["sine", "sawtooth"];
let trials = [];
let currentTrial = 0;
let results = [];
let userData = {};

// Generar trials: cubrir todos los intervalos de 0.5dB hasta 5dB, tanto aumentos como disminuciones, únicos por tipo de onda
function generateTrials() {
  trials = [];
  types.forEach(type => {
    const steps = [];
    for (let i = 1; i <= 10; i++) { // 5 aumentos y 5 disminuciones
      steps.push(0.5 * i); // aumento
      steps.push(-0.5 * i); // disminución
    }
    for (const freq of freqs) {
      steps.forEach(step => {
        trials.push({ type, freq, step });
      });
      trials.push({ type, freq, step: 0 }); // sin diferencia
    }
  });
  trials.sort(() => Math.random() - 0.5);
  trials = trials.slice(0, 30);
}

function dbToGain(db) { return Math.pow(10, db / 20); }

function playTone(freq, type, gainValue, startTime) {
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();
  osc.type = type;
  osc.frequency.value = freq;
  gain.gain.value = gainValue;
  osc.connect(gain).connect(audioCtx.destination);
  osc.start(startTime);
  osc.stop(startTime + 1);
}

function playCalibration() {
  const now = audioCtx.currentTime;
  playTone(500, "sine", dbToGain(0), now);
}

function playTrial() {
  const trial = trials[currentTrial];
  const baseGain = dbToGain(0);
  const alteredGain = dbToGain(trial.step);
  const firstIsAltered = Math.random() > 0.5;
  const now = audioCtx.currentTime;

  if (trial.step === 0) {
    playTone(trial.freq, trial.type, baseGain, now);
    playTone(trial.freq, trial.type, baseGain, now + 1.5);
    trial.correct = 0;
  } else if (firstIsAltered) {
    playTone(trial.freq, trial.type, alteredGain, now);
    playTone(trial.freq, trial.type, baseGain, now + 1.5);
    trial.correct = 1;
  } else {
    playTone(trial.freq, trial.type, baseGain, now);
    playTone(trial.freq, trial.type, alteredGain, now + 1.5);
    trial.correct = 2;
  }

  document.getElementById("question").classList.remove("hidden");
}

function answer(choice) {
  const trial = trials[currentTrial];
  results.push({ ...trial, choice, correct: trial.correct === choice });

  currentTrial++;
  document.getElementById("question").classList.add("hidden");
  updateProgress();

  if (currentTrial >= trials.length) {
    sendData();
  } else {
    document.getElementById("status").innerText = `Prueba ${currentTrial + 1} de ${trials.length}`;
  }
}

function updateProgress() {
  const percent = (currentTrial / trials.length) * 100;
  document.getElementById("progress").style.width = percent + "%";
}

function goCalibration() {
  userData.edad = document.getElementById("edad").value;
  userData.musica = document.getElementById("musica").value;
  document.getElementById("intro").classList.add("hidden");
  document.getElementById("calibration").classList.remove("hidden");
}

function startExperiment() {
  generateTrials();
  document.getElementById("calibration").classList.add("hidden");
  document.getElementById("experiment").classList.remove("hidden");
  document.getElementById("status").innerText = `Prueba 1 de ${trials.length}`;
  updateProgress();
}

async function sendData() {
  const payload = {...userData, results};
  const url = "https://script.google.com/macros/s/AKfycbzFYWXOcdlOmPH8wM65zGUu8tZ0ehv8V8dy00CTctOFjsO1RQa_pHxeJL1rrwvjnaQz_Q/exec";
  try {
    await fetch(url, { method:"POST", body: JSON.stringify(payload), headers: {"Content-Type":"application/json"} });
  } catch(e) { console.error("Error enviando datos", e); }

  document.body.innerHTML = '<div class="container"><div class="card"><h2>Gracias por participar</h2></div></div>';
}
</script>
</body>
</html>
