<script>
  import { onMount } from "svelte";
  import Letter from "../components/Letter.svelte";
  import { track } from "../store/track";
  import interact from "interactjs";
  import Secret from "../components/Secret.svelte";
  let queue = null;
  let loader = true;
  let progress = 0;
  let letterWithPos = [];
  let dropped = [];
  let str = "RANDI";
  let result = false;
  let reveal = false;
  let dragEnter = false;
  $: result =
    dropped.length == str.length
      ? dropped.join("") == str
        ? "success"
        : "failed"
      : false;
  $: console.log(result, dropped.join(""));
  onMount(() => {
    queue = new createjs.LoadQueue();
    queue.loadFile({
      id: "background",
      src: "https://ik.imagekit.io/7r3lksjyp/cropped-1920-1080-1318408.png?updatedAt=1691233015642",
    });
    queue.loadFile({
      id: "women",
      src: "https://images.pexels.com/photos/17046572/pexels-photo-17046572.jpeg?cs=srgb&dl=pexels-iv%C3%A1n-cauich-17046572.jpg&fm=jpg&_gl=1*1p9j4bq*_ga*MTIzMzQ2OTU2MC4xNjkxMTQ5MTM0*_ga_8JE65Q40S6*MTY5MTIzNzk0NC4zLjEuMTY5MTIzNzk1Mi4wLjAuMA..",
    });
    queue.loadFile({
      id: "shin_chan",
      src: "https://ik.imagekit.io/7r3lksjyp/shin-chan-dance.gif?updatedAt=1691344971161",
    });
    queue.on("complete", handleComplete, this);
    queue.on("progress", (p) => {
      progress = parseInt(p.progress * 100);
    });
    let existing_x_values = {};
    let existing_y_values = {};
    for (let i = 0; i < str.length; i++) {
      let randX;
      let randY;
      do {
        randX = Math.abs(Math.floor(Math.random() * 100) - 10);
      } while (existing_x_values[randX]);
      do {
        randY = Math.abs(Math.floor(Math.random() * 100) - 5);
      } while (existing_y_values[randY]);
      existing_x_values[randX] = true;
      existing_y_values[randY] = true;
      letterWithPos.push({
        letter: str[i],
        pos: {
          x: randX,
          y: randY,
        },
      });
      track.update((n) => {
        return { ...n, [i]: false };
      });
    }
    //  drop part goes here
    interact(".dropzone").dropzone({
      // only accept elements matching this CSS selector
      accept: ".drag-drop",
      // Require a 75% element overlap for a drop to be possible
      overlap: 0.75,
      // listen for drop related events:
      ondragenter: function (event) {
        console.log("working");
        dragEnter = true;
      },
      ondragleave: function () {
        console.log("drag leave");
        dragEnter = false;
      },
      ondrop: function (event) {
        dragEnter = false;
        const dragElement = event.relatedTarget;
        const index = dragElement.getAttribute("data-id");
        dropped = [...dropped, str[index]];
      },
    });
  });
  function handleComplete() {
    loader = false;
  }
</script>

<div class="text-white h-screen">
  {#if loader}
    <div class="h-full w-full flex justify-center items-center flex-col gap-2">
      <div
        class=" border-[1px] border-slate-800 px-10 py-4 rounded-md shadow-2xl font-tektur text-2xl"
      >
        {progress}%
      </div>
      <h1 class="text-lg">loading assets for you 💕</h1>
    </div>
  {:else}
    <h1
      class="text-center {reveal &&
        'opacity-0'} transition-all duration-500 font-Titillium_Web text-4xl pt-10"
    >
      WELCOME TO THE HORNY PORTFOLIO
    </h1>
    <p
      class="text-center mt-5 text-lg font-Montserrat {reveal &&
        'opacity-0'} transition-all duration-500"
    >
      Re arrange the characters to form the word <span
        class="font-tektur text-xl">{str}</span
      > and reveal my gift!
    </p>
    <div class="{reveal ? 'hidden' : 'block'} transition-all duration-300">
      {#if result == "success" || result == "failed"}
        <div class="flex h-[60vh] justify-center items-center">
          {#if result == "success"}
            <button
              on:click={() => {
                reveal = true;
              }}
              class="px-4 py-2 text-emerald-600 border-[1px] border-emerald-600 rounded-md"
            >
              REVEAL
            </button>
          {:else}
            <button
              on:click={() => {
                dropped = [];
              }}
              class="px-4 py-2 text-red-600 border-[1px] border-red-600 rounded-md"
              >PLEASE RELOAD AGAIN!</button
            >
          {/if}
        </div>
      {:else}
        <div class="h-[60vh] w-screen relative">
          {#each letterWithPos as value, index}
            <Letter
              letter={value.letter}
              position={value.pos}
              key={index}
              {dropped}
            />
          {/each}
        </div>
      {/if}

      <div class="flex justify-center mt-5 rounded-md pb-5 items-center gap-2">
        <div
          class="dropzone w-60 border-[1px] {dragEnter
            ? 'border-cyan-800'
            : 'border-slate-800 '} h-14 rounded-md flex items-center px-2 gap-2 justify-center"
        >
          {#if dropped.length == 0 && !dragEnter}
            <h1 class="font-Montserrat">DROP LETTERS HERE!</h1>
          {/if}
          {#each dropped as value}
            <div class=" rounded-md font-tektur">
              {value}
            </div>
          {/each}
        </div>
        <button
          on:click={() => {
            dropped = [];
          }}
          class="border-[1px] border-slate-800 p-2 rounded-md"
        >
          <svg
            fill="#334155"
            height="25px"
            width="25px"
            version="1.1"
            id="Capa_1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            viewBox="0 0 489.645 489.645"
            xml:space="preserve"
            style="--darkreader-inline-fill: #000000;"
            data-darkreader-inline-fill=""
            ><g id="SVGRepo_bgCarrier" stroke-width="0" /><g
              id="SVGRepo_tracerCarrier"
              stroke-linecap="round"
              stroke-linejoin="round"
            /><g id="SVGRepo_iconCarrier">
              <g>
                <path
                  d="M460.656,132.911c-58.7-122.1-212.2-166.5-331.8-104.1c-9.4,5.2-13.5,16.6-8.3,27c5.2,9.4,16.6,13.5,27,8.3 c99.9-52,227.4-14.9,276.7,86.3c65.4,134.3-19,236.7-87.4,274.6c-93.1,51.7-211.2,17.4-267.6-70.7l69.3,14.5 c10.4,2.1,21.8-4.2,23.9-15.6c2.1-10.4-4.2-21.8-15.6-23.9l-122.8-25c-20.6-2-25,16.6-23.9,22.9l15.6,123.8 c1,10.4,9.4,17.7,19.8,17.7c12.8,0,20.8-12.5,19.8-23.9l-6-50.5c57.4,70.8,170.3,131.2,307.4,68.2 C414.856,432.511,548.256,314.811,460.656,132.911z"
                />
              </g>
            </g></svg
          >
        </button>
      </div>
    </div>
    {#if reveal}
      <Secret {queue} />
    {/if}
  {/if}
</div>
