<script>
    import { searchQuery } from "@stores";
    export let results;

    function handleInput(event) {
        searchQuery.set(event.target.value);
    }
</script>

<div>
    <input
        type="text"
        name="search"
        autocomplete="off"
        on:input={handleInput}
    />
    {results} results
</div>
<div>
    {#if $searchQuery.length >= 4}
        Filtering assesments containing the word: "{$searchQuery}"
    {/if}
</div>

<style>
    div {
        margin-top: 10px;
    }
</style>
