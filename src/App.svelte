<link href="/fonts/abhaya_libre/AbhayaLibre-Regular.ttf" rel="stylesheet">

<script>
  import Scroller from "@sveltejs/svelte-scroller"
  import {onMount} from "svelte"
  import * as d3 from "d3"


  import Loremipsum from "./texto/Loremipsum.svelte"
  import FormulaDeslizable from "./components/FormulaDeslizable.svelte";
  import { fade } from 'svelte/transition';
 
  import Amplitud from "./texto/txtAmplitud.svelte"
  import TxtAmplitud from "./texto/txtAmplitud.svelte";
  import txtFrecuencia from "./texto/txtFrecuencia.svelte";
  import TxtFrecuencia from "./texto/txtFrecuencia.svelte";
  import TxtQueEsOnda from "./texto/txtQueEsOnda.svelte"
  import FourierDrawing from "./components/FourierDrawing.svelte"
  import txtSumaOndas from "./texto/txtSumaOndas.svelte"
    import TxtSumaOndas from "./texto/txtSumaOndas.svelte";
  import LitleDraw from "./components/LitleDraw.svelte";

 

  onMount(() => {
    // Establecer un timeout para iniciar el scroll constante después del delay
    timeout = setTimeout(() => {
      // Establecer un intervalo para ejecutar el scroll constantemente
      interval = setInterval(scrollToTop, scrollInterval);

      // Establecer otro timeout para detener el intervalo después del scrollDuration
      setTimeout(() => {
        clearInterval(interval);
      }, scrollDuration);
    }, delayBeforeStart);

    // Limpiar intervalos y timeouts si el componente se desmonta
    return () => {
      clearTimeout(timeout);
      clearInterval(interval);
    };
  });
  

  /* Variables para el scroller1 */
  let count
  let index
  let offset
  let progress
  let top = 0.1
  let threshold = 0.5
  let bottom = 0.9

  /*variables de los graficos*/
  let L = 1;
  let Frecuencia = 1;

</script>

