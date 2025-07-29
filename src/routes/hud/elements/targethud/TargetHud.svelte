<script lang="ts">
    import ArmorStatus from "./ArmorStatus.svelte";
    import {listen} from "../../../../integration/ws.js";
    import type {PlayerData} from "../../../../integration/types";
    import {REST_BASE} from "../../../../integration/host";
    import {fly} from "svelte/transition";
    import HealthProgress from "./HealthProgress.svelte";
    import type {TargetChangeEvent} from "../../../../integration/events";
    import { getPlayerData } from "../../../../integration/rest";

    let target: PlayerData | null = null;
    let visible = true;
    let state = "unknown"

    let hideTimeout: number;
    let stateTimeout: number;

    function startHideTimeout() {
        hideTimeout = setTimeout(() => {
            visible = false;
        }, 2000);

        stateTimeout = setTimeout(async () => {
            let data: PlayerData = await getPlayerData();
            if (target) {
                if (data.actualHealth + data.absorption > target.actualHealth + target.absorption) {
                    state = "Win"
                } else {
                    state = "Lose"
                }
            }
        }, 100);
    }

    listen("targetChange", (data: TargetChangeEvent) => {
        target = data.target;
        visible = true;
        clearTimeout(hideTimeout);
        startHideTimeout();
    });

    startHideTimeout();
</script>

{#if visible && target != null}
    <div class="armor-stats">
        {#if target.mainHandStack}
            <ArmorStatus itemStack={target.mainHandStack} />
        {/if}
        {#if target.offHandStack}
            <ArmorStatus itemStack={target.offHandStack} />
        {/if}
        {#if target.armorItems[3].count > 0}
            <ArmorStatus itemStack={target.armorItems[3]} />
        {/if}
        {#if target.armorItems[2].count > 0}
            <ArmorStatus itemStack={target.armorItems[2]} />
        {/if}
        {#if target.armorItems[1].count > 0}
            <ArmorStatus itemStack={target.armorItems[1]} />
        {/if}
        {#if target.armorItems[0].count > 0}
            <ArmorStatus itemStack={target.armorItems[0]} />
        {/if}
    </div>
    <div class="targethud" transition:fly={{ y: -10, duration: 200 }}>
        <div class="avatar">
            <img src="{REST_BASE}/api/v1/client/resource/skin?uuid={target.uuid}" alt="img/steve.png" />
        </div>
        <div class="metadata-container">
            <span class="name">{target.username}</span>
            <div class="data">
                <span class="txt-data">Health: {target.health + target.absorption} | {state}</span>
                <HealthProgress maxHealth={target.maxHealth + target.absorption} health={target.health}></HealthProgress>
            </div>
        </div>
    </div>
{/if}

<style lang="scss">
    .armor-stats {
        display: flex;
        flex-direction: row;

    }
    .targethud {
        background-color: rgba(34, 34, 34, 0.705);
        border-radius: 5px;
        display: flex;
        flex-direction: row;
        padding: 5px;
        width: 200px;
        height: 60px;
        gap: 10px;
        font-family: "Montserrat";
        font-size: 16px;
    }

    .data {
        font-family: "Montserrat";
        width: 125px;
    }

    .txt-data {
        font-size: 10px;
        color: white;
    }

    .metadata-container {
        display: flex;
        flex-direction: column;
        font-family: "Montserrat";

        .name {
            color: white;
        }
    }

    .avatar {
        width: 50px;
        height: 50px;
        position: relative;
        image-rendering: pixelated;
        background-image: url("/img/steve.png");
        background-repeat: no-repeat;
        background-size: cover;
        overflow: hidden;
        border-radius: 4px;

        img {
            position: absolute;
            scale: 6.25;
            left: 118px;
            top: 118px;
        }
    }

</style>
