<script>
  import Scroller from "@sveltejs/svelte-scroller"
  import {onMount} from "svelte"
  import * as d3 from "d3"

  import DebugScroller from "./components/DebugScroller.svelte"
  import Loremipsum from "./texto/Loremipsum.svelte"
  import FormulaDeslizable from "./components/FormulaDeslizable.svelte";
  import { fade } from 'svelte/transition';

  import Amplitud from "./texto/txtAmplitud.svelte"
  import TxtAmplitud from "./texto/txtAmplitud.svelte";
  import txtFrecuencia from "./texto/txtFrecuencia.svelte";
  import TxtFrecuencia from "./texto/txtFrecuencia.svelte";
  import TxtQueEsOnda from "./texto/txtQueEsOnda.svelte"
  import FourierDrawing from "./components/FourierDrawing.svelte"
  

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
    <img src="/images/fourier.png" width="100" alt="anillos" />
    <h3 class="headline">
      <b>Titulo</b>
      Ondas, Fourier y otras cosas
    </h3>
    <p class="bajada">Entendiendo como funcionan las ondas</p>
    <div class="lorem_ipsum">
      <p>text</p>
    </div>
  </div>

  {#if progress < 1}
  <DebugScroller
    index={index}
    count={count}
    offset={offset}
    progress={progress}
  />
  {/if}
  
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
      <section class="step_foreground" style="height:700px">
        <TxtQueEsOnda/>
        </section>
      <section class="step_foreground" style="height:700px">
      <TxtAmplitud/>
      </section>
      <section class="step_foreground" style="height:600px">
      <TxtFrecuencia/>
        
      </section>
    
    </div>
  </Scroller>

<hr style="padding-top:100px">


  <div  class="img-gif">
    <img src="images\VidCW.gif" alt="CW"style="width:600px;">
    </div>
    <div class="lorem_ipsum">
      <Loremipsum />
    </div>
    <FourierDrawing/>
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
  .lorem_ipsum {
    color: black;
    margin: 100px auto;
    max-width:500px;
    height: auto;
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
  
</style>
