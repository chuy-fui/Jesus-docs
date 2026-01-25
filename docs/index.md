# Inicio
<div class="mechatronics-panel">

  <div class="status-bar">
    <div id="status-led" class="led off"></div>
    <span id="status-text">SYSTEM STOPPED</span>
  </div>

  <div class="conveyor">
    <div class="belt"></div>
    <div class="box" style="left: 0px;"></div>
    <div class="box" style="left: 120px;"></div>
    <div class="box" style="left: 240px;"></div>
  </div>

  <div class="controls">
    <button class="ctrl-btn start" onclick="startBelt()">START</button>
    <button class="ctrl-btn stop" onclick="stopBelt()">STOP</button>
    <button class="ctrl-btn reverse" onclick="reverseBelt()">REVERSE</button>
  </div>

</div>

<style>
.mechatronics-panel {
  background: linear-gradient(145deg, #0f0f0f, #1c1c1c);
  padding: 32px;
  border-radius: 18px;
  max-width: 700px;
  margin: 40px auto;
  box-shadow: 0 20px 40px rgba(0,0,0,0.8);
  color: #e0e0e0;
  font-family: monospace;
}

.status-bar {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 20px;
}

.led {
  width: 16px;
  height: 16px;
  border-radius: 50%;
}
.led.off { background: #555; }
.led.green { background: #2ecc71; box-shadow: 0 0 10px #2ecc71; }
.led.red { background: #e74c3c; box-shadow: 0 0 10px #e74c3c; }
.led.yellow { background: #f1c40f; box-shadow: 0 0 10px #f1c40f; }

/* CONVEYOR */
.conveyor {
  position: relative;
  height: 140px;
  background: #111;
  border-radius: 12px;
  border: 2px solid #333;
  overflow: hidden;
}

.belt {
  position: absolute;
  bottom: 20px;
  height: 40px;
  width: 200%;
  background: repeating-linear-gradient(
    45deg,
    #333,
    #333 20px,
    #222 20px,
    #222 40px
  );
  animation: beltMove 1s linear infinite;
}

.box {
  position: absolute;
  bottom: 60px;
  width: 60px;
  height: 40px;
  background: linear-gradient(145deg, #c49b63, #8e6b3f);
  border: 2px solid #5a3e1b;
  border-radius: 6px;
  box-shadow: 0 6px 10px rgba(0,0,0,0.6);
}

/* CONTROLS */
.controls {
  display: flex;
  justify-content: center;
  gap: 24px;
  margin-top: 24px;
}

.ctrl-btn {
  padding: 12px 26px;
  border-radius: 10px;
  border: 2px solid #555;
  background: linear-gradient(145deg, #1e1e1e, #2a2a2a);
  color: #e0e0e0;
  cursor: pointer;
  font-weight: bold;
}

.start { border-color: #2ecc71; color: #2ecc71; }
.stop { border-color: #e74c3c; color: #e74c3c; }
.reverse { border-color: #f1c40f; color: #f1c40f; }

@keyframes beltMove {
  from { transform: translateX(0); }
  to { transform: translateX(-80px); }
}
</style>

<script>
let boxes = document.querySelectorAll(".box");
let led = document.getElementById("status-led");
let text = document.getElementById("status-text");
let belt = document.querySelector(".belt");

let direction = 1;
let running = false;

function setStatus(color, msg) {
  led.className = "led " + color;
  text.textContent = msg;
}

function moveBoxes() {
  if (!running) return;

  boxes.forEach(box => {
    let x = parseInt(box.style.left);
    x += direction * 2;

    if (x > 640) x = -60;
    if (x < -60) x = 640;

    box.style.left = x + "px";
  });

  requestAnimationFrame(moveBoxes);
}

function startBelt() {
  running = true;
  belt.style.animationPlayState = "running";
  setStatus("green", "CONVEYOR RUNNING");
  moveBoxes();
}

function stopBelt() {
  running = false;
  belt.style.animationPlayState = "paused";
  setStatus("red", "EMERGENCY STOP");
}

function reverseBelt() {
  running = true;
  direction *= -1;
  belt.style.animationDirection = direction === 1 ? "normal" : "reverse";
  setStatus("yellow", "REVERSE MODE");
  moveBoxes();
}
</script>
