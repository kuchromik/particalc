<script>
    let scorePagesIn = $state(false);
    let bindType = $state("");
    let scorePages = $state(1);
    let scorePagesPlus = $state(1);
    let scorePagesAdjustedRueckstich = $derived(Math.ceil(scorePagesPlus / 4) * 4);
    let scorePagesAdjustedDrahtkammer = $derived(Math.ceil(scorePagesPlus / 2) * 2);
    let printedCoverDrahtkammer = $state(false);
    let printedBackDrahtkammer = $state(false);
    let printedCoverRueckstich = $state(false);
    
    let paperCost = 0.1;
    let printCost = 0.1;
    let foldCost = 0.1;
    let stitchCost = 2;
    let wireCost = 5;
    let c4ToA4 = 1.1;
    let mwst = 1.07;

    let paperSize = $state('A4');
    let paperSizeChoosen = $state(false);

    function copyToClipboard() {
        const summaryBox = document.querySelector('.summaryBox');
        const textToCopy = summaryBox.innerText;
        navigator.clipboard.writeText(textToCopy).then(() => {
            alert('Text wurde in die Zwischenablage kopiert!');
        }).catch(err => {
            console.error('Fehler beim Kopieren in die Zwischenablage:', err);
        });
    }
</script>


<main>
    <h2>Partituren</h2>
    <p>Der Druck erfolgt in s/w auf beschreibbarem Offsetpapier weiß 120 g/m²</p>
    <p>Bitte wählen Sie das gewünschte Endformat:</p>
    <div class="inputPaperSize">
        <label>
            <input type="radio" bind:group={paperSize} value="A4" checked />
            DIN A4 - 21 cm x 29,7 cm
        </label>
        <label>
            <input type="radio" bind:group={paperSize} value="C4" />
            DIN C4 - 22,9 cm x 32,4 cm
        </label>
    </div>
    <div class="score">
        {#if !scorePagesIn}
            <label class="buttonBeside">
                Seitenzahl der eigentlichen Partitur ohne Deckblatt:
                <input class="inputPages" type="number" bind:value={scorePages}/>
                <button onclick={() => {scorePagesIn = true; scorePagesPlus = scorePages}}>OK</button>
            </label>   
        {:else}
            <div class="buttonBeside">
                <p>Die eigentlichen Notenseitenzahl der Partitur ist {scorePages}.</p>
                <button onclick={() => { 
                    scorePagesIn = false; 
                    scorePages = 1; 
                    scorePagesPlus = 1;
                    bindType = "";
                    printedCoverDrahtkammer = false;
                    printedBackDrahtkammer = false;
                    printedCoverRueckstich = false;
                }}>
                    Neu eingeben
                </button>
            </div>
            {#if scorePages <= 80}
                <p>Für die angegebene Seitenzahl sind folgende Bindearten möglich:</p>
                <div class="choosebinding">
                    <label>
                        <input type="radio" name="binding" value="rueckstich" bind:group={bindType} onchange={() => scorePagesPlus = scorePages}/>
                        Rückstichheftung <a href="https://www.metapaper.io/wiki/rueckstichheftung/detail" target="_blank" rel="noopener noreferrer">Was ist das?</a>
                    </label>
                    <label>
                        <input type="radio" name="binding" value="drahtkammer" bind:group={bindType} onchange={() => scorePagesPlus = scorePages}/>
                        Drahtkammerbindung <a href="https://www.metapaper.io/wiki/wire-o-auch-spiralbindung/detail/" target="_blank" rel="noopener noreferrer">Was ist das?</a>
                    </label>
                </div>

                {#if bindType === "rueckstich"}
                    <label>
                        Hat die Partitur zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes Deckblatt?
                        <input type="checkbox" onchange={(e) => { 
                            scorePagesPlus += e.target.checked ? 2 : -2; 
                            printedCoverRueckstich = e.target.checked; 
                        }} />
                    </label>
                    
                    <div class="summaryBox">
                        <b>Zusammenfassung für einen Auftrag via e-Mail an kai.chromik@online.de</b>
                        <p>Die Gesamtseitenzahl der Partitur ist {scorePagesPlus}.</p>
                        <p>Der Druck erfolgt in s/w auf beschreibbarem Offsetpapier weiß 120 g/m²</p>
                        <p>Die Art der Bindung ist eine beschnittene 2-Klammer-Rückstichheftung.</p>
                        {#if printedCoverRueckstich}
                            <p>Es wird ein zusätzliches ein- oder beidseitig farbig oder s/w-bedrucktes Deckblatt gewünscht.</p>
                        {/if}
                        {#if scorePagesPlus % 4 != 0}
                            <p>Aus technischen Gründen muss die Seitenzahl auf {Math.ceil(scorePagesPlus / 4) * 4} erhöht werden, da die Partitur zu 4 Seiten pro Falzbogen gedruckt wird. Daher fügen wir am Ende {scorePagesAdjustedRueckstich-scorePagesPlus} Leerseiten an.</p>
                        {/if}
                        {#if paperSize === 'A4'}
                            <p>Die Kosten der Partitur betragen {((scorePagesAdjustedRueckstich / 4 * (paperCost + printCost + foldCost)) + stitchCost).toFixed(2)} € zzgl. 7% MwSt. = <b>{(((scorePagesAdjustedRueckstich / 4 * 0.3) + 2) * mwst).toFixed(2)} €</b>.</p>
                        {/if}
                        {#if paperSize === 'C4'}
                        <p>Die Kosten der Partitur betragen {(((scorePagesAdjustedRueckstich / 4 * (paperCost + printCost + foldCost)) + stitchCost) * c4ToA4).toFixed(2)} € zzgl. 7% MwSt. = <b>{(((scorePagesAdjustedRueckstich / 4 * 0.3) + 2) * c4ToA4 * mwst).toFixed(2)} €</b>.</p>
                        {/if}
                        <p>zzgl. ggfs. Versand Deutschland 7.00 Euro</p>
                        <p>Die Daten füge ich der e-Mail als Anhang hinzu.</p>
                    </div>
                    <button onclick={() => copyToClipboard()}>Die Zusammenfassung in die Zwischenablage kopieren</button>

                {:else if bindType === "drahtkammer"}
                    <label>
                        Hat die Partitur zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Deckblatt? Dieses ersetzt das unbedruckte Blankodeckblatt.
                        <input type="checkbox" onchange={(e) => { 
                            scorePagesPlus += e.target.checked ? 2 : -2; 
                            printedCoverDrahtkammer = e.target.checked; 
                        }} />
                    </label>
                    <label>
                        Hat die Partitur zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Schlussblatt? Dieses ersetzt das unbedruckte Blankoschlussblatt.
                        <input type="checkbox" onchange={(e) => { 
                            scorePagesPlus += e.target.checked ? 2 : -2; 
                            printedBackDrahtkammer = e.target.checked; 
                        }} />
                    </label>
                
                    <div class="summaryBox">
                        <b>Zusammenfassung für einen Auftrag via e-Mail an kai.chromik@online.de</b>
                        <p>Die Gesamtseitenzahl der Partitur ist {scorePagesPlus}.</p>
                        <p>Der Druck erfolgt in s/w auf beschreibbarem Offsetpapier weiß 120 g/m²</p>
                        <p>Die Partitur wird mit einer weißen Drahtkammerbindung gebunden. Standardmäßig werden je ein unbedrucktes Deck- und Schlussblatt aus festem weißen Karton ergänzt.</p>
                        {#if printedCoverDrahtkammer}
                            <p>Die Partitur hat zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Deckblatt? Dieses ersetzt das unbedruckte Blankodeckblatt</p>
                        {/if}
                        {#if printedBackDrahtkammer}
                        <p>Die Partitur hat zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Schlussblatt? Dieses ersetzt das unbedruckte Blankoschlussblatt.</p>
                        {/if}
                        {#if scorePagesPlus % 2 == 1}
                        <p>Aus technischen Gründen muss die Seitenzahl auf {Math.ceil(scorePagesPlus / 2) * 2} erhöht werden, da die Partitur zu 2 Seiten pro Blatt gedruckt wird. Ggfs. fügen wir am Ende eine Leerseite an.</p>
                        {/if}
                        {#if paperSize === 'A4'}
                        <p>Die Kosten der Partitur betragen {((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost).toFixed(2)} € zzgl. 7% MwSt. = <b>{(((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost) * mwst).toFixed(2)} €</b>.</p>
                        {/if}
                        {#if paperSize === 'C4'}
                        <p>Die Kosten der Partitur betragen {(((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost) * c4ToA4).toFixed(2)} € zzgl. 7% MwSt. = <b>{((((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost) * c4ToA4) * mwst).toFixed(2)} €</b>.</p>
                        {/if}
                        <p>zzgl. ggfs. Versand Deutschland 7.00 Euro</p>
                        <p>Die Daten füge ich der e-Mail als Anhang hinzu.</p>
                    </div>
                    <button onclick={() => copyToClipboard()}>Die Zusammenfassung in die Zwischenablage kopieren</button>
                {/if}
            {:else if scorePages > 80 && scorePages <= 200}
                <p>Für die angegebene Seitenzahl ist eine Drahtkammerbindung notwendig.</p>
                <label>
                    Hat die Partitur zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Deckblatt? Dieses ersetzt das feste Blankodeckblatt der Drahtkammerbindung.
                    <input type="checkbox" onchange={(e) => { 
                        scorePagesPlus += e.target.checked ? 2 : -2; 
                        printedCoverDrahtkammer = e.target.checked; 
                    }} />
                </label>
                <label>
                    Hat die Partitur zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Schlussblatt? Dieses ersetzt das feste Blankoschlussblatt der Drahtkammerbindung.
                    <input type="checkbox" onchange={(e) => { 
                        scorePagesPlus += e.target.checked ? 2 : -2; 
                        printedBackDrahtkammer = e.target.checked; 
                    }} />
                </label>
                
                <div class="summaryBox">
                    <b>Zusammenfassung für einen Auftrag via e-Mail an kai.chromik@online.de</b>
                    <p>Die Gesamtseitenzahl der Partitur ist {scorePagesPlus}.</p>
                    <p>Der Druck erfolgt in s/w auf beschreibbarem Offsetpapier weiß 120 g/m²</p>
                    <p>Die Partitur wird mit einer weißen Drahtkammerbindung gebunden. Standardmäßig werden je ein unbedrucktes Deck- und Schlussblatt aus festem weißen Karton ergänzt.</p>
                    {#if printedCoverDrahtkammer}
                        <p>Die Partitur hat zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Deckblatt? Dieses ersetzt das unbedruckte Blankodeckblatt</p>
                    {/if}
                    {#if printedBackDrahtkammer}
                    <p>Die Partitur hat zusätzlich ein ein- oder beidseitig farbig oder s/w-bedrucktes festes Schlussblatt? Dieses ersetzt das unbedruckte Blankoschlussblatt.</p>
                    {/if}
                    {#if scorePagesPlus % 2 == 1}
                    <p>Aus technischen Gründen muss die Seitenzahl auf {Math.ceil(scorePagesPlus / 2) * 2} erhöht werden, da die Partitur zu 2 Seiten pro Blatt gedruckt wird. Ggfs. fügen wir am Ende eine Leerseite an.</p>
                    {/if}
                    {#if paperSize === 'A4'}
                    <p>Die Kosten der Partitur betragen {((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost).toFixed(2)} € zzgl. 7% MwSt. = <b>{(((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost) * mwst).toFixed(2)} €</b>.</p>
                    {/if}
                    {#if paperSize === 'C4'}
                    <p>Die Kosten der Partitur betragen {(((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost) * c4ToA4).toFixed(2)} € zzgl. 7% MwSt. = <b>{((((scorePagesAdjustedDrahtkammer / 4 * (paperCost + printCost + foldCost)) + wireCost) * c4ToA4) * mwst).toFixed(2)} €</b>.</p>
                    {/if}
                    <p>zzgl. ggfs. Versand Deutschland 7.00 Euro</p>
                    <p>Die Daten füge ich der e-Mail als Anhang hinzu.</p>
                </div>
                <button onclick={() => copyToClipboard()}>Die Zusammenfassung in die Zwischenablage kopieren</button>
            {:else}
                <p>Bitte wählen Sie eine Seitenzahl von maximal 200.</p>
            {/if}
        {/if}
    </div>
</main>

<style>
    .score {
        display: flex;
        flex-direction: column;
        border: 1px solid #ddd;
        border-radius: 8px;
        padding: 1.5em;
        margin: 1.5em 0;
        background-color: #ffffff;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .buttonBeside {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-top: 1rem;
    }

    .buttonBeside button {
        margin-left: 1em;
    }

    .buttonBeside p {
        margin: 0;
    }

    .choosebinding {
        display: flex;
        flex-direction: row;
        margin-top: 1rem;
        margin-bottom: 1rem;
        gap: 1rem ;
    }
    .choosebinding label {
        margin: 0.5em 0;
    }
    .inputPages {
        width: 50px;
        margin-left: 0.5em;
    }
    .inputPages:focus {
        outline: none;
        border-color: #3498db;
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
        margin: 1em 0em;
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
    margin: 0.3em 0.0em;
    padding: 1em;
    border-radius: 8px;
    font-size: 1rem;
    color: #555;
}

.inputPaperSize {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    padding: 0.5em;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 1rem;
    gap: 0.5em;
}
</style>
