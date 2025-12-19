<!--> TODO: Make timer beep many times, beautify it, add widgets -->

<script lang=ts>
	import { onMount, afterUpdate, onDestroy } from "svelte";

	// default prop is 25
	export let countdown = 25 * 60;
	let timer: ReturnType<typeof setInterval> | null = null;
    const sleep = (ms: number) =>
  new Promise<void>(resolve => setTimeout(resolve, ms));

        

	// when countdown update, update displayValue
	$: {
  const minutes = Math.floor(countdown / 60);
  const seconds = countdown % 60;
  displayValue = `${minutes.toString().padStart(2, '0')}:${seconds
    .toString()
    .padStart(2, '0')}`;
    }
    let displayValue = "";

	function startTimer() {
      beeped = false;
	  timer = setInterval(() => {
	    countdown -= 1;
	  }, 1250);
	}

    function stopTimer() {
    if (timer) {
      clearInterval(timer);
      timer = null;
        }
    }

    function resetTimer() {
      stopTimer();
      countdown = 25 * 60; // reset to default value
    }

	afterUpdate(() => {
	  if (countdown === 0) {
	    if (timer) {
	      clearInterval(timer);
	      timer = null;
	    }
	  }
	});

	onDestroy(() => {
	  if (timer) {
	    clearInterval(timer);
	  }
	});

function beep() {
  const ctx = new AudioContext();

  const osc = ctx.createOscillator();
  const gain = ctx.createGain();

  osc.type = "sine";
  osc.frequency.value = 880; // A5

  gain.gain.setValueAtTime(0, ctx.currentTime);
  gain.gain.linearRampToValueAtTime(0.1, ctx.currentTime + 0.01);
  gain.gain.linearRampToValueAtTime(0, ctx.currentTime + 1);

  osc.connect(gain);
  gain.connect(ctx.destination);

  osc.start(ctx.currentTime);
  osc.stop(ctx.currentTime + 1);
}


let beeped = false;

$: if (countdown === 0 && !beeped) {
  beep();
  beeped = true;
}

</script>

<h1>{displayValue}</h1>
<button class="timer-btn" onclick={startTimer}>start</button>
<button class="timer-btn" onclick={stopTimer}>stop</button>
<button class="timer-btn" onclick={resetTimer}>reset</button>
