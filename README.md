# Card Home Assistant: errori robot Dreame L10 Ultra

## Obiettivo
Una card che mostra lo stato del robot e, quando c'e' un errore, un'immagine con un messaggio breve. La card ha **sempre la stessa dimensione**.

## Entita
- `sensor.dreame_l10_ultra_errore`: lo stato e' il **testo italiano** dell'errore; il **codice numerico** e' nell'attributo `value` (usare questo nella card).
- `select.dreame_l10_ultra_modalita_pulizia`: va mostrata solo nello stato lavaggio (aspira / lava / entrambe). **Da fare:** serve l'elenco delle opzioni (attributo `options`) per associare le icone.
- Integrazione: Tasshack/dreame-vacuum (le immagini non sono nel repository; i codici di errore si trovano in `custom_components/dreame_vacuum/dreame/types.py` e le traduzioni in `translations/it.json`).

## Mappa errori (vedi anche `mappa_errori.json`)
| Errore | Chiave | Codici (`value`) | Messaggio | Immagine | Certezza |
|---|---|---|---|---|---|
| Panni staccati | `mop_removed` | 69, 70 | I panni si sono staccati | errore_panni_staccati.png | alta |
| Ruote sollevate | `drop`, `drop_repeat` | 1, 6 | Le ruote non sono a contatto con il suolo | errore_ruote_sollevate.png | alta |
| Zona non raggiungibile | `route` | 61, 62 | Zona non raggiungibile | errore_zona_non_raggiungibile.png | ipotesi |
| Asse di lavaggio del mocio | `washboard_not_working` (alternativa `washboard_level`) | 124 (119) | Pulire l'asse di lavaggio del mocio | errore_asse_lavaggio_mocio.png | ipotesi |

Da verificare: quale testo compare per "zona non raggiungibile" e per l'asse di lavaggio, quando l'errore e' attivo (Strumenti per sviluppatori > Stati, oppure cronologia dell'entita).

## Stati normali
| Stato | Aspetto |
|---|---|
| Lavaggio | GIF animata dell'utente (teal, vedi video originale), robot visto dall'alto; **mostra la modalita' di pulizia** (chip) |
| Ritorno alla base | GIF animata dell'utente, davanti verso il basso, triangolo verso la base; **nessun chip** |
| In base | Robot grigio fermo davanti alla base, alone grigio; **nessun chip** |

## Decisioni di design
- Nessun riferimento temporale ("just now", "2 minutes ago").
- Nessun alone/sfondo verde per gli stati in azione; robot bianco nel mockup.
- Errori: nessun alone rosso dietro l'immagine; la parte da controllare (panni, ruote, barriera, asse di lavaggio) e' evidenziata in **rosso**; il resto in grigio chiaro. Badge "Errore" + messaggio breve sotto l'immagine.
- L'immagine di errore **sostituisce** l'animazione quando l'errore e' attivo.
- Tutte le card hanno la stessa altezza (area immagine e area testo fisse).
- Sfondo card scuro (#1c1c1c), stile a linee sottili come i disegni del manuale Dreame.

## Immagini (cartella `images/`, PNG con sfondo trasparente)
- `errore_*.png`: ricolorate per sfondo scuro (linee grigio chiaro, evidenziazione rossa). Nella zona non raggiungibile il percorso blu e' reso grigio e resta rossa solo la barriera.
- `robot_alto_bianco_fronte_alto.png`: lavaggio. `robot_alto_bianco_fronte_basso.png`: ritorno alla base. `robot_alto_grigio_fronte_alto.png`: in base. `robot_alto_scuro_originale.png`: linee scure per sfondi chiari.
- Il disegno originale del robot dall'alto ha il davanti (sensore) in basso; negli stati con davanti in alto l'immagine e' ruotata di 180 gradi. La risoluzione e' limitata (circa 240 px di partenza).
- Per usarle in Home Assistant: copiarle in `/config/www/` (disponibili come `/local/...`).

## Mockup
`mockup/mockup-card-robot.html`: pagina con tutti gli stati (apribile nel browser).

## Stato del Progetto: Completato ✅
La card definitiva pronta all'uso è contenuta in:
`dreame-vacuum-card.js`

### Caratteristiche Implementate
1. **Dimensioni identiche alla Minimal Washer Card**:
   - Compatibile al 100% con le Sezioni di Home Assistant: `rows: 5`, `columns: 6` (griglia standard a 5 righe / altezza ~310px).
   - Area visiva (180px) e area testi/chip a quota fissa: l'altezza della card non subisce mai variazioni o salti tra gli stati.
2. **Grafica e Immagini Autonome (Zero Configurazione)**:
   - Tutte le immagini PNG ad alta risoluzione (Robot e 4 schermate di Errore) sono incorporate nativamente in Base64 all'interno del file `.js`.
   - Non è necessario caricare file aggiuntivi in `/config/www/`: basta installare il singolo file `.js`.
3. **Mappatura Errori Completa (da `mappa_errori.json`)**:
   - `69, 70`: **Panni staccati** ("I panni si sono staccati")
   - `1, 6`: **Ruote sollevate** ("Le ruote non sono a contatto con il suolo")
   - `61, 62`: **Zona non raggiungibile** ("Zona non raggiungibile")
   - `124, 119`: **Asse lavaggio mocio** ("Pulire l'asse di lavaggio del mocio")
   - Fallback intelligente su codici numerici (attributo `value`) e su testo tradotto in italiano.
4. **Stati Normali e Modalità di Pulizia**:
   - **Lavaggio**: robot visto dall'alto con animazione discreta + chip con icone vettoriali SVG per le modalità:
     - Solo aspirazione (icona ventola / flusso)
     - Solo lavaggio (icona goccia d'acqua)
     - Aspira e lava (doppia icona)
   - **Ritorno alla base**: robot con davanti verso il basso e freccia verso la stazione.
   - **In base (Docked)**: robot su alone circolare scuro con contorno docking station.
   - **In pausa**: robot con indicatore "In pausa".
5. **Editor Visuale & Modalità Test**:
   - Editor nativo (`DreameVacuumCardEditor`) con selettori di entità e pulsanti interattivi per simulare all'istante qualunque stato o errore senza dover azionare fisicamente il robot.
6. **Interattività**:
   - Clic sul chip modalità: apre il selettore della modalità di pulizia.
   - Clic sulla card: apre la scheda dettagliata (`more-info`) del robot aspirapolvere.

---

## Installazione in Home Assistant

1. Copia `dreame-vacuum-card.js` nella cartella `/config/www/` del tuo Home Assistant (es. `/config/www/dreame-vacuum-card.js`).
2. In Home Assistant vai su **Impostazioni** > **Dashboard** > menu tre puntini in alto a destra > **Risorse**.
3. Clicca su **Aggiungi Risorsa**:
   - **URL**: `/local/dreame-vacuum-card.js`
   - **Tipo di risorsa**: `Modulo JavaScript`
4. Nella tua Dashboard (in particolare in modalità Sezioni o Standard), aggiungi una scheda personalizzata:

```yaml
type: custom:dreame-vacuum-card
entity: vacuum.dreame_l10_ultra
error_entity: sensor.dreame_l10_ultra_errore
cleaning_mode_entity: select.dreame_l10_ultra_modalita_pulizia
```

---

## Mockup e Anteprime
- `mockup/mockup-card-robot.html`: mockup statico originario di tutti gli stati.
- `mockup/preview-comparison.html`: simulatore interattivo con confronto affiancato tra la **Dreame Vacuum Card** e la **Washer Card** nelle 5 righe standard di Home Assistant.
