<script>
    import { onMount } from 'svelte';

    let canvas1, canvas2;
    let ctx1, ctx2;

    // Parámetros para la serie de Fourier
    const N = 3; // Número de círculos
    let time = 0;

    function drawCircle(ctx, x, y, radius, phase, circlenumber) {
        ctx.beginPath();
        ctx.arc(x, y, radius, 0, 2 * Math.PI);
        ctx.stroke();
        
        // Dibujar la línea hasta el punto de la circunferencia
        ctx.moveTo(x, y);
        const endX = x + radius * Math.cos(phase);
        const endY = y + (radius * Math.sin(phase));
        ctx.lineTo(endX, endY);
        if (circlenumber == 1) {
            ctx.strokeStyle = '#338B31';
        }
        else{
            ctx.strokeStyle = 'black';
        }
        ctx.stroke();
        
        return { x: endX, y: endY };
    }

    function drawWave(ctx, startX, startY, radius, phase, flag = false) {
        ctx.strokeStyle = 'black';
        ctx.beginPath();
        ctx.moveTo(startX, startY);
        for (let t = 0; t < 400; t++) { // Aumentar el rango t para ocupar más espacio horizontal
            const posX = startX + t;
            const posY = startY + radius * Math.sin(phase + (t * 2 * Math.PI) / 400); // Ajustar para el nuevo rango t
            ctx.lineTo(posX, posY);
        }
        if (flag) {
            ctx.strokeStyle = '#338B31';
        }
        ctx.stroke();
    }

    function animate() {
        ctx1.clearRect(0, 0, canvas1.width, canvas1.height);
        ctx2.clearRect(0, 0, canvas2.width, canvas2.height);
        
        // Coordenadas iniciales en el centro del canvas
        let x2 = canvas2.width / 4;
        let y2 = canvas2.height / 2;

        // Guardar el punto inicial para las ondas
        let waveStartX = canvas1.width /10000;
        let waveStartY = y2;

        let previousPoint = { x: x2, y: y2 };

        let previousCircle = { x: x2, y: y2 };

        for (let i = 0; i < N; i++) {
            const radius = 60 * (4 / ((2 * i + 1) * Math.PI)); // Radios de los círculos
            const phase = (2 * Math.PI * (2 * i + 1) * time) / 9000; // Fase de la onda
            if (i == 1){
                // pinto solo una onda
                drawWave(ctx1, waveStartX, waveStartY, radius, phase, true);
            }
            else{
                drawWave(ctx1, waveStartX, waveStartY, radius, phase);
            }
            x2 = i+100;
            if (i == 1){
                x2 = i+220;
            }
            if (i == 2){
                x2 = i+300;
            }
            
            const point = drawCircle(ctx2, x2, y2, radius, phase, i);
            waveStartY = y2 = point.y; // Ajustar la posición vertical para la onda y el siguiente círculo


            previousPoint = point;
        }

        // Desplazar horizontalmente
        x2 += 2;

        // Mantener el dibujo dentro del canvas
        if (x2 > canvas2.width) {
            x2 = canvas2.width / 4;
            previousPoint = { x: x2, y: y2 };
        }

        time += 16; // Incrementar el tiempo para la animación
        requestAnimationFrame(animate);
    }

    onMount(() => {
        canvas1 = document.getElementById('waveCanvas');
        ctx1 = canvas1.getContext('2d');
        canvas1.width = 400;
        canvas1.height = 300;
        ctx1.lineWidth = 2;

        canvas2 = document.getElementById('fourierCanvas');
        ctx2 = canvas2.getContext('2d');
        canvas2.width = 400;
        canvas2.height = 300;
        ctx2.lineWidth = 2;

        animate();
    });
</script>

<style>
    .container {
        margin-top: 20px;
        display: flex;
        justify-content: center;
        gap: 20px; /* Ajustar para reducir el espacio entre los lienzos */
        align-items: center;
    }
    canvas {
        background-color: #f9f9f9;
        border: 1px solid black;
    }
</style>

<div class="container">
    <canvas id="waveCanvas"></canvas>
    <canvas id="fourierCanvas"></canvas>
</div>
