<script lang="ts">
    export let title: string;
    export let message: string;
</script>

<div class="notification">
    <div class="title">{title}:</div>
    <div class="message">{message}</div>
</div>

<style lang="scss">
  .notification {
    display: flex;
    flex-direction: row;
    column-gap: 10px;
    align-items: center;
    background: rgba(0, 0, 0, 0.68);
    border-radius: 5px;
    overflow: hidden;
    padding: 10px;
    margin-bottom: 10px;
    font-family: "Montserrat";
  }

  .title {
    font-size: 14px;
    color: white;
    font-weight: 600;
  }

  .message {
    font-size: 14px;
    color: #cbd1e3;
  }
</style>
