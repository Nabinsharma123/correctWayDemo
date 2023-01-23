<script>
    import { fly } from "svelte/transition";
    import { clickOutside } from "./click_outside.js";
    import { url } from "@roxi/routify";
    import Close from "carbon-icons-svelte/lib/Close.svelte";
    import ChevronRight from "carbon-icons-svelte/lib/ChevronRight.svelte";

    import { authenticationStatus, userData } from "../pages/store";
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
</script>

<div
    class="fixed w-full top-0 left-0 z-10  h-screen"
    style="background-color: rgba(0, 0, 0, 0.7)"
>
    <div
        class="h-full p-4 bg-white left-0 rounded-r-xl w-72"
        transition:fly|local={{ x: -200 }}
        use:clickOutside
        on:outclick={() => dispatch("close")}
    >
        <div
            class="flex border-b py-6 border-black items-center justify-between"
        >
            <h1 class="font-bold text-2xl ">CorrectWay</h1>
            <div on:click={() => dispatch("close")}>
                <Close size={32} />
            </div>
        </div>

        <div class="w-full mt-3">
            <a on:click={() => dispatch("close")} href={$url("/UserProfile")}>
                {#if $authenticationStatus == "SIGNED_IN"}
                    <div class="flex items-center justify-start">
                        <div class=" flex-1">
                            <img
                                class="border border-black p-1 rounded-full h-12 w-12"
                                src={$userData.avatarUrl}
                                referrerpolicy="no-referrer"
                                alt=""
                            />
                        </div>
                        <div class="flex flex-[4] w-full justify-between">
                            <h1 class="ml-3 text-lg font-semibold">
                                {$userData.displayName}
                            </h1>
                            <div>
                                <ChevronRight size={32} />
                            </div>
                        </div>
                    </div>
                {:else}
                    <div>
                        <button
                            class=" w-full rounded-md p-2 bg-slate-500 text-white text-xl"
                            >Sign In</button
                        >
                    </div>
                {/if}
            </a>
        </div>
    </div>
</div>
