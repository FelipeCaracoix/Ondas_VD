<script lang="ts">
    import * as d3 from 'd3';

    import { onMount } from 'svelte';
    

    function fourierSeries(x, L) { // L = longitud del intervalo donde se repite la funcion (periodo)
 return L * Math.sin(x)
}


  // Lógica de D3.js aquí
  let svg;

  onMount(() => {
  
    const escalaX = d3.scaleLinear()
      .domain([0, 10])
      .range([0, 500]);
  
    const escalaY = d3.scaleLinear()
      .domain([-10, 10])
      .range([300, 0]);
  
    const datos = [];
  
    for (let x = 0; x <= 10; x += 0.1) {
      const y = fourierSeries(x,2);
      datos.push([ x, y ]);
    }
  
    const svg = d3.select("#grafico")
      .append("svg")
      .attr("width", 500)
      .attr("height", 300);
  
    const linea = d3.line() // Corrección aquí: especificamos el tipo de datos
      .x(d => escalaX(d[0]))
      .y(d => escalaY(d[1]));
  
      svg.append("path")
  .datum(datos)
  .attr("d", linea)
  .style("stroke", "blue")
  .style("stroke-width", 2)
  .attr("filter", "none")
  .attr("fill", "none"); 

  
    // Agregamos el SVG al DOM
    document.getElementById("grafico").appendChild(svg.node());
});
  </script>
  <div id="grafico" />


  