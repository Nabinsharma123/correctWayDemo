<script>
    import Search from "carbon-icons-svelte/lib/Search.svelte";
    import ArrowRight from "carbon-icons-svelte/lib/ArrowRight.svelte";
    import { url, goto, focus } from "@roxi/routify";
    import { authenticationStatus } from "./store";
    import { gql } from "@apollo/client/core";
    import nhost from "./nhostConfig";
    import { searchText } from "./store";

    var inputSearch = "";
    var searchResults = [];
    var myTimeout;
    var isSearchLoading = false;
    $: $searchText = inputSearch;

    if ($searchText) {
        isSearchLoading = true;
        inputSearch = $searchText;
        onInputChange();
    }

    function onInputChange() {
        searchResults = [];
        if (inputSearch != "") {
            const searchQuery = gql`
        query {
          categories(where: {name: {_ilike: "%${inputSearch}%"}}) {
            name
          }

          subCategories(where: {name: {_ilike: "%${inputSearch}%"}}) {
            name
             categories {
                  name
             }
             }

          categoryTopics(where: { Topic: { _ilike:"%${inputSearch}%" } }) {
            TopicId
            Topic
            subCategories {
               name
                  }
            category {
            name
             }
          }
        }
      `;

            if ($authenticationStatus == "SIGNED_IN") {
                nhost.graphql.request(searchQuery).then((res) => {
                    isSearchLoading = false;
                    console.log(res);
                    if (
                        res.data.categories.length == 0 &&
                        res.data.categoryTopics.length == 0 &&
                        res.data.subCategories.length == 0
                    ) {
                        searchResults = [
                            {
                                link: "",
                                topic: "No Result Found",
                            },
                        ];
                    } else {
                        res.data.categories.map((categoriesName) => {
                            searchResults = [
                                ...searchResults,
                                {
                                    link: `/categories/${categoriesName.name}`,
                                    topic: categoriesName.name,
                                },
                            ];
                        });

                        res.data.subCategories.map((subCategoriesName) => {
                            searchResults = [
                                ...searchResults,
                                {
                                    link: `/categories/${
                                        subCategoriesName.categories.name
                                    }/${subCategoriesName.name.replace(
                                        /\s+/g,
                                        "-"
                                    )}`,
                                    topic: subCategoriesName.name,
                                },
                            ];
                        });

                        res.data.categoryTopics.map((categoryTopicName) => {
                            searchResults = [
                                ...searchResults,
                                {
                                    link: `/categories/${
                                        categoryTopicName.category.name
                                    }/${categoryTopicName.subCategories.name.replace(
                                        /\s+/g,
                                        "-"
                                    )}/${categoryTopicName.TopicId}`,
                                    topic: categoryTopicName.Topic,
                                },
                            ];
                        });
                    }
                });
            }
        } else isSearchLoading = false;
    }
</script>

<div class="relative flex justify-center">
    <div class="mx-5 fixed flex">
        <div
            class=" flex items-center py-1 border w-full border-black rounded-md bg-[#E3E7F5]"
        >
            <label
                class="flex w-full justify-center items-center "
                for="Search"
            >
                <Search class="ml-3 mr-1" size={32} />
                <input
                    use:focus
                    id="Search"
                    class="outline-none mr-1 text-xl w-full bg-[#E3E7F5]"
                    type="text"
                    autocomplete="off"
                    bind:value={inputSearch}
                    on:keydown={() => {
                        isSearchLoading = true;
                        clearTimeout(myTimeout);
                    }}
                    on:keyup={() => {
                        clearTimeout(myTimeout);
                        myTimeout = setTimeout(onInputChange, 300);
                    }}
                />
            </label>
        </div>

        <button
            class="bg-black ml-2 w-12 rounded-md flex justify-center items-center "
            on:click={() => {
                inputSearch = "";
                searchResults = [];
            }}
        >
            <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 32 32"
                fill="#fff"
                preserveAspectRatio="xMidYMid meet"
                width="32"
                height="32"
            >
                <path
                    d="M24 9.4L22.6 8 16 14.6 9.4 8 8 9.4 14.6 16 8 22.6 9.4 24 16 17.4 22.6 24 24 22.6 17.4 16 24 9.4z"
                    fill="#fff"
                />
            </svg>
        </button>
    </div>

    <div class="pt-12 w-full md:w-[768px]">
        {#if $authenticationStatus == "SIGNED_IN"}
            {#if !isSearchLoading}
                {#each searchResults as result}
                    <a href={$url(`${result.link}`)}>
                        <div
                            class=" flex flex-col justify-center border-b min-h-12 max-h-fit pl-2 py-2 border-black"
                        >
                            <div class="flex justify-between items-center">
                                <h1 class="text-xl font-semibold  ">
                                    {result.topic}
                                </h1>

                                {#if result.topic != "No result Found"}
                                    <div>
                                        <ArrowRight size={32} />
                                    </div>
                                {/if}
                            </div>
                        </div>
                    </a>
                {/each}
            {:else if inputSearch != ""}
                <div class="flex justify-center items-center h-full w-full">
                    <img class="top-0 h-12" src="/loading.svg" alt="" />
                </div>
            {/if}
        {/if}
    </div>
</div>
