<script>
  import { ConfettiExplosion } from "svelte-confetti-explosion";

  const numbers = Array(15)
    .fill(null)
    .map((_, i) => i + 1);

  const shuffle = () => {
    const shuffled = [...numbers].sort(() => Math.random() - 0.5);
    return [...shuffled, null];
  };

  const numCols = 4;
  let finished = false;

  let shufflz = shuffle();

  const move = (position) => {
    const before = position - 1;
    const after = position + 1;
    const above = position - numCols;
    const below = position + numCols;

    const empty = [before, after, above, below].find(
      (pos) => shufflz[pos] === null
    );

    if (shufflz[empty] !== null) return;

    shufflz[empty] = shufflz[position];
    shufflz[position] = null;

    verify();
  };

  const verify = () => {
    finished = numbers.join() === shufflz.slice(0, -1).join();
    // finished = shufflz.join().match(/1,2/) !== null;
    if (!finished) return;

    setTimeout(() => {
      shufflz = shuffle();
    }, 3500);
  };
</script>

<div class="min-h-screen flex items-center justify-center flex-col">
  <img src="shufflz.svg" alt="Shufflz" class="pb-14 pt-14 w-80" />
  <div class="grid gap-4 grid-cols-4 p-4 m-4 bg-zinc-100 rounded-lg">
    {#each shufflz as number, position}
      <button
        class="{number
          ? 'bg-zinc-200'
          : 'bg-zinc-300'} text-zinc-600 text-xl font-bold p-10 rounded-lg"
        on:click={() => move(position)}
      >
        {number || ""}
      </button>
    {/each}
  </div>
  <a href="https://github.com/eduardosasso/shufflz" target="_blank">
    <img src="github.svg" alt="Shufflz" class="pt-14 pb-4 w-8" />
  </a>
</div>

{#if finished}
  <div class="absolute top-10 left-1/2">
    <ConfettiExplosion />
  </div>
{/if}
