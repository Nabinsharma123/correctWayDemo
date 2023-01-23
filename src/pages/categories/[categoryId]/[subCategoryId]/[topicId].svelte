<script>
    import { url, params } from "@roxi/routify";
    import { authenticationStatus } from "../../../store";
    import { onMount } from "svelte";
    import nhost from "../../../nhostConfig";

    import { gql } from "@apollo/client/core";
    import { ready } from "@roxi/routify";

    var TopicHead = "loading...";

    var TopicData = false;

    $: if ($authenticationStatus == "SIGNED_IN") {
        fetch();
    }
    function fetch() {
        const query = gql`
                query {
                    categoryTopics_by_pk(TopicId: ${$params.topicId}) {
    Topic
    TopicData
  }
  }
                
            `;
        nhost.graphql.request(query).then((res) => {
            $ready();
            TopicHead = res.data.categoryTopics_by_pk.Topic;
            TopicData = res.data.categoryTopics_by_pk.TopicData;
        });
    }
</script>

<svelte:head>
    <title>{TopicHead}</title>
</svelte:head>
<div>
    <div class="mb-7 items-center ">
        <h1 class="text-2xl Headline  text-gray-700 font-semibold">
            {TopicHead}
        </h1>
    </div>
    <!-- {#if TopicImageId}
        <img
            class="h-56 w-full max-w-[500px] mb-7 rounded-3xl border border-black object-cover"
            src={`https://ebnddldyrinhdryebuyo.storage.ap-south-1.nhost.run/v1/files/${TopicImageId}`}
            alt=""
        />
    {/if} -->

    {#if TopicData}
        <div class="Topicdata">
            {@html TopicData}
        </div>
    {:else}
        <div class="w-full flex justify-center items-center">
            <img class="h-20" src="/loading.svg" alt="" />
        </div>
    {/if}
</div>

<style>
    @import url("https://fonts.googleapis.com/css2?family=Arvo&display=swap");
    .Headline {
        font-family: "Arvo", serif;
    }
    .Topicdata :global(ol) {
        list-style: decimal;
        padding-left: 20px;
    }
    .Topicdata :global(ol > li)::marker {
        font-weight: bold;
    }
    .Topicdata :global(li) {
        margin-bottom: 10px;
        font-size: larger;
    }
</style>
