<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Experimento de Percepción Auditiva</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
            min-height: 100vh;
            color: #e8e8e8;
            line-height: 1.6;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 40px 20px;
        }

        header {
            text-align: center;
            margin-bottom: 40px;
        }

        h1 {
            font-size: 2rem;
            font-weight: 600;
            margin-bottom: 10px;
            color: #fff;
        }

        .subtitle {
            color: #a0a0a0;
            font-size: 1rem;
        }

        .card {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 16px;
            padding: 30px;
            margin-bottom: 24px;
            backdrop-filter: blur(10px);
        }

        .card h2 {
            font-size: 1.25rem;
            margin-bottom: 20px;
            color: #fff;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .card h2 .step {
            background: #4a9eff;
            color: #fff;
            width: 28px;
            height: 28px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.9rem;
        }

        .info-box {
            background: rgba(74, 158, 255, 0.1);
            border-left: 3px solid #4a9eff;
            padding: 16px 20px;
            border-radius: 0 8px 8px 0;
            margin-bottom: 24px;
        }

        .info-box p {
            margin-bottom: 8px;
        }

        .info-box p:last-child {
            margin-bottom: 0;
        }

        .form-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            color: #d0d0d0;
        }

        input[type="number"],
        select {
            width: 100%;
            padding: 12px 16px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 8px;
            background: rgba(255, 255, 255, 0.05);
            color: #fff;
            font-size: 1rem;
            transition: border-color 0.2s, background 0.2s;
        }

        input[type="number"]:focus,
        select:focus {
            outline: none;
            border-color: #4a9eff;
            background: rgba(255, 255, 255, 0.08);
        }

        select option {
            background: #1a1a2e;
            color: #fff;
        }

        .btn {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            padding: 14px 28px;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: 500;
            cursor: pointer;
            transition: all 0.2s;
        }

        .btn-primary {
            background: #4a9eff;
            color: #fff;
            width: 100%;
        }

        .btn-primary:hover {
            background: #3a8eef;
            transform: translateY(-1px);
        }

        .btn-primary:disabled {
            background: #3a5a7a;
            cursor: not-allowed;
            transform: none;
        }

        .btn-sound {
            background: rgba(255, 255, 255, 0.1);
            color: #fff;
            border: 1px solid rgba(255, 255, 255, 0.2);
            padding: 12px 20px;
            min-width: 80px;
        }

        .btn-sound:hover {
            background: rgba(255, 255, 255, 0.15);
        }

        .btn-sound.playing {
            background: #4a9eff;
            border-color: #4a9eff;
        }

        /* Trial section */
        .trial-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 24px;
            flex-wrap: wrap;
            gap: 10px;
        }

        .trial-counter {
            font-size: 0.9rem;
            color: #a0a0a0;
        }

        .wave-type {
            background: rgba(74, 158, 255, 0.2);
            color: #4a9eff;
            padding: 6px 12px;
            border-radius: 20px;
            font-size: 0.85rem;
            font-weight: 500;
        }

        .progress-bar {
            width: 100%;
            height: 6px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 3px;
            overflow: hidden;
            margin-bottom: 30px;
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #4a9eff, #7b68ee);
            border-radius: 3px;
            transition: width 0.3s ease;
        }

        .sounds-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 12px;
            margin-bottom: 24px;
        }

        .sound-option {
            position: relative;
        }

        .sound-option input {
            position: absolute;
            opacity: 0;
            pointer-events: none;
        }

        .sound-option label {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
            padding: 20px 16px;
            background: rgba(255, 255, 255, 0.05);
            border: 2px solid rgba(255, 255, 255, 0.1);
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.2s;
            margin: 0;
        }

        .sound-option label:hover {
            background: rgba(255, 255, 255, 0.08);
            border-color: rgba(255, 255, 255, 0.2);
        }

        .sound-option input:checked + label {
            background: rgba(74, 158, 255, 0.15);
            border-color: #4a9eff;
        }

        .sound-label {
            font-size: 1.1rem;
            font-weight: 600;
            color: #fff;
        }

        .play-icon {
            width: 48px;
            height: 48px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
        }

        .sound-option label:hover .play-icon {
            background: rgba(255, 255, 255, 0.15);
        }

        .sound-option.playing .play-icon {
            background: #4a9eff;
            animation: pulse 1s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        .play-icon svg {
            width: 20px;
            height: 20px;
            fill: #fff;
        }

        .instructions {
            text-align: center;
            color: #a0a0a0;
            font-size: 0.95rem;
            margin-bottom: 20px;
        }

        /* Results section */
        .results-summary {
            text-align: center;
        }

        .results-icon {
            width: 80px;
            height: 80px;
            background: linear-gradient(135deg, #4a9eff, #7b68ee);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 auto 24px;
        }

        .results-icon svg {
            width: 40px;
            height: 40px;
            fill: #fff;
        }

        .results-message {
            font-size: 1.5rem;
            font-weight: 600;
            margin-bottom: 16px;
            color: #fff;
        }

        .results-detail {
            color: #a0a0a0;
            margin-bottom: 30px;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 16px;
            margin-bottom: 30px;
        }

        .stat-card {
            background: rgba(255, 255, 255, 0.05);
            padding: 20px;
            border-radius: 12px;
            text-align: center;
        }

        .stat-value {
            font-size: 2rem;
            font-weight: 700;
            color: #4a9eff;
            margin-bottom: 4px;
        }

        .stat-label {
            font-size: 0.85rem;
            color: #a0a0a0;
        }

        .hidden {
            display: none !important;
        }

        /* Responsive */
        @media (max-width: 600px) {
            .container {
                padding: 24px 16px;
            }

            h1 {
                font-size: 1.5rem;
            }

            .card {
                padding: 20px;
            }

            .sounds-grid {
                grid-template-columns: repeat(3, 1fr);
                gap: 8px;
            }

            .sound-option label {
                padding: 14px 10px;
            }

            .play-icon {
                width: 40px;
                height: 40px;
            }

            .stats-grid {
                grid-template-columns: 1fr;
            }
        }

        /* Volume warning */
        .volume-warning {
            display: flex;
            align-items: center;
            gap: 12px;
            background: rgba(255, 193, 7, 0.1);
            border: 1px solid rgba(255, 193, 7, 0.3);
            padding: 12px 16px;
            border-radius: 8px;
            margin-bottom: 20px;
            font-size: 0.9rem;
            color: #ffc107;
        }

        .volume-warning svg {
            width: 20px;
            height: 20px;
            fill: #ffc107;
            flex-shrink: 0;
        }

        /* Confidence display during experiment */
        .confidence-section {
            margin-top: 16px;
            padding-top: 16px;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
        }

        .confidence-label {
            font-size: 0.9rem;
            color: #a0a0a0;
            margin-bottom: 10px;
        }

        .confidence-options {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
        }

        .confidence-options label {
            flex: 1;
            min-width: 80px;
            padding: 10px;
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 6px;
            text-align: center;
            cursor: pointer;
            transition: all 0.2s;
            font-size: 0.85rem;
            margin: 0;
        }

        .confidence-options input {
            display: none;
        }

        .confidence-options input:checked + span {
            color: #4a9eff;
        }

        .confidence-options label:has(input:checked) {
            background: rgba(74, 158, 255, 0.15);
            border-color: #4a9eff;
        }

        .loading-spinner {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border-top-color: #fff;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>Experimento de Percepción Auditiva</h1>
            <p class="subtitle">Variación de amplitud en ondas sonoras</p>
        </header>

        <!-- Sección de bienvenida -->
        <section id="welcome-section">
            <div class="card">
                <h2><span class="step">i</span> Información del experimento</h2>
                
                <div class="info-box">
                    <p><strong>Objetivo:</strong> Evaluar la capacidad de percibir variaciones de amplitud (volumen) en diferentes tipos de ondas sonoras.</p>
                    <p><strong>Duración estimada:</strong> 5-8 minutos</p>
                    <p><strong>Anonimato:</strong> Este experimento es completamente anónimo. Solo se registran la edad y el nivel de conocimientos musicales.</p>
                </div>

                <div class="volume-warning">
                    <svg viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/></svg>
                    <span>Usa auriculares y ajusta el volumen a un nivel cómodo antes de comenzar.</span>
                </div>

                <h2><span class="step">1</span> Datos del participante</h2>
                
                <div class="form-group">
                    <label for="age">Edad</label>
                    <input type="number" id="age" min="10" max="100" placeholder="Introduce tu edad">
                </div>

                <div class="form-group">
                    <label for="musical-knowledge">Conocimientos musicales</label>
                    <select id="musical-knowledge">
                        <option value="">Selecciona una opción</option>
                        <option value="none">Sin conocimientos musicales</option>
                        <option value="basic">Básicos (escucha música regularmente)</option>
                        <option value="amateur">Amateur (toca algún instrumento / canta)</option>
                        <option value="intermediate">Intermedios (estudios musicales informales)</option>
                        <option value="advanced">Avanzados (estudios en conservatorio / profesional)</option>
                    </select>
                </div>

                <button class="btn btn-primary" id="start-btn" disabled>
                    Comenzar experimento
                </button>
            </div>

            <div class="card">
                <h2><span class="step">?</span> ¿Cómo funciona?</h2>
                <p style="margin-bottom: 12px;">En cada prueba escucharás <strong>3 sonidos</strong>. Uno de ellos tendrá una variación de amplitud (cambio gradual de volumen). Los otros dos serán constantes.</p>
                <p style="margin-bottom: 12px;">Tu tarea es <strong>identificar cuál de los tres sonidos</strong> presenta la variación.</p>
                <p>Escucharás tanto ondas <strong>sinusoidales</strong> (sonido puro) como ondas de <strong>diente de sierra</strong> (sonido más rico en armónicos).</p>
            </div>
        </section>

        <!-- Sección de pruebas -->
        <section id="trial-section" class="hidden">
            <div class="card">
                <div class="trial-header">
                    <span class="trial-counter">Prueba <span id="current-trial">1</span> de <span id="total-trials">12</span></span>
                    <span class="wave-type" id="wave-type-badge">Onda sinusoidal</span>
                </div>

                <div class="progress-bar">
                    <div class="progress-fill" id="progress-fill" style="width: 0%"></div>
                </div>

                <p class="instructions">Escucha los tres sonidos y selecciona el que presenta variación de amplitud</p>

                <div class="sounds-grid" id="sounds-grid">
                    <div class="sound-option" data-sound="A">
                        <input type="radio" name="sound-choice" id="sound-a" value="A">
                        <label for="sound-a">
                            <div class="play-icon">
                                <svg viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
                            </div>
                            <span class="sound-label">A</span>
                        </label>
                    </div>
                    <div class="sound-option" data-sound="B">
                        <input type="radio" name="sound-choice" id="sound-b" value="B">
                        <label for="sound-b">
                            <div class="play-icon">
                                <svg viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
                            </div>
                            <span class="sound-label">B</span>
                        </label>
                    </div>
                    <div class="sound-option" data-sound="C">
                        <input type="radio" name="sound-choice" id="sound-c" value="C">
                        <label for="sound-c">
                            <div class="play-icon">
                                <svg viewBox="0 0 24 24"><path d="M8 5v14l11-7z"/></svg>
                            </div>
                            <span class="sound-label">C</span>
                        </label>
                    </div>
                </div>

                <div class="confidence-section">
                    <p class="confidence-label">¿Qué tan seguro/a estás de tu respuesta?</p>
                    <div class="confidence-options">
                        <label>
                            <input type="radio" name="confidence" value="1">
                            <span>Adivinando</span>
                        </label>
                        <label>
                            <input type="radio" name="confidence" value="2">
                            <span>Poco seguro</span>
                        </label>
                        <label>
                            <input type="radio" name="confidence" value="3">
                            <span>Bastante seguro</span>
                        </label>
                        <label>
                            <input type="radio" name="confidence" value="4">
                            <span>Muy seguro</span>
                        </label>
                    </div>
                </div>

                <button class="btn btn-primary" id="next-btn" disabled style="margin-top: 24px;">
                    Siguiente prueba
                </button>
            </div>
        </section>

        <!-- Sección de resultados -->
        <section id="results-section" class="hidden">
            <div class="card results-summary">
                <div class="results-icon">
                    <svg viewBox="0 0 24 24"><path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/></svg>
                </div>
                <h2 class="results-message">¡Experimento completado!</h2>
                <p class="results-detail">Gracias por tu participación. Tus respuestas han sido registradas de forma anónima.</p>

                <div class="stats-grid">
                    <div class="stat-card">
                        <div class="stat-value" id="correct-count">0</div>
                        <div class="stat-label">Respuestas correctas</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value" id="accuracy-percent">0%</div>
                        <div class="stat-label">Precisión total</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value" id="sine-accuracy">0%</div>
                        <div class="stat-label">Precisión (sinusoidal)</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value" id="saw-accuracy">0%</div>
                        <div class="stat-label">Precisión (diente de sierra)</div>
                    </div>
                </div>

                <button class="btn btn-primary" id="download-btn">
                    Descargar mis resultados (JSON)
                </button>
            </div>
        </section>
    </div>

    <script>
        // ========== Audio Context Setup ==========
        let audioContext = null;
        let currentOscillator = null;
        let currentGain = null;

        function initAudioContext() {
            if (!audioContext) {
                audioContext = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioContext.state === 'suspended') {
                audioContext.resume();
            }
        }

        // ========== Experiment Configuration ==========
        const CONFIG = {
            totalTrials: 12,
            trialsPerWaveType: 6,
            soundDuration: 2.5, // seconds
            baseFrequency: 440, // Hz (A4)
            frequencies: [220, 330, 440, 523, 659, 784], // Various musical frequencies
            baseVolume: 0.3,
            modulationDepth: 0.15, // How much the amplitude varies
            modulationFrequency: 2 // Hz - speed of amplitude variation
        };

        // ========== Experiment State ==========
        let state = {
            participantAge: null,
            musicalKnowledge: null,
            currentTrial: 0,
            trials: [],
            responses: [],
            experimentStartTime: null,
            currentTrialData: null
        };

        // ========== Generate Trial Structure ==========
        function generateTrials() {
            const trials = [];
            const waveTypes = ['sine', 'sawtooth'];
            
            // Create balanced trials
            for (let waveType of waveTypes) {
                for (let i = 0; i < CONFIG.trialsPerWaveType; i++) {
                    const targetPosition = Math.floor(Math.random() * 3); // 0, 1, or 2 (A, B, C)
                    const frequency = CONFIG.frequencies[Math.floor(Math.random() * CONFIG.frequencies.length)];
                    
                    trials.push({
                        waveType: waveType,
                        targetPosition: targetPosition,
                        frequency: frequency,
                        trialNumber: trials.length + 1
                    });
                }
            }
            
            // Shuffle trials
            for (let i = trials.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [trials[i], trials[j]] = [trials[j], trials[i]];
            }
            
            // Renumber after shuffle
            trials.forEach((trial, index) => {
                trial.trialNumber = index + 1;
            });
            
            return trials;
        }

        // ========== Sound Generation ==========
        function playSound(waveType, frequency, hasModulation, duration = CONFIG.soundDuration) {
            initAudioContext();
            stopCurrentSound();

            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            
            oscillator.type = waveType;
            oscillator.frequency.setValueAtTime(frequency, audioContext.currentTime);
            
            // Set base volume
            gainNode.gain.setValueAtTime(CONFIG.baseVolume, audioContext.currentTime);
            
            if (hasModulation) {
                // Create amplitude modulation (tremolo effect)
                const lfo = audioContext.createOscillator();
                const lfoGain = audioContext.createGain();
                
                lfo.type = 'sine';
                lfo.frequency.setValueAtTime(CONFIG.modulationFrequency, audioContext.currentTime);
                lfoGain.gain.setValueAtTime(CONFIG.modulationDepth, audioContext.currentTime);
                
                lfo.connect(lfoGain);
                lfoGain.connect(gainNode.gain);
                lfo.start(audioContext.currentTime);
                lfo.stop(audioContext.currentTime + duration);
            }
            
            // Fade in/out to avoid clicks
            gainNode.gain.setValueAtTime(0, audioContext.currentTime);
            gainNode.gain.linearRampToValueAtTime(CONFIG.baseVolume, audioContext.currentTime + 0.05);
            gainNode.gain.setValueAtTime(CONFIG.baseVolume, audioContext.currentTime + duration - 0.05);
            gainNode.gain.linearRampToValueAtTime(0, audioContext.currentTime + duration);
            
            oscillator.connect(gainNode);
            gainNode.connect(audioContext.destination);
            
            oscillator.start(audioContext.currentTime);
            oscillator.stop(audioContext.currentTime + duration);
            
            currentOscillator = oscillator;
            currentGain = gainNode;
            
            return new Promise(resolve => {
                oscillator.onended = resolve;
            });
        }

        function stopCurrentSound() {
            if (currentOscillator) {
                try {
                    currentOscillator.stop();
                } catch (e) {}
                currentOscillator = null;
            }
        }

        // ========== UI Elements ==========
        const elements = {
            welcomeSection: document.getElementById('welcome-section'),
            trialSection: document.getElementById('trial-section'),
            resultsSection: document.getElementById('results-section'),
            startBtn: document.getElementById('start-btn'),
            nextBtn: document.getElementById('next-btn'),
            downloadBtn: document.getElementById('download-btn'),
            ageInput: document.getElementById('age'),
            musicalKnowledgeSelect: document.getElementById('musical-knowledge'),
            currentTrialSpan: document.getElementById('current-trial'),
            totalTrialsSpan: document.getElementById('total-trials'),
            waveTypeBadge: document.getElementById('wave-type-badge'),
            progressFill: document.getElementById('progress-fill'),
            soundsGrid: document.getElementById('sounds-grid'),
            correctCount: document.getElementById('correct-count'),
            accuracyPercent: document.getElementById('accuracy-percent'),
            sineAccuracy: document.getElementById('sine-accuracy'),
            sawAccuracy: document.getElementById('saw-accuracy')
        };

        // ========== Event Listeners ==========
        function validateForm() {
            const age = elements.ageInput.value;
            const knowledge = elements.musicalKnowledgeSelect.value;
            elements.startBtn.disabled = !(age && age >= 10 && age <= 100 && knowledge);
        }

        elements.ageInput.addEventListener('input', validateForm);
        elements.musicalKnowledgeSelect.addEventListener('change', validateForm);

        elements.startBtn.addEventListener('click', startExperiment);
        elements.nextBtn.addEventListener('click', nextTrial);
        elements.downloadBtn.addEventListener('click', downloadResults);

        // Sound option clicks
        document.querySelectorAll('.sound-option').forEach(option => {
            option.addEventListener('click', async (e) => {
                const soundId = option.dataset.sound;
                await playSoundOption(soundId);
            });
        });

        // Radio and confidence change handlers
        document.querySelectorAll('input[name="sound-choice"]').forEach(radio => {
            radio.addEventListener('change', validateTrialResponse);
        });
        document.querySelectorAll('input[name="confidence"]').forEach(radio => {
            radio.addEventListener('change', validateTrialResponse);
        });

        function validateTrialResponse() {
            const soundChoice = document.querySelector('input[name="sound-choice"]:checked');
            const confidence = document.querySelector('input[name="confidence"]:checked');
            elements.nextBtn.disabled = !(soundChoice && confidence);
        }

        // ========== Experiment Flow ==========
        function startExperiment() {
            state.participantAge = parseInt(elements.ageInput.value);
            state.musicalKnowledge = elements.musicalKnowledgeSelect.value;
            state.experimentStartTime = new Date().toISOString();
            state.trials = generateTrials();
            state.currentTrial = 0;
            state.responses = [];

            elements.totalTrialsSpan.textContent = CONFIG.totalTrials;
            
            elements.welcomeSection.classList.add('hidden');
            elements.trialSection.classList.remove('hidden');
            
            loadTrial();
        }

        function loadTrial() {
            const trial = state.trials[state.currentTrial];
            state.currentTrialData = {
                ...trial,
                startTime: Date.now(),
                soundsPlayed: { A: 0, B: 0, C: 0 }
            };

            // Update UI
            elements.currentTrialSpan.textContent = trial.trialNumber;
            elements.waveTypeBadge.textContent = trial.waveType === 'sine' ? 'Onda sinusoidal' : 'Onda diente de sierra';
            
            const progress = (state.currentTrial / CONFIG.totalTrials) * 100;
            elements.progressFill.style.width = `${progress}%`;

            // Reset selections
            document.querySelectorAll('input[name="sound-choice"]').forEach(r => r.checked = false);
            document.querySelectorAll('input[name="confidence"]').forEach(r => r.checked = false);
            document.querySelectorAll('.sound-option').forEach(o => o.classList.remove('playing'));
            elements.nextBtn.disabled = true;

            // Update button text for last trial
            if (state.currentTrial === CONFIG.totalTrials - 1) {
                elements.nextBtn.textContent = 'Finalizar experimento';
            } else {
                elements.nextBtn.textContent = 'Siguiente prueba';
            }
        }

        async function playSoundOption(soundId) {
            const trial = state.currentTrialData;
            const optionIndex = ['A', 'B', 'C'].indexOf(soundId);
            const hasModulation = optionIndex === trial.targetPosition;

            // Track plays
            trial.soundsPlayed[soundId]++;

            // Visual feedback
            document.querySelectorAll('.sound-option').forEach(o => o.classList.remove('playing'));
            const currentOption = document.querySelector(`.sound-option[data-sound="${soundId}"]`);
            currentOption.classList.add('playing');

            await playSound(trial.waveType, trial.frequency, hasModulation);
            
            currentOption.classList.remove('playing');
        }

        function nextTrial() {
            // Record response
            const soundChoice = document.querySelector('input[name="sound-choice"]:checked').value;
            const confidence = parseInt(document.querySelector('input[name="confidence"]:checked').value);
            const trial = state.currentTrialData;
            
            const correctAnswer = ['A', 'B', 'C'][trial.targetPosition];
            const isCorrect = soundChoice === correctAnswer;
            
            state.responses.push({
                trialNumber: trial.trialNumber,
                waveType: trial.waveType,
                frequency: trial.frequency,
                correctAnswer: correctAnswer,
                userAnswer: soundChoice,
                isCorrect: isCorrect,
                confidence: confidence,
                responseTime: Date.now() - trial.startTime,
                soundsPlayed: { ...trial.soundsPlayed }
            });

            state.currentTrial++;

            if (state.currentTrial >= CONFIG.totalTrials) {
                showResults();
            } else {
                loadTrial();
            }
        }

        function showResults() {
            stopCurrentSound();
            
            elements.trialSection.classList.add('hidden');
            elements.resultsSection.classList.remove('hidden');

            // Calculate stats
            const totalCorrect = state.responses.filter(r => r.isCorrect).length;
            const sineResponses = state.responses.filter(r => r.waveType === 'sine');
            const sawResponses = state.responses.filter(r => r.waveType === 'sawtooth');
            
            const sineCorrect = sineResponses.filter(r => r.isCorrect).length;
            const sawCorrect = sawResponses.filter(r => r.isCorrect).length;

            elements.correctCount.textContent = totalCorrect;
            elements.accuracyPercent.textContent = `${Math.round((totalCorrect / CONFIG.totalTrials) * 100)}%`;
            elements.sineAccuracy.textContent = `${Math.round((sineCorrect / sineResponses.length) * 100)}%`;
            elements.sawAccuracy.textContent = `${Math.round((sawCorrect / sawResponses.length) * 100)}%`;
        }

        function downloadResults() {
            const results = {
                experimentInfo: {
                    name: "Percepción de Variación de Amplitud",
                    version: "1.0",
                    date: state.experimentStartTime,
                    completedAt: new Date().toISOString()
                },
                participant: {
                    age: state.participantAge,
                    musicalKnowledge: state.musicalKnowledge
                },
                summary: {
                    totalTrials: CONFIG.totalTrials,
                    correctResponses: state.responses.filter(r => r.isCorrect).length,
                    accuracy: state.responses.filter(r => r.isCorrect).length / CONFIG.totalTrials,
                    sineAccuracy: state.responses.filter(r => r.waveType === 'sine' && r.isCorrect).length / CONFIG.trialsPerWaveType,
                    sawtoothAccuracy: state.responses.filter(r => r.waveType === 'sawtooth' && r.isCorrect).length / CONFIG.trialsPerWaveType,
                    averageConfidence: state.responses.reduce((sum, r) => sum + r.confidence, 0) / state.responses.length,
                    averageResponseTime: state.responses.reduce((sum, r) => sum + r.responseTime, 0) / state.responses.length
                },
                trials: state.responses
            };

            const blob = new Blob([JSON.stringify(results, null, 2)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `experimento-amplitud-${Date.now()}.json`;
            document.body.appendChild(a);
            a.click();
            document.body.removeChild(a);
            URL.revokeObjectURL(url);
        }

        // Initialize
        elements.totalTrialsSpan.textContent = CONFIG.totalTrials;
    </script>
</body>
</html>
