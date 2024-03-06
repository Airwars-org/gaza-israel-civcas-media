<script>
    export let data;
    import { searchQuery, blurAmount } from "@stores";

    function toggleBlur(event) {
        const img = event.target;
        img.classList.toggle("unblurred");
    }

    let rangeSnippet = 50;

    function generateSnippets(text, token) {
        let snippets = [];
        let index = 0;

        while (index !== -1) {
            let startIndex = text
                .toLowerCase()
                .indexOf(token.toLowerCase(), index);

            if (startIndex === -1) {
                break;
            }

            let endIndex = startIndex + token.length;
            let snippet = "";

            for (
                let i = Math.max(0, startIndex - rangeSnippet);
                i < Math.min(text.length, endIndex + rangeSnippet);
                i++
            ) {
                snippet += text[i];
            }

            let finalSnippet = snippet.split(" ").slice(1, -1).join(" ");
            snippets.push(finalSnippet);

            index = endIndex;
        }

        return snippets;
    }

    const stripHtmlTags = (html) => {
        const doc = new DOMParser().parseFromString(html, "text/html");
        return doc.body.textContent || "";
    };

    $: snippets =
        $searchQuery.length >= 4
            ? generateSnippets(
                  stripHtmlTags(data.content),
                  $searchQuery.toLowerCase(),
              )
            : [];
</script>

<section>
    {#if snippets.length > 0}
        <div class="snippet">
            {#each snippets as snippet}
                <div>
                    ...{@html snippet.replace(
                        $searchQuery.toLowerCase(),
                        `<strong>${$searchQuery}</strong>`,
                    )}...
                </div>
            {/each}
        </div>
    {/if}

    <div class="medias">
        {#each data.media as m}
            <div class="media">
                <img
                    src={m.medium}
                    alt={m.title}
                    on:click={toggleBlur}
                    on:keydown={toggleBlur}
                    class:unblurred={$blurAmount === 0 && unblurred}
                    style={`filter: blur(${$blurAmount}px) grayscale(100%);`}
                    loading="lazy"
                />
            </div>
        {/each}
    </div>

    <div class="info">
        <p>
            {data.title.replace("&#8211;", "")}
            <a href={data.link} target="_blank">Source ↗</a>
        </p>
    </div>
</section>

<style>
    section {
        padding: 5px;
        border-bottom: 1px dashed;
    }

    .snippet {
        padding: 10px 0;
        font-style: italic;
    }

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
        border: 1px dashed rgb(145, 145, 145);
    }

    .info {
        color: rgb(145, 145, 145);
        padding: 10px 0;
    }

    .unblurred {
        color: rgb(145, 145, 145);
        filter: blur(0) grayscale(100%) !important;
    }
</style>
