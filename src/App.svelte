<script lang="ts">
  import viteLogo from '/revolve.svg'

  let spinFrequency = 30;
  let flashFrequency = 30;

  $: spinFrequencySeconds = (spinFrequency / 60).toFixed(2);
  $: flashFrequencySeconds = (flashFrequency / 60).toFixed(2);

  let nextFlash;
  let rotation;
  let lastFrame;

  function calculateRotationAt(elapsed: number) {
    return (rotation + elapsed / (60_000 / spinFrequency) * 360) % 360
  }

  function flash() {
    const elapsed = nextFlash - lastFrame;
    nextFlash += 60_000 / flashFrequency;

    const rot = calculateRotationAt(elapsed);
    const elem = document.getElementById("flash");
    elem.style.transform = `rotate(${rot}deg)`;
  }

  function frame(time: number) {
    if (lastFrame === undefined)
      lastFrame = time;
    if (rotation === undefined)
      rotation = 0;
    if (nextFlash === undefined) {
      setTimeout(() => {
        nextFlash = null;
      }, 2000);
      nextFlash = null;
    }

    if (nextFlash === null)
      nextFlash = time;
    const elapsed = time - lastFrame;
    if (time >= nextFlash)
      flash();
  
    lastFrame = time;

    rotation = calculateRotationAt(elapsed);
    const elem = document.getElementById("spin");
    elem.style.transform = `rotate(${rotation}deg)`;

    requestAnimationFrame(frame);
  }
  requestAnimationFrame(frame);
</script>

<main>
  <div>
    <img
      src={viteLogo}
      id="flash"
      class="spinner"
      alt="Spinner Estroboscopio" />
    <img
      src={viteLogo}
      id="spin"
      class="spinner"
      alt="Spinner Normal" />
  </div>
  <h1>Estroboscopio</h1>

  <div class="card">
    <label>
      <input type="range" min="30" max="120" bind:value={spinFrequency} /><br />
      <span style='font-size: 2rem'>{spinFrequency} rpm ({spinFrequencySeconds} rps)</span><br /><br />
    </label>

    <label>
      <input type="range" min="30" max="120" bind:value={flashFrequency} /><br />
      <span style='font-size: 2rem'>{flashFrequency} fpm ({flashFrequencySeconds} Hz)</span><br />
    </label>

    <br />
    <button on:click={() => flashFrequency = spinFrequency}>Igualar</button>
  </div>
</main>

<style>
  .spinner {
    height: 6em;
    padding: 1.5em;
    /* animation: spin var(--duration) linear infinite; */
  }

  @keyframes spin {
    100% {
      transform: rotate(360deg);
    }
  }
</style>
