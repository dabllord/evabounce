<script lang="ts">
    import {onMount, tick} from "svelte";
    import type {Module} from "../../../integration/types";
    import {getModules} from "../../../integration/rest";
    import {listen} from "../../../integration/ws";
    import {getTextWidth} from "../../../integration/text_measurement";
    import {flip} from "svelte/animate";
    import {fly} from "svelte/transition";
    import {convertToSpacedString, spaceSeperatedNames} from "../../../theme/theme_config";

    let enabledModules: Module[] = [];

    async function updateEnabledModules() {
        const modules = await getModules();
        enabledModules = modules.filter(m => m.enabled && !m.hidden && m.keyBind.boundKey!="key.keyboard.unknown");
        await tick();
    }

    function extractKeybind(translationKey: string) {
      const parts = translationKey.split('.');
      return parts[parts.length - 1];
    }

    spaceSeperatedNames.subscribe(async () => {
        await updateEnabledModules();
    });

    onMount(async () => {
        await updateEnabledModules();
    });

    listen("moduleToggle", async () => {
        await updateEnabledModules();
    });

    listen("refreshArrayList", async () => {
        await updateEnabledModules();
    });
</script>

<div class="arraylist">
    <div class="arraylist-header">
        <img class="header-icon" src=img/hud/keybinds.svg alt=potions/>
        <span class="header-text">Keybinds</span>
    </div>
    <div class="arraylist-list">
        {#each enabledModules as m}
            <div class="module">
                <span class="name">{m.name}</span>
                <span class="keybind">{extractKeybind(m.keyBind.boundKey).toUpperCase()}</span>
            </div>
        {/each}
    </div>
</div>

<style lang="scss">
  .arraylist {
    font-family: "Montserrat";
  }

  .header-icon {
    width: 20px;
    height: 20px;
  }

  .header-text {
    color: white;
  }
  .arraylist-header {
    width: 160px;
    padding: 5px;
    background-color: rgba(68, 68, 68, 0.705);
    border-top-left-radius: 4px;
    border-top-right-radius: 4px;
    display: flex;
    align-items: center;
    gap: 7px;
  }

  .arraylist-list {
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

  .module {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }


  .name {
    color: white;
  }

  .keybind {
    color: white;
  }
</style>