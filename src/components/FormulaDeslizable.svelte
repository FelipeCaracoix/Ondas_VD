<script lang="ts">
    import * as d3 from 'd3';
    import { onMount } from 'svelte';
    import App from '../App.svelte';
    let datos = []; // Almacena los datos del gráfico
    let svg; // Referencia al SVG
    let an = 0;
    let bn = 1;
    let sliderValue = 1;
    let i =0;


    export let L =1;
    export let Frecuencia =1;
    export let index = 0;

    $:  if (L ||Frecuencia){
    actualizarGrafico();
    }
    $: if ( index === 0){
      animar();
    }


    function animar(){
  
  setTimeout(() => { // Utiliza setTimeout para controlar el intervalo de tiempo entre cada incremento
    i -= 0.01; // Aumenta i en el valor de step
    actualizarGrafico();
    
    // Aquí puedes llamar a cualquier función que necesite el valor actualizado de i
    // Por ejemplo: actualizarSVG(i);

    if(index == 0){requestAnimationFrame(animar); }// Llama a animar() en el próximo frame para continuar la animación
  }, 0.2);
}
    






    // Función para calcular la serie de Fourier
    function fourierSeries(x, L, an, bn) {
      return 4 * L * Math.sin((x-i) * Frecuencia )
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
        .style("stroke-width", 4)
        .attr("filter", "none")
        .attr("fill", "none");

        svg.append("line")
    .attr("x1", escalaX(0))
    .attr("y1", escalaY(0))
    .attr("x2", escalaX(10))
    .attr("y2", escalaY(0))
    .style("stroke", "black")
    .style("stroke-width", 3);

    }
  
    // Llama a la función cuando cambie el valor de L
    $: actualizarGrafico();
    onMount(() => {
      actualizarGrafico();
    });
  </script>
  
  <div id="grafico"  style= "width: 400px; height: 400px; display:flex;flex-direction:row"/>

 
  
  <style>
    #grafico {
      width: 100%;
      height: 400px;
      border: 1px solid black;
      align-items: center;
      display: flex;
      justify-content: center;
    }
    
    
    svg {
      display:block;
    }
    
  </style>