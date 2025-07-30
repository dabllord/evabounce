<script lang="ts">
    import ArmorStatus from "./ArmorStatus.svelte";
    import {listen} from "../../../../integration/ws.js";
    import type {PlayerData} from "../../../../integration/types";
    import {REST_BASE} from "../../../../integration/host";
    import {fly} from "svelte/transition";
    import HealthProgress from "./HealthProgress.svelte";
    import type {TargetChangeEvent} from "../../../../integration/events";
    import { getPlayerData } from "../../../../integration/rest";
    import AbsorptionProgress from "./AbsorptionProgress.svelte";

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
        <div class="metadata">
            <div class="avatar">
                <img src="{REST_BASE}/api/v1/client/resource/skin?uuid={target.uuid}" alt="img/steve.png" />
            </div>
            <div class="metadata-container">
                <span class="name">{target.username}</span>
                <div class="data">
                    <span class="txt-data">Health: {Math.round(target.health)}, Absorption: {Math.round(target.absorption)}, {state}</span>
                </div>
            </div>
        </div>

        <div class="health-container">
            <HealthProgress maxHealth={target.maxHealth} health={target.health}></HealthProgress>
            <AbsorptionProgress maxHealth={target.maxHealth} health={target.absorption}></AbsorptionProgress>
        </div>
    </div>

{/if}

<style lang="scss">
    .armor-stats {
        display: flex;
        flex-direction: row;

    }
    .targethud {
        background-color: rgba(34, 34, 34, 1);
        border-radius: 6px;
        display: flex;
        flex-direction: column;
        width: 250px;
        height: 40px;
        gap: 3px;
        font-family: "Montserrat";
        font-size: 12px;
    }

    .metadata {
        padding: 5px;
        display: flex;
        flex-direction: row;
        gap: 10px;
    }

    .data {
        font-family: "Montserrat";
    }

    .health-container {
        display: grid;
        grid-template-columns: 1fr;
        grid-template-rows: 1fr;
        width: 250px;
        border-radius: 3px;
        background-color: rgba(23, 23, 23, 1);
        padding: 3px;
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
            max-width: 200px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
    }

    .avatar {
        width: 30px;
        height: 30px;
        position: relative;
        image-rendering: pixelated;
        background-image: url("/img/steve.png");
        background-repeat: no-repeat;
        background-size: cover;
        overflow: hidden;
        border-radius: 4px;

        img {
            position: absolute;
            scale: 5;
            left: 118px;
            top: 118px;
        }
    }

</style>
