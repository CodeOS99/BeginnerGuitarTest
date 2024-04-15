<script>
// @ts-nocheck

  import TestComponent from "./TestComponent.svelte";
  import { pass } from "./storage";
  import { Confetti } from "svelte-confetti";

  let currentState = 'mainMenu';
  let showConfettiInHTML = false;

  const startTest = () => {
    currentState = "testOngoing"
  }

  const resetState = () => {
    currentState = 'mainMenu'
  }

  const showConfetti = () => {
    showConfettiInHTML = !showConfettiInHTML;
  }
</script>

<style>
  *{
    font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
  }

  #mainHeadingCont{
    margin: 0, auto;
      display: flex;
    justify-content: center;
  }

  #mainHeading{
    font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  }

  #introPara{
    font-size: medium;
  }

  #startTestBtn{
    font-size: large;
    border-radius: 10px;
    background-color: rgb(0, 255, 170);
  }

  #startTestBtn:hover{
    opacity: 0.7;
  }
  
  #startTestBtn:active{
    background-color: rgb(0, 126, 84);
  }

  #passSpan:hover{
    cursor: pointer;
    opacity: 0.7;
  }
</style>

<main>
  {#if currentState === 'mainMenu'}
      <div id='mainHeadingCont'>
        <h1 id='mainHeading'>Guitar Mock Test</h1>
      </div>
      <p id='introPara'>
        This was created by <a href='https://github.com/CodeOS99'>me</a> for my beginner guitar test. I didn't use it much, but it was a fun small project.
        This was originally written in Python, then I rewrote it in HTML, CSS, JS.
      </p>
      <h3>Click this button to start the test!</h3>
      <button id='startTestBtn' on:click={startTest}>Start test!</button>
      <p>
        {#if $pass}
          You have <span id='passSpan' style="color:green" on:click={showConfetti}>PASSED</span> the test.
        {:else}
          You have <span style="color:red">NOT PASSED</span> the test.
        {/if}
      </p>
      <p>© Me, All right reserved, I don't actually know how to do this but it feels professional to do this</p>
  {:else if currentState === 'testOngoing'}
      <TestComponent on:failed={resetState}></TestComponent>
  {/if}

  {#if showConfettiInHTML}
      <!--I just copied this from the svelte confetti site, I know it shows confetti all over the screen, and have no idea how it does that-->
      <div style="
      position: fixed;
      top: -50px;
      left: 0;
      height: 100vh;
      width: 100vw;
      display: flex;
      justify-content: center;
      overflow: hidden;
      pointer-events: none;">
      <Confetti x={[-5, 5]} y={[0, 0.1]} delay={[500, 2000]} infinite duration=5000 amount=200 fallDistance="100vh" />
      </div>      
  {/if}
</main>