<main >
  <div style="width:max-content; height:max-content; background-color:#f9f9f9;position:absolute"></div>
  <div class="header">
    <img src="/images/title.svg" width="800" alt="anillos" />
    <h1 class="headline" style="font-family: abhaya_libre;">
      Aprendé sobre series matemáticas
      
    </h1>
   
  </div>


  

  <div class="scroller-cont">
  <!-- Segundo scroller -->
  <Scroller
    top={top}
    threshold={threshold}
    bottom={bottom}
    bind:count={count}
    bind:index={index}
    bind:offset={offset}
    bind:progress={progress}
  >
    <div slot="background" class="image_container">
      
      <FormulaDeslizable {L}{Frecuencia} {index}/>


      <div style="width:-webkit-fill-available;display:flex;flex-direction:row; justify-content:space-evenly">

        {#if index > 0}
      <div style="display:flex;flex-direction:column; align-items:center; transition: margin 0.5s ease;">
      <p style="margin-bottom:4px" in:fade={{ delay: 0, duration: 150 }} out:fade={{ delay: 0, duration: 150 }}>Amplitud</p>
      <input type="range" min="0" max="2" step="0.001" style="" bind:value={L} />
      </div>
      {/if}
      
    {#if index == 2}
    <div style="display:flex;flex-direction:column; align-items:center">
      <p style="margin-bottom:4px" in:fade={{ delay: 0, duration: 150 }} out:fade={{ delay: 0, duration: 150 }}>Frecuencia</p>
      
      <input type="range" min="1" max="3" step="0.0001" bind:value={Frecuencia} in:fade={{ delay: 0, duration: 200 }} out:fade={{ delay: 0, duration: 150 }} />
       </div> 
      {/if}
  
      </div>
      </div>

    <div slot="foreground" class="foreground_container">
      <section class="step_foreground" style="height:850px">
        <TxtQueEsOnda/>
        </section>
      <section class="step_foreground" style="height:700px">
      <TxtAmplitud {L}/>
      </section>
      <section class="step_foreground" style="height:900px">
      <TxtFrecuencia {Frecuencia}/>
        
      </section>
    
    </div>
  </Scroller>
  </div>
 


  <div class= "scroller-cont">
      <div  class="image_container" style="margin-top: 0px;">
      <img  src="/images/gifSumaOnda.gif"  alt="miGif" style="border:1px solid black; width:400px; height: 400px">
      </div>
      <div  class="foreground_container">
        <div class="step_foreground" style=" margin-top: 90px"> 
       <TxtSumaOndas/>
        </div>
      </div>
    </div>
    <br>

    <!-- <div style="display: flex;flex-direction:row"> -->
      <!-- <div class="image-container" style="clip-path: inset(0 0 0 75px)"> -->
        <!-- <iframe title="ondaGeo" src="https://www.geogebra.org/calculator/wpgvpuns" width="400" height="400"  style="border: 1px solid #e4e4e4;border-radius: 4px;" frameborder="0"></iframe> -->
        <!-- <iframe title="ondaGeo" src="https://www.geogebra.org/calculator/wpgvpuns" width="400" height="400"  style="border: 1px solid #e4e4e4;border-radius: 4px;" frameborder="0"></iframe> -->
        <!-- <iframe title="ondaGeo" src="https://www.geogebra.org/calculator/wpgvpuns" width="400" height="400"  style="border: 1px solid #e4e4e4;border-radius: 4px;" frameborder="0"></iframe> -->
        <!-- <iframe title="onda"src="https://www.geogebra.org/calculator/wpgvpuns?embed" frameborder="0" width="475px" height="400px" draggable="false"></iframe> -->
      <!-- </div> -->
      <!-- <div class="foreground_container" style="position: absolute;"> -->
      <!-- <div class="step_foreground" style="font-size:larger;"> -->
      <!-- <p style="font-size: larger; text-align:justify">Como vimos posteriormente, las ondas al poder representarse tanto como ondas como circulos, podemos jugar con las amplitudes y frecuencias de estas para poder generar distintos movimientos especificos.</p> -->
      <!-- </div> -->
      <!-- </div> -->
    <!-- </div> -->
    

<div style="margin-left: 11%;margin-right:11%">


    <div style="font-size:larger; display:flex;flex-direction:column;text-align:center"><h2>Ondas vs. Circunferencias</h2><p style="font-size:larger; text-align:justify">Una vez descompuesta una onda en una suma de ondas, debemos entender cómo se traducen estas a circunferencias. La amplitud de la onda está directamente relacionada al radio de la circunferencia, mientras la frecuencia se relaciona con la velocidad a la que gira el punto verde.</p></div>

    <div  class="img-gif">
      <img src="/images/VidCW.gif" alt="CW"style="width:600px;">
    </div>
    <div class="littleDrawDescript">
      <h2 style="text-align: center;">¿Qué pasa si combinamos los 2 conceptos anteriores?</h2>
      <p style="text-align: justify;">
        La imagen que tenemos ante nosotros muestra la representación gráfica de la suma de tres ondas: Observamos cómo cada círculo, que representa a su respectiva onda, se encuentra centrado en la suma de las amplitudes de las ondas anteriores. Esto permite descomponer el dibujo en ondas y hacer su representación como una cadena de circunferencias. 
      </p>
  </div>

    <div class="littleDraw">
      <LitleDraw/>
    </div>
    
    <FourierDrawing/>
    
  </div>

    <hr>
    <p style="text-align: center;">Caracoix Felipe; Castore Juan Ignacio; Salem Guido.</p>
</main>


<style>

   
  .img-gif{
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    
  }
  .header {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    margin-top: 50px;
    margin-bottom: 80px;
  }
  .headline {
    font-size: 30px;
    line-height: 1.2;
    font-weight: normal;
    text-align: center;
    margin: 20px;
  }
  .bajada {
    font-size: 18px;
    font-weight: normal;
    text-align: center;
    margin: 10px;
  }
  .headline b {
    display: block;
  }

  /* Estilos para el scroller */
  .foreground_container {
    padding-left: 50%;
    scroll-snap-type: y mandatory;
    width:500px
  }

  .step_foreground {
    display: flex;
    justify-content: start;
    height:auto;
    scroll-snap-align: start;
    color: black;
  
    
  }
  input[type="range"] {
  accent-color: #338B31;}

  .epi_foreground {
    padding: 20px;
    max-width: 150px;
    background-color: rgba(0, 0, 0, 0.5);
  }
  
  .image_container {
    display: flex;
    position: absolute;
    flex-direction: column;
    justify-content: start;
    margin-right: 500px;
    height: 100vh;
    padding-left: 10%;
    padding-top: 2%;
    
  }

  .littleDraw{
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    margin-bottom: 100px;
  }
  .littleDrawDescript{
    
    font-size: 20px;
    font-weight: normal;
    justify-content: center;
    align-items: center;
    text-align: start;
    margin-top: 120px;
  }

  .onda {
      position: relative;
      width: 400px;  /* Ajusta según tus necesidades */
      height: 400px; /* Ajusta según tus necesidades */
      overflow: hidden;
      border: 1px solid black;  
  }

  .onda iframe {
      position: absolute;
      top: 0;
      left: 0;
      width: 800px; /* Ajusta según tus necesidades */
      height: 100%;
      border: none;
      clip-path: inset(0 0 0 75px); /* Ajusta los valores para recortar el lado izquierdo */
      /* desabilitar poder hacer zoom */

      
  }

  #gifSuma{
    border-radius: solid 10px black;
    border: black;
    margin-left: 50px;
    width:450px;
    height: 450px;
  }

  .sumaOndaContainer p {
    width: 500px;

    padding-left: 50px;
    font-size: 1.5rem;
    line-height: 1.5;
    font-weight: normal;
    text-align: left;
  }

  .sumaOndaLineal {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    margin-right: 70px;
  }

  .sumaOndaCircular{
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    width: 100%;
  }
  .sumaOndaCircular iframe{
    /* move left */
    transform: translateX(-50px);
  }
 
  h1{
    font-family: abhaya_libre;
  }
  h2{
    font-family: abhaya_libre;
  }
  h3{
    font-family: abhaya_libre;
  }
  p{
    font-size: larger;
  }




  
</style>
