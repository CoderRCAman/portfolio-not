<script>
  import interact from "interactjs";
  import { track } from "../store/track";
  export let letter;
  export let position; // x and y co-ordinates
  export let key;
  export let dropped;
  let isDragging;
  track.subscribe((value) => {
    isDragging = value;
  });
  let hide = false;
  $: hide = dropped.find((char) => char == letter) ? true : false;

  interact(".drag-drop").draggable({
    inertia: true,
    modifiers: [
      interact.modifiers.restrictRect({
        endOnly: true,
      }),
    ],
    autoScroll: true,
    // dragMoveListener from the dragging demo above
    listeners: {
      move: dragMoveListener,
      end: (event) => {
        var target = event.target;
        const id = target.getAttribute("data-id");
        track.update((n) => {
          return {
            ...n,
            [id]: false,
          };
        });
      },
    },
  });
  function dragMoveListener(event) {
    var target = event.target;
    // keep the dragged position in the data-x/data-y attributes
    var x = (parseFloat(target.getAttribute("data-x")) || 0) + event.dx;
    var y = (parseFloat(target.getAttribute("data-y")) || 0) + event.dy;
    let id = target.getAttribute("data-id");
    track.update((n) => {
      return {
        ...n,
        [id]: true,
      };
    });
    // translate the element
    target.style.transform = "translate(" + x + "px, " + y + "px)";

    // update the posiion attributes
    target.setAttribute("data-x", x);
    ``;
    target.setAttribute("data-y", y);
  }

  // this function is used later in the resizing and gesture demos
  window.dragMoveListener = dragMoveListener;
</script>

<div
  data-id={key}
  class="drag-drop absolute {hide ? 'hidden' : 'block'} {isDragging[key]
    ? 'border-green-500'
    : 'border-gray-800'} shadow-lg border-[1px] px-5 py-2 rounded-md font-tektur select-none cursor-grab"
  style="top: {position.x}%; left:{position.y}%;touch-action:none;"
  role="button"
  tabindex="-1"
>
  {letter}
</div>

<!-- on:drag={() => {
    hide = true;
  }}
  on:dragstart={(e) => {
    e.dataTransfer.setData("index", JSON.stringify({ index: key }));
  }}
  on:dragend={() => {
    hide = dropped.find((char) => char == letter) ? true : false;
  }}
  on:touchstart={(e) => { 
    // console.log(e)
    // e.dataTransfer.setData("index", JSON.stringify({ index: key }));
    e.draggable = true
    console.log(e)
  }}
  on:touchend={() => {
    hide = dropped.find((char) => char == letter) ? true : false;
  }} -->
