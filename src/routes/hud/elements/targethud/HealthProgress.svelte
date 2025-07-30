<script lang="ts">
    export let maxHealth: number;
    export let health: number;

    let width = Math.ceil(health / maxHealth * 100);
    $: width = Math.ceil(health / maxHealth * 100);
</script>

<div class="health-progress">
    <div class="thumb" style="width: {width}%;"></div>
</div>

<style lang="scss">
    @use "../../../../colors.scss" as *;

    .health-progress {
        grid-column: 1 / 2;
        grid-row: 1 / 2;
    }

    .thumb {
        border-radius: 2px;
        height: 8px;
        background-color: rgb(61, 161, 207);
        transition: ease width 0.5s;
        z-index: 1;
    }
</style>