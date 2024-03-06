<script>
    export let data;
    function toggleBlur(event) {
        const img = event.target;
        img.classList.toggle("blurred");
    }
</script>

<section>
    <h1>
        {data.title.replace("&#8211;", "")}
    </h1>
    <a href={data.link} target="_blank">Source ↗</a>

    <div class="medias">
        {#each data.media as m}
            <div class="media">
                <img
                    class="blurred"
                    src={m.url}
                    alt={m.title}
                    on:click={toggleBlur}
                    on:keydown={toggleBlur}
                />
            </div>
        {/each}
    </div>
</section>

<style>
    section {
        padding: 5px;
        border-bottom: 1px dashed;
    }

    h1 {
        font-size: 24px;
        font-weight: 400;
    }

    a {
        font-size: 18px;
    }

    h1,
    p {
        padding: 0;
        margin: 0;
    }

    .medias {
        line-height: 0px;
        /* text-align: center; */
        height: fit-content;
    }

    .media {
        display: inline-flex;
        width: 100px;
        height: 150px;
        margin: 3px;
        object-fit: contain;
        vertical-align: top;
        cursor: pointer;
    }

    img {
        width: 100%;
        height: 100%;
        object-fit: contain;
        filter: grayscale(100%);
        transition: filter 0.3s ease;
    }

    .blurred {
        filter: blur(5px) grayscale(100%);
    }
</style>
