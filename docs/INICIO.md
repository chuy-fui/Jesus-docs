# INICIO
# Bienvenido a mi página de proyectos de Mecatrónica


<div class="gears" style="display:flex; justify-content:center; align-items:center; margin:40px 0;">
  <div class="gear" style="--i:1;"></div>
  <div class="gear" style="--i:-1; margin-left:-20px;"></div>
  <div class="gear" style="--i:1; margin-left:-20px;"></div>
</div>

<style>
.gear {
  width: 80px;
  height: 80px;
  border: 10px solid #555;
  border-radius: 50%;
  position: relative;
  animation: girar 6s linear infinite;
  animation-direction: normal;
  background: radial-gradient(circle, #888 30%, #555 100%);
  box-shadow: inset 0 0 10px #000, 0 0 10px rgba(0,0,0,0.5);
  transform-origin: center;
}
.gear::before {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  width: 20px;
  height: 20px;
  background: #222;
  border-radius: 50%;
  transform: translate(-50%, -50%);
}
.gear::after {
  content: "";
  position: absolute;
  top: -14px;
  left: 50%;
  width: 10px;
  height: 10px;
  background: #444;
  border-radius: 2px;
  box-shadow: 0 0 3px #000;
  transform: translateX(-50%);
}
@keyframes girar {
  from { transform: rotate(0deg); }
  to { transform: rotate(calc(360deg * var(--i))); }
}
</style>
