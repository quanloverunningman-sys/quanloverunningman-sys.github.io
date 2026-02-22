<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="SubDrop">
<meta name="theme-color" content="#0a0a0e">
<title>SubDrop</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=Instrument+Serif:ital@0;1&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }

  :root {
    --bg: #0a0a0e;
    --card: #13131a;
    --border: #1e1e2a;
    --accent: #ff6b35;
    --accent2: #a78bfa;
    --green: #4ade80;
    --text: #f4f4f0;
    --muted: #5a5a6e;
  }

  html, body { height: 100%; overscroll-behavior: none; }
  body { background: var(--bg); color: var(--text); font-family: 'Syne', sans-serif; display: flex; flex-direction: column; min-height: 100dvh; }

  .header { padding: 16px 20px 14px; display: flex; align-items: center; justify-content: space-between; border-bottom: 1px solid var(--border); flex-shrink: 0; }
  .logo { font-family: 'Instrument Serif', serif; font-style: italic; font-size: 1.5rem; color: var(--accent); }
  .logo span { color: var(--text); font-style: normal; }
  .status-dot { width: 8px; height: 8px; border-radius: 50%; background: var(--muted); transition: background 0.3s; }
  .status-dot.ready { background: var(--green); box-shadow: 0 0 8px var(--green); }

  .scroll-area { flex: 1; overflow-y: auto; -webkit-overflow-scrolling: touch; padding: 20px 16px 40px; display: flex; flex-direction: column; gap: 16px; }

  /* Mode toggle */
  .mode-toggle { display: grid; grid-template-columns: 1fr 1fr; background: var(--card); border: 1px solid var(--border); border-radius: 16px; padding: 5px; gap: 4px; }
  .mode-btn { background: none; border: none; color: var(--muted); font-family: 'Syne', sans-serif; font-size: 0.8rem; font-weight: 700; padding: 12px 10px; border-radius: 12px; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 8px; transition: background 0.2s, color 0.2s; }
  .mode-btn.active { background: var(--accent); color: #fff; }

  /* Cards */
  .card { background: var(--card); border: 1px solid var(--border); border-radius: 16px; padding: 18px; }
  .card-label { font-size: 0.65rem; letter-spacing: 0.16em; text-transform: uppercase; color: var(--muted); margin-bottom: 12px; }

  .panel { display: none; flex-direction: column; gap: 16px; }
  .panel.active { display: flex; }

  /* URL */
  .url-row { display: flex; gap: 10px; align-items: stretch; }
  input[type="text"] { flex: 1; min-width: 0; background: var(--bg); border: 1.5px solid var(--border); color: var(--text); font-family: 'Syne', sans-serif; font-size: 0.85rem; padding: 14px; border-radius: 12px; outline: none; -webkit-appearance: none; transition: border-color 0.2s; }
  input[type="text"]:focus { border-color: var(--accent); }
  input[type="text"]::placeholder { color: var(--muted); }
  .go-btn { background: var(--accent); border: none; color: #fff; font-family: 'Syne', sans-serif; font-size: 0.75rem; font-weight: 800; padding: 12px 14px; border-radius: 12px; cursor: pointer; white-space: nowrap; letter-spacing: 0.05em; line-height: 1.4; transition: opacity 0.15s, transform 0.1s; text-align: center; }
  .go-btn:active { opacity: 0.8; transform: scale(0.96); }

  /* File pickers */
  .file-btn { width: 100%; background: var(--bg); border: 1.5px dashed var(--border); border-radius: 12px; padding: 16px 18px; display: flex; align-items: center; gap: 14px; cursor: pointer; transition: border-color 0.2s, background 0.2s; }
  .file-btn.loaded { border-color: var(--green); border-style: solid; background: rgba(74,222,128,0.05); }
  .file-btn.loaded .file-icon { background: rgba(74,222,128,0.15); }
  .file-icon { width: 42px; height: 42px; border-radius: 10px; background: var(--border); display: flex; align-items: center; justify-content: center; font-size: 1.2rem; flex-shrink: 0; }
  .file-info { flex: 1; min-width: 0; }
  .file-name { font-size: 0.88rem; font-weight: 700; color: var(--text); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .file-btn.loaded .file-name { color: var(--green); }
  .file-sub { font-size: 0.72rem; color: var(--muted); margin-top: 2px; }
  input[type="file"] { display: none; }

  /* ── Subtitle card sub-tabs ── */
  .sub-tabs { display: grid; grid-template-columns: 1fr 1fr; gap: 6px; margin-bottom: 14px; }
  .sub-tab { background: var(--bg); border: 1.5px solid var(--border); color: var(--muted); font-family: 'Syne', sans-serif; font-size: 0.75rem; font-weight: 700; padding: 10px 8px; border-radius: 10px; cursor: pointer; transition: all 0.2s; letter-spacing: 0.03em; }
  .sub-tab.active { border-color: var(--accent2); color: var(--accent2); background: rgba(167,139,250,0.08); }

  /* ── Transcript area ── */
  textarea {
    width: 100%; background: var(--bg); border: 1.5px solid var(--border);
    color: var(--text); font-family: 'Syne', sans-serif; font-size: 0.82rem;
    padding: 14px; border-radius: 12px; outline: none; resize: none;
    min-height: 140px; line-height: 1.6; -webkit-appearance: none;
    transition: border-color 0.2s;
  }
  textarea:focus { border-color: var(--accent2); }
  textarea::placeholder { color: var(--muted); font-size: 0.78rem; }

  .transcript-actions { display: flex; align-items: center; gap: 10px; margin-top: 10px; }
  .transcript-hint { flex: 1; font-size: 0.72rem; color: var(--muted); line-height: 1.5; }

  .translate-btn {
    background: var(--accent2); border: none; color: #fff;
    font-family: 'Syne', sans-serif; font-size: 0.78rem; font-weight: 800;
    padding: 12px 16px; border-radius: 10px; cursor: pointer;
    white-space: nowrap; letter-spacing: 0.04em;
    transition: opacity 0.15s, transform 0.1s; display: flex; align-items: center; gap: 6px;
  }
  .translate-btn:active { opacity: 0.8; transform: scale(0.97); }
  .translate-btn:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

  /* Translate status */
  .translate-status {
    display: none; align-items: center; gap: 10px;
    background: var(--bg); border: 1px solid var(--border);
    border-radius: 10px; padding: 12px 14px; margin-top: 10px;
    font-size: 0.78rem;
  }
  .translate-status.visible { display: flex; }
  .translate-status.success { border-color: var(--green); }
  .translate-status.error { border-color: rgba(255,80,80,0.4); color: #ff8080; }

  .spinner {
    width: 16px; height: 16px; border: 2px solid var(--border);
    border-top-color: var(--accent2); border-radius: 50%;
    animation: spin 0.7s linear infinite; flex-shrink: 0;
  }
  @keyframes spin { to { transform: rotate(360deg); } }

  .status-text { flex: 1; color: var(--muted); }
  .translate-status.success .status-text { color: var(--green); }

  /* Offset */
  .offset-row { display: flex; align-items: center; gap: 12px; }
  .offset-label { flex: 1; font-size: 0.82rem; color: var(--muted); }
  .offset-controls { display: flex; align-items: center; background: var(--bg); border: 1.5px solid var(--border); border-radius: 12px; overflow: hidden; }
  .offset-btn { background: none; border: none; color: var(--text); font-size: 1.2rem; width: 44px; height: 44px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
  .offset-btn:active { background: var(--border); }
  .offset-val { font-size: 0.82rem; font-weight: 700; min-width: 52px; text-align: center; color: var(--text); }

  /* Load button */
  .load-btn { width: 100%; background: var(--accent); color: #fff; border: none; font-family: 'Syne', sans-serif; font-size: 1rem; font-weight: 800; letter-spacing: 0.04em; padding: 18px; border-radius: 16px; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 10px; transition: opacity 0.15s, transform 0.1s; }
  .load-btn:active { opacity: 0.85; transform: scale(0.98); }

  /* Error */
  .error-card { background: rgba(255,80,80,0.08); border: 1px solid rgba(255,80,80,0.25); border-radius: 12px; padding: 14px 16px; font-size: 0.82rem; color: #ff8080; display: none; line-height: 1.6; }
  .error-card.visible { display: block; }

  /* Player */
  .player-section { display: none; flex-direction: column; gap: 16px; }
  .player-section.visible { display: flex; }
  .player-outer { border-radius: 16px; overflow: hidden; border: 1px solid var(--border); }
  .video-box { position: relative; width: 100%; padding-bottom: 56.25%; background: #000; height: 0; }
  .video-box iframe, .video-box video { position: absolute; top: 0; left: 0; width: 100%; height: 100%; border: none; display: block; }
  .video-box video { object-fit: contain; background: #000; }
  .sub-bar { background: #111; min-height: 54px; display: flex; align-items: center; justify-content: center; padding: 10px 16px; border-top: 1px solid var(--border); }
  .sub-text { color: #fff; font-family: 'Instrument Serif', serif; font-style: italic; font-size: clamp(0.95rem, 4vw, 1.2rem); line-height: 1.5; text-align: center; }
  .sub-text.empty { color: var(--muted); font-style: normal; font-size: 0.72rem; letter-spacing: 0.05em; }
  .player-bar { background: var(--card); padding: 12px 16px; display: flex; align-items: center; gap: 12px; border-top: 1px solid var(--border); }
  .time-label { font-size: 0.7rem; color: var(--muted); min-width: 90px; font-weight: 700; }
  .progress-track { flex: 1; height: 6px; background: var(--border); border-radius: 3px; cursor: pointer; }
  .progress-fill { height: 100%; background: var(--accent); border-radius: 3px; width: 0%; pointer-events: none; transition: width 0.3s linear; }
  .reset-btn { background: none; border: 1px solid var(--border); color: var(--muted); font-size: 0.9rem; width: 36px; height: 36px; border-radius: 8px; display: flex; align-items: center; justify-content: center; cursor: pointer; }
  .reset-btn:active { background: var(--border); }

  /* Subtitle list */
  .sublist-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 10px; }
  .sublist-title { font-size: 0.65rem; letter-spacing: 0.16em; text-transform: uppercase; color: var(--muted); }
  .sublist-count { font-size: 0.65rem; color: var(--accent2); font-weight: 700; }
  .sublist-body { background: var(--card); border: 1px solid var(--border); border-radius: 16px; overflow: hidden; max-height: 260px; overflow-y: auto; -webkit-overflow-scrolling: touch; }
  .sub-item { padding: 12px 16px; display: grid; grid-template-columns: 56px 1fr; gap: 12px; border-bottom: 1px solid rgba(255,255,255,0.04); cursor: pointer; }
  .sub-item:last-child { border-bottom: none; }
  .sub-item:active { background: rgba(255,255,255,0.04); }
  .sub-item.active { background: rgba(255,107,53,0.08); border-left: 2px solid var(--accent); }
  .sub-t { font-size: 0.68rem; color: var(--muted); font-weight: 700; padding-top: 2px; }
  .sub-c { font-size: 0.82rem; color: var(--text); line-height: 1.45; }

  .hint { font-size: 0.72rem; color: var(--muted); line-height: 1.7; text-align: center; padding: 0 8px; }
  .hint strong { color: var(--text); }
</style>
</head>
<body>

<div class="header">
  <div class="logo"><span>Sub</span>Drop</div>
  <div class="status-dot" id="statusDot"></div>
</div>

<div class="scroll-area">

  <!-- Video mode toggle -->
  <div class="mode-toggle">
    <button class="mode-btn active" id="btnModeLocal" onclick="setMode('local')">📁 Local Video</button>
    <button class="mode-btn" id="btnModeYT" onclick="setMode('youtube')">▶ YouTube Link</button>
  </div>

  <!-- LOCAL panel -->
  <div class="panel active" id="panelLocal">
    <div class="card">
      <div class="card-label">Video File</div>
      <div class="file-btn" id="videoBtn" onclick="document.getElementById('videoInput').click()">
        <div class="file-icon" id="videoIcon">🎬</div>
        <div class="file-info">
          <div class="file-name" id="videoName">Tap to choose video file</div>
          <div class="file-sub" id="videoSub">.mp4, .mkv, .webm, .mov from Downloads</div>
        </div>
      </div>
      <input type="file" id="videoInput" accept="video/*,.mkv">
    </div>
  </div>

  <!-- YOUTUBE panel -->
  <div class="panel" id="panelYT">
    <div class="card">
      <div class="card-label">YouTube URL</div>
      <div class="url-row">
        <input type="text" id="ytUrl" placeholder="Paste YouTube link here..." autocomplete="off" autocorrect="off" spellcheck="false">
        <button class="go-btn" onclick="pasteAndGo()">PASTE<br>&amp; GO</button>
      </div>
    </div>
  </div>

  <!-- SUBTITLE CARD -->
  <div class="card">
    <div class="card-label">Subtitles</div>

    <!-- Sub-tabs -->
    <div class="sub-tabs">
      <button class="sub-tab active" id="tabFile" onclick="setSubTab('file')">📄 Upload .srt</button>
      <button class="sub-tab" id="tabTranscript" onclick="setSubTab('transcript')">✏️ Paste Transcript</button>
    </div>

    <!-- File upload panel -->
    <div id="subPanelFile">
      <div class="file-btn" id="srtBtn" onclick="document.getElementById('srtInput').click()">
        <div class="file-icon" id="srtIcon">📄</div>
        <div class="file-info">
          <div class="file-name" id="srtName">Tap to choose .srt file</div>
          <div class="file-sub" id="srtSub">From your phone's files</div>
        </div>
      </div>
      <input type="file" id="srtInput" accept=".srt">
    </div>

    <!-- Transcript paste panel -->
    <div id="subPanelTranscript" style="display:none;">
      <textarea id="transcriptBox" placeholder="Paste your transcript here...

Examples of supported formats:
[00:01:23] Hello everyone, welcome back
(1:23) Hello everyone, welcome back
00:01:23 Hello everyone, welcome back
1:23 Hello everyone, welcome back"></textarea>

      <div class="transcript-actions">
        <div class="transcript-hint">Paste transcript → translate to English → subtitles ready</div>
        <button class="translate-btn" id="translateBtn" onclick="translateAndConvert()">
          🌐 Translate
        </button>
      </div>

      <!-- Status bar -->
      <div class="translate-status" id="translateStatus">
        <div class="spinner" id="translateSpinner"></div>
        <div class="status-text" id="translateStatusText">Translating...</div>
      </div>
    </div>
  </div>

  <!-- Offset -->
  <div class="card">
    <div class="card-label">Timing Offset</div>
    <div class="offset-row">
      <div class="offset-label">Nudge subtitles if they feel early or late</div>
      <div class="offset-controls">
        <button class="offset-btn" onclick="changeOffset(-0.5)">−</button>
        <div class="offset-val" id="offsetVal">0.0s</div>
        <button class="offset-btn" onclick="changeOffset(+0.5)">+</button>
      </div>
    </div>
  </div>

  <div class="error-card" id="errorCard"></div>

  <button class="load-btn" onclick="loadPlayer()">▶ &nbsp;Play with Subtitles</button>

  <!-- Player -->
  <div class="player-section" id="playerSection">
    <div class="player-outer">
      <div class="video-box" id="videoBox"></div>
      <div class="sub-bar">
        <div class="sub-text empty" id="subDisplay">— waiting for playback —</div>
      </div>
      <div class="player-bar">
        <div class="time-label" id="timeLabel">0:00 / 0:00</div>
        <div class="progress-track" id="progressTrack">
          <div class="progress-fill" id="progressFill"></div>
        </div>
        <button class="reset-btn" onclick="resetPlayer()">✕</button>
      </div>
    </div>
    <div>
      <div class="sublist-header">
        <div class="sublist-title">Subtitle List</div>
        <div class="sublist-count" id="sublistCount"></div>
      </div>
      <div class="sublist-body" id="sublistBody"></div>
    </div>
  </div>

  <p class="hint" id="hintText">
    <strong>Local Video:</strong> Use the .mp4 downloaded with yt-dlp + .srt or pasted transcript.<br><br>
    <strong>Make it an app:</strong> Chrome ⋮ → "Add to Home screen"
  </p>

</div>

<script>
// ── State ─────────────────────────────────────────────────
let cues = [];
let ticker = null;
let offsetSec = 0;
let mode = 'local';
let subTab = 'file';
let videoObjectURL = null;
let ytCurrentTime = 0;
let ytDuration = 0;

// ── Video mode toggle ──────────────────────────────────────
function setMode(m) {
  mode = m;
  document.getElementById('btnModeLocal').classList.toggle('active', m === 'local');
  document.getElementById('btnModeYT').classList.toggle('active', m === 'youtube');
  document.getElementById('panelLocal').classList.toggle('active', m === 'local');
  document.getElementById('panelYT').classList.toggle('active', m === 'youtube');
  clearError();
  resetPlayer();
}

// ── Subtitle tab toggle ────────────────────────────────────
function setSubTab(tab) {
  subTab = tab;
  document.getElementById('tabFile').classList.toggle('active', tab === 'file');
  document.getElementById('tabTranscript').classList.toggle('active', tab === 'transcript');
  document.getElementById('subPanelFile').style.display = tab === 'file' ? 'block' : 'none';
  document.getElementById('subPanelTranscript').style.display = tab === 'transcript' ? 'block' : 'none';
}

// ── Offset ─────────────────────────────────────────────────
function changeOffset(delta) {
  offsetSec = Math.round((offsetSec + delta) * 10) / 10;
  document.getElementById('offsetVal').textContent = (offsetSec >= 0 ? '+' : '') + offsetSec.toFixed(1) + 's';
}

// ── Paste & Go ─────────────────────────────────────────────
async function pasteAndGo() {
  let text = document.getElementById('ytUrl').value.trim();
  if (!text) {
    try { text = await navigator.clipboard.readText(); } catch(e) {
      document.getElementById('ytUrl').focus();
      showError('Tap and hold the text box → Paste — then tap PASTE & GO again.');
      return;
    }
  }
  if (text) { document.getElementById('ytUrl').value = text; loadPlayer(); }
  else { document.getElementById('ytUrl').focus(); showError('Copy a YouTube link first, then tap PASTE & GO.'); }
}

// ── SRT Parser ─────────────────────────────────────────────
function parseSRT(raw) {
  const blocks = raw.trim().replace(/\r\n/g,'\n').replace(/\r/g,'\n').split(/\n\s*\n/);
  const out = [];
  for (const b of blocks) {
    const lines = b.trim().split('\n');
    const tl = lines.find(l => l.includes('-->'));
    if (!tl) continue;
    const [a, b2] = tl.split('-->').map(s => s.trim());
    const start = srtSecs(a), end = srtSecs(b2);
    const txt = lines.slice(lines.indexOf(tl)+1).join('\n').replace(/<[^>]+>/g,'').trim();
    if (!isNaN(start) && txt) out.push({ start, end, text: txt });
  }
  return out;
}

function srtSecs(s) {
  const p = s.replace(',','.').split(':');
  return p.length === 3 ? +p[0]*3600 + +p[1]*60 + +p[2] : NaN;
}

function fmt(s) {
  s = Math.max(0, s || 0);
  return `${Math.floor(s/60)}:${String(Math.floor(s%60)).padStart(2,'0')}`;
}

// ── Transcript parser (timestamps → cues) ─────────────────
// Handles: [00:01:23], (1:23), 00:01:23, 1:23 at line start
function parseTranscript(raw) {
  const lines = raw.trim().replace(/\r\n/g,'\n').replace(/\r/g,'\n').split('\n');
  const entries = [];

  // Regex: optional bracket/paren, then H:MM:SS or M:SS or H:MM:SS.mmm
  const tsRe = /^[\[\(]?(\d{1,2}):(\d{2})(?::(\d{2}))?(?:[,.](\d+))?[\]\)]?\s*/;

  for (const line of lines) {
    const m = line.match(tsRe);
    if (!m) continue;
    const h = m[3] !== undefined ? +m[1] : 0;
    const min = m[3] !== undefined ? +m[2] : +m[1];
    const sec = m[3] !== undefined ? +m[3] : +m[2];
    const secs = h * 3600 + min * 60 + sec;
    const text = line.replace(tsRe, '').trim();
    if (text) entries.push({ start: secs, text });
  }

  // Build cues: each entry ends when the next one starts (or +5s for last)
  const cues = [];
  for (let i = 0; i < entries.length; i++) {
    const start = entries[i].start;
    const end = i < entries.length - 1 ? entries[i+1].start : start + 5;
    const text = entries[i].text;
    // Smart split: break long lines at ~60 chars on sentence boundary
    const chunks = smartChunk(text, start, end);
    cues.push(...chunks);
  }
  return cues;
}

// Split a long text segment into subtitle-sized chunks
function smartChunk(text, start, end) {
  const MAX_CHARS = 80;
  const duration = end - start;

  if (text.length <= MAX_CHARS) {
    return [{ start, end, text }];
  }

  // Split on sentence endings or commas
  const splitPoints = [];
  const re = /[.!?,]\s+/g;
  let m;
  while ((m = re.exec(text)) !== null) splitPoints.push(m.index + m[0].length);

  if (splitPoints.length === 0) {
    // Just split at midpoint word
    const mid = Math.floor(text.length / 2);
    const spaceAfter = text.indexOf(' ', mid);
    splitPoints.push(spaceAfter > -1 ? spaceAfter + 1 : mid);
  }

  // Pick split closest to middle
  const mid = text.length / 2;
  const bestSplit = splitPoints.reduce((a, b) => Math.abs(a - mid) < Math.abs(b - mid) ? a : b);

  const part1 = text.slice(0, bestSplit).trim();
  const part2 = text.slice(bestSplit).trim();
  const midTime = start + duration * (bestSplit / text.length);

  return [
    ...smartChunk(part1, start, midTime),
    ...smartChunk(part2, midTime, end)
  ];
}

// ── TRANSLATE & CONVERT ────────────────────────────────────
async function translateAndConvert() {
  const raw = document.getElementById('transcriptBox').value.trim();
  if (!raw) { showTranslateStatus('error', 'Please paste a transcript first.'); return; }

  const btn = document.getElementById('translateBtn');
  btn.disabled = true;
  showTranslateStatus('loading', 'Translating to English...');

  try {
    // Step 1: detect if it needs translation + translate using Claude API
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 4000,
        system: `You are a transcript translator. The user will give you a transcript with timestamps.
Your job:
1. Detect the language of the text portions (ignoring timestamps)
2. If already in English, return it exactly as-is unchanged
3. If not in English, translate ONLY the text portions to English — keep all timestamps exactly as they are, in exactly the same format
4. Keep the same line structure and timestamp format
5. Return ONLY the translated/original transcript, nothing else — no explanation, no preamble`,
        messages: [{ role: 'user', content: raw }]
      })
    });

    if (!response.ok) {
      const err = await response.json().catch(() => ({}));
      throw new Error(err.error?.message || `API error ${response.status}`);
    }

    const data = await response.json();
    const translated = data.content.find(b => b.type === 'text')?.text?.trim();
    if (!translated) throw new Error('Empty response from translation API');

    // Step 2: parse the translated transcript into cues
    const parsed = parseTranscript(translated);
    if (parsed.length === 0) {
      throw new Error('Could not find any timestamps in the transcript. Make sure lines start with [00:01:23] or (1:23) format.');
    }

    cues = parsed;
    document.getElementById('statusDot').classList.add('ready');
    showTranslateStatus('success', `✅ ${cues.length} subtitle cues ready — tap Play!`);

  } catch(err) {
    showTranslateStatus('error', '❌ ' + err.message);
  } finally {
    btn.disabled = false;
  }
}

function showTranslateStatus(type, msg) {
  const el = document.getElementById('translateStatus');
  const spinner = document.getElementById('translateSpinner');
  const text = document.getElementById('translateStatusText');
  el.className = 'translate-status visible ' + (type === 'success' ? 'success' : type === 'error' ? 'error' : '');
  spinner.style.display = type === 'loading' ? 'block' : 'none';
  text.textContent = msg;
  text.style.color = type === 'error' ? '#ff8080' : type === 'success' ? 'var(--green)' : 'var(--muted)';
}

// ── File inputs ────────────────────────────────────────────
document.getElementById('videoInput').addEventListener('change', function() {
  const file = this.files[0];
  if (!file) return;
  if (videoObjectURL) URL.revokeObjectURL(videoObjectURL);
  videoObjectURL = URL.createObjectURL(file);
  document.getElementById('videoBtn').classList.add('loaded');
  document.getElementById('videoIcon').textContent = '🎬';
  document.getElementById('videoName').textContent = file.name;
  document.getElementById('videoSub').textContent = (file.size / 1024 / 1024).toFixed(1) + ' MB';
});

document.getElementById('srtInput').addEventListener('change', function() {
  const file = this.files[0];
  if (!file) return;
  const reader = new FileReader();
  reader.onload = e => {
    cues = parseSRT(e.target.result);
    document.getElementById('srtBtn').classList.add('loaded');
    document.getElementById('srtIcon').textContent = '✅';
    document.getElementById('srtName').textContent = file.name;
    document.getElementById('srtSub').textContent = `${cues.length} subtitle cues loaded`;
    document.getElementById('statusDot').classList.add('ready');
  };
  reader.readAsText(file);
});

// ── YT ID ──────────────────────────────────────────────────
function getYTId(url) {
  const m = url.match(/(?:v=|\/embed\/|\/shorts\/|youtu\.be\/)([a-zA-Z0-9_-]{11})/);
  return m ? m[1] : null;
}

// ── Error ──────────────────────────────────────────────────
function showError(msg) { const el = document.getElementById('errorCard'); el.innerHTML = msg; el.classList.add('visible'); }
function clearError() { document.getElementById('errorCard').classList.remove('visible'); }

// ── Load Player ────────────────────────────────────────────
function loadPlayer() {
  clearError();
  if (!cues.length) {
    if (subTab === 'transcript') showError('Paste a transcript and tap 🌐 Translate first.');
    else showError('Please choose an .srt subtitle file first.');
    return;
  }
  if (mode === 'local') loadLocalPlayer();
  else loadYTPlayer();
}

// ── Local video ────────────────────────────────────────────
function loadLocalPlayer() {
  if (!videoObjectURL) { showError('Please choose a video file first.'); return; }
  const box = document.getElementById('videoBox');
  box.innerHTML = '';
  const vid = document.createElement('video');
  vid.src = videoObjectURL; vid.controls = true; vid.playsInline = true; vid.autoplay = true; vid.id = 'localVideo';
  box.appendChild(vid);
  document.getElementById('playerSection').classList.add('visible');
  buildList('local');
  clearInterval(ticker);
  ticker = setInterval(() => {
    const v = document.getElementById('localVideo');
    if (!v) return;
    const t = v.currentTime + offsetSec;
    const dur = v.duration || 0;
    if (dur > 0) {
      document.getElementById('progressFill').style.width = ((v.currentTime / dur) * 100).toFixed(2) + '%';
      document.getElementById('timeLabel').textContent = `${fmt(v.currentTime)} / ${fmt(dur)}`;
    }
    const active = cues.find(c => t >= c.start && t < c.end);
    const subEl = document.getElementById('subDisplay');
    if (active) { subEl.textContent = active.text; subEl.classList.remove('empty'); }
    else { subEl.textContent = '—'; subEl.classList.add('empty'); }
    document.querySelectorAll('.sub-item').forEach((el, i) => {
      const isActive = cues[i] === active;
      if (isActive && !el.classList.contains('active')) { el.classList.add('active'); el.scrollIntoView({ block: 'nearest', behavior: 'smooth' }); }
      else if (!isActive) el.classList.remove('active');
    });
  }, 150);
  setTimeout(() => document.getElementById('playerSection').scrollIntoView({ behavior: 'smooth', block: 'start' }), 300);
}

// ── YouTube ────────────────────────────────────────────────
function loadYTPlayer() {
  const url = document.getElementById('ytUrl').value.trim();
  const vid = getYTId(url);
  if (!vid) { showError('Couldn\'t find a YouTube video ID.<br>Try: <b>youtube.com/watch?v=XXXXXXXXXXX</b>'); return; }
  ytCurrentTime = 0; ytDuration = 0;
  const box = document.getElementById('videoBox');
  box.innerHTML = '';
  const frame = document.createElement('iframe');
  frame.id = 'ytFrame';
  frame.src = `https://www.youtube-nocookie.com/embed/${vid}?autoplay=1&playsinline=1&rel=0&modestbranding=1&enablejsapi=1`;
  frame.allow = 'autoplay; encrypted-media; fullscreen; picture-in-picture';
  frame.allowFullscreen = true;
  box.appendChild(frame);
  document.getElementById('playerSection').classList.add('visible');
  buildList('youtube');
  window.removeEventListener('message', onYTMessage);
  window.addEventListener('message', onYTMessage);
  clearInterval(ticker);
  ticker = setInterval(() => {
    const t = ytCurrentTime + offsetSec;
    if (ytDuration > 0) {
      document.getElementById('progressFill').style.width = Math.min(100, (ytCurrentTime / ytDuration) * 100).toFixed(2) + '%';
      document.getElementById('timeLabel').textContent = `${fmt(ytCurrentTime)} / ${fmt(ytDuration)}`;
    }
    const active = cues.find(c => t >= c.start && t < c.end);
    const subEl = document.getElementById('subDisplay');
    if (active) { subEl.textContent = active.text; subEl.classList.remove('empty'); }
    else { subEl.textContent = '—'; subEl.classList.add('empty'); }
    document.querySelectorAll('.sub-item').forEach((el, i) => {
      const isActive = cues[i] === active;
      if (isActive && !el.classList.contains('active')) { el.classList.add('active'); el.scrollIntoView({ block: 'nearest', behavior: 'smooth' }); }
      else if (!isActive) el.classList.remove('active');
    });
  }, 300);
  setTimeout(() => document.getElementById('playerSection').scrollIntoView({ behavior: 'smooth', block: 'start' }), 300);
}

function onYTMessage(e) {
  try {
    const d = typeof e.data === 'string' ? JSON.parse(e.data) : e.data;
    if (!d) return;
    if (d.event === 'infoDelivery' && d.info) {
      if (typeof d.info.currentTime === 'number') ytCurrentTime = d.info.currentTime;
      if (typeof d.info.duration === 'number') ytDuration = d.info.duration;
    }
  } catch(err) {}
}

// ── Subtitle list ──────────────────────────────────────────
function buildList(playerMode) {
  const body = document.getElementById('sublistBody');
  body.innerHTML = '';
  document.getElementById('sublistCount').textContent = `${cues.length} cues`;
  cues.forEach(c => {
    const div = document.createElement('div');
    div.className = 'sub-item';
    div.innerHTML = `<div class="sub-t">${fmt(c.start)}</div><div class="sub-c">${c.text.replace(/\n/g,' ')}</div>`;
    div.onclick = () => {
      const seekTo = c.start - offsetSec;
      if (playerMode === 'local') { const v = document.getElementById('localVideo'); if (v) v.currentTime = seekTo; }
      else {
        const frame = document.getElementById('ytFrame');
        if (frame) { try { frame.contentWindow.postMessage(JSON.stringify({ event: 'command', func: 'seekTo', args: [seekTo, true] }), '*'); } catch(e) {} ytCurrentTime = seekTo; }
      }
    };
    body.appendChild(div);
  });
}

// ── Reset ──────────────────────────────────────────────────
function resetPlayer() {
  clearInterval(ticker);
  window.removeEventListener('message', onYTMessage);
  document.getElementById('videoBox').innerHTML = '';
  document.getElementById('playerSection').classList.remove('visible');
  const subEl = document.getElementById('subDisplay');
  subEl.textContent = '— waiting for playback —';
  subEl.classList.add('empty');
  document.getElementById('progressFill').style.width = '0';
  document.getElementById('timeLabel').textContent = '0:00 / 0:00';
  ytCurrentTime = 0; ytDuration = 0;
  clearError();
}

// ── Progress bar ───────────────────────────────────────────
document.getElementById('progressTrack').addEventListener('click', function(e) {
  const rect = this.getBoundingClientRect();
  const ratio = (e.clientX - rect.left) / rect.width;
  if (mode === 'local') {
    const v = document.getElementById('localVideo');
    if (v && v.duration) v.currentTime = ratio * v.duration;
  } else {
    if (!ytDuration) return;
    const seekTo = ratio * ytDuration;
    ytCurrentTime = seekTo;
    const frame = document.getElementById('ytFrame');
    if (frame) { try { frame.contentWindow.postMessage(JSON.stringify({ event: 'command', func: 'seekTo', args: [seekTo, true] }), '*'); } catch(e) {} }
  }
});
</script>
</body>
</html>

