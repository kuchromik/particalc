<script>

let voicesArray = $state([]);
let voiceSum = $state(0);
let paperSize = $state('A4');
let paperSizeChoosen = $state(false);

let arraySum = $derived(voicesArray.reduce((sum, voice) => sum + voice.voiceSum, 0));

let basicPrice = 1; // Grundpreis pro Stimme
let paperCost = 0.1;
let printCost = 0.1;
let foldCost = 0.1;
let stitchCost = 1;
let c4ToA4 = 1.1;
let mwst = 1.07;


function copyToClipboard() {
    const summaryBox = document.querySelector('.summaryBox');
    const textToCopy = summaryBox.innerText;
    const message = textToCopy.replace(/Entfernen/g, '');
    if (!navigator.clipboard) {
        alert('Ihr Browser unterstützt das Kopieren in die Zwischenablage nicht. Bitte markieren Sie den Text manuell und kopieren Sie ihn.');
        return;
    }
    navigator.clipboard.writeText(message).then(() => {
        alert('Text wurde in die Zwischenablage kopiert!');
    }).catch(err => {
        console.error('Fehler beim Kopieren in die Zwischenablage:', err);
    });
    }


   
let stimmenName = $state('');
let seitenZahl = $state(0);
let menge = $state(0);

function addVoiceToArray() {
    if (stimmenName && seitenZahl > 0 && menge > 0) {
        if (seitenZahl === 1 || seitenZahl === 2 || seitenZahl === 4 || seitenZahl % 4 === 0) {
            if (seitenZahl === 1 || seitenZahl === 2) {
                voiceSum = (paperCost + printCost) * menge;
            } else if (seitenZahl === 4) {
                voiceSum = (paperCost + printCost + foldCost) * menge;
            } else {
                voiceSum = ((paperCost + printCost + foldCost) * seitenZahl/4 + stitchCost) * menge;
            }
            if (paperSize === 'C4') {
                voiceSum *= c4ToA4;
            }
            voiceSum += basicPrice;
            voicesArray = [
                ...voicesArray,
                { stimmenName, pagenumber: seitenZahl, count: menge, voiceSum: voiceSum }
            ];
            stimmenName = '';
            seitenZahl = 0;
            menge = 0;
        } else {
            alert('Die Seitenzahl muss 1, 2, 4 oder ein Vielfaches von 4 sein.');
        }
   
    } else {
        alert('Bitte alle Felder korrekt ausfüllen.');
    }
}
</script>

<h2>Orchesterstimmen</h2>
<p>Der Druck erfolgt in s/w auf beschreibbarem Offsetpapier weiß in 120 g/m² und die Bindung ab 8 Seiten als 2-Klammer-Rückstichheftung.</p>
<p>Umfänge von vier Seiten werden als Faltblatt geliefert, ein- oder zwei Seiten als Einzelblatt.</p>
<p>Bitte beachten Sie, dass bei Falzbogen die Seitenzahl ohne Rest durch vier teilbar sein muss, also 4, 8, 12, 16, 20. ggfs. fügen wir am Ende Leerseiten an.</p>

<p>Bitte wählen Sie das gewünschte Endformat:</p>
<div class="inputPaperSize">
    <label>
        <input type="radio" bind:group={paperSize} value="A4" checked/>
        DIN A4 - 21 cm x 29,7 cm
    </label>
    <label>
        <input type="radio" bind:group={paperSize} value="C4" />
        DIN C4 - 22,9 cm x 32,4 cm
    </label>
</div>
<div class="inputPages">
    <label>
        Stimmenname:
        <input type="text" bind:value={stimmenName} placeholder="z.B. Violine 1"/>
    </label>
    <label>
        Seitenzahl:
        <input type="number" bind:value={seitenZahl} placeholder="Seitenzahl" min="1"/>
    </label>
    <label>
        Menge:
        <input type="number" bind:value={menge} placeholder="Menge" min="1"/>
    </label>
    <button onclick={addVoiceToArray}>Hinzufügen</button>
</div>






<div class="summaryBox">
    <b>Zusammenfassung für einen Auftrag via e-Mail an kai.chromik@online.de</b>
    {#if paperSizeChoosen}
        <p>Das gewählte Papierformat ist <b>DIN {paperSize}</b>.</p>
    {/if}
  
    <p>Das gewählte Papierformat ist <b>DIN {paperSize}</b>. Ihre Dateien werden ggfs. skaliert.</p>
    {#each voicesArray as page, index}
        <div class="summaryItem">
            <p><strong>{index + 1}. {page.stimmenName}</strong> - Seitenanzahl: {page.pagenumber}, Menge: {page.count}, Kosten für A4 bzw. C4: {page.voiceSum.toFixed(2)} bzw. {(page.voiceSum*c4ToA4).toFixed(2)} €</p>
            <button onclick={() => {
                voicesArray = voicesArray.filter((_, i) => i !== index);
                }}>
                Entfernen
            </button>
        </div>
    {/each}
    
    {#if paperSize === 'A4'}
        <p>Die Gesamtkosten der Orchesterstimmen im Format <b>DIN A4</b> betragen {arraySum.toFixed(2)} zzgl. 7% MwSt. = <b>{(arraySum*mwst).toFixed(2)} €</b>.</p>
    {/if}
    {#if paperSize === 'C4'}
        <p>Die Gesamtkosten der Orchesterstimmen im Format <b>DIN C4</b> betragen {(arraySum*c4ToA4).toFixed(2)} € zzgl. 7% MwSt. = <b>{(arraySum*c4ToA4*mwst).toFixed(2)} €</b>.</p>
    {/if}
    <p>zzgl. ggfs. Versand Deutschland 7.00 Euro</p>
    <p>Die Daten füge ich der e-Mail als Anhang hinzu.</p>
</div>

{#if paperSize && voicesArray.length > 0}
    <button onclick={() => copyToClipboard()}>Die Zusammenfassung in die Zwischenablage kopieren</button>
{/if}



<style>

button {
    background-color: #3498db;
    color: white;
    border: none;
    padding: 0.3em 0.7em;
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

.inputPages {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    padding: 0.5em;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 1rem;
    margin: 0.5em;
    gap: 0.5em;
}
@media (max-width: 799px) {
    .inputPages {
        flex-direction: column;
    }
}

.inputPages button {
    background-color: #2ecc71;
}
.inputPages button:hover {
    background-color: #27ae60;
}
.summaryItem {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 0.5em 0;
    padding: 0.5em;
    background-color: #f0f4f8;
    border-radius: 4px;
    box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}
.summaryItem button {
    background-color: #e74c3c;
}
.summaryItem button:hover {
    background-color: #c0392b;
}
.inputPaperSize {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    padding: 0.5em;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 1rem;
    margin: 0.5em;
    gap: 0.5em;
}
</style>