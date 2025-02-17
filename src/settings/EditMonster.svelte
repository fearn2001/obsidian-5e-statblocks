<script lang="ts">
    import { createEventDispatcher } from "svelte";
    import type { Monster } from "@types";
    import {
        ButtonComponent,
        ExtraButtonComponent,
        Notice,
        parseYaml,
        stringifyYaml
    } from "obsidian";

    const dispatch = createEventDispatcher();

    export let monster: Partial<Monster> = {};
    let useJson = false;
    let textArea: HTMLTextAreaElement;

    const json = (node: HTMLElement) => {
        new ExtraButtonComponent(node).setIcon("code-glyph").setTooltip("JSON");
    };
    const yaml = (node: HTMLElement) => {
        new ExtraButtonComponent(node)
            .setIcon("lines-of-text")
            .setTooltip("YAML");
    };
    const save = (node: HTMLElement) => {
        new ButtonComponent(node)
            .setIcon("checkmark")
            .setTooltip("Save Changes")
            .onClick(() => {
                if (useJson) {
                    try {
                        if (useJson) {
                            monster = JSON.parse(textArea.value);
                        } else {
                            monster = parseYaml(textArea.value);
                        }
                    } catch (e) {
                        console.error(e);
                        new Notice(
                            `There was an error saving the creaturen\n\n${e.message}`
                        );
                        return;
                    }
                }
                dispatch("save", monster);
            });
    };
    const cancel = (node: HTMLElement) => {
        new ExtraButtonComponent(node)
            .setIcon("cross")
            .setTooltip("Cancel")
            .onClick(() => {
                dispatch("cancel");
            });
    };

    function getMonsterText() {
        if (useJson) return JSON.stringify(monster, null, 2);
        if (!monster || !Object.keys(monster ?? {})?.length) return "";
        return stringifyYaml(monster).trim();
    }

    function setMonster() {
        try {
            if (useJson) {
                monster = JSON.parse(textArea.value);
            } else {
                monster = parseYaml(textArea.value);
            }
        } catch (e) {
            console.error(e);
        }
    }
</script>

<div class="edit-monster-modal">
    <h2>Edit Monster</h2>
    <div class="top-level">
        <div class="json">
            <div
                class:active={!useJson}
                use:yaml
                on:click={() => (useJson = false)}
            />
            <div
                class:active={useJson}
                use:json
                on:click={() => (useJson = true)}
            />
        </div>
        {#key useJson}
            <textarea bind:this={textArea} on:blur={() => setMonster()}
                >{getMonsterText()}</textarea
            >
        {/key}
        <!--         {:else}
            <div class="info">
                <div>
                    <label for="name">Name</label>
                    <input id="name" type="text" bind:value={monster.name} />
                </div>
                <div>
                    <label for="source">Source</label>
                    <input
                        id="source"
                        type="text"
                        bind:value={monster.source}
                    />
                </div>
                <div>
                    <label for="type">Type</label>
                    <input id="type" type="text" bind:value={monster.type} />
                </div>
                <div>
                    <label for="size">Size</label>
                    <input id="size" type="text" bind:value={monster.size} />
                </div>
                <div>
                    <label for="alignment">Alignment</label>
                    <input
                        id="alignment"
                        type="text"
                        bind:value={monster.alignment}
                    />
                </div>
            </div>
            <div class="basic-stats">
                <div>
                    <label for="hp">Hit Points</label>
                    <input id="hp" type="text" bind:value={monster.hp} />
                </div>
                <div>
                    <label for="ac">Armor Class</label>
                    <input id="ac" type="text" bind:value={monster.ac} />
                </div>
                <div>
                    <label for="speed">Speed</label>
                    <input id="speed" type="text" bind:value={monster.speed} />
                </div>
                <div>
                    <label for="senses">Senses</label>
                    <input
                        id="senses"
                        type="text"
                        bind:value={monster.senses}
                    />
                </div>
                <div>
                    <label for="languages">Languages</label>
                    <input
                        id="languages"
                        type="text"
                        bind:value={monster.languages}
                    />
                </div>
                <div>
                    <label for="cr">Challenge</label>
                    <input id="cr" type="text" bind:value={monster.cr} />
                </div>
            </div>
            <div class="attributes">
                <div>
                    <label for="str">Str.</label>
                    <input id="str" type="text" bind:value={monster.stats[0]} />
                </div>
                <div>
                    <label for="dex">Dex.</label>
                    <input id="dex" type="text" bind:value={monster.stats[1]} />
                </div>
                <div>
                    <label for="con">Con.</label>
                    <input id="con" type="text" bind:value={monster.stats[2]} />
                </div>
                <div>
                    <label for="int">Int.</label>
                    <input id="int" type="text" bind:value={monster.stats[3]} />
                </div>
                <div>
                    <label for="wis">Wis.</label>
                    <input id="wis" type="text" bind:value={monster.stats[4]} />
                </div>
                <div>
                    <label for="cha">Cha.</label>
                    <input id="cha" type="text" bind:value={monster.stats[5]} />
                </div>
            </div>
            <div class="damages">
                <p>Damage Vulnerabilities</p>
                <p>Damage Resistances</p>
                <p>Damage Immunities</p>
            </div> -->
    </div>
    <div class="buttons">
        <div use:save />
        <div use:cancel />
    </div>
</div>

<style>
    .top-level {
        display: flex;
        flex-flow: column nowrap;
    }
    textarea {
        flex-grow: 1;
        height: 500px;
        max-height: 50vh;
    }
    .json {
        margin-bottom: 1rem;
        display: flex;
        justify-content: flex-start;
        align-items: center;
    }
    .json > div {
        border-radius: 4px;
        margin: 5px 0px;
    }
    .active {
        background-color: var(--background-secondary-alt);
    }

    .buttons {
        margin-top: 1rem;
        display: flex;
        justify-content: flex-end;
        align-items: center;
    }
</style>
