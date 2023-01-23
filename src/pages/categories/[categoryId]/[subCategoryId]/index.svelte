<script>
    import { url, params } from "@roxi/routify";
    import { gql } from "@apollo/client/core";
    import nhost from "../../../nhostConfig";
    import { authenticationStatus } from "../../../store";

    var categoryTopics = false;

    const subCategoryHead = $params.subCategoryId.replace(/-/g, " ");
    $: if ($authenticationStatus == "SIGNED_IN") {
        fetch();
    }

    function fetch() {
        const query = gql`
        query {
      categoryTopics(where: {subCategories: {name: {_eq: "${subCategoryHead}"}}}) {
        Topic
        TopicId
      }
    }`;

        nhost.graphql.request(query).then(({ data }) => {
            categoryTopics = [];
            if (data.categoryTopics.length != 0) {
                data.categoryTopics.map((categoryTopic) => {
                    categoryTopics = [...categoryTopics, categoryTopic];
                });
            }
        });
    }
</script>

<div class="relative ">
    <div class=" w-full  mb-6 bg-white">
        <h1 class="text-3xl font-semibold text-gray-700 ">
            {subCategoryHead}
        </h1>
    </div>

    <div class="w-full ]">
        {#if categoryTopics}
            {#if categoryTopics.length != 0}
                <div class="gridDiv">
                    {#each categoryTopics as categoryTopic}
                        <a
                            href={$url(
                                `/categories/${$params.categoryId}/${$params.subCategoryId}/:topicId`,
                                {
                                    topicId: categoryTopic.TopicId,
                                }
                            )}
                        >
                            <div
                                class=" relative border flex gap-2 p-3 border-black rounded-lg"
                                style="box-shadow: 3px 3px 0px #000;"
                            >
                                <!-- <img
                                    class=" h-full w-full rounded-3xl object-cover object-center"
                                    src={`https://ebnddldyrinhdryebuyo.storage.ap-south-1.nhost.run/v1/files/${categoryTopic.ImageId}`}
                                    alt=""
                                /> -->
                                <div class="h-full flex items-center mt-[2px]">
                                    ◼
                                </div>
                                <h1
                                    class="text-xl  text-gray-700 font-semibold"
                                >
                                    {categoryTopic.Topic}
                                </h1>
                                <!-- ♦🔴🟠🟡◻◼▫▪ -->
                            </div>
                        </a>
                    {/each}
                </div>
            {:else}
                <h1 class="text-2xl text-gray-700 font-semibold">
                    Coming Soon
                </h1>
            {/if}
        {:else}
            <div class="w-full flex justify-center items-center">
                <img class="h-20" src="/loading.svg" alt="" />
            </div>
        {/if}
    </div>
</div>

<style>
    .gridDiv {
        display: grid;
        gap: 15px;
        grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    }
</style>
