<script lang="ts">
    import ArrayList from "./elements/ArrayList.svelte";
    import TargetHud from "./elements/targethud/TargetHud.svelte";
    import Watermark from "./elements/Watermark.svelte";
    import Notifications from "./elements/notifications/Notifications.svelte";
    import TabGui from "./elements/tabgui/TabGui.svelte";
    import HotBar from "./elements/hotbar/HotBar.svelte";
    import Scoreboard from "./elements/Scoreboard.svelte";
    import {onMount} from "svelte";
    import {getComponents, getGameWindow} from "../../integration/rest";
    import {listen} from "../../integration/ws";
    import type {Component} from "../../integration/types";
    import Taco from "./elements/taco/Taco.svelte";
    import type {ComponentsUpdateEvent, ScaleFactorChangeEvent} from "../../integration/events";
    import Keystrokes from "./elements/keystrokes/Keystrokes.svelte";
    import Effects from "./elements/Effects.svelte";

    let zoom = 100;
    let components: Component[] = [];

    onMount(async () => {
        const gameWindow = await getGameWindow();
        zoom = gameWindow.scaleFactor * 50;

        components = await getComponents();
    });

    listen("scaleFactorChange", (data: ScaleFactorChangeEvent) => {
        zoom = data.scaleFactor * 50;
    });

    listen("componentsUpdate", (data: ComponentsUpdateEvent) => {
        components = data.components;
    });
</script>

<div class="hud" style="zoom: {zoom}%">
    {#each components as c}
        {#if c.settings.enabled}
            <div style="{c.settings.alignment}">
                {#if c.name === "Watermark"}
                    <Watermark/>
                {:else if c.name === "ArrayList"}
                    <ArrayList/>
                {:else if c.name === "TabGui"}
                    <TabGui/>
                {:else if c.name === "Notifications"}
                    <Notifications/>
                {:else if c.name === "TargetHud"}
                    <TargetHud/>
                {:else if c.name === "Hotbar"}
                    <HotBar/>
                {:else if c.name === "Scoreboard"}
                    <Scoreboard/>
                {:else if c.name === "Taco"}
                    <Taco/>
                {:else if c.name === "Keystrokes"}
                    <Keystrokes/>
                {:else if c.name === "Effects"}
                    <Effects />
                {:else if c.name === "Text"}
                    <p>{c.settings.text}</p>
                {:else if c.name === "Image"}
                    <img alt="" src="{c.settings.src}" style="scale: {c.settings.scale};">
                {/if}
            </div>
        {/if}
    {/each}
</div>

<style lang="scss">
  .hud {
    height: 100vh;
    width: 100vw;
  }
</style>
