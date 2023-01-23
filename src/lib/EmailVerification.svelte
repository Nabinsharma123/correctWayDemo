<script>
    import Close from "carbon-icons-svelte/lib/Close.svelte";
    import { authenticationStatus } from "../pages/store";
    import { fly, fade } from "svelte/transition";
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();

    export var email = "nabinsharma16.ns@gmail.com";

    $: if ($authenticationStatus == "SIGNED_IN") dispatch("close");
</script>

<div
    class="fixed top-0 left-0 z-50 w-full h-screen flex justify-center items-center"
    style="background-color: rgba(0, 0, 0, 0.5);"
>
    <div
        class="bg-white p-4 h-48 w-64 rounded-xl flex  flex-col  items-center"
        transition:fly|local={{ y: -200 }}
    >
        <div class="w-full mb-3 flex justify-end">
            <div on:click={() => dispatch("close")}>
                <Close size={24} />
            </div>
        </div>
        <div class="text-center font-semibold ">
            <h1>We send a verification mail at</h1>
            <h1>{email}</h1>
            <h1>Please click "Verify Email"</h1>
        </div>
    </div>
</div>
