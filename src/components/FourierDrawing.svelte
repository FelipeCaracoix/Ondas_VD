<link href="/fonts/abhaya_libre/AbhayaLibre-Regular.ttf" rel="stylesheet">

<script>
    import { onMount } from "svelte";
    import { writable, get } from "svelte/store";

    let drawingCanvas;
    let fourierCanvas;
    let drawing = false;
    let interval;

    let points = writable([]);
    let fourierData = writable([]);

    function handleMouseDown(event) {
      clearCanvas();
      const ctx = drawingCanvas.getContext("2d");
      ctx.beginPath();
      ctx.moveTo(event.offsetX, event.offsetY);
      points.set([{ x: event.offsetX, y: event.offsetY }]);
      drawing = true;
      drawingCanvas.addEventListener("mousemove", handleMouseMove);
    }

    function handleMouseMove(event) {
      if (!drawing) return;
      const ctx = drawingCanvas.getContext("2d");
      ctx.lineTo(event.offsetX, event.offsetY);

      //ctx.imageSmoothingQuality = "high";
      ctx.stroke();
      points.update(pts => [...pts, { x: event.offsetX, y: event.offsetY }]);
    }

    function handleMouseUp() {
      drawing = false;
      drawingCanvas.removeEventListener("mousemove", handleMouseMove);
      points.update(pts => {
        if (pts.length > 0) {
          const firstPoint = pts[0];
          const ctx = drawingCanvas.getContext("2d");
          ctx.lineTo(firstPoint.x, firstPoint.y);
          ctx.stroke();
          pts.push(firstPoint);
        }
        return pts;
      });
      calculateFourierSeries();
    }

    function calculateFourierSeries() {
      const pts = get(points);
      const fourier = dft(pts);
      fourierData.set(fourier);
      drawFourier();
    }

    function dft(points) {
      const N = points.length;
      const fourier = [];
      for (let k = 0; k < N; k++) {
        let re = 0;
        let im = 0;
        for (let n = 0; n < N; n++) {
          const phi = (2 * Math.PI * k * n) / N;
          re += points[n].x * Math.cos(phi) + points[n].y * Math.sin(phi);
          im -= points[n].x * Math.sin(phi) - points[n].y * Math.cos(phi);
        }
        re = re / N;
        im = im / N;
        let freq = k;
        let amp = Math.sqrt(re * re + im * im);
        let phase = Math.atan2(im, re);
        fourier[k] = { re, im, freq, amp, phase };
      }
      fourier.sort((a, b) => b.amp - a.amp);
      return fourier.slice(0, 100000);
    }

    function drawFourier() {
      clearInterval(interval);
      const ctx = fourierCanvas.getContext("2d");
      const W = fourierCanvas.width;
      const H = fourierCanvas.height;
      const data = get(fourierData);

      let time = 0;
      let path = [];
      interval = setInterval(() => {
        ctx.clearRect(0, 0, W, H);
        ctx.beginPath();
        let x = W / 2;
        let y = H / 2;
        ctx.moveTo(x, y);

        for (let i = 1; i < data.length; i++) {
          let prevx = x;
          let prevy = y;
          let freq = data[i].freq;
          let radius = data[i].amp;
          let phase = data[i].phase;
          x += radius * Math.cos((freq * 2 * Math.PI * time) / data.length + phase);
          y += radius * Math.sin((freq * 2 * Math.PI * time) / data.length + phase);
          ctx.lineTo(x, y);
          ctx.moveTo(prevx, prevy);
          ctx.arc(prevx, prevy, radius, 0, 2 * Math.PI, false);
          ctx.moveTo(x, y);
        }
        ctx.stroke();

        path.push({ x, y });
        if (path.length > data.length) {
          path.shift();
        }

        ctx.beginPath();
        // Aca le metemos un show al dibujito
        // mas info en console.log(ctx)

        //ctx.imageSmoothingQuality = "high";
        //ctx.shadowColor = "#338B31";  // Green shadow
        //ctx.shadowBlur = 10;
        //ctx.shadowOffsetX = 5;
        //ctx.shadowOffsetY = 5;
        for (let i = 0; i < path.length; i++) {
          if (i === 0) {
            ctx.moveTo(path[i].x, path[i].y);
          } else {
            ctx.lineTo(path[i].x, path[i].y);
          }
        }
        ctx.stroke();

        time += 1;
        if (time >= data.length) {
          time = 0;
        }
      }, 1000 / 60);
    }

    function clearCanvas() {
      const ctx = drawingCanvas.getContext("2d");
      ctx.clearRect(0, 0, drawingCanvas.width, drawingCanvas.height);
      const ctx2 = fourierCanvas.getContext("2d");
      ctx2.clearRect(0, 0, fourierCanvas.width, fourierCanvas.height);
      points.set([]);
      fourierData.set([]);
      clearInterval(interval);
      setupDrawingCanvas();
      setupFourierCanvas();
      console.log("Canvas cleared");
    }

    function setupDrawingCanvas() {
      const ctx = drawingCanvas.getContext("2d");
      ctx.lineWidth = 2;
      ctx.strokeStyle = "#000";
    }

    function setupFourierCanvas() {
      const ctx = fourierCanvas.getContext("2d");
      ctx.lineWidth = 2;
      ctx.strokeStyle = "#000";
    }

    onMount(() => {
      setupDrawingCanvas();
      setupFourierCanvas();
    });
