<script>
    import { url, isActive } from "@roxi/routify";
    import Home from "carbon-icons-svelte/lib/Home.svelte";
    import Workspace from "carbon-icons-svelte/lib/Workspace.svelte";
    import Search from "carbon-icons-svelte/lib/Search.svelte";
    import Settings from "carbon-icons-svelte/lib/Settings.svelte";
</script>

<div
    class="taskbar bottom-3 z-30  fixed  pointer-events-none flex justify-center items-center md:hidden"
    style="width: 100%;"
>
    <div
        class=" w-11/12 max-w-[600px] pointer-events-auto flex  py-1 justify-around items-center rounded-xl backdrop-blur-sm"
        style="background-color: rgba(255, 255, 255, 0.3); box-shadow: 0px 0px 8px 1px; "
    >
        <a class="flex flex-1 justify-center items-center" href={$url("/")}>
            <button class:currentTab={$isActive("./index")}>
                <Home size={32} />
            </button>
        </a>
        <a
            class="flex flex-1 justify-center  items-center"
            href={$url("/categories")}
        >
            <button class:currentTab={$isActive("/categories")}>
                <Workspace size={32} />
            </button>
        </a>
        <a
            class="flex flex-1 justify-center  items-center"
            href={$url("/Search")}
        >
            <button class:currentTab={$isActive("/Search")}>
                <Search size={32} />
            </button>
        </a>
        <a
            class="flex flex-1 justify-center  items-center"
            href={$url("/UserProfile")}
        >
            <button class:currentTab={$isActive("/UserProfile")}>
                <Settings size={32} />
            </button>
        </a>
    </div>
</div>

<style>
    .currentTab {
        border-bottom: 2px solid black;
        padding-bottom: 2px;
    }
    /* 
    @media (min-width: 770px) {
        .taskbar {
            display: none;
        }
    } */
</style>
