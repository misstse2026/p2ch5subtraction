<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <!-- Tailwind CSS -->
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@600;700&family=Noto+Sans+TC:wght@700;900&display=swap" rel="stylesheet">
    
    <style>
        * {
            touch-action: manipulation;
            user-select: none;
            -webkit-user-select: none;
            -webkit-touch-callout: none;
        }

        body {
            font-family: 'Noto Sans TC', 'Fredoka', sans-serif;
            background: #0f172a;
            overflow: hidden;
            color: #f8fafc;
        }

        /* Animated Starry Space Background */
        .space-bg {
            background-color: #0b0f19;
            background-image: 
                radial-gradient(2px 2px at 20px 30px, #e2e8f0, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 40px 70px, #38bdf8, rgba(0,0,0,0)),
                radial-gradient(3px 3px at 80px 120px, #f472b6, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 150px 180px, #e2e8f0, rgba(0,0,0,0)),
                radial-gradient(3px 3px at 220px 250px, #fbbf24, rgba(0,0,0,0));
            background-size: 300px 300px;
            animation: spaceMove 60s linear infinite;
        }

        @keyframes spaceMove {
            from { background-position: 0 0; }
            to { background-position: 1000px 1000px; }
        }

        /* Floating Monster Hover Animation */
        .monster-float {
            animation: monsterFloat 3.5s ease-in-out infinite alternate;
        }

        @keyframes monsterFloat {
            0% { transform: translateY(0px) rotate(-2deg); }
            50% { transform: translateY(-12px) rotate(2deg); }
            100% { transform: translateY(0px) rotate(-2deg); }
        }

        /* Monster Wrong Answer Shake */
        .shake-err {
            animation: shakeError 0.5s cubic-bezier(.36,.07,.19,.97) both;
        }

        @keyframes shakeError {
            10%, 90% { transform: translate3d(-4px, 0, 0) scale(0.95); }
            20%, 80% { transform: translate3d(8px, 0, 0) scale(1.05); }
            30%, 50%, 70% { transform: translate3d(-10px, 0, 0); }
            40%, 60% { transform: translate3d(10px, 0, 0); }
        }

        /* Defeat Blast Animation */
        .pop-defeat {
            animation: popDefeat 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        @keyframes popDefeat {
            0% { transform: scale(1) rotate(0deg); opacity: 1; }
            50% { transform: scale(1.4) rotate(15deg); opacity: 0.8; }
            100% { transform: scale(0) rotate(45deg); opacity: 0; }
        }

        /* Vertical Slash for Column Borrowing Notation */
        .slash-mark {
            position: relative;
        }
        .slash-mark::after {
            content: '';
            position: absolute;
            left: 20%;
            top: 15%;
            width: 60%;
            height: 4px;
            background-color: #f43f5e;
            transform: rotate(-45deg);
            border-radius: 2px;
            animation: strokeSlash 0.25s ease-out forwards;
        }

        @keyframes strokeSlash {
            from { width: 0%; opacity: 0; }
            to { width: 60%; opacity: 1; }
        }

        /* Custom Scrollbar for Drawer */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-thumb {
            background: #38bdf8;
            border-radius: 999px;
        }
    </style>
</head>
<body class="space-bg h-screen w-screen flex flex-col justify-between p-3 md:p-6 relative overflow-hidden select-none">

    <!-- TOP HEADER / DASHBOARD -->
    <header class="w-full max-w-6xl mx-auto bg-slate-900/80 backdrop-blur-md border-2 border-indigo-500/40 rounded-2xl p-3 shadow-2xl flex items-center justify-between z-20">
        
        <!-- Left: Game Title & Wave -->
        <div class="flex items-center space-x-3">
            <div class="bg-indigo-600 text-white p-2 rounded-xl text-2xl shadow-lg border border-indigo-400">
                🚀
            </div>
            <div>
                <h1 class="text-base md:text-xl font-black text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 via-sky-300 to-indigo-300">
                    擊退怪獸大作戰
                </h1>
                <div class="flex items-center space-x-2">
                    <span id="wave-badge" class="bg-purple-900/80 text-purple-300 text-xs font-bold px-2 py-0.5 rounded-md border border-purple-500/40">
                        關卡 1 / 5
                    </span>
                    <span id="combo-badge" class="bg-amber-500/20 text-amber-300 text-xs font-extrabold px-2 py-0.5 rounded-md border border-amber-500/40 hidden">
                        🔥 連擊 x0
                    </span>
                </div>
            </div>
        </div>

        <!-- Center: Health / Lives -->
        <div class="flex items-center space-x-1.5 bg-slate-800/80 px-3 py-1.5 rounded-xl border border-slate-700">
            <span class="text-xs font-bold text-slate-400 mr-1 hidden sm:inline">生命:</span>
            <div id="lives-container" class="flex space-x-1 text-xl md:text-2xl">
                <span>❤️</span><span>❤️</span><span>❤️</span>
            </div>
        </div>

        <!-- Right: Score & Actions -->
        <div class="flex items-center space-x-3">
            <div class="text-right">
                <span class="text-[10px] font-bold text-slate-400 block uppercase tracking-wider">Score</span>
                <span id="score-text" class="text-xl md:text-2xl font-black text-amber-400">0</span>
            </div>

            <!-- Helper Visualizer Button -->
            <button onclick="toggleVisualizer()" class="bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 text-white text-xs md:text-sm font-black px-3 py-2 rounded-xl shadow-lg border border-emerald-300/50 active:scale-95 transition flex items-center space-x-1">
                <span>🧮</span>
                <span class="hidden sm:inline">拆十魔法</span>
            </button>

            <!-- Sound Mute Toggle -->
            <button id="sound-btn" onclick="toggleSound()" class="bg-slate-800 hover:bg-slate-700 text-slate-200 text-lg p-2 rounded-xl border border-slate-600 active:scale-95">
                🔊
            </button>
        </div>
    </header>

    <!-- MAIN BATTLEFIELD AREA -->
    <main class="w-full max-w-6xl mx-auto flex-1 flex flex-col justify-between my-2 md:my-4 relative z-10">
        
        <!-- Target Question Box / Cannon Interface -->
        <div class="w-full bg-slate-900/90 border-2 border-cyan-500/50 rounded-3xl p-3 md:p-4 shadow-2xl flex flex-col sm:flex-row items-center justify-between gap-3 relative overflow-hidden">
            <div class="absolute inset-0 bg-gradient-to-r from-cyan-500/10 via-indigo-500/10 to-purple-500/10 pointer-events-none"></div>

            <div class="flex items-center space-x-3 z-10">
                <span class="text-3xl md:text-4xl animate-pulse">🎯</span>
                <div>
                    <span class="text-xs font-bold text-cyan-400 uppercase tracking-widest block">目標退位減法題</span>
                    <span class="text-xs text-slate-400">找尋並擊退帶有正確答案的怪獸！</span>
                </div>
            </div>

            <!-- Big Math Equation Display -->
            <div class="bg-slate-950 border-2 border-cyan-400/60 rounded-2xl px-6 py-2 shadow-inner z-10 flex items-center space-x-3">
                <span id="problem-text" class="text-3xl md:text-5xl font-black tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-cyan-300 via-white to-pink-300">
                    52 - 27 = ?
                </span>
            </div>

            <div class="text-xs font-extrabold text-amber-300 bg-amber-950/60 border border-amber-500/40 px-3 py-1.5 rounded-xl z-10 flex items-center space-x-1">
                <span>💡 提示:</span>
                <span id="quick-hint-text">個位 2 不夠減 7，記得向十位借1！</span>
            </div>
        </div>

        <!-- Floating Monsters Battlefield Grid -->
        <div id="monsters-zone" class="flex-1 my-4 grid grid-cols-2 md:grid-cols-4 gap-4 items-center justify-items-center relative min-h-[260px]">
            <!-- Monsters dynamically injected here -->
        </div>

        <!-- Defense Hero Cannon / Wand Base at Bottom -->
        <div class="w-full flex justify-center items-center relative">
            <div id="player-hero" class="relative group cursor-pointer" onclick="triggerHeroWiggle()">
                <!-- Hero SVG Cannon/Wand Avatar -->
                <div class="w-20 h-20 md:w-24 md:w-24 bg-gradient-to-t from-indigo-700 to-purple-500 rounded-full border-4 border-cyan-400 shadow-[0_0_25px_rgba(56,189,248,0.5)] flex items-center justify-center text-4xl relative">
                    🧙‍♂️
                    <div class="absolute -top-2 bg-cyan-400 text-slate-950 text-[10px] font-black px-2 py-0.5 rounded-full shadow">
                        數學大救星
                    </div>
                </div>
            </div>
        </div>
    </main>

    <!-- INTERACTIVE BORROWING VISUALIZER DRAWER ("退位拆十魔法") -->
    <div id="visualizer-drawer" class="fixed inset-x-0 bottom-0 max-w-4xl mx-auto bg-slate-900/95 backdrop-blur-xl border-t-4 border-x-4 border-emerald-400 rounded-t-3xl shadow-[0_-10px_40px_rgba(16,185,129,0.3)] z-40 transform translate-y-full transition-transform duration-300 ease-out p-4 md:p-6 text-slate-100 max-h-[85vh] overflow-y-auto">
        
        <!-- Drawer Header -->
        <div class="flex justify-between items-center mb-4 border-b border-slate-700 pb-3">
            <div class="flex items-center space-x-2">
                <span class="text-3xl">🧮</span>
                <div>
                    <h2 class="text-lg md:text-xl font-black text-emerald-400">退位拆十魔法對照器</h2>
                    <p class="text-xs text-slate-400">親手將1個「十棒」拆成10個「一塊」，理解借位過程！</p>
                </div>
            </div>
            <button onclick="toggleVisualizer()" class="text-slate-400 hover:text-white bg-slate-800 p-2 rounded-xl text-lg border border-slate-700">
                ✖
            </button>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6 items-center">
            
            <!-- Left Side: Physical Counting Blocks (十位棒棒與個位小塊) -->
            <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800 flex flex-col justify-between min-h-[220px]">
                <div class="flex justify-between items-center mb-2">
                    <span class="text-xs font-bold text-emerald-400">積木圖像化顯示</span>
                    <span id="viz-total-text" class="text-xs bg-slate-800 px-2 py-0.5 rounded text-slate-300">總數: 52</span>
                </div>

                <div class="grid grid-cols-2 gap-3 flex-1">
                    <!-- Tens Zone (十位) -->
                    <div class="bg-slate-900/80 p-3 rounded-xl border border-indigo-500/30 flex flex-col items-center">
                        <span class="text-xs font-black text-indigo-400 mb-2">十位 (十棒)</span>
                        <div id="viz-tens-container" class="flex flex-wrap justify-center gap-1.5 min-h-[100px] items-center">
                            <!-- Ten bars rendered here -->
                        </div>
                    </div>

                    <!-- Ones Zone (個位) -->
                    <div class="bg-slate-900/80 p-3 rounded-xl border border-pink-500/30 flex flex-col items-center">
                        <span class="text-xs font-black text-pink-400 mb-2">個位 (一塊)</span>
                        <div id="viz-ones-container" class="flex flex-wrap justify-center gap-1.5 min-h-[100px] items-center">
                            <!-- Single blocks rendered here -->
                        </div>
                    </div>
                </div>

                <!-- Interactive Break-Ten Button -->
                <button id="break-ten-btn" onclick="executeBreakTenVisualizer()" class="mt-3 w-full bg-gradient-to-r from-amber-500 to-rose-500 hover:from-amber-400 hover:to-rose-400 text-slate-950 font-black py-2.5 rounded-xl shadow-lg border border-amber-300 text-sm active:scale-95 transition flex items-center justify-center space-x-2">
                    <span>✨ 點擊執行「魔法拆十」 (1個十 ➔ 10個一)</span>
                </button>
            </div>

            <!-- Right Side: Standard Vertical Column Subtraction (直式對照) -->
            <div class="bg-slate-950 p-4 rounded-2xl border border-slate-800 flex flex-col items-center justify-center">
                <span class="text-xs font-bold text-slate-400 mb-3">直式退位紀錄</span>
                
                <div class="bg-slate-900 border-2 border-indigo-500/40 rounded-2xl p-4 min-w-[200px] relative font-mono">
                    
                    <!-- Top Column Headers -->
                    <div class="grid grid-cols-2 text-center text-xs font-black pb-2 border-b border-slate-800 mb-4">
                        <span class="text-indigo-400">十位</span>
                        <span class="text-pink-400">個位</span>
                    </div>

                    <!-- Borrow Marks Row (Shows adjusted numbers after borrow) -->
                    <div class="grid grid-cols-2 text-center text-emerald-400 font-bold text-base h-6">
                        <span id="viz-borrow-tens" class="opacity-0 transition-opacity">4</span>
                        <span id="viz-borrow-ones" class="opacity-0 transition-opacity">12</span>
                    </div>

                    <!-- Minuend Row -->
                    <div class="grid grid-cols-2 text-center text-3xl font-black text-slate-100 mb-2 relative">
                        <span id="viz-m-tens" class="transition-all">5</span>
                        <span id="viz-m-ones" class="transition-all">2</span>
                    </div>

                    <!-- Subtrahend Row -->
                    <div class="grid grid-cols-2 text-center text-3xl font-black text-slate-100 border-b-4 border-slate-100 pb-2 mb-2 relative">
                        <span class="absolute -left-2 top-0 text-rose-500 font-sans">-</span>
                        <span id="viz-s-tens">2</span>
                        <span id="viz-s-ones">7</span>
                    </div>

                    <!-- Calculated Result Row -->
                    <div class="grid grid-cols-2 text-center text-3xl font-black text-amber-400">
                        <span id="viz-res-tens">?</span>
                        <span id="viz-res-ones">?</span>
                    </div>
                </div>

                <p id="viz-step-explain" class="text-xs text-amber-300 font-bold mt-3 text-center bg-amber-950/40 px-3 py-1.5 rounded-lg border border-amber-500/30">
                    點擊「魔法拆十」觀看個位與十位變化！
                </p>
            </div>
        </div>
    </div>

    <!-- STAGE / GAME OVER SUMMARY MODAL -->
    <div id="summary-modal" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-slate-900 border-4 border-indigo-500 rounded-3xl p-6 max-w-md w-full text-center shadow-2xl relative overflow-hidden">
            <div id="modal-icon" class="text-6xl mb-2 animate-bounce">🏆</div>
            <h2 id="modal-title" class="text-2xl md:text-3xl font-black text-transparent bg-clip-text bg-gradient-to-r from-amber-300 via-pink-400 to-purple-400 mb-2">
                挑戰成功！
            </h2>
            <p id="modal-subtext" class="text-sm text-slate-300 font-bold mb-4">你成功擊退了所有退位減法怪獸！</p>

            <!-- Star Rating Display -->
            <div id="modal-stars" class="flex justify-center space-x-2 text-4xl mb-4">
                <span>⭐</span><span>⭐</span><span>⭐</span>
            </div>

            <!-- Stats Box -->
            <div class="bg-slate-950 rounded-2xl p-4 border border-slate-800 mb-6 grid grid-cols-2 gap-2 text-left">
                <div>
                    <span class="text-xs text-slate-400 font-bold block">最終得分</span>
                    <span id="modal-score" class="text-xl font-black text-amber-400">1200</span>
                </div>
                <div>
                    <span class="text-xs text-slate-400 font-bold block">答對率</span>
                    <span id="modal-accuracy" class="text-xl font-black text-emerald-400">100%</span>
                </div>
            </div>

            <!-- Modal Action Buttons -->
            <div class="flex space-x-3">
                <button onclick="restartFullGame()" class="w-full bg-gradient-to-r from-indigo-600 to-purple-600 hover:from-indigo-500 hover:to-purple-500 text-white font-black py-3 rounded-2xl border border-indigo-400/50 shadow-lg active:scale-95 transition text-base">
                    再玩一次 🔄
                </button>
            </div>
        </div>
    </div>

    <script>
        /* ===================================================================
         * Web Audio API Synthesizer (Zero External Audio Files Required)
         * =================================================================== */
        let audioCtx = null;
        let isSoundMuted = false;

        function initAudio() {
            if (!audioCtx) {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                audioCtx = new AudioContext();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        }

        function playSound(type) {
            if (isSoundMuted) return;
            initAudio();
            if (!audioCtx) return;

            const now = audioCtx.currentTime;

            if (type === 'zap') {
                // Laser Cannon Fire
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(800, now);
                osc.frequency.exponentialRampToValueAtTime(100, now + 0.15);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.15);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.15);
            } 
            else if (type === 'pop') {
                // Monster Defeat Explosion
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(150, now);
                osc.frequency.exponentialRampToValueAtTime(600, now + 0.1);
                gain.gain.setValueAtTime(0.4, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.12);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.12);
            } 
            else if (type === 'wrong') {
                // Incorrect Buzz
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(180, now);
                osc.frequency.setValueAtTime(130, now + 0.1);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.25);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.25);
            }
            else if (type === 'magic') {
                // Borrowing Magic Break sound
                [523.25, 659.25, 783.99, 1046.50].forEach((freq, idx) => {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.05);
                    gain.gain.setValueAtTime(0.2, now + idx * 0.05);
                    gain.gain.linearRampToValueAtTime(0.01, now + idx * 0.05 + 0.15);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now + idx * 0.05);
                    osc.stop(now + idx * 0.05 + 0.15);
                });
            }
            else if (type === 'fanfare') {
                // Stage Victory Fanfare
                const notes = [440, 554.37, 659.25, 880];
                notes.forEach((freq, i) => {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, now + i * 0.12);
                    gain.gain.setValueAtTime(0.3, now + i * 0.12);
                    gain.gain.linearRampToValueAtTime(0.01, now + i * 0.12 + 0.3);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now + i * 0.12);
                    osc.stop(now + i * 0.12 + 0.3);
                });
            }
        }

        function toggleSound() {
            isSoundMuted = !isSoundMuted;
            document.getElementById('sound-btn').innerText = isSoundMuted ? '🔇' : '🔊';
        }

        /* ===================================================================
         * Pedagogical Math Engine for Grade 2 Subtraction with Regrouping
         * =================================================================== */
        function generateBorrowingProblem() {
            // Minuend Ones (0-5)
            const mOnes = Math.floor(Math.random() * 6); 
            // Subtrahend Ones (mOnes + 2 ~ 9) -> Guarantees borrowing required!
            const sOnes = mOnes + Math.floor(Math.random() * 3) + 3;

            // Minuend Tens (3-9)
            const mTens = Math.floor(Math.random() * 7) + 3;
            // Subtrahend Tens (1 ~ mTens - 1)
            const sTens = Math.floor(Math.random() * (mTens - 2)) + 1;

            const minuend = mTens * 10 + mOnes;
            const subtrahend = sTens * 10 + sOnes;
            const correctAnswer = minuend - subtrahend;

            // Generate Smart Distractors based on P.2 Misconceptions
            const options = new Set();
            options.add(correctAnswer);

            // Misconception 1: Flipped subtraction in ones place (Subtracting top from bottom: sOnes - mOnes)
            const flippedOnes = sOnes - mOnes;
            const distractorFlipped = (mTens - sTens) * 10 + flippedOnes;
            if (distractorFlipped > 0 && distractorFlipped !== correctAnswer) {
                options.add(distractorFlipped);
            }

            // Misconception 2: Forgot to decrement tens place (Borrowed 10 in ones, but did mTens - sTens)
            const correctOnes = (mOnes + 10) - sOnes;
            const distractorForgotDec = (mTens - sTens) * 10 + correctOnes;
            if (distractorForgotDec > 0 && distractorForgotDec !== correctAnswer) {
                options.add(distractorForgotDec);
            }

            // Misconception 3: Off by 10 or Off by 1 calculation error
            const distractorOff10 = correctAnswer + 10;
            if (distractorOff10 < 100 && distractorOff10 !== correctAnswer) {
                options.add(distractorOff10);
            }

            const distractorOff1 = correctAnswer - 1 > 0 ? correctAnswer - 1 : correctAnswer + 2;
            options.add(distractorOff1);

            // Convert set to array and shuffle
            const shuffledOptions = Array.from(options).slice(0, 4);
            while (shuffledOptions.length < 4) {
                const randomOffset = Math.floor(Math.random() * 20) + 5;
                if (!shuffledOptions.includes(randomOffset)) shuffledOptions.push(randomOffset);
            }

            // Shuffle array
            for (let i = shuffledOptions.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [shuffledOptions[i], shuffledOptions[j]] = [shuffledOptions[j], shuffledOptions[i]];
            }

            return {
                minuend,
                subtrahend,
                mTens,
                mOnes,
                sTens,
                sOnes,
                correctAnswer,
                options: shuffledOptions
            };
        }

        /* ===================================================================
         * Game State & Controller
         * =================================================================== */
        let gameState = {
            currentWave: 1,
            totalWaves: 5,
            score: 0,
            lives: 3,
            combo: 0,
            totalAttempted: 0,
            totalCorrect: 0,
            currentProblem: null,
            visualizerBorrowed: false
        };

        // Monster Palette & Shapes Generator
        const monsterStyles = [
            { body: '#a855f7', eyes: 1, horn: '#f59e0b' }, // Purple 1-eye
            { body: '#ec4899', eyes: 2, horn: '#06b6d4' }, // Pink 2-eyes
            { body: '#10b981', eyes: 3, horn: '#f43f5e' }, // Green 3-eyes
            { body: '#f59e0b', eyes: 2, horn: '#8b5cf6' }  // Amber Blob
        ];

        function initGame() {
            gameState.currentWave = 1;
            gameState.score = 0;
            gameState.lives = 3;
            gameState.combo = 0;
            gameState.totalAttempted = 0;
            gameState.totalCorrect = 0;

            updateHeaderUI();
            loadNextProblem();
        }

        function updateHeaderUI() {
            document.getElementById('score-text').innerText = gameState.score;
            document.getElementById('wave-badge').innerText = `關卡 ${gameState.currentWave} / ${gameState.totalWaves}`;
            
            // Render Hearts
            const livesContainer = document.getElementById('lives-container');
            livesContainer.innerHTML = '';
            for (let i = 0; i < 3; i++) {
                const heart = document.createElement('span');
                heart.innerText = i < gameState.lives ? '❤️' : '🖤';
                livesContainer.appendChild(heart);
            }

            // Combo Badge
            const comboBadge = document.getElementById('combo-badge');
            if (gameState.combo > 1) {
                comboBadge.innerText = `🔥 連擊 x${gameState.combo}`;
                comboBadge.classList.remove('hidden');
            } else {
                comboBadge.classList.add('hidden');
            }
        }

        function loadNextProblem() {
            gameState.currentProblem = generateBorrowingProblem();
            gameState.visualizerBorrowed = false;

            // Render Target Equation
            const p = gameState.currentProblem;
            document.getElementById('problem-text').innerText = `${p.minuend} - ${p.subtrahend} = ?`;
            document.getElementById('quick-hint-text').innerText = `個位 ${p.mOnes} 不夠減 ${p.sOnes}，記得向十位借1！`;

            // Render Monsters
            renderMonstersZone(p.options);

            // Sync Visualizer Drawer Data
            syncVisualizerData();
        }

        /* ===================================================================
         * Monster SVG Rendering
         * =================================================================== */
        function createMonsterSVG(number, styleIndex) {
            const style = monsterStyles[styleIndex % monsterStyles.length];
            return `
                <div class="monster-card relative flex flex-col items-center cursor-pointer monster-float group active:scale-90 transition-transform" onclick="shootMonster(this, ${number})">
                    <!-- SVG Avatar -->
                    <div class="w-24 h-24 md:w-32 md:h-32 filter drop-shadow-[0_10px_15px_rgba(0,0,0,0.5)]">
                        <svg viewBox="0 0 120 120" class="w-full h-full">
                            <!-- Body -->
                            <path d="M20,60 C20,20 100,20 100,60 C100,100 90,110 60,110 C30,110 20,100 20,60 Z" fill="${style.body}"/>
                            <!-- Horns -->
                            <path d="M35,25 Q25,5 15,15 Q25,30 38,28 Z" fill="${style.horn}"/>
                            <path d="M85,25 Q95,5 105,15 Q95,30 82,28 Z" fill="${style.horn}"/>
                            <!-- Eyes -->
                            ${style.eyes === 1 ? `
                                <circle cx="60" cy="50" r="14" fill="white"/>
                                <circle cx="60" cy="52" r="6" fill="#0f172a"/>
                            ` : style.eyes === 2 ? `
                                <circle cx="45" cy="50" r="11" fill="white"/>
                                <circle cx="75" cy="50" r="11" fill="white"/>
                                <circle cx="47" cy="52" r="5" fill="#0f172a"/>
                                <circle cx="77" cy="52" r="5" fill="#0f172a"/>
                            ` : `
                                <circle cx="35" cy="52" r="9" fill="white"/>
                                <circle cx="60" cy="46" r="10" fill="white"/>
                                <circle cx="85" cy="52" r="9" fill="white"/>
                                <circle cx="36" cy="53" r="4" fill="#0f172a"/>
                                <circle cx="60" cy="48" r="5" fill="#0f172a"/>
                                <circle cx="86" cy="53" r="4" fill="#0f172a"/>
                            `}
                            <!-- Mouth -->
                            <path d="M45,80 Q60,95 75,80 Q60,86 45,80 Z" fill="#4c0519"/>
                            <polygon points="52,80 57,86 62,80" fill="white"/>
                        </svg>
                    </div>

                    <!-- Carrying Number Badge Orb -->
                    <div class="absolute -bottom-2 bg-gradient-to-r from-cyan-400 to-indigo-500 text-slate-950 font-black text-2xl md:text-3xl px-4 py-1 rounded-full border-2 border-white shadow-[0_0_15px_rgba(56,189,248,0.8)] group-hover:scale-110 transition-transform">
                        ${number}
                    </div>
                </div>
            `;
        }

        function renderMonstersZone(options) {
            const container = document.getElementById('monsters-zone');
            container.innerHTML = '';

            options.forEach((num, idx) => {
                const wrapper = document.createElement('div');
                wrapper.innerHTML = createMonsterSVG(num, idx);
                container.appendChild(wrapper.firstElementChild);
            });
        }

        /* ===================================================================
         * Battle Shoot & Hit Detection
         * =================================================================== */
        function shootMonster(element, chosenNumber) {
            playSound('zap');
            gameState.totalAttempted++;

            const isCorrect = chosenNumber === gameState.currentProblem.correctAnswer;

            if (isCorrect) {
                // Correct Answer Hit!
                playSound('pop');
                element.classList.add('pop-defeat');

                gameState.score += 100 + (gameState.combo * 20);
                gameState.combo++;
                gameState.totalCorrect++;

                updateHeaderUI();

                // Check Wave Progression
                setTimeout(() => {
                    if (gameState.currentWave < gameState.totalWaves) {
                        gameState.currentWave++;
                        updateHeaderUI();
                        loadNextProblem();
                    } else {
                        // Game Victory!
                        showSummaryModal(true);
                    }
                }, 600);

            } else {
                // Wrong Answer Hit!
                playSound('wrong');
                element.classList.add('shake-err');
                setTimeout(() => element.classList.remove('shake-err'), 500);

                gameState.combo = 0;
                gameState.lives--;

                updateHeaderUI();

                // Auto Open Visualizer Helper if stuck!
                setTimeout(() => {
                    toggleVisualizer(true);
                }, 400);

                if (gameState.lives <= 0) {
                    setTimeout(() => showSummaryModal(false), 800);
                }
            }
        }

        function triggerHeroWiggle() {
            playSound('zap');
            const hero = document.getElementById('player-hero');
            hero.classList.add('scale-125');
            setTimeout(() => hero.classList.remove('scale-125'), 200);
        }

        /* ===================================================================
         * Borrowing Visualizer Logic ("退位拆十魔法")
         * =================================================================== */
        function toggleVisualizer(forceOpen = false) {
            initAudio();
            const drawer = document.getElementById('visualizer-drawer');
            if (forceOpen) {
                drawer.classList.remove('translate-y-full');
            } else {
                drawer.classList.toggle('translate-y-full');
            }
        }

        function syncVisualizerData() {
            const p = gameState.currentProblem;
            document.getElementById('viz-total-text').innerText = `題目: ${p.minuend} - ${p.subtrahend}`;
            
            // Standard Minuend & Subtrahend
            document.getElementById('viz-m-tens').innerText = p.mTens;
            document.getElementById('viz-m-ones').innerText = p.mOnes;
            document.getElementById('viz-s-tens').innerText = p.sTens;
            document.getElementById('viz-s-ones').innerText = p.sOnes;

            document.getElementById('viz-res-tens').innerText = '?';
            document.getElementById('viz-res-ones').innerText = '?';

            // Reset Slash & Borrow Overlays
            document.getElementById('viz-m-tens').classList.remove('slash-mark', 'text-slate-500');
            document.getElementById('viz-m-ones').classList.remove('slash-mark', 'text-slate-500');
            
            document.getElementById('viz-borrow-tens').classList.add('opacity-0');
            document.getElementById('viz-borrow-ones').classList.add('opacity-0');

            document.getElementById('viz-step-explain').innerText = `個位 ${p.mOnes} 不夠減 ${p.sOnes}！點擊下方按鈕拆開1個十！`;

            renderVisualizerBlocks(p.mTens, p.mOnes);
        }

        function renderVisualizerBlocks(tensCount, onesCount) {
            const tensContainer = document.getElementById('viz-tens-container');
            const onesContainer = document.getElementById('viz-ones-container');

            tensContainer.innerHTML = '';
            onesContainer.innerHTML = '';

            // Render Ten-Bars (十棒)
            for (let i = 0; i < tensCount; i++) {
                const bar = document.createElement('div');
                bar.className = 'w-4 h-16 bg-gradient-to-t from-indigo-600 to-cyan-400 rounded-sm border border-cyan-200 shadow flex flex-col justify-between p-0.5';
                bar.innerHTML = Array(10).fill(0).map(() => `<div class="w-full h-1 bg-indigo-950/40 rounded-xs"></div>`).join('');
                tensContainer.appendChild(bar);
            }

            // Render Single Cubes (一塊)
            for (let i = 0; i < onesCount; i++) {
                const cube = document.createElement('div');
                cube.className = 'w-4 h-4 bg-gradient-to-tr from-pink-500 to-rose-400 rounded-xs border border-pink-200 shadow-sm animate-pop';
                onesContainer.appendChild(cube);
            }
        }

        function executeBreakTenVisualizer() {
            if (gameState.visualizerBorrowed) return;

            playSound('magic');
            gameState.visualizerBorrowed = true;
            const p = gameState.currentProblem;

            // 1. Update Blocks (Tens -1, Ones +10)
            renderVisualizerBlocks(p.mTens - 1, p.mOnes + 10);

            // 2. Column Notation Slash Animation
            document.getElementById('viz-m-tens').classList.add('slash-mark', 'text-slate-500');
            document.getElementById('viz-m-ones').classList.add('slash-mark', 'text-slate-500');

            const borrowTens = document.getElementById('viz-borrow-tens');
            const borrowOnes = document.getElementById('viz-borrow-ones');

            borrowTens.innerText = p.mTens - 1;
            borrowOnes.innerText = p.mOnes + 10;

            borrowTens.classList.remove('opacity-0');
            borrowOnes.classList.remove('opacity-0');

            // 3. Show Calculated Column Result
            const ansOnes = (p.mOnes + 10) - p.sOnes;
            const ansTens = (p.mTens - 1) - p.sTens;

            document.getElementById('viz-res-ones').innerText = ansOnes;
            document.getElementById('viz-res-tens').innerText = ansTens;

            document.getElementById('viz-step-explain').innerHTML = `
                ✨ 魔法拆十成功！<br>
                個位：<span class="text-pink-400 font-black">${p.mOnes + 10} - ${p.sOnes} = ${ansOnes}</span> | 
                十位：<span class="text-indigo-400 font-black">${p.mTens - 1} - ${p.sTens} = ${ansTens}</span><br>
                答案是 <span class="text-amber-400 text-sm font-black">${p.correctAnswer}</span>！
            `;
        }

        /* ===================================================================
         * Summary & Victory Modal
         * =================================================================== */
        function showSummaryModal(isVictory) {
            playSound('fanfare');
            const modal = document.getElementById('summary-modal');
            const title = document.getElementById('modal-title');
            const subtext = document.getElementById('modal-subtext');
            const icon = document.getElementById('modal-icon');

            modal.classList.remove('hidden');

            if (isVictory) {
                icon.innerText = '🏆';
                title.innerText = '退位減法大獲全勝！';
                subtext.innerText = '你太厲害了！成功解救了所有數學能量！';
            } else {
                icon.innerText = '👾';
                title.innerText = '再接再勵！';
                subtext.innerText = '怪獸稍微佔了上風，利用「拆十魔法」再試一次！';
            }

            document.getElementById('modal-score').innerText = gameState.score;
            
            const accuracy = gameState.totalAttempted > 0 
                ? Math.round((gameState.totalCorrect / gameState.totalAttempted) * 100) 
                : 0;
            document.getElementById('modal-accuracy').innerText = `${accuracy}%`;

            // Render Stars
            const starsContainer = document.getElementById('modal-stars');
            let starCount = 1;
            if (isVictory && accuracy >= 80) starCount = 3;
            else if (isVictory || accuracy >= 50) starCount = 2;

            starsContainer.innerHTML = Array(3).fill(0).map((_, i) => 
                `<span class="${i < starCount ? 'text-amber-400' : 'text-slate-600'}">⭐</span>`
            ).join('');
        }

        function restartFullGame() {
            document.getElementById('summary-modal').classList.add('hidden');
            toggleVisualizer(false);
            initGame();
        }

        // Start Game on Page Load
        window.onload = () => {
            initGame();
        };
    </script>
</body>
</html>
