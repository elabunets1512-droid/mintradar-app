<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>MintRadar App</title>
    <script src="https://telegram.org"></script>
    <style>
        :root {
            --bg: #0b0e14;
            --card: #151a23;
            --border: #232a36;
            --green: #22c55e;
            --blue: #38bdf8;
            --gold: #f59e0b;
            --text-muted: #8b949e;
        }
        * { box-sizing: border-box; }
        body {
            background-color: var(--bg);
            color: #ffffff;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            margin: 0;
            padding: 16px 16px 85px 16px;
            user-select: none;
        }
        /* Верхня шапка як на твоєму скріншоті */
        .top-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 18px;
        }
        .brand {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 20px;
            font-weight: 700;
        }
        .dot {
            width: 12px;
            height: 12px;
            background-color: var(--green);
            border-radius: 50%;
            box-shadow: 0 0 10px var(--green);
        }
        .live-badge {
            background: rgba(34, 197, 94, 0.12);
            color: var(--green);
            border: 1px solid rgba(34, 197, 94, 0.3);
            padding: 4px 10px;
            border-radius: 20px;
            font-size: 11px;
            font-weight: 700;
            letter-spacing: 0.5px;
        }
        /* Сітка карток */
        .grid-2 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 12px;
            margin-bottom: 16px;
        }
        .stat-card {
            background: var(--card);
            border: 1px solid var(--border);
            border-radius: 14px;
            padding: 14px;
        }
        .stat-label {
            font-size: 11px;
            color: var(--text-muted);
            text-transform: uppercase;
            margin-bottom: 6px;
        }
        .stat-val {
            font-size: 20px;
            font-weight: 700;
        }
        .green-text { color: var(--green); }
        
        /* Список подарунків */
        .section-title {
            font-size: 14px;
            color: var(--text-muted);
            margin: 18px 0 10px;
            font-weight: 600;
        }
        .gift-card {
            background: var(--card);
            border: 1px solid var(--border);
            border-radius: 14px;
            padding: 14px;
            margin-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .gift-left h4 { margin: 0 0 4px 0; font-size: 15px; }
        .gift-left p { margin: 0; font-size: 12px; color: var(--text-muted); }
        .gift-right { text-align: right; }
        .ton-price { color: var(--blue); font-weight: 700; font-size: 15px; }
        .usd-price { color: var(--text-muted); font-size: 12px; }
        .tag-wait {
            background: rgba(56, 189, 248, 0.15);
            color: var(--blue);
            font-size: 11px;
            padding: 4px 8px;
            border-radius: 8px;
            font-weight: 600;
        }

        /* Вкладки */
        .tab-content { display: none; }
        .tab-content.active { display: block; animation: fadeIn 0.2s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

        /* Рулетка */
        .roulette-box {
            background: var(--card);
            border: 1px solid var(--border);
            border-radius: 18px;
            padding: 24px 16px;
            text-align: center;
            margin-top: 10px;
        }
        .roulette-wheel {
            font-size: 64px;
            margin: 16px 0;
            display: inline-block;
            transition: transform 1.5s cubic-bezier(0.17, 0.67, 0.12, 0.99);
        }
        .spin-btn {
            background: linear-gradient(90deg, #f59e0b, #d97706);
            color: #000;
            border: none;
            width: 100%;
            padding: 14px;
            border-radius: 12px;
            font-size: 16px;
            font-weight: 800;
            cursor: pointer;
            margin-top: 14px;
        }
        .spin-btn:disabled { opacity: 0.5; }
        .chances {
            display: flex;
            justify-content: space-around;
            margin-top: 16px;
            font-size: 12px;
            color: var(--text-muted);
            background: var(--bg);
            padding: 10px;
            border-radius: 10px;
        }

        /* Гаманець та Профіль */
        .action-btn {
            background: #0284c7;
            color: white;
            border: none;
            width: 100%;
            padding: 14px;
            border-radius: 12px;
            font-size: 15px;
            font-weight: 700;
            cursor: pointer;
            margin-top: 12px;
        }
        .profile-header {
            text-align: center;
            padding: 20px 0;
        }
        .avatar {
            width: 72px;
            height: 72px;
            border-radius: 50%;
            background: var(--card);
            border: 2px solid var(--blue);
            display: inline-flex;
            align-items: center;
            justify-content: center;
            font-size: 28px;
            margin-bottom: 10px;
            overflow: hidden;
        }
        .avatar img { width: 100%; height: 100%; object-fit: cover; }

        /* Нижнє меню (Таб-бар) */
        .bottom-nav {
            position: fixed;
            bottom: 0;
            left: 0;
            right: 0;
            background: #11151d;
            border-top: 1px solid var(--border);
            display: flex;
            justify-content: space-around;
            padding: 8px 0 14px 0;
            z-index: 100;
        }
        .nav-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            color: var(--text-muted);
            font-size: 11px;
            cursor: pointer;
            gap: 4px;
            width: 25%;
        }
        .nav-item.active { color: var(--blue); font-weight: 600; }
        .nav-icon { font-size: 20px; }
    </style>
</head>
<body>

    <div class="top-bar">
        <div class="brand"><div class="dot"></div> MintRadar</div>
        <div class="live-badge">LIVE SCAN</div>
    </div>

    <!-- ВКЛАДКА 1: РАДАР -->
    <div id="tab-radar" class="tab-content active">
        <div class="grid-2">
            <div class="stat-card">
                <div class="stat-label">Нових мінтів (24г)</div>
                <div class="stat-val green-text">1,428</div>
            </div>
            <div class="s
