<script>
  import "animate.css";
  import { tick } from "svelte";
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

  const move = async (position) => {
    const before = position - 1;
    const after = position + 1;
    const above = position - numCols;
    const below = position + numCols;

    const empty = [before, after, above, below].find(
      (pos) => shufflz[pos] === null
    );

    if (shufflz[empty] !== null) return;

    // change tile position
    shufflz[empty] = shufflz[position];
    shufflz[position] = null;

    await tick();

    const animation = {
      [before]: ["slideInRight", "slideInLeft"],
      [after]: ["slideInLeft", "slideInRight"],
      [above]: ["slideInUp", "slideInDown"],
      [below]: ["slideInDown", "slideInUp"],
    }[empty];

    const clickedTile = document.getElementById(empty);
    const emptyTile = document.getElementById(position);
    clickedTile.classList.add(`animate__${animation[0]}`);
    emptyTile.classList.add(`animate__${animation[1]}`);

    clickedTile.addEventListener("animationend", () => {
      clickedTile.classList.remove(`animate__${animation[0]}`);
      emptyTile.classList.remove(`animate__${animation[1]}`);
    });

    finished = numbers.join() === shufflz.slice(0, -1).join();
    if (!finished) return;

    setTimeout(() => {
      shufflz = shuffle(); // restart game
    }, 3500);
  };
</script>

<div class="min-h-screen flex items-center justify-center flex-col">
  <img src="shufflz.svg" alt="Shufflz" class="pb-12 w-35 md:w-55" />
  <div class="grid gap-4 grid-cols-4 p-4 m-4 bg-zinc-100 rounded-lg">
    {#each shufflz as number, position}
      <button
        id={position.toString()}
        class="{number
          ? 'bg-zinc-200'
          : 'bg-zinc-300'} text-zinc-600 text-xl md:text-2xl font-bold p-6 md:p-16 lg:p-10 rounded-lg animate__animated animate__faster"
        on:click={() => move(position)}
      >
        {number || ""}
      </button>
    {/each}
  </div>
  <a href="https://github.com/eduardosasso/shufflz" target="_blank">
    <img src="github.svg" alt="Shufflz" class="pt-12 md:pb-4 w-5" />
  </a>
</div>

{#if finished}
  <div class="absolute top-10 left-1/2">
    <ConfettiExplosion />
  </div>
{/if}
