<script>
    import { url, goto } from "@roxi/routify";
    import {
        userData,
        authenticationStatus,
        CategoryCard,
        notification,
    } from "../store";
    import { gql } from "@apollo/client/core";
    import { fly } from "svelte/transition";
    import nhost from "../nhostConfig";
    import ArrowRight from "carbon-icons-svelte/lib/ArrowRight.svelte";
    import Add from "carbon-icons-svelte/lib/Add.svelte";

    import "https://cdn.ckeditor.com/ckeditor5/35.4.0/decoupled-document/ckeditor.js";
    import { onMount } from "svelte";

    var editor;
    var toolbar_container;
    var topicName;
    var imageURL;
    var imageFile;
    var loading = false;
    var subcategoriesList = [];
    var UploadTo = {
        categoriesId: null,
        subcategoriesId: null,
    };

    onMount(() => {
        DecoupledEditor.create(editor)
            .then((editor) => {
                const toolbarContainer = toolbar_container;

                toolbarContainer.appendChild(editor.ui.view.toolbar.element);
            })
            .catch((error) => {
                console.error(error);
            });
    });

    // async function uploadImgToDB() {
    //     if (imageFile) {
    //         var file = imageFile;
    //         var res = await nhost.storage.upload({ file });
    //         return res.fileMetadata.id;
    //     }
    // }
    async function UploadData() {
        loading = true;
        // var imagId = await uploadImgToDB();
        var data = editor.innerHTML;
        const query = gql`mutation  {
  insert_categoryTopics_one(object: {Topic: "${topicName}",TopicData: ${JSON.stringify(
            data
        )},CategoryId: ${UploadTo.categoriesId},subCategoryId: ${
            UploadTo.subcategoriesId
        }}) {
    TopicId
  }
}
`;

        nhost.graphql.request(query).then((e) => {
            console.log(e);
            loading = false;
            if (e.error) {
                $notification = {
                    text: e.error[0].message,
                    color: "#FF6465",
                };
            } else {
                $notification = {
                    text: "Uploaded",
                    color: "#17c21d",
                };
            }
        });
    }

    // function uploadImg(e) {
    //     imageFile = e.target.files[0];
    //     let reader = new FileReader();
    //     reader.readAsDataURL(imageFile);
    //     reader.onload = (e) => {
    //         imageURL = e.target.result;
    //     };
    // }

    function onCategorySelect(e) {
        var query = gql`
            query MyQuery {
                subCategories(where: { categoryId: { _eq: ${e.target.value} } }) {
                    name
                    id
                }
            }
        `;

        nhost.graphql.request(query).then((data) => {
            subcategoriesList = data.data.subCategories;
        });
    }

    function addNewCategory(e) {
        var form = new FormData(e.target);

        var query = gql`
            mutation MyMutation {
                insert_categories_one(
                    object: { color: "${form
                        .get("color")
                        .slice(1)}", name: "${form.get("category")}" }
                ) {
                    id
                }
            }
        `;
        nhost.graphql.request(query).then((data) => {
            if (e.error) {
                $notification = {
                    text: e.error[0].message,
                    color: "#FF6465",
                };
            } else {
                $notification = {
                    text: "Added",
                    color: "#17c21d",
                };
            }
        });
    }

    function addNewSubCategory(e) {
        var form = new FormData(e.target);
        var query = gql`mutation MyMutation {
  insert_subCategories_one(object: {name: "${form.get(
      "subcategory"
  )}", categoryId: ${UploadTo.categoriesId}}) {
    id
  }
}
`;
        nhost.graphql.request(query).then((data) => {
            console.log(data);
            if (e.error) {
                $notification = {
                    text: e.error[0].message,
                    color: "#FF6465",
                };
            } else {
                $notification = {
                    text: "Added",
                    color: "#17c21d",
                };
                onCategorySelect({ target: { value: UploadTo.categoriesId } });
            }
        });
    }
</script>

<h1 class="text-lg font-semibold">Topic Name:</h1>
<input
    class="mb-10 w-full rounded-md p-1 text-xl border border-gray-400"
    bind:value={topicName}
    type="text"
/>

