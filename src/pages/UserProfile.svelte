<script>
    import { authenticationStatus, userData, notification } from "./store";
    import Edit from "carbon-icons-svelte/lib/Edit.svelte";
    import Close from "carbon-icons-svelte/lib/Close.svelte";
    import CloudUpload from "carbon-icons-svelte/lib/CloudUpload.svelte";
    import { scale } from "svelte/transition";
    import nhost from "./nhostConfig";
    import { gql } from "@apollo/client/core";

    var isEditclick = false;
    var isLoading = false;

    function SignOutUser() {
        nhost.auth.signOut();
        $notification = {
            text: "Successfully Sign Out",
            color: "#17c21d",
        };
    }
    function deleteOldAvatar() {
        const oldAvatarId = $userData.avatarUrl.slice(
            $userData.avatarUrl.lastIndexOf("/") + 1,
            $userData.avatarUrl.length
        );

        nhost.storage.delete({ fileId: `${oldAvatarId}` }).then((e) => {
            console.log(e);
        });
    }
    function uploadImg(e) {
        if (e.target.files.length != 0) {
            isLoading = true;

            deleteOldAvatar();
            var file = e.target.files[0];
            var newAvatarUrl;
            nhost.storage
                .upload({ file, bucketId: "UserProfilePic" })
                .then((e) => {
                    newAvatarUrl = `https://ebnddldyrinhdryebuyo.storage.ap-south-1.nhost.run/v1/files/${e.fileMetadata.id}`;

                    const updateAvatarUrl = gql`mutation {
                     updateUser(pk_columns: {id: "${$userData.id}"}, _set: {avatarUrl: "${newAvatarUrl}"}) {
                             id
                         avatarUrl
                             }
        }`;

                    nhost.graphql.request(updateAvatarUrl).then((e) => {
                        $userData.avatarUrl = e.data.updateUser.avatarUrl;
                        isLoading = false;
                        $notification = {
                            text: "Updated",
                            color: "#17c21d",
                        };
                    });
                });
        }
    }
</script>

<svelte:head>
    <link rel="prefetch" href="/userProfile.svg" as="document" />
</svelte:head>
<div class="h-full">
    {#if $authenticationStatus == "SIGNED_IN"}
        <div class="w-full flex flex-col items-center ">
            <div
                class="relative w-full max-w-[500px] flex p-2 border-t-2 border-l-2 border-black rounded-xl justify-around items-center"
                style={`box-shadow: 3px 3px 1px 1px; background-color:#fff;`}
            >
                <div class="absolute top-3 right-3">
                    <button on:click={() => (isEditclick = true)}>
                        <Edit size={24} />
                    </button>
                </div>
                <div class=" flex-1 flex justify-center">
                    <img
                        class="  h-20 w-20 p-1 border-2 border-black rounded-full"
                        src={$userData.avatarUrl}
                        referrerpolicy="no-referrer"
                        alt=""
                    />
                </div>
                <div class="flex-[3] flex flex-col items-center">
                    <h1 class="font-bold text-xl">
                        {$userData.displayName}
                    </h1>
                    <p class="text-gray-500">{$userData.email}</p>
                </div>
            </div>
            <div class="w-full flex  mt-5  justify-center">
                <button
                    class="text-red-500 text-xl border-2 bg-white border-red-500 rounded-xl  h-12 w-40 font-semibold active:bg-red-500 active:text-white hover:bg-red-500 hover:text-white"
                    on:click={() => SignOutUser()}>Sign Out</button
                >
            </div>
        </div>
    {/if}
</div>

{#if isEditclick}
    <div
        class="fixed top-0 left-0 z-50 w-full h-screen flex justify-center items-center"
        style="background-color: rgba(0, 0, 0, 0.5);"
    >
        <div
            class=" bg-white border-t border-l border-black p-5 rounded-xl "
            style="height: 80vh ; width: 80vw; max-width: 600px; box-shadow: 3px 3px 1px 1px"
            transition:scale|local
        >
            <div class="flex justify-end mb-3">
                <button on:click={() => (isEditclick = false)}>
                    <Close size={32} />
                </button>
            </div>

            <div class=" w-full ">
                <div class="relative w-full flex justify-center mb-3">
                    <img
                        class=" w-24 h-24 border border-black rounded-full"
                        src={$userData.avatarUrl}
                        referrerpolicy="no-referrer"
                        alt=""
                    />
                    <!-- loading -->
                    {#if isLoading}
                        <div
                            class="absolute flex justify-center items-center w-full h-full top-0 left-0 rounded-xl "
                            style="background-color: rgba(255, 255, 255 , 0.5);"
                        >
                            <img class="w-14" src="/loading.svg" alt="" />
                        </div>
                    {/if}
                    <!-- loading -->
                </div>
                <div class=" w-full flex justify-center ">
                    <label
                        class="p-2 rounded-md flex items-center w-fit  bg-pink-400"
                        for="imgFile"
                    >
                        <CloudUpload size={24} />
                        Upload Image
                    </label>
                    <input
                        accept=".jpg, .jpeg, .png, .svg"
                        on:change={uploadImg}
                        class="w-[0.1px] h-[0.1px]"
                        id="imgFile"
                        type="file"
                    />
                </div>
                <!-- <button on:click={upload}>click</button> -->
            </div>
        </div>
    </div>
{/if}

<style>
</style>
