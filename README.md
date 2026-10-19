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
                        sans: ['Nunito', 'sans-serif'],
                    },
                    colors: {
                        blooket: {
                            purple: '#4c1d95',
                            indigo: '#312e81',
                            card: '#1e1b4b',
                            accent: '#0284c7',
                            gold: '#fbbf24',
                            supreme: '#ec4899',
                        },
                        bakery: {
                            900: '#1c100b',
                            800: '#2c1a12',
                            700: '#42281d',
                            600: '#5c3828',
                            500: '#7a4b35',
                            400: '#9e6246',
                            accent: '#f59e0b',
                            gold: '#fbbf24',
                            glow: '#fef08a'
                        }
                    },
                    keyframes: {
                        floatUp: {
                            '0%': { opacity: '1', transform: 'translateY(0) scale(1)' },
                            '100%': { opacity: '0', transform: 'translateY(-60px) scale(1.1)' }
                        },
                        shimmer: {
                            '0%': { transform: 'rotate(0deg)' },
                            '100%': { transform: 'rotate(360deg)' }
                        },
                        goldenPulse: {
                            '0%, 100%': { transform: 'scale(1) rotate(0deg)', filter: 'drop-shadow(0 0 15px rgba(251, 191, 36, 0.8))' },
                            '50%': { transform: 'scale(1.1) rotate(5deg)', filter: 'drop-shadow(0 0 25px rgba(251, 191, 36, 1))' }
                        },
                        wave: {
                            '0%, 100%': { transform: 'translateY(0)' },
                            '50%': { transform: 'translateY(-10px)' }
                        },
                        affordableGlow: {
                            '0%, 100%': { boxShadow: '0 0 15px rgba(251, 191, 36, 0.6), inset 0 0 10px rgba(251, 191, 36, 0.3)', borderColor: 'rgba(251, 191, 36, 0.9)' },
                            '50%': { boxShadow: '0 0 25px rgba(251, 191, 36, 0.9), inset 0 0 18px rgba(251, 191, 36, 0.5)', borderColor: 'rgba(254, 240, 138, 1)' }
                        }
                    },
                    animation: {
                        'float-up': 'floatUp 1s ease-out forwards',
                        'spin-slow': 'shimmer 25s linear infinite',
                        'golden-glow': 'goldenPulse 2s ease-in-out infinite',
                        'milk-wave': 'wave 3s ease-in-out infinite',
                        'affordable-glow': 'affordableGlow 1.5s ease-in-out infinite'
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

        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #0f172a; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #475569; }

        .glow-common { box-shadow: 0 0 10px rgba(148, 163, 184, 0.3); }
        .glow-uncommon { box-shadow: 0 0 10px rgba(34, 197, 94, 0.4); }
        .glow-rare { box-shadow: 0 0 12px rgba(59, 130, 246, 0.5); }
        .glow-epic { box-shadow: 0 0 15px rgba(168, 85, 247, 0.6); }
        .glow-legendary { box-shadow: 0 0 18px rgba(245, 158, 11, 0.7); }
        .glow-chroma { box-shadow: 0 0 20px rgba(236, 72, 153, 0.8); }
        .glow-mystical { box-shadow: 0 0 22px rgba(239, 68, 68, 0.9); }

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

        .cookie-active { transform: scale(0.93) !important; }
        .buy-card { touch-action: manipulation; -webkit-tap-highlight-color: transparent; }
        .buy-card:active { transform: scale(0.98); }

        /* NATIVE BLOCK BLAST STYLES */
        .bb-cell {
            background-color: #1e293b;
            border-radius: 6px;
            transition: background-color 0.15s ease, transform 0.1s ease, opacity 0.1s ease;
        }

        .bb-cell.filled {
            box-shadow: inset 0 0 8px rgba(255, 255, 255, 0.4);
        }

        .bb-cell.preview {
            box-shadow: inset 0 0 12px rgba(255, 255, 255, 0.9);
            border: 2px dashed rgba(255, 255, 255, 0.8);
        }

        .bb-draggable-piece {
            display: grid;
            gap: 2px;
            cursor: grab;
            position: absolute;
            transition: transform 0.1s ease;
            z-index: 10;
            touch-action: none;
        }

        .bb-draggable-piece:active { cursor: grabbing; }

        .bb-piece-block {
            width: 20px;
            height: 20px;
            border-radius: 4px;
            box-shadow: inset 0 0 5px rgba(255,255,255,0.4);
        }

        .bb-dragging {
            z-index: 1000 !important;
            opacity: 0.9;
            transform: scale(1.1);
            pointer-events: none;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-950 text-slate-100 selection:bg-purple-500 selection:text-white relative">

    <canvas id="particleCanvas" class="pointer-events-none fixed inset-0 z-50 w-full h-full"></canvas>
    <div id="goldenCookieContainer" class="fixed z-40"></div>

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
                        <p id="header-subtitle" class="text-xs text-slate-400 font-semibold">1,000 Blooks • Arcade Games • Supremes: Aidan & Maxim Malhaire</p>
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
                    <p class="text-slate-300 text-sm mt-1">Explore 50 themed packs! Pull the mythical 0.001% Supreme Blooks: <strong class="text-amber-300">Aidan 👑✨ & Maxim Malhaire 🌟✨</strong></p>
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
                        <option value="Supreme">Supreme (Aidan & Maxim)</option>
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
                <div class="flex items-center justify-between max-w-5xl mx-auto px-2">
                    <button onclick="switchTab('shop')" class="flex items-center gap-2 px-4 py-2 bg-slate-800 hover:bg-slate-700 text-slate-200 font-fredoka font-bold rounded-xl text-sm border border-slate-700 transition-all shadow-md active:translate-y-0.5">
                        <i class="fa-solid fa-arrow-left"></i> Exit Arcade Hub
                    </button>
                </div>

                <div class="text-center space-y-2">
                    <h2 class="font-fredoka text-3xl font-bold text-white flex items-center justify-center gap-2">
                        <span>🕹️</span> Mini Game Arcade
                    </h2>
                    <p class="text-slate-400 text-sm">Choose a game mode below! Every point earned converts <strong class="text-amber-400">1:1 directly to your Main Token Balance!</strong></p>
                </div>

                <!-- BLOOKET SIMULATOR ARCADE MODES (30 MIN COOLDOWN) -->
                <div class="space-y-4 max-w-5xl mx-auto">
                    <h3 class="font-fredoka text-xl font-bold text-slate-300 flex items-center gap-2">
                        <span>⭐</span> Token Earning Modes
                    </h3>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                        
                        <!-- Game 1: Don't Look Down -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-emerald-500/50 rounded-3xl p-6 flex flex-col justify-between space-y-4 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-3">
                                <div class="flex items-center justify-between">
                                    <span class="text-4xl">🧗‍♂️</span>
                                    <span class="bg-emerald-500/10 border border-emerald-500/30 text-emerald-400 px-3 py-1 rounded-full text-xs font-bold font-fredoka">Unlimited Plays</span>
                                </div>
                                <h3 class="font-fredoka text-2xl font-bold text-white">Don't Look Down</h3>
                                <p class="text-slate-400 text-sm">Climb as high as you can! Height reached converts directly 1:1 into Tokens.</p>
                            </div>
                            <button onclick="launchGame('dl-down')" class="w-full py-3 bg-emerald-600 hover:bg-emerald-500 text-white border-b-4 border-emerald-800 font-fredoka font-bold rounded-2xl transition-all">
                                PLAY DON'T LOOK DOWN 🚀
                            </button>
                        </div>

                        <!-- Game 2: Gold Quest (30m Cooldown) -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-amber-500/50 rounded-3xl p-6 flex flex-col justify-between space-y-4 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-3">
                                <div class="flex items-center justify-between">
                                    <span class="text-4xl">👑</span>
                                    <span id="gq-play-badge" class="bg-amber-500/10 border border-amber-500/30 text-amber-400 px-3 py-1 rounded-full text-xs font-bold font-fredoka">1 Play / 30m</span>
                                </div>
                                <h3 class="font-fredoka text-2xl font-bold text-white">Gold Quest</h3>
                                <p class="text-slate-400 text-sm">Answer questions across 10 rounds! All gold looted transfers 1:1 to Tokens.</p>
                            </div>
                            <button id="gq-play-btn" onclick="launchGame('gold-quest')" class="w-full py-3 bg-amber-500 hover:bg-amber-400 text-slate-950 border-b-4 border-amber-700 font-fredoka font-bold rounded-2xl transition-all">
                                PLAY GOLD QUEST 🪙
                            </button>
                        </div>

                        <!-- Game 3: Crypto Hack (30m Cooldown) -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-cyan-500/50 rounded-3xl p-6 flex flex-col justify-between space-y-4 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-3">
                                <div class="flex items-center justify-between">
                                    <span class="text-4xl">💻</span>
                                    <span id="ch-play-badge" class="bg-cyan-500/10 border border-cyan-500/30 text-cyan-400 px-3 py-1 rounded-full text-xs font-bold font-fredoka">1 Play / 30m</span>
                                </div>
                                <h3 class="font-fredoka text-2xl font-bold text-white">Crypto Hack</h3>
                                <p class="text-slate-400 text-sm">Hack target blooks across 10 rounds! Stolen crypto transfers 1:1 to Tokens.</p>
                            </div>
                            <button id="ch-play-btn" onclick="launchGame('crypto-hack')" class="w-full py-3 bg-cyan-600 hover:bg-cyan-500 text-white border-b-4 border-cyan-800 font-fredoka font-bold rounded-2xl transition-all">
                                PLAY CRYPTO HACK ⚡
                            </button>
                        </div>

                    </div>
                </div>

                <!-- STANDALONE MINI GAMES CATEGORY -->
                <div class="space-y-4 max-w-5xl mx-auto pt-6 border-t border-slate-800">
                    <h3 class="font-fredoka text-xl font-bold text-slate-300 flex items-center gap-2">
                        <span>🎮</span> External & Embedded Mini Games
                    </h3>
                    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                        
                        <!-- Block Blast: Neon Edition (NATIVE) -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-pink-500/50 rounded-2xl p-5 flex flex-col justify-between space-y-3 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-2">
                                <div class="flex items-center justify-between">
                                    <span class="text-3xl">🧱</span>
                                    <span class="bg-pink-500/10 border border-pink-500/30 text-pink-400 px-2.5 py-0.5 rounded-full text-[11px] font-bold font-fredoka">Puzzle</span>
                                </div>
                                <h4 class="font-fredoka text-lg font-bold text-white">Block Blast: Neon</h4>
                                <p class="text-slate-400 text-xs">Block puzzle game. 100% of score points transfer directly to your Token balance!</p>
                            </div>
                            <button onclick="launchGame('block-blast')" class="w-full py-2.5 bg-pink-600 hover:bg-pink-500 text-white font-fredoka font-bold rounded-xl text-center transition-all block">
                                PLAY GAME 🚀
                            </button>
                        </div>

                        <!-- Mini Soccer Arcade -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-green-500/50 rounded-2xl p-5 flex flex-col justify-between space-y-3 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-2">
                                <div class="flex items-center justify-between">
                                    <span class="text-3xl">⚽</span>
                                    <span class="bg-green-500/10 border border-green-500/30 text-green-400 px-2.5 py-0.5 rounded-full text-[11px] font-bold font-fredoka">Sports</span>
                                </div>
                                <h4 class="font-fredoka text-lg font-bold text-white">Mini Soccer Arcade</h4>
                                <p class="text-slate-400 text-xs">Arcade football. Wins grant +100 Tokens directly to your balance!</p>
                            </div>
                            <button onclick="launchExternalEmbedded('Mini Soccer Arcade', 'mini_soccer_arcade.html')" class="w-full py-2.5 bg-green-600 hover:bg-green-500 text-white font-fredoka font-bold rounded-xl text-center transition-all block">
                                PLAY GAME ⚽
                            </button>
                        </div>

                        <!-- Spellbound Arenas: Wizard Duels -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-purple-500/50 rounded-2xl p-5 flex flex-col justify-between space-y-3 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-2">
                                <div class="flex items-center justify-between">
                                    <span class="text-3xl">🧙‍♂️</span>
                                    <span class="bg-purple-500/10 border border-purple-500/30 text-purple-400 px-2.5 py-0.5 rounded-full text-[11px] font-bold font-fredoka">RPG / Duel</span>
                                </div>
                                <h4 class="font-fredoka text-lg font-bold text-white">Spellbound Arenas</h4>
                                <p class="text-slate-400 text-xs">Wizard duels. Duel reward points and gold convert directly to Main Tokens!</p>
                            </div>
                            <button onclick="launchExternalEmbedded('Spellbound Arenas', 'spellbound_arenas_wizard_duels.html')" class="w-full py-2.5 bg-purple-600 hover:bg-purple-500 text-white font-fredoka font-bold rounded-xl text-center transition-all block">
                                PLAY GAME 🪄
                            </button>
                        </div>

                        <!-- NEON CHESS // AI CORE 2077 -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-cyan-500/50 rounded-2xl p-5 flex flex-col justify-between space-y-3 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-2">
                                <div class="flex items-center justify-between">
                                    <span class="text-3xl">♟️</span>
                                    <span class="bg-cyan-500/10 border border-cyan-500/30 text-cyan-400 px-2.5 py-0.5 rounded-full text-[11px] font-bold font-fredoka">Strategy</span>
                                </div>
                                <h4 class="font-fredoka text-lg font-bold text-white">Neon Chess 2077</h4>
                                <p class="text-slate-400 text-xs">Cyberpunk chess engine. Winning grants +100 Tokens to your balance!</p>
                            </div>
                            <button onclick="launchExternalEmbedded('Neon Chess 2077', 'neon_cyberpunk_chess (1).html')" class="w-full py-2.5 bg-cyan-600 hover:bg-cyan-500 text-white font-fredoka font-bold rounded-xl text-center transition-all block">
                                PLAY GAME ♟️
                            </button>
                        </div>

                        <!-- Cookie Clicker: Deluxe Edition (30m play limit / 2hr Cooldown) -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-amber-500/50 rounded-2xl p-5 flex flex-col justify-between space-y-3 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-2">
                                <div class="flex items-center justify-between">
                                    <span class="text-3xl">🍪</span>
                                    <span id="cc-play-badge" class="bg-amber-500/10 border border-amber-500/30 text-amber-400 px-2.5 py-0.5 rounded-full text-[11px] font-bold font-fredoka">30m Play / 2h Limit</span>
                                </div>
                                <h4 class="font-fredoka text-lg font-bold text-white">Cookie Clicker: Deluxe</h4>
                                <p class="text-slate-400 text-xs">1 Cookie = 1 Token! Play up to 30 mins per 2-hour cooldown period.</p>
                            </div>
                            <button id="cc-play-btn" onclick="launchGame('cookie-clicker')" class="w-full py-2.5 bg-amber-600 hover:bg-amber-500 text-white font-fredoka font-bold rounded-xl text-center transition-all block">
                                PLAY GAME 🍪
                            </button>
                        </div>

                        <!-- NEW: Ocean Explorer Fishing -->
                        <div class="bg-slate-900 border border-slate-800 hover:border-cyan-500/50 rounded-2xl p-5 flex flex-col justify-between space-y-3 shadow-xl transition-all hover:-translate-y-1">
                            <div class="space-y-2">
                                <div class="flex items-center justify-between">
                                    <span class="text-3xl">🎣</span>
                                    <span class="bg-cyan-500/10 border border-cyan-500/30 text-cyan-400 px-2.5 py-0.5 rounded-full text-[11px] font-bold font-fredoka">Arcade / Fishing</span>
                                </div>
                                <h4 class="font-fredoka text-lg font-bold text-white">Ocean Explorer Fishing</h4>
                                <p class="text-slate-400 text-xs">Deep sea neon fishing! All coins earned convert 1:1 directly to Main Tokens!</p>
                            </div>
                            <button onclick="launchExternalEmbedded('Ocean Explorer Fishing', 'ocean_explorer_fishing.html')" class="w-full py-2.5 bg-cyan-600 hover:bg-cyan-500 text-white font-fredoka font-bold rounded-xl text-center transition-all block">
                                PLAY GAME 🎣
                            </button>
                        </div>

                    </div>
                </div>

            </div>

            <!-- ACTIVE GAME VIEWPORT CONTAINER -->
            <div id="active-game-wrapper" class="hidden space-y-4">
                <div class="flex items-center justify-between max-w-5xl mx-auto px-2">
                    <button onclick="returnToGameHub()" class="flex items-center gap-2 px-4 py-2 bg-slate-800 hover:bg-slate-700 text-slate-200 font-fredoka font-bold rounded-xl text-sm border border-slate-700 transition-all">
                        <i class="fa-solid fa-arrow-left"></i> Exit to Arcade Hub
                    </button>
                    <div class="flex items-center gap-3">
                        <button onclick="manualSyncPrompt()" class="flex items-center gap-1.5 px-3 py-1.5 bg-amber-500/20 hover:bg-amber-500/30 text-amber-300 font-fredoka text-xs font-bold rounded-xl border border-amber-500/40 transition-all">
                            <span>🪙</span> Sync / Claim Tokens
                        </button>
                        <span id="active-game-title" class="font-fredoka font-bold text-lg text-amber-400">Game Active</span>
                    </div>
                </div>

                <!-- EMBEDDED EXTERNAL IFRAME VIEW -->
                <div id="view-external-iframe" class="game-view hidden max-w-lg mx-auto bg-slate-900 border border-slate-800 rounded-2xl p-2 sm:p-3 shadow-2xl transition-all">
                    <iframe id="external-iframe-el" src="" class="w-full h-[460px] sm:h-[490px] max-h-[calc(100vh-200px)] rounded-xl border-0 bg-slate-950 shadow-inner"></iframe>
                </div>

                <!-- NATIVE BLOCK BLAST VIEW -->
                <div id="view-block-blast" class="game-view hidden max-w-md mx-auto bg-slate-900 border border-slate-800 rounded-3xl p-4 text-center space-y-4 shadow-2xl">
                    <div class="flex items-center justify-between border-b border-slate-800 pb-3 px-2">
                        <div>
                            <h3 class="font-fredoka text-xl font-bold bg-gradient-to-r from-pink-500 to-purple-400 bg-clip-text text-transparent text-left">BLOCK BLAST</h3>
                            <p class="text-[11px] text-slate-400 text-left">Drag blocks to clear lines!</p>
                        </div>
                        <div class="bg-slate-950 border border-slate-800 px-3 py-1 rounded-xl text-right">
                            <span class="text-[10px] text-slate-400 font-bold block uppercase">Tokens Earned</span>
                            <span id="bb-score-text" class="text-lg font-bold text-amber-400">0</span> 🪙
                        </div>
                    </div>

                    <div id="bb-game-board" class="grid grid-cols-8 grid-rows-8 gap-1 w-full max-w-[360px] h-[360px] mx-auto bg-slate-950 border-2 border-slate-800 rounded-2xl p-1.5 shadow-inner"></div>

                    <div id="bb-piece-dock" class="flex justify-around items-center w-full max-w-[360px] h-[110px] mx-auto bg-slate-950 border-2 border-slate-800 rounded-2xl p-2">
                        <div class="bb-dock-slot relative w-[90px] h-[90px] flex items-center justify-center" data-slot="0"></div>
                        <div class="bb-dock-slot relative w-[90px] h-[90px] flex items-center justify-center" data-slot="1"></div>
                        <div class="bb-dock-slot relative w-[90px] h-[90px] flex items-center justify-center" data-slot="2"></div>
                    </div>
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
                            <div class="hud-card">Tokens Earned: <span id="coin-val">0</span> 🪙</div>
                        </div>
                        <div class="controls-tip">A / D or ← / →: Move | Space / W: Jump</div>
                        <button id="quiz-btn" onclick="openQuiz('dl-down')">Earn Energy ⚡</button>
                    </div>
                </div>

                <!-- 2. GOLD QUEST VIEW -->
                <div id="view-gold-quest" class="game-view hidden max-w-2xl mx-auto bg-slate-900 border border-slate-800 rounded-3xl p-6 text-center space-y-6">
                    <div class="flex justify-between items-center border-b border-slate-800 pb-4">
                        <span class="font-fredoka font-bold text-slate-400">Round: <span id="gq-round" class="text-white">1</span>/10</span>
                        <span class="font-fredoka font-bold text-amber-400 text-xl">Gold Looted: <span id="gq-gold">0</span> 🪙</span>
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
                        <span class="font-fredoka font-bold text-cyan-400 text-xl">Crypto Stolen: <span id="ch-crypto">0</span> ⚡</span>
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

                <!-- 4. COOKIE CLICKER DELUXE VIEW -->
                <div id="view-cookie-clicker" class="game-view hidden max-w-6xl mx-auto bg-bakery-900 border border-bakery-700 rounded-3xl overflow-hidden shadow-2xl">
                    <div class="flex flex-col lg:flex-row min-h-[600px]">
                        
                        <section class="w-full lg:w-5/12 bg-gradient-to-b from-bakery-800 to-bakery-900 p-6 flex flex-col items-center justify-between relative border-b lg:border-b-0 lg:border-r border-bakery-700 shadow-2xl min-h-[500px] overflow-hidden no-select">
                            <div class="absolute inset-0 flex items-center justify-center opacity-10 pointer-events-none overflow-hidden">
                                <div class="w-[800px] h-[800px] animate-spin-slow bg-[conic-gradient(at_center,_var(--tw-gradient-stops))] from-amber-300 via-transparent to-amber-300 rounded-full"></div>
                            </div>
                            <div class="z-10 text-center w-full mt-2">
                                <div class="flex items-center justify-center gap-2">
                                    <h1 id="bakeryName" class="font-fredoka text-2xl lg:text-3xl font-bold text-amber-300 tracking-wide cursor-pointer hover:text-amber-200 transition-colors" title="Click to rename bakery" onclick="cc_renameBakery()">
                                        My Delicious Bakery
                                    </h1>
                                    <i class="fa-solid fa-pen text-xs text-amber-500/70 cursor-pointer" onclick="cc_renameBakery()"></i>
                                </div>
                                <p class="text-xs text-amber-400/70 mt-1">Baking delicious cookies since eternity</p>
                            </div>
                            <div class="z-10 text-center my-4 bg-bakery-900/80 px-6 py-4 rounded-2xl border border-bakery-600/50 backdrop-blur-md w-full max-w-xs shadow-lg">
                                <div id="cookieCount" class="font-fredoka text-3xl lg:text-4xl font-extrabold text-amber-300 drop-shadow-md">
                                    0
                                </div>
                                <div class="text-xs font-semibold text-amber-200/80 uppercase tracking-widest mt-1">
                                    Cookies
                                </div>
                                <div class="grid grid-cols-2 gap-2 mt-4 pt-3 border-t border-bakery-700/60 text-xs">
                                    <div>
                                        <span class="text-amber-400/70 block">per second</span>
                                        <span id="cpsDisplay" class="font-bold text-amber-200 text-sm">0</span>
                                    </div>
                                    <div>
                                        <span class="text-amber-400/70 block">per click</span>
                                        <span id="cpcDisplay" class="font-bold text-amber-200 text-sm">1</span>
                                    </div>
                                </div>
                            </div>
                            <div class="relative z-10 my-auto py-4 flex items-center justify-center">
                                <div class="absolute w-56 h-56 bg-amber-500/20 rounded-full blur-2xl pointer-events-none"></div>
                                <button id="bigCookie" class="relative group outline-none focus:outline-none transition-transform duration-75 ease-out cursor-pointer active:scale-95" aria-label="Click Big Cookie">
                                    <svg class="w-48 h-48 lg:w-56 lg:h-56 drop-shadow-[0_15px_25px_rgba(0,0,0,0.6)] transition-transform duration-200 group-hover:scale-105" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
                                        <circle cx="100" cy="100" r="90" fill="url(#cookieGradient)"/>
                                        <circle cx="100" cy="100" r="86" stroke="#8C5027" stroke-width="3" opacity="0.4"/>
                                        <path d="M 20 100 Q 15 80 25 60 Q 40 20 80 15 Q 120 10 160 30 Q 185 60 180 100 Q 185 140 160 170 Q 120 190 80 185 Q 35 180 20 140 Z" fill="#D98A4B"/>
                                        <path d="M 25 95 Q 20 78 30 60 Q 45 25 82 20 Q 120 15 155 35 Q 178 62 175 98 Q 178 135 155 162 Q 120 182 82 178 Q 40 172 25 135 Z" fill="url(#cookieGradient)"/>
                                        <ellipse cx="60" cy="55" rx="12" ry="9" transform="rotate(-15 60 55)" fill="#3B1E08"/>
                                        <ellipse cx="62" cy="56" rx="8" ry="6" transform="rotate(-15 62 56)" fill="#261204"/>
                                        <ellipse cx="125" cy="50" rx="14" ry="10" transform="rotate(25 125 50)" fill="#3B1E08"/>
                                        <ellipse cx="127" cy="51" rx="9" ry="6" transform="rotate(25 127 51)" fill="#261204"/>
                                        <ellipse cx="150" cy="105" rx="13" ry="11" transform="rotate(-10 150 105)" fill="#3B1E08"/>
                                        <ellipse cx="151" cy="106" rx="8" ry="7" transform="rotate(-10 151 106)" fill="#261204"/>
                                        <ellipse cx="90" cy="110" rx="16" ry="12" transform="rotate(40 90 110)" fill="#3B1E08"/>
                                        <ellipse cx="92" cy="111" rx="10" ry="8" transform="rotate(40 92 111)" fill="#261204"/>
                                        <ellipse cx="130" cy="145" rx="11" ry="9" transform="rotate(-30 130 145)" fill="#3B1E08"/>
                                        <ellipse cx="131" cy="146" rx="7" ry="5" transform="rotate(-30 131 146)" fill="#261204"/>
                                        <ellipse cx="65" cy="140" rx="13" ry="10" transform="rotate(15 65 140)" fill="#3B1E08"/>
                                        <ellipse cx="66" cy="141" rx="8" ry="6" transform="rotate(15 66 141)" fill="#261204"/>
                                        <circle cx="95" cy="65" r="4" fill="#3B1E08"/>
                                        <circle cx="155" cy="70" r="3" fill="#261204"/>
                                        <circle cx="45" cy="100" r="5" fill="#3B1E08"/>
                                        <circle cx="105" cy="155" r="4" fill="#261204"/>
                                        <path d="M 40 60 Q 70 30 120 30" stroke="#FCE7F3" stroke-width="4" stroke-linecap="round" opacity="0.25"/>
                                        <defs>
                                            <radialGradient id="cookieGradient" cx="40%" cy="40%" r="60%">
                                                <stop offset="0%" stop-color="#EAA368"/>
                                                <stop offset="70%" stop-color="#C87533"/>
                                                <stop offset="100%" stop-color="#9E5118"/>
                                            </radialGradient>
                                        </defs>
                                    </svg>
                                </button>
                            </div>
                            <div id="buffContainer" class="z-10 min-h-[36px] flex flex-wrap gap-2 justify-center my-1 w-full"></div>
                            <div class="absolute bottom-0 left-0 right-0 h-12 pointer-events-none overflow-hidden z-0 opacity-80">
                                <div id="milkLevel" class="absolute inset-x-0 bottom-0 bg-amber-100/20 transition-all duration-700 ease-out" style="height: 20%;">
                                    <svg class="absolute -top-3 left-0 w-[200%] h-4 animate-milk-wave fill-amber-100/30" viewBox="0 0 1200 120" preserveAspectRatio="none">
                                        <path d="M0,0 C150,90 350,-40 500,40 C650,120 900,-20 1200,40 L1200,120 L0,120 Z"></path>
                                    </svg>
                                </div>
                            </div>
                        </section>

                        <section class="w-full lg:w-7/12 bg-bakery-900 flex flex-col h-[600px] overflow-hidden no-select">
                            <header class="bg-bakery-800 border-b border-bakery-700 px-4 py-3 flex flex-wrap items-center justify-between gap-2 z-10 shadow-md">
                                <nav class="flex items-center gap-1 bg-bakery-900/60 p-1 rounded-xl border border-bakery-700/60 text-xs sm:text-sm overflow-x-auto">
                                    <button id="cc-tabBtn-buildings" onclick="cc_switchTab('buildings')" class="cc-tab-btn px-3 py-1.5 rounded-lg font-semibold transition-all flex items-center gap-2 bg-amber-500 text-bakery-900 shadow">
                                        <i class="fa-solid fa-store"></i> Buildings
                                    </button>
                                    <button id="cc-tabBtn-upgrades" onclick="cc_switchTab('upgrades')" class="cc-tab-btn px-3 py-1.5 rounded-lg font-semibold text-amber-200/80 hover:bg-bakery-700/50 transition-all flex items-center gap-2">
                                        <i class="fa-solid fa-bolt"></i> Upgrades <span id="upgradeBadge" class="hidden bg-amber-400 text-bakery-900 text-[10px] px-1.5 py-0.2 rounded-full font-bold">0</span>
                                    </button>
                                    <button id="cc-tabBtn-achievements" onclick="cc_switchTab('achievements')" class="cc-tab-btn px-3 py-1.5 rounded-lg font-semibold text-amber-200/80 hover:bg-bakery-700/50 transition-all flex items-center gap-2">
                                        <i class="fa-solid fa-trophy"></i> Badges
                                    </button>
                                    <button id="cc-tabBtn-stats" onclick="cc_switchTab('stats')" class="cc-tab-btn px-3 py-1.5 rounded-lg font-semibold text-amber-200/80 hover:bg-bakery-700/50 transition-all flex items-center gap-2">
                                        <i class="fa-solid fa-chart-line"></i> Stats
                                    </button>
                                    <button id="cc-tabBtn-settings" onclick="cc_switchTab('settings')" class="cc-tab-btn px-3 py-1.5 rounded-lg font-semibold text-amber-200/80 hover:bg-bakery-700/50 transition-all flex items-center gap-2">
                                        <i class="fa-solid fa-gear"></i> Options
                                    </button>
                                </nav>
                            </header>

                            <main class="flex-1 overflow-y-auto p-4 lg:p-6 relative">
                                <div id="cc-tab-buildings" class="cc-tab-content space-y-3">
                                    <div class="flex items-center justify-between mb-2">
                                        <h2 class="font-fredoka text-xl font-bold text-amber-200">Cookie Production Facilities</h2>
                                        <span class="text-xs text-amber-400/60">Price curve (15% per building)</span>
                                    </div>
                                    <div id="buildingsList" class="grid grid-cols-1 gap-2.5"></div>
                                </div>

                                <div id="cc-tab-upgrades" class="cc-tab-content hidden space-y-4">
                                    <div>
                                        <h2 class="font-fredoka text-xl font-bold text-amber-200 mb-1">Bakery Enhancements & Click Boosters</h2>
                                        <p class="text-xs text-amber-400/70">Unlock powerful click power & passive multipliers as you progress!</p>
                                    </div>
                                    <div id="upgradesList" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-3"></div>
                                    <div id="noUpgradesMessage" class="hidden text-center py-12 text-amber-400/50 italic">
                                        No upgrades available right now. Keep baking cookies to unlock more!
                                    </div>
                                </div>

                                <div id="cc-tab-achievements" class="cc-tab-content hidden space-y-4">
                                    <div class="flex items-center justify-between">
                                        <div>
                                            <h2 class="font-fredoka text-xl font-bold text-amber-200">Hall of Fame & Achievements</h2>
                                            <p class="text-xs text-amber-400/70">Milestones unlocked raise your bakery milk level!</p>
                                        </div>
                                        <div class="text-right">
                                            <span id="achievementProgressText" class="text-sm font-bold text-amber-400">0 / 0</span>
                                        </div>
                                    </div>
                                    <div id="achievementsList" class="grid grid-cols-1 sm:grid-cols-2 gap-3"></div>
                                </div>

                                <div id="cc-tab-stats" class="cc-tab-content hidden space-y-4">
                                    <h2 class="font-fredoka text-xl font-bold text-amber-200 mb-2">Bakery Statistics</h2>
                                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                                        <div class="bg-bakery-800/80 p-4 rounded-xl border border-bakery-700">
                                            <h3 class="text-xs font-semibold text-amber-400/70 uppercase tracking-wider mb-3">General Progress</h3>
                                            <ul class="space-y-2 text-sm">
                                                <li class="flex justify-between"><span class="text-amber-100/70">Cookies in Bank:</span> <span id="statCookiesBank" class="font-mono text-amber-300">0</span></li>
                                                <li class="flex justify-between"><span class="text-amber-100/70">Cookies Baked (All Time):</span> <span id="statCookiesBaked" class="font-mono text-amber-300">0</span></li>
                                                <li class="flex justify-between"><span class="text-amber-100/70">Cookie Clicks:</span> <span id="statCookieClicks" class="font-mono text-amber-300">0</span></li>
                                                <li class="flex justify-between"><span class="text-amber-100/70">Hand-made Cookies:</span> <span id="statHandmade" class="font-mono text-amber-300">0</span></li>
                                                <li class="flex justify-between"><span class="text-amber-100/70">Golden Cookies Clicked:</span> <span id="statGoldenClicked" class="font-mono text-amber-300">0</span></li>
                                                <li class="flex justify-between"><span class="text-amber-100/70">Time Played:</span> <span id="statTimePlayed" class="font-mono text-amber-300">0s</span></li>
                                            </ul>
                                        </div>
                                        <div class="bg-bakery-800/80 p-4 rounded-xl border border-bakery-700">
                                            <h3 class="text-xs font-semibold text-amber-400/70 uppercase tracking-wider mb-3">Production Ratios</h3>
                                            <div class="space-y-3">
                                                <div>
                                                    <div class="flex justify-between text-xs mb-1">
                                                        <span>Hand-made Share</span>
                                                        <span id="handmadePercent">0%</span>
                                                    </div>
                                                    <div class="w-full bg-bakery-900 rounded-full h-2 overflow-hidden border border-bakery-700">
                                                        <div id="handmadeBar" class="bg-amber-500 h-full transition-all duration-300" style="width: 0%"></div>
                                                    </div>
                                                </div>
                                                <div>
                                                    <div class="flex justify-between text-xs mb-1">
                                                        <span>Passive Income Share</span>
                                                        <span id="passivePercent">0%</span>
                                                    </div>
                                                    <div class="w-full bg-bakery-900 rounded-full h-2 overflow-hidden border border-bakery-700">
                                                        <div id="passiveBar" class="bg-amber-300 h-full transition-all duration-300" style="width: 0%"></div>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>

                                <div id="cc-tab-settings" class="cc-tab-content hidden space-y-6">
                                    <h2 class="font-fredoka text-xl font-bold text-amber-200 mb-2">Game Options & Data</h2>
                                    <div class="bg-bakery-800/80 p-5 rounded-xl border border-bakery-700 space-y-4">
                                        <h3 class="text-sm font-bold text-amber-300 uppercase tracking-wider border-b border-bakery-700 pb-2">Save Management</h3>
                                        <div class="flex flex-wrap gap-3">
                                            <button onclick="cc_saveGame(true)" class="px-4 py-2 bg-amber-600 hover:bg-amber-500 text-bakery-900 font-bold rounded-lg transition-colors flex items-center gap-2">
                                                <i class="fa-solid fa-floppy-disk"></i> Save Game
                                            </button>
                                            <button onclick="cc_exportSave()" class="px-4 py-2 bg-bakery-700 hover:bg-bakery-600 text-amber-200 font-bold rounded-lg transition-colors flex items-center gap-2">
                                                <i class="fa-solid fa-download"></i> Export Save Code
                                            </button>
                                            <button onclick="cc_importSave()" class="px-4 py-2 bg-bakery-700 hover:bg-bakery-600 text-amber-200 font-bold rounded-lg transition-colors flex items-center gap-2">
                                                <i class="fa-solid fa-upload"></i> Import Save Code
                                            </button>
                                            <button onclick="cc_confirmReset()" class="px-4 py-2 bg-red-900/80 hover:bg-red-700 text-red-200 font-bold rounded-lg transition-colors flex items-center gap-2 ml-auto">
                                                <i class="fa-solid fa-trash-can"></i> Wipe Progress
                                            </button>
                                        </div>
                                        <p class="text-xs text-amber-400/60">Game automatically saves to LocalStorage.</p>
                                    </div>
                                </div>
                            </main>
                        </section>
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

    <!-- MODAL 2: SUPREME PULL CELEBRATION MODAL -->
    <div id="modal-aidan" class="fixed inset-0 z-50 hidden bg-black/95 backdrop-blur-xl flex items-center justify-center p-4">
        <div class="aidan-card w-full max-w-lg rounded-3xl p-8 text-center shadow-2xl space-y-6 text-white relative">
            <div class="animate-bounce text-6xl">👑✨</div>
            <h2 class="font-fredoka text-4xl font-extrabold text-amber-300 drop-shadow-md">
                SUPREME UNLOCKED!
            </h2>
            <div class="bg-slate-950/80 rounded-2xl p-6 border-2 border-amber-400/80 space-y-3">
                <div id="supreme-modal-emoji" class="text-7xl animate-pulse">🌟✨</div>
                <h3 id="supreme-modal-name" class="font-fredoka text-3xl font-bold text-amber-300">Maxim Malhaire</h3>
                <span class="inline-block px-4 py-1 rounded-full text-xs font-bold uppercase tracking-wider bg-pink-600 text-white shadow-md">
                    SUPREME (0.001% Drop Rate)
                </span>
                <p class="text-xs text-slate-300">You pulled one of the rarest Blooks in the entire simulator!</p>
            </div>
            <button id="supreme-modal-btn" onclick="closeModal('modal-aidan')" class="px-8 py-3.5 rounded-2xl font-fredoka font-bold text-lg bg-amber-400 hover:bg-amber-300 text-slate-950 border-b-4 border-amber-600 active:translate-y-0.5 transition-all shadow-xl">
                CLAIM Supreme Blook 👑
            </button>
        </div>
    </div>

    <div id="toast-container" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2 pointer-events-none"></div>

    <script>
        let activeTab = 'shop';

        // CONSTANTS FOR TIME LIMITS
        const HALF_HOUR_MS = 30 * 60 * 1000;  // 30 Minutes
        const TWO_HOURS_MS = 120 * 60 * 1000; // 2 Hours

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

                if (pack.id === 50 && i === 19) {
                    ALL_BLOOKS.push({
                        id: 999,
                        name: "Maxim Malhaire",
                        packId: 50,
                        packName: pack.name,
                        rarity: "Supreme",
                        emoji: "🌟✨",
                        dropChance: 0.001,
                        sellValue: 500000,
                        isSupreme: true
                    });
                } else if (blookId === 1000 || (pack.id === 50 && i === 20)) {
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

        let gameState = {
            tokens: 5000,
            inventory: {},
            lastSpinTimestamp: null,
            soundEnabled: true,
            lastPlayTimestamps: {
                'gold-quest': null,
                'crypto-hack': null,
                'cookie-clicker': null
            }
        };

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
                    gameState.tokens = Number(gameState.tokens) || 0;
                    if (!gameState.lastPlayTimestamps) {
                        gameState.lastPlayTimestamps = { 'gold-quest': null, 'crypto-hack': null, 'cookie-clicker': null };
                    }
                } catch (e) {}
            }
            updateGlobalHeader();
            updateArcadeHubUI();
        }

        function saveState() {
            updateGlobalHeader();
            updateArcadeHubUI();
            try {
                localStorage.setItem("blooket_sim_1k_v1", JSON.stringify(gameState));
            } catch (e) {}
        }

        setInterval(() => {
            saveState();
        }, 3000);

        function updateGlobalHeader() {
            const tokenEl = document.getElementById("stat-tokens");
            if (tokenEl) tokenEl.innerText = Math.floor(Number(gameState.tokens) || 0).toLocaleString();
            const unlockedCount = Object.keys(gameState.inventory).filter(id => gameState.inventory[id] > 0).length;
            const unlockEl = document.getElementById("stat-unlocked");
            if (unlockEl) unlockEl.innerText = unlockedCount;
            const pctEl = document.getElementById("stat-percent");
            if (pctEl) pctEl.innerText = `(${((unlockedCount / 1000) * 100).toFixed(1)}%)`;

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

            const gqBadge = document.getElementById("gq-play-badge");
            const chBadge = document.getElementById("ch-play-badge");
            const ccBadge = document.getElementById("cc-play-badge");
            const gqBtn = document.getElementById("gq-play-btn");
            const chBtn = document.getElementById("ch-play-btn");
            const ccBtn = document.getElementById("cc-play-btn");

            // Gold Quest 30m Cooldown logic
            const gqLast = lastPlay['gold-quest'];
            if (gqLast && (now - gqLast < HALF_HOUR_MS)) {
                const remMs = HALF_HOUR_MS - (now - gqLast);
                const mins = Math.floor(remMs / (1000 * 60));
                const secs = Math.floor((remMs % (1000 * 60)) / 1000);
                if (gqBadge) gqBadge.innerText = `⏳ Ready in ${mins}m ${secs}s`;
                if (gqBtn) {
                    gqBtn.classList.add("opacity-50", "cursor-not-allowed");
                    gqBtn.innerText = `30M LIMIT REACHED (${mins}m ${secs}s) 🔒`;
                }
            } else {
                if (gqBadge) gqBadge.innerText = `1 Play / 30m`;
                if (gqBtn) {
                    gqBtn.classList.remove("opacity-50", "cursor-not-allowed");
                    gqBtn.innerText = "PLAY GOLD QUEST 🪙";
                }
            }

            // Crypto Hack 30m Cooldown logic
            const chLast = lastPlay['crypto-hack'];
            if (chLast && (now - chLast < HALF_HOUR_MS)) {
                const remMs = HALF_HOUR_MS - (now - chLast);
                const mins = Math.floor(remMs / (1000 * 60));
                const secs = Math.floor((remMs % (1000 * 60)) / 1000);
                if (chBadge) chBadge.innerText = `⏳ Ready in ${mins}m ${secs}s`;
                if (chBtn) {
                    chBtn.classList.add("opacity-50", "cursor-not-allowed");
                    chBtn.innerText = `30M LIMIT REACHED (${mins}m ${secs}s) 🔒`;
                }
            } else {
                if (chBadge) chBadge.innerText = `1 Play / 30m`;
                if (chBtn) {
                    chBtn.classList.remove("opacity-50", "cursor-not-allowed");
                    chBtn.innerText = "PLAY CRYPTO HACK ⚡";
                }
            }

            // Cookie Clicker 2-Hour Cooldown logic
            const ccLast = lastPlay['cookie-clicker'];
            if (ccLast && (now - ccLast < TWO_HOURS_MS)) {
                const remMs = TWO_HOURS_MS - (now - ccLast);
                const mins = Math.floor(remMs / (1000 * 60));
                const secs = Math.floor((remMs % (1000 * 60)) / 1000);
                if (ccBadge) ccBadge.innerText = `⏳ Ready in ${mins}m ${secs}s`;
                if (ccBtn) {
                    ccBtn.classList.add("opacity-50", "cursor-not-allowed");
                    ccBtn.innerText = `2H LIMIT REACHED (${mins}m ${secs}s) 🔒`;
                }
            } else {
                if (ccBadge) ccBadge.innerText = `30m Play / 2h Limit`;
                if (ccBtn) {
                    ccBtn.classList.remove("opacity-50", "cursor-not-allowed");
                    ccBtn.innerText = "PLAY GAME 🍪";
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
            if (!pack) return;
            const totalCost = pack.cost * count;

            const currentTokens = Number(gameState.tokens) || 0;
            if (currentTokens < totalCost) {
                showToast(`⚠ You need 🪙 ${totalCost.toLocaleString()} tokens!`, 'error');
                return;
            }

            gameState.tokens = Math.max(0, currentTokens - totalCost);
            saveState();
            updateGlobalHeader();
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

            let pulledSupremeBlook = null;
            results.forEach((blook) => {
                if (blook.isSupreme) pulledSupremeBlook = blook;
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
            if (pulledSupremeBlook) {
                document.getElementById("supreme-modal-emoji").innerText = pulledSupremeBlook.emoji;
                document.getElementById("supreme-modal-name").innerText = pulledSupremeBlook.name;
                document.getElementById("supreme-modal-btn").innerText = `CLAIM ${pulledSupremeBlook.name} ✨`;
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
            gameState.tokens = (Number(gameState.tokens) || 0) + blook.sellValue;
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

            gameState.tokens = (Number(gameState.tokens) || 0) + totalTokensEarned;
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
                transferGamePoints(reward);
                gameState.lastSpinTimestamp = Date.now();
                saveState();
                checkWheelCooldown();
                confetti({ particleCount: 100, spread: 80, origin: { y: 0.6 } });
                canvas.style.transform = 'rotate(0deg)';
            }, 4000);
        }

        // MULTI MINI-GAMES ARCADE ENGINE
        let currentGameMode = null;
        let activeQuizCallback = null;
        let cookieClickerSessionTimer = null;

        function launchGame(gameId) {
            gameState.lastPlayTimestamps = gameState.lastPlayTimestamps || {};
            const now = Date.now();

            // ENFORCE 30-MINUTE COOLDOWN FOR GOLD QUEST AND CRYPTO HACK
            if (gameId === 'gold-quest' || gameId === 'crypto-hack') {
                const lastPlay = gameState.lastPlayTimestamps[gameId];

                if (lastPlay && (now - lastPlay < HALF_HOUR_MS)) {
                    const remMs = HALF_HOUR_MS - (now - lastPlay);
                    const mins = Math.floor(remMs / (1000 * 60));
                    const secs = Math.floor((remMs % (1000 * 60)) / 1000);
                    showToast(`⚠ Cooldown active! Try again in ${mins}m ${secs}s.`, "error");
                    return;
                }
                gameState.lastPlayTimestamps[gameId] = now;
                saveState();
            }

            // ENFORCE 30-MIN PLAY LIMIT PER 2 HOURS FOR COOKIE CLICKER
            if (gameId === 'cookie-clicker') {
                const lastPlay = gameState.lastPlayTimestamps['cookie-clicker'];

                if (lastPlay && (now - lastPlay < TWO_HOURS_MS)) {
                    const remMs = TWO_HOURS_MS - (now - lastPlay);
                    const mins = Math.floor(remMs / (1000 * 60));
                    const secs = Math.floor((remMs % (1000 * 60)) / 1000);
                    showToast(`⚠ Cookie Clicker play limit reached! Try again in ${mins}m ${secs}s.`, "error");
                    return;
                }
                gameState.lastPlayTimestamps['cookie-clicker'] = now;
                saveState();

                // Start 30-minute session duration timer
                if (cookieClickerSessionTimer) clearTimeout(cookieClickerSessionTimer);
                cookieClickerSessionTimer = setTimeout(() => {
                    if (currentGameMode === 'cookie-clicker') {
                        showToast("⏰ Your 30-minute Cookie Clicker session has ended! (2-hour cooldown started)", "info");
                        returnToGameHub();
                    }
                }, HALF_HOUR_MS);
            }

            currentGameMode = gameId;
            document.getElementById("minigame-hub").classList.add("hidden");
            document.getElementById("active-game-wrapper").classList.remove("hidden");
            document.querySelectorAll(".game-view").forEach(v => v.classList.add("hidden"));

            const titleMap = {
                'dl-down': '🧗‍♂️ Don\'t Look Down',
                'gold-quest': '👑 Gold Quest',
                'crypto-hack': '💻 Crypto Hack',
                'cookie-clicker': '🍪 Cookie Clicker: Deluxe Edition',
                'block-blast': '🧱 Block Blast: Neon'
            };
            document.getElementById("active-game-title").innerText = titleMap[gameId] || "Mini Game";
            document.getElementById(`view-${gameId}`).classList.remove("hidden");

            if (gameId === 'dl-down') resetDLGame();
            if (gameId === 'gold-quest') startGoldQuest();
            if (gameId === 'crypto-hack') startCryptoHack();
            if (gameId === 'cookie-clicker') initCookieClicker();
            if (gameId === 'block-blast') initBlockBlast();
        }

        function launchExternalEmbedded(gameTitle, gameUrl) {
            currentGameMode = 'external-iframe';
            document.getElementById("minigame-hub").classList.add("hidden");
            document.getElementById("active-game-wrapper").classList.remove("hidden");
            document.querySelectorAll(".game-view").forEach(v => v.classList.add("hidden"));

            document.getElementById("active-game-title").innerText = gameTitle;
            const view = document.getElementById("view-external-iframe");
            const iframe = document.getElementById("external-iframe-el");

            view.className = "game-view max-w-4xl mx-auto bg-slate-900 border border-slate-800 rounded-3xl p-2 sm:p-4 shadow-2xl transition-all";
            iframe.className = "w-full h-[520px] max-h-[calc(100vh-200px)] rounded-2xl border-0 bg-slate-950";

            iframe.src = gameUrl;
            view.classList.remove("hidden");

            // Smoothly scroll active game window into focus
            document.getElementById("active-game-wrapper").scrollIntoView({ behavior: 'smooth', block: 'start' });

            iframe.onload = function() {
                try {
                    const win = iframe.contentWindow;
                    if (win) {
                        win.transferGamePoints = window.transferGamePoints;
                        win.addTokens = window.transferGamePoints;
                        win.sendGameScore = window.sendGameScore;
                        win.gameState = gameState;
                        win.onGameWin = function(bonus = 100) {
                            window.transferGamePoints(bonus);
                        };
                    }
                } catch(e) {}
            };
        }

        function returnToGameHub() {
            if (cookieClickerSessionTimer) {
                clearTimeout(cookieClickerSessionTimer);
                cookieClickerSessionTimer = null;
            }

            const iframe = document.getElementById("external-iframe-el");
            if (iframe) iframe.src = "";

            document.getElementById("active-game-wrapper").classList.add("hidden");
            document.getElementById("quiz-modal").classList.add("hidden");
            document.getElementById("minigame-hub").classList.remove("hidden");
            currentGameMode = null;
            saveState();
            updateGlobalHeader();
            updateArcadeHubUI();
        }

        window.transferGamePoints = function(amount) {
            const num = Math.floor(Number(amount));
            if (!isNaN(num) && num > 0) {
                gameState.tokens = (Number(gameState.tokens) || 0) + num;
                saveState();
                updateGlobalHeader();
                showToast(`🎉 +${num.toLocaleString()} 🪙 Tokens added to Main Balance!`, 'success');
            }
        };

        window.addTokens = window.transferGamePoints;

        window.sendGameScore = function(score, isWin) {
            let total = Number(score) || 0;
            if (isWin) total += 100;
            if (total > 0) window.transferGamePoints(total);
        };

        function manualSyncPrompt() {
            const val = prompt("Enter any points or win tokens earned in the mini game to transfer to your main balance:");
            if (val) {
                const num = Math.floor(Number(val));
                if (!isNaN(num) && num > 0) {
                    transferGamePoints(num);
                } else {
                    showToast("⚠ Please enter a valid positive number.", "error");
                }
            }
        }

        let lastTrackedScores = {};
        const keysToMonitor = [
            'external_game_tokens', 'earned_tokens_transfer', 'block_blast_score',
            'block_blast_tokens', 'block_blast_high_score', 'game_points_transfer',
            'spellbound_score', 'spellbound_gold', 'spellbound_tokens',
            'soccer_score', 'chess_score', 'mini_soccer_tokens', 'neon_chess_tokens',
            'ocean_explorer_coins'
        ];

        keysToMonitor.forEach(key => {
            const val = Number(localStorage.getItem(key));
            if (!isNaN(val) && val > 0) {
                lastTrackedScores[key] = val;
            }
        });

        setInterval(() => {
            keysToMonitor.forEach(key => {
                const rawVal = localStorage.getItem(key);
                if (rawVal !== null && rawVal !== undefined) {
                    const numVal = Number(rawVal);
                    if (!isNaN(numVal) && numVal > 0) {
                        const prevVal = lastTrackedScores[key] || 0;
                        if (numVal > prevVal) {
                            const delta = numVal - prevVal;
                            lastTrackedScores[key] = numVal;
                            transferGamePoints(delta);
                        }
                    }
                }
            });

            // Ocean Explorer Fishing Save Data Polling
            try {
                const fishSave = localStorage.getItem('ocean_explorer_neon_save');
                if (fishSave) {
                    const parsed = JSON.parse(fishSave);
                    if (parsed && typeof parsed.coins === 'number') {
                        const prev = lastTrackedScores['ocean_explorer_coins'] || 0;
                        if (parsed.coins > prev) {
                            const delta = parsed.coins - prev;
                            lastTrackedScores['ocean_explorer_coins'] = parsed.coins;
                            transferGamePoints(delta);
                        }
                    }
                }
            } catch(e) {}

            ['soccer_win', 'chess_win', 'game_win', 'mini_game_victory'].forEach(winKey => {
                if (localStorage.getItem(winKey) === 'true') {
                    localStorage.removeItem(winKey);
                    transferGamePoints(100);
                }
            });
        }, 500);

        window.addEventListener("message", (event) => {
            if (!event.data) return;
            let data = event.data;

            if (typeof data === "string") {
                try { data = JSON.parse(data); } catch(e) { return; }
            }

            if (typeof data === "object" && data !== null) {
                if (data.type === "GAME_TOKENS" || data.action === "addTokens" || data.blooketReward) {
                    const amount = Number(data.tokens || data.points || data.amount || 0);
                    if (!isNaN(amount) && amount > 0) {
                        window.transferGamePoints(amount);
                    }
                }
            }
        });

        // NATIVE BLOCK BLAST ENGINE
        const BB_BOARD_SIZE = 8;
        let bbBoardState = Array(BB_BOARD_SIZE).fill(null).map(() => Array(BB_BOARD_SIZE).fill(null));
        let bbScore = 0;
        let bbActiveDockPieces = [null, null, null];

        const BB_COLOR_PALETTE = [
            '#ec4899', '#3b82f6', '#10b981', '#f59e0b', '#8b5cf6', '#06b6d4', '#f43f5e'
        ];

        const BB_SHAPES = [
            [[1]],
            [[1, 1]],
            [[1], [1]],
            [[1, 1, 1]],
            [[1], [1], [1]],
            [[1, 1], [1, 1]],
            [[1, 1, 1], [0, 1, 0]],
            [[1, 0], [1, 0], [1, 1]],
            [[1, 1, 1], [1, 0, 0]],
            [[1, 1, 1, 1]]
        ];

        let bbDragTarget = null;
        let bbDragSlotIndex = -1;
        let bbDragOffset = { x: 0, y: 0 };
        let bbEventsBound = false;

        function initBlockBlast() {
            bbBoardState = Array(BB_BOARD_SIZE).fill(null).map(() => Array(BB_BOARD_SIZE).fill(null));
            bbScore = 0;
            bbActiveDockPieces = [null, null, null];
            document.getElementById('bb-score-text').innerText = '0';

            initBBBoard();
            spawnBBDockPieces();

            if (!bbEventsBound) {
                window.addEventListener('mousemove', moveBBDrag);
                window.addEventListener('touchmove', moveBBDrag, { passive: false });
                window.addEventListener('mouseup', endBBDrag);
                window.addEventListener('touchend', endBBDrag);
                bbEventsBound = true;
            }
        }

        function initBBBoard() {
            const boardEl = document.getElementById('bb-game-board');
            if (!boardEl) return;
            boardEl.innerHTML = '';
            for (let r = 0; r < BB_BOARD_SIZE; r++) {
                for (let c = 0; c < BB_BOARD_SIZE; c++) {
                    const cell = document.createElement('div');
                    cell.className = 'bb-cell';
                    cell.dataset.row = r;
                    cell.dataset.col = c;
                    boardEl.appendChild(cell);
                }
            }
            renderBBBoard();
        }

        function renderBBBoard() {
            const boardEl = document.getElementById('bb-game-board');
            if (!boardEl) return;
            for (let r = 0; r < BB_BOARD_SIZE; r++) {
                for (let c = 0; c < BB_BOARD_SIZE; c++) {
                    const idx = r * BB_BOARD_SIZE + c;
                    const cell = boardEl.children[idx];
                    if (!cell) continue;
                    cell.style.opacity = '1';
                    const color = bbBoardState[r][c];
                    if (color) {
                        cell.style.backgroundColor = color;
                        cell.classList.add('filled');
                        cell.classList.remove('preview');
                    } else {
                        cell.style.backgroundColor = '#1e293b';
                        cell.classList.remove('filled', 'preview');
                    }
                }
            }
        }

        function clearBBPreview() {
            const boardEl = document.getElementById('bb-game-board');
            if (!boardEl) return;
            for (let r = 0; r < BB_BOARD_SIZE; r++) {
                for (let c = 0; c < BB_BOARD_SIZE; c++) {
                    const idx = r * BB_BOARD_SIZE + c;
                    const cell = boardEl.children[idx];
                    if (!cell) continue;
                    cell.style.opacity = '1';
                    const color = bbBoardState[r][c];
                    if (color) {
                        cell.style.backgroundColor = color;
                        cell.classList.add('filled');
                        cell.classList.remove('preview');
                    } else {
                        cell.style.backgroundColor = '#1e293b';
                        cell.classList.remove('filled', 'preview');
                    }
                }
            }
        }

        function spawnBBDockPieces() {
            for (let i = 0; i < 3; i++) {
                if (!bbActiveDockPieces[i]) {
                    const shape = BB_SHAPES[Math.floor(Math.random() * BB_SHAPES.length)];
                    const color = BB_COLOR_PALETTE[Math.floor(Math.random() * BB_COLOR_PALETTE.length)];
                    bbActiveDockPieces[i] = { shape, color, id: Date.now() + '_' + i };
                }
            }
            renderBBDock();
            checkBBGameOver();
        }

        function renderBBDock() {
            const slots = document.querySelectorAll('.bb-dock-slot');
            slots.forEach((slot, index) => {
                slot.innerHTML = '';
                const pieceData = bbActiveDockPieces[index];
                if (!pieceData) return;

                const pieceEl = document.createElement('div');
                pieceEl.className = 'bb-draggable-piece';
                pieceEl.style.gridTemplateColumns = `repeat(${pieceData.shape[0].length}, 20px)`;

                pieceData.shape.forEach(row => {
                    row.forEach(val => {
                        const block = document.createElement('div');
                        block.className = 'bb-piece-block';
                        if (val) {
                            block.style.backgroundColor = pieceData.color;
                        } else {
                            block.style.opacity = '0';
                        }
                        pieceEl.appendChild(block);
                    });
                });

                attachBBDragEvents(pieceEl, index);
                slot.appendChild(pieceEl);
            });
        }

        function attachBBDragEvents(el, slotIndex) {
            const startDrag = (e) => {
                if (currentGameMode !== 'block-blast') return;
                const pointer = e.touches ? e.touches[0] : e;
                bbDragTarget = el;
                bbDragSlotIndex = slotIndex;

                const rect = el.getBoundingClientRect();
                bbDragOffset.x = pointer.clientX - rect.left;
                bbDragOffset.y = pointer.clientY - rect.top;

                el.classList.add('bb-dragging');
                document.body.appendChild(el);
                moveBBDrag(e);
            };

            el.addEventListener('mousedown', startDrag);
            el.addEventListener('touchstart', startDrag, { passive: false });
        }

        function moveBBDrag(e) {
            if (!bbDragTarget || currentGameMode !== 'block-blast') return;
            if (e.cancelable) e.preventDefault();
            const pointer = e.touches ? e.touches[0] : e;

            bbDragTarget.style.left = `${pointer.clientX - bbDragOffset.x}px`;
            bbDragTarget.style.top = `${pointer.clientY - bbDragOffset.y - 50}px`;

            clearBBPreview();

            const boardEl = document.getElementById('bb-game-board');
            const pieceData = bbActiveDockPieces[bbDragSlotIndex];
            if (boardEl && pieceData) {
                const boardRect = boardEl.getBoundingClientRect();
                const cellSize = boardRect.width / BB_BOARD_SIZE;

                const relativeX = pointer.clientX - boardRect.left;
                const relativeY = (pointer.clientY - 50) - boardRect.top;

                const targetCol = Math.floor(relativeX / cellSize);
                const targetRow = Math.floor(relativeY / cellSize);

                if (canPlaceBBShape(pieceData.shape, targetRow, targetCol)) {
                    for (let r = 0; r < pieceData.shape.length; r++) {
                        for (let c = 0; c < pieceData.shape[r].length; c++) {
                            if (pieceData.shape[r][c]) {
                                const br = targetRow + r;
                                const bc = targetCol + c;
                                const idx = br * BB_BOARD_SIZE + bc;
                                const cell = boardEl.children[idx];
                                if (cell) {
                                    cell.classList.add('preview');
                                    cell.style.backgroundColor = pieceData.color;
                                    cell.style.opacity = '0.55';
                                }
                            }
                        }
                    }
                }
            }
        }

        function endBBDrag(e) {
            if (!bbDragTarget || currentGameMode !== 'block-blast') return;
            const pointer = e.changedTouches ? e.changedTouches[0] : e;
            const pieceData = bbActiveDockPieces[bbDragSlotIndex];
            const boardEl = document.getElementById('bb-game-board');

            clearBBPreview();

            if (!boardEl || !pieceData) {
                if (bbDragTarget) {
                    bbDragTarget.classList.remove('bb-dragging');
                    bbDragTarget = null;
                }
                return;
            }

            const boardRect = boardEl.getBoundingClientRect();
            const cellSize = boardRect.width / BB_BOARD_SIZE;

            const relativeX = pointer.clientX - boardRect.left;
            const relativeY = (pointer.clientY - 50) - boardRect.top;

            const targetCol = Math.floor(relativeX / cellSize);
            const targetRow = Math.floor(relativeY / cellSize);

            if (canPlaceBBShape(pieceData.shape, targetRow, targetCol)) {
                placeBBShape(pieceData.shape, pieceData.color, targetRow, targetCol);
                bbActiveDockPieces[bbDragSlotIndex] = null;
                bbDragTarget.remove();
                bbDragTarget = null;

                clearBBLines();
                renderBBBoard();

                if (bbActiveDockPieces.every(p => p === null)) {
                    spawnBBDockPieces();
                } else {
                    checkBBGameOver();
                }
            } else {
                const slot = document.querySelector(`.bb-dock-slot[data-slot="${bbDragSlotIndex}"]`);
                bbDragTarget.classList.remove('bb-dragging');
                bbDragTarget.style.left = 'auto';
                bbDragTarget.style.top = 'auto';
                if (slot) slot.appendChild(bbDragTarget);
                bbDragTarget = null;
            }
        }

        function canPlaceBBShape(shape, r, c) {
            for (let row = 0; row < shape.length; row++) {
                for (let col = 0; col < shape[row].length; col++) {
                    if (shape[row][col]) {
                        const boardR = r + row;
                        const boardC = c + col;
                        if (boardR < 0 || boardR >= BB_BOARD_SIZE || boardC < 0 || boardC >= BB_BOARD_SIZE) return false;
                        if (bbBoardState[boardR][boardC] !== null) return false;
                    }
                }
            }
            return true;
        }

        function placeBBShape(shape, color, r, c) {
            let placedCount = 0;
            for (let row = 0; row < shape.length; row++) {
                for (let col = 0; col < shape[row].length; col++) {
                    if (shape[row][col]) {
                        bbBoardState[r + row][c + col] = color;
                        placedCount++;
                    }
                }
            }
            addBBScore(placedCount * 10);
        }

        function clearBBLines() {
            let rowsToClear = [];
            let colsToClear = [];

            for (let r = 0; r < BB_BOARD_SIZE; r++) {
                if (bbBoardState[r].every(cell => cell !== null)) rowsToClear.push(r);
            }

            for (let c = 0; c < BB_BOARD_SIZE; c++) {
                let full = true;
                for (let r = 0; r < BB_BOARD_SIZE; r++) {
                    if (bbBoardState[r][c] === null) { full = false; break; }
                }
                if (full) colsToClear.push(c);
            }

            rowsToClear.forEach(r => {
                for (let c = 0; c < BB_BOARD_SIZE; c++) bbBoardState[r][c] = null;
            });

            colsToClear.forEach(c => {
                for (let r = 0; r < BB_BOARD_SIZE; r++) bbBoardState[r][c] = null;
            });

            const clearedCount = rowsToClear.length + colsToClear.length;
            if (clearedCount > 0) {
                addBBScore(clearedCount * 100);
            }
        }

        function addBBScore(points) {
            bbScore += points;
            document.getElementById('bb-score-text').innerText = bbScore.toLocaleString();
            transferGamePoints(points);
        }

        function checkBBGameOver() {
            let movesPossible = false;
            for (let i = 0; i < 3; i++) {
                const piece = bbActiveDockPieces[i];
                if (!piece) continue;

                for (let r = 0; r < BB_BOARD_SIZE; r++) {
                    for (let c = 0; c < BB_BOARD_SIZE; c++) {
                        if (canPlaceBBShape(piece.shape, r, c)) {
                            movesPossible = true;
                            break;
                        }
                    }
                    if (movesPossible) break;
                }
                if (movesPossible) break;
            }

            if (!movesPossible && bbActiveDockPieces.some(p => p !== null)) {
                setTimeout(() => {
                    showToast(`🎮 Game Over! Final Block Blast Score: ${bbScore.toLocaleString()}`, 'info');
                    initBlockBlast();
                }, 300);
            }
        }

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

        // DON'T LOOK DOWN
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
                const gained = currentMeters - peakHeight;
                peakHeight = currentMeters;
                dlCoins = peakHeight;
                gameState.tokens = (Number(gameState.tokens) || 0) + gained;
                updateGlobalHeader();
            }

            if (player.y > 600) {
                dlEnded = true;
                saveState();
                showToast(`🧗 Run Finished! Reached ${peakHeight}m (+${dlCoins.toLocaleString()} Tokens transferred!)`, 'success');
                returnToGameHub();
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

        // GOLD QUEST
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
                showToast(`🏆 Gold Quest Completed! Total Gold Won: ${gqGold.toLocaleString()} 🪙`, 'success');
                returnToGameHub();
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
            let gained = 0;
            if (choice.mult) {
                gained = gqGold > 0 ? gqGold : 500;
                gqGold = gqGold > 0 ? gqGold * choice.mult : 1000;
            } else {
                gained = choice.val;
                gqGold += choice.val;
            }

            transferGamePoints(gained);

            gqRound++;
            nextGQRound();
        }

        // CRYPTO HACK
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
                showToast(`🏆 Crypto Hack Completed! Total Crypto Stolen: ${chCrypto.toLocaleString()} ⚡`, 'success');
                returnToGameHub();
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
                    transferGamePoints(gained);
                    chRound++;
                    nextCHRound();
                };
                grid.appendChild(btn);
            }
            box.classList.remove("hidden");
        }

        // COOKIE CLICKER DELUXE
        const cc_GAME = {
            cookies: 0,
            totalCookiesBaked: 0,
            totalClicks: 0,
            handmadeCookies: 0,
            goldenCookiesClicked: 0,
            startTime: Date.now(),
            timePlayed: 0,
            bakeryName: "My Delicious Bakery",
            soundEnabled: true,
            volume: 0.3,
            previouslyAffordable: {},
            activeBuffs: [],
            buildings: [
                { id: 'cursor', name: 'Cursor', baseCost: 15, baseCps: 0.1, owned: 0, icon: 'fa-hand-pointer', desc: 'Autoclicks the big cookie periodically.' },
                { id: 'grandma', name: 'Grandma', baseCost: 100, baseCps: 1, owned: 0, icon: 'fa-person-mrs', desc: 'A nice grandma to bake cookies with love.' },
                { id: 'farm', name: 'Farm', baseCost: 1100, baseCps: 8, owned: 0, icon: 'fa-wheat-awn', desc: 'Grows cookie plants from cookie seeds.' },
                { id: 'mine', name: 'Mine', baseCost: 12000, baseCps: 47, owned: 0, icon: 'fa-pickaxe', desc: 'Mines out cookie dough and chocolate chips.' },
                { id: 'factory', name: 'Factory', baseCost: 130000, baseCps: 260, owned: 0, icon: 'fa-industry', desc: 'Produces massive batches of cookies.' },
                { id: 'bank', name: 'Bank', baseCost: 1400000, baseCps: 1400, owned: 0, icon: 'fa-landmark', desc: 'Generates cookies from interest & investments.' },
                { id: 'temple', name: 'Temple', baseCost: 20000000, baseCps: 7800, owned: 0, icon: 'fa-gopuram', desc: 'Prays to the ancient Cookie Gods.' },
                { id: 'wizard', name: 'Wizard Tower', baseCost: 330000000, baseCps: 44000, owned: 0, icon: 'fa-hat-wizard', desc: 'Summons cookies using ancient magic.' },
                { id: 'alchemy', name: 'Alchemy Lab', baseCost: 5100000000, baseCps: 260000, owned: 0, icon: 'fa-flask', desc: 'Transmutes gold directly into chocolate.' },
                { id: 'portal', name: 'Portal', baseCost: 75000000000, baseCps: 1600000, owned: 0, icon: 'fa-door-open', desc: 'Opens portals to the infinite Cookieverse.' },
                { id: 'timeMachine', name: 'Time Machine', baseCost: 1000000000000, baseCps: 10000000, owned: 0, icon: 'fa-clock-rotate-left', desc: 'Brings back delicious cookies from the past.' },
                { id: 'antimatter', name: 'Antimatter Condenser', baseCost: 14000000000000, baseCps: 65000000, owned: 0, icon: 'fa-atom', desc: 'Condenses raw antimatter into sweet cookies.' },
                { id: 'prism', name: 'Prism', baseCost: 170000000000000, baseCps: 430000000, owned: 0, icon: 'fa-gem', desc: 'Converts ambient light rays directly into cookies.' },
                { id: 'chancemaker', name: 'Chancemaker', baseCost: 2100000000000000, baseCps: 2900000000, owned: 0, icon: 'fa-dice', desc: 'Generates cookies out of thin air through luck.' },
                { id: 'fractal', name: 'Fractal Engine', baseCost: 31000000000000000, baseCps: 21000000000, owned: 0, icon: 'fa-diagram-project', desc: 'Turns cookies into smaller cookies that produce cookies.' },
                { id: 'jsConsole', name: 'Javascript Console', baseCost: 500000000000000000, baseCps: 150000000000, owned: 0, icon: 'fa-code', desc: 'Creates cookies by editing the reality code.' },
                { id: 'idleverse', name: 'Idleverse', baseCost: 8200000000000000000, baseCps: 1100000000000, owned: 0, icon: 'fa-vr-cardboard', desc: 'Harvests cookies from alternate idle universes.' },
                { id: 'cortexBaker', name: 'Cortex Baker', baseCost: 130000000000000000000, baseCps: 8300000000003, owned: 0, icon: 'fa-brain', desc: 'Envisioning cookies into physical materialization.' },
                { id: 'you', name: 'You', baseCost: 2100000000000000000000, baseCps: 64000000000004, owned: 0, icon: 'fa-user-ninja', desc: 'Baking cookies yourself with pure willpower.' }
            ],
            upgrades: [
                { id: 'click_1', name: 'Plastic Mouse', cost: 50, reqBuilding: 'cursor', reqOwned: 1, addCpc: 1, bought: false, desc: 'Adds +1 Cookie Per Click.' },
                { id: 'click_2', name: 'Iron Mouse', cost: 500, reqBuilding: 'cursor', reqOwned: 5, addCpc: 5, bought: false, desc: 'Adds +5 Cookies Per Click.' },
                { id: 'click_3', name: 'Titanium Mouse', cost: 5000, reqBuilding: 'cursor', reqOwned: 10, addCpc: 25, bought: false, desc: 'Adds +25 Cookies Per Click.' },
                { id: 'click_4', name: 'Adamantite Cursor', cost: 50000, reqBuilding: 'cursor', reqOwned: 15, addCpc: 150, bought: false, desc: 'Adds +150 Cookies Per Click.' },
                { id: 'click_5', name: 'Cybernetic Finger', cost: 500000, reqBuilding: 'cursor', reqOwned: 20, addCpc: 1200, bought: false, desc: 'Adds +1,200 Cookies Per Click.' },
                { id: 'click_6', name: 'Particle Clicker', cost: 5000000, reqBuilding: 'cursor', reqOwned: 25, addCpc: 10000, bought: false, desc: 'Adds +10,000 Cookies Per Click.' },
                { id: 'syn_1', name: 'Thousand Fingers', cost: 100000, reqBuilding: 'cursor', reqOwned: 25, cpsPercentClick: 0.001, bought: false, desc: 'Each click gains +0.1% of total CPS.' },
                { id: 'syn_2', name: 'Million Fingers', cost: 10000000, reqBuilding: 'cursor', reqOwned: 50, cpsPercentClick: 0.005, bought: false, desc: 'Each click gains +0.5% of total CPS.' },
                { id: 'syn_3', name: 'Billion Fingers', cost: 1000000000, reqBuilding: 'cursor', reqOwned: 75, cpsPercentClick: 0.01, bought: false, desc: 'Each click gains +1% of total CPS.' },
                { id: 'syn_4', name: 'Trillion Fingers', cost: 100000000000, reqBuilding: 'cursor', reqOwned: 100, cpsPercentClick: 0.02, bought: false, desc: 'Each click gains +2% of total CPS.' },
                { id: 'syn_5', name: 'Quadrillion Fingers', cost: 10000000000000, reqBuilding: 'cursor', reqOwned: 125, cpsPercentClick: 0.03, bought: false, desc: 'Each click gains +3% of total CPS.' },
                { id: 'syn_6', name: 'Quintillion Fingers', cost: 1000000000000000, reqBuilding: 'cursor', reqOwned: 150, cpsPercentClick: 0.05, bought: false, desc: 'Each click gains +5% of total CPS!' },
                { id: 'click_7', name: 'Dimensional Pointer', cost: 50000000, reqBuilding: 'cursor', reqOwned: 30, addCpc: 80000, bought: false, desc: 'Adds +80,000 Cookies Per Click.' },
                { id: 'click_8', name: 'Reality Tap', cost: 500000000, reqBuilding: 'cursor', reqOwned: 35, addCpc: 500000, bought: false, desc: 'Adds +500,000 Cookies Per Click.' },
                { id: 'click_9', name: 'Cosmic Touch', cost: 5000000000, reqBuilding: 'cursor', reqOwned: 40, addCpc: 3500000, bought: false, desc: 'Adds +3.5M Cookies Per Click.' },
                { id: 'click_10', name: 'Singularity Click', cost: 50000000000, reqBuilding: 'cursor', reqOwned: 50, addCpc: 25000000, bought: false, desc: 'Adds +25M Cookies Per Click.' },
                { id: 'click_11', name: 'Multiverse Thumb', cost: 500000000000, reqBuilding: 'cursor', reqOwned: 60, addCpc: 200000000, bought: false, desc: 'Adds +200M Cookies Per Click.' },
                { id: 'click_12', name: 'Omni Touch', cost: 5000000000000, reqBuilding: 'cursor', reqOwned: 70, addCpc: 1500000000, bought: false, desc: 'Adds +1.5B Cookies Per Click.' },
                { id: 'click_13', name: 'Divine Pointer', cost: 50000000000000, reqBuilding: 'cursor', reqOwned: 80, addCpc: 12000000000, bought: false, desc: 'Adds +12B Cookies Per Click.' },
                { id: 'click_14', name: 'Infinite Click', cost: 500000000000000, reqBuilding: 'cursor', reqOwned: 100, addCpc: 100000000000, bought: false, desc: 'Adds +100B Cookies Per Click!' },
                { id: 'gma_1', name: 'Forwards from Grandma', cost: 300, reqBuilding: 'grandma', reqOwned: 1, bought: false, desc: 'Grandmas are 2x as efficient.' },
                { id: 'gma_2', name: 'Steel-plated Rolling Pins', cost: 1500, reqBuilding: 'grandma', reqOwned: 10, bought: false, desc: 'Grandmas are 2x as efficient.' },
                { id: 'farm_1', name: 'Cheap Hoes', cost: 1200, reqBuilding: 'farm', reqOwned: 1, bought: false, desc: 'Farms are 2x as efficient.' },
                { id: 'farm_2', name: 'Fertilizer Boost', cost: 6000, reqBuilding: 'farm', reqOwned: 10, bought: false, desc: 'Farms are 2x as efficient.' },
                { id: 'mine_1', name: 'Sugar Gas', cost: 5000, reqBuilding: 'mine', reqOwned: 1, bought: false, desc: 'Mines are 2x as efficient.' },
                { id: 'fac_1', name: 'Sturdier Assembly Lines', cost: 25000, reqBuilding: 'factory', reqOwned: 1, bought: false, desc: 'Factories are 2x as efficient.' }
            ],
            achievements: [
                { id: 'ach_1', name: 'First Batch', desc: 'Bake 1 cookie.', req: (g) => g.totalCookiesBaked >= 1, unlocked: false, icon: 'fa-cookie' },
                { id: 'ach_2', name: 'Clicking Frenzy', desc: 'Click the Big Cookie 100 times.', req: (g) => g.totalClicks >= 100, unlocked: false, icon: 'fa-arrow-pointer' },
                { id: 'ach_3', name: 'Bakery Empire', desc: 'Bake 100,000 cookies.', req: (g) => g.totalCookiesBaked >= 100000, unlocked: false, icon: 'fa-crown' },
                { id: 'ach_4', name: 'Golden Touch', desc: 'Click 5 Golden Cookies.', req: (g) => g.goldenCookiesClicked >= 5, unlocked: false, icon: 'fa-wand-magic-sparkles' },
                { id: 'ach_5', name: 'Grandma Army', desc: 'Own 25 Grandmas.', req: (g) => (g.buildings.find(b=>b.id==='grandma')?.owned || 0) >= 25, unlocked: false, icon: 'fa-users' },
                { id: 'ach_6', name: 'Cookie Millionaire', desc: 'Bake 1,000,000 cookies.', req: (g) => g.totalCookiesBaked >= 1000000, unlocked: false, icon: 'fa-gem' }
            ]
        };

        function cc_getBuildingCost(b) {
            return Math.floor(b.baseCost * Math.pow(1.15, b.owned));
        }

        function cc_getTotalCps() {
            let baseCps = 0;
            cc_GAME.buildings.forEach(b => {
                let buildingCps = b.baseCps * b.owned;
                if (b.id === 'grandma') {
                    if (cc_GAME.upgrades.find(u => u.id === 'gma_1')?.bought) buildingCps *= 2;
                    if (cc_GAME.upgrades.find(u => u.id === 'gma_2')?.bought) buildingCps *= 2;
                } else if (b.id === 'farm') {
                    if (cc_GAME.upgrades.find(u => u.id === 'farm_1')?.bought) buildingCps *= 2;
                    if (cc_GAME.upgrades.find(u => u.id === 'farm_2')?.bought) buildingCps *= 2;
                } else if (b.id === 'mine') {
                    if (cc_GAME.upgrades.find(u => u.id === 'mine_1')?.bought) buildingCps *= 2;
                } else if (b.id === 'factory') {
                    if (cc_GAME.upgrades.find(u => u.id === 'fac_1')?.bought) buildingCps *= 2;
                }
                baseCps += buildingCps;
            });
            cc_GAME.activeBuffs.forEach(buff => {
                if (buff.type === 'frenzy') baseCps *= buff.multiplier;
            });
            return baseCps;
        }

        function cc_getCpc() {
            let baseCpc = 1;
            cc_GAME.upgrades.forEach(u => {
                if (u.bought && u.addCpc) baseCpc += u.addCpc;
            });
            const totalCps = cc_getTotalCps();
            cc_GAME.upgrades.forEach(u => {
                if (u.bought && u.cpsPercentClick) baseCpc += totalCps * u.cpsPercentClick;
            });
            cc_GAME.activeBuffs.forEach(buff => {
                if (buff.type === 'clickFrenzy') baseCpc *= buff.multiplier;
            });
            return Math.max(1, Math.floor(baseCpc));
        }

        const cc_canvas = document.getElementById('particleCanvas');
        const cc_ctx = cc_canvas ? cc_canvas.getContext('2d') : null;
        let cc_particles = [];

        function cc_resizeCanvas() {
            if (cc_canvas) {
                cc_canvas.width = window.innerWidth;
                cc_canvas.height = window.innerHeight;
            }
        }
        window.addEventListener('resize', cc_resizeCanvas);
        cc_resizeCanvas();

        function cc_triggerFirework(x, y, color = '#f59e0b') {
            if (!cc_ctx) return;
            for (let i = 0; i < 12; i++) {
                const angle = Math.random() * Math.PI * 2;
                const speed = 2 + Math.random() * 3;
                cc_particles.push({
                    x: x, y: y,
                    vx: Math.cos(angle) * speed,
                    vy: Math.sin(angle) * speed,
                    radius: 2 + Math.random() * 2,
                    color: color, alpha: 1,
                    decay: 0.03 + Math.random() * 0.02
                });
            }
        }

        function cc_createCookieCrumbs(x, y) {
            if (!cc_ctx) return;
            for (let i = 0; i < 5; i++) {
                const angle = Math.random() * Math.PI * 2;
                const speed = 1 + Math.random() * 2;
                cc_particles.push({
                    x: x, y: y,
                    vx: Math.cos(angle) * speed,
                    vy: Math.sin(angle) * speed + 1,
                    radius: 1.5 + Math.random() * 1.5,
                    color: Math.random() > 0.5 ? '#8C5027' : '#D98A4B',
                    alpha: 1,
                    decay: 0.04 + Math.random() * 0.03
                });
            }
        }

        function cc_updateParticles() {
            if (!cc_ctx || cc_particles.length === 0) {
                requestAnimationFrame(cc_updateParticles);
                return;
            }
            cc_ctx.clearRect(0, 0, cc_canvas.width, cc_canvas.height);
            for (let i = cc_particles.length - 1; i >= 0; i--) {
                const p = cc_particles[i];
                p.x += p.vx;
                p.y += p.vy;
                p.alpha -= p.decay;
                if (p.alpha <= 0) {
                    cc_particles.splice(i, 1);
                    continue;
                }
                cc_ctx.save();
                cc_ctx.globalAlpha = p.alpha;
                cc_ctx.fillStyle = p.color;
                cc_ctx.beginPath();
                cc_ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2);
                cc_ctx.fill();
                cc_ctx.restore();
            }
            requestAnimationFrame(cc_updateParticles);
        }
        requestAnimationFrame(cc_updateParticles);

        function cc_formatNum(num) {
            if (num >= 1e21) return (num / 1e21).toFixed(2) + 'Sx';
            if (num >= 1e18) return (num / 1e18).toFixed(2) + 'Qi';
            if (num >= 1e15) return (num / 1e15).toFixed(2) + 'Qa';
            if (num >= 1e12) return (num / 1e12).toFixed(2) + 'T';
            if (num >= 1e9) return (num / 1e9).toFixed(2) + 'B';
            if (num >= 1e6) return (num / 1e6).toFixed(2) + 'M';
            if (num >= 1e3) return (num / 1e3).toFixed(1) + 'k';
            return Math.floor(num).toLocaleString();
        }

        function cc_initBuildingsDOM() {
            const container = document.getElementById('buildingsList');
            if (!container) return;
            container.innerHTML = '';
            cc_GAME.buildings.forEach(b => {
                const card = document.createElement('div');
                card.id = 'building_' + b.id;
                card.className = `buy-card p-3.5 rounded-xl border transition-all flex items-center justify-between cursor-pointer bg-bakery-900/60 border-bakery-700/50 opacity-60`;
                
                const triggerBuy = (e) => {
                    if (e) { e.preventDefault(); e.stopPropagation(); }
                    cc_buyBuilding(b.id);
                };
                card.addEventListener('pointerdown', triggerBuy);
                card.innerHTML = `
                    <div class="flex items-center gap-3 pointer-events-none">
                        <div id="b_icon_${b.id}" class="w-12 h-12 rounded-xl bg-bakery-700/40 text-amber-400/40 flex items-center justify-center text-xl shadow-inner">
                            <i class="fa-solid ${b.icon}"></i>
                        </div>
                        <div>
                            <div class="font-bold text-amber-200 text-sm sm:text-base flex items-center gap-2">
                                ${b.name}
                                <span id="b_ready_${b.id}" class="hidden text-[10px] bg-amber-400/20 text-amber-300 px-1.5 py-0.5 rounded font-semibold border border-amber-400/40 animate-pulse">BUY READY!</span>
                            </div>
                            <div class="text-xs text-amber-400/70">${b.desc}</div>
                        </div>
                    </div>
                    <div class="text-right pl-2 pointer-events-none">
                        <div id="b_cost_${b.id}" class="text-xs font-bold text-amber-500/60 flex items-center justify-end gap-1">
                            <i class="fa-solid fa-cookie text-[10px]"></i> <span>0</span>
                        </div>
                        <div class="text-[11px] text-amber-400/60 mt-0.5">Owned: <span id="b_owned_${b.id}" class="font-bold text-amber-200">0</span></div>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        function cc_initUpgradesDOM() {
            const container = document.getElementById('upgradesList');
            if (!container) return;
            container.innerHTML = '';
            cc_GAME.upgrades.forEach(u => {
                const card = document.createElement('div');
                card.id = 'upgrade_' + u.id;
                card.className = `buy-card hidden p-3 rounded-xl border transition-all flex flex-col justify-between cursor-pointer bg-bakery-900/60 border-bakery-700/50 opacity-60`;
                
                const triggerBuy = (e) => {
                    if (e) { e.preventDefault(); e.stopPropagation(); }
                    cc_buyUpgrade(u.id);
                };
                card.addEventListener('pointerdown', triggerBuy);
                card.innerHTML = `
                    <div class="pointer-events-none">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-bold text-sm text-amber-200 flex items-center gap-1.5">
                                <i class="fa-solid fa-bolt text-amber-400 text-xs"></i> ${u.name}
                            </span>
                            <span id="u_ready_${u.id}" class="hidden text-[9px] bg-amber-400/20 text-amber-300 px-1 py-0.5 rounded font-bold border border-amber-400/40">READY!</span>
                        </div>
                        <p class="text-xs text-amber-400/70 mb-2">${u.desc}</p>
                    </div>
                    <div id="u_cost_${u.id}" class="pointer-events-none text-right text-xs font-bold text-amber-500/60 pt-2 border-t border-bakery-700/50 flex items-center justify-end gap-1">
                        <i class="fa-solid fa-cookie text-[10px]"></i> <span>${cc_formatNum(u.cost)}</span>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        function cc_updateBuildingsUI() {
            cc_GAME.buildings.forEach(b => {
                const card = document.getElementById('building_' + b.id);
                if (!card) return;
                const cost = cc_getBuildingCost(b);
                const canAfford = cc_GAME.cookies >= cost;
                const elementKey = 'building_' + b.id;

                if (canAfford && !cc_GAME.previouslyAffordable[elementKey]) {
                    cc_GAME.previouslyAffordable[elementKey] = true;
                    const rect = card.getBoundingClientRect();
                    cc_triggerFirework(rect.left + rect.width * 0.5, rect.top + rect.height * 0.5, '#f59e0b');
                } else if (!canAfford) {
                    cc_GAME.previouslyAffordable[elementKey] = false;
                }

                if (canAfford) {
                    card.className = "buy-card p-3.5 rounded-xl border transition-all flex items-center justify-between cursor-pointer bg-bakery-800 border-amber-400/80 hover:bg-bakery-700 animate-affordable-glow";
                } else {
                    card.className = "buy-card p-3.5 rounded-xl border transition-all flex items-center justify-between cursor-pointer bg-bakery-900/60 border-bakery-700/50 opacity-60";
                }

                const iconBox = document.getElementById(`b_icon_${b.id}`);
                if (iconBox) {
                    iconBox.className = `w-12 h-12 rounded-xl ${canAfford ? 'bg-amber-500/20 text-amber-300' : 'bg-bakery-700/40 text-amber-400/40'} flex items-center justify-center text-xl shadow-inner`;
                }

                const readyTag = document.getElementById(`b_ready_${b.id}`);
                if (readyTag) {
                    if (canAfford) readyTag.classList.remove('hidden');
                    else readyTag.classList.add('hidden');
                }

                const costVal = document.querySelector(`#b_cost_${b.id} span`);
                if (costVal) costVal.innerText = cc_formatNum(cost);

                const costBox = document.getElementById(`b_cost_${b.id}`);
                if (costBox) {
                    costBox.className = `text-xs font-bold ${canAfford ? 'text-amber-300' : 'text-amber-500/60'} flex items-center justify-end gap-1`;
                }

                const ownedVal = document.getElementById(`b_owned_${b.id}`);
                if (ownedVal) ownedVal.innerText = b.owned;
            });
        }

        function cc_updateUpgradesUI() {
            const noMsg = document.getElementById('noUpgradesMessage');
            let availableCount = 0;
            cc_GAME.upgrades.forEach(u => {
                const card = document.getElementById('upgrade_' + u.id);
                if (!card) return;
                if (u.bought) {
                    card.classList.add('hidden');
                    return;
                }

                const reqB = cc_GAME.buildings.find(b => b.id === u.reqBuilding);
                if (reqB && reqB.owned < u.reqOwned) {
                    card.classList.add('hidden');
                    return;
                }

                card.classList.remove('hidden');
                availableCount++;
                const canAfford = cc_GAME.cookies >= u.cost;
                const elementKey = 'upgrade_' + u.id;

                if (canAfford && !cc_GAME.previouslyAffordable[elementKey]) {
                    cc_GAME.previouslyAffordable[elementKey] = true;
                    const rect = card.getBoundingClientRect();
                    cc_triggerFirework(rect.left + rect.width * 0.5, rect.top + rect.height * 0.5, '#fbbf24');
                } else if (!canAfford) {
                    cc_GAME.previouslyAffordable[elementKey] = false;
                }

                if (canAfford) {
                    card.className = "buy-card p-3 rounded-xl border transition-all flex flex-col justify-between cursor-pointer bg-bakery-800 border-amber-400/80 hover:bg-bakery-700 animate-affordable-glow";
                } else {
                    card.className = "buy-card p-3 rounded-xl border transition-all flex flex-col justify-between cursor-pointer bg-bakery-900/60 border-bakery-700/50 opacity-60";
                }

                const readyTag = document.getElementById(`u_ready_${u.id}`);
                if (readyTag) {
                    if (canAfford) readyTag.classList.remove('hidden');
                    else readyTag.classList.add('hidden');
                }

                const costBox = document.getElementById(`u_cost_${u.id}`);
                if (costBox) {
                    costBox.className = `pointer-events-none text-right text-xs font-bold ${canAfford ? 'text-amber-300' : 'text-amber-500/60'} pt-2 border-t border-bakery-700/50 flex items-center justify-end gap-1`;
                }
            });

            const badge = document.getElementById('upgradeBadge');
            if (badge) {
                if (availableCount > 0) {
                    badge.innerText = availableCount;
                    badge.classList.remove('hidden');
                    if (noMsg) noMsg.classList.add('hidden');
                } else {
                    badge.classList.add('hidden');
                    if (noMsg) noMsg.classList.remove('hidden');
                }
            }
        }

        function cc_renderAchievements() {
            const container = document.getElementById('achievementsList');
            if (!container) return;
            container.innerHTML = '';
            let unlockedCount = 0;
            cc_GAME.achievements.forEach(a => {
                if (a.unlocked) unlockedCount++;
                const card = document.createElement('div');
                card.className = `p-3 rounded-xl border flex items-center gap-3 ${
                    a.unlocked
                        ? 'bg-bakery-800/90 border-amber-400/50 text-amber-100'
                        : 'bg-bakery-900/40 border-bakery-700/30 text-amber-400/30'
                }`;
                card.innerHTML = `
                    <div class="w-10 h-10 rounded-lg ${a.unlocked ? 'bg-amber-400 text-bakery-900' : 'bg-bakery-800 text-amber-400/30'} flex items-center justify-center text-lg font-bold shrink-0">
                        <i class="fa-solid ${a.icon}"></i>
                    </div>
                    <div>
                        <div class="font-bold text-xs sm:text-sm ${a.unlocked ? 'text-amber-200' : 'text-amber-400/40'}">${a.name}</div>
                        <div class="text-[11px] ${a.unlocked ? 'text-amber-300/80' : 'text-amber-400/30'}">${a.desc}</div>
                    </div>
                `;
                container.appendChild(card);
            });

            const progText = document.getElementById('achievementProgressText');
            if (progText) progText.innerText = `${unlockedCount} / ${cc_GAME.achievements.length}`;

            const milkLevel = document.getElementById('milkLevel');
            if (milkLevel) {
                const milkPercent = Math.min(100, Math.max(15, (unlockedCount / cc_GAME.achievements.length) * 100));
                milkLevel.style.height = `${milkPercent}%`;
            }
        }

        function cc_updateStatsDisplay() {
            const bank = document.getElementById('statCookiesBank');
            if (bank) bank.innerText = cc_formatNum(cc_GAME.cookies);
            const baked = document.getElementById('statCookiesBaked');
            if (baked) baked.innerText = cc_formatNum(cc_GAME.totalCookiesBaked);
            const clicks = document.getElementById('statCookieClicks');
            if (clicks) clicks.innerText = cc_GAME.totalClicks.toLocaleString();
            const handmade = document.getElementById('statHandmade');
            if (handmade) handmade.innerText = cc_formatNum(cc_GAME.handmadeCookies);
            const golden = document.getElementById('statGoldenClicked');
            if (golden) golden.innerText = cc_GAME.goldenCookiesClicked.toLocaleString();

            const elapsedSecs = Math.floor((Date.now() - cc_GAME.startTime) / 1000) + cc_GAME.timePlayed;
            const timeEl = document.getElementById('statTimePlayed');
            if (timeEl) timeEl.innerText = elapsedSecs + 's';

            const total = cc_GAME.totalCookiesBaked || 1;
            const handmadePct = Math.min(100, Math.round((cc_GAME.handmadeCookies / total) * 100));
            const passivePct = 100 - handmadePct;

            const hmPctEl = document.getElementById('handmadePercent');
            if (hmPctEl) hmPctEl.innerText = handmadePct + '%';
            const hmBar = document.getElementById('handmadeBar');
            if (hmBar) hmBar.style.width = handmadePct + '%';

            const psPctEl = document.getElementById('passivePercent');
            if (psPctEl) psPctEl.innerText = passivePct + '%';
            const psBar = document.getElementById('passiveBar');
            if (psBar) psBar.style.width = passivePct + '%';
        }

        function cc_updateHeaderStats() {
            const count = document.getElementById('cookieCount');
            if (count) count.innerText = cc_formatNum(cc_GAME.cookies);
            const cps = document.getElementById('cpsDisplay');
            if (cps) cps.innerText = cc_formatNum(cc_getTotalCps());
            const cpc = document.getElementById('cpcDisplay');
            if (cpc) cpc.innerText = cc_formatNum(cc_getCpc());
        }

        function cc_buyBuilding(id) {
            const b = cc_GAME.buildings.find(item => item.id === id);
            if (!b) return;
            const cost = cc_getBuildingCost(b);
            if (cc_GAME.cookies >= cost) {
                cc_GAME.cookies -= cost;
                b.owned++;
                playSound('click');
                const cardElem = document.getElementById('building_' + id);
                if (cardElem) {
                    const rect = cardElem.getBoundingClientRect();
                    cc_triggerFirework(rect.left + rect.width * 0.5, rect.top + rect.height * 0.5, '#fbbf24');
                }
                cc_updateHeaderStats();
                cc_updateBuildingsUI();
                cc_updateUpgradesUI();
                cc_checkAchievements();
            }
        }

        function cc_buyUpgrade(id) {
            const u = cc_GAME.upgrades.find(item => item.id === id);
            if (!u || u.bought) return;
            if (cc_GAME.cookies >= u.cost) {
                cc_GAME.cookies -= u.cost;
                u.bought = true;
                playSound('click');
                const cardElem = document.getElementById('upgrade_' + id);
                if (cardElem) {
                    const rect = cardElem.getBoundingClientRect();
                    cc_triggerFirework(rect.left + rect.width * 0.5, rect.top + rect.height * 0.5, '#f59e0b');
                }
                showToast(`Unlocked: ${u.name}!`, 'success');
                cc_updateHeaderStats();
                cc_updateUpgradesUI();
                cc_updateBuildingsUI();
                cc_checkAchievements();
            }
        }

        function cc_clickBigCookie(e) {
            const gained = cc_getCpc();
            cc_GAME.cookies += gained;
            cc_GAME.totalCookiesBaked += gained;
            cc_GAME.handmadeCookies += gained;
            cc_GAME.totalClicks++;

            gameState.tokens = (Number(gameState.tokens) || 0) + gained;
            updateGlobalHeader();

            playSound('click');

            const btn = document.getElementById('bigCookie');
            if (btn) {
                btn.classList.add('cookie-active');
                setTimeout(() => btn.classList.remove('cookie-active'), 80);
                const rect = btn.getBoundingClientRect();
                const x = e ? e.clientX : rect.left + rect.width / 2;
                const y = e ? e.clientY : rect.top + rect.height / 2;
                cc_createCookieCrumbs(x, y);
                cc_spawnFloatingText(`+${cc_formatNum(gained)}`, x, y);
            }

            cc_updateHeaderStats();
            cc_updateBuildingsUI();
            cc_updateUpgradesUI();
            cc_checkAchievements();
        }

        function cc_spawnFloatingText(text, x, y) {
            const el = document.createElement('div');
            el.className = 'fixed font-fredoka font-extrabold text-amber-300 pointer-events-none z-50 animate-float-up drop-shadow-[0_2px_4px_rgba(0,0,0,0.8)] text-xl';
            el.innerText = text;
            el.style.left = `${x - 15}px`;
            el.style.top = `${y - 20}px`;
            document.body.appendChild(el);
            setTimeout(() => el.remove(), 950);
        }

        function cc_spawnGoldenCookie() {
            if (currentGameMode !== 'cookie-clicker') return;
            const container = document.getElementById('goldenCookieContainer');
            if (!container) return;
            container.innerHTML = '';
            const gc = document.createElement('button');
            gc.className = 'w-16 h-16 rounded-full bg-gradient-to-tr from-amber-500 via-amber-300 to-yellow-100 p-2 shadow-2xl cursor-pointer animate-golden-glow border-2 border-amber-200 hover:scale-110 transition-transform fixed z-50';
            
            const x = 10 + Math.random() * (window.innerWidth - 100);
            const y = 10 + Math.random() * (window.innerHeight - 100);
            gc.style.left = `${x}px`;
            gc.style.top = `${y}px`;
            gc.innerHTML = `
                <svg class="w-full h-full text-amber-900 drop-shadow pointer-events-none" viewBox="0 0 100 100" fill="currentColor">
                    <circle cx="50" cy="50" r="45" fill="#facc15"/>
                    <circle cx="35" cy="35" r="6" fill="#78350f"/>
                    <circle cx="65" cy="40" r="7" fill="#78350f"/>
                    <circle cx="50" cy="70" r="6" fill="#78350f"/>
                </svg>
            `;
            gc.onclick = () => {
                cc_GAME.goldenCookiesClicked++;
                playSound('pullRare');
                cc_triggerFirework(x + 30, y + 30, '#fef08a');
                if (Math.random() > 0.5) {
                    cc_addBuff('frenzy', 'Frenzy! (7x CPS)', 15, 7);
                    showToast('GOLDEN COOKIE! 7x Production Frenzy for 15s!', 'success');
                } else {
                    cc_addBuff('clickFrenzy', 'Click Frenzy! (777x CPC)', 10, 777);
                    showToast('GOLDEN COOKIE! 777x Click Power for 10s!', 'success');
                }
                gc.remove();
                cc_updateHeaderStats();
                cc_checkAchievements();
            };
            container.appendChild(gc);
            setTimeout(() => { if (gc.parentNode) gc.remove(); }, 12000);
        }

        function cc_addBuff(type, name, duration, multiplier) {
            cc_GAME.activeBuffs = cc_GAME.activeBuffs.filter(b => b.type !== type);
            cc_GAME.activeBuffs.push({
                type: type,
                name: name,
                multiplier: multiplier,
                expiresAt: Date.now() + (duration * 1000)
            });
            cc_renderBuffs();
        }

        function cc_renderBuffs() {
            const container = document.getElementById('buffContainer');
            if (!container) return;
            container.innerHTML = '';
            cc_GAME.activeBuffs.forEach(buff => {
                const remaining = Math.max(0, Math.ceil((buff.expiresAt - Date.now()) / 1000));
                const badge = document.createElement('div');
                badge.className = 'px-3 py-1 bg-gradient-to-r from-amber-500 to-yellow-400 text-slate-950 text-xs font-black rounded-full shadow-lg flex items-center gap-1.5 animate-pulse';
                badge.innerHTML = `<i class="fa-solid fa-bolt"></i> ${buff.name} (${remaining}s)`;
                container.appendChild(badge);
            });
        }

        function cc_checkAchievements() {
            cc_GAME.achievements.forEach(a => {
                if (!a.unlocked && a.req(cc_GAME)) {
                    a.unlocked = true;
                    showToast(`Achievement Unlocked: ${a.name}!`, 'success');
                    cc_renderAchievements();
                }
            });
        }

        function cc_renameBakery() {
            const name = prompt("Enter your new bakery name:", cc_GAME.bakeryName);
            if (name && name.trim()) {
                cc_GAME.bakeryName = name.trim();
                const nameEl = document.getElementById('bakeryName');
                if (nameEl) nameEl.innerText = cc_GAME.bakeryName;
            }
        }

        function cc_switchTab(tabId) {
            playSound('click');
            document.querySelectorAll('.cc-tab-content').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.cc-tab-btn').forEach(btn => {
                btn.classList.remove('bg-amber-500', 'text-bakery-900', 'shadow');
                btn.classList.add('text-amber-200/80');
            });
            const targetTab = document.getElementById(`cc-tab-${tabId}`);
            if (targetTab) targetTab.classList.remove('hidden');
            const activeBtn = document.getElementById(`cc-tabBtn-${tabId}`);
            if (activeBtn) {
                activeBtn.classList.add('bg-amber-500', 'text-bakery-900', 'shadow');
                activeBtn.classList.remove('text-amber-200/80');
            }
            if (tabId === 'stats') cc_updateStatsDisplay();
        }

        function cc_saveGame(manual = false) {
            const saveData = {
                cookies: cc_GAME.cookies,
                totalCookiesBaked: cc_GAME.totalCookiesBaked,
                totalClicks: cc_GAME.totalClicks,
                handmadeCookies: cc_GAME.handmadeCookies,
                goldenCookiesClicked: cc_GAME.goldenCookiesClicked,
                bakeryName: cc_GAME.bakeryName,
                timePlayed: Math.floor((Date.now() - cc_GAME.startTime) / 1000) + cc_GAME.timePlayed,
                buildings: cc_GAME.buildings.map(b => ({ id: b.id, owned: b.owned })),
                upgrades: cc_GAME.upgrades.map(u => ({ id: u.id, bought: u.bought })),
                achievements: cc_GAME.achievements.map(a => ({ id: a.id, unlocked: a.unlocked }))
            };
            try {
                localStorage.setItem('cookieClickerDeluxeSave', JSON.stringify(saveData));
                if (manual) showToast('Cookie Clicker saved successfully!', 'success');
            } catch (e) {}
        }

        function cc_loadGame() {
            try {
                const raw = localStorage.getItem('cookieClickerDeluxeSave');
                if (!raw) return;
                const data = JSON.parse(raw);
                cc_GAME.cookies = data.cookies || 0;
                cc_GAME.totalCookiesBaked = data.totalCookiesBaked || 0;
                cc_GAME.totalClicks = data.totalClicks || 0;
                cc_GAME.handmadeCookies = data.handmadeCookies || 0;
                cc_GAME.goldenCookiesClicked = data.goldenCookiesClicked || 0;
                cc_GAME.bakeryName = data.bakeryName || "My Delicious Bakery";
                cc_GAME.timePlayed = data.timePlayed || 0;
                
                const nameEl = document.getElementById('bakeryName');
                if (nameEl) nameEl.innerText = cc_GAME.bakeryName;

                if (data.buildings) {
                    data.buildings.forEach(savedB => {
                        const b = cc_GAME.buildings.find(item => item.id === savedB.id);
                        if (b) b.owned = savedB.owned;
                    });
                }
                if (data.upgrades) {
                    data.upgrades.forEach(savedU => {
                        const u = cc_GAME.upgrades.find(item => item.id === savedU.id);
                        if (u) u.bought = savedU.bought;
                    });
                }
                if (data.achievements) {
                    data.achievements.forEach(savedA => {
                        const a = cc_GAME.achievements.find(item => item.id === savedA.id);
                        if (a) a.unlocked = savedA.unlocked;
                    });
                }
            } catch (e) {}
        }

        function cc_exportSave() {
            cc_saveGame();
            const raw = localStorage.getItem('cookieClickerDeluxeSave');
            if (raw) {
                const b64 = btoa(raw);
                prompt("Copy your export save string below:", b64);
            }
        }

        function cc_importSave() {
            const code = prompt("Paste your export save string here:");
            if (code) {
                try {
                    const raw = atob(code.trim());
                    localStorage.setItem('cookieClickerDeluxeSave', raw);
                    cc_loadGame();
                    cc_updateHeaderStats();
                    cc_updateBuildingsUI();
                    cc_updateUpgradesUI();
                    cc_renderAchievements();
                    showToast("Save game imported successfully!", 'success');
                } catch (e) {
                    alert("Invalid save code string!");
                }
            }
        }

        function cc_confirmReset() {
            if (confirm("Are you sure you want to completely wipe all Cookie Clicker progress?")) {
                localStorage.removeItem('cookieClickerDeluxeSave');
                location.reload();
            }
        }

        let cc_initialized = false;
        let cc_interval = null;

        function initCookieClicker() {
            if (!cc_initialized) {
                cc_loadGame();
                cc_initBuildingsDOM();
                cc_initUpgradesDOM();
                cc_updateBuildingsUI();
                cc_updateUpgradesUI();
                cc_renderAchievements();
                cc_updateHeaderStats();

                const bigCookie = document.getElementById('bigCookie');
                if (bigCookie) {
                    bigCookie.addEventListener('pointerdown', (e) => {
                        e.preventDefault();
                        cc_clickBigCookie(e);
                    });
                }
                cc_initialized = true;
            }

            if (!cc_interval) {
                cc_interval = setInterval(() => {
                    if (currentGameMode === 'cookie-clicker') {
                        const cps = cc_getTotalCps();
                        const deltaCookies = cps / 10;
                        cc_GAME.cookies += deltaCookies;
                        cc_GAME.totalCookiesBaked += deltaCookies;

                        gameState.tokens = (Number(gameState.tokens) || 0) + deltaCookies;
                        updateGlobalHeader();

                        const now = Date.now();
                        cc_GAME.activeBuffs = cc_GAME.activeBuffs.filter(b => b.expiresAt > now);
                        cc_renderBuffs();
                        cc_updateHeaderStats();
                        cc_updateBuildingsUI();
                        cc_updateUpgradesUI();
                        cc_checkAchievements();
                    }
                }, 100);

                setInterval(() => {
                    if (currentGameMode === 'cookie-clicker') {
                        cc_saveGame(false);
                    }
                }, 10000);

                setInterval(() => {
                    if (currentGameMode === 'cookie-clicker' && Math.random() < 0.3) {
                        cc_spawnGoldenCookie();
                    }
                }, 20000);
            }
        }

        function mainLoop() {
            if (currentGameMode === 'dl-down') {
                updateDLGame();
                renderDLGame();
            }
            requestAnimationFrame(mainLoop);
        }

        setInterval(() => {
            if (activeTab === 'minigame') {
                updateArcadeHubUI();
            }
            if (activeTab === 'wheel') {
                checkWheelCooldown();
            }
        }, 1000);

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


Changes Made:
Added Blook Definition:
Maxim Malhaire added into ALL_BLOOKS with rarity: "Supreme", dropChance: 0.001, sellValue: 500000, isSupreme: true, and emoji 🌟✨.
Pack Drop Logic:
Celestial / Supreme Pack (Pack 50) now features both Maxim Malhaire and Aidan Ali Chunara in the Supreme drop pool at 0.001% drop rate.
Dynamic Supreme Unlock Modal:
The celebration popup dynamically updates its title, name (supreme-modal-name), emoji (supreme-modal-emoji), and claim button whenever either Supreme blook is pulled.
UI Filters & Labels:
Updated shop description, inventory rarity dropdown (Supreme (Aidan & Maxim)), and header title.
Your The Awty International School chats aren’t used to improve our models. Gemini isn’t human. It can make mistakes, so double-check it. Your privacy & Gemini
Opens in a new window


