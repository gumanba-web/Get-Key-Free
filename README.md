<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
  <title>KEY VAULT · 4-Step Verification</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      background: #0b0b12;
      color: #e0e0e8;
      font-family: system-ui, 'Segoe UI', Roboto, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 16px;
      margin: 0;
      line-height: 1.4;
    }

    .container {
      max-width: 500px;
      width: 100%;
      display: flex;
      flex-direction: column;
      gap: 16px;
    }

    .header {
      text-align: center;
      margin-bottom: 4px;
    }

    .header h1 {
      font-size: 1.7rem;
      background: linear-gradient(135deg, #25D366, #34ebb4);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
    }

    .progress-wrap {
      background: #13131d;
      border-radius: 30px;
      padding: 10px 18px;
      border: 1px solid #252535;
    }

    .progress-bar {
      height: 7px;
      background: #1e1e2e;
      border-radius: 20px;
      overflow: hidden;
      margin: 6px 0 4px;
    }

    .progress-fill {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, #25D366, #34ebb4);
      border-radius: 20px;
      transition: width .4s ease;
    }

    .progress-label {
      font-size: 0.75rem;
      color: #9f9fb8;
      text-align: center;
    }

    .steps {
      display: flex;
      flex-direction: column;
      gap: 14px;
    }

    .step-card {
      background: #111119;
      border: 1.5px solid #232335;
      border-radius: 18px;
      padding: 16px;
      display: flex;
      flex-direction: column;
      gap: 8px;
      transition: 0.2s;
      position: relative;
    }

    .step-card.done {
      border-color: #25D366;
      background: #0f1f17;
    }

    .step-card.locked {
      opacity: 0.5;
      pointer-events: none;
      filter: grayscale(0.3);
    }

    .step-badge {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      font-weight: 600;
      font-size: 0.7rem;
      text-transform: uppercase;
      letter-spacing: 0.4px;
      padding: 3px 10px;
      border-radius: 20px;
      width: fit-content;
      background: #1e1e2e;
      color: #aaa;
    }

    .step-card.done .step-badge {
      background: #1a3822;
      color: #25D366;
    }

    .step-title {
      font-weight: 600;
      font-size: 1rem;
    }

    .step-link {
      font-size: 0.7rem;
      color: #8a8aa0;
      word-break: break-all;
      background: #0a0a14;
      padding: 4px 8px;
      border-radius: 6px;
      font-family: monospace;
      border: 1px solid #1f1f2e;
    }

    .btn {
      font-weight: 600;
      border: none;
      border-radius: 30px;
      padding: 10px 18px;
      font-size: 0.8rem;
      cursor: pointer;
      transition: all 0.2s;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      gap: 5px;
      width: 100%;
      background: #1e1e2e;
      color: #ccc;
      border: 1px solid #333;
    }

    .btn.primary {
      background: #25D366;
      color: #000;
      border-color: #25D366;
      font-weight: 700;
    }

    .btn.primary:disabled {
      background: #2a2a3a;
      color: #6a6a7a;
      border-color: #3a3a4a;
    }

    .btn.countdown {
      background: #1e1a2a;
      color: #ffa502;
      border-color: #3a3540;
      cursor: default;
      font-family: monospace;
    }

    .btn.done-btn {
      background: #142616;
      color: #25D366;
      border-color: #1f4a28;
      cursor: default;
    }

    .timer-dot {
      display: inline-block;
      width: 7px;
      height: 7px;
      background: #ffa502;
      border-radius: 50%;
      animation: blink 0.8s infinite;
    }

    @keyframes blink {
      0%, 100% { opacity: 1; }
      50% { opacity: 0.2; }
    }

    .hidden {
      display: none !important;
    }

    .toast {
      position: fixed;
      top: 20px;
      left: 50%;
      transform: translateX(-50%);
      background: #1e1e2e;
      color: #fff;
      padding: 10px 22px;
      border-radius: 30px;
      font-size: 0.8rem;
      z-index: 999;
      box-shadow: 0 8px 24px rgba(0,0,0,0.5);
      animation: fadeOut 2s forwards;
    }

    @keyframes fadeOut {
      0% { opacity: 1; top: 20px; }
      70% { opacity: 1; }
      100% { opacity: 0; top: 0; }
    }

    .key-area {
      margin-top: 8px;
      background: #0b0b15;
      border: 2px solid #25D366;
      border-radius: 18px;
      padding: 18px;
      display: none;
      flex-direction: column;
      gap: 12px;
    }

    .key-area.show {
      display: flex;
    }

    .key-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: #0d0d1c;
      padding: 10px 12px;
      border-radius: 12px;
      border: 1px solid #2a2a3a;
      gap: 8px;
    }

    .key-text {
      font-family: 'SF Mono', 'Fira Code', monospace;
      font-size: 0.7rem;
      color: #34ebb4;
      word-break: break-all;
      flex: 1;
      user-select: all;
    }

    .copy-btn {
      background: #203a2a;
      color: #34ebb4;
      border: 1px solid #2f5a3a;
      padding: 6px 14px;
      border-radius: 20px;
      cursor: pointer;
      font-weight: 600;
      font-size: 0.7rem;
    }

    .copy-btn.copied {
      background: #25D366;
      color: #000;
      border-color: #25D366;
    }

    .cooldown-info {
      text-align: center;
      font-size: 0.75rem;
      color: #ffa502;
      margin-top: 6px;
      padding: 6px;
      background: #1e1a2a;
      border-radius: 30px;
    }
  </style>
