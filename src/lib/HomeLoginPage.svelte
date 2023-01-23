<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
</script>

<div class=" h-screen flex justify-center items-center w-screen">
    <div class="mx-7  w-full max-w-[1000px]">
        <div>
            <h1
                class="md:text-5xl text-4xl mb-6 Headline text-center text-slate-800  font-semibold"
            >
                Correct Way
            </h1>
        </div>

        <div class="md:flex">
            <div class="md:flex-1 flex justify-center">
                <img
                    class=" w-[300px] md:w-full"
                    src="/QuestionsLogin.svg"
                    alt=""
                />
            </div>

            <div
                class="flex flex-col  items-center md:justify-evenly md:flex-1"
            >
                <div class="w-full max-w-[400px] mb-11">
                    <h1 class="text-center font-bold text-3xl text-slate-800">
                        Lorem ipsum dolor
                    </h1>
                    <p class="text-slate-500 text-center">
                        Lorem ipsum dolor sit amet consectetur adipisicing elit.
                        Cumque facilis dolor tempora cum hic eius ipsa eveniet
                        aliquam, nesciunt,
                    </p>
                </div>
                <div class="flex justify-center gap-2">
                    <button
                        class=" text-green-500 text-xl border-2 bg-white border-green-500 rounded-xl  h-12 w-40 font-semibold active:bg-green-500 active:text-white"
                        on:click={() => {
                            dispatch("Login");
                        }}>Log In</button
                    >
                    <button
                        class=" text-yellow-500 text-xl border-2 bg-white border-yellow-500 rounded-xl  h-12 w-40 font-semibold active:bg-yellow-500 active:text-white"
                        on:click={() => {
                            dispatch("Signup");
                        }}>Sign Up</button
                    >
                </div>
            </div>
        </div>
    </div>
</div>

<style>
    @import url("https://fonts.googleapis.com/css2?family=Arvo&display=swap");
    .Headline {
        font-family: "Arvo", serif;
    }
</style>
