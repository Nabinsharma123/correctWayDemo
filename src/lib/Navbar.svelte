<script>
    import ArrowLeft from "carbon-icons-svelte/lib/ArrowLeft.svelte";
    import Search from "carbon-icons-svelte/lib/Search.svelte";

    import Menu from "carbon-icons-svelte/lib/Menu.svelte";
    import { isActive } from "@roxi/routify";
    import LeftPane from "./LeftPane.svelte";
    import { userData } from "../pages/store";
    import { url } from "@roxi/routify";
    import { authenticationStatus } from "../pages/store";

    var isLeftPanelClick = false;
</script>

<div
    class="navbar z-10 w-full px-4 py-2  flex items-center fixed left-0 top-0 md:border md:border-gray-100  md:bg-white/50 md:backdrop-blur-sm"
>
    <div class="h-10 flex items-center gap-16 flex-1">
        <div
            class="navbar3Dot bg-white rounded-full md:bg-transparent p-1 -ml-1"
        >
            {#if $isActive("./index")}
                <div
                    on:click={() => {
                        isLeftPanelClick = true;
                    }}
                >
                    <Menu size={32} />
                </div>
            {:else}
                <div on:click={() => window.history.back()}>
                    <ArrowLeft size={32} />
                </div>
            {/if}
        </div>

        <a class="hidden md:block" href={$url("/index")}>
            <div>
                <h1 class="font-semibold text-xl">Home</h1>
            </div>
        </a>
        <a class="hidden md:block" href={$url("/categories")}>
            <div>
                <h1 class="font-semibold text-xl">Categories</h1>
            </div>
        </a>
        <a class="hidden md:block" href={$url("/Search")}>
            <div>
                <div class="flex items-center">
                    <Search class="-mb-1" size={24} />
                    <h1 class=" ml-1 font-semibold text-xl">Search</h1>
                </div>
            </div>
        </a>
    </div>
    {#if $authenticationStatus == "SIGNED_IN"}
        <div class=" flex justify-end">
            <a href={$url("/UserProfile")}>
                <div
                    class="flex gap-2 bg-white md:bg-transparent justify-center rounded-full items-center"
                >
                    <img
                        class="h-10 w-10  rounded-full"
                        src={$userData.avatarUrl}
                        referrerpolicy="no-referrer"
                        alt=""
                    />
                    <h1 class="hidden md:block username">
                        {$userData.displayName}
                    </h1>
                </div>
            </a>
        </div>
    {:else}
        <a href={$url("/UserProfile")}>
            <button
                class="px-4 py-[6px] font-bold border-2 bg-white rounded-lg border-black "
            >
                Sign In
            </button>
        </a>
    {/if}
</div>

{#if isLeftPanelClick}
    <LeftPane on:close={() => (isLeftPanelClick = false)} />
{/if}

<style>
</style>
