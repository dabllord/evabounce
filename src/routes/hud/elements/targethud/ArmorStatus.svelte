<script lang="ts">
    import {REST_BASE} from "../../../../integration/host";
    import type {ItemStack} from "../../../../integration/types";

    export let itemStack: ItemStack;

    let damage = Math.ceil(10 - (itemStack.damage / itemStack.maxDamage * 10));
    $: damage = Math.ceil(10 - (itemStack.damage / itemStack.maxDamage * 10));
</script>

<div class="armor-status">
    <img class="icon" src="{REST_BASE}/api/v1/client/resource/itemTexture?id={itemStack.identifier}" alt={itemStack.identifier} />
</div>

<style lang="scss">
    @use "../../../../colors.scss" as *;

    .armor-status {
        display: flex;
        align-items: center;
        column-gap: 5px;
    }

    .icon {
        height: 16px;
        width: 16px;
        image-rendering: pixelated;
    }
</style>