</script>

<style>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .canvas-container {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 10px;
  }
  canvas {
    border: 1px solid black;
    margin: 10px;
  }
  button {
    margin: 10px;
    padding: 10px 20px;
    background-color: white;
    border: 2px solid black;
    transition: background-color 0.3s, transform 0.3s;
  }
  button:hover {
    background-color: #338B31;
    transform: scale(1.1);
    color: white;
  }
  .explanation-container {
    display: flex;
    justify-content: center;
    width: 100%;
  }
  .explanation {
    flex: 1;
    margin: 10px;
  }
  .aclaracion {
    width: 900px;
  }
  h2 {
    text-align: center;
    font-family: abhaya_libre;
  }
  p {
    font-size: larger;
  }
  .strong {
    font-family: abhaya_libre;
    font-size: larger;
  }
</style>

<div class="container" style="font-size:larger;">
  <h2>Visualización de Series de Fourier</h2>
  <div style="text-align:justify">
    <p style="font-size: larger;">
      Las series de Fourier son una herramienta matemática que nos permite descomponer una función periódica en una suma infinita de funciones periódicas más simples. Estas funciones están formadas por combinaciones de senos y cosenos con frecuencias enteras. Imagina que tienes una función que se repite a intervalos regulares, y deseas expresarla como la suma de ondas más simples. Esto es lo que permite las series de Fourier.
    </p>
    <p style="font-size: larger;">Dibuja en el cuadrado izquierdo y observa cómo se implementan las series de Fourier en tiempo real:</p>
  </div>
  <div class="canvas-container">
    <canvas
      bind:this={drawingCanvas}
      width="400"
      height="400"
      on:mousedown={handleMouseDown}
      on:mouseup={handleMouseUp}
    ></canvas>
    <canvas bind:this={fourierCanvas} width="400" height="400"></canvas>
  </div>
  <button on:click={clearCanvas}>Limpiar</button>
</div>

<div class="explanation-container" style="font-size:larger;justify-content: center;display:flex;flex-direction:column">
  <h2>¿Cómo se logra este efecto?</h2>


  <div style="font-size: larger; text-align:justify">
    <ul>
      <li><strong class="strong">Identificación de puntos clave:</strong>
        <ul>
          <li>Tomamos un dibujo y localizamos los puntos clave en la curva. Estos puntos representan ubicaciones importantes a lo largo del dibujo.</li>
        </ul><br>
      </li>
      <li><strong class="strong">Cálculo de coordenadas:</strong>
        <ul>
          <li>Para cada punto identificado, calculamos sus coordenadas (x, y). Estas coordenadas nos indican dónde se encuentra cada punto en el plano.</li>
        </ul><br>
      </li>
      <li><strong class="strong">Creación de circunferencias:</strong>
        <ul>
          <li>Creamos una circunferencia centrada en cada uno de los puntos clave.</li>
          <li>El radio de cada circunferencia se determina por la distancia al siguiente punto. Cuanto más lejos esté el siguiente punto, mayor será el radio de la circunferencia.</li>
        </ul><br>
      </li>
      <li><strong class="strong">Velocidad angular y frecuencia:</strong>
        <ul>
          <li>Asociamos una velocidad angular a cada circunferencia. La velocidad angular está relacionada con la frecuencia de la función que representa la circunferencia. Cuanto más rápido gire la circunferencia, mayor será la frecuencia de la onda correspondiente.</li>
        </ul><br>
      </li>
      <li><strong class="strong">Suma de circunferencias:</strong>
        <ul>
          <li>Sumamos todas las circunferencias creadas. Cada circunferencia contribuye con su función correspondiente.</li>
          <li>La suma de todas estas funciones nos da la representación en series de Fourier del dibujo original.</li>
        </ul><br>
      </li>
      <li><strong class="strong">Visualización y precisión:</strong>
        <ul>
          <li>Cuantas más circunferencias agreguemos, más precisa será la aproximación al dibujo original.</li>
          <li>La representación final se acercará cada vez más al dibujo a medida que aumentemos el número de circunferencias.</li>
        </ul>
      </li>
    </ul><br>
  </div>
</div>