<div class=" w-full ">
    <!-- {#if imageURL}
            <img
                class="mb-3 max-h-[200px] border border-gray-40 rounded-md"
                src={imageURL}
                alt=""
            />
        {/if} -->

    <!-- <label
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
        /> -->
</div>

<div bind:this={toolbar_container} />
<div class="min-h-[200px]" style="border: 1px solid gray;" bind:this={editor}>
    Write your content here
</div>

<div class="mt-10 p-2 border border-blue-400 rounded-md border-dashed">
    <h1 class="text-2xl font-semibold text-blue-400">Upload To</h1>
    <div class="flex gap-8 items-center">
        <div>
            <div>
                <h1 class="text-xl mb-2 font-semibold">Category :-</h1>
                <select
                    on:change={onCategorySelect}
                    bind:value={UploadTo.categoriesId}
                    class="w-40   border rounded-sm border-blue-400"
                >
                    <option style="display:none" />
                    {#each $CategoryCard as { name, id }}
                        <option value={id}>{name}</option>
                    {/each}
                </select>
            </div>
            <div
                class="mt-3 p-2 rounded-md border border-dashed border-blue-400"
            >
                <h1 class="font-semibold text-blue-400">*Add New Category</h1>

                <form
                    class="flex mt-2 items-center gap-2"
                    action=""
                    on:submit={(e) => {
                        e.preventDefault();
                        addNewCategory(e);
                        e.target.reset();
                    }}
                >
                    <div class="flex flex-col gap-3">
                        <input
                            class="border p-1 border-gray-400 rounded-md "
                            name="category"
                            required
                        />
                        <div class="flex gap-2 items-center">
                            <label class=" text-blue-400" for="color"
                                >Color :</label
                            >
                            <input
                                class="h-7 w-10"
                                type="color"
                                id="color"
                                name="color"
                                required
                            />
                        </div>
                    </div>
                    <button
                        class="border h-14 w-10 flex justify-center items-center border-green-600 rounded-md bg-green-600"
                        type="submit"><Add size={24} /></button
                    >
                </form>
            </div>
        </div>

        <ArrowRight size={32} />
        <div>
            <div>
                <h1 class="text-xl mb-2 font-semibold">Subcategory:-</h1>
                <select
                    disabled={UploadTo.categoriesId == null}
                    bind:value={UploadTo.subcategoriesId}
                    class="w-40   border rounded-sm border-blue-400"
                >
                    <option style="display:none" />
                    {#each subcategoriesList as { name, id }}
                        <option value={id}>{name}</option>
                    {/each}
                </select>
            </div>

            <div
                class="mt-3 p-2 rounded-md border border-dashed border-blue-400"
            >
                <h1 class="font-semibold text-blue-400">
                    *Add New SubCategory
                </h1>

                <form
                    class="flex mt-2 items-center gap-2"
                    action=""
                    on:submit={(e) => {
                        e.preventDefault();
                        addNewSubCategory(e);
                        e.target.reset();
                    }}
                >
                    <input
                        class="border p-1 border-gray-400 rounded-md "
                        name="subcategory"
                        disabled={UploadTo.categoriesId == null}
                    />

                    <button
                        disabled={UploadTo.categoriesId == null}
                        class="border  border-green-600 rounded-md bg-green-600"
                        type="submit"><Add size={24} /></button
                    >
                </form>
            </div>
        </div>
    </div>
</div>

<button
    class="mt-10 p-2 rounded-md border-2 border-green-600 text-green-600 text-lg font-semibold hover:bg-green-600 hover:text-white"
    on:click={UploadData}
>
    Upload</button
>

{#if loading}
    <div
        class="fixed z-50 top-0 left-0 h-screen w-screen flex justify-center items-center"
        style="background-color: rgba(0, 0, 0, 0.5);"
    >
        <div
            transition:fly|local={{ y: 400, duration: 400 }}
            class="w-[300px] rounded-md flex justify-center items-center gap-2 bg-white h-[200px]"
        >
            <img class="h-10" src="/loading.svg" alt="" />
            <h1 class="text-2xl font-semibold">Uploading...</h1>
        </div>
    </div>
{/if}
