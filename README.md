<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Vocab Practice</title>
    <style>
        :root {
            --primary-color: #2c3e50;
            --secondary-color: #3498db;
            --accent-color: #1abc9c;
            --light-bg: #f9fbfd;
            --dark-text: #2c3e50;
            --success-color: #27ae60;
            --warning-color: #f1c40f;
            --error-color: #e74c3c;
        }

        body { font-family: 'Segoe UI', Tahoma, sans-serif; background: var(--light-bg); color: var(--dark-text); margin: 0; padding-bottom: 50px; }
        header { background: var(--primary-color); color: white; padding: 25px; text-align: center; position: relative; }
        .score-display { position: absolute; top: 25px; right: 25px; background: rgba(255,255,255,0.1); padding: 5px 15px; border-radius: 20px; font-weight: bold; }
        .container { max-width: 950px; margin: 30px auto; padding: 0 20px; }
        
        .nav-buttons { display: flex; gap: 10px; margin-bottom: 25px; flex-wrap: wrap; }
        .nav-button { flex: 1; padding: 12px; border: none; border-radius: 8px; background: #dfe6e9; cursor: pointer; font-weight: bold; transition: 0.3s; }
        .nav-button.active { background: var(--secondary-color); color: white; }

        .exercise-section { background: white; padding: 30px; border-radius: 12px; box-shadow: 0 10px 30px rgba(0,0,0,0.05); }
        .word-bank { display: flex; flex-wrap: wrap; gap: 12px; background: #f1f4f8; padding: 20px; border-radius: 10px; margin-bottom: 20px; border: 2px dashed var(--secondary-color); }
        .word-bank-item { background: white; padding: 8px 15px; border-radius: 6px; font-weight: bold; box-shadow: 0 2px 5px rgba(0,0,0,0.1); color: var(--primary-color); }

        .vocab-item { display: flex; align-items: center; gap: 20px; padding: 15px 0; border-bottom: 1px solid #eee; }
        .word-cell { flex: 0 0 180px; font-weight: bold; color: var(--secondary-color); font-size: 1.1em; }

        .gap-sentence { margin: 15px 0; font-size: 1.1em; line-height: 1.6; padding: 10px; border-left: 4px solid transparent; transition: 0.3s; }
        .gap-sentence:hover { background: #fcfcfc; border-left-color: #eee; }
        .gap-input { border: none; border-bottom: 2px solid var(--secondary-color); width: 150px; text-align: center; font-size: inherit; outline: none; background: transparent; }
        
        .matching-columns { display: grid; grid-template-columns: 1fr 1fr; gap: 40px; }
        .match-column { display: flex; flex-direction: column; gap: 10px; }
        .match-box { padding: 15px; border: 2px solid #ecf0f1; border-radius: 8px; cursor: pointer; transition: 0.2s; background: white; min-height: 50px; display: flex; align-items: center; justify-content: center; text-align: center; }
        .match-box.selected { border-color: var(--secondary-color); background: #ebf5fb; }
        .match-box.correct { background: var(--success-color); color: white; border-color: var(--success-color); cursor: default; }

        .status-green { color: var(--success-color); font-weight: bold; font-size: 0.9em; }
        .status-red { color: var(--error-color); font-weight: bold; font-size: 0.9em; }

        .speaker-btn, .mic-btn { background: #f1f4f8; border: none; border-radius: 50%; width: 40px; height: 40px; cursor: pointer; display: inline-flex; align-items: center; justify-content: center; font-size: 1.2em; }
        .action-btn { background: var(--accent-color); color: white; border: none; padding: 12px 30px; border-radius: 8px; cursor: pointer; font-weight: bold; margin-top: 20px; }
    </style>
</head>
<body>

<header>
    <h1>Daily English Practice</h1>
    <div class="score-display">Total Score: <span id="score-val">0</span></div>
</header>

<div class="container">
    <div class="nav-buttons">
        <button class="nav-button active" onclick="switchTab(0)">1. Study List</button>
        <button class="nav-button" onclick="switchTab(1)">2. Writing</button>
        <button class="nav-button" onclick="switchTab(2)">3. Matching</button>
        <button class="nav-button" onclick="switchTab(3)">4. Pronunciation</button>
    </div>

    <div id="tab-0" class="exercise-section">
        <h2>📚 Vocabulary List</h2>
        <div id="vocab-list"></div>
    </div>

    <div id="tab-1" class="exercise-section" style="display:none">
        <h2>✍️ Expanded Writing Practice</h2>
        <div id="writing-bank" class="word-bank"></div>
        <div id="writing-content"></div>
        <button class="action-btn" onclick="checkWriting()">Check All Answers</button>
    </div>

    <div id="tab-2" class="exercise-section" style="display:none">
        <h2>🔗 Meaning Matching</h2>
        <div class="matching-columns">
            <div id="match-left" class="match-column"></div>
            <div id="match-right" class="match-column"></div>
        </div>
    </div>

    <div id="tab-3" class="exercise-section" style="display:none">
        <h2>🗣️ Pronunciation: Guess the missing word</h2>
        <div id="pron-bank" class="word-bank"></div>
        <div id="pron-content"></div>
    </div>
</div>

<script>
    // Expanded data with multiple sentences per word
    const data = [
        { 
            word: "low-season", 
            def: "The time of year when travel is less popular and cheaper.", 
            writingSents: [
                "I prefer traveling during the ___ because there are fewer crowds.",
                "Hotels usually offer massive discounts during the ___."
            ],
            pronSents: [
                "Flights are much cheaper in the ___.",
                "Is it still warm enough to visit the beach during the ___?"
            ]
        },
        { 
            word: "rusty", 
            def: "Out of practice in a skill; or covered in iron oxide.", 
            writingSents: [
                "My English is a bit ___ because I haven't spoken it in years.",
                "Be careful! That old metal fence is very ___."
            ],
            pronSents: [
                "I need to practice my guitar; I'm feeling quite ___.",
                "The hinges on the door were so ___ that they squeaked."
            ]
        },
        { 
            word: "replace", 
            def: "To take the place of something; to provide a substitute.", 
            writingSents: [
                "I need to ___ the batteries in my remote control.",
                "It’s hard to ___ a colleague who was so efficient."
            ],
            pronSents: [
                "Can you ___ the broken window by tomorrow?",
                "We had to ___ the old carpet with wooden flooring."
            ]
        },
        { 
            word: "used to", 
            def: "In the process of becoming accustomed to something.", 
            writingSents: [
                "I am slowly getting ___ waking up early for my new job.",
                "It took me months to get ___ driving on the left side of the road."
            ],
            pronSents: [
                "She's not ___ the cold weather in Canada yet.",
                "Are you getting ___ your new office routine?"
            ]
        }
    ];

    let totalScore = 0;

    function shuffle(array) {
        let currentIndex = array.length, randomIndex;
        while (currentIndex != 0) {
            randomIndex = Math.floor(Math.random() * currentIndex);
            currentIndex--;
            [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
        }
        return array;
    }

    // Force English native voice selection
    function speak(text) {
        const synth = window.speechSynthesis;
        const utterance = new SpeechSynthesisUtterance(text);
        const voices = synth.getVoices();
        
        // Priority: Google US English -> Any US/GB English -> Any English
        const nativeVoice = voices.find(v => v.lang === 'en-US' && v.name.includes('Google')) ||
                           voices.find(v => v.lang === 'en-GB' && v.name.includes('Google')) ||
                           voices.find(v => v.lang.startsWith('en-')) ||
                           voices[0];
        
        utterance.voice = nativeVoice;
        utterance.rate = 0.9;
        synth.speak(utterance);
    }

    function switchTab(idx) {
        document.querySelectorAll('.exercise-section').forEach((s, i) => s.style.display = i === idx ? 'block' : 'none');
        document.querySelectorAll('.nav-button').forEach((b, i) => b.classList.toggle('active', i === idx));
    }

    function init() {
        // Tab 0: Study List
        const list = document.getElementById('vocab-list');
        list.innerHTML = "";
        data.forEach(item => {
            list.innerHTML += `<div class="vocab-item"><div class="word-cell">${item.word}</div><button class="speaker-btn" onclick="speak('${item.word}')">🔊</button><div>${item.def}</div></div>`;
        });

        const wordList = data.map(d => d.word);

        // Tab 1: Writing
        document.getElementById('writing-bank').innerHTML = shuffle([...wordList]).map(w => `<div class="word-bank-item">${w}</div>`).join('');
        const wCont = document.getElementById('writing-content');
        wCont.innerHTML = "";
        data.forEach((item, i) => {
            item.writingSents.forEach((s, j) => {
                const id = `w-in-${i}-${j}`;
                wCont.innerHTML += `<div class="gap-sentence">${s.replace('___', `<input type="text" class="gap-input" data-ans="${item.word}" id="${id}">`)} <span id="fb-${id}"></span></div>`;
            });
        });

        // Tab 2: Matching
        const leftCol = document.getElementById('match-left');
        const rightCol = document.getElementById('match-right');
        const meanings = shuffle(data.map((d, i) => ({ t: d.def, id: i, type: 'd' })));
        const words = shuffle(data.map((d, i) => ({ t: d.word, id: i, type: 'w' })));
        leftCol.innerHTML = meanings.map(obj => `<div class="match-box" data-id="${obj.id}" data-type="${obj.type}" onclick="handleMatch(this)">${obj.t}</div>`).join('');
        rightCol.innerHTML = words.map(obj => `<div class="match-box" data-id="${obj.id}" data-type="${obj.type}" onclick="handleMatch(this)">${obj.t}</div>`).join('');

        // Tab 3: Pronunciation
        document.getElementById('pron-bank').innerHTML = shuffle([...wordList]).map(w => `<div class="word-bank-item">${w}</div>`).join('');
        const pCont = document.getElementById('pron-content');
        pCont.innerHTML = "";
        data.forEach((item, i) => {
            item.pronSents.forEach((s, j) => {
                pCont.innerHTML += `<div class="gap-sentence">${s.replace(item.word, '_________')} <button class="mic-btn" onclick="testSpeech('${item.word}', 'p-fb-${i}-${j}')">🎤</button> <div id="p-fb-${i}-${j}" style="color:#888; font-size: 0.8em;">Click mic & say the word!</div></div>`;
            });
        });
    }

    function checkWriting() {
        document.querySelectorAll('.gap-input').forEach(input => {
            const fb = document.getElementById(`fb-${input.id}`);
            const val = input.value.trim().toLowerCase();
            const target = input.dataset.ans.toLowerCase();
            if (val === target) {
                fb.innerHTML = `<span class="status-green">✅</span>`;
                totalScore += 0.5;
            } else {
                fb.innerHTML = `<span class="status-red">❌ (${input.dataset.ans})</span>`;
            }
        });
        updateDisplayScore();
    }

    let firstMatch = null;
    function handleMatch(el) {
        if (el.classList.contains('correct')) return;
        if (!firstMatch) {
            firstMatch = el; el.classList.add('selected');
        } else {
            if (firstMatch.dataset.id === el.dataset.id && firstMatch.dataset.type !== el.dataset.type) {
                firstMatch.classList.replace('selected', 'correct'); el.classList.add('correct');
                totalScore += 1;
            } else {
                firstMatch.classList.remove('selected');
            }
            firstMatch = null;
            updateDisplayScore();
        }
    }

    function testSpeech(word, fbId) {
        const rec = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
        rec.lang = 'en-US'; rec.start();
        document.getElementById(fbId).innerText = "Listening...";
        rec.onresult = (e) => {
            const heard = e.results[0][0].transcript.toLowerCase();
            const target = word.toLowerCase();
            if (heard.includes(target)) {
                document.getElementById(fbId).innerHTML = `<span class="status-green">✅ Perfect!</span>`;
                totalScore += 1;
            } else {
                document.getElementById(fbId).innerHTML = `<span class="status-red">❌ Heard "${heard}". Try again!</span>`;
            }
            updateDisplayScore();
        };
    }

    function updateDisplayScore() {
        document.getElementById('score-val').innerText = Math.floor(totalScore);
    }

    window.onload = () => {
        if ('speechSynthesis' in window) {
            window.speechSynthesis.onvoiceschanged = init;
            if (window.speechSynthesis.getVoices().length > 0) init();
        } else { init(); }
    };
</script>
</body>
</html>
