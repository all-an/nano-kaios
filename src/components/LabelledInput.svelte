<script lang="ts">
    export let languageId: string | undefined
    export let text: string | undefined
    export let placeholderLanguage: string | undefined
    export let type: string | undefined
    export let value: string = '';
</script>

<div class="kui-input-holder">
    {#if languageId || text}
        <label class="kui-input-label kui-h3" data-l10n-id={languageId}>{text}</label>
    {/if}
    <input type={type} class="kui-input kui-text navigation" data-l10n-id={placeholderLanguage} on:change value={value}>
</div>
