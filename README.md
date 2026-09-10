<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>Мобильный Игровой Хаб</title>
<style>
    :root {
        --bg-color: #0f0f13;
        --bg-gradient: radial-gradient(circle at 20% 0%, #1a1a2e 0%, #0f0f13 60%);
        --card-bg: rgba(30, 30, 40, 0.85);
        --card-border: rgba(255,255,255,0.06);
        --primary: #4caf50;
        --secondary: #ff9800;
        --match3-color: #9c27b0;
        --reaction-color: #00bcd4;
        --puzzle-color: #f44336;
        --arknoid-color: #3f51b5;
        --premium-color: #ffc107;
        --tetris-color: #00e676;
        --tower-color: #ff5722;
        --ad-color: #ff4081;
        --vk-color: #0077ff;
        --gold: #ffd54f;
        --text: #ffffff;
        --text-secondary: #9a9aa8;
        --shadow: 0 10px 30px rgba(0,0,0,0.4);
    }

    * { box-sizing: border-box; }

    body {
        background-color: var(--bg-color);
        background-image: var(--bg-gradient);
        background-attachment: fixed;
        color: var(--text);
        font-family: system-ui, -apple-system, "Segoe UI", sans-serif;
        margin: 0;
        padding: 0;
        display: flex;
        flex-direction: column;
        align-items: center;
        min-height: 100vh;
        overflow-x: hidden;
        -webkit-font-smoothing: antialiased;
    }

    .screen {
        display: none;
        width: 100%;
        max-width: 460px;
        padding: 20px;
        flex-direction: column;
        align-items: center;
    }
    .screen.active { display: flex; animation: fadeIn 0.3s ease; }
    @keyframes fadeIn {
        from { opacity: 0; transform: translateY(8px); }
        to   { opacity: 1; transform: translateY(0); }
    }

    .header-bar {
        width: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-bottom: 4px;
    }

    h1 {
        text-align: center;
        margin: 8px 0 0;
        font-size: 30px;
        font-weight: 800;
        letter-spacing: 2px;
        background: linear-gradient(90deg, #4caf50, #ff9800, #9c27b0, #00bcd4, #f44336, #3f51b5, #00e676, #ff5722, #ff4081, #ffc107);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        animation: hueShift 8s linear infinite;
    }
    @keyframes hueShift {
        0%,100% { filter: hue-rotate(0deg); }
        50%     { filter: hue-rotate(30deg); }
    }

    .coin-badge {
        display: inline-flex;
        align-items: center;
        gap: 6px;
        background: linear-gradient(135deg, rgba(255,193,7,0.15), rgba(255,152,0,0.1));
        border: 1px solid rgba(255,193,7,0.4);
        color: var(--gold);
        padding: 6px 12px;
        border-radius: 20px;
        font-weight: 800;
        font-size: 15px;
        box-shadow: 0 0 14px rgba(255,193,7,0.15);
    }
    .coin-badge.bump { animation: coinBump 0.4s ease; }
    @keyframes coinBump {
        0%   { transform: scale(1); }
        50%  { transform: scale(1.25); box-shadow: 0 0 24px rgba(255,193,7,0.6); }
        100% { transform: scale(1); }
    }

    .ad-banner {
        width: 100%;
        margin-top: 14px;
        background: linear-gradient(135deg, rgba(255,64,129,0.15), rgba(156,39,176,0.1));
        border: 1px solid rgba(255,64,129,0.4);
        border-radius: 16px;
        padding: 14px 16px;
        display: flex;
        align-items: center;
        gap: 12px;
        cursor: pointer;
        transition: transform 0.15s, box-shadow 0.25s;
        box-shadow: 0 6px 20px rgba(255,64,129,0.15);
        -webkit-tap-highlight-color: transparent;
    }
    .ad-banner:active { transform: scale(0.97); box-shadow: 0 0 24px rgba(255,64,129,0.4); }
    .ad-banner-icon { font-size: 28px; flex-shrink: 0; }
    .ad-banner-info { flex: 1; text-align: left; }
    .ad-banner-info h4 { margin: 0 0 3px 0; font-size: 15px; font-weight: 800; color: #ff4081; }
    .ad-banner-info p { margin: 0; font-size: 12px; color: var(--text-secondary); }
    .ad-banner-reward {
        background: linear-gradient(135deg, #ffc107, #ff9800);
        color: #1a1200;
        font-weight: 800;
        font-size: 13px;
        padding: 6px 10px;
        border-radius: 12px;
        white-space: nowrap;
    }

    .section-title {
        width: 100%;
        margin: 20px 0 6px;
        font-size: 14px;
        font-weight: 700;
        letter-spacing: 1.5px;
        color: var(--text-secondary);
        text-transform: uppercase;
        display: flex;
        align-items: center;
        gap: 10px;
    }
    .section-title::after {
        content: "";
        flex: 1;
        height: 1px;
        background: linear-gradient(90deg, rgba(255,255,255,0.12), transparent);
    }
    .section-title.premium { color: var(--gold); }
    .section-title.premium::after {
        background: linear-gradient(90deg, rgba(255,193,7,0.4), transparent);
    }

    .grid-menu {
        display: flex;
        flex-direction: column;
        gap: 12px;
        width: 100%;
    }

    .game-card {
        position: relative;
        background: var(--card-bg);
        backdrop-filter: blur(12px);
        -webkit-backdrop-filter: blur(12px);
        border: 1px solid var(--card-border);
        border-radius: 18px;
        padding: 16px 18px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        cursor: pointer;
        overflow: hidden;
        box-shadow: var(--shadow);
        transition: transform 0.15s ease, box-shadow 0.25s ease;
        -webkit-tap-highlight-color: transparent;
    }
    .game-card::before {
        content: "";
        position: absolute;
        inset: 0;
        background: linear-gradient(120deg, transparent 30%, rgba(255,255,255,0.06) 50%, transparent 70%);
        transform: translateX(-100%);
        transition: transform 0.6s ease;
    }
    .game-card:hover::before { transform: translateX(100%); }
    .game-card:active { transform: scale(0.97); }

    .card-snake    { border-left: 3px solid var(--primary); }
    .card-2048     { border-left: 3px solid var(--secondary); }
    .card-match3   { border-left: 3px solid var(--match3-color); }
    .card-reaction { border-left: 3px solid var(--reaction-color); }
    .card-puzzle   { border-left: 3px solid var(--puzzle-color); }
    .card-arknoid  { border-left: 3px solid var(--arknoid-color); }
    .card-premium {
        border-left: 3px solid var(--premium-color);
        background: linear-gradient(135deg, rgba(255,193,7,0.08), rgba(156,39,176,0.08)), var(--card-bg);
    }
    .card-tetris {
        border-left: 3px solid var(--tetris-color);
        background: linear-gradient(135deg, rgba(0,230,118,0.08), rgba(0,188,212,0.08)), var(--card-bg);
    }
    .card-tower {
        border-left: 3px solid var(--tower-color);
        background: linear-gradient(135deg, rgba(255,87,34,0.08), rgba(255,193,7,0.08)), var(--card-bg);
    }

    .card-snake:active    { box-shadow: 0 0 24px rgba(76,175,80,0.35); }
    .card-2048:active     { box-shadow: 0 0 24px rgba(255,152,0,0.35); }
    .card-match3:active   { box-shadow: 0 0 24px rgba(156,39,176,0.35); }
    .card-reaction:active { box-shadow: 0 0 24px rgba(0,188,212,0.35); }
    .card-puzzle:active   { box-shadow: 0 0 24px rgba(244,67,54,0.35); }
    .card-arknoid:active  { box-shadow: 0 0 24px rgba(63,81,181,0.4); }
    .card-premium:active  { box-shadow: 0 0 28px rgba(255,193,7,0.4); }
    .card-tetris:active   { box-shadow: 0 0 28px rgba(0,230,118,0.4); }
    .card-tower:active    { box-shadow: 0 0 28px rgba(255,87,34,0.4); }

    .game-info h3 { margin: 0 0 4px 0; font-size: 18px; font-weight: 700; }
    .game-info p  { margin: 0; color: var(--text-secondary); font-size: 13px; }

    .game-icon {
        font-size: 32px;
        filter: drop-shadow(0 4px 10px rgba(0,0,0,0.4));
        transition: transform 0.3s ease;
    }
    .game-card:active .game-icon { transform: scale(1.15) rotate(-6deg); }

    .price-tag {
        display: inline-flex;
        align-items: center;
        gap: 4px;
        background: linear-gradient(135deg, #ffc107, #ff9800);
        color: #1a1200;
        font-weight: 800;
        font-size: 13px;
        padding: 4px 10px;
        border-radius: 12px;
        margin-top: 4px;
        box-shadow: 0 2px 8px rgba(255,152,0,0.4);
    }
    .price-tag.tetris-price {
        background: linear-gradient(135deg, #00e676, #00b8d4);
        color: #001a0a;
        box-shadow: 0 2px 8px rgba(0,230,118,0.4);
    }
    .price-tag.tower-price {
        background: linear-gradient(135deg, #ff5722, #ff9800);
        color: #1a0a00;
        box-shadow: 0 2px 8px rgba(255,87,34,0.4);
    }
    .price-tag.owned {
        background: linear-gradient(135deg, #66bb6a, #2e7d32);
        color: #fff;
    }

    .back-btn {
        align-self: flex-start;
        background: rgba(255,255,255,0.06);
        color: white;
        border: 1px solid rgba(255,255,255,0.1);
        padding: 10px 18px;
        border-radius: 10px;
        font-size: 15px;
        font-weight: 600;
        margin-bottom: 15px;
        cursor: pointer;
        backdrop-filter: blur(8px);
        transition: background 0.2s, transform 0.15s;
    }
    .back-btn:active { transform: scale(0.94); background: rgba(255,255,255,0.12); }

    .score-container {
        display: flex;
        gap: 14px;
        margin-bottom: 15px;
        font-size: 16px;
        width: 100%;
        justify-content: center;
    }
    .score-box {
        background: var(--card-bg);
        backdrop-filter: blur(10px);
        padding: 10px 18px;
        border-radius: 12px;
        border: 1px solid var(--card-border);
        text-align: center;
        flex: 1;
        max-width: 160px;
        box-shadow: var(--shadow);
    }
    .score-box.record {
        border-color: rgba(255,213,79,0.5);
        box-shadow: 0 0 18px rgba(255,213,79,0.25);
    }
    .score-val {
        font-weight: 800;
        color: #fff;
        display: block;
        font-size: 20px;
        transition: transform 0.2s, color 0.3s;
    }
    .score-val.bump { animation: bump 0.4s ease; }
    @keyframes bump {
        0%   { transform: scale(1); }
        50%  { transform: scale(1.35); color: #ffd54f; }
        100% { transform: scale(1); }
    }

    canvas {
        border-radius: 12px;
        max-width: 100%;
        height: auto;
        display: block;
        box-shadow: var(--shadow);
        touch-action: none;
    }

    #snakeCanvas    { border: 2px solid rgba(76,175,80,0.5);   background-color: #060a06; }
    #canvas2048     { border: 2px solid rgba(255,152,0,0.5);   background-color: #1a1610; }
    #match3Canvas   { border: 2px solid rgba(156,39,176,0.5);  background-color: #100812; }
    #reactionCanvas { border: 2px solid rgba(0,188,212,0.5);   background-color: #041014; }
    #puzzleCanvas   { border: 2px solid rgba(244,67,54,0.5);   background-color: #140606; }
    #shooterCanvas  { border: 2px solid rgba(255,193,7,0.5);   background-color: #000510; }
    #arknoidCanvas  { border: 2px solid rgba(63,81,181,0.5);   background-color: #04060f; }
    #tetrisCanvas   { border: 2px solid rgba(0,230,118,0.5);   background-color: #021208; }
    #towerCanvas    { border: 2px solid rgba(255,87,34,0.5);   background-color: #0f0602; }

    .controls {
        display: grid;
        grid-template-areas:
            ".    up    ."
            "left .     right"
            ".    down  .";
        gap: 10px;
        margin-top: 20px;
        width: 210px;
    }

    .btn {
        background: rgba(255,255,255,0.06);
        color: white;
        border: 1px solid rgba(255,255,255,0.12);
        border-radius: 14px;
        padding: 18px;
        font-size: 22px;
        font-weight: bold;
        user-select: none;
        outline: none;
        cursor: pointer;
        backdrop-filter: blur(8px);
        transition: background 0.15s, transform 0.1s, border-color 0.15s;
        -webkit-tap-highlight-color: transparent;
    }
    .btn:active { transform: scale(0.9); }

    .snake-btn:active    { background: rgba(76,175,80,0.4);   border-color: #81c784; }
    .btn2048:active      { background: rgba(255,152,0,0.4);   border-color: #ffb74d; }
    .btn-match3:active   { background: rgba(156,39,176,0.4);  border-color: #ba68c8; }
    .btn-arknoid:active  { background: rgba(63,81,181,0.4);   border-color: #7986cb; }
    .btn-tetris:active   { background: rgba(0,230,118,0.4);   border-color: #69f0ae; }
    .btn-shooter:active  { background: rgba(255,193,7,0.4);   border-color: #ffe082; }
    .btn-tower:active    { background: rgba(255,87,34,0.4);   border-color: #ff8a65; }

    .btn-up { grid-area: up; }
    .btn-down { grid-area: down; }
    .btn-left { grid-area: left; }
    .btn-right { grid-area: right; }

    .big-action-btn {
        margin-top: 18px;
        padding: 16px 36px;
        font-size: 18px;
        font-weight: 800;
        letter-spacing: 1px;
        border: none;
        border-radius: 16px;
        cursor: pointer;
        color: white;
        background: linear-gradient(135deg, #00bcd4, #0097a7);
        box-shadow: 0 8px 24px rgba(0,188,212,0.35);
        transition: transform 0.15s, box-shadow 0.2s;
        -webkit-tap-highlight-color: transparent;
    }
    .big-action-btn:active { transform: scale(0.94); }
    .big-action-btn.puzzle-style {
        background: linear-gradient(135deg, #f44336, #c62828);
        box-shadow: 0 8px 24px rgba(244,67,54,0.35);
    }
    .big-action-btn.tetris-btn {
        background: linear-gradient(135deg, #00e676, #00b8d4);
        color: #001a0a;
        box-shadow: 0 8px 24px rgba(0,230,118,0.4);
    }
    .big-action-btn:disabled {
        opacity: 0.5;
        cursor: not-allowed;
        transform: none;
    }

    .hint-text {
        margin-top: 12px;
        font-size: 13px;
        color: var(--text-secondary);
        text-align: center;
        max-width: 300px;
        line-height: 1.4;
    }

    .toast {
        position: fixed;
        top: 20px;
        left: 50%;
        transform: translateX(-50%) translateY(-100px);
        background: rgba(30,30,40,0.95);
        backdrop-filter: blur(12px);
        border: 1px solid rgba(255,255,255,0.1);
        padding: 14px 24px;
        border-radius: 14px;
        box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        font-size: 15px;
        font-weight: 600;
        z-index: 1000;
        opacity: 0;
        transition: transform 0.4s cubic-bezier(0.2, 1.2, 0.4, 1), opacity 0.3s;
        pointer-events: none;
        text-align: center;
        max-width: 90vw;
    }
    .toast.show { transform: translateX(-50%) translateY(0); opacity: 1; }
    .toast.success { border-color: rgba(76,175,80,0.5); }
    .toast.info    { border-color: rgba(255,152,0,0.5); }
    .toast.record  { border-color: rgba(255,213,79,0.7); background: rgba(50,40,10,0.95); }
    .toast.coin    { border-color: rgba(255,193,7,0.7); color: var(--gold); }
    .toast.ad      { border-color: rgba(255,64,129,0.7); color: #ff4081; }

    .modal-overlay {
        position: fixed;
        inset: 0;
        background: rgba(0,0,0,0.7);
        backdrop-filter: blur(6px);
        display: none;
        align-items: center;
        justify-content: center;
        z-index: 999;
        padding: 20px;
    }
    .modal-overlay.show { display: flex; animation: fadeIn 0.25s ease; }
    .modal {
        background: linear-gradient(160deg, #1e1e2e, #141420);
        border: 1px solid rgba(255,193,7,0.3);
        border-radius: 22px;
        padding: 26px 22px;
        max-width: 360px;
        width: 100%;
        text-align: center;
        box-shadow: 0 20px 60px rgba(0,0,0,0.7), 0 0 40px rgba(255,193,7,0.15);
    }
    .modal-icon { font-size: 56px; margin-bottom: 6px; }
    .modal h2 {
        margin: 4px 0 8px;
        font-size: 22px;
        background: linear-gradient(135deg, #ffc107, #ff9800);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
    }
    .modal h2.tetris-title {
        background: linear-gradient(135deg, #00e676, #00b8d4);
        -webkit-background-clip: text;
        background-clip: text;
    }
    .modal h2.tower-title {
        background: linear-gradient(135deg, #ff5722, #ff9800);
        -webkit-background-clip: text;
        background-clip: text;
    }
    .modal p {
        color: var(--text-secondary);
        font-size: 14px;
        margin: 6px 0 18px;
        line-height: 1.5;
    }
    .modal-price {
        display: inline-flex;
        align-items: center;
        gap: 6px;
        font-size: 22px;
        font-weight: 800;
        color: var(--gold);
        margin-bottom: 18px;
    }
    .modal-buttons {
        display: flex;
        gap: 10px;
    }
    .modal-btn {
        flex: 1;
        padding: 14px;
        border-radius: 12px;
        border: none;
        font-weight: 700;
        font-size: 15px;
        cursor: pointer;
        transition: transform 0.15s;
    }
    .modal-btn:active { transform: scale(0.94); }
    .modal-btn.cancel {
        background: rgba(255,255,255,0.08);
        color: white;
        border: 1px solid rgba(255,255,255,0.1);
    }
    .modal-btn.buy {
        background: linear-gradient(135deg, #ffc107, #ff9800);
        color: #1a1200;
        box-shadow: 0 6px 20px rgba(255,193,7,0.4);
    }
    .modal-btn.buy:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }

    /* Реклама — слайды */
    .ad-modal {
        background: linear-gradient(160deg, #1e1e2e, #141420);
        border: 1px solid rgba(255,64,129,0.4);
        border-radius: 22px;
        padding: 22px;
        max-width: 400px;
        width: 100%;
        text-align: center;
        box-shadow: 0 20px 60px rgba(0,0,0,0.7), 0 0 40px rgba(255,64,129,0.2);
    }
    .ad-modal h2 {
        margin: 0 0 6px;
        font-size: 22px;
        background: linear-gradient(135deg, #ff4081, #9c27b0);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
    }
    .ad-modal .ad-channel {
        color: var(--text-secondary);
        font-size: 13px;
        margin: 0 0 14px;
    }
    .ad-modal .ad-channel strong { color: #ff4081; }

    .ad-slide-container {
        position: relative;
        width: 100%;
        aspect-ratio: 16 / 11;
        background: linear-gradient(135deg, #1a0a2e, #0a0620);
        border-radius: 14px;
        overflow: hidden;
        box-shadow: 0 6px 20px rgba(0,0,0,0.5);
        margin-bottom: 14px;
        border: 1px solid rgba(255,64,129,0.2);
    }
    .ad-slide {
        position: absolute;
        inset: 0;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 16px;
        opacity: 0;
        transform: scale(0.9) translateY(10px);
        transition: opacity 0.5s ease, transform 0.5s ease;
        text-align: center;
    }
    .ad-slide.active {
        opacity: 1;
        transform: scale(1) translateY(0);
    }
    .ad-slide-icon {
        font-size: 42px;
        margin-bottom: 6px;
        filter: drop-shadow(0 4px 12px rgba(255,64,129,0.5));
        animation: adIconFloat 2s ease-in-out infinite;
    }
    @keyframes adIconFloat {
        0%, 100% { transform: translateY(0); }
        50%      { transform: translateY(-6px); }
    }
    .ad-slide-title {
        font-size: 18px;
        font-weight: 800;
        margin: 0 0 6px;
        background: linear-gradient(135deg, #ff4081, #ffc107);
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        letter-spacing: 0.5px;
    }
    .ad-slide-text {
        font-size: 12px;
        color: #ccc;
        line-height: 1.45;
        margin: 0 0 8px;
        max-width: 300px;
    }
    .ad-slide-tag {
        display: inline-block;
        margin-bottom: 8px;
        padding: 3px 10px;
        background: rgba(255,64,129,0.15);
        border: 1px solid rgba(255,64,129,0.4);
        border-radius: 10px;
        font-size: 10px;
        font-weight: 700;
        color: #ff4081;
        letter-spacing: 0.5px;
    }

    /* Кнопки действий на слайде */
    .ad-slide-actions {
        display: flex;
        gap: 6px;
        flex-wrap: wrap;
        justify-content: center;
        margin-top: 4px;
    }
    .ad-slide-btn {
        display: inline-flex;
        align-items: center;
        gap: 4px;
        padding: 8px 14px;
        border-radius: 10px;
        border: none;
        font-weight: 700;
        font-size: 12px;
        cursor: pointer;
        text-decoration: none;
        color: #fff;
        transition: transform 0.15s, box-shadow 0.2s;
        -webkit-tap-highlight-color: transparent;
        white-space: nowrap;
    }
    .ad-slide-btn:active { transform: scale(0.93); }
    .ad-slide-btn.vk {
        background: linear-gradient(135deg, #0077ff, #0055cc);
        box-shadow: 0 4px 14px rgba(0,119,255,0.45);
        animation: vkPulse 2s ease-in-out infinite;
    }
    @keyframes vkPulse {
        0%, 100% { box-shadow: 0 4px 14px rgba(0,119,255,0.45); }
        50%      { box-shadow: 0 4px 22px rgba(0,119,255,0.75); }
    }
    .ad-slide-btn.video {
        background: linear-gradient(135deg, #ff4081, #9c27b0);
        box-shadow: 0 4px 14px rgba(255,64,129,0.45);
    }
    .ad-slide-btn.time {
        background: linear-gradient(135deg, #ffc107, #ff9800);
        color: #1a1200;
        box-shadow: 0 4px 14px rgba(255,193,7,0.45);
    }

    .ad-progress-dots {
        display: flex;
        justify-content: center;
        gap: 6px;
        margin-bottom: 12px;
    }
    .ad-dot {
        width: 8px;
        height: 8px;
        border-radius: 50%;
        background: rgba(255,255,255,0.15);
        transition: background 0.3s, transform 0.3s;
    }
    .ad-dot.active {
        background: #ff4081;
        transform: scale(1.3);
        box-shadow: 0 0 10px rgba(255,64,129,0.6);
    }
    .ad-dot.done {
        background: rgba(255,64,129,0.5);
    }

    .ad-timer-bar {
        width: 100%;
        height: 6px;
        background: rgba(255,255,255,0.1);
        border-radius: 3px;
        overflow: hidden;
        margin-bottom: 12px;
    }
    .ad-timer-fill {
        height: 100%;
        background: linear-gradient(90deg, #ff4081, #9c27b0);
        width: 0%;
        transition: width 1s linear;
    }
    .ad-status {
        font-size: 14px;
        color: var(--text-secondary);
        margin-bottom: 14px;
        min-height: 20px;
    }
    .ad-status strong { color: var(--gold); }
    .ad-modal-buttons {
        display: flex;
        gap: 10px;
    }
    .ad-modal-btn {
        flex: 1;
        padding: 14px;
        border-radius: 12px;
        border: none;
        font-weight: 700;
        font-size: 15px;
        cursor: pointer;
        transition: transform 0.15s;
    }
    .ad-modal-btn:active { transform: scale(0.94); }
    .ad-modal-btn.cancel {
        background: rgba(255,255,255,0.08);
        color: white;
        border: 1px solid rgba(255,255,255,0.1);
    }
    .ad-modal-btn.claim {
        background: linear-gradient(135deg, #ff4081, #9c27b0);
        color: white;
        box-shadow: 0 6px 20px rgba(255,64,129,0.4);
    }
    .ad-modal-btn.claim:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }
</style>
</head>
<body>

<div id="toast" class="toast"></div>

<!-- МОДАЛЬНОЕ ОКНО ПОКУПКИ -->
<div class="modal-overlay" id="buyModal">
    <div class="modal">
        <div class="modal-icon" id="modalIcon">🚀</div>
        <h2 id="modalTitle">Космический Шутер</h2>
        <p id="modalDesc"></p>
        <div class="modal-price">
            <span>🪙</span><span id="modalPrice">200</span>
        </div>
        <div class="modal-buttons">
            <button class="modal-btn cancel" onclick="closeBuyModal()">Отмена</button>
            <button class="modal-btn buy" id="modalBuyBtn" onclick="confirmBuy()">Купить</button>
        </div>
    </div>
</div>

<!-- МОДАЛЬНОЕ ОКНО РЕКЛАМЫ -->
<div class="modal-overlay" id="adModal">
    <div class="ad-modal">
        <h2>📺 Реклама</h2>
        <p class="ad-channel">Канал: <strong>Dley перезаливы</strong> · VK Video</p>

        <div class="ad-slide-container" id="adSlideContainer"></div>

        <div class="ad-progress-dots" id="adDots"></div>
        <div class="ad-timer-bar"><div class="ad-timer-fill" id="adTimerFill"></div></div>
        <div class="ad-status" id="adStatus">Смотрите рекламу <strong>15 секунд</strong></div>
        <div class="ad-modal-buttons">
            <button class="ad-modal-btn cancel" onclick="closeAdModal()">Закрыть</button>
            <button class="ad-modal-btn claim" id="adClaimBtn" onclick="claimAdReward()" disabled>Получить 40 🪙</button>
        </div>
    </div>
</div>

<!-- ЭКРАН МЕНЮ -->
<div id="menu-screen" class="screen active">
    <div class="header-bar">
        <h1>GAME HUB</h1>
        <div class="coin-badge" id="coinBadge"><span>🪙</span><span id="coinCount">0</span></div>
    </div>

    <div class="ad-banner" id="adBanner" onclick="openAdModal()">
        <div class="ad-banner-icon">📺</div>
        <div class="ad-banner-info">
            <h4>Смотреть рекламу</h4>
            <p>Канал Dley перезаливы · VK Video</p>
        </div>
        <div class="ad-banner-reward" id="adBannerReward">+40 🪙</div>
    </div>

    <div class="section-title">Бесплатные игры</div>
    <div class="grid-menu">
        <div class="game-card card-snake" onclick="openGame('snake-screen')">
            <div class="game-info">
                <h3>Змейка</h3>
                <p>Рекорд: <span id="menu-snake-best">0</span></p>
            </div>
            <div class="game-icon">🐍</div>
        </div>
        <div class="game-card card-2048" onclick="openGame('game2048-screen')">
            <div class="game-info">
                <h3>2048</h3>
                <p>Рекорд: <span id="menu-2048-best">0</span></p>
            </div>
            <div class="game-icon">🔢</div>
        </div>
        <div class="game-card card-match3" onclick="openGame('match3-screen')">
            <div class="game-info">
                <h3>3 в ряд</h3>
                <p>Рекорд: <span id="menu-match3-best">0</span></p>
            </div>
            <div class="game-icon">💎</div>
        </div>
        <div class="game-card card-reaction" onclick="openGame('reaction-screen')">
            <div class="game-info">
                <h3>Реакция</h3>
                <p>Лучшее: <span id="menu-reaction-best">—</span></p>
            </div>
            <div class="game-icon">⚡</div>
        </div>
        <div class="game-card card-puzzle" onclick="openGame('puzzle-screen')">
            <div class="game-info">
                <h3>Пятнашки</h3>
                <p>Рекорд: <span id="menu-puzzle-best">0</span> ходов</p>
            </div>
            <div class="game-icon">🧩</div>
        </div>
        <div class="game-card card-arknoid" onclick="openGame('arknoid-screen')">
            <div class="game-info">
                <h3>Арканоид</h3>
                <p>Рекорд: <span id="menu-arknoid-best">0</span></p>
            </div>
            <div class="game-icon">🧱</div>
        </div>
    </div>

    <div class="section-title premium">💎 Премиум игры</div>
    <div class="grid-menu">
        <div class="game-card card-premium" onclick="onPremiumClick('shooter')">
            <div class="game-info">
                <h3>Космический Шутер</h3>
                <p>Волны врагов, боссы, бонусы</p>
                <div class="price-tag" id="shooterPriceTag">🪙 200</div>
            </div>
            <div class="game-icon">🚀</div>
        </div>
        <div class="game-card card-tetris" onclick="onPremiumClick('tetris')">
            <div class="game-info">
                <h3>Тетрис</h3>
                <p>Классика с новым дыханием</p>
                <div class="price-tag tetris-price" id="tetrisPriceTag">🪙 250</div>
            </div>
            <div class="game-icon">🧊</div>
        </div>
        <div class="game-card card-tower" onclick="onPremiumClick('tower')">
            <div class="game-info">
                <h3>Защитник Башни</h3>
                <p>Tower Defense с волнами врагов</p>
                <div class="price-tag tower-price" id="towerPriceTag">🪙 300</div>
            </div>
            <div class="game-icon">🛡️</div>
        </div>
    </div>
</div>

<!-- ЭКРАН: ЗМЕЙКА -->
<div id="snake-screen" class="screen">
    <button class="back-btn" onclick="closeGame('snake-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Очки <span id="snake-score" class="score-val">0</span></div>
        <div class="score-box" id="snake-best-box">Рекорд <span id="snake-best" class="score-val">0</span></div>
    </div>
    <canvas id="snakeCanvas" width="320" height="320"></canvas>
    <div class="controls">
        <button class="btn snake-btn btn-up"    onpointerdown="setSnakeDir(0,-1)">▲</button>
        <button class="btn snake-btn btn-left"  onpointerdown="setSnakeDir(-1,0)">◀</button>
        <button class="btn snake-btn btn-right" onpointerdown="setSnakeDir(1,0)">▶</button>
        <button class="btn snake-btn btn-down"  onpointerdown="setSnakeDir(0,1)">▼</button>
    </div>
</div>

<!-- ЭКРАН: 2048 -->
<div id="game2048-screen" class="screen">
    <button class="back-btn" onclick="closeGame('game2048-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Счет <span id="score2048" class="score-val">0</span></div>
        <div class="score-box" id="best2048-box">Рекорд <span id="best2048" class="score-val">0</span></div>
    </div>
    <canvas id="canvas2048" width="320" height="320"></canvas>
    <div class="controls">
        <button class="btn btn2048 btn-up"    onpointerdown="move2048('up')">▲</button>
        <button class="btn btn2048 btn-left"  onpointerdown="move2048('left')">◀</button>
        <button class="btn btn2048 btn-right" onpointerdown="move2048('right')">▶</button>
        <button class="btn btn2048 btn-down"  onpointerdown="move2048('down')">▼</button>
    </div>
</div>

<!-- ЭКРАН: 3 В РЯД -->
<div id="match3-screen" class="screen">
    <button class="back-btn" onclick="closeGame('match3-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Очки <span id="match3-score" class="score-val">0</span></div>
        <div class="score-box" id="match3-best-box">Рекорд <span id="match3-best" class="score-val">0</span></div>
    </div>
    <canvas id="match3Canvas" width="320" height="320"></canvas>
    <div class="controls">
        <button class="btn btn-match3 btn-up"    onpointerdown="moveMatch3('up')">▲</button>
        <button class="btn btn-match3 btn-left"  onpointerdown="moveMatch3('left')">◀</button>
        <button class="btn btn-match3 btn-right" onpointerdown="moveMatch3('right')">▶</button>
        <button class="btn btn-match3 btn-down"  onpointerdown="moveMatch3('down')">▼</button>
    </div>
    <div class="hint-text">Нажимайте на кристаллы, свайпайте или используйте кнопки.</div>
</div>

<!-- ЭКРАН: РЕАКЦИЯ -->
<div id="reaction-screen" class="screen">
    <button class="back-btn" onclick="closeGame('reaction-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Время <span id="reaction-time" class="score-val">—</span></div>
        <div class="score-box" id="reaction-best-box">Лучшее <span id="reaction-best" class="score-val">—</span></div>
    </div>
    <canvas id="reactionCanvas" width="320" height="320"></canvas>
    <button class="big-action-btn" id="reactionBtn" onpointerdown="reactionTap(event)">СТАРТ</button>
    <div class="hint-text">Дождитесь зелёного экрана и нажмите как можно быстрее.</div>
</div>

<!-- ЭКРАН: ПЯТНАШКИ -->
<div id="puzzle-screen" class="screen">
    <button class="back-btn" onclick="closeGame('puzzle-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Ходы <span id="puzzle-moves" class="score-val">0</span></div>
        <div class="score-box" id="puzzle-best-box">Рекорд <span id="puzzle-best" class="score-val">0</span></div>
    </div>
    <canvas id="puzzleCanvas" width="320" height="320"></canvas>
    <button class="big-action-btn puzzle-style" onpointerdown="shufflePuzzle()">ПЕРЕМЕШАТЬ</button>
    <div class="hint-text">Нажимайте на плитку рядом с пустой клеткой.</div>
</div>

<!-- ЭКРАН: АРКАНОИД -->
<div id="arknoid-screen" class="screen">
    <button class="back-btn" onclick="closeGame('arknoid-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Очки <span id="arknoid-score" class="score-val">0</span></div>
        <div class="score-box" id="arknoid-best-box">Рекорд <span id="arknoid-best" class="score-val">0</span></div>
    </div>
    <canvas id="arknoidCanvas" width="320" height="380"></canvas>
    <div class="controls">
        <button class="btn btn-arknoid btn-up"    onpointerdown="arknoidControl('fire')">🔥</button>
        <button class="btn btn-arknoid btn-left"  onpointerdown="arknoidControl('left')">◀</button>
        <button class="btn btn-arknoid btn-right" onpointerdown="arknoidControl('right')">▶</button>
        <button class="btn btn-arknoid btn-down"  onpointerdown="arknoidControl('pause')">⏸</button>
    </div>
    <div class="hint-text">Двигайте платформу, отбивайте мяч и разбивайте кирпичи.</div>
</div>

<!-- ЭКРАН: КОСМИЧЕСКИЙ ШУТЕР -->
<div id="shooter-screen" class="screen">
    <button class="back-btn" onclick="closeGame('shooter-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Очки <span id="shooter-score" class="score-val">0</span></div>
        <div class="score-box" id="shooter-best-box">Рекорд <span id="shooter-best" class="score-val">0</span></div>
    </div>
    <canvas id="shooterCanvas" width="320" height="380"></canvas>
    <div class="controls">
        <button class="btn btn-shooter btn-up"    onpointerdown="shooterControl('fire')">🔥</button>
        <button class="btn btn-shooter btn-left"  onpointerdown="shooterControl('left')">◀</button>
        <button class="btn btn-shooter btn-right" onpointerdown="shooterControl('right')">▶</button>
        <button class="btn btn-shooter btn-down"  onpointerdown="shooterControl('pause')">⏸</button>
    </div>
    <div class="hint-text">Бонусы 💎 щит, ⚡ тройной выстрел, 💣 бомба. Боссы каждые 500 очков!</div>
</div>

<!-- ЭКРАН: ТЕТРИС -->
<div id="tetris-screen" class="screen">
    <button class="back-btn" onclick="closeGame('tetris-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Очки <span id="tetris-score" class="score-val">0</span></div>
        <div class="score-box" id="tetris-best-box">Рекорд <span id="tetris-best" class="score-val">0</span></div>
    </div>
    <canvas id="tetrisCanvas" width="240" height="380"></canvas>
    <div class="controls">
        <button class="btn btn-tetris btn-up"    onpointerdown="tetrisControl('rotate')">🔄</button>
        <button class="btn btn-tetris btn-left"  onpointerdown="tetrisControl('left')">◀</button>
        <button class="btn btn-tetris btn-right" onpointerdown="tetrisControl('right')">▶</button>
        <button class="btn btn-tetris btn-down"  onpointerdown="tetrisControl('drop')">⬇</button>
    </div>
    <button class="big-action-btn tetris-btn" id="tetrisPauseBtn" onpointerdown="tetrisControl('pause')">ПАУЗА</button>
    <div class="hint-text">Собирайте линии из блоков.</div>
</div>

<!-- ЭКРАН: ЗАЩИТНИК БАШНИ -->
<div id="tower-screen" class="screen">
    <button class="back-btn" onclick="closeGame('tower-screen')">◀ Меню</button>
    <div class="score-container">
        <div class="score-box">Волна <span id="tower-wave" class="score-val">0</span></div>
        <div class="score-box">🪙 <span id="tower-gold" class="score-val">0</span></div>
        <div class="score-box" id="tower-best-box">Рекорд <span id="tower-best" class="score-val">0</span></div>
    </div>
    <canvas id="towerCanvas" width="320" height="380"></canvas>
    <div class="tower-tools" id="towerTools">
        <button class="tool-btn" data-type="arrow" onclick="selectTower('arrow')" onpointerdown="selectTower('arrow')">
            <span class="tool-icon">🏹</span><span class="tool-name">Лучник</span><span class="tool-cost">50</span>
        </button>
        <button class="tool-btn" data-type="fire" onclick="selectTower('fire')" onpointerdown="selectTower('fire')">
            <span class="tool-icon">🔥</span><span class="tool-name">Огонь</span><span class="tool-cost">100</span>
        </button>
        <button class="tool-btn" data-type="ice" onclick="selectTower('ice')" onpointerdown="selectTower('ice')">
            <span class="tool-icon">❄️</span><span class="tool-name">Лёд</span><span class="tool-cost">75</span>
        </button>
        <button class="tool-btn tool-start" onclick="startTowerWave()" onpointerdown="startTowerWave()">
            <span class="tool-icon">▶</span><span class="tool-name">Волна</span><span class="tool-cost">GO</span>
        </button>
    </div>
    <div class="hint-text">Выберите башню и тапните по дорожке. Запустите волну!</div>
</div>

<style>
.tower-tools {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 8px;
    margin-top: 14px;
    width: 100%;
    max-width: 320px;
}
.tool-btn {
    background: rgba(255,255,255,0.06);
    border: 1px solid rgba(255,255,255,0.12);
    border-radius: 12px;
    padding: 8px 4px;
    color: white;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2px;
    cursor: pointer;
    transition: transform 0.12s, background 0.15s, border-color 0.15s;
    -webkit-tap-highlight-color: transparent;
}
.tool-btn:active { transform: scale(0.92); }
.tool-btn.selected {
    background: rgba(255,87,34,0.25);
    border-color: #ff5722;
    box-shadow: 0 0 16px rgba(255,87,34,0.4);
}
.tool-btn:disabled { opacity: 0.4; }
.tool-icon { font-size: 20px; }
.tool-name { font-size: 10px; font-weight: 700; letter-spacing: 0.3px; }
.tool-cost {
    font-size: 10px;
    font-weight: 800;
    color: var(--gold);
    background: rgba(255,193,7,0.15);
    padding: 1px 5px;
    border-radius: 6px;
}
.tool-start {
    background: linear-gradient(135deg, rgba(0,230,118,0.2), rgba(0,188,212,0.15));
    border-color: rgba(0,230,118,0.4);
}
</style>

<script>
/* ============================================================
   УТИЛИТЫ
============================================================ */
const $ = id => document.getElementById(id);

function toast(msg, type = 'info', duration = 2200) {
    const el = $('toast');
    el.textContent = msg;
    el.className = 'toast show ' + type;
    clearTimeout(el._t);
    el._t = setTimeout(() => el.classList.remove('show'), duration);
}
function haptic(ms = 12) { if (navigator.vibrate) navigator.vibrate(ms); }
function bump(el) { el.classList.remove('bump'); void el.offsetWidth; el.classList.add('bump'); }
function getBest(key) { return parseInt(localStorage.getItem(key) || '0', 10); }
function getBestFloat(key) { return parseFloat(localStorage.getItem(key) || '0'); }
function roundRect(ctx, x, y, w, h, r) {
    ctx.beginPath();
    if (ctx.roundRect) { ctx.roundRect(x, y, w, h, r); return; }
    ctx.moveTo(x + r, y);
    ctx.arcTo(x + w, y, x + w, y + h, r);
    ctx.arcTo(x + w, y + h, x, y + h, r);
    ctx.arcTo(x, y + h, x, y, r);
    ctx.arcTo(x, y, x + w, y, r);
    ctx.closePath();
}

/* ============================================================
   МОНЕТЫ
============================================================ */
function getCoins() { return parseInt(localStorage.getItem('coins') || '0', 10); }
function setCoins(v) { localStorage.setItem('coins', Math.max(0, v)); updateCoinsUI(); }
function addCoins(n, showToast = true) {
    if (n <= 0) return;
    setCoins(getCoins() + n);
    if (showToast) toast('+' + n + ' 🪙', 'coin', 1200);
}
function updateCoinsUI() {
    $('coinCount').textContent = getCoins();
    const badge = $('coinBadge');
    badge.classList.remove('bump');
    void badge.offsetWidth;
    badge.classList.add('bump');
}

/* ============================================================
   ПРЕМИУМ-ИГРЫ
============================================================ */
const PREMIUM = {
    shooter: {
        title: 'Космический Шутер', icon: '🚀', price: 200,
        desc: 'Волны врагов, боссы каждые 500 очков, бонусы (щит, тройной выстрел, бомба, жизни) и система комбо.',
        ownedKey: 'shooter_owned', screen: 'shooter-screen', tagId: 'shooterPriceTag', titleClass: ''
    },
    tetris: {
        title: 'Тетрис', icon: '🧊', price: 250,
        desc: 'Легендарная головоломка с падающими блоками. Собирайте линии, ускоряйтесь, ставьте рекорды.',
        ownedKey: 'tetris_owned', screen: 'tetris-screen', tagId: 'tetrisPriceTag', titleClass: 'tetris-title'
    },
    tower: {
        title: 'Защитник Башни', icon: '🛡️', price: 300,
        desc: 'Tower Defense: враги идут по дорожке, вы ставите башни. 3 типа башен, бесконечные волны.',
        ownedKey: 'tower_owned', screen: 'tower-screen', tagId: 'towerPriceTag', titleClass: 'tower-title'
    }
};

function isOwned(key) { return localStorage.getItem(PREMIUM[key].ownedKey) === '1'; }
function setOwned(key) { localStorage.setItem(PREMIUM[key].ownedKey, '1'); }

function updatePremiumTags() {
    for (const key in PREMIUM) {
        const p = PREMIUM[key];
        const tag = $(p.tagId);
        if (isOwned(key)) { tag.textContent = '✅ Куплено'; tag.classList.add('owned'); }
        else { tag.textContent = '🪙 ' + p.price; tag.classList.remove('owned'); }
    }
}

let pendingPurchase = null;

function onPremiumClick(key) {
    haptic(12);
    if (isOwned(key)) openGame(PREMIUM[key].screen);
    else openBuyModal(key);
}

function openBuyModal(key) {
    const p = PREMIUM[key];
    pendingPurchase = key;
    $('modalIcon').textContent = p.icon;
    $('modalTitle').textContent = p.title;
    $('modalTitle').className = p.titleClass || '';
    $('modalDesc').textContent = p.desc;
    $('modalPrice').textContent = p.price;
    const canAfford = getCoins() >= p.price;
    const btn = $('modalBuyBtn');
    btn.disabled = !canAfford;
    btn.textContent = canAfford ? 'Купить' : 'Не хватает 🪙';
    $('buyModal').classList.add('show');
}
function closeBuyModal() { $('buyModal').classList.remove('show'); pendingPurchase = null; }

function confirmBuy() {
    if (!pendingPurchase) return;
    const p = PREMIUM[pendingPurchase];
    if (getCoins() < p.price) { toast('Недостаточно монет', 'info', 1500); return; }
    setCoins(getCoins() - p.price);
    setOwned(pendingPurchase);
    haptic(40);
    const screen = p.screen;
    closeBuyModal();
    updatePremiumTags();
    toast('🎉 Игра куплена! Она ваша навсегда', 'success', 2000);
    setTimeout(() => openGame(screen), 400);
}

/* ============================================================
   РЕКЛАМА — ТЕКСТОВЫЕ СЛАЙДЫ С КНОПКАМИ ПЕРЕХОДА
============================================================ */
const AD_DURATION = 15;
const AD_REWARD = 40;

// Ссылки канала Dley перезаливы
const VK_CHANNEL_URL = 'https://vk.com/club239085797';           // канал/сообщество
const VK_VIDEO_URL   = 'https://vkvideo.ru/video-239085797_456239018'; // само видео
const VK_VIDEO_TIME  = 'https://vkvideo.ru/video-239085797_456239018?t=43s'; // видео с 43 сек

// Рекламные слайды канала Dley перезаливы (VK Video)
const AD_SLIDES = [
    {
        icon: '📺',
        title: 'Dley перезаливы',
        text: 'Лучшие видео, нарезки и приколы каждый день. Подписывайся на канал!',
        tag: 'VK VIDEO',
        buttons: [
            {text: '📺 Подписаться', url: VK_CHANNEL_URL, cls: 'vk'}
        ]
    },
    {
        icon: '🎬',
        title: 'Новые видео каждый день',
        text: 'Свежие перезаливы популярных роликов. Не пропусти самое интересное!',
        tag: 'СМОТРЕТЬ',
        buttons: [
            {text: '🔥 Смотреть видео', url: VK_VIDEO_URL, cls: 'video'}
        ]
    },
    {
        icon: '🔥',
        title: 'Топ-нарезки недели',
        text: 'Самое смешное, неожиданное и крутое — всё собрано в одном канале.',
        tag: 'VK VIDEO',
        buttons: [
            {text: '📺 Подписаться', url: VK_CHANNEL_URL, cls: 'vk'},
            {text: '▶ С 43 сек', url: VK_VIDEO_TIME, cls: 'time'}
        ]
    },
    {
        icon: '🎮',
        title: 'Игровые моменты',
        text: 'Забавные ситуации из игр, реакции и фейлы. Заряжайся позитивом!',
        tag: 'СМОТРЕТЬ',
        buttons: [
            {text: '🎬 Открыть видео', url: VK_VIDEO_URL, cls: 'video'}
        ]
    },
    {
        icon: '💎',
        title: 'Канал Dley перезаливы',
        text: 'Тысячи подписчиков уже смотрят. Присоединяйся и ты! Ссылка в VK.',
        tag: 'ПОДПИСАТЬСЯ',
        buttons: [
            {text: '📺 Подписаться на канал', url: VK_CHANNEL_URL, cls: 'vk'}
        ]
    }
];

let adTimer = null;
let adSecondsLeft = 0;
let adSlideIndex = 0;
let adSlideInterval = null;

function buildAdSlides() {
    const container = $('adSlideContainer');
    container.innerHTML = '';
    AD_SLIDES.forEach((s, i) => {
        const div = document.createElement('div');
        div.className = 'ad-slide' + (i === 0 ? ' active' : '');

        let buttonsHTML = '';
        if (s.buttons && s.buttons.length) {
            buttonsHTML = '<div class="ad-slide-actions">' +
                s.buttons.map(b =>
                    `<a class="ad-slide-btn ${b.cls}" href="${b.url}" target="_blank" rel="noopener noreferrer">${b.text}</a>`
                ).join('') +
            '</div>';
        }

        div.innerHTML = `
            <div class="ad-slide-icon">${s.icon}</div>
            <div class="ad-slide-title">${s.title}</div>
            <p class="ad-slide-text">${s.text}</p>
            <span class="ad-slide-tag">${s.tag}</span>
            ${buttonsHTML}
        `;
        container.appendChild(div);
    });
}

function buildAdDots() {
    const container = $('adDots');
    container.innerHTML = '';
    AD_SLIDES.forEach((_, i) => {
        const dot = document.createElement('div');
        dot.className = 'ad-dot' + (i === 0 ? ' active' : '');
        container.appendChild(dot);
    });
}

function showAdSlide(index) {
    document.querySelectorAll('.ad-slide').forEach((el, i) => {
        el.classList.toggle('active', i === index);
    });
    document.querySelectorAll('.ad-dot').forEach((el, i) => {
        el.classList.remove('active', 'done');
        if (i === index) el.classList.add('active');
        else if (i < index) el.classList.add('done');
    });
}

function openAdModal() {
    haptic(15);
    buildAdSlides();
    buildAdDots();
    $('adTimerFill').style.width = '0%';
    $('adStatus').innerHTML = `Смотрите рекламу <strong>${AD_DURATION} секунд</strong>`;
    $('adClaimBtn').disabled = true;
    $('adClaimBtn').textContent = 'Получить ' + AD_REWARD + ' 🪙';
    $('adModal').classList.add('show');

    adSecondsLeft = AD_DURATION;
    adSlideIndex = 0;
    showAdSlide(0);

    clearInterval(adSlideInterval);
    adSlideInterval = setInterval(() => {
        adSlideIndex = (adSlideIndex + 1) % AD_SLIDES.length;
        showAdSlide(adSlideIndex);
        haptic(5);
    }, 3000);

    clearInterval(adTimer);
    adTimer = setInterval(() => {
        adSecondsLeft--;
        const pct = ((AD_DURATION - adSecondsLeft) / AD_DURATION) * 100;
        $('adTimerFill').style.width = pct + '%';
        if (adSecondsLeft > 0) {
            $('adStatus').innerHTML = `Осталось: <strong>${adSecondsLeft} сек</strong>`;
        } else {
            clearInterval(adTimer);
            clearInterval(adSlideInterval);
            $('adStatus').innerHTML = `✅ Готово! Забирайте награду`;
            $('adClaimBtn').disabled = false;
            haptic(30);
        }
    }, 1000);
}

function closeAdModal() {
    clearInterval(adTimer);
    clearInterval(adSlideInterval);
    $('adModal').classList.remove('show');
}

function claimAdReward() {
    if (adSecondsLeft > 0) return;
    haptic(40);
    addCoins(AD_REWARD, false);
    toast('+' + AD_REWARD + ' 🪙 за просмотр рекламы!', 'ad', 2000);
    closeAdModal();
}

/* ============================================================
   РЕКОРДЫ
============================================================ */
function tryUpdateRecord(key, value, uiEl, boxEl, format = 'int', lowerIsBetter = false) {
    if (lowerIsBetter && value <= 0) return false;
    const cur = format === 'float' ? getBestFloat(key) : getBest(key);
    const isRecord = lowerIsBetter ? (cur === 0 || value < cur) : (value > cur);
    if (isRecord) {
        localStorage.setItem(key, value);
        if (uiEl) { uiEl.textContent = format === 'float' ? value.toFixed(0) : value; bump(uiEl); }
        if (boxEl) { boxEl.classList.add('record'); setTimeout(() => boxEl.classList.remove('record'), 1200); }
        return true;
    }
    return false;
}

function updateBestScoresUI() {
    const keys = {
        'snake_best': ['snake-best', 'menu-snake-best'],
        '2048_best': ['best2048', 'menu-2048-best'],
        'match3_best': ['match3-best', 'menu-match3-best'],
        'puzzle_best': ['puzzle-best', 'menu-puzzle-best'],
        'arknoid_best': ['arknoid-best', 'menu-arknoid-best'],
        'shooter_best': ['shooter-best', null],
        'tetris_best': ['tetris-best', null],
        'tower_best': ['tower-best', null]
    };
    for (const k in keys) {
        const [el1, el2] = keys[k];
        const v = getBest(k);
        if (el1 && $(el1)) $(el1).textContent = v;
        if (el2 && $(el2)) $(el2).textContent = v;
    }
    const rBest = getBest('reaction_best');
    const rTxt = rBest > 0 ? rBest + ' мс' : '—';
    $('reaction-best').textContent = rTxt;
    $('menu-reaction-best').textContent = rTxt;
    updateCoinsUI();
    updatePremiumTags();
}

/* ============================================================
   НАВИГАЦИЯ
============================================================ */
let currentScreen = 'menu-screen';
const screenHooks = {
    'snake-screen':    { enter: startSnake,     exit: stopSnake },
    'game2048-screen': { enter: start2048,      exit: null },
    'match3-screen':   { enter: startMatch3,    exit: stopMatch3 },
    'reaction-screen': { enter: startReaction,  exit: stopReaction },
    'puzzle-screen':   { enter: startPuzzle,    exit: stopPuzzle },
    'arknoid-screen':  { enter: startArknoid,   exit: stopArknoid },
    'shooter-screen':  { enter: startShooter,   exit: stopShooter },
    'tetris-screen':   { enter: startTetris,    exit: stopTetris },
    'tower-screen':    { enter: startTower,     exit: stopTower }
};

function openGame(screenId) {
    haptic(15);
    const hook = screenHooks[screenId];
    if (hook && hook.enter) hook.enter();
    switchScreen(screenId);
}
function closeGame(screenId) {
    haptic(10);
    const hook = screenHooks[screenId];
    if (hook && hook.exit) hook.exit();
    switchScreen('menu-screen');
}
function switchScreen(screenId) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    $(screenId).classList.add('active');
    currentScreen = screenId;
    updateBestScoresUI();
}

/* ============================================================
   ЗМЕЙКА
============================================================ */
const sCanvas = $('snakeCanvas');
const sCtx = sCanvas.getContext('2d');
const sScoreEl = $('snake-score');
const sBestEl = $('snake-best');
const sBestBox = $('snake-best-box');
const sGrid = 16;
const sTiles = sCanvas.width / sGrid;
let snake, food, sDx, sDy, sScore, snakeInterval, snakeRunning = false;

function startSnake() {
    snake = [{x: 8, y: 8}]; food = {x: 4, y: 4};
    sDx = 1; sDy = 0; sScore = 0;
    sScoreEl.textContent = '0';
    sBestEl.textContent = getBest('snake_best');
    clearInterval(snakeInterval);
    snakeRunning = true;
    spawnFood();
    snakeInterval = setInterval(updateSnake, 130);
    drawSnake();
}
function stopSnake() { clearInterval(snakeInterval); snakeRunning = false; }

function updateSnake() {
    if (!snakeRunning) return;
    const head = {x: snake[0].x + sDx, y: snake[0].y + sDy};
    const hitWall = head.x < 0 || head.x >= sTiles || head.y < 0 || head.y >= sTiles;
    const hitSelf = snake.some((p, i) => i !== 0 && p.x === head.x && p.y === head.y);
    if (hitWall || hitSelf) { gameOverSnake(); return; }
    snake.unshift(head);
    if (head.x === food.x && head.y === food.y) {
        sScore += 10;
        sScoreEl.textContent = sScore;
        bump(sScoreEl); haptic(20);
        addCoins(1, false);
        if (tryUpdateRecord('snake_best', sScore, sBestEl, sBestBox))
            toast('🏆 Новый рекорд: ' + sScore, 'record', 1400);
        spawnFood();
    } else snake.pop();
    drawSnake();
}
function drawSnake() {
    sCtx.fillStyle = '#060a06'; sCtx.fillRect(0, 0, sCanvas.width, sCanvas.height);
    sCtx.strokeStyle = 'rgba(76,175,80,0.05)';
    for (let i = 1; i < sTiles; i++) {
        sCtx.beginPath(); sCtx.moveTo(i*sGrid, 0); sCtx.lineTo(i*sGrid, sCanvas.height); sCtx.stroke();
        sCtx.beginPath(); sCtx.moveTo(0, i*sGrid); sCtx.lineTo(sCanvas.width, i*sGrid); sCtx.stroke();
    }
    const t = performance.now() / 300;
    const pulse = 1 + Math.sin(t) * 0.12;
    const fx = food.x * sGrid + sGrid/2, fy = food.y * sGrid + sGrid/2;
    const fr = (sGrid/2 - 2) * pulse;
    const grad = sCtx.createRadialGradient(fx, fy, 0, fx, fy, fr * 1.6);
    grad.addColorStop(0, '#ff8a65'); grad.addColorStop(1, 'rgba(255,87,34,0)');
    sCtx.fillStyle = grad;
    sCtx.beginPath(); sCtx.arc(fx, fy, fr * 1.6, 0, Math.PI*2); sCtx.fill();
    sCtx.fillStyle = '#ff5722';
    sCtx.beginPath(); sCtx.arc(fx, fy, fr, 0, Math.PI*2); sCtx.fill();
    snake.forEach((p, i) => {
        const x = p.x * sGrid, y = p.y * sGrid;
        const isHead = i === 0;
        const pad = isHead ? 1 : 2;
        sCtx.fillStyle = isHead ? '#a5d6a7' : `hsl(122, 39%, ${45 - Math.min(i*1.5, 20)}%)`;
        roundRect(sCtx, x + pad, y + pad, sGrid - pad*2, sGrid - pad*2, 4);
        sCtx.fill();
    });
}
function spawnFood() {
    do { food.x = Math.floor(Math.random() * sTiles); food.y = Math.floor(Math.random() * sTiles); }
    while (snake.some(p => p.x === food.x && p.y === food.y));
}
function gameOverSnake() {
    stopSnake(); haptic(80);
    toast('Игра окончена. Очки: ' + sScore, 'info', 1600);
    setTimeout(() => { if (currentScreen === 'snake-screen') startSnake(); }, 900);
}
function setSnakeDir(nx, ny) {
    if (!snakeRunning) return;
    if (nx === -sDx && sDx !== 0) return;
    if (ny === -sDy && sDy !== 0) return;
    sDx = nx; sDy = ny; haptic(8);
}

/* ============================================================
   2048
============================================================ */
const canvas2048 = $('canvas2048');
const ctx2048 = canvas2048.getContext('2d');
const score2048El = $('score2048');
const best2048El = $('best2048');
const best2048Box = $('best2048-box');
const size2048 = 4;
const cellW = canvas2048.width / size2048;
const tileColors = {2:'#eee4da',4:'#ede0c8',8:'#f2b179',16:'#f59563',32:'#f67c5f',64:'#f65e3b',128:'#edcf72',256:'#edcc61',512:'#edc850',1024:'#edc53f',2048:'#edc22e'};
let board2048 = [], score2048 = 0, board2048Anim = {}, lastCoinMilestone = 0;

function start2048() {
    board2048 = Array.from({length: size2048}, () => Array(size2048).fill(0));
    score2048 = 0; lastCoinMilestone = 0;
    score2048El.textContent = '0';
    best2048El.textContent = getBest('2048_best');
    addTile2048(); addTile2048();
    drawBoard2048();
}
function addTile2048() {
    const empty = [];
    for (let r = 0; r < size2048; r++)
        for (let c = 0; c < size2048; c++)
            if (board2048[r][c] === 0) empty.push({r, c});
    if (!empty.length) return;
    const cell = empty[Math.floor(Math.random() * empty.length)];
    board2048[cell.r][cell.c] = Math.random() < 0.9 ? 2 : 4;
    board2048Anim[cell.r + ',' + cell.c] = performance.now();
}
function drawBoard2048() {
    ctx2048.fillStyle = '#1a1610';
    ctx2048.fillRect(0, 0, canvas2048.width, canvas2048.height);
    for (let r = 0; r < size2048; r++) {
        for (let c = 0; c < size2048; c++) {
            const val = board2048[r][c];
            const x = c * cellW + 5, y = r * cellW + 5, w = cellW - 10;
            ctx2048.fillStyle = 'rgba(255,255,255,0.04)';
            roundRect(ctx2048, x, y, w, w, 8);
            ctx2048.fill();
            if (val > 0) {
                const key = r + ',' + c;
                const born = board2048Anim[key] || 0;
                const age = (performance.now() - born) / 150;
                const scale = age < 1 ? 0.5 + age * 0.5 : 1;
                ctx2048.save();
                ctx2048.translate(x + w/2, y + w/2);
                ctx2048.scale(scale, scale);
                ctx2048.translate(-(x + w/2), -(y + w/2));
                ctx2048.fillStyle = tileColors[val] || '#3c3a32';
                roundRect(ctx2048, x, y, w, w, 8);
                ctx2048.fill();
                ctx2048.fillStyle = (val === 2 || val === 4) ? '#776e65' : '#f9f6f2';
                const fontSize = val > 1000 ? 18 : val > 100 ? 22 : 28;
                ctx2048.font = `bold ${fontSize}px system-ui, sans-serif`;
                ctx2048.textAlign = 'center';
                ctx2048.textBaseline = 'middle';
                ctx2048.fillText(val, x + w/2, y + w/2 + 1);
                ctx2048.restore();
            }
        }
    }
}
function move2048(dir) {
    if (currentScreen !== 'game2048-screen') return;
    haptic(8);
    let moved = false;
    if (dir === 'right')      { reverseBoard(); moved = slideLeft(); reverseBoard(); }
    else if (dir === 'left')  { moved = slideLeft(); }
    else if (dir === 'up')    { transposeBoard(); moved = slideLeft(); transposeBoard(); }
    else if (dir === 'down')  { transposeBoard(); reverseBoard(); moved = slideLeft(); reverseBoard(); transposeBoard(); }
    if (moved) {
        addTile2048();
        score2048El.textContent = score2048;
        bump(score2048El);
        const milestone = Math.floor(score2048 / 50);
        if (milestone > lastCoinMilestone) { addCoins(milestone - lastCoinMilestone, false); lastCoinMilestone = milestone; }
        if (tryUpdateRecord('2048_best', score2048, best2048El, best2048Box))
            toast('🏆 Новый рекорд: ' + score2048, 'record', 1400);
        drawBoard2048();
        if (isGameOver2048()) {
            haptic(80);
            toast('Игра окончена. Счет: ' + score2048, 'info', 1800);
            setTimeout(() => { if (currentScreen === 'game2048-screen') start2048(); }, 1100);
        }
    }
}
function slideLeft() {
    let moved = false;
    for (let r = 0; r < size2048; r++) {
        let row = board2048[r].filter(v => v !== 0);
        for (let i = 0; i < row.length - 1; i++)
            if (row[i] === row[i + 1]) { row[i] *= 2; score2048 += row[i]; row.splice(i + 1, 1); moved = true; }
        while (row.length < size2048) row.push(0);
        if (JSON.stringify(board2048[r]) !== JSON.stringify(row)) moved = true;
        board2048[r] = row;
    }
    return moved;
}
function reverseBoard() { board2048.forEach(r => r.reverse()); }
function transposeBoard() { board2048 = board2048.map((_, i) => board2048.map(row => row[i])); }
function isGameOver2048() {
    for (let r = 0; r < size2048; r++)
        for (let c = 0; c < size2048; c++) {
            if (board2048[r][c] === 0) return false;
            if (c < size2048 - 1 && board2048[r][c] === board2048[r][c+1]) return false;
            if (r < size2048 - 1 && board2048[r][c] === board2048[r+1][c]) return false;
        }
    return true;
}

/* ============================================================
   3 В РЯД
============================================================ */
const mCanvas = $('match3Canvas');
const mCtx = mCanvas.getContext('2d');
const mScoreEl = $('match3-score');
const mBestEl = $('match3-best');
const mBestBox = $('match3-best-box');
const mSize = 6;
const mCell = mCanvas.width / mSize;
const gemColors = ['#e91e63', '#2196f3', '#4caf50', '#ffeb3b', '#9c27b0', '#ff9800'];
let mBoard = [], mScore = 0, selR = 0, selC = 0, mInterval, mRunning = false;
let mParticles = [], mFirstTap = null, mCoinMilestoneM3 = 0;

function startMatch3() {
    mScore = 0; mScoreEl.textContent = '0';
    mBestEl.textContent = getBest('match3_best');
    selR = 0; selC = 0; mFirstTap = null; mCoinMilestoneM3 = 0; mParticles = [];
    initMatch3Board();
    clearInterval(mInterval);
    mRunning = true;
    mInterval = setInterval(drawMatch3, 60);
    drawMatch3();
}
function stopMatch3() { clearInterval(mInterval); mRunning = false; }
function initMatch3Board() {
    for (let r = 0; r < mSize; r++) {
        mBoard[r] = [];
        for (let c = 0; c < mSize; c++) {
            let idx, guard = 0;
            do { idx = Math.floor(Math.random() * gemColors.length); guard++; }
            while (guard < 50 && (
                (c >= 2 && mBoard[r][c-1] === idx && mBoard[r][c-2] === idx) ||
                (r >= 2 && mBoard[r-1][c] === idx && mBoard[r-2][c] === idx)
            ));
            mBoard[r][c] = idx;
        }
    }
    if (checkMatchesMatch3()) initMatch3Board();
}
let blinkPhase = 0;
function drawMatch3() {
    if (!mRunning) return;
    blinkPhase += 0.08;
    mCtx.fillStyle = '#100812';
    mCtx.fillRect(0, 0, mCanvas.width, mCanvas.height);
    mCtx.strokeStyle = 'rgba(156,39,176,0.08)';
    for (let i = 1; i < mSize; i++) {
        mCtx.beginPath(); mCtx.moveTo(i * mCell, 0); mCtx.lineTo(i * mCell, mCanvas.height); mCtx.stroke();
        mCtx.beginPath(); mCtx.moveTo(0, i * mCell); mCtx.lineTo(mCanvas.width, i * mCell); mCtx.stroke();
    }
    for (let r = 0; r < mSize; r++) {
        for (let c = 0; c < mSize; c++) {
            const color = gemColors[mBoard[r][c]];
            const cx = c * mCell + mCell / 2, cy = r * mCell + mCell / 2;
            const radius = mCell / 2 - 7;
            const glow = mCtx.createRadialGradient(cx, cy, 0, cx, cy, radius * 1.8);
            glow.addColorStop(0, color + '55');
            glow.addColorStop(1, 'rgba(0,0,0,0)');
            mCtx.fillStyle = glow;
            mCtx.beginPath(); mCtx.arc(cx, cy, radius * 1.8, 0, Math.PI * 2); mCtx.fill();
            mCtx.fillStyle = color;
            mCtx.beginPath(); mCtx.arc(cx, cy, radius, 0, Math.PI * 2); mCtx.fill();
            mCtx.fillStyle = 'rgba(255,255,255,0.35)';
            mCtx.beginPath();
            mCtx.arc(cx - radius * 0.3, cy - radius * 0.3, radius * 0.35, 0, Math.PI * 2);
            mCtx.fill();
            if (r === selR && c === selC) {
                const a = 0.6 + Math.sin(blinkPhase * 6) * 0.4;
                mCtx.strokeStyle = `rgba(255,255,255,${a})`;
                mCtx.lineWidth = 3;
                mCtx.beginPath(); mCtx.arc(cx, cy, radius + 3, 0, Math.PI * 2); mCtx.stroke();
            }
        }
    }
    for (let i = mParticles.length - 1; i >= 0; i--) {
        const p = mParticles[i];
        p.x += p.vx; p.y += p.vy; p.vy += 0.15; p.life -= 0.02;
        if (p.life <= 0) { mParticles.splice(i, 1); continue; }
        mCtx.globalAlpha = p.life;
        mCtx.fillStyle = p.color;
        mCtx.beginPath(); mCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2); mCtx.fill();
    }
    mCtx.globalAlpha = 1;
}
function trySwap(r1, c1, r2, c2) {
    if (Math.abs(r1 - r2) + Math.abs(c1 - c2) !== 1) return false;
    [mBoard[r1][c1], mBoard[r2][c2]] = [mBoard[r2][c2], mBoard[r1][c1]];
    if (!checkMatchesMatch3()) {
        [mBoard[r1][c1], mBoard[r2][c2]] = [mBoard[r2][c2], mBoard[r1][c1]];
        return false;
    }
    return true;
}
function moveMatch3(dir) {
    if (!mRunning || currentScreen !== 'match3-screen') return;
    haptic(10);
    let nr = selR, nc = selC;
    if (dir === 'up') nr--; else if (dir === 'down') nr++;
    else if (dir === 'left') nc--; else if (dir === 'right') nc++;
    if (nr < 0 || nr >= mSize || nc < 0 || nc >= mSize) return;
    if (!trySwap(selR, selC, nr, nc)) { toast('Нет линии из 3', 'info', 900); return; }
    selR = nr; selC = nc;
    processMatches();
}
function match3Tap(r, c) {
    if (!mRunning) return;
    if (r < 0 || r >= mSize || c < 0 || c >= mSize) return;
    if (mFirstTap === null) { selR = r; selC = c; mFirstTap = {r, c}; haptic(8); return; }
    if (mFirstTap.r === r && mFirstTap.c === c) { mFirstTap = null; haptic(8); return; }
    if (trySwap(mFirstTap.r, mFirstTap.c, r, c)) {
        selR = r; selC = c; mFirstTap = null; haptic(15);
        processMatches();
    } else { selR = r; selC = c; mFirstTap = {r, c}; haptic(8); }
}
function checkMatchesMatch3() {
    for (let r = 0; r < mSize; r++)
        for (let c = 0; c < mSize; c++) {
            if (c < mSize - 2 && mBoard[r][c] === mBoard[r][c+1] && mBoard[r][c] === mBoard[r][c+2]) return true;
            if (r < mSize - 2 && mBoard[r][c] === mBoard[r+1][c] && mBoard[r][c] === mBoard[r+2][c]) return true;
        }
    return false;
}
function processMatches() {
    const toRemove = Array.from({length: mSize}, () => Array(mSize).fill(false));
    for (let r = 0; r < mSize; r++)
        for (let c = 0; c < mSize; c++) {
            if (c < mSize - 2 && mBoard[r][c] === mBoard[r][c+1] && mBoard[r][c] === mBoard[r][c+2])
                toRemove[r][c] = toRemove[r][c+1] = toRemove[r][c+2] = true;
            if (r < mSize - 2 && mBoard[r][c] === mBoard[r+1][c] && mBoard[r][c] === mBoard[r+2][c])
                toRemove[r][c] = toRemove[r+1][c] = toRemove[r+2][c] = true;
        }
    let removed = 0;
    for (let r = 0; r < mSize; r++)
        for (let c = 0; c < mSize; c++)
            if (toRemove[r][c]) removed++;
    if (removed === 0) return;
    for (let r = 0; r < mSize; r++)
        for (let c = 0; c < mSize; c++)
            if (toRemove[r][c]) {
                const color = gemColors[mBoard[r][c]];
                const cx = c * mCell + mCell / 2, cy = r * mCell + mCell / 2;
                for (let k = 0; k < 4; k++)
                    mParticles.push({
                        x: cx, y: cy,
                        vx: (Math.random() - 0.5) * 4,
                        vy: (Math.random() - 0.5) * 4 - 1,
                        life: 1, size: 2 + Math.random() * 2, color
                    });
            }
    for (let c = 0; c < mSize; c++) {
        let writeRow = mSize - 1;
        for (let r = mSize - 1; r >= 0; r--)
            if (!toRemove[r][c]) { mBoard[writeRow][c] = mBoard[r][c]; writeRow--; }
        for (let r = writeRow; r >= 0; r--)
            mBoard[r][c] = Math.floor(Math.random() * gemColors.length);
    }
    mScore += removed * 10;
    mScoreEl.textContent = mScore;
    bump(mScoreEl);
    haptic(20);
    const milestone = Math.floor(mScore / 30);
    if (milestone > mCoinMilestoneM3) { addCoins(milestone - mCoinMilestoneM3, false); mCoinMilestoneM3 = milestone; }
    if (tryUpdateRecord('match3_best', mScore, mBestEl, mBestBox))
        toast('🏆 Новый рекорд: ' + mScore, 'record', 1400);
    setTimeout(() => { if (mRunning) processMatches(); }, 280);
}
function match3Click(e) {
    if (!mRunning) return;
    const rect = mCanvas.getBoundingClientRect();
    const x = (e.clientX - rect.left) * (mCanvas.width / rect.width);
    const y = (e.clientY - rect.top) * (mCanvas.height / rect.height);
    match3Tap(Math.floor(y / mCell), Math.floor(x / mCell));
}

/* ============================================================
   РЕАКЦИЯ
============================================================ */
const rCanvas = $('reactionCanvas');
const rCtx = rCanvas.getContext('2d');
const rTimeEl = $('reaction-time');
const rBestEl = $('reaction-best');
const rBestBox = $('reaction-best-box');
const rBtn = $('reactionBtn');
let rState = 'idle', rStartTime = 0, rTimeout = null;

function startReaction() {
    rState = 'idle';
    rTimeEl.textContent = '—';
    rBestEl.textContent = getBest('reaction_best') > 0 ? getBest('reaction_best') + ' мс' : '—';
    rBtn.textContent = 'СТАРТ';
    drawReactionIdle();
    clearTimeout(rTimeout);
}
function stopReaction() { clearTimeout(rTimeout); rState = 'idle'; }
function drawReactionIdle() {
    rCtx.fillStyle = '#041014';
    rCtx.fillRect(0, 0, rCanvas.width, rCanvas.height);
    rCtx.fillStyle = 'rgba(0,188,212,0.4)';
    rCtx.font = 'bold 22px system-ui, sans-serif';
    rCtx.textAlign = 'center';
    rCtx.textBaseline = 'middle';
    rCtx.fillText('НАЖМИТЕ СТАРТ', rCanvas.width/2, rCanvas.height/2);
}
function drawReactionState(color, text) {
    rCtx.fillStyle = color;
    rCtx.fillRect(0, 0, rCanvas.width, rCanvas.height);
    rCtx.fillStyle = '#ffffff';
    rCtx.font = 'bold 32px system-ui, sans-serif';
    rCtx.textAlign = 'center';
    rCtx.textBaseline = 'middle';
    rCtx.fillText(text, rCanvas.width/2, rCanvas.height/2);
}
function reactionTap(e) {
    if (e) e.preventDefault();
    haptic(15);
    if (rState === 'idle') {
        rState = 'waiting';
        rBtn.textContent = 'ЖДИТЕ...';
        drawReactionState('#b71c1c', 'ЖДИТЕ...');
        rTimeout = setTimeout(() => {
            if (rState !== 'waiting') return;
            rState = 'ready';
            rStartTime = performance.now();
            drawReactionState('#1b5e20', 'ЖМИ!');
            rBtn.textContent = 'ЖМИ!';
            haptic(40);
        }, 1500 + Math.random() * 2500);
    } else if (rState === 'waiting') {
        clearTimeout(rTimeout);
        rState = 'idle';
        drawReactionState('#4a148c', 'РАНО!');
        toast('Слишком рано!', 'info', 1200);
        rBtn.textContent = 'ПОПРОБОВАТЬ СНОВА';
        haptic(60);
    } else if (rState === 'ready') {
        const t = Math.round(performance.now() - rStartTime);
        rState = 'done';
        rTimeEl.textContent = t + ' мс';
        bump(rTimeEl);
        drawReactionState('#0d47a1', t + ' мс');
        rBtn.textContent = 'ЕЩЁ РАЗ';
        haptic(30);
        const coins = t < 250 ? 5 : t < 400 ? 3 : t < 600 ? 1 : 0;
        if (coins > 0) addCoins(coins, false);
        if (tryUpdateRecord('reaction_best', t, rBestEl, rBestBox, 'int', true))
            toast('🏆 Новый рекорд: ' + t + ' мс!', 'record', 1600);
        else if (coins > 0) toast('+' + coins + ' 🪙', 'coin', 1000);
    } else if (rState === 'done') startReaction();
}

/* ============================================================
   ПЯТНАШКИ
============================================================ */
const pCanvas = $('puzzleCanvas');
const pCtx = pCanvas.getContext('2d');
const pMovesEl = $('puzzle-moves');
const pBestEl = $('puzzle-best');
const pBestBox = $('puzzle-best-box');
const pSize = 3, pTotal = pSize * pSize, pCell = pCanvas.width / pSize;
let pBoard = [], pMoves = 0, pSolved = false;

function startPuzzle() {
    pMoves = 0; pMovesEl.textContent = '0';
    pBestEl.textContent = getBest('puzzle_best') || 0;
    pSolved = false;
    shufflePuzzle();
    drawPuzzle();
}
function stopPuzzle() {}
function shufflePuzzle() {
    haptic(15);
    pBoard = [];
    for (let i = 0; i < pTotal; i++) pBoard.push(i);
    let emptyIdx = pTotal - 1;
    const steps = 80 + Math.floor(Math.random() * 40);
    for (let s = 0; s < steps; s++) {
        const neighbors = getNeighbors(emptyIdx);
        const pick = neighbors[Math.floor(Math.random() * neighbors.length)];
        [pBoard[emptyIdx], pBoard[pick]] = [pBoard[pick], pBoard[emptyIdx]];
        emptyIdx = pick;
    }
    pMoves = 0; pMovesEl.textContent = '0'; pSolved = false;
    drawPuzzle();
}
function getNeighbors(idx) {
    const r = Math.floor(idx / pSize), c = idx % pSize;
    const out = [];
    if (r > 0) out.push(idx - pSize);
    if (r < pSize - 1) out.push(idx + pSize);
    if (c > 0) out.push(idx - 1);
    if (c < pSize - 1) out.push(idx + 1);
    return out;
}
function drawPuzzle() {
    pCtx.fillStyle = '#140606';
    pCtx.fillRect(0, 0, pCanvas.width, pCanvas.height);
    const pad = 5, w = pCell - pad * 2;
    for (let i = 0; i < pTotal; i++) {
        const val = pBoard[i];
        const r = Math.floor(i / pSize), c = i % pSize;
        const x = c * pCell + pad, y = r * pCell + pad;
        if (val === 0) {
            pCtx.fillStyle = 'rgba(255,255,255,0.03)';
            roundRect(pCtx, x, y, w, w, 10); pCtx.fill();
            continue;
        }
        const isCorrect = val === i + 1;
        const grad = pCtx.createLinearGradient(x, y, x + w, y + w);
        if (isCorrect) { grad.addColorStop(0, '#66bb6a'); grad.addColorStop(1, '#2e7d32'); }
        else { grad.addColorStop(0, '#ef5350'); grad.addColorStop(1, '#b71c1c'); }
        pCtx.fillStyle = grad;
        roundRect(pCtx, x, y, w, w, 10); pCtx.fill();
        pCtx.fillStyle = 'rgba(255,255,255,0.15)';
        roundRect(pCtx, x + 4, y + 4, w - 8, w * 0.35, 6); pCtx.fill();
        pCtx.fillStyle = '#fff';
        pCtx.font = 'bold 32px system-ui, sans-serif';
        pCtx.textAlign = 'center';
        pCtx.textBaseline = 'middle';
        pCtx.fillText(val, x + w/2, y + w/2 + 2);
    }
}
function puzzleClick(e) {
    if (pSolved) return;
    const rect = pCanvas.getBoundingClientRect();
    const x = (e.clientX - rect.left) * (pCanvas.width / rect.width);
    const y = (e.clientY - rect.top) * (pCanvas.height / rect.height);
    const c = Math.floor(x / pCell), r = Math.floor(y / pCell);
    if (r < 0 || r >= pSize || c < 0 || c >= pSize) return;
    const idx = r * pSize + c;
    const emptyIdx = pBoard.indexOf(0);
    if (!getNeighbors(emptyIdx).includes(idx)) return;
    [pBoard[emptyIdx], pBoard[idx]] = [pBoard[idx], pBoard[emptyIdx]];
    pMoves++;
    pMovesEl.textContent = pMoves;
    bump(pMovesEl);
    haptic(10);
    drawPuzzle();
    if (isPuzzleSolved()) {
        pSolved = true;
        haptic(60);
        const coins = pMoves <= 30 ? 20 : pMoves <= 60 ? 10 : 5;
        addCoins(coins, false);
        if (tryUpdateRecord('puzzle_best', pMoves, pBestEl, pBestBox, 'int', true))
            toast('🏆 Рекорд: ' + pMoves + ' ходов! +' + coins + '🪙', 'record', 2000);
        else toast('Собрано за ' + pMoves + ' ходов! +' + coins + '🪙', 'success', 1800);
    }
}
function isPuzzleSolved() {
    for (let i = 0; i < pTotal - 1; i++)
        if (pBoard[i] !== i + 1) return false;
    return pBoard[pTotal - 1] === 0;
}

/* ============================================================
   АРКАНОИД
============================================================ */
const arkCanvas = $('arknoidCanvas');
const arkCtx = arkCanvas.getContext('2d');
const arkScoreEl = $('arknoid-score');
const arkBestEl = $('arknoid-best');
const arkBestBox = $('arknoid-best-box');
const ARK_W = arkCanvas.width, ARK_H = arkCanvas.height;
const ARK_PADDLE_W = 70, ARK_PADDLE_H = 12, ARK_PADDLE_Y = ARK_H - 30;
const ARK_BALL_R = 6, ARK_BRICK_ROWS = 5, ARK_BRICK_COLS = 8;
const ARK_BRICK_H = 18, ARK_BRICK_GAP = 4, ARK_BRICK_TOP = 40, ARK_BRICK_SIDE = 8;
let arkRunning = false, arkFrameId = null, arkLastTime = 0;
let arkPaddleX = ARK_W / 2, arkDir = 0;
let arkBall = {x: 0, y: 0, vx: 0, vy: 0, launched: false};
let arkBricks = [], arkScore = 0, arkLives = 3, arkPaused = false, arkCoinMilestone = 0, arkParticles = [];
const ARK_BRICK_COLORS = ['#e53935', '#fb8c00', '#fdd835', '#43a047', '#1e88e5'];

function startArknoid() {
    arkPaddleX = ARK_W / 2; arkDir = 0;
    arkScore = 0; arkLives = 3; arkPaused = false; arkCoinMilestone = 0;
    arkScoreEl.textContent = '0';
    arkBestEl.textContent = getBest('arknoid_best');
    arkParticles = [];
    resetArknoidBall();
    buildArknoidBricks();
    arkRunning = true;
    arkLastTime = performance.now();
    if (arkFrameId) cancelAnimationFrame(arkFrameId);
    arkFrameId = requestAnimationFrame(arknoidLoop);
}
function stopArknoid() { arkRunning = false; if (arkFrameId) cancelAnimationFrame(arkFrameId); arkFrameId = null; }
function resetArknoidBall() {
    arkBall.x = arkPaddleX;
    arkBall.y = ARK_PADDLE_Y - ARK_BALL_R - 2;
    arkBall.vx = 0; arkBall.vy = 0; arkBall.launched = false;
}
function launchArknoidBall() {
    if (arkBall.launched) return;
    const angle = (-60 + Math.random() * 120) * Math.PI / 180;
    const speed = 4.2;
    arkBall.vx = Math.sin(angle) * speed;
    arkBall.vy = -Math.abs(Math.cos(angle) * speed);
    if (Math.abs(arkBall.vx) < 1.5) arkBall.vx = arkBall.vx > 0 ? 1.5 : -1.5;
    arkBall.launched = true;
    haptic(15);
}
function buildArknoidBricks() {
    arkBricks = [];
    const totalW = ARK_W - ARK_BRICK_SIDE * 2;
    const brickW = (totalW - ARK_BRICK_GAP * (ARK_BRICK_COLS - 1)) / ARK_BRICK_COLS;
    for (let r = 0; r < ARK_BRICK_ROWS; r++)
        for (let c = 0; c < ARK_BRICK_COLS; c++)
            arkBricks.push({
                x: ARK_BRICK_SIDE + c * (brickW + ARK_BRICK_GAP),
                y: ARK_BRICK_TOP + r * (ARK_BRICK_H + ARK_BRICK_GAP),
                w: brickW, h: ARK_BRICK_H,
                color: ARK_BRICK_COLORS[r % ARK_BRICK_COLORS.length],
                points: (ARK_BRICK_ROWS - r) * 5
            });
}
function arknoidControl(action) {
    if (!arkRunning) return;
    if (action === 'left') { arkDir = -1; haptic(6); }
    else if (action === 'right') { arkDir = 1; haptic(6); }
    else if (action === 'fire') { launchArknoidBall(); haptic(10); }
    else if (action === 'pause') { arkPaused = !arkPaused; haptic(10); }
}
function arknoidLoop(t) {
    if (!arkRunning) return;
    const dt = Math.min(40, t - arkLastTime);
    arkLastTime = t;
    if (!arkPaused) updateArknoid(dt);
    drawArknoid();
    arkFrameId = requestAnimationFrame(arknoidLoop);
}
function updateArknoid(dt) {
    arkPaddleX += arkDir * 0.45 * dt;
    arkPaddleX = Math.max(ARK_PADDLE_W/2, Math.min(ARK_W - ARK_PADDLE_W/2, arkPaddleX));
    if (!arkBall.launched) {
        arkBall.x = arkPaddleX;
        arkBall.y = ARK_PADDLE_Y - ARK_BALL_R - 2;
    } else {
        arkBall.x += arkBall.vx * dt * 0.06;
        arkBall.y += arkBall.vy * dt * 0.06;
        if (arkBall.x < ARK_BALL_R) { arkBall.x = ARK_BALL_R; arkBall.vx = Math.abs(arkBall.vx); }
        if (arkBall.x > ARK_W - ARK_BALL_R) { arkBall.x = ARK_W - ARK_BALL_R; arkBall.vx = -Math.abs(arkBall.vx); }
        if (arkBall.y < ARK_BALL_R) { arkBall.y = ARK_BALL_R; arkBall.vy = Math.abs(arkBall.vy); }
        if (arkBall.y + ARK_BALL_R > ARK_PADDLE_Y && arkBall.y - ARK_BALL_R < ARK_PADDLE_Y + ARK_PADDLE_H &&
            arkBall.x > arkPaddleX - ARK_PADDLE_W/2 - ARK_BALL_R && arkBall.x < arkPaddleX + ARK_PADDLE_W/2 + ARK_BALL_R && arkBall.vy > 0) {
            arkBall.y = ARK_PADDLE_Y - ARK_BALL_R;
            const rel = (arkBall.x - arkPaddleX) / (ARK_PADDLE_W/2);
            const angle = rel * Math.PI / 3;
            const speed = Math.min(6.5, Math.hypot(arkBall.vx, arkBall.vy) + 0.05);
            arkBall.vx = Math.sin(angle) * speed;
            arkBall.vy = -Math.abs(Math.cos(angle) * speed);
            haptic(8);
        }
        for (let i = arkBricks.length - 1; i >= 0; i--) {
            const b = arkBricks[i];
            if (arkBall.x + ARK_BALL_R > b.x && arkBall.x - ARK_BALL_R < b.x + b.w &&
                arkBall.y + ARK_BALL_R > b.y && arkBall.y - ARK_BALL_R < b.y + b.h) {
                const oL = (arkBall.x + ARK_BALL_R) - b.x, oR = (b.x + b.w) - (arkBall.x - ARK_BALL_R);
                const oT = (arkBall.y + ARK_BALL_R) - b.y, oB = (b.y + b.h) - (arkBall.y - ARK_BALL_R);
                const minOv = Math.min(oL, oR, oT, oB);
                if (minOv === oL || minOv === oR) arkBall.vx = -arkBall.vx;
                else arkBall.vy = -arkBall.vy;
                arkScore += b.points;
                arkScoreEl.textContent = arkScore;
                bump(arkScoreEl);
                for (let k = 0; k < 6; k++)
                    arkParticles.push({x: b.x + b.w/2, y: b.y + b.h/2, vx: (Math.random() - 0.5) * 3, vy: (Math.random() - 0.5) * 3, life: 1, size: 2 + Math.random() * 2, color: b.color});
                arkBricks.splice(i, 1);
                haptic(10);
                const ms = Math.floor(arkScore / 30);
                if (ms > arkCoinMilestone) { addCoins(ms - arkCoinMilestone, false); arkCoinMilestone = ms; }
                if (tryUpdateRecord('arknoid_best', arkScore, arkBestEl, arkBestBox))
                    toast('🏆 Новый рекорд: ' + arkScore, 'record', 1400);
                break;
            }
        }
        if (arkBall.y > ARK_H + 20) {
            arkLives--;
            haptic(80);
            if (arkLives <= 0) { gameOverArknoid(); return; }
            toast('💔 Потерян мяч! Осталось: ' + arkLives, 'info', 1000);
            resetArknoidBall();
        }
        if (arkBricks.length === 0) {
            arkScore += 100;
            arkScoreEl.textContent = arkScore;
            toast('🎉 Уровень пройден! +100 очков', 'success', 1600);
            addCoins(15, false);
            if (tryUpdateRecord('arknoid_best', arkScore, arkBestEl, arkBestBox))
                toast('🏆 Новый рекорд: ' + arkScore, 'record', 1600);
            setTimeout(() => { if (!arkRunning) return; buildArknoidBricks(); resetArknoidBall(); }, 700);
        }
    }
    for (let i = arkParticles.length - 1; i >= 0; i--) {
        const p = arkParticles[i];
        p.x += p.vx; p.y += p.vy; p.life -= 0.03;
        if (p.life <= 0) arkParticles.splice(i, 1);
    }
}
function gameOverArknoid() {
    stopArknoid();
    toast('Игра окончена! Очки: ' + arkScore, 'info', 2000);
    setTimeout(() => { if (currentScreen === 'arknoid-screen') startArknoid(); }, 1200);
}
function drawArknoid() {
    const grad = arkCtx.createLinearGradient(0, 0, 0, ARK_H);
    grad.addColorStop(0, '#04060f'); grad.addColorStop(1, '#0a0620');
    arkCtx.fillStyle = grad; arkCtx.fillRect(0, 0, ARK_W, ARK_H);
    for (const b of arkBricks) {
        arkCtx.fillStyle = b.color;
        arkCtx.shadowColor = b.color;
        arkCtx.shadowBlur = 8;
        roundRect(arkCtx, b.x, b.y, b.w, b.h, 4); arkCtx.fill();
        arkCtx.shadowBlur = 0;
        arkCtx.fillStyle = 'rgba(255,255,255,0.25)';
        roundRect(arkCtx, b.x + 2, b.y + 2, b.w - 4, b.h * 0.4, 3); arkCtx.fill();
    }
    arkCtx.fillStyle = '#3f51b5';
    arkCtx.shadowColor = '#3f51b5';
    arkCtx.shadowBlur = 14;
    roundRect(arkCtx, arkPaddleX - ARK_PADDLE_W/2, ARK_PADDLE_Y, ARK_PADDLE_W, ARK_PADDLE_H, 6);
    arkCtx.fill();
    arkCtx.shadowBlur = 0;
    arkCtx.fillStyle = '#fff';
    arkCtx.shadowColor = '#fff';
    arkCtx.shadowBlur = 14;
    arkCtx.beginPath(); arkCtx.arc(arkBall.x, arkBall.y, ARK_BALL_R, 0, Math.PI * 2); arkCtx.fill();
    arkCtx.shadowBlur = 0;
    if (!arkBall.launched && !arkPaused) {
        arkCtx.fillStyle = 'rgba(255,255,255,0.4)';
        arkCtx.font = 'bold 12px system-ui';
        arkCtx.textAlign = 'center';
        arkCtx.fillText('НАЖМИТЕ 🔥 ДЛЯ ЗАПУСКА', ARK_W/2, ARK_PADDLE_Y - 30);
    }
    for (const p of arkParticles) {
        arkCtx.globalAlpha = p.life;
        arkCtx.fillStyle = p.color;
        arkCtx.beginPath(); arkCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2); arkCtx.fill();
    }
    arkCtx.globalAlpha = 1;
    for (let i = 0; i < arkLives; i++) {
        arkCtx.fillStyle = '#3f51b5';
        arkCtx.font = 'bold 14px system-ui';
        arkCtx.textAlign = 'left';
        arkCtx.fillText('❤', 8 + i * 16, 20);
    }
    if (arkPaused) {
        arkCtx.fillStyle = 'rgba(0,0,0,0.5)';
        arkCtx.fillRect(0, 0, ARK_W, ARK_H);
        arkCtx.fillStyle = '#fff';
        arkCtx.font = 'bold 28px system-ui';
        arkCtx.textAlign = 'center';
        arkCtx.textBaseline = 'middle';
        arkCtx.fillText('ПАУЗА', ARK_W/2, ARK_H/2);
    }
}

/* ============================================================
   КОСМИЧЕСКИЙ ШУТЕР
============================================================ */
const shCanvas = $('shooterCanvas');
const shCtx = shCanvas.getContext('2d');
const shScoreEl = $('shooter-score');
const shBestEl = $('shooter-best');
const shBestBox = $('shooter-best-box');
const SH_W = shCanvas.width, SH_H = shCanvas.height;
let shRunning = false;
let shPlayerX = SH_W / 2, shPlayerW = 36, shPlayerH = 30;
let shBullets = [], shEnemies = [], shEnemyBullets = [], shStars = [], shParticles = [];
let shPowerups = [];
let shScore = 0, shLives = 3, shMaxLives = 5;
let shLastFire = 0, shLastEnemySpawn = 0, shLastEnemyShot = 0, shLastPowerupSpawn = 0;
let shDir = 0, shFrameId = null, shLastTime = 0, shPaused = false, shCoinMilestoneSh = 0;
let shBossActive = null, shNextBossScore = 500;
let shShieldTime = 0, shTripleTime = 0, shCombo = 0, shLastKillTime = 0;
const SH_PLAYER_Y = SH_H - 60;
const SH_FIRE_COOLDOWN = 220;
const SH_POWERUP_DURATION = 8000;

function startShooter() {
    shPlayerX = SH_W / 2;
    shBullets = []; shEnemies = []; shEnemyBullets = []; shParticles = []; shPowerups = [];
    shScore = 0; shLives = 3;
    shScoreEl.textContent = '0';
    shBestEl.textContent = getBest('shooter_best');
    shDir = 0; shLastFire = 0; shLastEnemySpawn = 0; shLastEnemyShot = 0; shLastPowerupSpawn = 0;
    shCoinMilestoneSh = 0; shPaused = false;
    shBossActive = null; shNextBossScore = 500;
    shShieldTime = 0; shTripleTime = 0; shCombo = 0; shLastKillTime = 0;
    initStars();
    shRunning = true;
    shLastTime = performance.now();
    if (shFrameId) cancelAnimationFrame(shFrameId);
    shFrameId = requestAnimationFrame(shooterLoop);
}
function stopShooter() { shRunning = false; if (shFrameId) cancelAnimationFrame(shFrameId); shFrameId = null; }
function initStars() {
    shStars = [];
    for (let i = 0; i < 60; i++)
        shStars.push({x: Math.random() * SH_W, y: Math.random() * SH_H, speed: 0.3 + Math.random() * 2, size: 0.5 + Math.random() * 1.8});
}
function shooterControl(action) {
    if (!shRunning) return;
    if (action === 'left') { shDir = -1; haptic(6); }
    else if (action === 'right') { shDir = 1; haptic(6); }
    else if (action === 'fire') { fireBullet(true); haptic(10); }
    else if (action === 'pause') { shPaused = !shPaused; haptic(10); }
}
function fireBullet(force = false) {
    const now = performance.now();
    if (!force && now - shLastFire < SH_FIRE_COOLDOWN) return;
    shLastFire = now;
    const triple = now < shTripleTime;
    if (triple) {
        shBullets.push({x: shPlayerX, y: SH_PLAYER_Y - 4, vy: -8});
        shBullets.push({x: shPlayerX - 10, y: SH_PLAYER_Y - 4, vy: -8, vx: -1});
        shBullets.push({x: shPlayerX + 10, y: SH_PLAYER_Y - 4, vy: -8, vx: 1});
    } else {
        shBullets.push({x: shPlayerX, y: SH_PLAYER_Y - 4, vy: -8});
    }
}
function shooterLoop(t) {
    if (!shRunning) return;
    const dt = Math.min(40, t - shLastTime);
    shLastTime = t;
    if (!shPaused) updateShooter(dt, t);
    drawShooter();
    shFrameId = requestAnimationFrame(shooterLoop);
}
function updateShooter(dt, t) {
    shPlayerX += shDir * 0.34 * dt;
    shPlayerX = Math.max(shPlayerW/2, Math.min(SH_W - shPlayerW/2, shPlayerX));
    for (const s of shStars) {
        s.y += s.speed * dt * 0.06;
        if (s.y > SH_H) { s.y = 0; s.x = Math.random() * SH_W; }
    }
    if (t - shLastFire > SH_FIRE_COOLDOWN) fireBullet();
    for (let i = shBullets.length - 1; i >= 0; i--) {
        const b = shBullets[i];
        b.y += b.vy * dt * 0.06;
        if (b.vx) b.x += b.vx * dt * 0.06;
        if (b.y < -10 || b.x < -10 || b.x > SH_W + 10) shBullets.splice(i, 1);
    }
    if (!shBossActive && t - shLastEnemySpawn > 800 + Math.random() * 800) { shLastEnemySpawn = t; spawnEnemy(); }
    if (t - shLastPowerupSpawn > 5000 + Math.random() * 4000 && shPowerups.length < 2) { shLastPowerupSpawn = t; spawnPowerup(); }
    if (!shBossActive && shScore >= shNextBossScore) {
        shBossActive = spawnBoss();
        shNextBossScore += 500;
        toast('👹 БОСС!', 'info', 1500);
        haptic(60);
    }
    for (let i = shEnemies.length - 1; i >= 0; i--) {
        const e = shEnemies[i];
        e.y += e.vy * dt * 0.06;
        e.x += e.vx * dt * 0.06;
        if (e.x < e.w/2 || e.x > SH_W - e.w/2) e.vx *= -1;
        if (e.y > SH_H + 20) { shEnemies.splice(i, 1); continue; }
        for (let j = shBullets.length - 1; j >= 0; j--) {
            const b = shBullets[j];
            if (Math.abs(b.x - e.x) < e.w/2 && Math.abs(b.y - e.y) < e.h/2) {
                shBullets.splice(j, 1);
                e.hp--;
                for (let k = 0; k < 4; k++)
                    shParticles.push({x: e.x, y: e.y, vx: (Math.random() - 0.5) * 3, vy: (Math.random() - 0.5) * 3, life: 1, size: 2 + Math.random() * 2, color: e.color});
                if (e.hp <= 0) killEnemy(e, i);
                break;
            }
        }
    }
    if (shBossActive) {
        const b = shBossActive;
        b.y += b.vy * dt * 0.06;
        b.x += b.vx * dt * 0.06;
        if (b.x < b.w/2 || b.x > SH_W - b.w/2) b.vx *= -1;
        if (b.y > 100) { b.y = 100; b.vy = 0; }
        b.shootTimer = (b.shootTimer || 0) + dt;
        if (b.shootTimer > 900) {
            b.shootTimer = 0;
            for (let k = -1; k <= 1; k++) shEnemyBullets.push({x: b.x + k * 20, y: b.y + b.h/2, vy: 4});
        }
        for (let j = shBullets.length - 1; j >= 0; j--) {
            const bullet = shBullets[j];
            if (Math.abs(bullet.x - b.x) < b.w/2 && Math.abs(bullet.y - b.y) < b.h/2) {
                shBullets.splice(j, 1);
                b.hp--;
                for (let k = 0; k < 5; k++)
                    shParticles.push({x: bullet.x, y: bullet.y, vx: (Math.random() - 0.5) * 4, vy: (Math.random() - 0.5) * 4, life: 1, size: 2 + Math.random() * 2, color: '#ff4081'});
                if (b.hp <= 0) {
                    shScore += 500;
                    shScoreEl.textContent = shScore;
                    bump(shScoreEl);
                    for (let k = 0; k < 30; k++)
                        shParticles.push({x: b.x + (Math.random() - 0.5) * b.w, y: b.y + (Math.random() - 0.5) * b.h, vx: (Math.random() - 0.5) * 6, vy: (Math.random() - 0.5) * 6, life: 1, size: 3 + Math.random() * 3, color: '#ff4081'});
                    addCoins(10, false);
                    toast('🎉 Босс повержен! +500', 'success', 1800);
                    haptic(80);
                    if (tryUpdateRecord('shooter_best', shScore, shBestEl, shBestBox))
                        toast('🏆 Новый рекорд: ' + shScore, 'record', 1600);
                    shBossActive = null;
                }
                break;
            }
        }
    }
    for (let i = shPowerups.length - 1; i >= 0; i--) {
        const p = shPowerups[i];
        p.y += 1.2 * dt * 0.06;
        if (p.y > SH_H + 20) { shPowerups.splice(i, 1); continue; }
        if (Math.abs(p.x - shPlayerX) < shPlayerW/2 + 10 && Math.abs(p.y - SH_PLAYER_Y) < shPlayerH/2 + 10) {
            applyPowerup(p.type);
            shPowerups.splice(i, 1);
        }
    }
    if (t - shLastEnemyShot > 900 && shEnemies.length > 0) {
        shLastEnemyShot = t;
        const e = shEnemies[Math.floor(Math.random() * shEnemies.length)];
        shEnemyBullets.push({x: e.x, y: e.y + e.h/2, vy: 4});
    }
    for (let i = shEnemyBullets.length - 1; i >= 0; i--) {
        const b = shEnemyBullets[i];
        b.y += b.vy * dt * 0.06;
        if (b.y > SH_H + 10) { shEnemyBullets.splice(i, 1); continue; }
        if (Math.abs(b.x - shPlayerX) < shPlayerW/2 && Math.abs(b.y - SH_PLAYER_Y) < shPlayerH/2) {
            shEnemyBullets.splice(i, 1);
            damagePlayer();
        }
    }
    for (let i = shEnemies.length - 1; i >= 0; i--) {
        const e = shEnemies[i];
        if (Math.abs(e.x - shPlayerX) < (e.w + shPlayerW)/2 - 6 && Math.abs(e.y - SH_PLAYER_Y) < (e.h + shPlayerH)/2 - 6) {
            shEnemies.splice(i, 1);
            damagePlayer();
        }
    }
    for (let i = shParticles.length - 1; i >= 0; i--) {
        const p = shParticles[i];
        p.x += p.vx; p.y += p.vy; p.life -= 0.03;
        if (p.life <= 0) shParticles.splice(i, 1);
    }
    if (performance.now() > shTripleTime) shTripleTime = 0;
    if (performance.now() > shShieldTime) shShieldTime = 0;
}
function killEnemy(e, idx) {
    shEnemies.splice(idx, 1);
    const now = performance.now();
    if (now - shLastKillTime < 1500) shCombo++; else shCombo = 1;
    shLastKillTime = now;
    const comboMult = Math.min(5, shCombo);
    const points = e.points * comboMult;
    shScore += points;
    shScoreEl.textContent = shScore;
    bump(shScoreEl);
    if (shCombo > 1) toast('×' + shCombo + ' комбо! +' + points, 'success', 900);
    const ms = Math.floor(shScore / 100);
    if (ms > shCoinMilestoneSh) { addCoins((ms - shCoinMilestoneSh) * 2, false); shCoinMilestoneSh = ms; }
    if (tryUpdateRecord('shooter_best', shScore, shBestEl, shBestBox))
        toast('🏆 Новый рекорд: ' + shScore, 'record', 1400);
}
function spawnEnemy() {
    const types = [
        {w: 30, h: 26, hp: 1, vy: 1.0, points: 10, color: '#ff5252'},
        {w: 34, h: 30, hp: 2, vy: 0.8, points: 20, color: '#e040fb'},
        {w: 26, h: 22, hp: 1, vy: 1.4, points: 15, color: '#40c4ff'},
        {w: 40, h: 34, hp: 3, vy: 0.6, points: 30, color: '#ffa726'}
    ];
    const t = types[Math.floor(Math.random() * types.length)];
    shEnemies.push({x: t.w + Math.random() * (SH_W - t.w * 2), y: -t.h, w: t.w, h: t.h, hp: t.hp, vy: t.vy, vx: (Math.random() - 0.5) * 0.6, points: t.points, color: t.color});
}
function spawnBoss() {
    return {x: SH_W / 2, y: -60, w: 90, h: 70, hp: 30 + Math.floor(shScore / 500) * 10, vy: 1.5, vx: 1.2, shootTimer: 0};
}
function spawnPowerup() {
    const types = ['shield', 'triple', 'bomb', 'life'];
    const weights = [0.35, 0.35, 0.2, 0.1];
    let r = Math.random(), acc = 0, type = 'shield';
    for (let i = 0; i < types.length; i++) {
        acc += weights[i];
        if (r < acc) { type = types[i]; break; }
    }
    shPowerups.push({x: 30 + Math.random() * (SH_W - 60), y: -20, type});
}
function applyPowerup(type) {
    haptic(20);
    if (type === 'shield') { shShieldTime = performance.now() + SH_POWERUP_DURATION; toast('💎 Щит активен!', 'success', 1200); }
    else if (type === 'triple') { shTripleTime = performance.now() + SH_POWERUP_DURATION; toast('⚡ Тройной выстрел!', 'success', 1200); }
    else if (type === 'bomb') {
        for (const e of shEnemies) {
            shScore += e.points;
            for (let k = 0; k < 6; k++)
                shParticles.push({x: e.x, y: e.y, vx: (Math.random() - 0.5) * 4, vy: (Math.random() - 0.5) * 4, life: 1, size: 2 + Math.random() * 2, color: e.color});
        }
        const count = shEnemies.length;
        shEnemies = [];
        shEnemyBullets = [];
        shScoreEl.textContent = shScore;
        bump(shScoreEl);
        toast('💣 Бомба! Уничтожено: ' + count, 'success', 1400);
        if (tryUpdateRecord('shooter_best', shScore, shBestEl, shBestBox))
            toast('🏆 Новый рекорд: ' + shScore, 'record', 1400);
    } else if (type === 'life') {
        if (shLives < shMaxLives) { shLives++; toast('❤ +1 жизнь!', 'success', 1200); }
        else { shScore += 100; shScoreEl.textContent = shScore; toast('❤ Максимум! +100', 'info', 1200); }
    }
}
function damagePlayer() {
    if (performance.now() < shShieldTime) { toast('💎 Щит поглотил удар', 'info', 800); return; }
    shLives--;
    haptic(80);
    shCombo = 0;
    if (shLives <= 0) gameOverShooter();
    else toast('💥 Попадание! Жизней: ' + shLives, 'info', 900);
}
function gameOverShooter() {
    stopShooter();
    const coinsEarned = Math.floor(shScore / 100) * 2;
    toast('Игра окончена! Очки: ' + shScore + (coinsEarned ? ' (+' + coinsEarned + '🪙)' : ''), 'info', 2000);
    setTimeout(() => { if (currentScreen === 'shooter-screen') startShooter(); }, 1200);
}
function drawShooter() {
    const grad = shCtx.createLinearGradient(0, 0, 0, SH_H);
    grad.addColorStop(0, '#000510'); grad.addColorStop(1, '#0a0620');
    shCtx.fillStyle = grad; shCtx.fillRect(0, 0, SH_W, SH_H);
    for (const s of shStars) {
        shCtx.fillStyle = 'rgba(255,255,255,' + (0.3 + s.speed * 0.3) + ')';
        shCtx.beginPath(); shCtx.arc(s.x, s.y, s.size, 0, Math.PI * 2); shCtx.fill();
    }
    for (const p of shPowerups) {
        const colors = {shield: '#00bcd4', triple: '#ffeb3b', bomb: '#ff5252', life: '#f44336'};
        const icons = {shield: '💎', triple: '⚡', bomb: '💣', life: '❤'};
        shCtx.fillStyle = colors[p.type];
        shCtx.shadowColor = colors[p.type];
        shCtx.shadowBlur = 14;
        shCtx.beginPath(); shCtx.arc(p.x, p.y, 12, 0, Math.PI * 2); shCtx.fill();
        shCtx.shadowBlur = 0;
        shCtx.font = 'bold 14px system-ui';
        shCtx.textAlign = 'center';
        shCtx.textBaseline = 'middle';
        shCtx.fillStyle = '#000';
        shCtx.fillText(icons[p.type], p.x, p.y + 1);
    }
    for (const b of shBullets) {
        shCtx.fillStyle = '#ffeb3b';
        shCtx.shadowColor = '#ffeb3b';
        shCtx.shadowBlur = 10;
        shCtx.fillRect(b.x - 1.5, b.y - 8, 3, 12);
        shCtx.shadowBlur = 0;
    }
    for (const e of shEnemies) {
        shCtx.fillStyle = e.color;
        shCtx.shadowColor = e.color;
        shCtx.shadowBlur = 12;
        shCtx.beginPath();
        shCtx.moveTo(e.x, e.y + e.h/2);
        shCtx.lineTo(e.x - e.w/2, e.y - e.h/2);
        shCtx.lineTo(e.x + e.w/2, e.y - e.h/2);
        shCtx.closePath();
        shCtx.fill();
        shCtx.shadowBlur = 0;
        shCtx.fillStyle = '#fff';
        shCtx.beginPath(); shCtx.arc(e.x, e.y - 2, 2.5, 0, Math.PI * 2); shCtx.fill();
    }
    if (shBossActive) {
        const b = shBossActive;
        shCtx.fillStyle = '#ff4081';
        shCtx.shadowColor = '#ff4081';
        shCtx.shadowBlur = 20;
        roundRect(shCtx, b.x - b.w/2, b.y - b.h/2, b.w, b.h, 12);
        shCtx.fill();
        shCtx.shadowBlur = 0;
        shCtx.fillStyle = '#fff';
        shCtx.font = 'bold 28px system-ui';
        shCtx.textAlign = 'center';
        shCtx.textBaseline = 'middle';
        shCtx.fillText('👹', b.x, b.y);
        const maxHp = 30 + Math.floor(shScore / 500) * 10;
        const hpPct = b.hp / maxHp;
        shCtx.fillStyle = 'rgba(0,0,0,0.5)';
        shCtx.fillRect(b.x - b.w/2, b.y - b.h/2 - 10, b.w, 5);
        shCtx.fillStyle = '#ff4081';
        shCtx.fillRect(b.x - b.w/2, b.y - b.h/2 - 10, b.w * hpPct, 5);
    }
    for (const b of shEnemyBullets) {
        shCtx.fillStyle = '#ff5252';
        shCtx.shadowColor = '#ff5252';
        shCtx.shadowBlur = 8;
        shCtx.beginPath(); shCtx.arc(b.x, b.y, 3, 0, Math.PI * 2); shCtx.fill();
        shCtx.shadowBlur = 0;
    }
    for (const p of shParticles) {
        shCtx.globalAlpha = p.life;
        shCtx.fillStyle = p.color;
        shCtx.beginPath(); shCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2); shCtx.fill();
    }
    shCtx.globalAlpha = 1;
    if (performance.now() < shShieldTime) {
        shCtx.strokeStyle = 'rgba(0,188,212,0.6)';
        shCtx.lineWidth = 2;
        shCtx.beginPath();
        shCtx.arc(shPlayerX, SH_PLAYER_Y, shPlayerW * 0.7, 0, Math.PI * 2);
        shCtx.stroke();
    }
    shCtx.save();
    shCtx.translate(shPlayerX, SH_PLAYER_Y);
    shCtx.fillStyle = '#4dd0e1';
    shCtx.shadowColor = '#4dd0e1';
    shCtx.shadowBlur = 14;
    shCtx.beginPath();
    shCtx.moveTo(0, -shPlayerH/2);
    shCtx.lineTo(-shPlayerW/2, shPlayerH/2);
    shCtx.lineTo(-shPlayerW/4, shPlayerH/2 - 6);
    shCtx.lineTo(shPlayerW/4, shPlayerH/2 - 6);
    shCtx.lineTo(shPlayerW/2, shPlayerH/2);
    shCtx.closePath();
    shCtx.fill();
    shCtx.shadowBlur = 0;
    shCtx.fillStyle = '#b2ebf2';
    shCtx.beginPath(); shCtx.arc(0, -2, 5, 0, Math.PI * 2); shCtx.fill();
    shCtx.restore();
    for (let i = 0; i < shLives; i++) {
        shCtx.fillStyle = '#4dd0e1';
        shCtx.font = 'bold 14px system-ui';
        shCtx.textAlign = 'left';
        shCtx.fillText('❤', 6 + i * 16, 20);
    }
    if (shCombo > 1) {
        shCtx.fillStyle = '#ffd54f';
        shCtx.font = 'bold 16px system-ui';
        shCtx.textAlign = 'right';
        shCtx.fillText('×' + Math.min(5, shCombo) + ' КОМБО', SH_W - 8, 22);
    }
    let by = 40;
    if (performance.now() < shShieldTime) {
        shCtx.fillStyle = '#00bcd4';
        shCtx.font = 'bold 12px system-ui';
        shCtx.textAlign = 'left';
        shCtx.fillText('💎 ' + Math.ceil((shShieldTime - performance.now())/1000) + 'с', 8, by);
        by += 16;
    }
    if (performance.now() < shTripleTime) {
        shCtx.fillStyle = '#ffeb3b';
        shCtx.font = 'bold 12px system-ui';
        shCtx.textAlign = 'left';
        shCtx.fillText('⚡ ' + Math.ceil((shTripleTime - performance.now())/1000) + 'с', 8, by);
    }
    if (shPaused) {
        shCtx.fillStyle = 'rgba(0,0,0,0.5)';
        shCtx.fillRect(0, 0, SH_W, SH_H);
        shCtx.fillStyle = '#fff';
        shCtx.font = 'bold 28px system-ui';
        shCtx.textAlign = 'center';
        shCtx.textBaseline = 'middle';
        shCtx.fillText('ПАУЗА', SH_W/2, SH_H/2);
    }
}

/* ============================================================
   ТЕТРИС
============================================================ */
const tCanvas = $('tetrisCanvas');
const tCtx = tCanvas.getContext('2d');
const tScoreEl = $('tetris-score');
const tBestEl = $('tetris-best');
const tBestBox = $('tetris-best-box');
const tPauseBtn = $('tetrisPauseBtn');
const T_COLS = 10, T_ROWS = 20, T_CELL = tCanvas.width / T_COLS;
const T_PIECES = {
    I: {shape: [[1,1,1,1]], color: '#00e5ff'},
    O: {shape: [[1,1],[1,1]], color: '#ffeb3b'},
    T: {shape: [[0,1,0],[1,1,1]], color: '#ba68c8'},
    S: {shape: [[0,1,1],[1,1,0]], color: '#66bb6a'},
    Z: {shape: [[1,1,0],[0,1,1]], color: '#ef5350'},
    J: {shape: [[1,0,0],[1,1,1]], color: '#42a5f5'},
    L: {shape: [[0,0,1],[1,1,1]], color: '#ff9800'}
};
let tGrid = [], tCurrent = null, tNext = null, tScore = 0;
let tRunning = false, tPaused = false, tFrameId = null, tLastTime = 0;
let tDropInterval = 700, tDropCounter = 0, tLines = 0, tCoinMilestone = 0;

function startTetris() {
    tGrid = Array.from({length: T_ROWS}, () => Array(T_COLS).fill(null));
    tScore = 0; tLines = 0; tCoinMilestone = 0;
    tScoreEl.textContent = '0';
    tBestEl.textContent = getBest('tetris_best');
    tPaused = false;
    tPauseBtn.textContent = 'ПАУЗА';
    tDropInterval = 700; tDropCounter = 0;
    tNext = randomTetrisPiece();
    spawnTetrisPiece();
    tRunning = true;
    tLastTime = performance.now();
    if (tFrameId) cancelAnimationFrame(tFrameId);
    tFrameId = requestAnimationFrame(tetrisLoop);
}
function stopTetris() { tRunning = false; if (tFrameId) cancelAnimationFrame(tFrameId); tFrameId = null; }
function randomTetrisPiece() {
    const keys = Object.keys(T_PIECES);
    const k = keys[Math.floor(Math.random() * keys.length)];
    return {type: k, shape: T_PIECES[k].shape.map(row => row.slice()), color: T_PIECES[k].color, x: 0, y: 0};
}
function spawnTetrisPiece() {
    tCurrent = tNext || randomTetrisPiece();
    tNext = randomTetrisPiece();
    tCurrent.x = Math.floor((T_COLS - tCurrent.shape[0].length) / 2);
    tCurrent.y = 0;
    if (collidesTetris(tCurrent.x, tCurrent.y, tCurrent.shape)) gameOverTetris();
}
function collidesTetris(x, y, shape) {
    for (let r = 0; r < shape.length; r++)
        for (let c = 0; c < shape[r].length; c++) {
            if (!shape[r][c]) continue;
            const nx = x + c, ny = y + r;
            if (nx < 0 || nx >= T_COLS || ny >= T_ROWS) return true;
            if (ny >= 0 && tGrid[ny][nx]) return true;
        }
    return false;
}
function lockTetrisPiece() {
    for (let r = 0; r < tCurrent.shape.length; r++)
        for (let c = 0; c < tCurrent.shape[r].length; c++)
            if (tCurrent.shape[r][c]) {
                const ny = tCurrent.y + r, nx = tCurrent.x + c;
                if (ny >= 0) tGrid[ny][nx] = tCurrent.color;
            }
    clearTetrisLines();
    spawnTetrisPiece();
}
function clearTetrisLines() {
    let cleared = 0;
    for (let r = T_ROWS - 1; r >= 0; r--) {
        if (tGrid[r].every(cell => cell)) {
            tGrid.splice(r, 1);
            tGrid.unshift(Array(T_COLS).fill(null));
            cleared++;
            r++;
        }
    }
    if (cleared > 0) {
        const points = [0, 100, 300, 500, 800][cleared] || 800;
        tScore += points;
        tLines += cleared;
        tScoreEl.textContent = tScore;
        bump(tScoreEl);
        haptic(cleared >= 4 ? 60 : 25);
        const ms = Math.floor(tScore / 200);
        if (ms > tCoinMilestone) { addCoins(ms - tCoinMilestone, false); tCoinMilestone = ms; }
        if (tryUpdateRecord('tetris_best', tScore, tBestEl, tBestBox))
            toast('🏆 Новый рекорд: ' + tScore, 'record', 1400);
        else if (cleared === 4) toast('🎉 ТЕТРИС! +800', 'success', 1200);
        tDropInterval = Math.max(120, 700 - Math.floor(tLines / 5) * 80);
    }
}
function tetrisControl(action) {
    if (!tRunning) return;
    if (action === 'pause') { tPaused = !tPaused; tPauseBtn.textContent = tPaused ? 'ПРОДОЛЖИТЬ' : 'ПАУЗА'; haptic(10); return; }
    if (tPaused) return;
    if (action === 'left') { if (!collidesTetris(tCurrent.x - 1, tCurrent.y, tCurrent.shape)) tCurrent.x--; haptic(6); }
    else if (action === 'right') { if (!collidesTetris(tCurrent.x + 1, tCurrent.y, tCurrent.shape)) tCurrent.x++; haptic(6); }
    else if (action === 'rotate') {
        const rotated = rotateMatrix(tCurrent.shape);
        if (!collidesTetris(tCurrent.x, tCurrent.y, rotated)) tCurrent.shape = rotated;
        haptic(8);
    } else if (action === 'drop') {
        while (!collidesTetris(tCurrent.x, tCurrent.y + 1, tCurrent.shape)) tCurrent.y++;
        lockTetrisPiece();
        haptic(12);
    }
}
function rotateMatrix(m) {
    const rows = m.length, cols = m[0].length;
    const out = Array.from({length: cols}, () => Array(rows).fill(0));
    for (let r = 0; r < rows; r++)
        for (let c = 0; c < cols; c++)
            out[c][rows - 1 - r] = m[r][c];
    return out;
}
function tetrisLoop(t) {
    if (!tRunning) return;
    const dt = t - tLastTime;
    tLastTime = t;
    if (!tPaused) {
        tDropCounter += dt;
        if (tDropCounter > tDropInterval) {
            tDropCounter = 0;
            if (!collidesTetris(tCurrent.x, tCurrent.y + 1, tCurrent.shape)) tCurrent.y++;
            else lockTetrisPiece();
        }
    }
    drawTetris();
    tFrameId = requestAnimationFrame(tetrisLoop);
}
function gameOverTetris() {
    stopTetris();
    const coinsEarned = Math.floor(tScore / 200);
    toast('Игра окончена! Очки: ' + tScore + (coinsEarned ? ' (+' + coinsEarned + '🪙)' : ''), 'info', 2000);
    setTimeout(() => { if (currentScreen === 'tetris-screen') startTetris(); }, 1200);
}
function drawTetris() {
    tCtx.fillStyle = '#021208';
    tCtx.fillRect(0, 0, tCanvas.width, tCanvas.height);
    tCtx.strokeStyle = 'rgba(0,230,118,0.08)';
    for (let i = 1; i < T_COLS; i++) { tCtx.beginPath(); tCtx.moveTo(i * T_CELL, 0); tCtx.lineTo(i * T_CELL, tCanvas.height); tCtx.stroke(); }
    for (let i = 1; i < T_ROWS; i++) { tCtx.beginPath(); tCtx.moveTo(0, i * T_CELL); tCtx.lineTo(tCanvas.width, i * T_CELL); tCtx.stroke(); }
    for (let r = 0; r < T_ROWS; r++)
        for (let c = 0; c < T_COLS; c++)
            if (tGrid[r][c]) drawTetrisCell(c, r, tGrid[r][c]);
    if (tCurrent)
        for (let r = 0; r < tCurrent.shape.length; r++)
            for (let c = 0; c < tCurrent.shape[r].length; c++)
                if (tCurrent.shape[r][c]) drawTetrisCell(tCurrent.x + c, tCurrent.y + r, tCurrent.color);
    if (tNext) {
        const previewX = tCanvas.width - 60, previewY = 10;
        tCtx.fillStyle = 'rgba(0,0,0,0.6)';
        roundRect(tCtx, previewX - 6, previewY - 4, 56, 56, 8); tCtx.fill();
        tCtx.fillStyle = 'rgba(255,255,255,0.5)';
        tCtx.font = 'bold 9px system-ui';
        tCtx.textAlign = 'center';
        tCtx.fillText('ДАЛЕЕ', previewX + 22, previewY + 6);
        const cellSize = 10;
        const offX = previewX + 22 - (tNext.shape[0].length * cellSize) / 2;
        const offY = previewY + 16;
        for (let r = 0; r < tNext.shape.length; r++)
            for (let c = 0; c < tNext.shape[r].length; c++)
                if (tNext.shape[r][c]) {
                    tCtx.fillStyle = tNext.color;
                    tCtx.fillRect(offX + c * cellSize, offY + r * cellSize, cellSize - 1, cellSize - 1);
                }
    }
    if (tPaused) {
        tCtx.fillStyle = 'rgba(0,0,0,0.6)';
        tCtx.fillRect(0, 0, tCanvas.width, tCanvas.height);
        tCtx.fillStyle = '#fff';
        tCtx.font = 'bold 24px system-ui';
        tCtx.textAlign = 'center';
        tCtx.textBaseline = 'middle';
        tCtx.fillText('ПАУЗА', tCanvas.width/2, tCanvas.height/2);
    }
}
function drawTetrisCell(c, r, color) {
    const x = c * T_CELL, y = r * T_CELL;
    tCtx.fillStyle = color;
    tCtx.shadowColor = color;
    tCtx.shadowBlur = 8;
    roundRect(tCtx, x + 1, y + 1, T_CELL - 2, T_CELL - 2, 3); tCtx.fill();
    tCtx.shadowBlur = 0;
    tCtx.fillStyle = 'rgba(255,255,255,0.25)';
    roundRect(tCtx, x + 2, y + 2, T_CELL - 4, T_CELL * 0.35, 2); tCtx.fill();
}

/* ============================================================
   ЗАЩИТНИК БАШНИ
============================================================ */
const twCanvas = $('towerCanvas');
const twCtx = twCanvas.getContext('2d');
const twWaveEl = $('tower-wave');
const twGoldEl = $('tower-gold');
const twBestEl = $('tower-best');
const twBestBox = $('tower-best-box');
const TW_W = twCanvas.width, TW_H = twCanvas.height;
const TW_PATH = [
    {x: 0, y: 60}, {x: 120, y: 60}, {x: 120, y: 160}, {x: 220, y: 160},
    {x: 220, y: 260}, {x: 80, y: 260}, {x: 80, y: 340}, {x: TW_W, y: 340}
];
const TW_SLOTS = [
    {x: 60, y: 100}, {x: 90, y: 130}, {x: 150, y: 100}, {x: 180, y: 130},
    {x: 60, y: 200}, {x: 90, y: 220}, {x: 150, y: 200}, {x: 180, y: 220},
    {x: 60, y: 300}, {x: 130, y: 300}, {x: 180, y: 300}, {x: 240, y: 220},
    {x: 250, y: 130}, {x: 250, y: 60}, {x: 170, y: 60}
];
const TOWER_TYPES = {
    arrow: {name: 'Лучник', cost: 50, range: 80, damage: 8, fireRate: 400, color: '#8bc34a', projectileColor: '#cddc39'},
    fire:  {name: 'Огонь', cost: 100, range: 60, damage: 20, fireRate: 800, color: '#ff5722', projectileColor: '#ff7043'},
    ice:   {name: 'Лёд', cost: 75, range: 70, damage: 5, fireRate: 500, color: '#03a9f4', projectileColor: '#4fc3f7', slow: 0.5, slowDuration: 1500}
};
let twRunning = false, twFrameId = null, twLastTime = 0;
let twTowers = [], twEnemies = [], twProjectiles = [], twParticles = [];
let twGold = 200, twWave = 0, twWaveActive = false, twSelectedType = null, twLives = 20, twBest = 0;
let twCoinMilestone = 0, twSpawnQueue = [], twSpawnTimer = 0, twWaveCompletePending = false;

function startTower() {
    twTowers = []; twEnemies = []; twProjectiles = []; twParticles = [];
    twGold = 200; twWave = 0; twWaveActive = false; twSelectedType = null; twLives = 20;
    twSpawnQueue = []; twSpawnTimer = 0; twWaveCompletePending = false; twCoinMilestone = 0;
    twBest = getBest('tower_best');
    twWaveEl.textContent = '0';
    twGoldEl.textContent = twGold;
    twBestEl.textContent = twBest;
    TW_SLOTS.forEach(s => s.occupied = false);
    updateTowerTools();
    twRunning = true;
    twLastTime = performance.now();
    if (twFrameId) cancelAnimationFrame(twFrameId);
    twFrameId = requestAnimationFrame(towerLoop);
}
function stopTower() { twRunning = false; if (twFrameId) cancelAnimationFrame(twFrameId); twFrameId = null; }

function updateTowerTools() {
    document.querySelectorAll('.tool-btn[data-type]').forEach(btn => {
        const type = btn.dataset.type;
        btn.disabled = twGold < TOWER_TYPES[type].cost;
        btn.classList.toggle('selected', twSelectedType === type);
    });
}
function selectTower(type) {
    if (!twRunning) return;
    haptic(10);
    twSelectedType = twSelectedType === type ? null : type;
    updateTowerTools();
}
function startTowerWave() {
    if (!twRunning || twWaveActive) return;
    haptic(20);
    twWave++;
    twWaveEl.textContent = twWave;
    twWaveActive = true;
    twWaveCompletePending = false;
    const count = 5 + twWave * 2;
    const hp = 20 + twWave * 8;
    const speed = 0.6 + twWave * 0.05;
    twSpawnQueue = [];
    for (let i = 0; i < count; i++) twSpawnQueue.push({hp, speed, reward: 15 + twWave * 2});
    twSpawnTimer = 0;
}
function towerLoop(t) {
    if (!twRunning) return;
    const dt = Math.min(40, t - twLastTime);
    twLastTime = t;
    updateTower(dt, t);
    drawTower();
    twFrameId = requestAnimationFrame(towerLoop);
}
function towerClick(e) {
    if (!twRunning) return;
    const rect = twCanvas.getBoundingClientRect();
    const x = (e.clientX - rect.left) * (twCanvas.width / rect.width);
    const y = (e.clientY - rect.top) * (twCanvas.height / rect.height);
    for (const slot of TW_SLOTS) {
        if (Math.hypot(slot.x - x, slot.y - y) < 22) {
            if (slot.occupied) { toast('Здесь уже стоит башня', 'info', 900); return; }
            if (!twSelectedType) { toast('Сначала выберите башню', 'info', 1000); return; }
            const t = TOWER_TYPES[twSelectedType];
            if (twGold < t.cost) { toast('Не хватает золота', 'info', 900); return; }
            twGold -= t.cost;
            twGoldEl.textContent = twGold;
            bump(twGoldEl);
            twTowers.push({x: slot.x, y: slot.y, type: twSelectedType, ...t, lastShot: 0, angle: 0});
            slot.occupied = true;
            haptic(15);
            updateTowerTools();
            return;
        }
    }
}
function updateTower(dt, t) {
    if (twWaveActive && twSpawnQueue.length > 0) {
        twSpawnTimer -= dt;
        if (twSpawnTimer <= 0) {
            twSpawnTimer = 700;
            const e = twSpawnQueue.shift();
            twEnemies.push({
                x: TW_PATH[0].x, y: TW_PATH[0].y, pathIdx: 0,
                hp: e.hp, maxHp: e.hp, speed: e.speed, reward: e.reward,
                slowUntil: 0, slowFactor: 1
            });
        }
    }
    for (let i = twEnemies.length - 1; i >= 0; i--) {
        const e = twEnemies[i];
        const slowMul = t < e.slowUntil ? e.slowFactor : 1;
        const speed = e.speed * slowMul;
        const target = TW_PATH[e.pathIdx + 1];
        if (!target) {
            twEnemies.splice(i, 1);
            twLives--;
            haptic(60);
            if (twLives <= 0) { gameOverTower(); return; }
            continue;
        }
        const dx = target.x - e.x, dy = target.y - e.y;
        const dist = Math.hypot(dx, dy);
        const move = speed * dt * 0.08;
        if (dist <= move) { e.x = target.x; e.y = target.y; e.pathIdx++; }
        else { e.x += (dx / dist) * move; e.y += (dy / dist) * move; }
    }
    for (const tower of twTowers) {
        if (t - tower.lastShot < tower.fireRate) continue;
        let target = null, minDist = Infinity;
        for (const e of twEnemies) {
            const d = Math.hypot(e.x - tower.x, e.y - tower.y);
            if (d < tower.range && d < minDist) { minDist = d; target = e; }
        }
        if (target) {
            tower.lastShot = t;
            tower.angle = Math.atan2(target.y - tower.y, target.x - tower.x);
            twProjectiles.push({
                x: tower.x, y: tower.y, target,
                damage: tower.damage, speed: 5, color: tower.projectileColor,
                slow: tower.slow, slowDuration: tower.slowDuration
            });
        }
    }
    for (let i = twProjectiles.length - 1; i >= 0; i--) {
        const p = twProjectiles[i];
        if (!p.target || p.target.hp <= 0) { twProjectiles.splice(i, 1); continue; }
        const dx = p.target.x - p.x, dy = p.target.y - p.y;
        const dist = Math.hypot(dx, dy);
        const move = p.speed * dt * 0.15;
        if (dist <= move) {
            p.target.hp -= p.damage;
            if (p.slow) { p.target.slowUntil = t + p.slowDuration; p.target.slowFactor = p.slow; }
            for (let k = 0; k < 4; k++)
                twParticles.push({x: p.target.x, y: p.target.y, vx: (Math.random() - 0.5) * 3, vy: (Math.random() - 0.5) * 3, life: 1, size: 2 + Math.random() * 2, color: p.color});
            if (p.target.hp <= 0) {
                twGold += p.target.reward;
                twGoldEl.textContent = twGold;
                bump(twGoldEl);
                const ms = Math.floor((twGold - 200) / 100);
                if (ms > twCoinMilestone) { addCoins(ms - twCoinMilestone, false); twCoinMilestone = ms; }
                for (let k = 0; k < 8; k++)
                    twParticles.push({x: p.target.x, y: p.target.y, vx: (Math.random() - 0.5) * 5, vy: (Math.random() - 0.5) * 5, life: 1, size: 2 + Math.random() * 2, color: '#ff5722'});
                const idx = twEnemies.indexOf(p.target);
                if (idx >= 0) twEnemies.splice(idx, 1);
                updateTowerTools();
            }
            twProjectiles.splice(i, 1);
        } else {
            p.x += (dx / dist) * move;
            p.y += (dy / dist) * move;
        }
    }
    for (let i = twParticles.length - 1; i >= 0; i--) {
        const p = twParticles[i];
        p.x += p.vx; p.y += p.vy; p.life -= 0.025;
        if (p.life <= 0) twParticles.splice(i, 1);
    }
    if (twWaveActive && twSpawnQueue.length === 0 && twEnemies.length === 0 && !twWaveCompletePending) {
        twWaveActive = false;
        twWaveCompletePending = true;
        const bonus = 30 + twWave * 5;
        twGold += bonus;
        twGoldEl.textContent = twGold;
        bump(twGoldEl);
        toast('🎉 Волна ' + twWave + ' пройдена! +' + bonus + '🪙 золота', 'success', 1600);
        if (twWave > twBest) {
            twBest = twWave;
            localStorage.setItem('tower_best', twBest);
            twBestEl.textContent = twBest;
            bump(twBestEl);
        }
        updateTowerTools();
    }
}
function gameOverTower() {
    stopTower();
    toast('Игра окончена! Волна: ' + twWave, 'info', 2000);
    setTimeout(() => { if (currentScreen === 'tower-screen') startTower(); }, 1200);
}
function drawTower() {
    const grad = twCtx.createLinearGradient(0, 0, 0, TW_H);
    grad.addColorStop(0, '#0f0602'); grad.addColorStop(1, '#050200');
    twCtx.fillStyle = grad;
    twCtx.fillRect(0, 0, TW_W, TW_H);
    twCtx.strokeStyle = 'rgba(255,193,7,0.3)';
    twCtx.lineWidth = 30;
    twCtx.lineCap = 'round';
    twCtx.lineJoin = 'round';
    twCtx.beginPath();
    twCtx.moveTo(TW_PATH[0].x, TW_PATH[0].y);
    for (let i = 1; i < TW_PATH.length; i++) twCtx.lineTo(TW_PATH[i].x, TW_PATH[i].y);
    twCtx.stroke();
    twCtx.fillStyle = '#4caf50';
    twCtx.beginPath(); twCtx.arc(TW_PATH[0].x, TW_PATH[0].y, 10, 0, Math.PI * 2); twCtx.fill();
    const end = TW_PATH[TW_PATH.length - 1];
    twCtx.fillStyle = '#f44336';
    twCtx.beginPath(); twCtx.arc(end.x, end.y, 12, 0, Math.PI * 2); twCtx.fill();
    twCtx.fillStyle = '#fff';
    twCtx.font = 'bold 12px system-ui';
    twCtx.textAlign = 'center';
    twCtx.textBaseline = 'middle';
    twCtx.fillText('🏠', end.x, end.y);
    for (const slot of TW_SLOTS) {
        if (slot.occupied) continue;
        twCtx.strokeStyle = 'rgba(255,255,255,0.2)';
        twCtx.lineWidth = 2;
        twCtx.setLineDash([4, 4]);
        twCtx.beginPath(); twCtx.arc(slot.x, slot.y, 18, 0, Math.PI * 2); twCtx.stroke();
        twCtx.setLineDash([]);
        if (twSelectedType) {
            const t = TOWER_TYPES[twSelectedType];
            twCtx.fillStyle = t.color;
            twCtx.globalAlpha = 0.15;
            twCtx.beginPath(); twCtx.arc(slot.x, slot.y, t.range, 0, Math.PI * 2); twCtx.fill();
            twCtx.globalAlpha = 1;
        }
    }
    for (const tower of twTowers) {
        twCtx.fillStyle = tower.color;
        twCtx.globalAlpha = 0.08;
        twCtx.beginPath(); twCtx.arc(tower.x, tower.y, tower.range, 0, Math.PI * 2); twCtx.fill();
        twCtx.globalAlpha = 1;
        twCtx.fillStyle = tower.color;
        twCtx.shadowColor = tower.color;
        twCtx.shadowBlur = 10;
        twCtx.beginPath(); twCtx.arc(tower.x, tower.y, 14, 0, Math.PI * 2); twCtx.fill();
        twCtx.shadowBlur = 0;
        twCtx.strokeStyle = '#333';
        twCtx.lineWidth = 4;
        twCtx.beginPath();
        twCtx.moveTo(tower.x, tower.y);
        twCtx.lineTo(tower.x + Math.cos(tower.angle) * 16, tower.y + Math.sin(tower.angle) * 16);
        twCtx.stroke();
        twCtx.fillStyle = '#000';
        twCtx.font = 'bold 12px system-ui';
        twCtx.textAlign = 'center';
        twCtx.textBaseline = 'middle';
        const icon = tower.type === 'arrow' ? '🏹' : tower.type === 'fire' ? '🔥' : '❄️';
        twCtx.fillText(icon, tower.x, tower.y);
    }
    for (const e of twEnemies) {
        const hpPct = e.hp / e.maxHp;
        const isSlowed = performance.now() < e.slowUntil;
        twCtx.fillStyle = isSlowed ? '#4fc3f7' : '#e53935';
        twCtx.shadowColor = twCtx.fillStyle;
        twCtx.shadowBlur = 8;
        twCtx.beginPath(); twCtx.arc(e.x, e.y, 12, 0, Math.PI * 2); twCtx.fill();
        twCtx.shadowBlur = 0;
        twCtx.fillStyle = '#fff';
        twCtx.beginPath(); twCtx.arc(e.x - 3, e.y - 2, 2, 0, Math.PI * 2); twCtx.fill();
        twCtx.beginPath(); twCtx.arc(e.x + 3, e.y - 2, 2, 0, Math.PI * 2); twCtx.fill();
        twCtx.fillStyle = 'rgba(0,0,0,0.5)';
        twCtx.fillRect(e.x - 12, e.y - 20, 24, 4);
        twCtx.fillStyle = hpPct > 0.5 ? '#4caf50' : hpPct > 0.25 ? '#ff9800' : '#f44336';
        twCtx.fillRect(e.x - 12, e.y - 20, 24 * hpPct, 4);
    }
    for (const p of twProjectiles) {
        twCtx.fillStyle = p.color;
        twCtx.shadowColor = p.color;
        twCtx.shadowBlur = 8;
        twCtx.beginPath(); twCtx.arc(p.x, p.y, 4, 0, Math.PI * 2); twCtx.fill();
        twCtx.shadowBlur = 0;
    }
    for (const p of twParticles) {
        twCtx.globalAlpha = p.life;
        twCtx.fillStyle = p.color;
        twCtx.beginPath(); twCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2); twCtx.fill();
    }
    twCtx.globalAlpha = 1;
    twCtx.fillStyle = '#ff5722';
    twCtx.font = 'bold 14px system-ui';
    twCtx.textAlign = 'left';
    twCtx.textBaseline = 'top';
    twCtx.fillText('🏠 ' + twLives, 8, 6);
    if (twWaveActive) {
        twCtx.fillStyle = '#ffd54f';
        twCtx.font = 'bold 13px system-ui';
        twCtx.textAlign = 'right';
        twCtx.fillText('Волна ' + twWave + ' идёт', TW_W - 8, 8);
    } else if (twWave > 0) {
        twCtx.fillStyle = '#4caf50';
        twCtx.font = 'bold 13px system-ui';
        twCtx.textAlign = 'right';
        twCtx.fillText('✅ Волна ' + twWave + ' пройдена', TW_W - 8, 8);
    }
}

/* ============================================================
   СВАЙПЫ + КЛИКИ
============================================================ */
function attachSwipe(canvas, handler, threshold = 24) {
    let sx = 0, sy = 0, tracking = false;
    canvas.addEventListener('touchstart', e => {
        const t = e.touches[0];
        sx = t.clientX; sy = t.clientY; tracking = true;
    }, {passive: true});
    canvas.addEventListener('touchend', e => {
        if (!tracking) return;
        tracking = false;
        const t = e.changedTouches[0];
        const dx = t.clientX - sx, dy = t.clientY - sy;
        if (Math.abs(dx) < threshold && Math.abs(dy) < threshold) return;
        if (Math.abs(dx) > Math.abs(dy)) handler(dx > 0 ? 'right' : 'left');
        else handler(dy > 0 ? 'down' : 'up');
    }, {passive: true});
}
attachSwipe(sCanvas, dir => {
    if (dir === 'up') setSnakeDir(0, -1);
    else if (dir === 'down') setSnakeDir(0, 1);
    else if (dir === 'left') setSnakeDir(-1, 0);
    else if (dir === 'right') setSnakeDir(1, 0);
});
attachSwipe(canvas2048, dir => move2048(dir));
attachSwipe(mCanvas, dir => moveMatch3(dir));
attachSwipe(tCanvas, dir => {
    if (dir === 'left') tetrisControl('left');
    else if (dir === 'right') tetrisControl('right');
    else if (dir === 'down') tetrisControl('drop');
    else if (dir === 'up') tetrisControl('rotate');
});
mCanvas.addEventListener('click', match3Click);
mCanvas.addEventListener('touchstart', e => {
    e.preventDefault();
    const t = e.changedTouches[0];
    match3Click({ clientX: t.clientX, clientY: t.clientY });
}, {passive: false});
pCanvas.addEventListener('click', puzzleClick);
pCanvas.addEventListener('touchstart', e => {
    e.preventDefault();
    const t = e.changedTouches[0];
    puzzleClick({ clientX: t.clientX, clientY: t.clientY });
}, {passive: false});
twCanvas.addEventListener('click', towerClick);
twCanvas.addEventListener('touchstart', e => {
    e.preventDefault();
    const t = e.changedTouches[0];
    towerClick({ clientX: t.clientX, clientY: t.clientY });
}, {passive: false});

/* ============================================================
   КЛАВИАТУРА
============================================================ */
document.addEventListener('keydown', e => {
    const dirMap = {'ArrowUp':'up','ArrowDown':'down','ArrowLeft':'left','ArrowRight':'right','w':'up','s':'down','a':'left','d':'right'};
    const dir = dirMap[e.key];
    if (!dir) {
        if (e.key === ' ' || e.key === 'Enter') {
            if (currentScreen === 'reaction-screen') reactionTap();
            else if (currentScreen === 'shooter-screen') shooterControl('fire');
            else if (currentScreen === 'arknoid-screen') arknoidControl('fire');
            else if (currentScreen === 'tetris-screen') tetrisControl('drop');
            else if (currentScreen === 'tower-screen') startTowerWave();
        }
        return;
    }
    e.preventDefault();
    if (currentScreen === 'snake-screen') {
        if (dir === 'up') setSnakeDir(0, -1);
        else if (dir === 'down') setSnakeDir(0, 1);
        else if (dir === 'left') setSnakeDir(-1, 0);
        else if (dir === 'right') setSnakeDir(1, 0);
    } else if (currentScreen === 'game2048-screen') move2048(dir);
    else if (currentScreen === 'match3-screen') moveMatch3(dir);
    else if (currentScreen === 'shooter-screen') {
        if (dir === 'left') shooterControl('left');
        else if (dir === 'right') shooterControl('right');
    } else if (currentScreen === 'arknoid-screen') {
        if (dir === 'left') arknoidControl('left');
        else if (dir === 'right') arknoidControl('right');
    } else if (currentScreen === 'tetris-screen') {
        if (dir === 'left') tetrisControl('left');
        else if (dir === 'right') tetrisControl('right');
        else if (dir === 'down') tetrisControl('drop');
        else if (dir === 'up') tetrisControl('rotate');
    }
});
document.addEventListener('keyup', e => {
    if (currentScreen === 'shooter-screen' && (e.key === 'ArrowLeft' || e.key === 'ArrowRight' || e.key === 'a' || e.key === 'd')) shDir = 0;
    if (currentScreen === 'arknoid-screen' && (e.key === 'ArrowLeft' || e.key === 'ArrowRight' || e.key === 'a' || e.key === 'd')) arkDir = 0;
});

/* ============================================================
   СТАРТ
============================================================ */
updateBestScoresUI();
updatePremiumTags();
updateTowerTools();
</script>
</body>
</html>
