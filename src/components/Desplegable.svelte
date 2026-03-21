<script>
    import { fade } from "svelte/transition";

    export let options = [];
    export let value = "";
    export let placeholder = "Seleccionar";
    export let formatCount = null;

    let dropdownOpen = false;

    import { onMount } from "svelte";

    onMount(() => {
        const handleClickOutside = (event) => {
            if (!event.target.closest(".select-wrapper")) {
                dropdownOpen = false;
            }
        };
        document.addEventListener("click", handleClickOutside);
        return () => document.removeEventListener("click", handleClickOutside);
    });
</script>

<div class="select-wrapper" class:open={dropdownOpen}>
    <button
        class="select-trigger"
        on:click={() => (dropdownOpen = !dropdownOpen)}
    >
        <span class={value ? "selected" : "placeholder"}>
            {value || placeholder}
        </span>
        <div class="trigger-right">
            <span class="separator" />
            <svg class="chevron" viewBox="0 0 10 6" width="10" height="6">
                <path
                    d="M1 1l4 4 4-4"
                    stroke="currentColor"
                    stroke-width="1.5"
                    fill="none"
                    stroke-linecap="round"
                />
            </svg>
        </div>
    </button>

    {#if dropdownOpen}
        <ul class="dropdown" transition:fade={{ duration: 150 }}>
            <li
                on:click={() => {
                    value = "";
                    dropdownOpen = false;
                }}
            >
                <span class="placeholder">Todos los países</span>
            </li>
            {#each options as option}
                <li
                    on:click={() => {
                        value = option.label;
                        dropdownOpen = false;
                    }}
                >
                    {option.label}
                    {#if option.count !== undefined}
                        <span class="count"
                            >({formatCount
                                ? formatCount(option.count)
                                : option.count})</span
                        >
                    {/if}
                </li>
            {/each}
        </ul>
    {/if}
</div>

<style>
    .select-wrapper {
        position: relative;
        margin: 0 auto;
        width: 100%;
        max-width: 300px;
    }

    .select-trigger {
        width: 100%;
        display: flex;
        transition: 100ms;
        justify-content: space-between;
        align-items: center;
        padding: 6px 14px;
        border: 1px solid #d0d0d0;
        border-radius: 4px;
        background-color: rgb(255, 255, 255);
        color: #3d3935;
        text-align: left;
    }

    .select-trigger:hover {
        border-color: #aaa;
    }

    .select-trigger:focus {
        outline: none;
    }

    .select-wrapper.open .select-trigger {
        outline: 2px solid #2684ff;
        outline-offset: -1px;
    }

    .trigger-right {
        display: flex;
        align-items: center;
        gap: 10px;
        flex-shrink: 0;
    }

    .separator {
        width: 1px;
        height: 18px;
        background: #d0d0d0;
    }

    .chevron {
        transition: transform 200ms ease;
        color: #888;
    }

    .select-wrapper.open .chevron {
        transform: rotate(180deg);
    }

    .placeholder {
        color: #aaa;
    }

    .selected {
        color: #3d3935;
    }

    .dropdown {
        position: absolute;
        top: 100%;
        left: 0;
        right: 0;
        margin: 6px 0 0 0;
        background: white;
        border: 1px solid #dadada;
        border-radius: 6px;
        max-height: 220px;
        overflow-y: auto;
        list-style: none;
        padding: 0;
        z-index: 100;
    }

    .dropdown li {
        padding: 9px 14px;
        color: #3d3935;
        user-select: none;
    }

    .dropdown li:hover {
        background: #deebff;
    }

    .count {
        color: #aaa;
        font-size: 0.75rem;
    }
</style>
