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
        position: relative;
    }

    .thumb {
        border-radius: 2px;
        height: 8px;
        background-color: rgb(167, 167, 167);
        transition: ease width 0.5s;
    }
</style>