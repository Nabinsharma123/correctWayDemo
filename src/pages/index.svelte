<script>
  import Search from "carbon-icons-svelte/lib/Search.svelte";
  import ArrowRight from "carbon-icons-svelte/lib/ArrowRight.svelte";
  import { authenticationStatus } from "./store";
  import { url, goto } from "@roxi/routify";
  import { CategoryCard } from "./store";
</script>

<svelte:head>
  <title>CorrectWay</title>
</svelte:head>
<div class="">
  <div class="homePageTop md:flex md:items-center ">
    <div class="homePageTopLeft md:flex-1">
      <div class=" flex flex-wrap mb-7">
        <h1 class=" text-5xl pr-4 font-bold">Lorem ipsum dolor</h1>
        <div class="relative w-fit text-5xl font-bold">
          <h1>sit amet</h1>
          <img class="absolute -bottom-3  " src="/Underline.svg" alt="" />
        </div>
      </div>

      <h1 class=" text-xl leading-6 mb-7">
        Cras quis cursus neque. Pellentesque aliquet, libero eget consectetur
        pretium, lacus justo
      </h1>

      <!-- search box -->
      <div class="h-24 w-fit">
        <div
          class="flex items-center py-1 border-2 w-72 border-black rounded-xl bg-[#E3E7F5]"
        >
          <a href="/Search">
            <label
              class="flex w-full justify-center items-center "
              for="Search"
            >
              <Search class="ml-3 mr-1" size={32} />
              <input
                id="Search"
                class="outline-none text-xl mr-1 w-full bg-[#E3E7F5]"
                type="text"
                autocomplete="off"
              />
            </label>
          </a>
        </div>
      </div>
    </div>

    <!-- homeImage -->
    <div class="homePageTopRight flex justify-center md:flex-1">
      <img class="w-[450px]" src="./homeImg.svg" alt="" />
    </div>
  </div>
  <!-- Categories -->
  <div class="flex items-center ">
    <h1 class="text-2xl font-bold md:text-3xl ">Categories</h1>
    <div class="translate-y-1">
      <ArrowRight size={32} />
    </div>
  </div>
  <!-- Categories cards-->
  <div class=" w-full md:flex  ">
    <div class="flex-1 hidden md:flex md:justify-center">
      <img class="h-[450px]" src="/homeMiddleImg.svg" alt="" />
    </div>

    <div
      class="flex-1 w-full categories mt-5 overflow-auto md:flex md:justify-end md:h-[450px]"
    >
      <div class="w-fit flex items-center md:grid md:grid-cols-2 ">
        {#if $authenticationStatus == "SIGNED_IN"}
          {#each $CategoryCard as { color, name, imageId }}
            <a href={$url("/categories/:id", { id: name })} class="py-1">
              <div
                class="relative border border-black w-40 p-3 mr-2 h-52 rounded-3xl "
                style={`background-color:#${color};box-shadow: 3px 3px 0px #000000;`}
              >
                {#if imageId}
                  <img
                    src={`https://ebnddldyrinhdryebuyo.storage.ap-south-1.nhost.run/v1/files/${imageId}`}
                    alt=""
                  />
                {/if}
                <h1 class="absolute text-2xl font-bold bottom-3 right-3  ">
                  {name}
                </h1>
              </div>
            </a>
          {/each}
        {/if}
      </div>
    </div>
  </div>

  <div class="mt-10 w-full flex justify-center">
    <a href={$url("/categories")} class="w-full flex justify-center">
      <button
        class="bg-[#E3E7F5] w-full max-w-[400px] py-3 border border-black rounded-xl text-xl font-bold "
      >
        All Categories</button
      >
    </a>
  </div>
</div>

<style>
</style>
