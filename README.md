<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blooket 1K Simulator & Multi-Game Arcade</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=Nunito:wght@400;700;900&display=swap" rel="stylesheet">
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Canvas Confetti Library -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    fontFamily: {
                        fredoka: ['Fredoka', 'sans-serif'],
                        nunito: ['Nunito', 'sans-serif'],
                    },
                    colors: {
                        blooket: {
                            purple: '#4c1d95',
                            indigo: '#312e81',
                            card: '#1e1b4b',
                            accent: '#0284c7',
                            gold: '#fbbf24',
                            supreme: '#ec4899',
                        }
                    }
                }
            }
        }
    </script>

    <style>
        body {
            font-family: 'Nunito', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
            user-select: none;
            touch-action: manipulation;
        }

        .font-fredoka { font-family: 'Fredoka', sans-serif; }

        /* Aidan Animated Card Effect */
        .aidan-card {
            background: linear-gradient(135deg, #ff007f, #7928ca, #00dfd8, #f59e0b, #ff007f);
            background-size: 400% 400%;
            animation: aidanGradient 4s ease infinite, aidanGlow 1.5s ease-in-out infinite alternate;
            border: 3px solid #fbbf24 !important;
            box-shadow: 0 0 25px rgba(251, 191, 36, 0.6), inset 0 0 15px rgba(255, 255, 255, 0.4);
        }

        @keyframes aidanGradient {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        @keyframes aidanGlow {
            from { transform: scale(1); filter: brightness(1); }
            to { transform: scale(1.03); filter: brightness(1.25); }
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #0f172a; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #475569; }

        /* Rarity Glows */
        .glow-common { box-shadow: 0 0 10px rgba(148, 163, 184, 0.3); }
        .glow-uncommon { box-shadow: 0 0 10px rgba(34, 197, 94, 0.4); }
        .glow-rare { box-shadow: 0 0 12px rgba(59, 130, 246, 0.5); }
        .glow-epic { box-shadow: 0 0 15px rgba(168, 85, 247, 0.6); }
        .glow-legendary { box-shadow: 0 0 18px rgba(245, 158, 11, 0.7); }
        .glow-chroma { box-shadow: 0 0 20px rgba(236, 72, 153, 0.8); }
        .glow-mystical { box-shadow: 0 0 22px rgba(239, 68, 68, 0.9); }

        /* MINI GAME STYLES */
        #game-container {
            position: relative;
            width: 100%;
            max-width: 800px;
            height: 550px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            border-radius: 20px;
            overflow: hidden;
            background: linear-gradient(to bottom, #0f2027, #203a43, #2c5364);
            margin: 0 auto;
        }

        #gameCanvas { display: block; width: 100%; height: 100%; }

        .hud {
            position: absolute;
            top: 15px;
            left: 15px;
            right: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            pointer-events: none;
            z-index: 5;
        }

        .hud-card {
            background: rgba(0, 0, 0, 0.7);
            backdrop-filter: blur(6px);
            padding: 8px 14px;
            border-radius: 12px;
            border: 1px solid rgba(255, 255, 255, 0.15);
            font-size: 13px;
            font-weight: 600;
        }

        .hud-card span { color: #4ecca3; }
        .hud-card .balance-span { color: #f9d423; }

        .energy-bar-container {
            width: 110px;
            height: 10px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 6px;
            overflow: hidden;
            margin-top: 4px;
        }

        .energy-bar {
            height: 100%;
            width: 100%;
            background: linear-gradient(90deg, #ff416c, #ff4b2b, #f9d423);
            transition: width 0.1s linear;
        }

        /* Question Overlay */
        .quiz-overlay {
            position: absolute;
            inset: 0;
            background: rgba(15, 23, 42, 0.95);
            backdrop-filter: blur(8px);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
            z-index: 25;
        }

        .quiz-card {
            background: #1e293b;
            border: 2px solid #3b82f6;
            border-radius: 20px;
            padding: 24px;
            width: 100%;
            max-width: 440px;
            text-align: center;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.6);
        }

        .options-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-top: 16px;
        }

        .btn-opt {
            background: #334155;
            color: white;
            border: 2px solid #475569;
            padding: 12px;
            border-radius: 12px;
            font-size: 15px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s;
        }

        .btn-opt:hover {
            background: #2563eb;
            border-color: #60a5fa;
            transform: translateY(-2px);
        }

        #quiz-btn {
            position: absolute;
            bottom: 20px;
            right: 20px;
            background: #e94560;
            color: white;
            border: none;
            padding: 12px 22px;
            font-size: 14px;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(233, 69, 96, 0.4);
            transition: all 0.2s;
            z-index: 5;
        }

        #quiz-btn:hover { transform: scale(1.05); background: #ff5277; }

        .controls-tip {
            position: absolute;
            bottom: 20px;
            left: 20px;
            background: rgba(0, 0, 0, 0.6);
            padding: 8px 14px;
            border-radius: 8px;
            font-size: 12px;
            color: #ccc;
            pointer-events: none;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-950 text-slate-100 selection:bg-purple-500 selection:text-white">

    <!-- TOP HEADER BAR -->
    <header class="sticky top-0 z-40 bg-slate-900/90 backdrop-blur-md border-b border-slate-800 px-4 py-3 shadow-lg transition-all duration-300">
        <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-3">
            
            <div class="flex items-center justify-between w-full sm:w-auto gap-3">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-purple-600 to-pink-500 flex items-center justify-center font-fredoka text-2xl font-bold shadow-md shadow-purple-500/20">
                        👑
                    </div>
                    <div>
                        <h1 class="font-fredoka text-2xl font-bold bg-gradient-to-r from-purple-400 via-pink-400 to-amber-300 bg-clip-text text-transparent">
                            BLOOKET 1K SIM
                        </h1>
                        <p id="header-subtitle" class="text-xs text-slate-400 font-semibold">1,000 Blooks • 3 Arcade Games • Supreme "Aidan"</p>
                    </div>
                </div>

                <button onclick="toggleHeaderCollapse()" class="flex items-center gap-2 bg-slate-800 hover:bg-slate-700 text-slate-300 px-3 py-1.5 rounded-xl font-fredoka text-xs font-bold transition-all border border-slate-700">
                    <span id="header-toggle-text">Collapse</span>
                    <i id="header-toggle-icon" class="fa-solid fa-chevron-up text-xs"></i>
                </button>
            </div>

            <div id="header-stats-container" class="flex items-center gap-3 transition-all duration-300">
                <div class="flex items-center gap-2 bg-slate-800/90 border border-slate-700/80 px-4 py-1.5 rounded-2xl shadow-inner">
                    <span class="text-xl">🪙</span>
                    <span id="stat-tokens" class="font-fredoka text-xl font-bold text-amber-400">5,000</span>
                    <span class="text-xs text-slate-400 uppercase font-bold">Tokens</span>
                </div>

                <div class="flex items-center gap-2 bg-slate-800/90 border border-slate-700/80 px-4 py-1.5 rounded-2xl shadow-inner">
                    <span class="text-xl">🎒</span>
                    <div>
                        <div class="font-fredoka text-base font-bold text-sky-400 flex items-center gap-1">
                            <span id="stat-unlocked">0</span> / 1000
                            <span id="stat-percent" class="text-xs text-slate-400 font-normal">(0%)</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <!-- TAB NAVIGATION BAR -->
    <nav class="bg-slate-900 border-b border-slate-800 px-4 py-2 sticky top-[65px] z-30 shadow-md">
        <div class="max-w-4xl mx-auto flex flex-wrap items-center justify-center gap-2 sm:gap-3">
            <button id="nav-shop" onclick="switchTab('shop')" class="nav-btn active flex items-center gap-2 px-4 py-2 rounded-xl font-fredoka font-bold text-xs sm:text-sm transition-all duration-200 border-b-4 border-purple-800 bg-purple-600 text-white shadow-lg">
                <i class="fa-solid me-1 fa-store"></i> Pack Shop
            </button>
            <button id="nav-inventory" onclick="switchTab('inventory')" class="nav-btn flex items-center gap-2 px-4 py-2 rounded-xl font-fredoka font-bold text-xs sm:text-sm transition-all duration-200 border-b-4 border-slate-800 bg-slate-800 text-slate-300 hover:bg-slate-700">
                <i class="fa-solid me-1 fa-box-open"></i> My Blooks
            </button>
            <button id="nav-wheel" onclick="switchTab('wheel')" class="nav-btn flex items-center gap-2 px-4 py-2 rounded-xl font-fredoka font-bold text-xs sm:text-sm transition-all duration-200 border-b-4 border-slate-800 bg-slate-800 text-slate-300 hover:bg-slate-700">
                <i class="fa-solid me-1 fa-dharmachakra"></i> Hourly Wheel
            </button>
            <button id="nav-minigame" onclick="switchTab('minigame')" class="nav-btn flex items-center gap-2 px-4 py-2 rounded-xl font-fredoka font-bold text-xs sm:text-sm transition-all duration-200 border-b-4 border-slate-800 bg-slate-800 text-slate-300 hover:bg-slate-700">
                <i class="fa-solid me-1 fa-gamepad"></i> Mini Games
            </button>
            <button id="nav-settings" onclick="switchTab('settings')" class="nav-btn flex items-center gap-2 px-3 py-2 rounded-xl font-fredoka font-bold text-xs sm:text-sm transition-all duration-200 border-b-4 border-slate-800 bg-slate-800 text-slate-300 hover:bg-slate-700">
                <i class="fa-solid fa-gear"></i>
            </button>
        </div>
    </nav>

    <!-- MAIN CONTENT AREA -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6">
        
        <!-- ================= TAB 1: SHOP ================= -->
        <section id="tab-shop" class="space-y-6">
            <div class="bg-gradient-to-r from-purple-900/60 to-slate-900 border border-purple-500/30 rounded-3xl p-6 shadow-xl flex flex-col md:flex-row items-center justify-between gap-4">
                <div>
                    <h2 class="font-fredoka text-2xl sm:text-3xl font-bold text-white flex items-center gap-2">
                        <span>📦</span> Open Packs & Collect Blooks
                    </h2>
                    <p class="text-slate-300 text-sm mt-1">Explore 50 themed packs! Pull the mythical 0.001% Supreme Blook: <strong class="text-amber-300">Aidan 👑✨</strong></p>
                </div>
                <div class="flex items-center gap-3 w-full md:w-auto">
                    <div class="relative w-full md:w-64">
                        <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400"></i>
                        <input type="text" id="shop-search" oninput="renderShop()" placeholder="Search packs..." class="w-full bg-slate-950 border border-slate-700 rounded-xl pl-10 pr-4 py-2 text-sm focus:outline-none focus:border-purple-500 text-white placeholder-slate-500">
                    </div>
                </div>
            </div>

            <div id="packs-container" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-4"></div>
        </section>

        <!-- ================= TAB 2: INVENTORY ================= -->
        <section id="tab-inventory" class="hidden space-y-6">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-5 shadow-xl space-y-4">
                <div class="flex flex-col md:flex-row items-center justify-between gap-4">
                    <div>
                        <h2 class="font-fredoka text-2xl font-bold text-white flex items-center gap-2">
                            <span>🎒</span> My Blooks Collection
                        </h2>
                        <p class="text-slate-400 text-sm">Manage, search, and sell duplicate Blooks for tokens.</p>
                    </div>

                    <div class="flex items-center gap-3 w-full md:w-auto">
                        <button onclick="sellAllDuplicates()" class="flex-1 md:flex-none px-4 py-2.5 rounded-xl font-fredoka font-bold text-sm bg-emerald-600 hover:bg-emerald-500 text-white border-b-4 border-emerald-800 active:translate-y-0.5 transition-all shadow-lg flex items-center justify-center gap-2">
                            <i class="fa-solid fa-coins"></i> Sell All Duplicates (<span id="sell-all-val">0</span> 🪙)
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-3 pt-2 border-t border-slate-800">
                    <input type="text" id="inv-search" oninput="renderInventory()" placeholder="Search Blook name..." class="bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-white focus:outline-none focus:border-purple-500">
                    
                    <select id="inv-filter-pack" onchange="renderInventory()" class="bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-white focus:outline-none focus:border-purple-500">
                        <option value="ALL">All Packs (50)</option>
                    </select>

                    <select id="inv-filter-rarity" onchange="renderInventory()" class="bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm text-white focus:outline-none focus:border-purple-500">
                        <option value="ALL">All Rarities</option>
                        <option value="Common">Common</option>
                        <option value="Uncommon">Uncommon</option>
                        <option value="Rare">Rare</option>
                        <option value="Epic">Epic</option>
                        <option value="Legendary">Legendary</option>
                        <option value="Chroma">Chroma</option>
                        <option value="Mystical">Mystical</option>
                        <option value="Supreme">Supreme (Aidan)</option>
                    </select>

                    <label class="flex items-center justify-center gap-2 bg-slate-950 border border-slate-700 rounded-xl px-3 py-2 text-sm font-semibold cursor-pointer text-slate-300 hover:text-white">
                        <input type="checkbox" id="inv-hide-locked" onchange="renderInventory()" class="rounded accent-purple-600 w-4 h-4">
                        Hide Locked Blooks
                    </label>
                </div>
            </div>

            <div id="inventory-container" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-6 xl:grid-cols-8 gap-3"></div>
        </section>

        <!-- ================= TAB 3: HOURLY WHEEL ================= -->
        <section id="tab-wheel" class="hidden space-y-6">
            <div class="max-w-2xl mx-auto bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-2xl text-center space-y-6">
                <div>
                    <h2 class="font-fredoka text-3xl font-bold text-white flex items-center justify-center gap-2">
                        <span>🎡</span> Hourly Token Wheel
                    </h2>
                    <p class="text-slate-400 text-sm mt-1">Spin once every hour to win anywhere from <strong class="text-amber-400">1,000 to 1,000,000 Tokens!</strong></p>
                </div>

                <div class="relative w-72 h-72 sm:w-80 sm:h-80 mx-auto">
                    <div class="absolute -top-3 left-1/2 -translate-x-1/2 z-20 text-red-500 text-3xl drop-shadow-md">
                        <i class="fa-solid fa-caret-down"></i>
                    </div>
                    <canvas id="wheel-canvas" width="320" height="320" class="w-full h-full rounded-full shadow-2xl border-4 border-slate-700 bg-slate-950 transition-transform duration-[4000ms] cubic-bezier(0.15, 0.99, 0.18, 0.99)"></canvas>
                </div>

                <div class="space-y-3">
                    <p id="wheel-status-text" class="font-fredoka text-base font-bold text-sky-400">Wheel is Ready to Spin!</p>

                    <div class="flex justify-center items-center">
                        <button id="spin-btn" onclick="spinWheel()" class="w-full sm:w-auto px-10 py-3.5 rounded-2xl font-fredoka font-bold text-lg bg-emerald-500 hover:bg-emerald-400 text-slate-950 border-b-4 border-emerald-700 active:translate-y-0.5 transition-all shadow-xl shadow-emerald-500/20 disabled:opacity-50 disabled:cursor-not-allowed">
                            🎯 SPIN WHEEL
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- ================= TAB 4: MULTI MINI-GAMES ARCADE ================= -->
        <section id="tab-minigame" class="hidden space-y-6">
            
            <!-- GAME SELECTION HUB -->
            <div id="minigame-hub" class="space-y-6">
                <div class="text-center space-y-2">
                    <h2 class="font-fredoka text-3xl font-bold text-white flex items-center justify-center gap-2">
                        <span>🕹️</span> Mini Game Arcade
                    </h2>
                    <p class="text-slate-400 text-sm">Choose a game mode below to test your skills and earn <strong class="text-amber-400">Tokens</strong> directly into your main balance!</p>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-6 max-w-5xl mx-auto">
                    
                    <!-- Game 1: Don't Look Down -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-emerald-500/50 rounded-3xl p-6 flex flex-col justify-between space-y-4 shadow-xl transition-all hover:-translate-y-1">
                        <div class="space-y-3">
                            <div class="flex items-center justify-between">
                                <span class="text-4xl">🧗‍♂️</span>
                                <span class="bg-emerald-500/10 border border-emerald-500/30 text-emerald-400 px-3 py-1 rounded-full text-xs font-bold font-fredoka">Unlimited Plays</span>
                            </div>
                            <h3 class="font-fredoka text-2xl font-bold text-white">Don't Look Down</h3>
                            <p class="text-slate-400 text-sm">Climb as high as you can on moving platforms! Answer quiz questions mid-climb to replenish energy.</p>
                        </div>
                        <button onclick="launchGame('dl-down')" class="w-full py-3 bg-emerald-600 hover:bg-emerald-500 text-white border-b-4 border-emerald-800 font-fredoka font-bold rounded-2xl transition-all">
                            PLAY DON'T LOOK DOWN 🚀
                        </button>
                    </div>

                    <!-- Game 2: Gold Quest -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-amber-500/50 rounded-3xl p-6 flex flex-col justify-between space-y-4 shadow-xl transition-all hover:-translate-y-1">
                        <div class="space-y-3">
                            <div class="flex items-center justify-between">
                                <span class="text-4xl">👑</span>
                                <span id="gq-play-badge" class="bg-amber-500/10 border border-amber-500/30 text-amber-400 px-3 py-1 rounded-full text-xs font-bold font-fredoka">1 Play / Hr</span>
                            </div>
                            <h3 class="font-fredoka text-2xl font-bold text-white">Gold Quest</h3>
                            <p class="text-slate-400 text-sm">Answer questions correctly across 10 rounds to open treasure chests! Earn multiplier boosts and extra gold.</p>
                        </div>
                        <button id="gq-play-btn" onclick="launchGame('gold-quest')" class="w-full py-3 bg-amber-500 hover:bg-amber-400 text-slate-950 border-b-4 border-amber-700 font-fredoka font-bold rounded-2xl transition-all">
                            PLAY GOLD QUEST 🪙
                        </button>
                    </div>

                    <!-- Game 3: Crypto Hack -->
                    <div class="bg-slate-900 border border-slate-800 hover:border-cyan-500/50 rounded-3xl p-6 flex flex-col justify-between space-y-4 shadow-xl transition-all hover:-translate-y-1">
                        <div class="space-y-3">
                            <div class="flex items-center justify-between">
                                <span class="text-4xl">💻</span>
                                <span id="ch-play-badge" class="bg-cyan-500/10 border border-cyan-500/30 text-cyan-400 px-3 py-1 rounded-full text-xs font-bold font-fredoka">1 Play / Hr</span>
                            </div>
                            <h3 class="font-fredoka text-2xl font-bold text-white">Crypto Hack</h3>
                            <p class="text-slate-400 text-sm">Solve questions across 10 rounds to unlock crypto mining passwords! Hack target blooks to steal their balances.</p>
                        </div>
                        <button id="ch-play-btn" onclick="launchGame('crypto-hack')" class="w-full py-3 bg-cyan-600 hover:bg-cyan-500 text-white border-b-4 border-cyan-800 font-fredoka font-bold rounded-2xl transition-all">
                            PLAY CRYPTO HACK ⚡
                        </button>
                    </div>

                </div>
            </div>

            <!-- ACTIVE GAME VIEWPORT CONTAINER -->
            <div id="active-game-wrapper" class="hidden space-y-4">
                <div class="flex items-center justify-between max-w-4xl mx-auto px-2">
                    <button onclick="returnToGameHub()" class="flex items-center gap-2 px-4 py-2 bg-slate-800 hover:bg-slate-700 text-slate-200 font-fredoka font-bold rounded-xl text-sm border border-slate-700 transition-all">
                        <i class="fa-solid fa-arrow-left"></i> Exit to Arcade Hub
                    </button>
                    <span id="active-game-title" class="font-fredoka font-bold text-lg text-amber-400">Game Active</span>
                </div>

                <!-- 1. DON'T LOOK DOWN VIEW -->
                <div id="view-dl-down" class="game-view hidden">
                    <div id="game-container">
                        <canvas id="gameCanvas" width="800" height="550"></canvas>
                        <div class="hud">
                            <div class="hud-card">
                                <div>Energy</div>
                                <div class="energy-bar-container">
                                    <div id="energy-bar" class="energy-bar"></div>
                                </div>
                            </div>
                            <div class="hud-card">Height: <span id="height-val">0</span>m</div>
                            <div class="hud-card">Coins: <span id="coin-val">0</span> 🪙</div>
                        </div>
                        <div class="controls-tip">A / D or ← / →: Move | Space / W: Jump</div>
                        <button id="quiz-btn" onclick="openQuiz('dl-down')">Earn Energy ⚡</button>
                    </div>
                </div>

                <!-- 2. GOLD QUEST VIEW -->
                <div id="view-gold-quest" class="game-view hidden max-w-2xl mx-auto bg-slate-900 border border-slate-800 rounded-3xl p-6 text-center space-y-6">
                    <div class="flex justify-between items-center border-b border-slate-800 pb-4">
                        <span class="font-fredoka font-bold text-slate-400">Round: <span id="gq-round" class="text-white">1</span>/10</span>
                        <span class="font-fredoka font-bold text-amber-400 text-xl">Gold: <span id="gq-gold">0</span> 🪙</span>
                    </div>

                    <div id="gq-question-box" class="space-y-4">
                        <h3 id="gq-question-text" class="font-fredoka text-xl text-white">Question...</h3>
                        <div id="gq-options" class="grid grid-cols-2 gap-3 max-w-md mx-auto"></div>
                    </div>

                    <div id="gq-chest-box" class="hidden space-y-4">
                        <h3 class="font-fredoka text-2xl text-amber-300 font-bold">Pick a Chest!</h3>
                        <div class="grid grid-cols-3 gap-4">
                            <button onclick="openChest(0)" class="p-6 bg-slate-950 hover:bg-slate-800 border-2 border-amber-500/50 rounded-2xl text-5xl transition-all transform hover:scale-105">📦</button>
                            <button onclick="openChest(1)" class="p-6 bg-slate-950 hover:bg-slate-800 border-2 border-amber-500/50 rounded-2xl text-5xl transition-all transform hover:scale-105">📦</button>
                            <button onclick="openChest(2)" class="p-6 bg-slate-950 hover:bg-slate-800 border-2 border-amber-500/50 rounded-2xl text-5xl transition-all transform hover:scale-105">📦</button>
                        </div>
                    </div>
                </div>

                <!-- 3. CRYPTO HACK VIEW -->
                <div id="view-crypto-hack" class="game-view hidden max-w-2xl mx-auto bg-slate-900 border border-slate-800 rounded-3xl p-6 text-center space-y-6">
                    <div class="flex justify-between items-center border-b border-slate-800 pb-4">
                        <span class="font-fredoka font-bold text-slate-400">Round: <span id="ch-round" class="text-white">1</span>/10</span>
                        <span class="font-fredoka font-bold text-cyan-400 text-xl">Crypto: <span id="ch-crypto">0</span> ⚡</span>
                    </div>

                    <div id="ch-question-box" class="space-y-4">
                        <h3 id="ch-question-text" class="font-fredoka text-xl text-white">Question...</h3>
                        <div id="ch-options" class="grid grid-cols-2 gap-3 max-w-md mx-auto"></div>
                    </div>

                    <div id="ch-hack-box" class="hidden space-y-4">
                        <h3 class="font-fredoka text-2xl text-cyan-300 font-bold">Hack a Target!</h3>
                        <div id="ch-targets" class="grid grid-cols-3 gap-3"></div>
                    </div>
                </div>

            </div>

            <!-- SHARED QUIZ OVERLAY MODAL -->
            <div id="quiz-modal" class="quiz-overlay hidden">
                <div class="quiz-card">
                    <h3 class="font-fredoka text-xl font-bold text-amber-400 mb-2">Answer to Gain Energy!</h3>
                    <div id="shared-question-text" class="font-fredoka text-2xl font-bold text-white mb-4">5 + 7 = ?</div>
                    <div class="options-grid" id="shared-options-grid"></div>
                </div>
            </div>

        </section>

        <!-- ================= TAB 5: SETTINGS ================= -->
        <section id="tab-settings" class="hidden max-w-xl mx-auto space-y-6">
            <div class="bg-slate-900 border border-slate-800 rounded-3xl p-6 shadow-xl space-y-6">
                <h2 class="font-fredoka text-2xl font-bold text-white border-b border-slate-800 pb-3">⚙️ Game Settings & Reset</h2>
                
                <div class="flex items-center justify-between p-4 bg-slate-950 rounded-2xl border border-slate-800">
                    <div>
                        <h3 class="font-fredoka font-bold text-white">Sound Effects</h3>
                        <p class="text-xs text-slate-400">Enable or disable sound synth</p>
                    </div>
                    <button id="toggle-sound-btn" onclick="toggleSound()" class="px-4 py-2 rounded-xl font-fredoka font-bold bg-purple-600 text-white">
                        🔊 ON
                    </button>
                </div>

                <div class="p-4 bg-red-950/30 border border-red-500/30 rounded-2xl space-y-3">
                    <h3 class="font-fredoka font-bold text-red-400">Reset Save Data</h3>
                    <p class="text-xs text-slate-400">Clears all tokens, inventory, play limits, and spin history back to default state.</p>
                    <button onclick="confirmResetModal()" class="px-4 py-2 rounded-xl font-fredoka font-bold bg-red-600 hover:bg-red-500 text-white border-b-4 border-red-800">
                        🗑 Reset Entire Game
                    </button>
                </div>
            </div>
        </section>

    </main>

    <!-- MODAL 1: PACK OPENING & SUMMARY MODAL -->
    <div id="modal-pack-open" class="fixed inset-0 z-50 hidden bg-slate-950/90 backdrop-blur-md flex items-center justify-center p-4">
        <div class="bg-slate-900 border-2 border-purple-500/50 w-full max-w-2xl rounded-3xl p-6 sm:p-8 text-center shadow-2xl relative space-y-6 max-h-[90vh] overflow-y-auto">
            <div id="pack-open-title-container">
                <h2 id="pack-open-title" class="font-fredoka text-2xl sm:text-3xl font-bold text-white">Opening Pack...</h2>
                <p id="pack-open-subtitle" class="text-slate-400 text-sm">Flipping cards!</p>
            </div>

            <div id="pack-cards-display" class="flex flex-wrap items-center justify-center gap-4 py-4 min-h-[220px]"></div>

            <div class="flex items-center justify-center gap-3 pt-2">
                <button id="pack-again-btn" class="px-6 py-3 rounded-2xl font-fredoka font-bold text-base bg-purple-600 hover:bg-purple-500 text-white border-b-4 border-purple-800 active:translate-y-0.5 transition-all shadow-lg">
                    Open Again
                </button>
                <button onclick="closeModal('modal-pack-open')" class="px-6 py-3 rounded-2xl font-fredoka font-bold text-base bg-slate-800 hover:bg-slate-700 text-slate-300 border-b-4 border-slate-950 active:translate-y-0.5 transition-all">
                    Done / Close
                </button>
            </div>
        </div>
    </div>

    <!-- MODAL 2: AIDAN SUPREME PULL CELEBRATION MODAL -->
    <div id="modal-aidan" class="fixed inset-0 z-50 hidden bg-black/95 backdrop-blur-xl flex items-center justify-center p-4">
        <div class="aidan-card w-full max-w-lg rounded-3xl p-8 text-center shadow-2xl space-y-6 text-white relative">
            <div class="animate-bounce text-6xl">👑✨</div>
            <h2 class="font-fredoka text-4xl font-extrabold text-amber-300 drop-shadow-md">
                SUPREME UNLOCKED!
            </h2>
            <div class="bg-slate-950/80 rounded-2xl p-6 border-2 border-amber-400/80 space-y-3">
                <div class="text-7xl animate-pulse">👑✨</div>
                <h3 class="font-fredoka text-3xl font-bold text-amber-300">Aidan</h3>
                <span class="inline-block px-4 py-1 rounded-full text-xs font-bold uppercase tracking-wider bg-pink-600 text-white shadow-md">
                    SUPREME (0.001% Drop Rate)
                </span>
                <p class="text-xs text-slate-300">You pulled the rarest Blook in the entire simulator!</p>
            </div>
            <button onclick="closeModal('modal-aidan')" class="px-8 py-3.5 rounded-2xl font-fredoka font-bold text-lg bg-amber-400 hover:bg-amber-300 text-slate-950 border-b-4 border-amber-600 active:translate-y-0.5 transition-all shadow-xl">
                CLAIM Aidan Ali Chunara 👑
            </button>
        </div>
    </div>

    <div id="toast-container" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <script>
        // --- ACTIVE TAB TRACKING ---
        let activeTab = 'shop';

        function toggleHeaderCollapse() {
            const stats = document.getElementById("header-stats-container");
            const subtitle = document.getElementById("header-subtitle");
            const icon = document.getElementById("header-toggle-icon");
            const text = document.getElementById("header-toggle-text");

            if (stats.classList.contains("hidden")) {
                stats.classList.remove("hidden");
                if (subtitle) subtitle.classList.remove("hidden");
                icon.className = "fa-solid fa-chevron-up text-xs";
                text.innerText = "Collapse";
            } else {
                stats.classList.add("hidden");
                if (subtitle) subtitle.classList.add("hidden");
                icon.className = "fa-solid fa-chevron-down text-xs";
                text.innerText = "Expand";
            }
        }

        // --- RARITIES & CONFIGURATION ---
        const RARITIES = {
            Common: { name: 'Common', color: 'bg-slate-600 text-slate-100', dropChance: 45, sellValue: 20 },
            Uncommon: { name: 'Uncommon', color: 'bg-green-600 text-white', dropChance: 25, sellValue: 50 },
            Rare: { name: 'Rare', color: 'bg-blue-600 text-white', dropChance: 15, sellValue: 150 },
            Epic: { name: 'Epic', color: 'bg-purple-600 text-white', dropChance: 9, sellValue: 400 },
            Legendary: { name: 'Legendary', color: 'bg-amber-500 text-slate-950 font-bold', dropChance: 4.5, sellValue: 1500 },
            Chroma: { name: 'Chroma', color: 'bg-pink-600 text-white font-bold', dropChance: 1.0, sellValue: 5000 },
            Mystical: { name: 'Mystical', color: 'bg-red-600 text-white font-bold', dropChance: 0.499, sellValue: 25000 },
            Supreme: { name: 'Supreme', color: 'bg-gradient-to-r from-pink-500 via-purple-500 to-amber-400 text-slate-950 font-extrabold', dropChance: 0.001, sellValue: 500000 }
        };

        // --- 50 PACK DEFINITIONS ---
        const PACK_DEFINITIONS = [
            { id: 1, name: "Medieval", icon: "🏰", cost: 500, color: "from-amber-800 to-stone-900" },
            { id: 2, name: "Space", icon: "🚀", cost: 500, color: "from-indigo-900 to-slate-950" },
            { id: 3, name: "Safari", icon: "🦁", cost: 500, color: "from-yellow-700 to-amber-950" },
            { id: 4, name: "Aquatic", icon: "🐙", cost: 600, color: "from-cyan-800 to-blue-950" },
            { id: 5, name: "Fantasy", icon: "🐉", cost: 600, color: "from-purple-900 to-fuchsia-950" },
            { id: 6, name: "Cyber", icon: "🤖", cost: 700, color: "from-emerald-800 to-teal-950" },
            { id: 7, name: "Food", icon: "🍕", cost: 700, color: "from-orange-700 to-red-950" },
            { id: 8, name: "Monster", icon: "👻", cost: 800, color: "from-violet-900 to-purple-950" },
            { id: 9, name: "Elemental", icon: "🔥", cost: 800, color: "from-red-800 to-orange-950" },
            { id: 10, name: "Cosmic", icon: "🌌", cost: 1000, color: "from-fuchsia-900 to-indigo-950" },
            { id: 11, name: "Anime", icon: "⚔️", cost: 1000, color: "from-pink-700 to-purple-900" },
            { id: 12, name: "Superhero", icon: "🦸", cost: 1200, color: "from-blue-700 to-red-900" },
            { id: 13, name: "Mythological", icon: "⚡", cost: 1200, color: "from-yellow-600 to-purple-900" },
            { id: 14, name: "Underwater", icon: "🐬", cost: 1400, color: "from-teal-700 to-blue-900" },
            { id: 15, name: "Jurassic", icon: "🦖", cost: 1400, color: "from-emerald-800 to-stone-900" },
            { id: 16, name: "Arcade", icon: "🕹️", cost: 1600, color: "from-purple-700 to-pink-900" },
            { id: 17, name: "Candy", icon: "🍬", cost: 1600, color: "from-pink-600 to-rose-900" },
            { id: 18, name: "Music", icon: "🎸", cost: 1800, color: "from-violet-800 to-slate-900" },
            { id: 19, name: "Steampunk", icon: "⚙️", cost: 1800, color: "from-amber-900 to-stone-900" },
            { id: 20, name: "Pirates", icon: "🏴‍☠", cost: 2000, color: "from-stone-800 to-red-950" },
            { id: 21, name: "Spooky", icon: "🎃", cost: 2000, color: "from-orange-800 to-purple-950" },
            { id: 22, name: "Winter", icon: "❄", cost: 2200, color: "from-sky-700 to-indigo-950" },
            { id: 23, name: "Galaxy", icon: "🌠", cost: 2200, color: "from-purple-900 to-black" },
            { id: 24, name: "Neon", icon: "🪩", cost: 2500, color: "from-fuchsia-800 to-cyan-900" },
            { id: 25, name: "Magic", icon: "🪄", cost: 2500, color: "from-indigo-800 to-purple-950" },
            { id: 26, name: "Royal", icon: "👑", cost: 3000, color: "from-amber-600 to-purple-950" },
            { id: 27, name: "Ninja", icon: "🥷", cost: 3000, color: "from-slate-800 to-black" },
            { id: 28, name: "Robot", icon: "🦾", cost: 3500, color: "from-cyan-800 to-slate-900" },
            { id: 29, name: "Zombie", icon: "🧟", cost: 3500, color: "from-lime-900 to-stone-950" },
            { id: 30, name: "Time Travel", icon: "⏳", cost: 4000, color: "from-yellow-800 to-indigo-950" },
            { id: 31, name: "Crystal", icon: "💎", cost: 4000, color: "from-sky-600 to-fuchsia-950" },
            { id: 32, name: "Wild West", icon: "🤠", cost: 4500, color: "from-amber-800 to-stone-900" },
            { id: 33, name: "Volcano", icon: "🌋", cost: 4500, color: "from-red-700 to-amber-950" },
            { id: 34, name: "Insect", icon: "🐞", cost: 5000, color: "from-green-800 to-amber-950" },
            { id: 35, name: "Circus", icon: "🎪", cost: 5000, color: "from-red-600 to-blue-900" },
            { id: 36, name: "Olympus", icon: "🏛️", cost: 6000, color: "from-amber-600 to-sky-950" },
            { id: 37, name: "Viking", icon: "🪓", cost: 6000, color: "from-cyan-900 to-stone-900" },
            { id: 38, name: "Alien", icon: "👽", cost: 7500, color: "from-lime-700 to-emerald-950" },
            { id: 39, name: "Prehistoric", icon: "🦣", cost: 7500, color: "from-amber-900 to-stone-950" },
            { id: 40, name: "School", icon: "📚", cost: 8000, color: "from-blue-800 to-slate-900" },
            { id: 41, name: "Office", icon: "💼", cost: 8000, color: "from-slate-700 to-stone-900" },
            { id: 42, name: "Fast Food", icon: "🍔", cost: 10000, color: "from-red-700 to-yellow-900" },
            { id: 43, name: "Dessert", icon: "🍩", cost: 10000, color: "from-pink-700 to-purple-950" },
            { id: 44, name: "Farm", icon: "🚜", cost: 12000, color: "from-green-700 to-amber-950" },
            { id: 45, name: "Jungle", icon: "🌴", cost: 12000, color: "from-emerald-800 to-teal-950" },
            { id: 46, name: "Arctic", icon: "🧊", cost: 15000, color: "from-sky-500 to-indigo-950" },
            { id: 47, name: "Sports", icon: "⚽", cost: 15000, color: "from-green-600 to-blue-900" },
            { id: 48, name: "Retro", icon: "📻", cost: 20000, color: "from-orange-800 to-purple-900" },
            { id: 49, name: "Shadow", icon: "👤", cost: 25000, color: "from-slate-900 to-black" },
            { id: 50, name: "Celestial / Supreme", icon: "👑", cost: 50000, color: "from-purple-900 via-pink-900 to-amber-900" }
        ];

        // --- PROCEDURAL GENERATOR FOR 1,000 UNIQUE BLOOKS ---
        const EMOJI_POOL = ["🐉", "🧙‍♂️", "🤖", "🦊", "👑", "🦄", "👾", "👽", "🐯", "⚔️", "🛡️", "🔮", "🍕", "🚀", "👻", "💎", "🔥", "🌊", "⚡", "🌿", "🦅", "🦈", "🦂", "🐺", "🦁", "🐼", "🐻", "🐙", "🦖", "🦩"];
        
        let ALL_BLOOKS = [];
        let blookCounter = 1;

        PACK_DEFINITIONS.forEach((pack) => {
            const rarityDistribution = [
                "Common", "Common", "Common", "Common", "Common", "Common", "Common", "Common",
                "Uncommon", "Uncommon", "Uncommon", "Uncommon", "Uncommon",
                "Rare", "Rare", "Rare",
                "Epic", "Epic",
                "Legendary"
            ];

            if (pack.id === 50) {
                rarityDistribution.push("Supreme");
            } else if (pack.id % 2 === 0) {
                rarityDistribution.push("Chroma");
            } else {
                rarityDistribution.push("Mystical");
            }

            for (let i = 1; i <= 20; i++) {
                const rarity = rarityDistribution[i - 1];
                const blookId = blookCounter++;

                if (blookId === 1000 || (pack.id === 50 && i === 20)) {
                    ALL_BLOOKS.push({
                        id: 1000,
                        name: "Aidan Ali Chunara",
                        packId: 50,
                        packName: pack.name,
                        rarity: "Supreme",
                        emoji: "👑✨",
                        dropChance: 0.001,
                        sellValue: 500000,
                        isSupreme: true
                    });
                } else {
                    const emoji = EMOJI_POOL[(pack.id + i) % EMOJI_POOL.length];
                    ALL_BLOOKS.push({
                        id: blookId,
                        name: `${pack.name} ${rarity} #${i}`,
                        packId: pack.id,
                        packName: pack.name,
                        rarity: rarity,
                        emoji: emoji,
                        dropChance: RARITIES[rarity].dropChance,
                        sellValue: RARITIES[rarity].sellValue,
                        isSupreme: false
                    });
                }
            }
        });

        // --- GAME STATE ---
        let gameState = {
            tokens: 5000,
            inventory: {},
            lastSpinTimestamp: null,
            soundEnabled: true,
            lastPlayTimestamps: {
                'gold-quest': null,
                'crypto-hack': null
            }
        };

        // --- WEB AUDIO API SYNTHESIZER ---
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        }

        function playSound(type) {
            if (!gameState.soundEnabled) return;
            try {
                initAudio();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                const now = audioCtx.currentTime;

                if (type === 'click') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(400, now);
                    osc.frequency.exponentialRampToValueAtTime(100, now + 0.08);
                    gain.gain.setValueAtTime(0.15, now);
                    gain.gain.linearRampToValueAtTime(0.01, now + 0.08);
                    osc.start(now);
                    osc.stop(now + 0.08);
                } else if (type === 'spinTick') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(600, now);
                    gain.gain.setValueAtTime(0.08, now);
                    gain.gain.linearRampToValueAtTime(0.01, now + 0.04);
                    osc.start(now);
                    osc.stop(now + 0.04);
                } else if (type === 'pullNormal') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(300, now);
                    osc.frequency.exponentialRampToValueAtTime(600, now + 0.2);
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.linearRampToValueAtTime(0.01, now + 0.2);
                    osc.start(now);
                    osc.stop(now + 0.2);
                } else if (type === 'pullRare') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(523.25, now);
                    osc.frequency.setValueAtTime(659.25, now + 0.1);
                    osc.frequency.setValueAtTime(783.99, now + 0.2);
                    gain.gain.setValueAtTime(0.3, now);
                    gain.gain.linearRampToValueAtTime(0.01, now + 0.35);
                    osc.start(now);
                    osc.stop(now + 0.35);
                }
            } catch (e) {}
        }

        function loadState() {
            const saved = localStorage.getItem("blooket_sim_1k_v1");
            if (saved) {
                try { 
                    const parsed = JSON.parse(saved);
                    gameState = { ...gameState, ...parsed };
                    if (!gameState.lastPlayTimestamps) {
                        gameState.lastPlayTimestamps = { 'gold-quest': null, 'crypto-hack': null };
                    }
                } catch (e) {}
            }
            updateGlobalHeader();
            updateArcadeHubUI();
        }

        function saveState() {
            localStorage.setItem("blooket_sim_1k_v1", JSON.stringify(gameState));
            updateGlobalHeader();
            updateArcadeHubUI();
        }

        function updateGlobalHeader() {
            document.getElementById("stat-tokens").innerText = gameState.tokens.toLocaleString();
            const unlockedCount = Object.keys(gameState.inventory).filter(id => gameState.inventory[id] > 0).length;
            document.getElementById("stat-unlocked").innerText = unlockedCount;
            document.getElementById("stat-percent").innerText = `(${((unlockedCount / 1000) * 100).toFixed(1)}%)`;

            const soundBtn = document.getElementById("toggle-sound-btn");
            if (soundBtn) {
                soundBtn.innerText = gameState.soundEnabled ? "🔊 ON" : "🔇 OFF";
                soundBtn.className = gameState.soundEnabled 
                    ? "px-4 py-2 rounded-xl font-fredoka font-bold bg-purple-600 text-white"
                    : "px-4 py-2 rounded-xl font-fredoka font-bold bg-slate-800 text-slate-400";
            }
        }

        function updateArcadeHubUI() {
            const lastPlay = gameState.lastPlayTimestamps || {};
            const now = Date.now();
            const ONE_HOUR = 60 * 60 * 1000;

            const gqBadge = document.getElementById("gq-play-badge");
            const chBadge = document.getElementById("ch-play-badge");
            const gqBtn = document.getElementById("gq-play-btn");
            const chBtn = document.getElementById("ch-play-btn");

            // Gold Quest Cooldown logic
            const gqLast = lastPlay['gold-quest'];
            if (gqLast && (now - gqLast < ONE_HOUR)) {
                const remMs = ONE_HOUR - (now - gqLast);
                const mins = Math.floor(remMs / (1000 * 60));
                const secs = Math.floor((remMs % (1000 * 60)) / 1000);
                if (gqBadge) gqBadge.innerText = `⏳ Ready in ${mins}m ${secs}s`;
                if (gqBtn) {
                    gqBtn.classList.add("opacity-50", "cursor-not-allowed");
                    gqBtn.innerText = `HOURLY LIMIT REACHED (${mins}m ${secs}s) 🔒`;
                }
            } else {
                if (gqBadge) gqBadge.innerText = `1 Play / Hr`;
                if (gqBtn) {
                    gqBtn.classList.remove("opacity-50", "cursor-not-allowed");
                    gqBtn.innerText = "PLAY GOLD QUEST 🪙";
                }
            }

            // Crypto Hack Cooldown logic
            const chLast = lastPlay['crypto-hack'];
            if (chLast && (now - chLast < ONE_HOUR)) {
                const remMs = ONE_HOUR - (now - chLast);
                const mins = Math.floor(remMs / (1000 * 60));
                const secs = Math.floor((remMs % (1000 * 60)) / 1000);
                if (chBadge) chBadge.innerText = `⏳ Ready in ${mins}m ${secs}s`;
                if (chBtn) {
                    chBtn.classList.add("opacity-50", "cursor-not-allowed");
                    chBtn.innerText = `HOURLY LIMIT REACHED (${mins}m ${secs}s) 🔒`;
                }
            } else {
                if (chBadge) chBadge.innerText = `1 Play / Hr`;
                if (chBtn) {
                    chBtn.classList.remove("opacity-50", "cursor-not-allowed");
                    chBtn.innerText = "PLAY CRYPTO HACK ⚡";
                }
            }
        }

        function showToast(message, type = 'info') {
            const container = document.getElementById("toast-container");
            const toast = document.createElement("div");
            toast.className = `px-4 py-3 rounded-2xl font-fredoka font-bold text-sm shadow-xl flex items-center gap-2 border transition-all duration-300 pointer-events-auto transform translate-y-2 opacity-0 ${
                type === 'success' ? 'bg-emerald-900 border-emerald-500 text-emerald-200' :
                type === 'error' ? 'bg-red-900 border-red-500 text-red-200' :
                'bg-slate-800 border-slate-700 text-white'
            }`;
            toast.innerHTML = message;
            container.appendChild(toast);

            setTimeout(() => toast.classList.remove('translate-y-2', 'opacity-0'), 10);
            setTimeout(() => {
                toast.classList.add('opacity-0', 'translate-y-2');
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        function switchTab(tabId) {
            playSound('click');
            activeTab = tabId;
            document.querySelectorAll('main > section').forEach(sec => sec.classList.add('hidden'));
            document.querySelectorAll('.nav-btn').forEach(btn => {
                btn.className = "nav-btn flex items-center gap-2 px-4 py-2 rounded-xl font-fredoka font-bold text-xs sm:text-sm transition-all duration-200 border-b-4 border-slate-800 bg-slate-800 text-slate-300 hover:bg-slate-700";
            });

            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            const activeNav = document.getElementById(`nav-${tabId}`);
            if (activeNav) {
                activeNav.className = "nav-btn active flex items-center gap-2 px-4 py-2 rounded-xl font-fredoka font-bold text-xs sm:text-sm transition-all duration-200 border-b-4 border-purple-800 bg-purple-600 text-white shadow-lg";
            }

            if (tabId === 'shop') renderShop();
            if (tabId === 'inventory') renderInventory();
            if (tabId === 'wheel') initWheel();
            if (tabId === 'minigame') updateArcadeHubUI();
        }

        // --- SHOP & INVENTORY RENDERING ---
        function renderShop() {
            const container = document.getElementById("packs-container");
            const search = document.getElementById("shop-search").value.toLowerCase();
            container.innerHTML = "";

            PACK_DEFINITIONS.forEach(pack => {
                if (search && !pack.name.toLowerCase().includes(search)) return;
                const packBlooks = ALL_BLOOKS.filter(b => b.packId === pack.id);
                const unlockedInPack = packBlooks.filter(b => gameState.inventory[b.id] > 0).length;

                const card = document.createElement("div");
                card.className = "bg-slate-900 border border-slate-800 hover:border-purple-500/50 rounded-2xl p-4 flex flex-col justify-between gap-4 transition-all hover:-translate-y-1 shadow-lg group";
                card.innerHTML = `
                    <div class="space-y-3">
                        <div class="h-28 rounded-xl bg-gradient-to-br ${pack.color} flex items-center justify-center text-5xl shadow-inner relative overflow-hidden group-hover:scale-105 transition-transform">
                            <span>${pack.icon}</span>
                            <span class="absolute top-2 right-2 bg-slate-950/80 px-2 py-0.5 rounded-full text-[10px] font-bold text-slate-300">
                                ${unlockedInPack}/20
                            </span>
                        </div>
                        <div>
                            <h3 class="font-fredoka font-bold text-lg text-white group-hover:text-purple-300 transition-colors">${pack.name} Pack</h3>
                            <div class="flex items-center gap-1.5 font-fredoka font-bold text-amber-400 text-sm">
                                <span>🪙</span> ${pack.cost.toLocaleString()} Tokens
                            </div>
                        </div>
                    </div>

                    <div class="grid grid-cols-3 gap-1.5 pt-2 border-t border-slate-800">
                        <button onclick="triggerPackOpen(${pack.id}, 1)" class="px-2 py-1.5 bg-purple-600 hover:bg-purple-500 border-b-2 border-purple-800 rounded-lg font-fredoka text-xs font-bold text-white transition-all active:translate-y-0.5">1x</button>
                        <button onclick="triggerPackOpen(${pack.id}, 5)" class="px-2 py-1.5 bg-purple-700 hover:bg-purple-600 border-b-2 border-purple-900 rounded-lg font-fredoka text-xs font-bold text-white transition-all active:translate-y-0.5">5x</button>
                        <button onclick="triggerPackOpen(${pack.id}, 10)" class="px-2 py-1.5 bg-purple-800 hover:bg-purple-700 border-b-2 border-purple-950 rounded-lg font-fredoka text-xs font-bold text-white transition-all active:translate-y-0.5">10x</button>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        function triggerPackOpen(packId, count) {
            const pack = PACK_DEFINITIONS.find(p => p.id === packId);
            const totalCost = pack.cost * count;

            if (gameState.tokens < totalCost) {
                showToast(`⚠ You need 🪙 ${totalCost.toLocaleString()} tokens!`, 'error');
                return;
            }

            gameState.tokens -= totalCost;
            saveState();
            playSound('click');

            const packBlooks = ALL_BLOOKS.filter(b => b.packId === packId);
            let results = [];

            for (let c = 0; c < count; c++) {
                const roll = Math.random() * 100;
                let rarity = 'Common';
                if (roll < 0.001) rarity = 'Supreme';
                else if (roll < 0.5) rarity = 'Mystical';
                else if (roll < 1.5) rarity = 'Chroma';
                else if (roll < 6.0) rarity = 'Legendary';
                else if (roll < 15.0) rarity = 'Epic';
                else if (roll < 30.0) rarity = 'Rare';
                else if (roll < 55.0) rarity = 'Uncommon';

                let candidateBlooks = packBlooks.filter(b => b.rarity === rarity);
                if (candidateBlooks.length === 0) candidateBlooks = packBlooks;

                const pulled = candidateBlooks[Math.floor(Math.random() * candidateBlooks.length)];
                gameState.inventory[pulled.id] = (gameState.inventory[pulled.id] || 0) + 1;
                results.push(pulled);
            }

            saveState();
            showPackOpenModal(pack, results, count);
        }

        function showPackOpenModal(pack, results, count) {
            const modal = document.getElementById("modal-pack-open");
            const display = document.getElementById("pack-cards-display");
            document.getElementById("pack-open-title").innerText = `${pack.name} Pack (${count}x)`;
            document.getElementById("pack-open-subtitle").innerText = `Opened for 🪙 ${(pack.cost * count).toLocaleString()} tokens!`;
            display.innerHTML = "";

            document.getElementById("pack-again-btn").onclick = () => {
                closeModal('modal-pack-open');
                triggerPackOpen(pack.id, count);
            };

            let pulledAidan = false;
            results.forEach((blook) => {
                if (blook.isSupreme) pulledAidan = true;
                const cardWrapper = document.createElement("div");
                cardWrapper.className = `w-32 h-44 sm:w-36 sm:h-48 rounded-2xl p-3 flex flex-col items-center justify-between border-2 text-center transition-all ${
                    blook.isSupreme ? 'aidan-card' : 'bg-slate-950 border-slate-700 glow-' + blook.rarity.toLowerCase()
                }`;

                const rarityMeta = RARITIES[blook.rarity] || RARITIES.Common;
                cardWrapper.innerHTML = `
                    <span class="text-xs px-2 py-0.5 rounded-full font-bold ${rarityMeta.color}">${blook.rarity}</span>
                    <div class="text-4xl my-auto">${blook.emoji}</div>
                    <div class="w-full">
                        <div class="font-fredoka font-bold text-xs truncate text-white">${blook.name}</div>
                        <div class="text-[10px] text-slate-400 font-semibold">x${gameState.inventory[blook.id]} owned</div>
                    </div>
                `;
                display.appendChild(cardWrapper);
            });

            modal.classList.remove("hidden");
            if (pulledAidan) {
                confetti({ particleCount: 200, spread: 100, origin: { y: 0.6 } });
                setTimeout(() => document.getElementById("modal-aidan").classList.remove("hidden"), 600);
            } else if (results.some(r => ['Legendary', 'Chroma', 'Mystical'].includes(r.rarity))) {
                playSound('pullRare');
                confetti({ particleCount: 70, spread: 60, origin: { y: 0.6 } });
            } else {
                playSound('pullNormal');
            }
        }

        function renderInventory() {
            const container = document.getElementById("inventory-container");
            const packSelect = document.getElementById("inv-filter-pack");
            const raritySelect = document.getElementById("inv-filter-rarity");
            const hideLocked = document.getElementById("inv-hide-locked").checked;
            const search = document.getElementById("inv-search").value.toLowerCase();
            
            if (packSelect.options.length <= 1) {
                PACK_DEFINITIONS.forEach(p => {
                    const opt = document.createElement("option");
                    opt.value = p.id;
                    opt.innerText = p.name;
                    packSelect.appendChild(opt);
                });
            }

            const filterPackId = packSelect.value;
            const filterRarity = raritySelect.value;

            const filtered = ALL_BLOOKS.filter(blook => {
                const count = gameState.inventory[blook.id] || 0;
                if (hideLocked && count === 0) return false;
                if (filterPackId !== 'ALL' && blook.packId != filterPackId) return false;
                if (filterRarity !== 'ALL' && blook.rarity !== filterRarity) return false;
                if (search && !blook.name.toLowerCase().includes(search)) return false;
                return true;
            });

            let totalDuplicateValue = 0;
            ALL_BLOOKS.forEach(blook => {
                const count = gameState.inventory[blook.id] || 0;
                if (count > 1) totalDuplicateValue += (count - 1) * blook.sellValue;
            });
            document.getElementById("sell-all-val").innerText = totalDuplicateValue.toLocaleString();

            container.innerHTML = "";
            if (filtered.length === 0) {
                container.innerHTML = `<div class="col-span-full py-12 text-center text-slate-500 font-fredoka text-lg">No Blooks match filters!</div>`;
                return;
            }

            filtered.forEach(blook => {
                const count = gameState.inventory[blook.id] || 0;
                const isLocked = count === 0;
                const rarityMeta = RARITIES[blook.rarity] || RARITIES.Common;

                const card = document.createElement("div");
                card.className = `relative rounded-2xl p-3 flex flex-col items-center justify-between border-2 text-center transition-all ${
                    isLocked ? 'bg-slate-900/40 border-slate-800 opacity-40 grayscale' :
                    blook.isSupreme ? 'aidan-card' : 'bg-slate-900 border-slate-800'
                }`;

                card.innerHTML = `
                    ${count > 1 ? `<span class="absolute top-2 right-2 bg-purple-600 text-white font-fredoka font-bold text-[10px] px-2 py-0.5 rounded-full shadow">x${count}</span>` : ''}
                    <span class="text-[10px] px-2 py-0.5 rounded-full font-bold ${rarityMeta.color} mb-1">${blook.rarity}</span>
                    <div class="text-3xl my-2">${isLocked ? '❓' : blook.emoji}</div>
                    <div class="w-full">
                        <div class="font-fredoka font-bold text-xs truncate text-white">${isLocked ? 'Locked' : blook.name}</div>
                        <div class="text-[9px] text-slate-400 font-semibold truncate">${blook.packName} Pack</div>
                    </div>
                    ${!isLocked && count > 1 ? `
                        <button onclick="sellDuplicate(${blook.id})" class="mt-2 w-full py-1 bg-emerald-600/80 hover:bg-emerald-500 rounded-lg text-[10px] font-fredoka font-bold text-white transition-all">
                            Sell 1 (+🪙${blook.sellValue})
                        </button>
                    ` : ''}
                `;
                container.appendChild(card);
            });
        }

        function sellDuplicate(blookId) {
            const count = gameState.inventory[blookId] || 0;
            if (count <= 1) return;
            const blook = ALL_BLOOKS.find(b => b.id === blookId);
            gameState.inventory[blookId]--;
            gameState.tokens += blook.sellValue;
            saveState();
            playSound('click');
            showToast(`🪙 Sold duplicate of <strong>${blook.name}</strong>!`, 'success');
            renderInventory();
        }

        function sellAllDuplicates() {
            let totalTokensEarned = 0;
            let totalSold = 0;

            ALL_BLOOKS.forEach(blook => {
                const count = gameState.inventory[blook.id] || 0;
                if (count > 1) {
                    const extra = count - 1;
                    totalTokensEarned += extra * blook.sellValue;
                    totalSold += extra;
                    gameState.inventory[blook.id] = 1;
                }
            });

            if (totalSold === 0) {
                showToast("ℹ️ No duplicate Blooks available!", 'info');
                return;
            }

            gameState.tokens += totalTokensEarned;
            saveState();
            playSound('click');
            showToast(`🎉 Sold ${totalSold} duplicates for +🪙 ${totalTokensEarned.toLocaleString()} tokens!`, 'success');
            renderInventory();
        }

        // --- HOURLY WHEEL ---
        const WHEEL_REWARDS = [1000, 2500, 5000, 10000, 25000, 50000, 100000, 250000, 500000, 1000000];
        const WHEEL_COLORS = ["#ef4444", "#3b82f6", "#10b981", "#f59e0b", "#8b5cf6", "#ec4899", "#06b6d4", "#eab308", "#6366f1", "#f43f5e"];
        let isSpinning = false;

        function initWheel() {
            const canvas = document.getElementById("wheel-canvas");
            const ctx = canvas.getContext("2d");
            const sliceAngle = (2 * Math.PI) / WHEEL_REWARDS.length;

            ctx.clearRect(0, 0, 320, 320);

            for (let i = 0; i < WHEEL_REWARDS.length; i++) {
                const angle = i * sliceAngle;
                ctx.beginPath();
                ctx.fillStyle = WHEEL_COLORS[i % WHEEL_COLORS.length];
                ctx.moveTo(160, 160);
                ctx.arc(160, 160, 155, angle, angle + sliceAngle);
                ctx.lineTo(160, 160);
                ctx.fill();

                ctx.save();
                ctx.translate(160, 160);
                ctx.rotate(angle + sliceAngle / 2);
                ctx.fillStyle = "white";
                ctx.font = "bold 13px Nunito, sans-serif";
                ctx.textAlign = "right";
                ctx.fillText(WHEEL_REWARDS[i] >= 1000000 ? '1M 🪙' : (WHEEL_REWARDS[i] / 1000) + 'K 🪙', 140, 4);
                ctx.restore();
            }

            checkWheelCooldown();
        }

        function checkWheelCooldown() {
            const spinBtn = document.getElementById("spin-btn");
            const statusText = document.getElementById("wheel-status-text");
            const now = Date.now();
            const oneHour = 60 * 60 * 1000;

            if (gameState.lastSpinTimestamp && (now - gameState.lastSpinTimestamp < oneHour)) {
                const remainingMs = oneHour - (now - gameState.lastSpinTimestamp);
                const mins = Math.floor(remainingMs / (1000 * 60));
                const secs = Math.floor((remainingMs % (1000 * 60)) / 1000);
                spinBtn.disabled = true;
                statusText.innerText = `⏳ Hourly spin available in ${mins}m ${secs}s`;
            } else {
                spinBtn.disabled = false;
                statusText.innerText = "✨ Wheel is Ready to Spin!";
            }
        }

        function spinWheel() {
            if (isSpinning) return;
            isSpinning = true;
            document.getElementById("spin-btn").disabled = true;

            const canvas = document.getElementById("wheel-canvas");
            const winningIndex = Math.floor(Math.random() * WHEEL_REWARDS.length);
            const reward = WHEEL_REWARDS[winningIndex];

            const sliceAngle = 360 / WHEEL_REWARDS.length;
            const targetRotation = (360 * 8) + (360 - (winningIndex * sliceAngle) - (sliceAngle / 2));
            canvas.style.transform = `rotate(${targetRotation}deg)`;

            setTimeout(() => {
                isSpinning = false;
                gameState.tokens += reward;
                gameState.lastSpinTimestamp = Date.now();
                saveState();
                checkWheelCooldown();
                confetti({ particleCount: 100, spread: 80, origin: { y: 0.6 } });
                showToast(`🎉 WHEEL WIN! Won +🪙 ${reward.toLocaleString()} Tokens!`, 'success');
                canvas.style.transform = 'rotate(0deg)';
            }, 4000);
        }

        // ==========================================
        // MULTI MINI-GAMES ARCADE ENGINE
        // ==========================================
        let currentGameMode = null;
        let activeQuizCallback = null;
        let currentEarnedTokens = 0;

        function launchGame(gameId) {
            // ENFORCE 1 PLAY PER HOUR FOR GOLD QUEST AND CRYPTO HACK
            if (gameId !== 'dl-down') {
                gameState.lastPlayTimestamps = gameState.lastPlayTimestamps || {};
                const lastPlay = gameState.lastPlayTimestamps[gameId];
                const now = Date.now();
                const ONE_HOUR = 60 * 60 * 1000;

                if (lastPlay && (now - lastPlay < ONE_HOUR)) {
                    const remMs = ONE_HOUR - (now - lastPlay);
                    const mins = Math.floor(remMs / (1000 * 60));
                    showToast(`⚠ Hourly play limit reached! Try again in ${mins} minute(s).`, "error");
                    return;
                }
                gameState.lastPlayTimestamps[gameId] = now;
                saveState();
            }

            currentGameMode = gameId;
            currentEarnedTokens = 0;
            document.getElementById("minigame-hub").classList.add("hidden");
            document.getElementById("active-game-wrapper").classList.remove("hidden");
            document.querySelectorAll(".game-view").forEach(v => v.classList.add("hidden"));

            const titleMap = {
                'dl-down': '🧗‍♂️ Don\'t Look Down',
                'gold-quest': '👑 Gold Quest',
                'crypto-hack': '💻 Crypto Hack'
            };
            document.getElementById("active-game-title").innerText = titleMap[gameId] || "Mini Game";
            document.getElementById(`view-${gameId}`).classList.remove("hidden");

            if (gameId === 'dl-down') resetDLGame();
            if (gameId === 'gold-quest') startGoldQuest();
            if (gameId === 'crypto-hack') startCryptoHack();
        }

        function returnToGameHub() {
            document.getElementById("active-game-wrapper").classList.add("hidden");
            document.getElementById("quiz-modal").classList.add("hidden");
            document.getElementById("minigame-hub").classList.remove("hidden");
            currentGameMode = null;
            updateGlobalHeader();
            updateArcadeHubUI();
        }

        function finishGameRun(tokensEarned) {
            currentEarnedTokens = tokensEarned;
            gameState.tokens += tokensEarned;
            saveState();

            showToast(`🎉 Earned +${tokensEarned.toLocaleString()} 🪙 Tokens!`, 'success');
            returnToGameHub();
        }

        // SHARED TRIVIA GENERATOR
        function generateTriviaQuestion() {
            const num1 = Math.floor(Math.random() * 20) + 5;
            const num2 = Math.floor(Math.random() * 20) + 5;
            const isAdd = Math.random() > 0.5;
            const answer = isAdd ? num1 + num2 : num1 * num2;
            const qText = isAdd ? `${num1} + ${num2} = ?` : `${num1} × ${num2} = ?`;

            let choices = [answer];
            while (choices.length < 4) {
                let dummy = answer + (Math.floor(Math.random() * 12) - 6);
                if (dummy >= 0 && !choices.includes(dummy)) choices.push(dummy);
            }
            choices.sort(() => Math.random() - 0.5);

            return { question: qText, answer: answer, choices: choices };
        }

        function openQuiz(gameContext) {
            const modal = document.getElementById("quiz-modal");
            const q = generateTriviaQuestion();
            document.getElementById("shared-question-text").innerText = q.question;
            
            const grid = document.getElementById("shared-options-grid");
            grid.innerHTML = "";

            q.choices.forEach(c => {
                const btn = document.createElement("button");
                btn.className = "btn-opt";
                btn.innerText = c;
                btn.onclick = () => {
                    modal.classList.add("hidden");
                    if (c === q.answer) {
                        if (gameContext === 'dl-down') energy = Math.min(300, energy + 120);
                        playSound('pullNormal');
                    }
                };
                grid.appendChild(btn);
            });

            modal.classList.remove("hidden");
        }

        // 1. DON'T LOOK DOWN GAME LOGIC
        const dlCanvas = document.getElementById('gameCanvas');
        const dlCtx = dlCanvas.getContext('2d');
        let energy = 200;
        let peakHeight = 0;
        let dlCoins = 0;
        let dlEnded = false;
        const keys = { left: false, right: false, up: false };

        const player = { x: 380, y: 480, width: 28, height: 38, vx: 0, vy: 0, speed: 4.2, jumpForce: -9.5, jumpsLeft: 2 };
        let dlPlatforms = [];

        function resetDLGame() {
            energy = 200;
            peakHeight = 0;
            dlCoins = 0;
            dlEnded = false;
            player.x = 380;
            player.y = 480;
            player.vx = 0;
            player.vy = 0;
            
            dlPlatforms = [{ x: 0, y: 520, width: 800, height: 30, isGround: true }];
            let curY = 430;
            while (curY > -50000) {
                const w = Math.random() * 80 + 70;
                dlPlatforms.push({ x: Math.random() * (800 - w), y: curY, width: w, height: 16 });
                curY -= (Math.random() * 45 + 70);
            }
        }

        window.addEventListener('keydown', (e) => {
            if (currentGameMode !== 'dl-down' || dlEnded) return;
            if (e.key === 'ArrowLeft' || e.key === 'a') keys.left = true;
            if (e.key === 'ArrowRight' || e.key === 'd') keys.right = true;
            if ((e.key === 'ArrowUp' || e.key === 'w' || e.key === ' ') && player.jumpsLeft > 0) {
                player.vy = player.jumpForce;
                player.jumpsLeft--;
                energy = Math.max(0, energy - 8);
            }
        });

        window.addEventListener('keyup', (e) => {
            if (e.key === 'ArrowLeft' || e.key === 'a') keys.left = false;
            if (e.key === 'ArrowRight' || e.key === 'd') keys.right = false;
        });

        function updateDLGame() {
            if (currentGameMode !== 'dl-down' || dlEnded) return;

            if (keys.left) player.vx = -player.speed;
            else if (keys.right) player.vx = player.speed;
            else player.vx = 0;

            player.vy += 0.35;
            player.x += player.vx;
            player.y += player.vy;

            dlPlatforms.forEach(p => {
                if (player.vy > 0 && player.x + player.width > p.x && player.x < p.x + p.width && player.y + player.height >= p.y && player.y + player.height <= p.y + p.height + player.vy) {
                    player.y = p.y - player.height;
                    player.vy = 0;
                    player.jumpsLeft = 2;
                }
            });

            const currentMeters = Math.max(0, Math.floor((520 - player.y) / 20));
            if (currentMeters > peakHeight) {
                peakHeight = currentMeters;
                dlCoins = peakHeight * 2;
            }

            if (player.y > 600) {
                dlEnded = true;
                finishGameRun(dlCoins);
            }

            document.getElementById('height-val').innerText = currentMeters;
            document.getElementById('coin-val').innerText = dlCoins;
            document.getElementById('energy-bar').style.width = `${(energy / 300) * 100}%`;
        }

        function renderDLGame() {
            if (currentGameMode !== 'dl-down') return;
            dlCtx.clearRect(0, 0, 800, 550);

            dlCtx.save();
            const camY = player.y - 300;
            dlCtx.translate(0, -camY);

            dlPlatforms.forEach(p => {
                dlCtx.fillStyle = p.isGround ? '#334155' : '#10b981';
                dlCtx.fillRect(p.x, p.y, p.width, p.height);
            });

            dlCtx.fillStyle = '#38bdf8';
            dlCtx.fillRect(player.x, player.y, player.width, player.height);
            dlCtx.restore();
        }

        // 2. GOLD QUEST GAME LOGIC
        let gqRound = 1;
        let gqGold = 0;
        let gqCurrentQ = null;

        function startGoldQuest() {
            gqRound = 1;
            gqGold = 0;
            nextGQRound();
        }

        function nextGQRound() {
            if (gqRound > 10) {
                finishGameRun(gqGold);
                return;
            }
            document.getElementById("gq-round").innerText = gqRound;
            document.getElementById("gq-gold").innerText = gqGold.toLocaleString();
            document.getElementById("gq-chest-box").classList.add("hidden");
            document.getElementById("gq-question-box").classList.remove("hidden");

            gqCurrentQ = generateTriviaQuestion();
            document.getElementById("gq-question-text").innerText = gqCurrentQ.question;
            const opts = document.getElementById("gq-options");
            opts.innerHTML = "";

            gqCurrentQ.choices.forEach(c => {
                const btn = document.createElement("button");
                btn.className = "btn-opt";
                btn.innerText = c;
                btn.onclick = () => {
                    if (c === gqCurrentQ.answer) {
                        playSound('pullNormal');
                        document.getElementById("gq-question-box").classList.add("hidden");
                        document.getElementById("gq-chest-box").classList.remove("hidden");
                    } else {
                        showToast("❌ Incorrect!", "error");
                        gqRound++;
                        nextGQRound();
                    }
                };
                opts.appendChild(btn);
            });
        }

        function openChest(idx) {
            const outcomes = [
                { text: "+500 Tokens", val: 500 },
                { text: "+1,500 Tokens", val: 1500 },
                { text: "2x Gold Multiplier!", mult: 2 },
                { text: "+3,000 Tokens!", val: 3000 }
            ];
            const choice = outcomes[Math.floor(Math.random() * outcomes.length)];
            if (choice.mult) gqGold *= choice.mult;
            else gqGold += choice.val;

            showToast(`🎁 Chest Outcome: ${choice.text}`, "success");
            gqRound++;
            nextGQRound();
        }

        // 3. CRYPTO HACK GAME LOGIC
        let chRound = 1;
        let chCrypto = 0;
        let chCurrentQ = null;

        function startCryptoHack() {
            chRound = 1;
            chCrypto = 0;
            nextCHRound();
        }

        function nextCHRound() {
            if (chRound > 10) {
                finishGameRun(chCrypto);
                return;
            }
            document.getElementById("ch-round").innerText = chRound;
            document.getElementById("ch-crypto").innerText = chCrypto.toLocaleString();
            document.getElementById("ch-hack-box").classList.add("hidden");
            document.getElementById("ch-question-box").classList.remove("hidden");

            chCurrentQ = generateTriviaQuestion();
            document.getElementById("ch-question-text").innerText = chCurrentQ.question;
            const opts = document.getElementById("ch-options");
            opts.innerHTML = "";

            chCurrentQ.choices.forEach(c => {
                const btn = document.createElement("button");
                btn.className = "btn-opt";
                btn.innerText = c;
                btn.onclick = () => {
                    if (c === chCurrentQ.answer) {
                        playSound('pullNormal');
                        document.getElementById("ch-question-box").classList.add("hidden");
                        showCHTargets();
                    } else {
                        showToast("❌ Password Hack Failed!", "error");
                        chRound++;
                        nextCHRound();
                    }
                };
                opts.appendChild(btn);
            });
        }

        function showCHTargets() {
            const box = document.getElementById("ch-hack-box");
            const grid = document.getElementById("ch-targets");
            grid.innerHTML = "";

            for (let i = 0; i < 3; i++) {
                const btn = document.createElement("button");
                btn.className = "p-4 bg-slate-950 hover:bg-cyan-950 border border-cyan-500/50 rounded-xl font-fredoka font-bold text-cyan-300";
                btn.innerText = `Target #${i + 1}`;
                btn.onclick = () => {
                    const gained = Math.floor(Math.random() * 2000) + 1000;
                    chCrypto += gained;
                    showToast(`⚡ Hacked +${gained} Crypto!`, "success");
                    chRound++;
                    nextCHRound();
                };
                grid.appendChild(btn);
            }
            box.classList.remove("hidden");
        }

        // MAIN LOOP
        function mainLoop() {
            if (currentGameMode === 'dl-down') {
                updateDLGame();
                renderDLGame();
            }
            requestAnimationFrame(mainLoop);
        }

        // REAL-TIME COOLDOWN TIMER TICKER
        setInterval(() => {
            if (activeTab === 'minigame') {
                updateArcadeHubUI();
            }
            if (activeTab === 'wheel') {
                checkWheelCooldown();
            }
        }, 1000);

        // INITIALIZATION
        window.addEventListener("DOMContentLoaded", () => {
            loadState();
            renderShop();
            initWheel();
            mainLoop();
        });

        function closeModal(modalId) {
            playSound('click');
            document.getElementById(modalId).classList.add("hidden");
        }

        function toggleSound() {
            gameState.soundEnabled = !gameState.soundEnabled;
            saveState();
            playSound('click');
        }

        function confirmResetModal() {
            if (confirm("Reset all game data back to default?")) {
                localStorage.removeItem("blooket_sim_1k_v1");
                location.reload();
            }
        }
    </script>
</body>
</html>
