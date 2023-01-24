<script>
    import { authenticationStatus, userData } from "../store";
    import { goto } from "@roxi/routify";
</script>

{#if $authenticationStatus == "SIGNED_IN" && $userData.defaultRole == "admin"}
    <slot />
{:else}
    {$goto("/")}
{/if}
