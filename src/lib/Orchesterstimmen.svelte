<script>

let maxPages = $state(0);
let countOfmaxPages = $state(0);
let maxPagesIn = $state(false);
let maxPagesFalse = $state(false);
let pagesArray = $state([]);
let arraySum = $state(0);

function copyToClipboard() {
        const summaryBox = document.querySelector('.summaryBox');
        const textToCopy = summaryBox.innerText;
        navigator.clipboard.writeText(textToCopy).then(() => {
            alert('Text wurde in die Zwischenablage kopiert!');
        }).catch(err => {
            console.error('Fehler beim Kopieren in die Zwischenablage:', err);
        });
    }

function resetAll() {
    maxPages = 0;
    countOfmaxPages = 0;
    maxPagesIn = false;
    maxPagesFalse = false;
    pagesArray = [];
    arraySum = 0;
    }
</script>

<h2>Orchesterstimmen</h2>
<p>Der Druck erfolgt in s/w auf beschreibbarem Offsetpapier weiß 120 g/m² und die Bindung ab 8 Seiten als 2-Klammer-Rückstichheftung.</p>
<p>Bitte beachten Sie, dass bei Rückstichheftungen die Seitenzahl ohne Rest durch vier teilbar sein muss, also 8, 12, 16, 20 ...</p>
<p>Umfänge von drei oder vier Seiten werden als Faltblatt geliefert und ein- oder zwei Seiten als Einzelblatt.</p>
{#if !maxPagesIn}
<label>
    Bitte geben Sie hier die Seitenzahl der Orchesterstimmen mit den meisten Seiten ein:
    <input class="inputPages" type="number" bind:value={maxPages}/>
    <button onclick={() => {
        if (maxPages % 4 === 0) {
            maxPagesIn = true;
            maxPagesFalse = false;
            for (let i = 0, pages = maxPages; pages >= 4; i++, pages -= 4) {
                pagesArray.push({ index: i, pagenumber: pages, count: 0, countSet: false, pagesSum: 0 });
            }
            pagesArray.push({ index: pagesArray.length, pagenumber: 2, count: 0, countSet: false, pagesSum: 0 });
            pagesArray.push({ index: (pagesArray.length)+1, pagenumber: 1, count: 0, countSet: false, pagesSum: 0 });
        } else {
            maxPagesFalse = true;
        }
    }}>OK</button>
</label>
<p class="error" style:display={maxPagesFalse ? 'block' : 'none'}>Die Seitenzahl muss ohne Rest durch 4 teilbar sein!</p>
{/if}


{#if maxPagesIn}
{#each pagesArray as page}
    <label>
        Bitte geben Sie hier die Anzahl der Orchesterstimmen mit {page.pagenumber} Seiten ein:
        <input class="inputPages" type="number" bind:value={page.count}/>
        <button onclick={() => {
            page.countSet = true;
            if (page.pagenumber >= 4) {
                page.pagesSum = (((page.pagenumber / 4) * .30) + 1) * page.count + 1;
            }
            else if (page.pagenumber === 2) {
                page.pagesSum = page.count * .20;
            } else if (page.pagenumber === 1) {
                page.pagesSum = page.count * .15;
            }
            arraySum += page.pagesSum;
            }}>OK
        </button>
    </label>
{/each}

    <div class="summaryBox">
        <b>Zusammenfassung für einen Auftrag via e-Mail an kai.chromik@online.de</b>
        {#each pagesArray as page}
            {#if page.countSet}
                <p>Seitenanzahl: {page.pagenumber} - Anzahl: {page.count} - Kosten: {page.pagesSum.toFixed(2)} € zzgl. 7% MwSt.</p>
            {/if}
        {/each}
        <b>Die Gesamtkosten der Orchesterstimmen betragen {arraySum.toFixed(2)} € zzgl. 7% MwSt.</b>
        <p>Die Daten füge ich der e-Mail als Anhang hinzu.</p>
    </div>
    <button onclick={() => resetAll()}>Eingaben löschen</button>
    <button onclick={() => copyToClipboard()}>Die Zusammenfassung in die Zwischenablage kopieren</button>
{/if}



<style>
    .inputPages {
        width: 3em;
        padding: 0.5em;
        margin: .3em 0;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 1rem;
    }
    .error {
        color: red;
        font-size: 0.9rem;
    }
   

    input[type="number"],
    input[type="checkbox"] {
        margin-left: 0.5rem;
    }

    button {
        background-color: #3498db;
        color: white;
        border: none;
        padding: 0.5em 1em;
        margin: .3em 0em;
        border-radius: 4px;
        cursor: pointer;
        font-size: 1rem;
        transition: background-color 0.3s ease;
    }

    button:hover {
        background-color: #2980b9;
    }
    .summaryBox {
        background-color: #ddd;
        padding: 1em;
        border-radius: 8px;
        margin-top: 1rem;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }
    label {
    display: block;
    background-color: #d6ebe7;
    margin: .3em 0;
    padding: .3em;
    border-radius: 8px;
    font-size: 1rem;
    color: #555;
}
</style>