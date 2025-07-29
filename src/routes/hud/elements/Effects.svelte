<script lang="ts">
    import {listen} from "../../../integration/ws";
    import type {ClientPlayerDataEvent} from "../../../integration/events";
    import type {StatusEffect} from "../../../integration/types";
    import { REST_BASE } from "../../../integration/host";

    let effects: StatusEffect[] = [];

    listen("clientPlayerData", (event: ClientPlayerDataEvent) => {
        effects = event.playerData.effects;
    });

    function formatTime(duration: number): string {
        return new Date(((duration / 20) | 0) * 1000).toISOString().substring(14, 19);
    }

    function convertToRoman(n: number): string {
        switch (n) {
            case 0: return "I";
            case 1: return "II";
            case 2: return "III";
            case 3: return "IV";
            case 4: return "V";
            case 5: return "VI";
            case 6: return "VII";
            case 7: return "VIII";
            case 8: return "IX";
            case 9: return "X";
            default: return "X+";
        }
    }

</script>

<div class="effects">
    <div class="effects-header">
        <img class="header-icon" src=img/hud/potions.svg alt=potions/>
        <span class="header-text">Potions</span>
    </div>
    <div class="effect-list">
        {#each effects as e}
            <div class="effect">
                <span class="name">{e.localizedName} {convertToRoman(e.amplifier)}:</span>
                <span class="duration">{formatTime(e.duration)}</span>
            </div>
        {/each}
    </div>
</div>

<style lang="scss">
    .effects {
        font-family: "Montserrat"
    }

    .header-icon {
        width: 20px;
        height: 20px;
    }

    .effects-header {
        width: 160px;
        padding: 5px;
        background-color: rgba(68, 68, 68, 0.705);
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        display: flex;
        align-items: center;
        gap: 7px;
    }

    .header-text {
        color: white;
    }

    .effect-list {
        background-color: rgba(34, 34, 34, 0.705);
        border-bottom-left-radius: 4px;
        border-bottom-right-radius: 4px;
        width: 160px;
        display: flex;
        flex-direction: column;
        font-size: 12px;
        gap: 8px; 
        padding: 5px;
    }

    .effect {
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .name {
        color: white;
    }

    .duration {
        color: white;
    }
</style>
