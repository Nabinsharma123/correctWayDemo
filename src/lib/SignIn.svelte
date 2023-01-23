<script>
    import Close from "carbon-icons-svelte/lib/Close.svelte";
    import { fly } from "svelte/transition";
    import { createEventDispatcher } from "svelte";
    import nhost from "../pages/nhostConfig";
    import { emailVerification, notification } from "../pages/store";
    import { clickOutside } from "./click_outside";
    const dispatch = createEventDispatcher();

    var isLoading;

    var signInData = {
        email: "",
        password: "",
    };

    function SignInUser() {
        isLoading = true;

        nhost.auth
            .signIn({
                email: signInData.email,
                password: signInData.password,
            })
            .then((e) => {
                isLoading = false;
                if (e.error) {
                    if (e.error.error == "unverified-user") {
                        nhost.auth.sendVerificationEmail({
                            email: signInData.email,
                        });

                        $emailVerification = { email: signInData.email };
                        $notification = {
                            text: e.error.message,
                            color: "#FBDF07",
                        };
                        dispatch("SignInClose");
                    } else {
                        $notification = {
                            text: e.error.message,
                            color: "#FF6465",
                        };
                    }
                } else {
                    $notification = {
                        text: "Successfully Sign In",
                        color: "#17c21d",
                    };
                    dispatch("SignInClose");
                }
            });
    }
</script>

<div
    class="SignIn fixed flex items-end w-full top-0 left-0 z-40  h-screen"
    style="background-color: rgba(0, 0, 0, 0.5)"
>
    <div
        class="SignInMainDiv w-full overflow-auto max-h-[100vh] h-[33rem] rounded-t-xl p-5 bg-white"
        use:clickOutside
        on:outclick={() => dispatch("SignInClose")}
        transition:fly|local={{ y: 400, duration: 400 }}
    >
        <div class=" mb-6">
            <h1 class="text-3xl font-bold">Log In</h1>
        </div>

        <form
            on:submit={(e) => {
                e.preventDefault();
                SignInUser();
            }}
        >
            <input
                class="border rounded-t font-semibold  border-gray-300 w-full h-12 p-4 bg-gray-100"
                bind:value={signInData.email}
                type="text"
                placeholder="Email"
                required
            />
            <input
                class="border rounded-b border-t-0 font-semibold border-gray-300 w-full h-12 p-4 bg-gray-100"
                bind:value={signInData.password}
                type="password"
                placeholder="Password"
                minlength="8"
                required
            />

            <div
                class="mt-6 flex justify-between font-semibold text-lg items-center"
            >
                <button
                    class="rounded-2xl h-11 flex justify-center items-center text-white font-semibold px-9 py-2 text-xl bg-green-500"
                    type="submit"
                >
                    {#if isLoading}
                        <img
                            src="/loading.svg"
                            style="top: 0; height: inherit;"
                            alt=""
                        />
                    {:else}
                        Log In
                    {/if}
                </button>

                <button type="button" on:click={() => dispatch("openSignUp")}
                    >or, Sign Up</button
                >
            </div>
        </form>
        <div class="my-5 w-full flex items-center justify-center">
            <hr class="border w-full " />
            <h1 class=" mx-2 text-lg font-bold">Or</h1>
            <hr class="border w-full " />
        </div>
        <div class=" w-full flex justify-center ">
            <button
                class="w-fit p-2 rounded-md"
                style="box-shadow: 0 1px 4px .1px ;"
                on:click={() => {
                    nhost.auth.signIn({
                        provider: "google",
                    });
                }}
            >
                <div class="flex w-fit mx-4 items-center justify-around">
                    <div class="w-8 mr-3">
                        <img
                            src="data:image/png;base64,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"
                            alt=""
                        />
                    </div>
                    <h1 class=" text-md font-semibold">Sign in with Google</h1>
                </div>
            </button>
        </div>
    </div>
</div>

<style>
    @media (min-width: 770px) {
        .SignIn {
            align-items: center;
            justify-content: center;
        }
        .SignInMainDiv {
            width: 600px;
            height: fit-content;
            border-radius: 12px;
        }
    }
</style>
