<script lang="ts">
  import Close from "./svg/Close.svelte";
  import Maximize from "./svg/Titlebar/Maximize.svelte";
  import Minimize from "./svg/Titlebar/Minimize.svelte";
  import Restore from "./svg/Titlebar/Restore.svelte";
  const { ipcRenderer } = require("electron");

  let maximized = false;
  let blurred = false;

  const minimize = () => {
    ipcRenderer.send("minimize-window");
  };

  const maximize = () => {
    ipcRenderer.send("maximize-window");
    maximized = true;
  };

  const restore = () => {
    ipcRenderer.send("restore-window");
    maximized = false;
  };

  const close = () => {
    ipcRenderer.send("close-window");
  };

  ipcRenderer.on("window-blurred", () => (blurred = true));
  ipcRenderer.on("window-focused", () => (blurred = false));
</script>

<header id="titlebar" class:blurred class:maximized>
  <div id="drag-region">
    <div id="window-icon">
      <img src="./icon.png" alt="Altus Icon" />
    </div>
    <div id="window-title"><span>Altus</span></div>
    <div id="window-controls">
      <div class="button" id="minimize" on:click={minimize}>
        <Minimize />
      </div>
      {#if maximized}
        <div class="button" id="restore" on:click={restore}>
          <Restore />
        </div>
      {:else}<div class="button" id="maximize" on:click={maximize}>
          <Maximize />
        </div>
      {/if}
      <div class="button" id="close" on:click={close}>
        <Close />
      </div>
    </div>
  </div>
</header>

<style lang="scss">
  #titlebar {
    display: flex;
    position: fixed;
    height: 32px;
    width: calc(100% - 2px);
    background: #191b1f;
    color: #fff;
    fill: #fff;
    padding: 4px;
  }
  #drag-region {
    display: flex;
    width: 100%;
    height: 100%;
    -webkit-app-region: drag;

    .blurred & {
      opacity: 0.75;
    }
  }
  #window-icon {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 1.25em;
    height: 100%;
    margin-left: 4px;

    img {
      width: 100%;
    }
  }
  #window-title {
    display: flex;
    align-items: center;
    margin-left: 8px;
    overflow: hidden;
    font-family: "Segoe UI", "Inter", sans-serif;
    font-size: 14px;

    span {
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
      line-height: 1.5;
    }
  }
  #window-controls {
    display: grid;
    grid-template-columns: repeat(3, 46px);
    position: absolute;
    top: 0;
    right: 0;
    height: 100%;
    -webkit-app-region: no-drag;
  }
  .button {
    grid-row: 1 / span 1;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    user-select: none;

    &:hover {
      background: rgba(255, 255, 255, 0.1);
    }

    &:active {
      background: rgba(255, 255, 255, 0.2);
    }
  }
  #minimize {
    grid-column: 1;
  }
  #maximize,
  #restore {
    grid-column: 2;
  }
  #close {
    grid-column: 3;

    &:hover {
      background: #e81123 !important;
    }
    &:active {
      background: #f1707a !important;
    }
  }
</style>
