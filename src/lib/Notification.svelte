<script>
    import { onMount } from "svelte";
    import { fade, fly } from "svelte/transition";
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
    export var color = "#FFCB42";
    export var text = "blabla";
    onMount(() => {
        setTimeout(() => {
            dispatch("close");
        }, 3000);
    });
</script>

<div
    transition:fly|local={{ y: -30, duration: 500 }}
    class="fixed top-10 z-50 pointer-events-none flex justify-center w-full"
>
    <div
        class="bg-white border-2 rounded-lg shadow-md py-3 items-center flex px-5"
        style={`border-color:${color} ;`}
    >
        <h1 class="font-semibold text-lg" style={`color:${color};`}>{text}</h1>
    </div>
</div>
