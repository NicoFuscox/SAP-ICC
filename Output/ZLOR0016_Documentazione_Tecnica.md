# Documentazione Tecnica

## Programma ZLOR0016 — Analisi architetturale, Clean Core Assessment e Roadmap di Modernizzazione

> **Ambito del documento**: analisi basata esclusivamente sul contenuto del file `Input/ZLOR0016.txt`. Nessun altro file della cartella `Input` è stato letto o utilizzato. Per l'inquadramento metodologico sono state consultate le skill tecniche `skills/sap-abap`, `skills/sap-btp-best-practices`, `skills/sap-btp-developer-guide` e la knowledge-base ICC in `knowledge-base/` (si veda il capitolo 14 per il dettaglio delle fonti). Ogni affermazione tecnica riportata è supportata da evidenza diretta nel codice estratto (con riferimento alle righe del file, secondo la numerazione dell'estrazione); i punti non verificabili sono raccolti nel capitolo finale "Punti da chiarire".

---

## 1. Executive Summary

`ZLOR0016` è un report ABAP classico (pacchetto `ZLO`, descrizione `SEG0058`) che rilegge un Calcolo Costi standard SAP (CO-PC, tabelle `KEKO`/`CKHS`/`CKIS`) già elaborato con gli strumenti standard (CK40N/CKW1) e ne produce una vista analitica "esplosa" (materiali, servizi esterni, manodopera, spese generali) su liste ALV interattive, con funzioni di confronto verso tempi standard, classificazione ABC e archiviazione di istantanee storiche.

Il file estratto **non è un singolo programma**, ma un bundle di 11 oggetti (si veda il capitolo 3): il programma principale, 4 include e alcuni artefatti di estrazione incompleti.

I due rilievi più significativi emersi dall'analisi sono:

