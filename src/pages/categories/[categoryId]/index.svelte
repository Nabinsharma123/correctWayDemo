<script>
    import { url, params } from "@roxi/routify";
    import { authenticationStatus, CategoryCard } from "../../store";
    import nhost from "../../nhostConfig";

    import ArrowRight from "carbon-icons-svelte/lib/ArrowRight.svelte";
    import { gql } from "@apollo/client/core";

    var name = "",
        color = "",
        imageId = "";
    var subCategories = false;
    $: if ($CategoryCard.length != 0) {
        $CategoryCard.map((element) => {
            if (element.name == $params.categoryId) {
                name = element.name;
                color = element.color;
                imageId = element.imageId;
            }
        });
    }
    $: if ($authenticationStatus == "SIGNED_IN") {
        fetch();
    }

    const query = gql`
                query {
                    subCategories(where: {categories: {name: {_eq: "${$params.categoryId}"}}}) {
                         name
                         }
                }
            `;

    function fetch() {
        nhost.graphql.request(query).then(({ data }) => {
            subCategories = [];
            if (data.subCategories.length != 0) {
                data.subCategories.map((subCategory) => {
                    subCategories = [...subCategories, subCategory.name];
                });
            }
        });
    }
</script>

<svelte:head>
    <title>{$params.categoryId}</title>
</svelte:head>
<div class="flex flex-col items-center">
    <div
        class="border-r border-b w-full max-w-[500px] border-black h-28  rounded-3xl p-10 mb-10 flex items-center"
        style={`background-color:#${color}; box-shadow: 3px 3px 0px #000;`}
    >
        <div class="flex w-full items-center justify-between">
            <div>
                {#if imageId}
                    <img
                        src={`https://ebnddldyrinhdryebuyo.storage.ap-south-1.nhost.run/v1/files/${imageId}`}
                        alt=""
                    />
                {/if}
            </div>
            <h1 class="text-3xl font-bold ">
                {name}
            </h1>
        </div>
    </div>

    <div class="w-full max-w-[500px]">
        {#if subCategories}
            <div class="flex flex-col gap-3">
                {#if subCategories.length != 0}
                    {#each subCategories as subCategory}
                        <a
                            href={$url(
                                `/categories/${$params.categoryId}/:subCategoryId`,
                                {
                                    subCategoryId: subCategory.replace(
                                        /\s+/g,
                                        "-"
                                    ),
                                }
                            )}
                        >
                            <div
                                class="flex p-3 justify-between border  border-black rounded-lg items-center "
                                style="box-shadow: 3px 3px 0px #000;"
                            >
                                <h1
                                    class="text-2xl text-gray-700 font-semibold"
                                >
                                    {subCategory}
                                </h1>
                                <div class=" translate-y-1">
                                    <ArrowRight size={32} />
                                </div>
                            </div>
                        </a>
                    {/each}
                {:else}
                    <h1 class="text-2xl text-gray-700 font-semibold">
                        Coming Soon
                    </h1>
                {/if}
            </div>
        {:else}
            <div class="w-full flex justify-center items-center">
                <img class="h-20" src="/loading.svg" alt="" />
            </div>
        {/if}
    </div>
</div>
