<!DOCTYPE html>  
<html lang="en">  
<head>  
  <meta charset="UTF-8">  
  <title>DCML Pathway Interactive Model</title>  
  <style>  
    body {  
      font-family: Arial, sans-serif;  
      background: #0f172a;  
      color: #f8fafc;  
      display: flex;  
      flex-direction: column;  
      align-items: center;  
      margin: 0;  
      padding: 20px;  
    }  
    h1 { margin-bottom: 5px; }  
    p { color: #94a3b8; margin-bottom: 20px; }  
      
    .container {  
      display: flex;  
      gap: 20px;  
      background: #1e293b;  
      padding: 20px;  
      border-radius: 12px;  
      box-shadow: 0 10px 25px rgba(0,0,0,0.5);  
    }  
      
    svg {  
      border: 2px solid #334155;  
      background: #090d16;  
      border-radius: 8px;  
    }  
  
    .node {  
      fill: #38bdf8;  
      stroke: #0284c7;  
      stroke-width: 2;  
      cursor: pointer;  
      transition: all 0.3s;  
    }  
    .node:hover { fill: #f59e0b; }  
  
    .pathway {  
      stroke: #475569;  
      stroke-width: 4;  
      fill: none;  
      stroke-dasharray: 8 4;  
    }  
  
    .pathway.active {  
      stroke: #22c55e;  
      stroke-dasharray: none;  
    }  
  
    .pathway.lesioned {  
      stroke: #ef4444 !important;  
      stroke-dasharray: 4 4;  
    }  
  
    /* Signal animation dot */  
    #signal {  
      fill: #facc15;  
      display: none;  
    }  
  
    .controls {  
      width: 300px;  
      display: flex;  
      flex-direction: column;  
      gap: 15px;  
    }  
  
    button {  
      background: #2563eb;  
      color: white;  
      border: none;  
      padding: 12px;  
      border-radius: 6px;  
      font-weight: bold;  
      cursor: pointer;  
      transition: background 0.2s;  
    }  
    button:hover { background: #1d4ed8; }  
  
    .info-panel {  
      background: #0f172a;  
      padding: 15px;  
      border-radius: 6px;  
      border-left: 4px solid #38bdf8;  
      min-height: 120px;  
    }  
      
    .status-ok { color: #22c55e; }  
    .status-cut { color: #ef4444; }  
  </style>  
</head>  
<body>  
  
  <h1>DCML Pathway Neural Circuit</h1>  
  <p>Click "Fire Signal" to trace an impulse, or click a lesion point to test clinical outcomes.</p>  
  
  <div class="container">  
    <!-- SVG Diagram Canvas -->  
    <svg width="340" height="520" viewBox="0 0 340 520">  
      <!-- Anatomical Sections -->  
      <rect x="20" y="20" width="300" height="70" rx="8" fill="#1e1b4b" stroke="#4338ca"/>  
      <text x="35" y="45" fill="#a5b4fc" font-size="12" font-weight="bold">PRIMARY SOMATOSENSORY CORTEX</text>  
  
      <rect x="20" y="120" width="300" height="60" rx="8" fill="#1e1b4b" stroke="#4338ca"/>  
      <text x="35" y="145" fill="#a5b4fc" font-size="12" font-weight="bold">THALAMUS (VPL Nucleus)</text>  
  
      <rect x="20" y="210" width="300" height="90" rx="8" fill="#312e81" stroke="#6366f1"/>  
      <text x="35" y="235" fill="#c7d2fe" font-size="12" font-weight="bold">LOWER MEDULLA (Decussation)</text>  
  
      <rect x="20" y="330" width="300" height="80" rx="8" fill="#1e1b4b" stroke="#4338ca"/>  
      <text x="35" y="355" fill="#a5b4fc" font-size="12" font-weight="bold">SPINAL CORD (Dorsal Column)</text>  
  
      <rect x="20" y="440" width="300" height="60" rx="8" fill="#065f46" stroke="#059669"/>  
      <text x="35" y="465" fill="#6ee7b7" font-size="12" font-weight="bold">PERIPHERY (Fine Touch / Proprioception)</text>  
  
      <!-- Pathway Lines (Neural Connections) -->  
      <!-- 1st Order Neuron -->  
      <path id="segment1" class="pathway" d="M 170 450 L 170 350" />  
      <!-- 2nd Order Decussation in Medulla -->  
      <path id="segment2" class="pathway" d="M 170 350 L 170 270 L 250 230 L 250 150" />  
      <!-- 3rd Order Thalamocortical Neuron -->  
      <path id="segment3" class="pathway" d="M 250 150 L 250 50" />  
  
      <!-- Clickable Lesion Target Nodes -->  
      <circle id="node-spinal" class="node" cx="170" cy="370" r="10" onclick="toggleLesion('spinal')" />  
      <circle id="node-medulla" class="node" cx="210" cy="250" r="10" onclick="toggleLesion('medulla')" />  
  
      <!-- Animated Impulse Dot -->  
      <circle id="signal" r="7" />  
    </svg>  
  
    <!-- Interactive Sidebar -->  
    <div class="controls">  
      <button onclick="fireSignal()">⚡ Fire Sensory Signal</button>  
      <button onclick="resetModel()" style="background: #475569;">🔄 Reset Circuit</button>  
  
      <div class="info-panel">  
        <h4 id="info-title" style="margin-top:0;">Circuit Status</h4>  
        <div id="info-body">Pathways intact. Click "Fire Sensory Signal" to begin stimulus transmission.</div>  
      </div>  
    </div>  
  </div>  
  
  <script>  
    let isSpinalLesioned = false;  
    let isMedullaLesioned = false;  
  
    function toggleLesion(location) {  
      if (location === 'spinal') {  
        isSpinalLesioned = !isSpinalLesioned;  
        document.getElementById('segment1').classList.toggle('lesioned', isSpinalLesioned);  
      } else if (location === 'medulla') {  
        isMedullaLesioned = !isMedullaLesioned;  
        document.getElementById('segment2').classList.toggle('lesioned', isMedullaLesioned);  
      }  
      updateStatus();  
    }  
  
    function updateStatus() {  
      const title = document.getElementById('info-title');  
      const body = document.getElementById('info-body');  
  
      if (isSpinalLesioned) {  
        title.innerHTML = "⚠️ Spinal Cord Lesion";  
        body.innerHTML = "<b class='status-cut'>Ipsilateral Loss:</b> Damage before decussation causes loss of fine touch and vibration on the <b>SAME side</b> as the lesion.";  
      } else if (isMedullaLesioned) {  
        title.innerHTML = "⚠️ Post-Decussation Lesion";  
        body.innerHTML = "<b class='status-cut'>Contralateral Loss:</b> Damage after decussation in the medulla causes loss of sensation on the <b>OPPOSITE side</b> of the body.";  
      } else {  
        title.innerHTML = "Circuit Status";  
        body.innerHTML = "<span class='status-ok'>Pathway Intact.</span> Signal will travel unhindered to the cortex.";  
      }  
    }  
  
    function fireSignal() {  
      const signal = document.getElementById('signal');  
      signal.style.display = 'block';  
  
      // Animation keypoints  
      const points = [  
        {x: 170, y: 450}, // Start at receptor  
        {x: 170, y: 370}, // Pass spinal node  
        {x: 170, y: 270}, // Enter medulla  
        {x: 250, y: 230}, // Cross over (Decussate)  
        {x: 250, y: 150}, // Enter Thalamus  
        {x: 250, y: 50}   // Arrive at Somatosensory Cortex  
      ];  
  
      let step = 0;  
  
      function move() {  
        // Check for blockages  
        if (step === 1 && isSpinalLesioned) {  
          document.getElementById('info-body').innerHTML += "<br><br><b class='status-cut'>🛑 Signal Blocked at Spinal Cord!</b>";  
          return;  
        }  
        if (step === 3 && isMedullaLesioned) {  
          document.getElementById('info-body').innerHTML += "<br><br><b class='status-cut'>🛑 Signal Blocked at Medulla!</b>";  
          return;  
        }  
  
        if (step < points.length) {  
          signal.setAttribute('cx', points[step].x);  
          signal.setAttribute('cy', points[step].y);  
          step++;  
          setTimeout(move, 400); // Speed of signal  
        } else {  
          document.getElementById('info-body').innerHTML += "<br><br><b class='status-ok'>🧠 Signal reached Somatosensory Cortex! Perception registered.</b>";  
        }  
      }  
  
      move();  
    }  
  
    function resetModel() {  
      isSpinalLesioned = false;  
      isMedullaLesioned = false;  
      document.getElementById('segment1').classList.remove('lesioned');  
      document.getElementById('segment2').classList.remove('lesioned');  
      document.getElementById('signal').style.display = 'none';  
      updateStatus();  
    }  
  </script>  
</body>  
</html>  