1. **Il programma modifica direttamente la tabella standard SAP `CKIS`** (azzerando temporaneamente l'indicatore di errore `FEHLKZ` e confermando la modifica con `COMMIT WORK AND WAIT` prima di ripristinarla) per forzare l'inclusione di voci normalmente escluse nell'esplosione della struttura di costo, invece di utilizzare il parametro standard `S_EXPLODE_KF_TOO` della function `CK_F_CSTG_STRUCTURE_EXPLOSION`, presente nel codice ma disattivato (commentato) in tutti i punti di chiamata. Si tratta di una violazione diretta dei principi Clean Core e di un rischio concreto di corruzione (seppur transitoria, ma effettivamente confermata su database) di dati standard condivisi con altri utenti/processi.
2. **Assenza totale di controlli di autorizzazione** (`AUTHORITY-CHECK`) nel programma, unita a un uso estensivo e non standard di `COMMIT WORK AND WAIT` (32 occorrenze, diverse delle quali dentro cicli), a forte duplicazione di logica (fino a 4 varianti quasi identiche della stessa routine) e a un numero rilevante di routine morte (11 su 51, circa il 20%).

Il documento descrive in dettaglio architettura, modello dati, logica di elaborazione, qualità del codice, valutazione Clean Core, rischio di upgrade e una proposta di roadmap di modernizzazione, in linea con le linee guida SAP ICC (preferenza per RAP, API rilasciate, BAdI, estensioni side-by-side su SAP BTP).

## 2. Origine e natura del file analizzato

Il file `Input/ZLOR0016.txt` è il prodotto di uno strumento di estrazione automatica del codice ("Enhanced ZCODE_EXTRACTOR", dichiarato compatibile S/4HANA 1809, come indicato nel blocco di chiusura del file), non il sorgente originale prelevato da un sistema SAP tramite strumenti di sviluppo standard. Questo comporta alcune conseguenze rilevanti per l'analisi:

- Il testo pool (le maschere `TEXT-xxx` con le relative traduzioni) **non è incluso**: nel codice sono presenti solo i riferimenti simbolici, non i testi effettivi.
- Tre degli 11 oggetti risultano essere artefatti di estrazione incompleta/malformata (si veda il capitolo 3) e non contengono logica di programma reale.
- Non sono presenti informazioni di trasporto, versioning storico completo o dipendenze di attivazione (Switch/Business Function) del sistema di origine.

## 3. Oggetti inclusi nell'estrazione

| # | Nome oggetto | Tipo | Contenuto / ruolo |
|---|---|---|---|
| 1 | `ZLOR0016` | Programma (PROG) | Programma principale: schermata di selezione, eventi `INITIALIZATION`/`AT SELECTION-SCREEN`/`START-OF-SELECTION`, richiamo dei tre include sottostanti. |
| 2 | `AL` | Artefatto di estrazione | Frammento incompleto/malformato, privo di logica di programma utilizzabile. |
| 3 | `ALL FU` | Artefatto di estrazione | Frammento incompleto/malformato, privo di logica di programma utilizzabile. |
| 4 | `ALL` | Artefatto di estrazione | Frammento incompleto/malformato, privo di logica di programma utilizzabile. |
| 5 | `ZLOR16TOP` | Include (dichiarazioni globali) | Dichiarazione di tutte le tabelle interne, strutture, variabili globali e dei tipi utilizzati dal report (comprese le strutture per il catalogo di campo ALV). |
| 6 | `BDCRECXY` | Include standard SAP | Include standard per la gestione di sessioni Batch-Input (`OPEN_GROUP`, `CLOSE_GROUP`, `BDC_TRANSACTION`, `BDC_DYNPRO`, `BDC_FIELD`, `BDC_NODATA`). **Non risulta mai richiamato** (si veda il capitolo 10). |
| 7 | `STRUCTURE` | Include | Include vuoto nell'estrazione analizzata. |
| 8 | `<ICON>` | Type-pool standard SAP | Type-pool standard delle icone (`ICON`), utilizzato per le icone a semaforo nel confronto ore. |
| 9 | `ZLOR16INPUT` | Include | Validazione della schermata di selezione e determinazione degli articoli da elaborare (`check_button`, `check_values`, `check_choice`, `get_anno`, `handle_message`, oltre a `get_values`, mai richiamato). |
| 10 | `ZLOR16PROC` | Include | Logica di elaborazione: esplosione della struttura di costo (`get_bom_cfg` e varianti), determinazione tipo materiale, inizializzazione tipi di report validi. |
| 11 | `ZLOR16OUTPUT` | Include | Generazione degli output ALV: le quattro combinazioni Dettaglio/Totali × Materiali/Ore, le viste combinate, i comandi utente (drill-down), la classificazione ABC, l'elenco "materiali non previsti", la gestione delle istantanee (`man_ztckis`). È l'include più esteso (oltre 3000 righe). |

## 4. Architettura del programma

### 4.1 Flusso di esecuzione

```
INITIALIZATION
  └─ CALL FUNCTION 'ZZ_CHECK_ACTIVATION_HC'  → se non attiva, forza P_BUKRS = 'AER1' (proposta iniziale)

AT SELECTION-SCREEN OUTPUT
  └─ PERFORM check_button                     (routine vuota / no-op)

AT SELECTION-SCREEN
  ├─ Validazione incrociata P_APL / P_MATNR / P_WBS   → PERFORM handle_message (TEXT-e01..e06)
  ├─ PERFORM f_init_tipo_report + verifica combinazione MATD/ORED/CHKD/MATT/ORET/CHKT → TEXT-e07
  ├─ PERFORM check_values  (se P_APL valorizzato)  oppure  PERFORM check_choice (se non valorizzato)
  └─ se P_RIF = 'X': eventuale PERFORM get_anno + PERFORM man_ztckis USING 'C' ' '

START-OF-SELECTION
  ├─ se modalità "headless" (P_ALV = false) e ci sono messaggi d'errore → EXPORT TO MEMORY + LEAVE TO SCREEN 0
  ├─ determina il titolo dinamico della lista (CONTROLLO ORE/MATERIALI ...)
  └─ LOOP AT gt_mat_proc (uno o più articoli da elaborare)
       ├─ se non P_RIF: PERFORM get_bom_cfg[_aem_new] (+ get_ored/get_oret se richiesto il confronto)
       └─ PERFORM dell'output pertinente in base a MATD/ORED/MATT/ORET
             (out_bom_cfg_do/dm/to/tm, varianti *_new/*_aem, oppure le combinate f_out_bom_cfg_do_dm_new / f_out_bom_cfg_to_tm_new)
```

Il flusso mostra un pattern architetturale tipico del custom code classico: **tutta la logica di validazione, elaborazione e presentazione risiede in un unico programma monolitico**, senza separazione in livelli (nessuna classe, nessun'interfaccia, nessuna incapsulazione); la comunicazione fra le fasi avviene esclusivamente tramite variabili globali dichiarate nell'include `ZLOR16TOP`.

### 4.2 Le due modalità di invocazione

Il parametro `P_ALV` (dichiarato `NO-DISPLAY`, default `abap_true`) non è visibile all'utente ma determina un comportamento architetturalmente rilevante:

- **Modalità interattiva** (`P_ALV = abap_true`, il caso normale): i risultati e gli eventuali messaggi d'errore sono mostrati a video tramite `REUSE_ALV_GRID_DISPLAY` e `MESSAGE`.
- **Modalità "headless"** (`P_ALV = abap_false`, impostabile solo da un programma chiamante tramite `SUBMIT ... WITH P_ALV = ' '`): i messaggi d'errore e le tabelle risultato (`TAB_MESSAGES`, `TAB_LAY_CD`, `TAB_LAY_CT`, `TAB_LAY_CTM`, …) vengono trasferiti tramite `EXPORT ... TO MEMORY ID` e il programma termina con `LEAVE TO SCREEN 0`, per essere poi letti da un programma chiamante con `IMPORT ... FROM MEMORY ID`.

Questo secondo meccanismo indica che il programma è pensato anche come **motore di calcolo riutilizzabile da altri programmi custom**, ma l'integrazione avviene tramite ABAP Memory (una tecnica valida solo all'interno dello stesso application server / stessa sessione utente, non un'interfaccia versionata, tipizzata o riutilizzabile al di fuori del sistema ABAP). Questo è un punto rilevante per la valutazione Clean Core (capitolo 11, dimensione "Integrazione").

## 5. Modello dati

### 5.1 Tabelle standard SAP lette

| Tabella | Ruolo funzionale (dal glossario incluso nell'intestazione del programma e dall'uso nel codice) |
|---|---|
| `KEKO` | Testata calcolo costi (una riga per ogni combinazione articolo/stabilimento/data/variante di calcolo). |
| `CKIS` | Voci/componenti del calcolo costi (materiali, manodopera, spese generali); campo `TYPPS` distingue `M`=materiale, `L`=prestazione/servizio esterno, `E`=manodopera/attività interna, `G`=quota di costi generali. **Oggetto della modifica diretta descritta al capitolo 8.** |
| `CKHS` | Testata dei costi unitari (riferita solo nella dichiarazione `TABLES` e nei parametri della function di esplosione; non interrogata direttamente con `SELECT`). |
| `KALA` | Log/testata dell'esecuzione del calcolo costi (verifica di esistenza dell'elaborazione richiesta). |
| `CRHD` / `CRCO` / `CRTX` | Anagrafica, centro di costo collegato e testo del centro di lavoro. |
| `MARA` / `MARC` / `MAKT` / `MBEW` | Anagrafica materiale, dati di stabilimento, descrizione, dati di valorizzazione (prezzo medio ponderato). |
| `T023T` | Testo del gruppo merceologico. |
| `PLPO` | Fasi del ciclo di lavorazione: dichiarata e referenziata solo in codice commentato/inattivo. |

### 5.2 Tabelle custom (Z) di configurazione e archiviazione

| Tabella | Ruolo |
|---|---|
| `ZT1016_CST` | Tabella di configurazione: associa un "Cod. Programma" (`LABOR`) all'identificativo dell'esecuzione di calcolo costi di riferimento (`KALAID`/`KALADAT`/`TVERS`) da utilizzare. |
| `ZT1016_PRM` | Tabella di parametri generici, interrogata con chiave `UTENTE = 'SAP*'`, `TRANS = 'ZLOR16'`, `PARAMETRO = 'FORM_OLD'` oppure `'SKIP_XKEKO'`: un meccanismo artigianale di feature toggle. |
| `ZT0102` | Anagrafica di collegamento fra "Cod. Programma"/Progetto Tecnico e gli articoli (Part Number) e stabilimenti associati, con indicatore di eleggibilità `FL_EIV`. |
| `ZCO_APL_AEMTECHS` | Tabella di mappatura, usata solo per la società con regole particolari, fra il codice tecnico (`TECHS`) e l'articolo/commessa jolly corrispondente. |
| `ZTCKIS` | Archivio delle istantanee di dettaglio salvate per anno (funzione "fotografia", capitolo 7.6), con numero progressivo (`PROGR`) e campo `TIPO` per distinguere righe di manodopera (`'E'`) da righe di materiale. |
| `ZT1012C` | Tabella dei tempi standard di riferimento per il confronto ore (chiave articolo interno + centro di lavoro, campo `TEMPO_RUN`). |
| `ZTFUS007` | Tabella di attivazione per la logica di valorizzazione alternativa "SPLIT" (si veda il capitolo 10.6). |

### 5.3 Dichiarazioni non utilizzate

Le tabelle `MAST` e `STPO` (rispettivamente collegamento distinta base/articolo e voce di distinta base standard) sono dichiarate nello statement `TABLES` dell'include `ZLOR16TOP` (riga 426) ma **non risultano mai referenziate** in nessuna istruzione `SELECT` del programma: dichiarazioni residue, prive di effetto ma indice di codice non ripulito da versioni precedenti.

### 5.4 Strutture di lavoro principali

Le tabelle interne globali `tckis`/`tkeko`/`xkeko`/`mckis` (dichiarate con la sintassi obsoleta `OCCURS 0 WITH HEADER LINE`, 26 occorrenze totali nel programma) fungono da area di lavoro condivisa fra le routine di esplosione (`ZLOR16PROC`) e le routine di output (`ZLOR16OUTPUT`); le tabelle `tab_lay_cd`/`tab_lay_ct`/`tab_lay_ctm` rappresentano invece il risultato pronto per la visualizzazione ALV, con i relativi accumulatori `_app2` utilizzati per la logica multi-articolo (capitolo 7.5).

## 6. Interfacce e dipendenze esterne

### 6.1 Function module standard SAP richiamate

| Function module | Utilizzo |
|---|---|
| `CK_F_CSTG_STRUCTURE_EXPLOSION` | Cuore del programma: esplode la struttura di costo di un calcolo costi (equivalente della logica utilizzata da CK40N/CKW1) restituendo la tabella struttura (`STRUKTURTABELLE`). Richiamata sia per il livello principale sia, ricorsivamente, per ciascun sotto-assieme configurato. |
| `CONVERSION_EXIT_ABPSP_INPUT` | Conversione standard del formato esterno di un elemento WBS (Commessa) nel formato interno. |
| `REUSE_ALV_GRID_DISPLAY`, `REUSE_ALV_COMMENTARY_WRITE`, `REUSE_ALV_VARIANT_F4` | Visualizzazione ALV "classica" (non Object Model, non SALV). |
| `SAPGUI_PROGRESS_INDICATOR` | Indicatore di avanzamento durante l'elaborazione. |
| `POPUP_GET_VALUES`, `POPUP_GET_VALUES_DB_CHECKED` (quest'ultima citata solo in commento) | Popup di richiesta valore (usato per l'anno in modalità Riferimento). |
| `POPUP_TO_DECIDE_LIST`, `POPUP_TO_DISPLAY_TEXT` | Popup di conferma/messaggio. |
| `MD_POPUP_SHOW_INTERNAL_TABLE` | Popup con elenco dei "Materiali non previsti". |
| `BDC_OPEN_GROUP`, `BDC_CLOSE_GROUP`, `BDC_INSERT` (nell'include morto `BDCRECXY`) | **Mai richiamate**: codice morto, si veda il capitolo 10. |

### 6.2 Function module custom (Z) richiamate

| Function module | Utilizzo | Nota |
|---|---|---|
| `ZZ_CHECK_ACTIVATION_HC` | Feature toggle per l'abilitazione multi-società, controllato in `INITIALIZATION`. | Logica interna non inclusa in questo estratto: comportamento esatto non verificabile. |
| `ZZ_GET_WILDCOV_FROM_TECHS` | Risolve, a partire dal codice tecnico (`TECHS`), il pattern di commessa (WBS) da utilizzare. | Idem. |
| `Z_MD_MAT_TYPE_DETERMINE` | Determina il tipo materiale (sostituisce, per compatibilità S/4HANA, una `SELECT SINGLE MTART FROM MARA` diretta — si veda `get_mtart`). | Idem. |

Tutte e tre sono function module custom (prefisso `Z`/`ZZ`): per definizione **non si tratta di API rilasciate SAP**, e la loro compatibilità Clean Core dipende dalla loro implementazione, non verificabile in questo estratto (si veda il capitolo 15).

### 6.3 Transazioni richiamate come azioni di approfondimento (drill-down)

| Codice funzione ALV | Transazione richiamata | Significato |
|---|---|---|
| `AMAT` | `MM03` | Visualizzazione anagrafica materiale (standard). |
| `VIEW` | `MD04` | Elenco stock/fabbisogni (standard). |
| `ROU` | `CA03` | Visualizzazione ciclo di lavorazione (standard). |
| `CDL` | `CR03` | Visualizzazione centro di lavoro (standard), con `SET PARAMETER ID` su centro di lavoro e stabilimento. |
| `CS03` | `ZCS03` | Transazione **custom**, non inclusa in questo estratto: comportamento non verificabile. |
| `CS12` | `CS12` | Visualizzazione multi-livello distinta base (standard). |
| `CS15` | `ZCS15` | Transazione **custom**, non inclusa in questo estratto: comportamento non verificabile. |
| `KEKO` | (routine interna `get_list_nokeko`) | Popup con elenco dei componenti privi di calcolo costi valido. |
| `ABC` | (routine interna `compute_abc`) | Classificazione A/B/C (solo materiali). |
| `WRITE` | (routine interna `man_ztckis` con parametro `'W'`) | Salvataggio istantanea annuale. |
| `READ` | *nessun gestore nel codice* | Presente nell'esclusione dei pulsanti per le liste Totali (quindi visibile nelle liste Dettaglio), ma **non gestito in nessun `CASE sy-ucomm`**: pulsante privo di effetto funzionale allo stato del codice analizzato. |

## 7. Logica di elaborazione — dettaglio

### 7.1 Validazione input (`AT SELECTION-SCREEN`, `check_values`, `check_choice`)

Il programma impone che l'oggetto dell'analisi sia specificato in uno solo di due modi mutuamente esclusivi:

- **Progetto Tecnico** (`P_APL` valorizzato, `P_MATNR` e `P_WBS` vuoti): tramite `check_values`, il programma deriva `P_CAP = P_APL(3)` e un riferimento tecnico `LV_TECHS = P_APL(9)`, quindi:
  - per la società con regole particolari, risolve l'elenco articoli tramite `ZCO_APL_AEMTECHS` e determina l'esecuzione di calcolo costi da `ZT1016_CST` (con un `CASE P_CAP` dedicato per i valori `'345'`/`'346'`, altrimenti fallback su `'999'`);
  - per le altre società, risolve il pattern di commessa tramite la function custom `ZZ_GET_WILDCOV_FROM_TECHS` e determina l'elenco articoli da `ZT0102` (con eventuale filtro su `P_APL+10(2)` = sotto-codice);
  - per due società specifiche (`LI03`, `LI04`), antepone rispettivamente il prefisso `'IA'`/`'IV'` al codice di commessa risolto;
  - popola la tabella di lavoro `gt_mat_proc` con l'elenco completo degli articoli da elaborare (`GV_LINES` = numero di articoli).
- **Materiale + Commessa** (`P_MATNR` e `P_WBS` entrambi valorizzati, `P_APL` vuoto): tramite `check_choice`, il programma determina l'esecuzione di calcolo costi da `ZT1016_CST` in base a `P_CAP` e al tipo di versione (`P_EFF`/`P_RIF` → `TVERS = '01'`; `P_SIM` → prima riga con `TVERS <> '01'`), verifica l'esistenza dell'elaborazione (`KALA`) e del calcolo costi (`KEKO`) corrispondente, e popola `gt_mat_proc` con un singolo articolo.

La combinazione ammessa fra le caselle `MATD`/`ORED`/`CHKD`/`MATT`/`ORET`/`CHKT` è verificata tramite un confronto a chiave multipla (`READ TABLE gt_report WITH KEY ...`) contro una tabella di 8 combinazioni valide costruita in `f_init_tipo_report` (`VALUE #( (matd='X') (ored='X') (chkd='X') (matt='X') (oret='X') (chkt='X') (matd='X' ored='X') (matt='X' oret='X') )`). Si veda il capitolo 10.7 per un'anomalia rilevata in questa logica.

### 7.2 Esplosione della struttura di costo (`get_bom_cfg` e varianti)

La routine (in 4 varianti quasi identiche: `get_bom_cfg`, `get_bom_cfg_aem`, `get_bom_cfg_new`, `get_bom_cfg_aem_new` — le ultime due mai richiamate, si veda il capitolo 10.1):

1. Individua il calcolo costi di livello più alto (`tkeko`) e, se configurato tramite `ZT1016_PRM` (parametro `SKIP_XKEKO`), estrae anche i calcoli costi non standard per i materiali di tipo `'ZPDP'`.
2. **Prima dell'esplosione**, ripulisce (azzera) l'indicatore di errore `CKIS-FEHLKZ` per le voci coinvolte (si veda il capitolo 8) e conferma con `COMMIT WORK AND WAIT`.
3. Richiama `CK_F_CSTG_STRUCTURE_EXPLOSION` (variante di calcolo `'ZPC5'` per il livello principale) per ottenere la struttura esplosa (`xckis` → `tckis`).
4. Per ogni componente risultante di tipo materiale gestito a configurazione (`MTART = 'ZPDP'` e `SBDKZ = '2'`), richiama **di nuovo** `CK_F_CSTG_STRUCTURE_EXPLOSION` (variante `'PPC1'`) per esplodere anche il relativo sotto-assieme, e **inserisce le righe risultanti direttamente nella tabella `tckis` che si sta scorrendo con `LOOP AT`** (tramite `INSERT tckis INDEX idx`), realizzando così un'esplosione multi-livello senza una routine ricorsiva esplicita.
5. Al termine, ripristina l'indicatore `CKIS-FEHLKZ` per tutte le voci precedentemente alterate (tracciate nella tabella `tfehlkz`) e conferma con un ulteriore `COMMIT WORK AND WAIT`.

### 7.3 Generazione dell'output (`out_bom_cfg_*`)

Le routine di output (una per ciascuna combinazione Dettaglio/Totali × Materiali/Ore, più le varianti per società e le combinate) condividono uno schema comune:

1. Costruiscono dinamicamente il catalogo di campo ALV (`fieldcatalog`/`fieldcatalog1`), con colonne aggiuntive condizionate da `P_APL IS NOT INITIAL` (colonne "Progetto APL"/"WBS (COV)"/"Part Number Top") e da `P_BUKRS` (colonne omesse per la società con regole particolari).
2. Se `P_RIF = 'X'`, delegano la lettura alla routine `man_ztckis` (parametro `'R'`) invece di elaborare i dati appena esplosi, e terminano.
3. Altrimenti, filtrano/trasformano le righe di `tckis`/`mckis` nella struttura di layout (`tab_lay_cd`/`tab_lay_ct`/`tab_lay_ctm`), con logica specifica per tipo voce (materiale/servizio/manodopera/spese generali) e, per talune varianti, gestione dei co-prodotti (righe `TYPPS = 'A'` riclassificate a `'M'`).
4. Accumulano il risultato nelle tabelle `_app2` (si veda il capitolo 7.5) e, solo quando è stato elaborato l'ultimo articolo (`GV_CONT = GV_LINES`), richiamano `stampa_report_alv_*` per la visualizzazione ALV effettiva.

### 7.4 Le viste combinate

`f_out_bom_cfg_do_dm_new` e `f_out_bom_cfg_to_tm_new` orchestrano rispettivamente la combinazione "Materiali + Ore Dettaglio" e "Materiali + Ore Totali": salvano una copia temporanea delle tabelle di lavoro condivise (`tckis`/`tkeko`/`xkeko`/`mckis`), eseguono la routine Ore, ripristinano le tabelle di lavoro dalla copia, eseguono la routine Materiali (con lo smistamento AER1/AEM dove pertinente), quindi costruiscono un catalogo di campo unico che comprende sia le colonne dei materiali sia quelle delle ore e uniscono le due liste risultanti in un'unica tabella da presentare in un solo ALV.

### 7.5 Meccanismo di accumulo multi-articolo

Quando `gt_mat_proc` contiene più di un articolo (modalità Progetto Tecnico), ciascuna routine di output viene eseguita una volta per articolo all'interno del ciclo principale in `START-OF-SELECTION`. All'interno di ogni routine, la tabella di lavoro (es. `tab_lay_cd`) viene azzerata a ogni chiamata, popolata con i soli dati dell'articolo corrente, quindi **accodata** (riga per riga) in un accumulatore persistente (es. `tab_lay_cd_app2`, mai azzerato all'interno della routine). Solo quando il contatore di iterazione (`GV_CONT`) coincide con il numero totale di articoli (`GV_LINES`), l'accumulatore viene ricopiato nella tabella di lavoro e passato a `stampa_report_alv_*`: la lista ALV viene quindi mostrata **una sola volta**, con i dati di tutti gli articoli elaborati.

### 7.6 Archiviazione e lettura storicizzata (`man_ztckis`)

La routine unica `man_ztckis`, invocata con un parametro modale (`'C'`=controllo, `'W'`=scrittura, `'R'`=lettura), gestisce l'intero ciclo di vita delle istantanee in `ZTCKIS`:

- `'C'`: verifica, subito dopo la validazione della schermata di selezione (se `P_RIF = 'X'`), che esista già un'istantanea per l'articolo/commessa/anno richiesti, segnalando altrimenti un messaggio d'errore.
- `'W'`: richiamata dal pulsante "WRITE" delle liste di dettaglio, verifica che non esista già un'istantanea per l'anno corrente (per lo stesso articolo/commessa) e, in caso contrario, inserisce in `ZTCKIS` una riga per ciascuna riga della lista corrente, con numero progressivo.
- `'R'`: richiamata all'inizio delle routine di output quando `P_RIF = 'X'`, rilegge da `ZTCKIS` le righe archiviate per l'anno richiesto (impostato tramite `get_anno`) e ricostruisce la lista di dettaglio da tali dati, **senza rieseguire l'esplosione della struttura di costo**.

## 8. Rilievo critico #1 — Modifica diretta della tabella standard `CKIS`

### Evidenza

In tutte le varianti attive di `get_bom_cfg`, il programma esegue, prima di ogni chiamata a `CK_F_CSTG_STRUCTURE_EXPLOSION`, la sequenza seguente (esempio da `get_bom_cfg`, righe 1536–1549 e 1668–1682, con schema replicato nelle altre varianti):

```
SELECT * FROM ckis WHERE bzobj = keko-bzobj AND kalnr = keko-kalnr AND ...
  IF ckis-fehlkz = 'X'.
    ckis-fehlkz = ' '.
    UPDATE ckis.                      " <-- scrittura diretta su tabella standard
    MOVE-CORRESPONDING ckis TO tfehlkz.
    APPEND tfehlkz.                   " <-- traccia le righe alterate, per il ripristino
  ENDIF.
ENDSELECT.
COMMIT WORK AND WAIT.                 " <-- la modifica diventa visibile e permanente
...
CALL FUNCTION 'CK_F_CSTG_STRUCTURE_EXPLOSION' ...
COMMIT WORK AND WAIT.
...
* (più avanti, righe 1813-1828)
LOOP AT tfehlkz.
  ... IF sy-subrc = 0 AND ckis-fehlkz = ' '.
    ckis-fehlkz = 'X'.
    UPDATE ckis.                      " <-- ripristino, sempre con scrittura diretta
  ENDIF.
ENDLOOP.
COMMIT WORK AND WAIT.
```

Lo stesso schema (azzeramento → commit → esplosione → commit → ripristino → commit) è replicato, con lievi variazioni, in tutte e 4 le varianti di `get_bom_cfg` (12 occorrenze totali di `UPDATE ckis` individuate nel file, alle righe 1545, 1678, 1826, 2084, 2185, 2278, 2348, 2482, 2576, 2631, 2752, 2872).

### Perché è un rilievo critico

- `CKIS` è una **tabella applicativa standard SAP**, condivisa da tutti i calcoli costi del sistema e da tutte le transazioni standard che li utilizzano (CK13N, CK40N, CKW1, valorizzazioni di magazzino, ecc.): non è un oggetto di proprietà del namespace custom, e la sua modifica diretta da programma custom è, per definizione, l'opposto di un'estensione "clean" (si veda il capitolo 11, dimensione "Estensibilità").
- Il flag `FEHLKZ` segnala un errore riconosciuto dal sistema standard su quella voce di costo: azzerarlo, anche temporaneamente, altera il significato di un dato standard per **tutti** gli utenti/processi che in quel momento dovessero leggere `CKIS` (non solo per la sessione che esegue `ZLOR0016`), poiché il `COMMIT WORK AND WAIT` rende la modifica immediatamente visibile e durevole.
- La sequenza "azzera → commit → esplodi → commit → ripristina → commit" **non è atomica**: se il programma termina in modo anomalo fra il primo e l'ultimo `COMMIT WORK AND WAIT` (per un dump, un'interruzione utente, un timeout, un'eccezione non gestita in `CK_F_CSTG_STRUCTURE_EXPLOSION`), le righe di `CKIS` restano con `FEHLKZ` azzerato **in modo permanente**, poiché la modifica è già stata confermata su database prima del ripristino. Considerata la complessità del ciclo (chiamate ricorsive multiple alla stessa function per ogni sotto-assieme configurato), questo scenario non è remoto.
- La function standard `CK_F_CSTG_STRUCTURE_EXPLOSION` viene invocata con il parametro `S_READ_ONLY_DB = 'X'` (riga 1583 e successive), che dichiara l'intento di una lettura non modificativa: il programma custom aggira comunque tale garanzia intervenendo manualmente sulla tabella sottostante prima della chiamata.

### La contromisura standard, disponibile ma non utilizzata

In tutti e 4 i punti di chiamata a `CK_F_CSTG_STRUCTURE_EXPLOSION` (righe 1718, 2223, 2520, 2792) è presente, commentato, il parametro:

```
*         s_explode_kf_too      = 'X'
```

Il nome del parametro (letteralmente "esplodi anche le [voci con] errore/FEHLKZ") indica come la function standard offra già, tramite un parametro ufficiale della sua interfaccia, la possibilità di includere nell'esplosione le voci altrimenti escluse per errore — esattamente il risultato che il programma ottiene con la modifica diretta di `CKIS`. La presenza del parametro, disattivato ma non rimosso, in tutti e 4 i punti di chiamata costituisce una forte evidenza che l'alternativa standard fosse nota a chi ha scritto/modificato il codice.

## 9. Rilievo critico #2 — Assenza di controlli di autorizzazione

Nell'intero programma (11 oggetti, quasi 6000 righe) **non è presente alcuna istruzione `AUTHORITY-CHECK`**. L'accesso ai dati di calcolo costi (informazione tipicamente riservata al controllo di gestione) e alle azioni di drill-down verso transazioni standard (`MM03`, `MD04`, `CA03`, `CR03`) si basa quindi implicitamente solo sulle autorizzazioni SAP standard verificate dalle transazioni richiamate in `CALL TRANSACTION`, e sull'autorizzazione generica di esecuzione del programma/della transazione `ZLOR16` stessa. Non risultano controlli aggiuntivi per società (`P_BUKRS`), per Progetto Tecnico/commessa, né per l'azione di scrittura dell'archivio storico (`ZTCKIS`, capitolo 7.6), che chiunque possa eseguire il programma può quindi generare.

## 10. Analisi della qualità del codice e Technical Debt

### 10.1 Duplicazione di logica

| Famiglia di routine | Varianti presenti | Attive nel flusso corrente | Nota |
|---|---|---|---|
| Esplosione struttura costo | `get_bom_cfg`, `get_bom_cfg_aem`, `get_bom_cfg_new`, `get_bom_cfg_aem_new` | Solo `get_bom_cfg` (società generiche) e `get_bom_cfg_aem_new` (società con regole particolari) | Le altre due sono codice morto (capitolo 10.2); le due attive sono circa al 90% identiche fra loro. |
| Output Materiali Dettaglio | `out_bom_cfg_dm`, `out_bom_cfg_dm_aem` | Entrambe (smistate per società) | Logica quasi identica, con differenze puntuali (gestione co-prodotti, colonne aggiuntive, valorizzazione "SPLIT"). |
| Output Ore Totali | `out_bom_cfg_tm`, `out_bom_cfg_tm_new`, `out_bom_cfg_tm_aem` | Solo le ultime due | `out_bom_cfg_tm` è codice morto. |
| Output Ore Dettaglio | `out_bom_cfg_do`, `out_bom_cfg_do_new` | Entrambe, smistate da un parametro di configurazione (`ZT1016_PRM`, parametro `FORM_OLD`) | Duplicazione mantenuta volontariamente per compatibilità, tramite un meccanismo di feature-flag non standard. |

Questa duplicazione (4 varianti quasi identiche per la sola esplosione costo, più le coppie duplicate in output) rappresenta il principale fattore di rischio in caso di correzioni future: una correzione applicata a una variante (ad esempio per risolvere un difetto) rischia di non essere applicata alle varianti "gemelle", come già suggerito dalle differenze riscontrate fra `out_bom_cfg_dm` e `out_bom_cfg_dm_aem` nella gestione della logica di valorizzazione "SPLIT" (capitolo 10.6).

### 10.2 Codice morto

Su 51 routine (`FORM`) definite nel programma, **11 non risultano mai richiamate attivamente** (chiamate solo da righe di commento, o non richiamate affatto):

- `get_values` (riferimento solo in commento, riga 1059);
- `get_bom_cfg_aem` (riferimento solo in commento, riga 296);
- `get_bom_cfg_new` (riferimento solo in commento, riga 300);
- `out_bom_cfg_tm` (riferimento solo in commento, riga 344);
- `OPEN_GROUP`, `CLOSE_GROUP`, `BDC_TRANSACTION`, `BDC_DYNPRO`, `BDC_FIELD`, `BDC_NODATA` (include `BDCRECXY`, mai richiamato in alcun punto, nemmeno in commento: probabile residuo di un template di generazione batch-input non più utilizzato);
- `bdc_transaction1` (mai richiamata).

A ciò si aggiunge un riferimento a una routine `check_init`, commentato in `INITIALIZATION` (riga 179), la cui definizione **non è nemmeno presente** nel programma: ulteriore traccia di logica rimossa in passato senza una pulizia completa dei riferimenti residui.

### 10.3 Sintassi e costrutti obsoleti

| Costrutto | Occorrenze rilevate | Impatto |
|---|---|---|
| `OCCURS 0 WITH HEADER LINE` (tabelle interne con "header line") | 26 | Sintassi obsoleta dagli anni '90; non disponibile in ABAP Cloud (linguaggio ristretto). |
| `SELECT ... ENDSELECT` (ciclo riga-per-riga anziché lettura massiva) | 56 occorrenze di `ENDSELECT` nel file (49 attive, 7 in blocchi di codice commentato) | Pattern superato dalle prestazioni di `SELECT ... INTO TABLE`; penalizzante su HANA per volumi elevati. |
| `WRITE` (output tipo lista classica, mescolato con ALV) | 62 | Retaggio di una versione pre-ALV del programma (si vedano i blocchi commentati con vecchie istruzioni `WRITE` accanto alla costruzione delle liste ALV); non disponibile in ABAP Cloud. |
| `COMMIT WORK AND WAIT` | 32 (diverse delle quali dentro cicli, es. ogni 1000 righe elaborate) | Frammenta l'unità logica di lavoro (LUW), introduce attese sincrone e rischi di inconsistenza (capitolo 8); sconsigliato anche dalle linee guida ABAP generali, non solo Clean Core. |
| `DESCRIBE TABLE` | 3 | Costrutto da evitare in ABAP Cloud (sostituito da `lines( )`). |

Il programma presenta comunque, in più punti, dei blocchi già riscritti con sintassi moderna (espressioni inline `DATA(...)`, `VALUE #( ... )`, `LOOP AT ... INTO DATA(...)`), introdotti dai commenti "NDC-UPGRADE" del 2020: si tratta quindi di codice stratificato in più epoche, con stili disomogenei coesistenti nello stesso oggetto.

### 10.4 Adattamenti S/4HANA già applicati (ma solo di compatibilità)

Sono presenti 53 blocchi marcati con commenti `S4 - HANA DB - BEGIN/END OF MODIFY` (riferimenti a correzioni `<S4HANA-003>`, `<S4HANA-004>`) che sostituiscono, ad esempio, `SELECT SINGLE * FROM keko WHERE ...` con:

```
SELECT * UP TO 1 ROWS FROM keko WHERE ...
ORDER BY PRIMARY KEY.
ENDSELECT.
```

Questo pattern **preserva il comportamento originale** (incluso l'uso implicito dell'area `keko` come header line dopo il ciclo) ma non ne coglie l'occasione per modernizzare realmente il costrutto (ad esempio con `SELECT SINGLE ... INTO @DATA(...)`), confermando che gli interventi storici sul programma sono stati di natura correttiva/compatibilità e non di refactoring architetturale.

### 10.5 Dichiarazioni inutilizzate

Le tabelle standard `MAST` e `STPO` sono dichiarate ma mai utilizzate (capitolo 5.3); la tabella `PLPO` è dichiarata e referenziata solo in codice commentato.

### 10.6 Logiche di configurazione incoerenti fra varianti

La logica di valorizzazione alternativa "SPLIT" (attivabile tramite la tabella `ZTFUS007`) risulta commentata/disattivata (con riferimento a un "Progetto Split... ROLLBACK" datato dicembre 2014) nelle varianti `out_bom_cfg_dm_aem`/`out_bom_cfg_tm_aem`, ma il blocco equivalente non presenta lo stesso commento di rollback nelle varianti generiche corrispondenti: non è possibile stabilire dal solo codice se questa differenza sia intenzionale o sia una dimenticanza nel propagare il rollback a tutte le varianti (si veda il capitolo 15).

### 10.7 Anomalia nella validazione delle caselle di controllo ore

La tabella delle combinazioni valide costruita in `f_init_tipo_report` (capitolo 7.1) elenca, fra le righe ammesse, `(chkd = 'X')` da sola (con `ored` vuoto) ma **non** la combinazione `(ored = 'X' AND chkd = 'X')` insieme; lo stesso vale per `chkt`/`oret`. Poiché sulla schermata di selezione la casella `chkd` è posizionata sulla stessa riga della casella `ored` (analogamente `chkt` con `oret`, righe 146-161), lasciando intendere un utilizzo congiunto, questa combinazione sembra essere un caso d'uso plausibile che, secondo la sola logica di validazione presente nel codice, risulterebbe però respinta con un messaggio d'errore. Si veda il capitolo 15 per la richiesta di conferma.

## 11. Clean Core Assessment

Valutazione strutturata secondo le sei dimensioni Clean Core descritte nella knowledge-base ICC (`01 Discovering the Clean Core Concept`): Software Stack & Core, Processi, Estensibilità, Integrazione, Dati, Operazioni.

| Dimensione | Valutazione | Evidenza |
|---|---|---|
| **Software Stack & Core** | 🔴 Non conforme | Sintassi ABAP obsoleta e non compatibile con il linguaggio ristretto ABAP Cloud (`WRITE`, `DESCRIBE TABLE`, `OCCURS ... HEADER LINE`, `MOVE ... TO` diffuso — costrutti esplicitamente da evitare in ABAP Cloud secondo la skill `sap-abap`); gli adattamenti S/4HANA applicati sono solo di compatibilità sintattica (capitolo 10.4), non di modernizzazione architetturale. |
| **Processi** | 🟠 Parzialmente conforme | Buona documentazione di dominio incorporata nei commenti di intestazione (glossario KEKO/CKHS/CKIS/KALF/CMFP) e finalità del programma chiara (verifica calcolo costi); ma logica duplicata in più varianti quasi identiche (capitolo 10.1) invece di un processo unico parametrizzato, in contrasto con il principio "ridurre le varianti al minimo indispensabile". |
| **Estensibilità** | 🔴 Non conforme (rilievo più grave) | Modifica diretta di una tabella applicativa standard (`CKIS`, capitolo 8) anziché uso del parametro ufficiale dell'interfaccia standard già disponibile; nessun uso di BAdI, Business Add-In o Switch Framework per la logica specifica di società (gestita con `IF P_BUKRS = ...` cablati nel codice); tre function module custom non valutabili in questa sede (capitolo 6.2). |
| **Integrazione** | 🟠 Parzialmente conforme | Nessuna interfaccia OData/API/eventi; l'unico meccanismo di riuso da altri programmi è il trasferimento tramite ABAP Memory (`EXPORT/IMPORT ... TO/FROM MEMORY ID`), funzionale ma non tipizzato, non versionato, e utilizzabile solo all'interno dello stesso sistema/application server. |
| **Dati** | 🔴 Non conforme | Rischio concreto di corruzione temporanea di dati standard condivisi (`CKIS.FEHLKZ`, capitolo 8) in caso di interruzione anomala dell'elaborazione; dipendenza da tabelle di configurazione custom (`ZT1016_PRM`, `ZT1016_CST`) il cui contenuto non è verificabile né documentato in un unico punto. |
| **Operazioni** | 🔴 Non conforme | Assenza totale di `AUTHORITY-CHECK` (capitolo 9); uso pesante e non controllato di `COMMIT WORK AND WAIT` anche dentro cicli (impatto su performance e "Batch job Management", esplicitamente citato come area di attenzione operativa nella metodologia Clean Core); presenza di codice morto (11 routine su 51) che appesantisce la manutenzione senza alcun beneficio; nessun test automatico (ABAP Unit) incluso in questo estratto. |

**Sintesi**: il programma presenta un livello di aderenza Clean Core **basso**, con un rilievo di gravità alta (modifica diretta di tabella standard con conferma su database) che da solo giustificherebbe una remediation prioritaria indipendentemente da qualunque iniziativa di modernizzazione più ampia.

## 12. Valutazione del rischio di upgrade

| Area di rischio | Livello | Motivazione |
|---|---|---|
| Modifica diretta di `CKIS` | **Alto** | Un cambiamento futuro della struttura, dei controlli di autorizzazione lato tabella, o della logica di blocco (lock) di `CKIS` da parte di SAP (ad esempio in una futura release o in una conversione a S/4HANA Cloud) può rendere la tecnica corrente non funzionante o, peggio, aggravarne gli effetti collaterali. In ambienti ABAP Cloud/RISE with SAP a governance stretta, l'accesso in scrittura diretto a tabelle applicative standard non di proprietà del namespace custom non è comunque consentito dal linguaggio ristretto. |
| Compatibilità linguaggio ABAP Cloud | **Alto** | I costrutti elencati al capitolo 10.3 (`WRITE`, `DESCRIBE TABLE`, `OCCURS ... HEADER LINE`, ALV classico) non sono ammessi nella variante linguistica ABAP Cloud: il programma, così com'è, **non potrebbe essere eseguito** in un ambiente ABAP Environment (Steampunk) o in un sistema S/4HANA Cloud pubblico senza un riscrittura sostanziale. |
| Function module custom non valutabili | **Medio** | `ZZ_CHECK_ACTIVATION_HC`, `ZZ_GET_WILDCOV_FROM_TECHS`, `Z_MD_MAT_TYPE_DETERMINE` non sono incluse in questo estratto: il loro stato di modernizzazione/rilascio non è verificabile e rappresenta un'incognita per qualunque stima di sforzo di migrazione. |
| Duplicazione della logica | **Medio** | Aumenta lo sforzo e il rischio di regressione di qualunque intervento di correzione o adeguamento futuro, poiché le stesse modifiche vanno replicate manualmente su più varianti (capitolo 10.1). |
| Codice morto | **Basso** (ma non nullo) | Non genera un rischio funzionale diretto, ma aumenta il tempo di analisi e la probabilità di errore in ogni intervento futuro (un intervento su una routine "gemella" morta darebbe la falsa impressione di aver corretto il problema). |
| Adattamenti S/4HANA già applicati | **Mitigante** | La presenza di 53 blocchi di adattamento già applicati (capitolo 10.4) indica che la compatibilità sintattica/DB con S/4HANA on-premise è già stata affrontata in almeno due iterazioni precedenti: il rischio residuo riguarda quindi principalmente l'architettura Clean Core, non la mera eseguibilità su S/4HANA on-premise. |

## 13. Roadmap di modernizzazione

Coerentemente con le preferenze della metodologia SAP ICC (RAP piuttosto che sviluppo custom classico, API rilasciate, BAdI piuttosto che enhancement, estensioni side-by-side su SAP BTP dove appropriato), si propone un percorso a tre orizzonti.

### 13.1 Breve termine — Riduzione immediata del rischio (nessun cambio architetturale)

1. **Eliminare la modifica diretta di `CKIS`**, sostituendola con l'attivazione del parametro standard `S_EXPLODE_KF_TOO` di `CK_F_CSTG_STRUCTURE_EXPLOSION`, già presente (disattivato) nel codice in tutti e 4 i punti di chiamata: intervento puntuale, a basso sforzo, che rimuove il rilievo più grave dell'intera analisi.
2. **Introdurre controlli `AUTHORITY-CHECK`** coerenti con l'oggetto di autorizzazione più adeguato (ad esempio per società/centro di costo), sia per l'esecuzione del programma sia per l'azione di scrittura dell'archivio storico (`ZTCKIS`).
3. **Rimuovere i `COMMIT WORK AND WAIT`** superflui o interni ai cicli; trattandosi di un programma di sola lettura/reportistica (a eccezione della scrittura intenzionale dell'archivio storico), l'unica `COMMIT` realmente necessaria è quella collegata al salvataggio esplicito dell'istantanea.
4. **Rimuovere le 11 routine morte** e le dichiarazioni di tabella inutilizzate (`MAST`, `STPO`), e consolidare le varianti duplicate ancora attive (`get_bom_cfg` / `get_bom_cfg_aem_new`) in un'unica routine parametrizzata per società.
5. **Introdurre una copertura di test ABAP Unit** sulla logica di esplosione ed elaborazione prima di procedere con ulteriori interventi di refactoring, per prevenire regressioni.

### 13.2 Medio termine — Ammodernamento "on-stack" a parità di architettura

1. Riscrivere i costrutti obsoleti secondo le linee guida della skill `sap-abap` (tabelle interne tipizzate senza header line, `SELECT ... INTO TABLE`/espressioni inline al posto di `SELECT ... ENDSELECT`, eliminazione delle istruzioni `WRITE` residue).
2. Sostituire il meccanismo di feature-toggle artigianale basato su `ZT1016_PRM` (interrogata con valori letterali `UTENTE = 'SAP*'`) con un vero oggetto di Customizing (vista di manutenzione) o con lo Switch Framework, rendendo esplicite e governate le varianti di comportamento per società.
3. Valutare la sostituzione dell'ALV classico (`REUSE_ALV_GRID_DISPLAY`) con l'ALV Object Model (`CL_SALV_TABLE`) come passo intermedio verso un'interfaccia utente moderna.
4. Eseguire un'analisi ATC (ABAP Test Cockpit) con la variante di verifica "Cloud readiness" (`ABAP_CLOUD_DEVELOPMENT_DEFAULT`, citata nella skill `sap-btp-developer-guide`) per ottenere una baseline oggettiva e strumentale, a complemento di questa analisi manuale.

### 13.3 Lungo termine — Allineamento Clean Core / ABAP Cloud

1. Ridisegnare la logica di esplosione ed elaborazione come **RAP Business Object di sola lettura** (o servizio analitico), esponendo il risultato tramite un servizio OData rilasciato, in sostituzione della combinazione schermata di selezione classica + ALV; l'interfaccia utente può evolvere verso un'app Fiori Elements (List Report/Analytical List Page), coerentemente con le linee guida `sap-btp-developer-guide`.
2. Spostare le regole specifiche di società (attualmente `IF P_BUKRS = 'AER1'/'AEM'/'LI03'/'LI04'` cablati nel codice) in **BAdI** o in Customizing, in modo che l'aggiunta di una nuova società non richieda di modificare il programma principale.
3. Sostituire il meccanismo di integrazione via ABAP Memory (capitolo 4.2) con un'**interfaccia rilasciata e versionata** (servizio OData esposto da un RAP Business Object, o classe ABAP Cloud-released), utilizzabile sia on-stack sia da un'eventuale estensione **side-by-side su SAP BTP**, secondo il modello a domini di estensione (on-stack / side-by-side / ibrido) descritto in `02 SAP Application Extension Methodology`.
4. Valutare se le funzionalità di confronto tempi standard, classificazione ABC e archiviazione storica (capitoli 7.6, 8.6) abbiano un valore sufficientemente generico da giustificare la loro estrazione in un servizio riusabile (ad esempio un piccolo servizio CAP side-by-side su SAP BTP), separandole dal programma di reportistica specifico.

## 14. Fonti e metodologia

Questa analisi tecnica è stata condotta a partire esclusivamente dal contenuto di `Input/ZLOR0016.txt`. Per l'inquadramento metodologico (terminologia Clean Core, criteri di valutazione, raccomandazioni di modernizzazione) sono state consultate le seguenti fonti, come richiesto dalle regole di selezione delle skill:

- **`skills/sap-abap/SKILL.md`**: riferimento primario per le pratiche ABAP moderne, la compatibilità con ABAP Cloud (costrutti da evitare: `WRITE`, `DESCRIBE TABLE`, `sy-datum`/`sy-uzeit`, `MOVE ... TO`) e i pattern di modernizzazione sintattica citati al capitolo 13.2.
- **`skills/sap-btp-best-practices/SKILL.md`** e **`skills/sap-btp-developer-guide/SKILL.md`**: consultate per l'inquadramento delle opzioni di estensione side-by-side su SAP BTP, il modello RAP/ABAP Cloud e gli strumenti di verifica (ATC, variante `ABAP_CLOUD_DEVELOPMENT_DEFAULT`) citati al capitolo 13.
- **`knowledge-base/01 Discovering the Clean Core Concept.docx.md`**: fonte delle sei dimensioni Clean Core utilizzate come griglia di valutazione al capitolo 11.
- **`knowledge-base/02 SAP Application Extention Methodology.pdf.md`**: fonte della classificazione degli "extension domain" (on-stack, side-by-side, ibrido) richiamata al capitolo 13.3.

Le skill `sap-abap-cds` e `sap-btp-integration-suite` **non sono state utilizzate**, poiché il programma non contiene artefatti CDS né scenari di integrazione/iFlow; la skill `sap-btp-build-work-zone-advanced` non è pertinente in assenza di scenari di portale/launchpad.

## 15. Punti da chiarire (tecnici)

1. **Comportamento delle function module custom richiamate** (`ZZ_CHECK_ACTIVATION_HC`, `ZZ_GET_WILDCOV_FROM_TECHS`, `Z_MD_MAT_TYPE_DETERMINE`) e delle transazioni custom richiamate in drill-down (`ZCS03`, `ZCS15`): non incluse in questo estratto, quindi non analizzabili; la loro conformità Clean Core andrebbe verificata separatamente.
2. **Intenzionalità dell'incongruenza fra `ored`/`chkd` e `oret`/`chkt`** nella tabella delle combinazioni valide (capitolo 10.7): andrebbe confermato con il proprietario funzionale se la combinazione congiunta debba essere ammessa, e in tal caso la tabella `gt_report` andrebbe corretta di conseguenza.
3. **Intenzionalità della differenza fra varianti nella logica di valorizzazione "SPLIT"** (capitolo 10.6): non è possibile stabilire dal solo codice se il mancato allineamento del rollback del dicembre 2014 fra le varianti generiche e quelle della società con regole particolari sia voluto o una svista.
4. **Contenuto attuale delle tabelle di configurazione** `ZT1016_CST`, `ZT1016_PRM`, `ZT0102`, `ZCO_APL_AEMTECHS`, `ZTFUS007`: il programma mostra come vengono interrogate, ma non il loro contenuto; senza tale contenuto non è possibile determinare con certezza, ad esempio, quali società o codici programma siano effettivamente attivi in produzione.
5. **Significato organizzativo dei codici società** `AER1`, `AEM`, `LI03`, `LI04`: non deducibile dal solo codice.
6. **Reale necessità architetturale della modalità "headless"** (capitolo 4.2): non essendo incluso il programma chiamante, non è possibile confermare quali altri oggetti dipendano da questo meccanismo, informazione necessaria per valutare l'impatto di un'eventuale sostituzione con un'interfaccia rilasciata (capitolo 13.3).
7. **Origine e stato del riferimento a `check_init`**: la routine è richiamata (in codice commentato) ma non definita in questo programma; non è chiaro se sia stata rimossa intenzionalmente o se l'estrazione sia incompleta su questo punto.
8. **Livello di release/API delle function module standard utilizzate** (in particolare `CK_F_CSTG_STRUCTURE_EXPLOSION`): la verifica puntuale dello stato "rilasciato" per ABAP Cloud richiede l'accesso al catalogo API del sistema SAP di riferimento (SAP API Business Hub / ABAP Cloud API State), non disponibile in questo ambiente di analisi.
