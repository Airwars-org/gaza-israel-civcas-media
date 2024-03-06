<script>
    import { onMount } from "svelte";
    import Cards from "@components/Cards.svelte";
    import Header from "@components/Header.svelte";

    import { searchQuery } from "@stores";

    const query = "https://airwars.org/wp-json/wp/v2/civ?conflict=93644";
    const pagNr = 25;

    let data = [];
    let posts = [];
    let filteredData = [];

    onMount(async () => {
        posts = await singleQuery(`${query}&per_page=${pagNr}`);

        async function singleQuery(query) {
            let i = 1;
            let datum = [];

            while (true) {
                // testing purposes
                if (i == 8) {
                    break;
                }

                try {
                    const response = await fetch(`${query}&page=${i}`);

                    if (!response.ok) {
                        throw new Error(
                            `HTTP error! Status: ${response.status}`,
                        );
                    }

                    const items = await response.json();

                    if (items.length > 0) {
                        datum.push(...items);
                        i++;
                    } else {
                        break;
                    }
                } catch (error) {
                    console.error("Error fetching data:", error);
                    break;
                }
            }

            return datum
                .map((d) => ({ ...d }))
                .sort((a, b) => b.count - a.count);
        }

        data = await Promise.all(
            posts
                .map(async (d) => {
                    try {
                        const mediaResponse = await fetch(
                            `${d._links["wp:attachment"]?.[0].href}&per_page=${pagNr}`,
                        );

                        if (!mediaResponse.ok) {
                            throw new Error(
                                `HTTP error! Status: ${mediaResponse.status}`,
                            );
                        }

                        const mediaData = await mediaResponse.json();

                        return {
                            id: d.id,
                            title: d.title.rendered,
                            slug: d.slug,
                            content: d.content.rendered,
                            link: d.link,
                            media: mediaData.map((i) => ({
                                url: i.source_url,
                                link: i.link,
                                title: i.title.rendered,
                            })),
                        };
                    } catch (error) {
                        console.error("Error fetching media data:", error);
                        return null; // or handle the error in a way that suits your application
                    }
                })
                .filter(Boolean), // filter out null values from failed media requests
        );

        console.log(data);
    });

    $: filteredData =
        ($searchQuery.length >= 4) & (data.length > 0)
            ? data.filter((item) =>
                  item.content
                      .toLowerCase()
                      .includes($searchQuery.toLowerCase()),
              )
            : data;
</script>

<Header {filteredData} />

{#if posts.length === 0}
    <p>Loading CIVCAS...</p>
{:else if data.length === 0}
    <p>Loading Images...</p>
{:else}
    {#each filteredData as d}
        <Cards data={d} />
    {/each}
{/if}