<!DOCTYPE html>
<html lang="zh-HK">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>《退位減法怪獸大作戰》</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@600;700&family=Noto+Sans+TC:wght@700;900&display=swap" rel="stylesheet">
    
    <style>
        html, body {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            touch-action: manipulation;
            user-select: none;
            -webkit-user-select: none;
            -webkit-touch-callout: none;
            font-family: 'Noto Sans TC', 'Fredoka', sans-serif;
            background-color: #0b0f19;
            color: #f8fafc;
        }

        .space-bg {
            background-color: #080c16;
            background-image: 
                radial-gradient(2px 2px at 20px 30px, #e2e8f0, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 40px 70px, #38bdf8, rgba(0,0,0,0)),
                radial-gradient(3px 3px at 80px 120px, #f472b6, rgba(0,0,0,0)),
                radial-gradient(2px 2px at 150px 180px, #e2e8f0, rgba(0,0,0,0)),
                radial-gradient(3px 3px at 220px 250px, #fbbf24, rgba(0,0,0,0));
            background-size: 320px 320px;
            animation: spaceMove 45s linear infinite;
        }

        @keyframes spaceMove {
            from { background-position: 0 0; }
            to { background-position: 1000px 1000px; }
        }

        /* Floating Monster Animation */
        .monster-float {
            animation: monsterFloat 3.2s ease-in-out infinite alternate;
        }

        @keyframes monsterFloat {
            0% { transform: translateY(0px) rotate(-1.5deg); }
            50% { transform: translateY(-10px) rotate(1.5deg); }
            100% { transform: translateY(0px) rotate(-1.5deg); }
        }

        .shake-err {
            animation: shakeError 0.45s cubic-bezier(.36,.07,.19,.97) both;
        }

        @keyframes shakeError {
            10%, 90% { transform: translate3d(-5px, 0, 0) scale(0.96); }
            20%, 80% { transform: translate3d(8px, 0, 0) scale(1.04); }
            30%, 50%, 70% { transform: translate3d(-10px, 0, 0); }
            40%, 60% { transform: translate3d(10px, 0, 0); }
        }

        /* Defeat Blast Pop Animation */
        .pop-defeat {
            animation: popDefeat 0.35s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        @keyframes popDefeat {
            0% { transform: scale(1) rotate(0deg); opacity: 1; }
            50% { transform: scale(1.35) rotate(12deg); opacity: 0.8; }
            100% { transform: scale(0) rotate(45deg); opacity: 0; }
        }

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
            border-radius: 999px;
            box-shadow: 0 0 8px rgba(244, 63, 94, 0.8);
            animation: strokeSlash 0.25s ease-out forwards;
        }

        @keyframes strokeSlash {
            from { width: 0%; opacity: 0; }
            to { width: 60%; opacity: 1; }
        }

        /* Custom Scrollbar for Visualizer Modal */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-thumb {
            background: #38bdf8;
            border-radius: 999px;
        }
        ::-webkit-scrollbar-track {
            background: #0f172a;
        }
    </style>
</head>
<body class="space-bg h-screen w-screen flex flex-col justify-between p-2 sm:p-4 md:p-6 relative overflow-hidden select-none">

    <header class="w-full max-w-6xl mx-auto bg-slate-900/90 backdrop-blur-md border-2 border-indigo-500/50 rounded-2xl p-2.5 sm:p-3.5 shadow-2xl flex items-center justify-between z-20 shrink-0">
        
        <!-- Left: Game Title & Level Badge -->
        <div class="flex items-center space-x-2.5">
            <div class="bg-gradient-to-tr from-indigo-600 to-cyan-500 text-white p-2 rounded-xl text-xl sm:text-2xl shadow-lg border border-cyan-300/40">
                🚀
            </div>
            <div>
                <h1 class="text-sm sm:text-lg md:text-xl font-black text-transparent bg-clip-text bg-gradient-to-r from-cyan-300 via-sky-200 to-indigo-300 tracking-wide">
                    退位減法怪獸大作戰
                </h1>
                <div class="flex items-center space-x-2 mt-0.5">
                    <span id="wave-badge" class="bg-purple-900/80 text-purple-200 text-[10px] sm:text-xs font-bold px-2 py-0.5 rounded-md border border-purple-500/40">
                        關卡 1 / 5
                    </span>
                    <span id="combo-badge" class="bg-amber-500/20 text-amber-300 text-[10px] sm:text-xs font-black px-2 py-0.5 rounded-md border border-amber-500/40 hidden">
                        🔥 連擊 x0
                    </span>
                </div>
            </div>
        </div>

        <!-- Center: Health / Lives -->
        <div class="flex items-center space-x-1.5 bg-slate-950/80 px-3 py-1.5 rounded-xl border border-slate-800">
            <span class="text-xs font-bold text-slate-400 mr-1 hidden sm:inline">生命值:</span>
            <div id="lives-container" class="flex space-x-1 text-lg sm:text-2xl">
                <span>❤️</span><span>❤️</span><span>❤️</span>
            </div>
        </div>

        <div class="flex items-center space-x-2 sm:space-x-3">
            <div class="text-right">
                <span class="text-[9px] sm:text-[11px] font-bold text-slate-400 block uppercase tracking-wider">Score</span>
                <span id="score-text" class="text-lg sm:text-2xl font-black text-amber-400">0</span>
            </div>

            <!-- Regrouping Helper Tool Toggle Button -->
            <button onclick="toggleVisualizer()" class="bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-400 hover:to-teal-500 text-white text-xs sm:text-sm font-black px-2.5 sm:px-3.5 py-2 rounded-xl shadow-lg border border-emerald-300/50 active:scale-95 transition flex items-center space-x-1.5">
                <span class="text-base sm:text-lg">🧮</span>
                <span class="hidden sm:inline">拆十魔法</span>
            </button>

            <!-- Audio Mute Toggle Button -->
            <button id="sound-btn" onclick="toggleSound()" class="bg-slate-800 hover:bg-slate-700 text-slate-200 text-base sm:text-lg p-2 rounded-xl border border-slate-700 active:scale-95">
                🔊
            </button>
        </div>
    </header>

    <main class="w-full max-w-6xl mx-auto flex-1 flex flex-col justify-between my-2 sm:my-3 relative z-10 overflow-hidden">
        
        <!-- Target Math Equation Banner -->
        <div class="w-full bg-slate-900/90 border-2 border-cyan-500/50 rounded-2xl sm:rounded-3xl p-2.5 sm:p-4 shadow-2xl flex flex-col sm:flex-row items-center justify-between gap-2.5 relative overflow-hidden shrink-0">
            <div class="absolute inset-0 bg-gradient-to-r from-cyan-500/10 via-indigo-500/10 to-purple-500/10 pointer-events-none"></div>

            <div class="flex items-center space-x-2.5 z-10">
                <span class="text-2xl sm:text-4xl animate-pulse">🎯</span>
                <div>
                    <span class="text-[11px] sm:text-xs font-bold text-cyan-400 uppercase tracking-widest block">目標二位數退位減法</span>
                    <span class="text-[11px] sm:text-xs text-slate-400">請選出帶有正確答案的怪獸擊退牠！</span>
                </div>
            </div>

            <!-- Math Problem Board -->
            <div class="bg-slate-950 border-2 border-cyan-400/60 rounded-xl sm:rounded-2xl px-5 sm:px-8 py-1.5 sm:py-2.5 shadow-inner z-10 flex items-center space-x-3">
                <span id="problem-text" class="text-2xl sm:text-4xl md:text-5xl font-black tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-cyan-300 via-white to-pink-300">
                    52 - 27 = ?
                </span>
            </div>

            <div class="text-[11px] sm:text-xs font-extrabold text-amber-300 bg-amber-950/70 border border-amber-500/40 px-3 py-1.5 rounded-xl z-10 flex items-center space-x-1">
                <span>💡 提示:</span>
                <span id="quick-hint-text">個位 2 不夠減 7，記得向十位借 1！</span>
            </div>
        </div>

        <div id="monsters-zone" class="flex-1 my-2 sm:my-4 grid grid-cols-2 md:grid-cols-4 gap-3 sm:gap-4 items-center justify-items-center relative min-h-[200px]">
            <!-- Monsters dynamically rendered here -->
        </div>

        <!-- Bottom Defense Magic Wizard / Cannon Base -->
        <div class="w-full flex justify-center items-center relative shrink-0">
            <div id="player-hero" class="relative group cursor-pointer" onclick="triggerHeroWiggle()">
                <div class="w-16 h-16 sm:w-20 sm:h-20 bg-gradient-to-t from-indigo-700 via-purple-600 to-pink-500 rounded-full border-4 border-cyan-300 shadow-[0_0_25px_rgba(56,189,248,0.6)] flex items-center justify-center text-3xl sm:text-4xl relative active:scale-95 transition-transform">
                    🧙‍♂️
                    <div class="absolute -top-2 bg-cyan-400 text-slate-950 text-[9px] sm:text-[10px] font-black px-2 py-0.5 rounded-full shadow border border-white">
                        數學救星
                    </div>
                </div>
            </div>
        </div>
    </main>

    <div id="visualizer-drawer" class="fixed inset-x-0 bottom-0 max-w-4xl mx-auto bg-slate-900/98 backdrop-blur-2xl border-t-4 border-x-4 border-emerald-400 rounded-t-3xl shadow-[0_-15px_50px_rgba(16,185,129,0.35)] z-40 transform translate-y-full transition-transform duration-300 ease-out p-3 sm:p-5 md:p-6 text-slate-100 max-h-[88vh] overflow-y-auto">
        
        <!-- Drawer Header -->
        <div class="flex justify-between items-center mb-3 border-b border-slate-700 pb-2.5">
            <div class="flex items-center space-x-2">
                <span class="text-2xl sm:text-3xl">🧮</span>
                <div>
                    <h2 class="text-base sm:text-xl font-black text-emerald-400">退位拆十魔法輔具</h2>
                    <p class="text-[11px] sm:text-xs text-slate-400">將 1 個「十棒」拆開成 10 個「一塊」，理解借位過程！</p>
                </div>
            </div>
            <button onclick="toggleVisualizer()" class="text-slate-400 hover:text-white bg-slate-800 p-1.5 sm:p-2 rounded-xl text-base border border-slate-700 active:scale-95">
                ✖
            </button>
        </div>

        <!-- Visualizer Content Grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 sm:gap-6 items-stretch">
            
            <div class="bg-slate-950 p-3 sm:p-4 rounded-2xl border border-slate-800 flex flex-col justify-between min-h-[220px]">
                <div class="flex justify-between items-center mb-2">
                    <span class="text-xs font-bold text-emerald-400">積木圖像化對照</span>
                    <span id="viz-total-text" class="text-xs bg-slate-800 px-2.5 py-1 rounded-lg text-slate-300 font-bold">總數: 52</span>
                </div>

                <div class="grid grid-cols-2 gap-2.5 flex-1 my-2">
                    <!-- Tens Column (十位棒) -->
                    <div class="bg-slate-900/90 p-2.5 rounded-xl border border-indigo-500/30 flex flex-col items-center">
                        <span class="text-xs font-black text-indigo-400 mb-2">十位 (十棒)</span>
                        <div id="viz-tens-container" class="flex flex-wrap justify-center gap-1.5 min-h-[110px] items-center">
                            <!-- Tens bars generated here -->
                        </div>
                    </div>

                    <!-- Ones Column (一塊) -->
                    <div class="bg-slate-900/90 p-2.5 rounded-xl border border-pink-500/30 flex flex-col items-center">
                        <span class="text-xs font-black text-pink-400 mb-2">個位 (一塊)</span>
                        <div id="viz-ones-container" class="flex flex-wrap justify-center gap-1.5 min-h-[110px] items-center">
                            <!-- Ones cubes generated here -->
                        </div>
                    </div>
                </div>

                <!-- Break Ten Action Button -->
                <button id="break-ten-btn" onclick="executeBreakTenVisualizer()" class="mt-2 w-full bg-gradient-to-r from-amber-500 to-rose-500 hover:from-amber-400 hover:to-rose-400 text-slate-950 font-black py-2.5 sm:py-3 rounded-xl shadow-lg border border-amber-300/60 text-xs sm:text-sm active:scale-95 transition flex items-center justify-center space-x-2">
                    <span>✨ 點擊執行「魔法拆十」 (1個十 ➔ 10個一)</span>
                </button>
            </div>

            <div class="bg-slate-950 p-3 sm:p-4 rounded-2xl border border-slate-800 flex flex-col items-center justify-center">
                <span class="text-xs font-bold text-slate-400 mb-2">直式退位紀錄 (劃線借位)</span>
                
                <div class="bg-slate-900 border-2 border-indigo-500/40 rounded-2xl p-4 min-w-[210px] relative font-mono shadow-inner">
                    
                    <!-- Column Headers -->
                    <div class="grid grid-cols-2 text-center text-xs font-black pb-1.5 border-b border-slate-800 mb-2">
                        <span class="text-indigo-400">十位</span>
                        <span class="text-pink-400">個位</span>
                    </div>

                    <!-- Borrow Adjustment Row -->
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
                        <span class="absolute -left-1 top-0 text-rose-500 font-sans">-</span>
                        <span id="viz-s-tens">2</span>
                        <span id="viz-s-ones">7</span>
                    </div>

                    <!-- Subtraction Column Result Row -->
                    <div class="grid grid-cols-2 text-center text-3xl font-black text-amber-400">
                        <span id="viz-res-tens">?</span>
                        <span id="viz-res-ones">?</span>
                    </div>
                </div>

                <p id="viz-step-explain" class="text-xs text-amber-300 font-bold mt-3 text-center bg-amber-950/50 px-3 py-2 rounded-xl border border-amber-500/30 leading-relaxed">
                    點擊「魔法拆十」觀看個位與十位變化！
                </p>
            </div>
        </div>
    </div>

    <div id="summary-modal" class="fixed inset-0 bg-slate-950/85 backdrop-blur-md z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-slate-900 border-4 border-indigo-500 rounded-3xl p-6 max-w-md w-full text-center shadow-2xl relative overflow-hidden">
            <div id="modal-icon" class="text-6xl mb-2 animate-bounce">🏆</div>
            <h2 id="modal-title" class="text-2xl sm:text-3xl font-black text-transparent bg-clip-text bg-gradient-to-r from-amber-300 via-pink-400 to-purple-400 mb-2">
                挑戰成功！
            </h2>
            <p id="modal-subtext" class="text-xs sm:text-sm text-slate-300 font-bold mb-4">你成功擊退了所有退位減法怪獸！</p>

            <!-- Star Rating Display -->
            <div id="modal-stars" class="flex justify-center space-x-2 text-4xl mb-4">
                <span>⭐</span><span>⭐</span><span>⭐</span>
            </div>

            <!-- Stats Box -->
            <div class="bg-slate-950 rounded-2xl p-4 border border-slate-800 mb-5 grid grid-cols-2 gap-2 text-left">
                <div>
                    <span class="text-[11px] text-slate-400 font-bold block">最終得分</span>
                    <span id="modal-score" class="text-xl font-black text-amber-400">1200</span>
                </div>
                <div>
                    <span class="text-[11px] text-slate-400 font-bold block">答對率</span>
                    <span id="modal-accuracy" class="text-xl font-black text-emerald-400">100%</span>
                </div>
            </div>

            <!-- Modal Action Controls -->
            <button onclick="restartFullGame()" class="w-full bg-gradient-to-r from-indigo-600 via-purple-600 to-pink-600 hover:from-indigo-500 hover:to-pink-500 text-white font-black py-3.5 rounded-2xl border border-indigo-400/50 shadow-lg active:scale-95 transition text-base">
                再玩一次 🔄
            </button>
        </div>
    </div>

    <script>
        /* ===================================================================
         * Web Audio API Synthesizer (Zero external sound dependency)
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
                // Laser Cannon Beam Sound
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(850, now);
                osc.frequency.exponentialRampToValueAtTime(110, now + 0.15);
                gain.gain.setValueAtTime(0.25, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.15);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.15);
            } 
            else if (type === 'pop') {
                // Monster Explosion Sound
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(160, now);
                osc.frequency.exponentialRampToValueAtTime(650, now + 0.12);
                gain.gain.setValueAtTime(0.35, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.12);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.12);
            } 
            else if (type === 'wrong') {
                // Incorrect Buzzer
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(190, now);
                osc.frequency.setValueAtTime(140, now + 0.1);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.25);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 0.25);
            }
            else if (type === 'magic') {
                // Borrowing Magic Break Sound (Arpeggio)
                [523.25, 659.25, 783.99, 1046.50].forEach((freq, idx) => {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.04);
                    gain.gain.setValueAtTime(0.2, now + idx * 0.04);
                    gain.gain.linearRampToValueAtTime(0.01, now + idx * 0.04 + 0.14);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now + idx * 0.04);
                    osc.stop(now + idx * 0.04 + 0.14);
                });
            }
            else if (type === 'fanfare') {
                // Victory Fanfare Tune
                const notes = [440, 554.37, 659.25, 880];
                notes.forEach((freq, i) => {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, now + i * 0.11);
                    gain.gain.setValueAtTime(0.3, now + i * 0.11);
                    gain.gain.linearRampToValueAtTime(0.01, now + i * 0.11 + 0.28);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(now + i * 0.11);
                    osc.stop(now + i * 0.11 + 0.28);
                });
            }
        }

        function toggleSound() {
            isSoundMuted = !isSoundMuted;
            document.getElementById('sound-btn').innerText = isSoundMuted ? '🔇' : '🔊';
        }

        /* ===================================================================
         * Pedagogical Grade 2 Regrouping Subtraction Engine
         * =================================================================== */
        function generateBorrowingProblem() {
            // Minuend Ones (0 ~ 5)
            const mOnes = Math.floor(Math.random() * 6); 
            // Subtrahend Ones (mOnes + 3 ~ 9) -> Guarantees borrowing is strictly required!
            const sOnes = mOnes + Math.floor(Math.random() * 3) + 3;

            // Minuend Tens (3 ~ 9)
            const mTens = Math.floor(Math.random() * 7) + 3;
            // Subtrahend Tens (1 ~ mTens - 1)
            const sTens = Math.floor(Math.random() * (mTens - 2)) + 1;

            const minuend = mTens * 10 + mOnes;
            const subtrahend = sTens * 10 + sOnes;
            const correctAnswer = minuend - subtrahend;

            // Generate distractors based on authentic Grade 2 student misconceptions
            const options = new Set();
            options.add(correctAnswer);

            // Misconception 1: Flipped Subtraction (Bottom minus Top in ones: sOnes - mOnes)
            const flippedOnes = sOnes - mOnes;
            const distractorFlipped = (mTens - sTens) * 10 + flippedOnes;
            if (distractorFlipped > 0 && distractorFlipped !== correctAnswer) {
                options.add(distractorFlipped);
            }

            // Misconception 2: Forgot to decrement Tens place (Borrowed 10 to ones, but kept original mTens - sTens)
            const correctOnes = (mOnes + 10) - sOnes;
            const distractorForgotDec = (mTens - sTens) * 10 + correctOnes;
            if (distractorForgotDec > 0 && distractorForgotDec !== correctAnswer) {
                options.add(distractorForgotDec);
            }

            // Misconception 3: Off-by-10 calculation error
            const distractorOff10 = correctAnswer + 10;
            if (distractorOff10 < 100 && distractorOff10 !== correctAnswer) {
                options.add(distractorOff10);
            }

            const distractorOff1 = correctAnswer - 1 > 0 ? correctAnswer - 1 : correctAnswer + 2;
            options.add(distractorOff1);

            // Convert set to array and pad if needed
            const shuffledOptions = Array.from(options).slice(0, 4);
            while (shuffledOptions.length < 4) {
                const randomOffset = Math.floor(Math.random() * 20) + 5;
                if (!shuffledOptions.includes(randomOffset)) shuffledOptions.push(randomOffset);
            }

            // Shuffle option list
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
         * Game State Controller
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
            
            // Render Lives Hearts
            const livesContainer = document.getElementById('lives-container');
            livesContainer.innerHTML = '';
            for (let i = 0; i < 3; i++) {
                const heart = document.createElement('span');
                heart.innerText = i < gameState.lives ? '❤️' : '🖤';
                livesContainer.appendChild(heart);
            }

            // Render Combo Streak Badge
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

            const p = gameState.currentProblem;
            document.getElementById('problem-text').innerText = `${p.minuend} - ${p.subtrahend} = ?`;
            document.getElementById('quick-hint-text').innerText = `個位 ${p.mOnes} 不夠減 ${p.sOnes}，記得向十位借 1！`;

            renderMonstersZone(p.options);
            syncVisualizerData();
        }

        function createMonsterSVG(number, styleIndex) {
            const style = monsterStyles[styleIndex % monsterStyles.length];
            return `
                <div class="monster-card relative flex flex-col items-center cursor-pointer monster-float group active:scale-90 transition-transform" onclick="shootMonster(this, ${number})">
                    <!-- SVG Monster Avatar -->
                    <div class="w-20 h-20 sm:w-28 sm:h-28 md:w-32 md:h-32 filter drop-shadow-[0_8px_15px_rgba(0,0,0,0.6)]">
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

                    <!-- Carrying Answer Orb -->
                    <div class="absolute -bottom-1 bg-gradient-to-r from-cyan-400 to-indigo-500 text-slate-950 font-black text-xl sm:text-2xl px-3.5 sm:px-5 py-0.5 sm:py-1 rounded-full border-2 border-white shadow-[0_0_15px_rgba(56,189,248,0.8)] group-hover:scale-110 transition-transform">
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
         * Battle Shoot & Hit Logic
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

                setTimeout(() => {
                    if (gameState.currentWave < gameState.totalWaves) {
                        gameState.currentWave++;
                        updateHeaderUI();
                        loadNextProblem();
                    } else {
                        showSummaryModal(true);
                    }
                }, 550);

            } else {
                // Wrong Answer Hit!
                playSound('wrong');
                element.classList.add('shake-err');
                setTimeout(() => element.classList.remove('shake-err'), 450);

                gameState.combo = 0;
                gameState.lives--;

                updateHeaderUI();

                // Open Regrouping Visualizer Tool automatically when stuck
                setTimeout(() => {
                    toggleVisualizer(true);
                }, 400);

                if (gameState.lives <= 0) {
                    setTimeout(() => showSummaryModal(false), 750);
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
         * Regrouping Visualizer Tool Logic ("退位拆十魔法")
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
            
            // Standard minuend and subtrahend display
            document.getElementById('viz-m-tens').innerText = p.mTens;
            document.getElementById('viz-m-ones').innerText = p.mOnes;
            document.getElementById('viz-s-tens').innerText = p.sTens;
            document.getElementById('viz-s-ones').innerText = p.sOnes;

            document.getElementById('viz-res-tens').innerText = '?';
            document.getElementById('viz-res-ones').innerText = '?';

            // Reset slashes and borrow markers
            document.getElementById('viz-m-tens').classList.remove('slash-mark', 'text-slate-500');
            document.getElementById('viz-m-ones').classList.remove('slash-mark', 'text-slate-500');
            
            document.getElementById('viz-borrow-tens').classList.add('opacity-0');
            document.getElementById('viz-borrow-ones').classList.add('opacity-0');

            document.getElementById('viz-step-explain').innerText = `個位 ${p.mOnes} 不夠減 ${p.sOnes}！點擊下方按鈕拆開 1 個十！`;

            renderVisualizerBlocks(p.mTens, p.mOnes);
        }

        function renderVisualizerBlocks(tensCount, onesCount) {
            const tensContainer = document.getElementById('viz-tens-container');
            const onesContainer = document.getElementById('viz-ones-container');

            tensContainer.innerHTML = '';
            onesContainer.innerHTML = '';

            // Render Tens Rods (十棒)
            for (let i = 0; i < tensCount; i++) {
                const bar = document.createElement('div');
                bar.className = 'w-3.5 sm:w-4 h-14 sm:h-16 bg-gradient-to-t from-indigo-600 to-cyan-400 rounded-xs border border-cyan-200 shadow flex flex-col justify-between p-0.5';
                bar.innerHTML = Array(10).fill(0).map(() => `<div class="w-full h-0.5 bg-indigo-950/40 rounded-xs"></div>`).join('');
                tensContainer.appendChild(bar);
            }

            // Render Ones Cubes (一塊)
            for (let i = 0; i < onesCount; i++) {
                const cube = document.createElement('div');
                cube.className = 'w-3.5 h-3.5 sm:w-4 sm:h-4 bg-gradient-to-tr from-pink-500 to-rose-400 rounded-xs border border-pink-200 shadow-sm animate-pop';
                onesContainer.appendChild(cube);
            }
        }

        function executeBreakTenVisualizer() {
            if (gameState.visualizerBorrowed) return;

            playSound('magic');
            gameState.visualizerBorrowed = true;
            const p = gameState.currentProblem;

            // 1. Update Manipulatives (Tens -1, Ones +10)
            renderVisualizerBlocks(p.mTens - 1, p.mOnes + 10);

            // 2. Add Column Slash Notation
            document.getElementById('viz-m-tens').classList.add('slash-mark', 'text-slate-500');
            document.getElementById('viz-m-ones').classList.add('slash-mark', 'text-slate-500');

            const borrowTens = document.getElementById('viz-borrow-tens');
            const borrowOnes = document.getElementById('viz-borrow-ones');

            borrowTens.innerText = p.mTens - 1;
            borrowOnes.innerText = p.mOnes + 10;

            borrowTens.classList.remove('opacity-0');
            borrowOnes.classList.remove('opacity-0');

            // 3. Compute Column Subtraction Results
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
         * Summary Modal & Restart Controller
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
                subtext.innerText = '怪獸佔了上風，利用「拆十魔法」再試一次！';
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
