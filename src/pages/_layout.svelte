<script>
    import TaskBar from "../lib/TaskBar.svelte";
    import Navbar from "../lib/Navbar.svelte";
    import HomeLoginPage from "../lib/HomeLoginPage.svelte";
    import SignIn from "../lib/SignIn.svelte";
    import SignUp from "../lib/SignUp.svelte";
    import nhost from "./nhostConfig";
    import { gql } from "@apollo/client/core";
    import EmailVerification from "../lib/EmailVerification.svelte";
    import Notification from "../lib/Notification.svelte";
    import {
        CategoryCard,
        authenticationStatus,
        emailVerification,
        notification,
        userData,
    } from "./store";
    import { fade } from "svelte/transition";
    import { onMount } from "svelte";
    import { afterPageLoad } from "@roxi/routify";
    import { beforeUrlChange } from "@roxi/routify";
    var isLoading = false;
    var isSignUpclick = false;
    var isSignInclick = false;
    $afterPageLoad(() => {
        isLoading = false;
    });
    $beforeUrlChange(() => {
        isLoading = true;
        return true;
    });

    onMount(() => {
        isLoading = true;
        const query = gql`
            query {
                categories(order_by: { id: asc }) {
                    color
                    id
                    name
                    imageId
                }
            }
        `;

        nhost.auth.onAuthStateChanged((event, session) => {
            $authenticationStatus = event;
            if (event == "SIGNED_IN") {
                $userData = session.user;
                if ($userData.avatarUrl.slice(-5) == "blank") {
                    $userData.avatarUrl = `https://avatars.dicebear.com/api/micah/${$userData.displayName}.svg`;
                }
                console.log($userData);

                nhost.graphql.request(query).then((data) => {
                    $CategoryCard = data.data.categories;
                });
            }

            console.log(
                `The auth state has changed. State is now ${event} with session: ${session}`
            );
            isLoading = false;
        });
    });
</script>

<svelte:head>
    <link rel="prefetch" href="/loading.svg" as="document" />
</svelte:head>

<div class="main h-screen absolute top-0 left-0 w-screen rounded-xl">
    {#if $authenticationStatus == "SIGNED_IN"}
        <div>
            <Navbar />
            <div
                class="SlotDiv md:flex md:justify-center overflow-auto p-5 pt-20 "
                style="height: 100vh ;"
            >
                <div class="md:w-[768px] lg:w-[1000px]">
                    <slot />
                    <div class="w-full h-20" />
                </div>
            </div>
            <TaskBar />
        </div>
    {:else}
        <HomeLoginPage
            on:Login={() => (isSignInclick = true)}
            on:Signup={() => (isSignUpclick = true)}
        />
    {/if}
    {#if isLoading}
        <div
            class="fixed  flex justify-center items-center left-0 bottom-0 bg-white"
            style="width: 100%;height: 100vh;"
        >
            <img
                transition:fade|local
                class="w-14 -mt-[60px]"
                src="/loading.svg"
                alt=""
            />
        </div>
    {/if}

    {#if $emailVerification}
        <EmailVerification
            email={$emailVerification.email}
            on:close={() => ($emailVerification = false)}
        />
    {/if}
    {#if $notification}
        <Notification
            color={$notification.color}
            text={$notification.text}
            on:close={() => ($notification = false)}
        />
    {/if}

    {#if isSignUpclick}
        <SignUp
            on:SignUpClose={() => (isSignUpclick = false)}
            on:openSignIn={() => {
                isSignInclick = true;
                isSignUpclick = false;
            }}
        />
    {/if}
    {#if isSignInclick}
        <SignIn
            on:SignInClose={() => (isSignInclick = false)}
            on:openSignUp={() => {
                isSignUpclick = true;
                isSignInclick = false;
            }}
        />
    {/if}
</div>

<style>
</style>
