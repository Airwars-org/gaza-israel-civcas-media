<script>
    import { blurAmount } from "@stores";
    export let filteredPosts;
    export let data;
    import Search from "./Search.svelte";

    let rangeValue = blurAmount;

    function handleBlurChange(event) {
        const newBlurAmount = event.target.value;
        blurAmount.set(newBlurAmount);
    }
</script>

<section>
    <div class="intro">
        <p>
            This web page collects all images from Airwars assessments of the
            <a href="https://airwars.org/conflict/israel-and-gaza-2023/"
                >Israeli and Gaza conflict 2023</a
            >.
            <br />
            Due to the graphic content, all the images are blurred.
            <br />
            By clicking on each image it is possible to unobscure them.
        </p>
    </div>

    {#if data?.length > 0}
        <div class="search">
            <div>
                The search input allows results to be filtered by the terms
                mentioned in the full reports of each civcas.

                <Search results={filteredPosts?.length} />
            </div>
        </div>
    {/if}

    <div class="blur">
        Change the value to adjust the blur.
        <input
            type="range"
            name="blur"
            bind:value={rangeValue}
            min="0"
            max="5"
            on:input={handleBlurChange}
        />
    </div>
</section>

<style>
    section {
        display: flex;
        flex-wrap: wrap;
        gap: 10px;
        border-bottom: 1px solid;
        /* min-heights: 300px; */
        margin-bottom: 10px;
        position: sticky;
        top: 0;
        z-index: 1;
        background-color: white;
        padding: 10px;
    }

    section > * {
        max-width: 400px;
    }

    .intro {
        font-size: 24px;
        max-width: 640px;
    }

    .search,
    .blur {
        color: rgb(145, 145, 145);
    }
</style>
