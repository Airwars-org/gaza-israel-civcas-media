<script>
    import { searchQuery } from "@stores";

    function handleInput(event) {
        searchQuery.set(event.target.value);
    }
</script>

<input type="text" name="search" autocomplete="off" on:input={handleInput} />
