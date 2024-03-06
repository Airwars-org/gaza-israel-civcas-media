<script>
    import { searchQuery } from "@stores";
    export let filteredData;
    export let data;
    import Search from "./Search.svelte";
</script>

<section>
    <div class="intro">
        <p>
            This web page collects all images from Airwars assessments of the
            <a href="https://airwars.org/conflict/israel-and-gaza-2023/"
                >Israeli and Gaza conflict 2023</a
            >. Due to the graphic content, all the images are blurred. By
            clicking on each image it is possible to unobscure them.
        </p>
    </div>
    <div class="search">
        {#if data.length > 0}
            <p>
                The search input allows results to be filtered by the terms
                mentioned in the full reports of each civcas.
            </p>
            <Search />
            <p>
                {filteredData.length} results
            </p>
            {#if $searchQuery.length >= 4}
                <p>
                    Filtering assesments containing the word: "{$searchQuery}"
                </p>
            {/if}
        {/if}
    </div>
</section>

<style>
    section {
        border-bottom: 1px solid;
        /* min-heights: 300px; */
        margin-bottom: 10px;
    }

    .intro {
        font-size: 24px;
        max-width: 640px;
    }

    .search {
        color: rgb(145, 145, 145);
    }
</style>
