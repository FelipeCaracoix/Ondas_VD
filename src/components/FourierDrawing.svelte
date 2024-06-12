<script>
  import { color } from "d3";
    import { onMount } from "svelte";
    import { writable } from "svelte/store";

    let drawingCanvas;
    let fourierCanvas;
    let drawing = false;
    let interval;

    let points = writable([]);
    let fourierData = writable([]);

    function handleMouseDown(event) {
        // Clear previous drawing
        clearCanvas();

        const ctx = drawingCanvas.getContext("2d");
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
            // Make the drawing continuous
            if (pts.length > 0) {
                const firstPoint = pts[0];
                const ctx = drawingCanvas.getContext("2d");
                ctx.lineTo(firstPoint.x, firstPoint.y);
                ctx.stroke();
                pts.push(firstPoint); // Close the loop
            }
            return pts;
        });
        calculateFourierSeries();
    }

    function calculateFourierSeries() {
        points.subscribe(pts => {
            const fourier = dft(pts);
            fourierData.set(fourier);
            drawFourier();
        });
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
        // Sort by amplitude in descending order
        fourier.sort((a, b) => b.amp - a.amp);
        // Limit to 20 circles
        return fourier.slice(0, 100000);
    }

    function drawFourier() {
        clearInterval(interval);
        const ctx = fourierCanvas.getContext("2d");
        const W = fourierCanvas.width;
        const H = fourierCanvas.height;
        fourierData.subscribe(data => {
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
                    x += radius * Math.cos(freq * 2 * Math.PI * time / data.length + phase);
                    y += radius * Math.sin(freq * 2 * Math.PI * time / data.length + phase);
                    ctx.lineTo(x, y);
                    ctx.moveTo(prevx, prevy);
                    ctx.arc(prevx, prevy, radius, 0, 2 * Math.PI,false );
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
        });
    }

    function clearCanvas() {
        const ctx = drawingCanvas.getContext("2d");
        ctx.clearRect(0, 0, drawingCanvas.width, drawingCanvas.height);
        const ctx2 = fourierCanvas.getContext("2d");
        ctx2.clearRect(0, 0, fourierCanvas.width, fourierCanvas.height);
        points.set([]);
        fourierData.set([]);
        clearInterval(interval);
        console.log("Canvas cleared");
        
    }

    onMount(() => {
        const ctx = drawingCanvas.getContext("2d");
        ctx.lineWidth = 2;
        ctx.strokeStyle = "#000";
    });
</script>

<style>
    canvas {
        border: 1px solid black;
        margin: 10px;
    }
    button {
        margin: 10px;
    }
</style>

<canvas
    bind:this={drawingCanvas}
    width="400"
    height="400"
    on:mousedown={handleMouseDown}
    on:mouseup={handleMouseUp}
></canvas>
<canvas bind:this={fourierCanvas} width="400" height="400"></canvas>
<button on:click={clearCanvas}>Clear</button>
