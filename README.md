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
.card{background:var(--card); border-radius:16px; padding:22px; margin-top:18px}
.hidden{display:none}
.row{display:flex; gap:10px; justify-content:center}
button{padding:12px 18px; border-radius:999px; cursor:pointer}
button.primary{background:linear-gradient(135deg, var(--accent), var(--accent-2)); border:none}
</style>
</head>
<body>
<div class="container">
<h1>Experimento de Percepción Auditiva</h1>

<div id="intro" class="card">
  <label>Edad<input type="number" id="edad"></label>
  <label>Conocimientos musicales
    <select id="musica">
      <option value="ninguno">Sin conocimientos</option>
      <option value="basico">Básico</option>
      <option value="medio">Medio</option>
      <option value="superior">Superior</option>
    </select>
  </label>
  <button class="primary" onclick="goCalibration()">Continuar</button>
</div>

<div id="calibration" class="card hidden">
  <button onclick="playCalibration()">Probar sonido</button>
  <button class="primary" onclick="startExperiment()">Comenzar</button>
</div>

<div id="experiment" class="card hidden">
  <div id="status"></div>
  <button onclick="playTrial()">Reproducir</button>
  <div id="question" class="hidden">
    <button onclick="answer(1)">Primero</button>
    <button onclick="answer(2)">Segundo</button>
    <button onclick="answer(0)">Iguales</button>
  </div>
</div>
</div>

<script>
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
const freqs = [100, 500, 2000];
const types = ["sine", "sawtooth"];
let trials = [];
let currentTrial = 0;

function generateTrials() {
  trials = [];

  types.forEach(type => {
    const magnitudes = [1,2,3,4,5];
    let steps = [];

    // cada magnitud x3 frecuencias
    magnitudes.forEach(m => { steps.push(m, m, m); });

    // mitad positivos, mitad negativos
    let half = Math.floor(steps.length/2);
    let signed = steps.map((m,i)=> i<half?m:-m);
    signed.sort(()=>Math.random()-0.5);

    // asignar a frecuencias sin mezclar tipos
    let idx = 0;
    freqs.forEach(freq => {
      for(let i=0;i<5;i++){
        trials.push({type:type,freq:freq,step:signed[idx++]});
      }
    });
  });

  // mezclar solo el orden de los pares, no los tipos dentro de cada par
  trials.sort(() => Math.random()-0.5);
}

function dbToGain(db){return Math.pow(10, db/20);}

function playTone(freq,type,gainValue,startTime){
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();
  osc.type = type;
  osc.frequency.value = freq;

  gain.gain.setValueAtTime(0, startTime);
  gain.gain.linearRampToValueAtTime(gainValue, startTime+0.05);
  gain.gain.setValueAtTime(gainValue, startTime+0.95);
  gain.gain.linearRampToValueAtTime(0, startTime+1);

  osc.connect(gain).connect(audioCtx.destination);
  osc.start(startTime);
  osc.stop(startTime+1);
}

function playCalibration(){
  playTone(500,"sine",dbToGain(-10),audioCtx.currentTime);
}

function playTrial(){
  const t = trials[currentTrial];
  const baseDb = -10;
  const baseGain = dbToGain(baseDb);
  const alteredGain = dbToGain(baseDb + t.step);

  const now = audioCtx.currentTime;

  // Primer sonido: siempre mismo nivel
  playTone(t.freq, t.type, baseGain, now);
  // Segundo sonido: solo este cambia
  playTone(t.freq, t.type, alteredGain, now+1.5);

  if(t.step>0) t.correct=2;
  else if(t.step<0) t.correct=1;
  else t.correct=0;

  document.getElementById("question").classList.remove("hidden");
}

function answer(c){
  currentTrial++;
  document.getElementById("question").classList.add("hidden");
  if(currentTrial>=trials.length){alert("Fin del experimento");}
}

function goCalibration(){
  document.getElementById("intro").classList.add("hidden");
  document.getElementById("calibration").classList.remove("hidden");
}

function startExperiment(){
  generateTrials();
  document.getElementById("calibration").classList.add("hidden");
  document.getElementById("experiment").classList.remove("hidden");
}
</script>
</body>
</html>
