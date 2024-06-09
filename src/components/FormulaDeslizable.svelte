<script lang="ts">
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
  
    let L = 0;
    let datos = []; // Almacena los datos del gráfico
    let svg; // Referencia al SVG
    let an = 0;
    let bn = 1;

    let sliderValue = 1;

  
    // Función para calcular la serie de Fourier
    function fourierSeries(x, L, an, bn) {
      return 4 * L * Math.sin(x * 1 * Math.PI)
    }
  
    // Función para actualizar el gráfico cuando cambie L
    $: actualizarGrafico = () => {
      datos = [];
      for (let x = 0; x <= 10; x += 0.1) {
        const y = fourierSeries(x, L,an , bn);
        datos.push([x, y]);
      }
      actualizarSVG();
    };
  
    function actualizarSVG() {
      if (svg) {
        // Si el SVG ya existe, elimínalo
        svg.remove();
      }
  
      // Crea un nuevo SVG
      svg = d3.select("#grafico")
        .append("svg")
        .attr("width", 500)
        .attr("height", 300);
  
      const escalaX = d3.scaleLinear()
        .domain([0, 10])
        .range([0, 500]);
  
      const escalaY = d3.scaleLinear()
        .domain([-10, 10])
        .range([300, 0]);
  
      const linea = d3.line()
        .x(d => escalaX(d[0]))
        .y(d => escalaY(d[1]));
  
      svg.append("path")
        .datum(datos)
        .attr("d", linea)
        .style("stroke", "#338B31")
        .style("stroke-width", 2)
        .attr("filter", "none")
        .attr("fill", "none");
    }
  
    // Llama a la función cuando cambie el valor de L
    $: actualizarGrafico();
    onMount(() => {
      actualizarGrafico();
    });
  </script>
  
  <div id="grafico"  style= "width: 300px; height: 300px; margin-bottom:25px;"/>

  <input type="range" min="0" max="2" step="0.001" style=""bind:value={L} on:input={actualizarGrafico} />
  <p>{L}</p>
 
 
  
  <style>
    #grafico {
      width: 100%;
      height: 400px;
      border: 1px solid black;
      align-items: center;
      display: flex;
      justify-content: center;
    }
    
    input[type="range"] {
  accent-color: #338B31;}

    svg {
      display:block;
    }
    
  </style>