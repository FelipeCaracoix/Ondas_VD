<script>
  import Scroller from "@sveltejs/svelte-scroller"
  import {onMount} from "svelte"
  import * as d3 from "d3"

  import DebugScroller from "./components/DebugScroller.svelte"
  import Loremipsum from "./texto/Loremipsum.svelte"
  import Formula from  "./components/Formula.svelte"
  import Sinx from  "./components/sinx.svelte"
  import FormulaDeslizable from "./components/FormulaDeslizable.svelte";
  import FormulaDeslizable1 from "./components/FormulaDeslizable1.svelte";

  import Amplitud from "./texto/txtAmplitud.svelte"
  import TxtAmplitud from "./texto/txtAmplitud.svelte";
  import txtFrecuencia from "./texto/txtFrecuencia.svelte";
  import TxtFrecuencia from "./texto/txtFrecuencia.svelte";
  import FourierDrawing from "./components/FourierDrawing.svelte"
  

  /* Variables para el scroller1 */
  let count
  let index
  let offset
  let progress
  let top = 0.1
  let threshold = 0.5
  let bottom = 0.9

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
      {#if index == 0}  
      <FormulaDeslizable />
      {:else}
      <FormulaDeslizable1/>
      {/if}

    </div>
    <div slot="foreground" class="foreground_container">
      <section class="step_foreground" style="height:700px">
      <TxtAmplitud/>
      </section>
      <section class="step_foreground" style="height:700px">
      <TxtFrecuencia/>
        
      </section>
      <section class="step_foreground" style="height:700px">
        <Loremipsum/> 
      </section>
    </div>
  </Scroller>
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
  }

  .step_foreground {
    display: flex;
    justify-content: start;
    height:auto;
    
    color: black;
  
    
  }
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
    align-items: center;
    height: 100vh;
  }
  
</style>
