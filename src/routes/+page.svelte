<script>
    import { onMount } from "svelte";
    import Cards from "@components/Cards.svelte";
    import Header from "@components/Header.svelte";

    import { searchQuery } from "@stores";

    const query =
        "https://airwars.org/wp-json/wp/v2/civ?country=767&after=2023-10-06T00:00:00";
    const pagNr = 25;

    let data = [];
    let loadingPosts = "";

    onMount(async () => {
        data = await fetchPosts(`${query}&per_page=${pagNr}`);
        await fetchImages();
    });

    async function fetchPosts(query) {
        let i = 1;
        let postsData = [];

        while (true) {
            loadingPosts = `${i * pagNr}`;

            try {
                const response = await fetch(`${query}&page=${i}`);

                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }

                const items = await response.json();

                if (items.length > 0) {
                    postsData.push(...items);
                    i++;
                } else {
                    break;
                }
            } catch (error) {
                break;
            }
        }

        return postsData
            .map((d) => ({
                id: d.id,
                link: d.link,
                title: d.title.rendered,
                content: d.content.rendered,
                _links: d._links["wp:attachment"]?.[0].href,
            }))
            .sort((a, b) => b.count - a.count);
    }

    // async function fetchImages() {
    //     if (data.length > 0) {
    //         data = await Promise.all(
    //             data
    //                 .map(async (d, i) => {
    //                     try {
    //                         const mediaResponse = await fetch(
    //                             `${d._links}&per_page=${pagNr}`,
    //                         );

    //                         if (!mediaResponse.ok) {
    //                             throw new Error(
    //                                 `HTTP error! Status: ${mediaResponse.status}`,
    //                             );
    //                         }

    //                         const mediaData = await mediaResponse.json();

    //                         return {
    //                             id: d.id,
    //                             title: d.title,
    //                             content: d.content,
    //                             link: d.link,
    //                             media: mediaData.map((i) => ({
    //                                 low: i.media_details.sizes?.thumbnail
    //                                     ?.source_url,
    //                                 medium: i.media_details.sizes?.medium
    //                                     ?.source_url,
    //                                 title: d.title,
    //                             })),
    //                         };
    //                     } catch (error) {
    //                         return null;
    //                     }
    //                 })
    //                 .filter(Boolean),
    //         );
    //     }
    // }
    async function fetchImages() {
        if (data.length > 0) {
            for (let i = 0; i < data.length; i++) {
                const d = data[i];
                try {
                    const mediaResponse = await fetch(
                        `${d._links}&per_page=${pagNr}`,
                    );

                    if (!mediaResponse.ok) {
                        throw new Error(
                            `HTTP error! Status: ${mediaResponse.status}`,
                        );
                    }

                    const mediaData = await mediaResponse.json();

                    data[i] = {
                        id: d.id,
                        title: d.title,
                        content: d.content,
                        link: d.link,
                        media: mediaData.map((i) => ({
                            low: i.media_details.sizes?.thumbnail?.source_url,
                            medium: i.media_details.sizes?.medium?.source_url,
                            title: d.title,
                        })),
                    };
                } catch (error) {
                    data[i] = null;
                }
            }

            data = data.filter(Boolean);
        }
    }

    $: filteredPosts =
        ($searchQuery.length >= 4) & (data.length > 0)
            ? data.filter((post) =>
                  post.content
                      .toLowerCase()
                      .includes($searchQuery.toLowerCase()),
              )
            : data;

</script>

<Header {filteredPosts} {data} />

{#if data.length === 0}
    <p>Loading {loadingPosts} CIVCAS...</p>
{:else}
    {#each filteredPosts as post}
        <Cards data={post} />
    {/each}
{/if}

<style>
    p {
        padding: 10px;
    }
</style>
