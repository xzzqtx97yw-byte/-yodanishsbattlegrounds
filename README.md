<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>1v1 Battlegrounds - Clean UI</title>
<style>
  :root {
    --bg: #08090d;
    --panel: rgba(12, 14, 20, .86);
    --panel-strong: rgba(19, 23, 32, .96);
    --border: rgba(255, 255, 255, .12);
    --border-strong: rgba(255, 255, 255, .22);
    --text: #f5f7fb;
    --muted: #a9b1c2;
    --soft: #d8deea;
    --gold: #f7c948;
    --p1: #f05252;
    --p2: #35d07f;
    --blue: #38bdf8;
    --shadow: 0 24px 80px rgba(0, 0, 0, .48);
    --tight-shadow: 0 14px 44px rgba(0, 0, 0, .34);
  }

  * {
    box-sizing: border-box;
  }

  html,
  body {
    width: 100%;
    height: 100%;
  }

  body {
    margin: 0;
    overflow: hidden;
    background:
      linear-gradient(150deg, rgba(240, 82, 82, .09), transparent 30%),
      linear-gradient(25deg, transparent 60%, rgba(53, 208, 127, .08)),
      var(--bg);
    color: var(--text);
    font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Arial, sans-serif;
  }

  button {
    font: inherit;
  }

  .stage {
    position: fixed;
    inset: 0;
    z-index: 0;
    background:
      linear-gradient(180deg, rgba(19, 23, 32, .94), rgba(7, 8, 12, .98)),
      repeating-linear-gradient(90deg, rgba(255, 255, 255, .030) 0 1px, transparent 1px 64px),
      repeating-linear-gradient(0deg, rgba(255, 255, 255, .022) 0 1px, transparent 1px 64px);
  }

  .stage::after {
    content: "";
    position: absolute;
    inset: 0;
    background:
      linear-gradient(120deg, transparent 0 42%, rgba(247, 201, 72, .06) 42% 44%, transparent 44%),
      linear-gradient(300deg, transparent 0 54%, rgba(56, 189, 248, .05) 54% 56%, transparent 56%);
    pointer-events: none;
  }

  canvas {
    position: relative;
    display: block;
    width: 100vw;
    height: 100vh;
  }

  .ui {
    position: fixed;
    inset: 0;
    z-index: 2;
    pointer-events: none;
  }

  .screen {
    position: absolute;
    inset: 0;
    z-index: 40;
    display: grid;
    place-items: center;
    padding: 28px;
    background:
      linear-gradient(180deg, rgba(8, 9, 13, .70), rgba(8, 9, 13, .54)),
      rgba(8, 9, 13, .32);
    opacity: 0;
    visibility: hidden;
    transform: scale(.98);
    transition:
      opacity 240ms ease,
      transform 240ms ease,
      visibility 0s linear 240ms;
    pointer-events: none;
  }

  .screen.active {
    opacity: 1;
    visibility: visible;
    transform: scale(1);
    transition-delay: 0s;
    pointer-events: auto;
    backdrop-filter: blur(10px);
  }

  .menu-shell,
  .end-shell {
    position: relative;
    width: min(600px, calc(100vw - 40px));
    padding: 32px;
    border: 1px solid var(--border-strong);
    border-radius: 8px;
    background:
      linear-gradient(180deg, rgba(255, 255, 255, .055), rgba(255, 255, 255, .020)),
      rgba(12, 14, 20, .96);
    box-shadow: var(--shadow);
    backdrop-filter: blur(14px);
    overflow: hidden;
  }

  .menu-shell::before,
  .end-shell::before {
    content: "";
    position: absolute;
    inset: 0 0 auto;
    height: 3px;
    background: linear-gradient(90deg, var(--p1), var(--gold), var(--p2));
    pointer-events: none;
  }

  .screen.active .menu-shell,
  .screen.active .end-shell,
  .screen.active .select-header,
  .screen.active .fighter-card {
    animation: panel-in 260ms ease both;
  }

  .brand-mark {
    width: 62px;
    height: 62px;
    margin: 0 auto 20px;
    border: 1px solid rgba(255, 255, 255, .24);
    border-radius: 8px;
    background:
      linear-gradient(135deg, var(--p1), transparent 48%),
      linear-gradient(315deg, var(--p2), transparent 48%),
      #11151d;
    box-shadow:
      inset 0 0 0 1px rgba(255, 255, 255, .10),
      0 0 32px rgba(247, 201, 72, .18);
  }

  .title {
    margin: 0;
    text-align: center;
    font-size: 50px;
    line-height: .95;
    letter-spacing: 0;
  }

  .subtitle {
    margin: 16px auto 28px;
    max-width: 28rem;
    color: var(--soft);
    text-align: center;
    line-height: 1.55;
  }

  .meta-row {
    display: flex;
    justify-content: center;
    gap: 8px;
    flex-wrap: wrap;
    margin: 0 0 26px;
  }

  .meta-chip {
    display: inline-flex;
    align-items: center;
    min-height: 30px;
    padding: 6px 10px;
    border: 1px solid rgba(255, 255, 255, .12);
    border-radius: 8px;
    background: rgba(255, 255, 255, .055);
    color: var(--muted);
    font-size: 12px;
    font-weight: 900;
    text-transform: uppercase;
  }

  .actions {
    display: flex;
    justify-content: center;
    gap: 12px;
    flex-wrap: wrap;
  }

  .btn {
    min-height: 50px;
    min-width: 138px;
    border: 1px solid rgba(255, 255, 255, .14);
    border-radius: 8px;
    padding: 13px 20px;
    color: var(--text);
    background:
      linear-gradient(180deg, rgba(255, 255, 255, .075), rgba(255, 255, 255, .020)),
      var(--panel-strong);
    cursor: pointer;
    font-weight: 900;
    letter-spacing: 0;
    transition:
      transform 120ms ease,
      box-shadow 160ms ease,
      border-color 160ms ease,
      background 160ms ease;
  }

  .btn.primary {
    color: #111318;
    border-color: rgba(247, 201, 72, .75);
    background: linear-gradient(180deg, #ffe68a, var(--gold));
    box-shadow: 0 12px 30px rgba(247, 201, 72, .22);
  }

  .btn:hover {
    transform: translateY(-2px);
    border-color: rgba(255, 255, 255, .32);
    box-shadow: 0 14px 34px rgba(0, 0, 0, .38);
  }

  .btn:active {
    transform: translateY(1px) scale(.98);
    box-shadow: 0 6px 16px rgba(0, 0, 0, .26);
  }

  .select-screen {
    align-content: start;
    justify-items: center;
    grid-template-rows: auto minmax(0, 1fr);
    gap: 16px;
    padding: 26px;
    overflow: hidden;
  }

  .select-header {
    width: min(1040px, 100%);
    display: flex;
    justify-content: space-between;
    align-items: end;
    gap: 16px;
    padding: 16px 18px;
    border: 1px solid var(--border);
    border-radius: 8px;
    background:
      linear-gradient(180deg, rgba(255, 255, 255, .055), rgba(255, 255, 255, .020)),
      rgba(12, 14, 20, .92);
    box-shadow: var(--tight-shadow);
    backdrop-filter: blur(12px);
  }

  .select-title {
    margin: 0;
    font-size: 32px;
    line-height: 1;
    letter-spacing: 0;
  }

  .select-actions {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
    justify-content: flex-end;
  }

  .select-layout {
    width: min(1040px, 100%);
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 18px;
    min-height: 0;
    max-height: 100%;
    overflow: auto;
    padding: 2px 6px 10px 2px;
    scrollbar-width: thin;
    scrollbar-color: rgba(255, 255, 255, .24) transparent;
  }

  .fighter-column {
    min-width: 0;
    padding: 12px;
    border: 1px solid rgba(255, 255, 255, .10);
    border-radius: 8px;
    background: rgba(8, 10, 15, .42);
    box-shadow: 0 12px 34px rgba(0, 0, 0, .18);
  }

  .player-label {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 12px;
    color: var(--soft);
    font-size: 14px;
    font-weight: 800;
    text-transform: uppercase;
  }

  .player-dot {
    width: 14px;
    height: 14px;
    border-radius: 50%;
    box-shadow: 0 0 18px currentColor;
  }

  .fighter-grid {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 12px;
  }

  .fighter-card {
    min-width: 0;
    display: flex;
    flex-direction: column;
    border: 1px solid rgba(255, 255, 255, .12);
    border-radius: 8px;
    padding: 10px;
    color: var(--text);
    background:
      linear-gradient(180deg, rgba(255, 255, 255, .050), rgba(255, 255, 255, .015)),
      rgba(17, 20, 28, .96);
    cursor: pointer;
    overflow: hidden;
    isolation: isolate;
    text-align: left;
    transition:
      transform 150ms ease,
      border-color 150ms ease,
      background 150ms ease,
      box-shadow 150ms ease;
  }

  .fighter-card:hover {
    transform: translateY(-2px);
    border-color: rgba(255, 255, 255, .28);
    box-shadow: 0 12px 28px rgba(0, 0, 0, .26);
  }

  .fighter-card.selected {
    border-color: var(--gold);
    background: rgba(247, 201, 72, .12);
    box-shadow:
      inset 0 0 0 1px rgba(247, 201, 72, .24),
      0 14px 34px rgba(247, 201, 72, .10);
  }

  .portrait {
    height: 70px;
    border: 1px solid rgba(255, 255, 255, .12);
    border-radius: 8px;
    margin-bottom: 8px;
    background:
      linear-gradient(135deg, color-mix(in srgb, var(--fighter-color), white 10%), transparent 50%),
      linear-gradient(315deg, color-mix(in srgb, var(--fighter-accent), white 8%), transparent 52%),
      #11151c;
    position: relative;
    overflow: hidden;
    flex: 0 0 auto;
  }

  .portrait::before,
  .portrait::after {
    content: "";
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(255, 255, 255, .88);
    pointer-events: none;
    z-index: 1;
  }

  .portrait::before {
    top: 14px;
    width: 26px;
    height: 26px;
    border-radius: 50%;
  }

  .portrait::after {
    top: 39px;
    width: 54px;
    height: 22px;
    border-radius: 18px 18px 6px 6px;
  }

  .portrait-mark {
    position: absolute;
    right: 8px;
    bottom: 6px;
    z-index: 2;
    color: rgba(255, 255, 255, .92);
    font-size: 18px;
    font-weight: 1000;
    letter-spacing: 0;
    text-shadow: 0 2px 10px rgba(0, 0, 0, .42);
  }

  .fighter-info {
    position: relative;
    z-index: 1;
    padding: 10px;
    border: 1px solid rgba(255, 255, 255, .08);
    border-radius: 8px;
    background: rgba(8, 10, 15, .74);
  }

  .fighter-name {
    display: block;
    margin-bottom: 8px;
    font-size: 17px;
    font-weight: 900;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .stat-line {
    display: grid;
    grid-template-columns: 46px 1fr;
    gap: 8px;
    align-items: center;
    margin-top: 6px;
    color: var(--muted);
    font-size: 12px;
    font-weight: 800;
  }

  .fighter-move {
    display: block;
    margin-top: 8px;
    color: color-mix(in srgb, var(--fighter-accent), white 22%);
    font-size: 11px;
    font-weight: 900;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    text-transform: uppercase;
  }

  .stat-track {
    height: 6px;
    border-radius: 4px;
    background: rgba(255, 255, 255, .10);
    overflow: hidden;
  }

  .stat-fill {
    display: block;
    height: 100%;
    border-radius: inherit;
    background: var(--fighter-accent);
  }

  .hud {
    position: absolute;
    left: 16px;
    right: 16px;
    top: 14px;
    z-index: 30;
    display: grid;
    grid-template-columns: minmax(180px, 1fr) auto minmax(180px, 1fr);
    gap: 14px;
    align-items: start;
    opacity: 0;
    transform: translateY(-12px);
    transition: opacity 220ms ease, transform 220ms ease;
  }

  .hud.visible {
    opacity: 1;
    transform: translateY(0);
    pointer-events: none;
  }

  .hud-card {
    min-width: 0;
    padding: 12px;
    border: 1px solid rgba(255, 255, 255, .14);
    border-radius: 8px;
    background:
      linear-gradient(180deg, rgba(255, 255, 255, .060), rgba(255, 255, 255, .020)),
      rgba(8, 10, 15, .76);
    box-shadow: 0 14px 34px rgba(0, 0, 0, .30);
    backdrop-filter: blur(10px);
  }

  .hud-card.low {
    border-color: rgba(240, 82, 82, .55);
    animation: low-health 740ms ease-in-out infinite alternate;
  }

  .hud-card.p2 {
    text-align: right;
  }

  .hud-top {
    display: flex;
    justify-content: space-between;
    gap: 12px;
    margin-bottom: 8px;
    color: var(--muted);
    font-size: 13px;
    font-weight: 900;
  }

  .hud-top span {
    min-width: 0;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .hud-card.p2 .hud-top {
    flex-direction: row-reverse;
  }

  .health-track,
  .cooldown-track {
    width: 100%;
    overflow: hidden;
    background: rgba(255, 255, 255, .10);
  }

  .health-track {
    height: 18px;
    border: 1px solid rgba(255, 255, 255, .10);
    border-radius: 6px;
  }

  .cooldown-track {
    height: 5px;
    margin-top: 7px;
    border-radius: 4px;
  }

  .health-fill,
  .cooldown-fill {
    display: block;
    width: 100%;
    height: 100%;
    transform-origin: left center;
  }

  .hud-card.p2 .health-fill,
  .hud-card.p2 .cooldown-fill {
    transform-origin: right center;
  }

  .camera-labels {
    position: absolute;
    left: 0;
    right: 0;
    top: 84px;
    z-index: 29;
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 28px;
    padding: 0 16px;
    opacity: 0;
    transform: translateY(-8px);
    transition: opacity 180ms ease, transform 180ms ease;
    pointer-events: none;
  }

  .camera-labels.visible {
    opacity: 1;
    transform: translateY(0);
  }

  .camera-label {
    justify-self: start;
    padding: 8px 12px;
    border: 1px solid rgba(255, 255, 255, .16);
    border-radius: 8px;
    background: rgba(10, 12, 17, .80);
    box-shadow: 0 10px 28px rgba(0, 0, 0, .24);
    color: var(--muted);
    font-size: 12px;
    font-weight: 1000;
    text-transform: uppercase;
    backdrop-filter: blur(10px);
  }

  .camera-label.p1-view {
    color: color-mix(in srgb, var(--p1), white 24%);
  }

  .camera-label.p2-view {
    justify-self: end;
    color: color-mix(in srgb, var(--p2), white 18%);
  }

  .health-fill {
    transform: scaleX(1);
    transition: transform 320ms cubic-bezier(.2, .8, .2, 1);
  }

  .health-fill.hit {
    animation: health-hit 300ms ease;
  }

  .cooldown-fill {
    background: var(--gold);
    transform: scaleX(1);
  }

  .p1 .health-fill {
    background: linear-gradient(90deg, var(--p1), #f7c948);
  }

  .p2 .health-fill {
    background: linear-gradient(90deg, #35d07f, #38bdf8);
  }

  .round-chip {
    min-width: 104px;
    padding: 10px 14px;
    border: 1px solid rgba(255, 255, 255, .14);
    border-radius: 8px;
    background:
      linear-gradient(180deg, rgba(247, 201, 72, .11), rgba(255, 255, 255, .020)),
      rgba(8, 10, 15, .82);
    color: var(--gold);
    text-align: center;
    font-weight: 1000;
    box-shadow: 0 12px 30px rgba(0, 0, 0, .26);
    backdrop-filter: blur(10px);
  }

  .controls-panel {
    position: absolute;
    bottom: 16px;
    z-index: 28;
    width: 244px;
    padding: 12px;
    border: 1px solid rgba(255, 255, 255, .14);
    border-radius: 8px;
    background:
      linear-gradient(180deg, rgba(255, 255, 255, .055), rgba(255, 255, 255, .018)),
      rgba(10, 12, 17, .86);
    box-shadow: 0 16px 38px rgba(0, 0, 0, .32);
    backdrop-filter: blur(10px);
    opacity: 0;
    transform: translateY(14px);
    transition: opacity 180ms ease, transform 180ms ease;
    pointer-events: none;
  }

  .controls-panel.visible {
    opacity: .96;
    transform: translateY(0);
  }

  .controls-panel.p1-controls {
    left: 16px;
  }

  .controls-panel.p2-controls {
    right: 16px;
  }

  .controls-panel.hidden {
    opacity: 0;
    transform: translateY(12px);
  }

  .controls-title {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
    margin-bottom: 10px;
    color: var(--muted);
    font-size: 12px;
    font-weight: 900;
    text-transform: uppercase;
  }

  .controls-dot {
    display: inline-block;
    width: 10px;
    height: 10px;
    margin-right: 6px;
    border-radius: 50%;
    background: currentColor;
    box-shadow: 0 0 18px currentColor;
    vertical-align: -1px;
  }

  .key-layout {
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 12px;
    align-items: end;
  }

  .move-keys {
    display: grid;
    grid-template-columns: repeat(3, 34px);
    grid-template-rows: repeat(2, 34px);
    gap: 6px;
  }

  .key {
    min-width: 34px;
    height: 34px;
    display: inline-grid;
    place-items: center;
    border: 1px solid rgba(255, 255, 255, .16);
    border-radius: 6px;
    background: rgba(255, 255, 255, .07);
    color: var(--text);
    font-size: 12px;
    font-weight: 1000;
    line-height: 1;
    transition: transform 90ms ease, background 90ms ease, border-color 90ms ease, color 90ms ease;
  }

  .key.empty {
    opacity: 0;
  }

  .key.wide {
    min-width: 54px;
    padding: 0 10px;
  }

  .key.pressed {
    transform: translateY(2px) scale(.96);
    border-color: var(--gold);
    background: var(--gold);
    color: #111318;
  }

  .attack-keys {
    display: flex;
    flex-direction: column;
    gap: 6px;
    align-items: flex-end;
  }

  .attack-label {
    color: var(--muted);
    font-size: 11px;
    font-weight: 900;
    text-transform: uppercase;
  }

  .moves-panel {
    position: absolute;
    top: 50%;
    z-index: 28;
    width: 238px;
    padding: 12px;
    border: 1px solid rgba(255, 255, 255, .14);
    border-radius: 8px;
    background:
      linear-gradient(180deg, rgba(255, 255, 255, .055), rgba(255, 255, 255, .018)),
      rgba(10, 12, 17, .84);
    box-shadow: 0 16px 38px rgba(0, 0, 0, .30);
    backdrop-filter: blur(10px);
    opacity: 0;
    transform: translateY(-50%) translateX(-10px);
    transition: opacity 180ms ease, transform 180ms ease;
    pointer-events: none;
  }

  .moves-panel.visible {
    opacity: .96;
    transform: translateY(-50%) translateX(0);
  }

  .moves-panel.p1-moves {
    left: 16px;
  }

  .moves-panel.p2-moves {
    right: 16px;
    transform: translateY(-50%) translateX(10px);
  }

  .moves-panel.p2-moves.visible {
    transform: translateY(-50%) translateX(0);
  }

  .moves-title {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 10px;
    margin-bottom: 10px;
    color: var(--muted);
    font-size: 12px;
    font-weight: 1000;
    text-transform: uppercase;
  }

  .moves-title span {
    min-width: 0;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .move-list {
    display: grid;
    gap: 8px;
  }

  .move-row {
    display: grid;
    grid-template-columns: 36px 1fr;
    gap: 10px;
    align-items: center;
    min-height: 50px;
    padding: 8px;
    border: 1px solid rgba(255, 255, 255, .10);
    border-radius: 8px;
    background: rgba(255, 255, 255, .055);
    transition: border-color 120ms ease, background 120ms ease;
  }

  .move-row.ready {
    border-color: color-mix(in srgb, var(--move-color), white 14%);
    background: rgba(255, 255, 255, .075);
  }

  .move-icon {
    width: 36px;
    height: 36px;
    display: grid;
    place-items: center;
    border: 1px solid rgba(255, 255, 255, .16);
    border-radius: 8px;
    color: #111318;
    background: var(--move-color, var(--gold));
    font-size: 14px;
    font-weight: 1000;
  }

  .move-copy {
    min-width: 0;
  }

  .move-name {
    display: flex;
    justify-content: space-between;
    gap: 8px;
    min-width: 0;
    color: var(--text);
    font-size: 13px;
    font-weight: 1000;
    line-height: 1.1;
  }

  .move-name span:first-child {
    min-width: 0;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .move-key {
    color: var(--muted);
    font-size: 11px;
  }

  .move-track {
    height: 6px;
    margin-top: 8px;
    border-radius: 6px;
    background: rgba(255, 255, 255, .10);
    overflow: hidden;
  }

  .move-fill {
    display: block;
    width: 100%;
    height: 100%;
    border-radius: inherit;
    background: var(--move-color, var(--gold));
    transform-origin: left center;
    transform: scaleX(1);
  }

  .p2-moves .move-fill {
    transform-origin: right center;
  }

  .countdown-number {
    min-width: 190px;
    min-height: 190px;
    display: grid;
    place-items: center;
    color: var(--gold);
    border: 1px solid rgba(247, 201, 72, .35);
    border-radius: 8px;
    background: rgba(8, 9, 13, .58);
    box-shadow: var(--shadow);
    font-size: 84px;
    font-weight: 1000;
    backdrop-filter: blur(10px);
  }

  .countdown-number.pop {
    animation: countdown-pop 360ms cubic-bezier(.2, .9, .2, 1);
  }

  .winner-name {
    margin: 0 0 10px;
    text-align: center;
    font-size: 42px;
    line-height: 1.05;
  }

  .winner-meta {
    margin: 0 0 24px;
    color: var(--muted);
    text-align: center;
  }

  @keyframes countdown-pop {
    from {
      opacity: .2;
      transform: scale(.72);
    }
    to {
      opacity: 1;
      transform: scale(1);
    }
  }

  @keyframes health-hit {
    0% { filter: brightness(2); }
    100% { filter: brightness(1); }
  }

  @keyframes panel-in {
    from {
      opacity: 0;
      transform: translateY(8px) scale(.985);
    }
    to {
      opacity: 1;
      transform: translateY(0) scale(1);
    }
  }

  @keyframes low-health {
    from { box-shadow: 0 12px 30px rgba(0, 0, 0, .24); }
    to { box-shadow: 0 12px 34px rgba(240, 82, 82, .22); }
  }

  @media (max-width: 820px) {
    .title {
      font-size: 36px;
    }

    .screen {
      padding: 18px;
    }

    .select-header {
      align-items: stretch;
      flex-direction: column;
    }

    .select-actions {
      justify-content: flex-start;
    }

    .select-layout {
      grid-template-columns: 1fr;
      overflow: auto;
      max-height: 100%;
      padding-right: 4px;
    }

    .fighter-grid {
      grid-template-columns: 1fr;
    }

    .hud {
      grid-template-columns: 1fr;
      gap: 8px;
    }

    .round-chip {
      order: -1;
      justify-self: center;
    }

    .camera-labels {
      top: 164px;
      gap: 12px;
    }

    .camera-label {
      font-size: 11px;
      padding: 7px 9px;
    }

    .hud-card.p2,
    .hud-card.p2 .hud-top {
      text-align: left;
      flex-direction: row;
    }

    .hud-card.p2 .health-fill,
    .hud-card.p2 .cooldown-fill {
      transform-origin: left center;
    }

    .controls-panel {
      width: calc(50vw - 24px);
      min-width: 156px;
      padding: 10px;
    }

    .moves-panel {
      top: auto;
      bottom: 172px;
      width: calc(50vw - 24px);
      min-width: 156px;
      padding: 10px;
      transform: translateY(8px);
    }

    .moves-panel.visible,
    .moves-panel.p2-moves.visible {
      transform: translateY(0);
    }

    .move-row {
      grid-template-columns: 30px 1fr;
      gap: 8px;
      min-height: 44px;
      padding: 7px;
    }

    .move-icon {
      width: 30px;
      height: 30px;
      font-size: 12px;
    }

    .move-name {
      display: block;
      font-size: 12px;
    }

    .key-layout {
      grid-template-columns: 1fr;
      gap: 8px;
    }

    .attack-keys {
      align-items: flex-start;
      flex-direction: row;
      flex-wrap: wrap;
    }

    .move-keys {
      grid-template-columns: repeat(3, 30px);
      grid-template-rows: repeat(2, 30px);
    }

    .key {
      min-width: 30px;
      height: 30px;
    }

    .key.wide {
      min-width: 46px;
      padding: 0 8px;
    }
  }

  @media (max-height: 650px) {
    .screen {
      padding: 14px;
    }

    .menu-shell,
    .end-shell {
      padding: 24px;
    }

    .brand-mark {
      width: 44px;
      height: 44px;
      margin-bottom: 12px;
    }

    .title {
      font-size: 40px;
    }

    .subtitle {
      margin: 12px auto 20px;
    }

    .controls-panel {
      bottom: 10px;
      width: 216px;
      padding: 9px;
    }

    .moves-panel {
      width: 210px;
      padding: 9px;
      max-height: calc(100vh - 220px);
      overflow: hidden;
    }

    .camera-labels {
      top: 94px;
    }

    .move-keys {
      grid-template-columns: repeat(3, 30px);
      grid-template-rows: repeat(2, 30px);
    }

    .key {
      min-width: 30px;
      height: 30px;
    }
  }
</style>
</head>
<body>
<div class="stage">
  <canvas id="game" aria-label="1v1 battleground"></canvas>
</div>

<div class="ui" aria-live="polite">
  <header class="hud" id="hud">
    <div class="hud-card p1">
      <div class="hud-top">
        <span id="p1Name">Player 1</span>
        <span id="p1HpText">100</span>
      </div>
      <div class="health-track"><span class="health-fill" id="p1Health"></span></div>
      <div class="cooldown-track"><span class="cooldown-fill" id="p1Cooldown"></span></div>
    </div>
    <div class="round-chip" id="roundChip">ROUND 1</div>
    <div class="hud-card p2">
      <div class="hud-top">
        <span id="p2Name">Player 2</span>
        <span id="p2HpText">100</span>
      </div>
      <div class="health-track"><span class="health-fill" id="p2Health"></span></div>
      <div class="cooldown-track"><span class="cooldown-fill" id="p2Cooldown"></span></div>
    </div>
  </header>

  <div class="camera-labels" id="cameraLabels" aria-hidden="true">
    <span class="camera-label p1-view">Player 1 Camera</span>
    <span class="camera-label p2-view">Player 2 Camera</span>
  </div>

  <aside class="moves-panel p1-moves" id="p1Moves">
    <div class="moves-title">
      <span>Player 1 Moves</span>
      <span id="p1FighterMoveName">Vanguard</span>
    </div>
    <div class="move-list" id="p1MoveList"></div>
  </aside>

  <aside class="moves-panel p2-moves" id="p2Moves">
    <div class="moves-title">
      <span>Player 2 Moves</span>
      <span id="p2FighterMoveName">Sentinel</span>
    </div>
    <div class="move-list" id="p2MoveList"></div>
  </aside>

  <aside class="controls-panel p1-controls" id="p1Controls">
    <div class="controls-title">
      <span><span class="controls-dot" style="color: var(--p1)"></span> Player 1</span>
      <span>Attack</span>
    </div>
    <div class="key-layout">
      <div class="move-keys" aria-label="Player 1 movement controls">
        <span class="key empty" aria-hidden="true"></span>
        <kbd class="key" data-code="KeyW">W</kbd>
        <span class="key empty" aria-hidden="true"></span>
        <kbd class="key" data-code="KeyA">A</kbd>
        <kbd class="key" data-code="KeyS">S</kbd>
        <kbd class="key" data-code="KeyD">D</kbd>
      </div>
      <div class="attack-keys">
        <kbd class="key wide" data-code="KeyF">F</kbd>
        <kbd class="key wide" data-code="Space">SPACE</kbd>
      </div>
    </div>
  </aside>

  <aside class="controls-panel p2-controls" id="p2Controls">
    <div class="controls-title">
      <span><span class="controls-dot" style="color: var(--p2)"></span> Player 2</span>
      <span>Attack</span>
    </div>
    <div class="key-layout">
      <div class="move-keys" aria-label="Player 2 movement controls">
        <span class="key empty" aria-hidden="true"></span>
        <kbd class="key" data-code="ArrowUp">↑</kbd>
        <span class="key empty" aria-hidden="true"></span>
        <kbd class="key" data-code="ArrowLeft">←</kbd>
        <kbd class="key" data-code="ArrowDown">↓</kbd>
        <kbd class="key" data-code="ArrowRight">→</kbd>
      </div>
      <div class="attack-keys">
        <kbd class="key wide" data-code="KeyM">M</kbd>
        <kbd class="key wide" data-code="KeyN">N</kbd>
      </div>
    </div>
  </aside>

  <section class="screen active" id="screen-menu">
    <div class="menu-shell">
      <div class="brand-mark" aria-hidden="true"></div>
      <h1 class="title">1v1 Battlegrounds</h1>
      <p class="subtitle">Two fighters enter a shifting arena. One walks out.</p>
      <div class="meta-row" aria-hidden="true">
        <span class="meta-chip">Split Cameras</span>
        <span class="meta-chip">6 Fighters</span>
        <span class="meta-chip">Local Duel</span>
      </div>
      <div class="actions">
        <button class="btn primary" id="playBtn" type="button">Choose Fighters</button>
      </div>
    </div>
  </section>

  <section class="screen select-screen" id="screen-select">
    <div class="select-header">
      <h2 class="select-title">Choose Fighters</h2>
      <div class="select-actions">
        <button class="btn" id="backBtn" type="button">Back</button>
        <button class="btn primary" id="startMatchBtn" type="button">Start Match</button>
      </div>
    </div>
    <div class="select-layout" id="characterSelect"></div>
  </section>

  <section class="screen" id="screen-countdown">
    <div class="countdown-number" id="countdownText">3</div>
  </section>

  <section class="screen" id="screen-end">
    <div class="end-shell">
      <h2 class="winner-name" id="winnerName">Player Wins</h2>
      <p class="winner-meta" id="winnerMeta">Clean finish.</p>
      <div class="actions">
        <button class="btn primary" id="rematchBtn" type="button">Rematch</button>
        <button class="btn" id="fightersBtn" type="button">Fighters</button>
      </div>
    </div>
  </section>
</div>

<script>
(() => {
  "use strict";

  const canvas = document.getElementById("game");
  const ctx = canvas.getContext("2d");

  const ui = {
    hud: document.getElementById("hud"),
    roundChip: document.getElementById("roundChip"),
    playBtn: document.getElementById("playBtn"),
    backBtn: document.getElementById("backBtn"),
    startMatchBtn: document.getElementById("startMatchBtn"),
    rematchBtn: document.getElementById("rematchBtn"),
    fightersBtn: document.getElementById("fightersBtn"),
    characterSelect: document.getElementById("characterSelect"),
    countdownText: document.getElementById("countdownText"),
    winnerName: document.getElementById("winnerName"),
    winnerMeta: document.getElementById("winnerMeta"),
    p1Name: document.getElementById("p1Name"),
    p2Name: document.getElementById("p2Name"),
    p1HpText: document.getElementById("p1HpText"),
    p2HpText: document.getElementById("p2HpText"),
    p1Health: document.getElementById("p1Health"),
    p2Health: document.getElementById("p2Health"),
    p1Cooldown: document.getElementById("p1Cooldown"),
    p2Cooldown: document.getElementById("p2Cooldown"),
    cameraLabels: document.getElementById("cameraLabels"),
    p1Card: document.querySelector(".hud-card.p1"),
    p2Card: document.querySelector(".hud-card.p2"),
    p1Moves: document.getElementById("p1Moves"),
    p2Moves: document.getElementById("p2Moves"),
    p1Controls: document.getElementById("p1Controls"),
    p2Controls: document.getElementById("p2Controls"),
    p1MoveList: document.getElementById("p1MoveList"),
    p2MoveList: document.getElementById("p2MoveList"),
    p1FighterMoveName: document.getElementById("p1FighterMoveName"),
    p2FighterMoveName: document.getElementById("p2FighterMoveName"),
    keyHints: Array.from(document.querySelectorAll(".key[data-code]")),
    screens: {
      menu: document.getElementById("screen-menu"),
      select: document.getElementById("screen-select"),
      countdown: document.getElementById("screen-countdown"),
      end: document.getElementById("screen-end")
    }
  };

  const PLAYER_COLORS = ["#f05252", "#35d07f"];
  const CONTROLS = [
    { up: "KeyW", down: "KeyS", left: "KeyA", right: "KeyD", primary: "KeyF", special: "Space" },
    { up: "ArrowUp", down: "ArrowDown", left: "ArrowLeft", right: "ArrowRight", primary: "KeyM", special: "KeyN" }
  ];

  const CONTROL_LABELS = [
    { primary: "F", special: "SPACE" },
    { primary: "M", special: "N" }
  ];

  const FIGHTERS = {
    vanguard: {
      name: "Vanguard",
      accent: "#f7c948",
      health: 112,
      speed: 252,
      damage: 16,
      range: 62,
      cooldown: .46,
      radius: 20,
      moves: {
        primary: { name: "Guard Break", icon: "GB", damage: 16, range: 62, cooldown: .46, width: 1.35, duration: .20, activeUntil: .12, lunge: 72, recoil: 42, knockback: 430, hitStop: .045 },
        special: { name: "Shield Rush", icon: "SR", damage: 22, range: 78, cooldown: 1.25, width: 1.15, duration: .28, activeUntil: .16, lunge: 210, recoil: 28, knockback: 560, hitStop: .060 }
      }
    },
    striker: {
      name: "Striker",
      accent: "#38bdf8",
      health: 92,
      speed: 306,
      damage: 12,
      range: 70,
      cooldown: .32,
      radius: 18,
      moves: {
        primary: { name: "Snap Jab", icon: "SJ", damage: 12, range: 70, cooldown: .32, width: 1.20, duration: .17, activeUntil: .10, lunge: 86, recoil: 36, knockback: 360, hitStop: .035 },
        special: { name: "Flash Step", icon: "FS", damage: 17, range: 92, cooldown: 1.05, width: .95, duration: .22, activeUntil: .14, lunge: 255, recoil: 20, knockback: 440, hitStop: .045 }
      }
    },
    sentinel: {
      name: "Sentinel",
      accent: "#f97316",
      health: 132,
      speed: 224,
      damage: 19,
      range: 56,
      cooldown: .56,
      radius: 22,
      moves: {
        primary: { name: "Iron Swing", icon: "IS", damage: 19, range: 56, cooldown: .56, width: 1.45, duration: .22, activeUntil: .13, lunge: 58, recoil: 50, knockback: 500, hitStop: .055 },
        special: { name: "Ground Slam", icon: "GS", damage: 25, range: 68, cooldown: 1.45, width: 1.90, duration: .34, activeUntil: .20, lunge: 92, recoil: 36, knockback: 660, hitStop: .075 }
      }
    },
    ronin: {
      name: "Ronin",
      accent: "#fb7185",
      health: 98,
      speed: 286,
      damage: 14,
      range: 82,
      cooldown: .40,
      radius: 18,
      moves: {
        primary: { name: "Quick Draw", icon: "QD", damage: 14, range: 82, cooldown: .40, width: 1.00, duration: .18, activeUntil: .11, lunge: 98, recoil: 32, knockback: 390, hitStop: .040 },
        special: { name: "Cross Cut", icon: "CC", damage: 20, range: 96, cooldown: 1.22, width: 1.28, duration: .26, activeUntil: .16, lunge: 180, recoil: 28, knockback: 520, hitStop: .058 }
      }
    },
    nova: {
      name: "Nova",
      accent: "#a78bfa",
      health: 86,
      speed: 316,
      damage: 11,
      range: 76,
      cooldown: .30,
      radius: 17,
      moves: {
        primary: { name: "Pulse Tap", icon: "PT", damage: 11, range: 76, cooldown: .30, width: 1.25, duration: .16, activeUntil: .10, lunge: 84, recoil: 26, knockback: 330, hitStop: .032 },
        special: { name: "Star Burst", icon: "SB", damage: 18, range: 86, cooldown: 1.16, width: 1.70, duration: .28, activeUntil: .18, lunge: 135, recoil: 24, knockback: 500, hitStop: .055 }
      }
    },
    warden: {
      name: "Warden",
      accent: "#2dd4bf",
      health: 124,
      speed: 238,
      damage: 17,
      range: 64,
      cooldown: .50,
      radius: 21,
      moves: {
        primary: { name: "Anchor Hit", icon: "AH", damage: 17, range: 64, cooldown: .50, width: 1.34, duration: .21, activeUntil: .13, lunge: 64, recoil: 46, knockback: 470, hitStop: .050 },
        special: { name: "Lockdown", icon: "LD", damage: 21, range: 74, cooldown: 1.34, width: 1.55, duration: .30, activeUntil: .18, lunge: 120, recoil: 34, knockback: 610, hitStop: .065 }
      }
    }
  };

  const FIGHTER_STAT_MAX = { health: 132, speed: 316, damage: 19 };

  const game = {
    state: "menu",
    width: 0,
    height: 0,
    worldWidth: 0,
    worldHeight: 0,
    dpr: 1,
    lastTime: performance.now(),
    countdownStart: 0,
    countdownValue: "",
    endTimer: 0,
    round: 1,
    hasStartedMatch: false,
    selected: ["vanguard", "sentinel"],
    players: [],
    walls: [],
    attacks: [],
    particles: [],
    keys: new Set(),
    shake: 0,
    flash: 0,
    hitStop: 0,
    mapSeed: 0
  };

  function switchScreen(name) {
    Object.entries(ui.screens).forEach(([key, screen]) => {
      const active = key === name;
      screen.classList.toggle("active", active);
      screen.setAttribute("aria-hidden", String(!active));
    });
  }

  function blurActiveElement() {
    if (document.activeElement && typeof document.activeElement.blur === "function") {
      document.activeElement.blur();
    }
  }

  function setHudVisible(visible) {
    ui.hud.classList.toggle("visible", visible);
    ui.cameraLabels.classList.toggle("visible", visible);
  }

  function setMovePanelsVisible(visible) {
    ui.p1Moves.classList.toggle("visible", visible);
    ui.p2Moves.classList.toggle("visible", visible);
  }

  function setControlsVisible(visible) {
    ui.p1Controls.classList.toggle("visible", visible);
    ui.p2Controls.classList.toggle("visible", visible);
  }

  function clearMatchVisuals() {
    game.players = [];
    game.walls = [];
    game.attacks = [];
    game.particles = [];
    game.keys.clear();
    game.shake = 0;
    game.flash = 0;
    game.hitStop = 0;
    syncControlHints();
    syncHudStatus();
    setMovePanelsVisible(false);
    setControlsVisible(false);
  }

  function resizeCanvas() {
    game.dpr = Math.min(window.devicePixelRatio || 1, 2);
    game.width = window.innerWidth;
    game.height = window.innerHeight;
    const splitWidth = Math.max(1, game.width / 2);
    game.worldWidth = Math.max(1280, splitWidth * 2.15);
    game.worldHeight = Math.max(760, game.height * 1.22);
    canvas.width = Math.floor(game.width * game.dpr);
    canvas.height = Math.floor(game.height * game.dpr);
    ctx.setTransform(game.dpr, 0, 0, game.dpr, 0, 0);

    game.players.forEach(player => {
      player.x = clamp(player.x, player.radius + 12, game.worldWidth - player.radius - 12);
      player.y = clamp(player.y, player.radius + 12, game.worldHeight - player.radius - 12);
    });

    if ((game.state === "countdown" || game.state === "playing" || game.state === "ending") && !game.walls.length) {
      generateMap();
    }
  }

  function clamp(value, min, max) {
    return Math.max(min, Math.min(max, value));
  }

  function lerp(a, b, t) {
    return a + (b - a) * t;
  }

  function randomRange(min, max) {
    return min + Math.random() * (max - min);
  }

  function distanceToRect(x, y, rect) {
    const nx = clamp(x, rect.x, rect.x + rect.w);
    const ny = clamp(y, rect.y, rect.y + rect.h);
    return Math.hypot(x - nx, y - ny);
  }

  function rectsOverlap(a, b, padding = 0) {
    return (
      a.x - padding < b.x + b.w &&
      a.x + a.w + padding > b.x &&
      a.y - padding < b.y + b.h &&
      a.y + a.h + padding > b.y
    );
  }

  function generateMap() {
    const minSide = Math.min(game.worldWidth, game.worldHeight);
    const spawnA = { x: game.worldWidth * .22, y: game.worldHeight * .54, r: Math.max(120, minSide * .13) };
    const spawnB = { x: game.worldWidth * .78, y: game.worldHeight * .46, r: Math.max(120, minSide * .13) };
    const walls = [];
    const wallCount = clamp(Math.round(minSide / 86), 7, 13);
    let tries = 0;

    while (walls.length < wallCount && tries < 240) {
      tries += 1;
      const vertical = Math.random() > .5;
      const w = vertical ? randomRange(28, 48) : randomRange(88, 168);
      const h = vertical ? randomRange(88, 168) : randomRange(28, 48);
      const wall = {
        x: randomRange(88, Math.max(100, game.worldWidth - w - 88)),
        y: randomRange(112, Math.max(124, game.worldHeight - h - 84)),
        w,
        h
      };

      const touchesSpawn =
        distanceToRect(spawnA.x, spawnA.y, wall) < spawnA.r ||
        distanceToRect(spawnB.x, spawnB.y, wall) < spawnB.r;
      const tooClose = walls.some(existing => rectsOverlap(wall, existing, 42));

      if (!touchesSpawn && !tooClose) {
        walls.push(wall);
      }
    }

    const centerWall = {
      x: game.worldWidth * .5 - 24,
      y: game.worldHeight * .5 - 78,
      w: 48,
      h: 156
    };

    const centerIsClear =
      distanceToRect(spawnA.x, spawnA.y, centerWall) > spawnA.r &&
      distanceToRect(spawnB.x, spawnB.y, centerWall) > spawnB.r;

    const centerHasRoom = !walls.some(existing => rectsOverlap(centerWall, existing, 42));

    if (centerIsClear && centerHasRoom && game.worldWidth > 720 && game.worldHeight > 520) {
      walls.push(centerWall);
    }

    game.walls = walls;
  }

  function createPlayer(index, fighterKey) {
    const fighter = FIGHTERS[fighterKey];
    const side = index === 0 ? .18 : .82;
    return {
      id: index,
      label: `Player ${index + 1}`,
      fighterKey,
      fighter,
      color: PLAYER_COLORS[index],
      x: game.worldWidth * side,
      y: game.worldHeight * (index === 0 ? .54 : .46),
      vx: 0,
      vy: 0,
      kx: 0,
      ky: 0,
      radius: fighter.radius,
      health: fighter.health,
      maxHealth: fighter.health,
      angle: index === 0 ? 0 : Math.PI,
      attackCooldown: 0,
      moveCooldowns: {
        primary: 0,
        special: 0
      },
      attackTimer: 0,
      attackMove: "primary",
      hitFlash: 0,
      hitStun: 0,
      invulnerable: 0,
      dead: false
    };
  }

  function startMatch() {
    blurActiveElement();
    if (game.hasStartedMatch) {
      game.round += 1;
    }
    game.hasStartedMatch = true;
    game.state = "countdown";
    game.countdownStart = performance.now() / 1000;
    game.countdownValue = "";
    game.endTimer = 0;
    game.attacks = [];
    game.particles = [];
    game.keys.clear();
    game.shake = 0;
    game.flash = 0;
    game.hitStop = 0;
    syncControlHints();
    generateMap();
    game.players = [
      createPlayer(0, game.selected[0]),
      createPlayer(1, game.selected[1])
    ];
    ui.roundChip.textContent = `ROUND ${game.round}`;
    ui.p1Name.textContent = `${game.players[0].label} - ${game.players[0].fighter.name}`;
    ui.p2Name.textContent = `${game.players[1].label} - ${game.players[1].fighter.name}`;
    renderMovePanels();
    syncHud(true);
    setHudVisible(true);
    setMovePanelsVisible(true);
    setControlsVisible(true);
    switchScreen("countdown");
  }

  function endMatch(winner, loser) {
    if (game.state !== "playing") return;
    game.state = "ending";
    game.endTimer = .7;
    winner.vx *= .3;
    winner.vy *= .3;
    loser.dead = true;
    ui.winnerName.textContent = `${winner.label} Wins`;
    ui.winnerMeta.textContent = `${winner.fighter.name} finished with ${Math.ceil(winner.health)} health.`;
  }

  function finishEndScreen() {
    game.state = "end";
    setHudVisible(false);
    setMovePanelsVisible(false);
    setControlsVisible(false);
    switchScreen("end");
  }

  function renderMovePanels() {
    const [p1, p2] = game.players;
    if (!p1 || !p2) return;

    ui.p1FighterMoveName.textContent = p1.fighter.name;
    ui.p2FighterMoveName.textContent = p2.fighter.name;
    ui.p1MoveList.innerHTML = buildMoveRows(p1, 0);
    ui.p2MoveList.innerHTML = buildMoveRows(p2, 1);
    syncMovePanels();
  }

  function buildMoveRows(player, playerIndex) {
    const labels = CONTROL_LABELS[playerIndex];
    return Object.entries(player.fighter.moves).map(([moveKey, move]) => `
      <div class="move-row" data-player="${player.id}" data-move="${moveKey}" style="--move-color:${player.fighter.accent}">
        <span class="move-icon" aria-hidden="true">${move.icon}</span>
        <span class="move-copy">
          <span class="move-name">
            <span>${move.name}</span>
            <span class="move-key">${labels[moveKey]}</span>
          </span>
          <span class="move-track"><span class="move-fill"></span></span>
        </span>
      </div>
    `).join("");
  }

  function renderCharacterSelect() {
    const fighterEntries = Object.entries(FIGHTERS);
    ui.characterSelect.innerHTML = "";

    for (let playerIndex = 0; playerIndex < 2; playerIndex += 1) {
      const column = document.createElement("div");
      column.className = "fighter-column";
      column.innerHTML = `
        <div class="player-label">
          <span class="player-dot" style="color:${PLAYER_COLORS[playerIndex]}; background:${PLAYER_COLORS[playerIndex]}"></span>
          <span>Player ${playerIndex + 1}</span>
        </div>
        <div class="fighter-grid"></div>
      `;

      const grid = column.querySelector(".fighter-grid");
      fighterEntries.forEach(([key, fighter]) => {
        const card = document.createElement("button");
        card.type = "button";
        card.className = "fighter-card";
        card.dataset.player = String(playerIndex);
        card.dataset.fighter = key;
        card.style.setProperty("--fighter-color", PLAYER_COLORS[playerIndex]);
        card.style.setProperty("--fighter-accent", fighter.accent);
        card.innerHTML = `
          <span class="portrait" aria-hidden="true"><span class="portrait-mark">${fighter.moves.special.icon}</span></span>
          <span class="fighter-info">
            <span class="fighter-name">${fighter.name}</span>
            ${statLine("HP", fighter.health / FIGHTER_STAT_MAX.health, fighter.accent)}
            ${statLine("SPD", fighter.speed / FIGHTER_STAT_MAX.speed, fighter.accent)}
            ${statLine("DMG", fighter.damage / FIGHTER_STAT_MAX.damage, fighter.accent)}
            <span class="fighter-move">${fighter.moves.primary.name} / ${fighter.moves.special.name}</span>
          </span>
        `;
        card.addEventListener("click", () => {
          game.selected[playerIndex] = key;
          updateSelectedCards();
        });
        grid.appendChild(card);
      });

      ui.characterSelect.appendChild(column);
    }

    updateSelectedCards();
  }

  function statLine(label, ratio, color) {
    return `
      <span class="stat-line">
        <span>${label}</span>
        <span class="stat-track"><span class="stat-fill" style="width:${Math.round(ratio * 100)}%; background:${color}"></span></span>
      </span>
    `;
  }

  function updateSelectedCards() {
    ui.characterSelect.querySelectorAll(".fighter-card").forEach(card => {
      const playerIndex = Number(card.dataset.player);
      card.classList.toggle("selected", game.selected[playerIndex] === card.dataset.fighter);
    });
  }

  function getInput(player) {
    const control = CONTROLS[player.id];
    const x = Number(game.keys.has(control.right)) - Number(game.keys.has(control.left));
    const y = Number(game.keys.has(control.down)) - Number(game.keys.has(control.up));
    if (x === 0 && y === 0) return { x: 0, y: 0, active: false };
    const length = Math.hypot(x, y) || 1;
    return { x: x / length, y: y / length, active: true };
  }

  function tryAttack(player, moveKey = "primary") {
    if (game.state !== "playing" || player.dead) return;
    const fighter = player.fighter;
    const move = fighter.moves[moveKey] || fighter.moves.primary;
    if (player.moveCooldowns[moveKey] > 0) return;

    player.moveCooldowns[moveKey] = move.cooldown;
    player.attackCooldown = player.moveCooldowns.primary;
    player.attackTimer = move.duration;
    player.attackMove = moveKey;
    player.kx += Math.cos(player.angle) * move.lunge;
    player.ky += Math.sin(player.angle) * move.lunge;

    game.attacks.push({
      ownerId: player.id,
      moveKey,
      angle: player.angle,
      age: 0,
      duration: move.duration,
      activeUntil: move.activeUntil,
      range: move.range,
      width: move.width,
      damage: move.damage,
      knockback: move.knockback,
      recoil: move.recoil,
      hitStop: move.hitStop,
      hit: new Set()
    });

    syncMovePanels();
  }

  function isBlocked(x, y, radius) {
    if (
      x - radius < 0 ||
      y - radius < 0 ||
      x + radius > game.worldWidth ||
      y + radius > game.worldHeight
    ) {
      return true;
    }

    return game.walls.some(wall => distanceToRect(x, y, wall) < radius);
  }

  function moveWithCollision(player, dx, dy) {
    if (!isBlocked(player.x + dx, player.y, player.radius)) {
      player.x += dx;
    } else {
      player.vx = 0;
      player.kx *= -.12;
    }

    if (!isBlocked(player.x, player.y + dy, player.radius)) {
      player.y += dy;
    } else {
      player.vy = 0;
      player.ky *= -.12;
    }
  }

  function resolvePlayerCollision(a, b) {
    if (a.dead || b.dead) return;
    const dx = b.x - a.x;
    const dy = b.y - a.y;
    const dist = Math.hypot(dx, dy) || 1;
    const minDist = a.radius + b.radius + 2;
    if (dist >= minDist) return;

    const push = (minDist - dist) / 2;
    const nx = dx / dist;
    const ny = dy / dist;

    if (!isBlocked(a.x - nx * push, a.y - ny * push, a.radius)) {
      a.x -= nx * push;
      a.y -= ny * push;
    }
    if (!isBlocked(b.x + nx * push, b.y + ny * push, b.radius)) {
      b.x += nx * push;
      b.y += ny * push;
    }
  }

  function updatePlayer(player, dt) {
    if (player.dead) {
      player.vx = lerp(player.vx, 0, Math.min(1, dt * 10));
      player.vy = lerp(player.vy, 0, Math.min(1, dt * 10));
      return;
    }

    const input = getInput(player);
    if (input.active && player.hitStun <= 0) {
      player.angle = Math.atan2(input.y, input.x);
    }

    const controlScale = player.hitStun > 0 ? .22 : 1;
    const targetVx = input.x * player.fighter.speed * controlScale;
    const targetVy = input.y * player.fighter.speed * controlScale;
    const accel = Math.min(1, dt * 13);
    player.vx = lerp(player.vx, targetVx, accel);
    player.vy = lerp(player.vy, targetVy, accel);

    moveWithCollision(player, (player.vx + player.kx) * dt, (player.vy + player.ky) * dt);

    player.kx = lerp(player.kx, 0, Math.min(1, dt * 7));
    player.ky = lerp(player.ky, 0, Math.min(1, dt * 7));
    player.moveCooldowns.primary = Math.max(0, player.moveCooldowns.primary - dt);
    player.moveCooldowns.special = Math.max(0, player.moveCooldowns.special - dt);
    player.attackCooldown = player.moveCooldowns.primary;
    player.attackTimer = Math.max(0, player.attackTimer - dt);
    player.hitFlash = Math.max(0, player.hitFlash - dt);
    player.hitStun = Math.max(0, player.hitStun - dt);
    player.invulnerable = Math.max(0, player.invulnerable - dt);
  }

  function lineBlocked(ax, ay, bx, by) {
    const samples = 10;
    for (let i = 1; i < samples; i += 1) {
      const t = i / samples;
      const x = lerp(ax, bx, t);
      const y = lerp(ay, by, t);
      if (game.walls.some(wall => x > wall.x && x < wall.x + wall.w && y > wall.y && y < wall.y + wall.h)) {
        return true;
      }
    }
    return false;
  }

  function updateAttacks(dt) {
    for (const attack of game.attacks) {
      attack.age += dt;
      const owner = game.players[attack.ownerId];
      if (!owner || owner.dead || attack.age > attack.activeUntil) continue;

      for (const target of game.players) {
        if (target.id === owner.id || target.dead || target.invulnerable > 0 || attack.hit.has(target.id)) continue;

        const dx = target.x - owner.x;
        const dy = target.y - owner.y;
        const distance = Math.hypot(dx, dy);
        const reach = owner.radius + target.radius + attack.range;
        if (distance > reach) continue;

        const targetAngle = Math.atan2(dy, dx);
        const diff = Math.abs(Math.atan2(Math.sin(targetAngle - attack.angle), Math.cos(targetAngle - attack.angle)));
        if (diff > attack.width * .5) continue;
        if (lineBlocked(owner.x, owner.y, target.x, target.y)) continue;

        attack.hit.add(target.id);
        applyHit(owner, target, attack, targetAngle);
      }
    }

    game.attacks = game.attacks.filter(attack => attack.age < attack.duration);
  }

  function applyHit(attacker, target, attack, angle) {
    target.health = Math.max(0, target.health - attack.damage);
    target.hitFlash = .18;
    target.hitStun = .13;
    target.invulnerable = .09;
    target.kx += Math.cos(angle) * attack.knockback;
    target.ky += Math.sin(angle) * attack.knockback;
    attacker.kx -= Math.cos(angle) * attack.recoil;
    attacker.ky -= Math.sin(angle) * attack.recoil;
    game.shake = Math.max(game.shake, attack.moveKey === "special" ? 12 : 8);
    game.flash = Math.max(game.flash, .12);
    game.hitStop = Math.max(game.hitStop, attack.hitStop);
    spawnHitParticles(target.x, target.y, attacker.color, target.fighter.accent);
    syncHud(true, target.id);

    if (target.health <= 0) {
      endMatch(attacker, target);
    }
  }

  function spawnHitParticles(x, y, colorA, colorB) {
    for (let i = 0; i < 18; i += 1) {
      const angle = Math.random() * Math.PI * 2;
      const speed = randomRange(90, 330);
      game.particles.push({
        x,
        y,
        vx: Math.cos(angle) * speed,
        vy: Math.sin(angle) * speed,
        life: randomRange(.18, .36),
        maxLife: .36,
        size: randomRange(2, 5),
        color: Math.random() > .45 ? colorA : colorB
      });
    }
  }

  function updateParticles(dt) {
    for (const particle of game.particles) {
      particle.life -= dt;
      particle.x += particle.vx * dt;
      particle.y += particle.vy * dt;
      particle.vx *= Math.pow(.08, dt);
      particle.vy *= Math.pow(.08, dt);
    }
    game.particles = game.particles.filter(particle => particle.life > 0);
  }

  function updateCountdown(nowSeconds) {
    const elapsed = nowSeconds - game.countdownStart;
    let value = "3";
    if (elapsed >= 1 && elapsed < 2) value = "2";
    if (elapsed >= 2 && elapsed < 3) value = "1";
    if (elapsed >= 3) value = "FIGHT";

    if (value !== game.countdownValue) {
      game.countdownValue = value;
      ui.countdownText.textContent = value;
      ui.countdownText.classList.remove("pop");
      void ui.countdownText.offsetWidth;
      ui.countdownText.classList.add("pop");
    }

    if (elapsed >= 3.72) {
      game.state = "playing";
      blurActiveElement();
      switchScreen(null);
    }
  }

  function update(dt, nowSeconds) {
    game.shake = Math.max(0, game.shake - dt * 28);
    game.flash = Math.max(0, game.flash - dt * 4.2);

    if (game.state === "countdown") {
      updateCountdown(nowSeconds);
    }

    if (game.hitStop > 0) {
      game.hitStop = Math.max(0, game.hitStop - dt);
      updateParticles(dt * .25);
      return;
    }

    if (game.state === "playing" || game.state === "ending") {
      game.players.forEach(player => updatePlayer(player, dt));
      if (game.players.length === 2) {
        resolvePlayerCollision(game.players[0], game.players[1]);
      }
      updateAttacks(dt);
      updateParticles(dt);
      syncCooldowns();
    }

    if (game.state === "ending") {
      game.endTimer -= dt;
      if (game.endTimer <= 0) {
        finishEndScreen();
      }
    }
  }

  function syncHud(animate = false, damagedId = null) {
    const [p1, p2] = game.players;
    if (!p1 || !p2) return;

    syncPlayerHud(p1, ui.p1HpText, ui.p1Health, animate && damagedId === 0);
    syncPlayerHud(p2, ui.p2HpText, ui.p2Health, animate && damagedId === 1);
    syncHudStatus();
    syncCooldowns();
  }

  function syncPlayerHud(player, hpText, healthFill, damaged) {
    const ratio = clamp(player.health / player.maxHealth, 0, 1);
    hpText.textContent = String(Math.ceil(player.health));
    healthFill.style.transform = `scaleX(${ratio})`;
    if (damaged) {
      healthFill.classList.remove("hit");
      void healthFill.offsetWidth;
      healthFill.classList.add("hit");
    }
  }

  function syncHudStatus() {
    const [p1, p2] = game.players;
    if (!p1 || !p2) {
      ui.p1Card.classList.remove("low");
      ui.p2Card.classList.remove("low");
      return;
    }

    ui.p1Card.classList.toggle("low", p1.health / p1.maxHealth <= .28);
    ui.p2Card.classList.toggle("low", p2.health / p2.maxHealth <= .28);
  }

  function syncCooldowns() {
    const [p1, p2] = game.players;
    if (!p1 || !p2) return;
    ui.p1Cooldown.style.transform = `scaleX(${1 - clamp(p1.moveCooldowns.primary / p1.fighter.moves.primary.cooldown, 0, 1)})`;
    ui.p2Cooldown.style.transform = `scaleX(${1 - clamp(p2.moveCooldowns.primary / p2.fighter.moves.primary.cooldown, 0, 1)})`;
    syncMovePanels();
  }

  function syncMovePanels() {
    game.players.forEach(player => {
      Object.entries(player.fighter.moves).forEach(([moveKey, move]) => {
        const row = document.querySelector(`.move-row[data-player="${player.id}"][data-move="${moveKey}"]`);
        if (!row) return;
        const fill = row.querySelector(".move-fill");
        const ready = 1 - clamp(player.moveCooldowns[moveKey] / move.cooldown, 0, 1);
        fill.style.transform = `scaleX(${ready})`;
        row.classList.toggle("ready", ready >= 1);
      });
    });
  }

  function syncControlHints() {
    ui.keyHints.forEach(key => {
      key.classList.toggle("pressed", game.keys.has(key.dataset.code));
    });
  }

  function drawArena() {
    ctx.fillStyle = "#0a0c10";
    ctx.fillRect(0, 0, game.worldWidth, game.worldHeight);

    const grid = 48;
    ctx.lineWidth = 1;
    ctx.strokeStyle = "rgba(255,255,255,.045)";
    ctx.beginPath();
    for (let x = (game.mapSeed % grid); x < game.worldWidth; x += grid) {
      ctx.moveTo(x, 0);
      ctx.lineTo(x, game.worldHeight);
    }
    for (let y = (game.mapSeed % grid); y < game.worldHeight; y += grid) {
      ctx.moveTo(0, y);
      ctx.lineTo(game.worldWidth, y);
    }
    ctx.stroke();

    ctx.strokeStyle = "rgba(247,201,72,.16)";
    ctx.lineWidth = 2;
    ctx.strokeRect(18, 18, game.worldWidth - 36, game.worldHeight - 36);
  }

  function viewportForPlayer(playerIndex) {
    const w = game.width / 2;
    return {
      x: playerIndex === 0 ? 0 : w,
      y: 0,
      w,
      h: game.height
    };
  }

  function cameraFor(player, viewport) {
    const maxX = Math.max(0, game.worldWidth - viewport.w);
    const maxY = Math.max(0, game.worldHeight - viewport.h);
    return {
      x: clamp(player.x - viewport.w * .5, 0, maxX),
      y: clamp(player.y - viewport.h * .5, 0, maxY)
    };
  }

  function drawViewportFrame(viewport, playerIndex) {
    const color = playerIndex === 0 ? PLAYER_COLORS[0] : PLAYER_COLORS[1];
    const edgeX = playerIndex === 0 ? viewport.x : viewport.x + viewport.w;

    ctx.save();
    ctx.strokeStyle = color;
    ctx.globalAlpha = .55;
    ctx.lineWidth = 3;
    ctx.strokeRect(viewport.x + 10, viewport.y + 10, viewport.w - 20, viewport.h - 20);
    ctx.globalAlpha = .20;
    ctx.fillStyle = color;
    ctx.fillRect(playerIndex === 0 ? viewport.x : viewport.x + viewport.w - 8, 0, 8, viewport.h);
    ctx.globalAlpha = .90;
    ctx.fillStyle = color;
    ctx.fillRect(edgeX - (playerIndex === 0 ? 0 : 3), 0, 3, viewport.h);
    ctx.restore();
  }

  function drawSplitDivider() {
    const x = game.width / 2;
    ctx.save();
    ctx.fillStyle = "#05070a";
    ctx.fillRect(x - 4, 0, 8, game.height);
    ctx.fillStyle = "rgba(255,255,255,.18)";
    ctx.fillRect(x - 1, 0, 2, game.height);
    ctx.fillStyle = "rgba(247,201,72,.88)";
    ctx.fillRect(x - 2, game.height * .18, 4, game.height * .64);
    ctx.restore();
  }

  function drawWalls() {
    for (const wall of game.walls) {
      ctx.save();
      ctx.fillStyle = "#242834";
      ctx.strokeStyle = "rgba(255,255,255,.18)";
      ctx.lineWidth = 2;
      ctx.shadowColor = "rgba(0,0,0,.42)";
      ctx.shadowBlur = 18;
      ctx.fillRect(wall.x, wall.y, wall.w, wall.h);
      ctx.strokeRect(wall.x + .5, wall.y + .5, wall.w - 1, wall.h - 1);
      ctx.fillStyle = "rgba(247,201,72,.10)";
      ctx.fillRect(wall.x + 4, wall.y + 4, wall.w - 8, 5);
      ctx.restore();
    }
  }

  function drawFighterDesign(player, r) {
    const accent = player.fighter.accent;

    ctx.save();
    ctx.shadowBlur = 0;
    ctx.fillStyle = "rgba(5,7,10,.58)";
    ctx.beginPath();
    ctx.ellipse(-r * .10, 0, r * .58, r * .72, 0, 0, Math.PI * 2);
    ctx.fill();

    ctx.strokeStyle = accent;
    ctx.lineWidth = 2.5;
    ctx.beginPath();
    ctx.arc(0, 0, r * .68, -1.18, 1.18);
    ctx.stroke();

    ctx.fillStyle = "rgba(255,255,255,.88)";
    ctx.font = `900 ${Math.max(10, r * .46)}px system-ui, sans-serif`;
    ctx.textAlign = "center";
    ctx.textBaseline = "middle";
    ctx.fillText(`P${player.id + 1}`, -r * .12, 0);

    ctx.strokeStyle = accent;
    ctx.fillStyle = accent;
    ctx.lineCap = "round";
    ctx.lineJoin = "round";

    if (player.fighterKey === "vanguard") {
      ctx.beginPath();
      ctx.moveTo(r * .18, -r * .68);
      ctx.lineTo(r * .78, -r * .42);
      ctx.lineTo(r * .78, r * .42);
      ctx.lineTo(r * .18, r * .68);
      ctx.closePath();
      ctx.globalAlpha = .92;
      ctx.fill();
      ctx.globalAlpha = 1;
      ctx.strokeStyle = "#111318";
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.moveTo(r * .34, -r * .38);
      ctx.lineTo(r * .64, 0);
      ctx.lineTo(r * .34, r * .38);
      ctx.stroke();
    }

    if (player.fighterKey === "striker") {
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.moveTo(-r * .42, -r * .58);
      ctx.lineTo(r * .10, -r * .06);
      ctx.lineTo(-r * .08, -r * .02);
      ctx.lineTo(r * .48, r * .58);
      ctx.stroke();
      ctx.beginPath();
      ctx.moveTo(r * .20, -r * .52);
      ctx.lineTo(r * .58, -r * .12);
      ctx.stroke();
    }

    if (player.fighterKey === "sentinel") {
      ctx.globalAlpha = .90;
      ctx.fillRect(-r * .72, -r * .78, r * .48, r * .38);
      ctx.fillRect(-r * .72, r * .40, r * .48, r * .38);
      ctx.fillRect(r * .30, -r * .56, r * .42, r * 1.12);
      ctx.globalAlpha = 1;
      ctx.strokeStyle = "#111318";
      ctx.lineWidth = 2;
      ctx.strokeRect(r * .30, -r * .56, r * .42, r * 1.12);
    }

    if (player.fighterKey === "ronin") {
      ctx.lineWidth = 3;
      ctx.beginPath();
      ctx.moveTo(-r * .64, r * .48);
      ctx.lineTo(r * .72, -r * .48);
      ctx.stroke();
      ctx.strokeStyle = "rgba(255,255,255,.85)";
      ctx.lineWidth = 2;
      ctx.beginPath();
      ctx.moveTo(r * .12, -r * .72);
      ctx.lineTo(r * .92, -r * .98);
      ctx.stroke();
    }

    if (player.fighterKey === "nova") {
      ctx.lineWidth = 2;
      for (let i = 0; i < 3; i += 1) {
        ctx.save();
        ctx.rotate((Math.PI * 2 / 3) * i + game.mapSeed * .05);
        ctx.beginPath();
        ctx.ellipse(0, 0, r * .88, r * .22, 0, 0, Math.PI * 2);
        ctx.stroke();
        ctx.restore();
      }
      ctx.beginPath();
      ctx.arc(r * .42, -r * .40, r * .13, 0, Math.PI * 2);
      ctx.fill();
    }

    if (player.fighterKey === "warden") {
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.moveTo(-r * .12, -r * .72);
      ctx.lineTo(-r * .12, r * .72);
      ctx.moveTo(-r * .54, 0);
      ctx.lineTo(r * .54, 0);
      ctx.stroke();
      ctx.beginPath();
      ctx.arc(r * .44, 0, r * .26, -Math.PI * .55, Math.PI * .55);
      ctx.stroke();
    }

    ctx.restore();
  }

  function drawPlayers() {
    for (const player of game.players) {
      const pulse = player.hitFlash > 0 ? 1 : 0;
      const scale = player.dead ? .86 : 1 + pulse * .08;
      const r = player.radius * scale;

      ctx.save();
      ctx.translate(player.x, player.y);
      ctx.rotate(player.angle);

      ctx.globalAlpha = player.dead ? .46 : 1;
      ctx.shadowColor = player.color;
      ctx.shadowBlur = player.dead ? 0 : 24;
      ctx.fillStyle = player.hitFlash > 0 ? "#ffffff" : player.color;
      ctx.beginPath();
      ctx.arc(0, 0, r, 0, Math.PI * 2);
      ctx.fill();

      ctx.shadowBlur = 0;
      ctx.strokeStyle = player.fighter.accent;
      ctx.lineWidth = 3;
      ctx.stroke();

      drawFighterDesign(player, r);

      ctx.fillStyle = "#0a0c10";
      ctx.beginPath();
      ctx.moveTo(r + 10, 0);
      ctx.lineTo(r - 6, -8);
      ctx.lineTo(r - 6, 8);
      ctx.closePath();
      ctx.fill();

      if (player.attackTimer > 0) {
        const activeMove = player.fighter.moves[player.attackMove] || player.fighter.moves.primary;
        ctx.strokeStyle = player.fighter.accent;
        ctx.lineWidth = player.attackMove === "special" ? 7 : 5;
        ctx.lineCap = "round";
        ctx.beginPath();
        ctx.moveTo(r - 3, -r * .5);
        ctx.lineTo(r + activeMove.range * .58, 0);
        ctx.lineTo(r - 3, r * .5);
        ctx.stroke();
      }

      ctx.restore();
    }
  }

  function drawAttacks() {
    for (const attack of game.attacks) {
      const owner = game.players[attack.ownerId];
      if (!owner) continue;
      const t = attack.age / attack.duration;
      const alpha = Math.max(0, 1 - t);
      const start = attack.angle - attack.width * .5;
      const end = attack.angle + attack.width * .5;
      const inner = owner.radius + 4;
      const outer = owner.radius + attack.range;

      ctx.save();
      ctx.translate(owner.x, owner.y);
      ctx.fillStyle = owner.fighter.accent;
      ctx.globalAlpha = .20 * alpha;
      ctx.beginPath();
      ctx.arc(0, 0, outer, start, end);
      ctx.arc(0, 0, inner, end, start, true);
      ctx.closePath();
      ctx.fill();

      ctx.globalAlpha = .82 * alpha;
      ctx.strokeStyle = "#ffffff";
      ctx.lineWidth = 3;
      ctx.lineCap = "round";
      ctx.beginPath();
      ctx.arc(0, 0, lerp(inner, outer, .86), start + .12, end - .12);
      ctx.stroke();
      ctx.restore();
    }
  }

  function drawParticles() {
    for (const particle of game.particles) {
      const alpha = clamp(particle.life / particle.maxLife, 0, 1);
      ctx.globalAlpha = alpha;
      ctx.fillStyle = particle.color;
      ctx.beginPath();
      ctx.arc(particle.x, particle.y, particle.size, 0, Math.PI * 2);
      ctx.fill();
    }
    ctx.globalAlpha = 1;
  }

  function drawPlayerViewport(playerIndex, shakeX, shakeY) {
    const player = game.players[playerIndex] || game.players[0];
    if (!player) return;

    const viewport = viewportForPlayer(playerIndex);
    const camera = cameraFor(player, viewport);

    ctx.save();
    ctx.beginPath();
    ctx.rect(viewport.x, viewport.y, viewport.w, viewport.h);
    ctx.clip();
    ctx.translate(viewport.x + shakeX, viewport.y + shakeY);
    ctx.translate(-camera.x, -camera.y);
    drawArena();
    drawWalls();
    drawAttacks();
    drawParticles();
    drawPlayers();
    ctx.restore();

    drawViewportFrame(viewport, playerIndex);
  }

  function render(nowSeconds) {
    ctx.setTransform(game.dpr, 0, 0, game.dpr, 0, 0);
    ctx.clearRect(0, 0, game.width, game.height);

    const showGameplay =
      game.state === "countdown" ||
      game.state === "playing" ||
      game.state === "ending" ||
      game.state === "end";

    if (!showGameplay || !game.walls.length) {
      return;
    }

    const shakeX = game.shake > 0 ? randomRange(-game.shake, game.shake) : 0;
    const shakeY = game.shake > 0 ? randomRange(-game.shake, game.shake) : 0;

    drawPlayerViewport(0, shakeX, shakeY);
    drawPlayerViewport(1, shakeX, shakeY);
    drawSplitDivider();

    if (game.flash > 0) {
      ctx.fillStyle = `rgba(255,255,255,${Math.min(.22, game.flash)})`;
      ctx.fillRect(0, 0, game.width, game.height);
    }
  }

  function frame(now) {
    const nowSeconds = now / 1000;
    const dt = Math.min(.033, Math.max(0, (now - game.lastTime) / 1000));
    game.lastTime = now;
    game.mapSeed += dt * 8;
    update(dt, nowSeconds);
    render(nowSeconds);
    requestAnimationFrame(frame);
  }

  window.addEventListener("resize", resizeCanvas);

  window.addEventListener("keydown", event => {
    const gameKey = CONTROLS.some(control =>
      event.code === control.up ||
      event.code === control.down ||
      event.code === control.left ||
      event.code === control.right ||
      event.code === control.primary ||
      event.code === control.special
    );

    if (gameKey && game.state !== "menu" && game.state !== "select" && game.state !== "end") {
      event.preventDefault();
    }

    if (gameKey) {
      game.keys.add(event.code);
      syncControlHints();
    }

    if (event.repeat) return;
    for (let i = 0; i < CONTROLS.length; i += 1) {
      if (event.code === CONTROLS[i].primary && game.players[i]) {
        tryAttack(game.players[i], "primary");
      }
      if (event.code === CONTROLS[i].special && game.players[i]) {
        tryAttack(game.players[i], "special");
      }
    }
  });

  window.addEventListener("keyup", event => {
    game.keys.delete(event.code);
    syncControlHints();
  });

  window.addEventListener("blur", () => {
    game.keys.clear();
    syncControlHints();
  });

  ui.playBtn.addEventListener("click", () => {
    game.state = "select";
    setHudVisible(false);
    switchScreen("select");
  });

  ui.backBtn.addEventListener("click", () => {
    game.state = "menu";
    setHudVisible(false);
    clearMatchVisuals();
    switchScreen("menu");
  });

  ui.startMatchBtn.addEventListener("click", startMatch);
  ui.rematchBtn.addEventListener("click", startMatch);

  ui.fightersBtn.addEventListener("click", () => {
    game.state = "select";
    setHudVisible(false);
    clearMatchVisuals();
    switchScreen("select");
  });

  renderCharacterSelect();
  resizeCanvas();
  requestAnimationFrame(frame);
})();
</script>
</body>
</html>
