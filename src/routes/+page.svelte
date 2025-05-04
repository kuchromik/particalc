<script>
    import Partituren from "$lib/Partituren.svelte";
    import Orchesterstimmen from "$lib/Orchesterstimmen.svelte";

    let calctTypes = [
        { name: "Auswahl", value: "Auswahl" },
        { name: "Partituren", value: "Partituren" },
        { name: "Orchesterstimmen", value: "Orchesterstimmen" }
    ];
   let selectedType = calctTypes[0].value;
</script>

<main>
    <h1>Kalkulationstool</h1>
    <label for="calculationType">Was möchten Sie kalkulieren?</label>
    <select class="inputCalculationsType" id="calculationType" bind:value={selectedType} on:change={() => selectedType = event.target.value}>
        {#each calctTypes as type}
            <option value={type.value}>{type.name}</option>
        {/each}
    </select>
    {#if selectedType === "Partituren"}
        <Partituren />
    {:else if selectedType === "Orchesterstimmen"}
        <Orchesterstimmen />
    {/if}
</main>
<footer>
    <a href="https://www.chromikoffsetdruck.de" target="_blank" rel="noopener noreferrer">weitere Informationen zu Chromik Offsetdruck</a>
</footer>

<style>
    /* Existing styles */
    @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap');
    @import url('https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@400&display=swap');

    :global(body) {
        font-family: 'Roboto', sans-serif;
        background-color: #f9f9f9;
        color: #333;
        margin: 0;
        padding: 0;
        padding-bottom: 50px; /* Add padding to body to prevent content overlap */
    }
    main {
        display: flex;
        flex-direction: column;
        padding: 2em;
        background-color: #f0f4f8;
        max-width: 800px;
        margin: 2em auto;
        padding: 1.5em;
        background-color: #fff;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }
    h1 {
        font-size: 1.8rem;
        color: #2c3e50;
        margin-bottom: 1rem;
    }

    h2 {
        font-size: 1.5rem;
        color: #34495e;
        margin-bottom: 0.5rem;
    }

    p {
        font-size: 1rem;
        line-height: 1.5;
        margin: 0.5rem 0;
    }

    label {
        display: block;
        margin: 0.8em 0;
        font-size: 1rem;
        color: #555;
    }
    .inputCalculationsType {
        width: 200px;
        padding: 0.5em;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 1rem;
        margin: 0.5em;
    }

    /* Footer styles */
    footer {
        position: fixed;
        left: 0;
        bottom: 0;
        width: 100%;
        background-color: #34495e; /* Dark blue-grey */
        color: white;
        text-align: center;
        padding: 10px 0;
        font-size: 0.9rem;
        box-shadow: 0 -2px 5px rgba(0,0,0,0.1);
        z-index: 1000; /* Ensure footer is above other content */
    }

    footer a {
        color: #ecf0f1; /* Light grey */
        text-decoration: none;
        transition: color 0.3s ease;
    }

    footer a:hover {
        color: #bdc3c7; /* Slightly darker grey on hover */
        text-decoration: underline;
    }
</style>
