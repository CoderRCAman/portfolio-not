<script>
  import { onMount } from "svelte";
  import gsap from "gsap";
  export let queue;
  let tl;
  console.log(queue);
  onMount(() => {
    tl = gsap.timeline({
      delay: .2,
    });
    tl.to(".jyoti", {
      width: 600,
      height: 600,
      duration: 2,
    });
    tl.to(".gforce", {
      maxHeight: "90%",
      maxWidth: "90%",

      duration: 2,
    });
  });
</script>

<div class="h-[60vh] flex justify-center items-center">
  <div
    class="bg-white jyoti rounded-full w-[100px] h-[100px] flex justify-center items-center"
  >
    <div class="max-w-0 max-h-0 gforce">
      {#if queue}
        <img
          alt="hey"
          src={queue.getResult("shin_chan").src}
          class="rounded-full"
        />
      {/if}
    </div>
  </div>
</div>
