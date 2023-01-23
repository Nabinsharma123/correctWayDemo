<script>
    import { url } from "@roxi/routify";
    import { authenticationStatus } from "../store";
    import { CategoryCard } from "../store";
</script>

<svelte:head>
    <title>Categories</title>
</svelte:head>
<div>
    <div>
        <h1 class=" mb-7 text-3xl font-bold">All Categories</h1>
    </div>
    {#if $authenticationStatus == "SIGNED_IN"}
        <div class=" CategoryGrid ">
            {#each $CategoryCard as { color, name, imageId }}
                <a href={$url("/categories/:id", { id: name })}>
                    <div class="w-full flex justify-center">
                        <div
                            class="relative border border-black w-full p-3  h-52 rounded-3xl "
                            style={`background-color:#${color}; box-shadow: 3px 3px 0px #000;`}
                        >
                            {#if imageId}
                                <img
                                    src={`https://ebnddldyrinhdryebuyo.storage.ap-south-1.nhost.run/v1/files/${imageId}`}
                                    alt=""
                                />
                            {/if}
                            <h1
                                class="absolute text-2xl  font-bold bottom-3 right-3  "
                            >
                                {name}
                            </h1>
                        </div>
                    </div>
                </a>
            {/each}
        </div>
    {/if}
</div>

<style>
    .CategoryGrid {
        display: grid;
        gap: 1rem;
        grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    }
</style>
