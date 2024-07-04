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
      background-color: white;
      padding: 10px;
    }
    canvas {
      border: 1px solid black;
      margin: 10px;
    }
    button {
      margin: 10px;
    }
    .explanation-container {
      display: flex;
      justify-content: space-around;
      width: 100%;
      max-width: 900px;
    }
    .explanation {
      flex: 1;
      margin: 10px;
    }

    .aclaracion{
      width: 900px;
    }
    h2 {
      text-align: center;
    }
  </style>
  
  <div class="container">
    <h2>Visualización de Series de Fourier</h2>
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
    <div class="explanation-container">
      <div class="explanation">
        <h2>Cómo Funciona Esto</h2>
        <p>
          Esta demostración visualiza cómo un dibujo se puede transformar en una serie de componentes de Fourier.
          El proceso comienza cuando dibujas una forma en el lienzo de la izquierda. La forma que dibujas es
          esencialmente una colección de puntos que definen el contorno de tu dibujo.
        </p>
        <p>
          Esta demostración visualiza cómo un dibujo se puede transformar en una serie de componentes de Fourier. 
          El proceso comienza cuando dibujas una forma en el lienzo de la izquierda. La forma que dibujas es esencialmente una colección de puntos que definen el contorno de tu dibujo.
          Una vez que completas tu dibujo y sueltas el botón del ratón, el programa captura la serie de puntos y aplica la Transformada Discreta de Fourier (DFT) a estos puntos. 
          La DFT convierte la información espacial (tu dibujo) en información de frecuencia, descomponiéndolo en una serie de componentes sinusoidales (ondas). 
        </p>
      </div>
      <div class="explanation">
        <h2>¿Por Qué Funciona?</h2>
        <p>
          Cada uno de estos componentes sinusoidales tiene una frecuencia, amplitud y fase específicas. Sumando estos componentes,
          podemos reconstruir el dibujo original como una combinación de estas formas de onda simples. Esto es lo que se visualiza
          en el lienzo de la derecha. Los círculos representan las amplitudes de estos componentes sinusoidales, y su movimiento
          traza la forma de tu dibujo original.
        </p>
        <p>
          La Transformada de Fourier es una herramienta poderosa en el procesamiento de señales y ayuda a analizar el contenido
          de frecuencia de las señales. Aquí, nos permite ver cómo las formas complejas se pueden representar como sumas de
          movimientos circulares más simples. Este concepto es fundamental en muchos campos, incluyendo el procesamiento de audio,
          la compresión de imágenes e incluso la física cuántica.
        </p>
       
      </div>
    </div>
    <div class="aclaracion">
      <p>
        "En este ejemplo, usamos hasta 100.000 componentes (círculos) para representar tu dibujo. Aunque pueda parecer demasiado, esta cantidad es crucial para lograr una similitud notable con la imagen original. La razón por la cual tantos círculos no afectan significativamente el rendimiento del programa radica en la simplicidad matemática subyacente. Cada círculo se puede describir mediante ecuaciones simples y operaciones geométricas, lo cual es computacionalmente eficiente. Esto significa que, aunque estamos sumando un gran número de componentes, cada uno de ellos requiere un cálculo sencillo, permitiendo que el programa funcione sin problemas y sin consumir excesivos recursos de la computadora."
    </p>
    </div>
  </div>