</head>
<body>
<div class="container">
  <div class="header">
    <h1>🔐 KEY UNLOCKER</h1>
    <p style="color:#aaa;font-size:0.8rem;">Complete 4 verification steps</p>
  </div>

  <div class="progress-wrap" id="progressWrap">
    <div class="progress-bar"><div class="progress-fill" id="progressFill"></div></div>
    <div class="progress-label" id="progressLabel">0 / 4 steps</div>
  </div>

  <div class="steps" id="stepsContainer"></div>

  <div class="key-area" id="keyArea">
    <div style="text-align:center;font-weight:700;font-size:1.2rem;">🏆 Your Key</div>
    <div id="keyDisplay"></div>
    <div class="cooldown-info" id="cooldownTimer"></div>
    <p style="font-size:0.65rem;color:#666;text-align:center;">Key resets after 17 hours cooldown</p>
  </div>
</div>

<script>
  (function() {
    // =============================================================
    // ALL 45 KEYS (exactly as provided)
    // =============================================================
    const FULL_KEYS_ARRAY = [
      "FREE_7a91bc2dff44aa8891c3e5d72ab41f0", "FREE_b18d4e7fa9234cc0ad5512f89ee73c1", "FREE_c55fa81d239b4aa1be73d66c9012ef4",
      "FREE_d903ab72ce114fd58aa17c9e2bb46f0", "FREE_e11c5d7ab8824f10bde44aa93f6c72e1", "FREE_f77ab29c3d884e50ac112ef7bb90d4c6",
      "FREE_1ae73bc944d24ff0a8c12de66ab97f51", "FREE_2bc90dd7ea114a5cb8831ff6de72a4b0", "FREE_3d61af8ce55244a9b7e11dc90ab5f237",
      "FREE_4ee72bc1a9d84f60c1ab35dd7712ef90", "FREE_5ab31fdce72448b0af6619cde28f73aa", "FREE_6c82de14bbf5479da9f12ee73ac550b1",
      "FREE_7df11ab893cc46b1be44f72ad88c9102", "FREE_8aa73bc55de24f10c9d17ffb623ae441", "FREE_9ce42df71ab547e0aa311fd88bc29e76",
      "FREE_a11bb22cc33dd44ee55ff6677889900", "FREE_b72ac18de95f4aa0c113ef76db28f4c9", "FREE_c8d19af62ee4477bbca51d9f20ab61e3",
      "FREE_d55ee71abf234cc19a880df72be46f5a", "FREE_e9f01bc772dd44a3be18cf55aa27d601", "FREE_f3ab82de14cc4f80bd7719ea62cf55d4",
      "FREE_10aa9bc81de744f0ac22bb67ef31d990", "FREE_21bc7de93fa1448dbf551ac72ee09c14", "FREE_32cd8fa11bb24e70ad337ef98bc52f61",
      "FREE_43de91ab72fc45c8be11da77ff20c5e2", "FREE_54ef18cd63aa4d91bc552f77de31ab80", "FREE_65fa27de81bb4e60af991cc72ed44b51",
      "FREE_76ab38ef92cc47d0be117fd63aa58c20", "FREE_87bc49fa03dd48a1cf662ee74bb19d35", "FREE_98cd50ab14ee49f2da881ff85cc20e46",
      "FREE_a9de61bc25ff40a3eb772aa96dd31f57", "FREE_b0ef72cd36aa41b4fc993bb07ee42a68", "FREE_c1fa83de47bb42c5ad114cc18ff53b79",
      "FREE_d2ab94ef58cc43d6be225dd29aa64c80", "FREE_e3bc05fa69dd44e7cf336ee30bb75d91", "FREE_f4cd16ab70ee45f8da447ff41cc86ea2",
      "FREE_05de27bc81ff46a9eb558aa52dd97fb3", "FREE_16ef38cd92aa47b0fc669bb63ee08ac4", "FREE_27fa49de03bb48c1ad770cc74ff19bd5",
      "FREE_38ab50ef14cc49d2be881dd85aa20ce6"
    ];

    // Steps configuration (cooldowns: 67, 69, 67, 69 seconds)
    const STEPS = [
      { name: "WhatsApp Contact", link: "https://wa.me/6285839382031", cooldown: 67 },
      { name: "WhatsApp Channel", link: "https://whatsapp.com/channel/0029VbCOhBnJkK73p2VAGl3R", cooldown: 69 },
      { name: "WhatsApp Group", link: "https://chat.whatsapp.com/KLNDOJYPDOc4fPFBKXYkTE", cooldown: 67 },
      { name: "Exclusive Channel", link: "https://whatsapp.com/channel/0029VbCKMBO3bbVAVeO9pY2T", cooldown: 69 }
    ];

    const STORAGE_UNLOCKED = "keyVault_unlocked";
    const STORAGE_KEY = "keyVault_key";
    const STORAGE_TIMESTAMP = "keyVault_timestamp";
    const COOLDOWN_HOURS = 17;
    const COOLDOWN_MS = COOLDOWN_HOURS * 60 * 60 * 1000;

    let completedSteps = 0;
    let activeTimer = null;
    let allStepsDone = false;
    let stepCountdownActive = {1: false, 2: false, 3: false, 4: false};
    let cooldownInterval = null;

    const progressFill = document.getElementById('progressFill');
    const progressLabel = document.getElementById('progressLabel');
    const stepsContainer = document.getElementById('stepsContainer');
    const keyArea = document.getElementById('keyArea');
    const keyDisplayDiv = document.getElementById('keyDisplay');
    const cooldownTimerDiv = document.getElementById('cooldownTimer');

    function showToast(msg) {
      const t = document.createElement('div');
      t.className = 'toast';
      t.textContent = msg;
      document.body.appendChild(t);
      setTimeout(() => t.remove(), 2100);
    }

    function updateProgressUI() {
      const pct = (completedSteps / 4) * 100;
      progressFill.style.width = pct + '%';
      progressLabel.textContent = `${completedSteps} / 4 steps`;
    }

    function renderSteps() {
      stepsContainer.innerHTML = STEPS.map((step, idx) => {
        const stepNum = idx + 1;
        return `
          <div class="step-card" id="stepCard${stepNum}">
            <span class="step-badge">🔗 STEP ${stepNum}</span>
            <div class="step-title">${step.name}</div>
            <div class="step-link">${step.link.replace('https://', '')}</div>
            <button class="btn primary" id="btnOpen${stepNum}">🔓 Verify & Open</button>
            <button class="btn countdown hidden" id="btnTimer${stepNum}"><span class="timer-dot"></span> <span id="timerText${stepNum}">${step.cooldown}</span> seconds</button>
            <button class="btn done-btn hidden" id="btnDone${stepNum}" disabled>✅ Verified</button>
          </div>`;
      }).join('');
    }

    function setStepUI(stepNum, state) {
      const card = document.getElementById(`stepCard${stepNum}`);
      const btnOpen = document.getElementById(`btnOpen${stepNum}`);
      const btnTimer = document.getElementById(`btnTimer${stepNum}`);
      const btnDone = document.getElementById(`btnDone${stepNum}`);
      if (!card) return;

      card.classList.remove('done', 'locked');
      btnOpen?.classList.add('hidden');
      btnTimer?.classList.add('hidden');
      btnDone?.classList.add('hidden');

      if (state === 'locked') {
        card.classList.add('locked');
        btnOpen?.classList.remove('hidden');
        btnOpen.disabled = true;
      } else if (state === 'active') {
        btnOpen?.classList.remove('hidden');
        btnOpen.disabled = false;
      } else if (state === 'counting') {
        btnTimer?.classList.remove('hidden');
      } else if (state === 'done') {
        card.classList.add('done');
        btnDone?.classList.remove('hidden');
      }
    }

    function startCountdownForStep(stepNum) {
      const step = STEPS[stepNum - 1];
      let remaining = step.cooldown;
      const timerSpan = document.getElementById(`timerText${stepNum}`);
      
      setStepUI(stepNum, 'counting');
      stepCountdownActive[stepNum] = true;
      
      if (activeTimer) clearInterval(activeTimer);
      activeTimer = setInterval(() => {
        remaining--;
        if (timerSpan) timerSpan.textContent = remaining;
        
        if (remaining <= 0) {
          clearInterval(activeTimer);
          activeTimer = null;
          stepCountdownActive[stepNum] = false;
          completeCurrentStep(stepNum);
        }
      }, 1000);
    }

    function completeCurrentStep(stepNum) {
      if (completedSteps >= stepNum) return;
      
      completedSteps = stepNum;
      setStepUI(stepNum, 'done');
      updateProgressUI();
      
      if (completedSteps === 4) {
        allStepsDone = true;
        const randomIndex = Math.floor(Math.random() * FULL_KEYS_ARRAY.length);
        const selectedKey = FULL_KEYS_ARRAY[randomIndex];
        const timestamp = Date.now();
        localStorage.setItem(STORAGE_UNLOCKED, "true");
        localStorage.setItem(STORAGE_KEY, selectedKey);
        localStorage.setItem(STORAGE_TIMESTAMP, timestamp);
        showKeyOnPage(selectedKey);
        document.getElementById('progressWrap').style.display = 'none';
        stepsContainer.style.display = 'none';
        startCooldownTimer(timestamp);
        sendWhatsAppThanks();
        showToast('🎉 All steps completed! Your key is ready.');
        return;
      }
      
      const nextStep = stepNum + 1;
      if (nextStep <= 4) {
        setStepUI(nextStep, 'active');
      }
    }

    function handleVerifyClick(stepNum) {
      if (completedSteps >= stepNum) {
        showToast('Step already completed');
        return;
      }
      if (stepNum > 1 && completedSteps < stepNum - 1) {
        showToast('Complete previous steps first');
        return;
      }
      if (stepCountdownActive[stepNum]) {
        showToast('Verification already in progress, please wait');
        return;
      }
      
      const step = STEPS[stepNum - 1];
      window.open(step.link, '_blank', 'noopener');
      startCountdownForStep(stepNum);
      showToast(`✅ Verification started! Wait ${step.cooldown} seconds.`);
    }

    function showKeyOnPage(key) {
      keyDisplayDiv.innerHTML = `
        <div class="key-item">
          <span class="key-text" id="theKeyText">${key}</span>
          <button class="copy-btn" id="copyKeyBtn">📋 Copy</button>
        </div>
      `;
      keyArea.classList.add('show');
      const copyBtn = document.getElementById('copyKeyBtn');
      if (copyBtn) {
        copyBtn.addEventListener('click', () => {
          const keyText = document.getElementById('theKeyText').innerText;
          navigator.clipboard.writeText(keyText).then(() => {
            copyBtn.textContent = '✅ Copied!';
            copyBtn.classList.add('copied');
            setTimeout(() => {
              copyBtn.textContent = '📋 Copy';
              copyBtn.classList.remove('copied');
            }, 2000);
          }).catch(() => {
            alert('Copy manually: ' + keyText);
          });
        });
      }
    }

    function startCooldownTimer(timestamp) {
      if (cooldownInterval) clearInterval(cooldownInterval);
      
      function updateTimer() {
        const now = Date.now();
        const elapsed = now - timestamp;
        const remaining = COOLDOWN_MS - elapsed;
        
        if (remaining <= 0) {
          clearInterval(cooldownInterval);
          resetAndReload();
          return;
        }
        
        const hours = Math.floor(remaining / (60 * 60 * 1000));
        const minutes = Math.floor((remaining % (60 * 60 * 1000)) / (60 * 1000));
        const seconds = Math.floor((remaining % (60 * 1000)) / 1000);
        cooldownTimerDiv.textContent = `⏳ Cooldown: ${hours}h ${minutes}m ${seconds}s until key resets`;
      }
      
      updateTimer();
      cooldownInterval = setInterval(updateTimer, 1000);
    }

    function resetAndReload() {
      localStorage.removeItem(STORAGE_UNLOCKED);
      localStorage.removeItem(STORAGE_KEY);
      localStorage.removeItem(STORAGE_TIMESTAMP);
      window.location.reload();
    }

    function sendWhatsAppThanks() {
      const waNumber = "6285839382031";
      const thankMsg = "Thank you for the key Sir 😃!";
      const waUrl = `https://wa.me/${waNumber}?text=${encodeURIComponent(thankMsg)}`;
      window.open(waUrl, '_blank');
    }

    function bindStepEvents() {
      for (let i = 1; i <= 4; i++) {
        const btn = document.getElementById(`btnOpen${i}`);
        if (btn) {
          btn.addEventListener('click', (e) => {
            e.preventDefault();
            handleVerifyClick(i);
          });
        }
      }
    }

    function checkAlreadyUnlocked() {
      const unlocked = localStorage.getItem(STORAGE_UNLOCKED);
      const savedKey = localStorage.getItem(STORAGE_KEY);
      const savedTimestamp = localStorage.getItem(STORAGE_TIMESTAMP);
      
      if (unlocked === "true" && savedKey && savedTimestamp) {
        const timestamp = parseInt(savedTimestamp, 10);
        const now = Date.now();
        const elapsed = now - timestamp;
        
        if (elapsed < COOLDOWN_MS) {
          document.getElementById('progressWrap').style.display = 'none';
          stepsContainer.style.display = 'none';
          showKeyOnPage(savedKey);
          startCooldownTimer(timestamp);
          allStepsDone = true;
          return true;
        } else {
          localStorage.removeItem(STORAGE_UNLOCKED);
          localStorage.removeItem(STORAGE_KEY);
          localStorage.removeItem(STORAGE_TIMESTAMP);
          window.location.reload();
          return false;
        }
      }
      return false;
    }

    function init() {
      if (checkAlreadyUnlocked()) {
        return;
      }
      renderSteps();
      setStepUI(1, 'active');
      for (let i = 2; i <= 4; i++) {
        setStepUI(i, 'locked');
      }
      updateProgressUI();
      bindStepEvents();
      allStepsDone = false;
      completedSteps = 0;
      stepCountdownActive = {1: false, 2: false, 3: false, 4: false};
      if (activeTimer) clearInterval(activeTimer);
    }

    init();
  })();
</script>
</body>
</html>
