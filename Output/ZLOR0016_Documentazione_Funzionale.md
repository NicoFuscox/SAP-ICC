# Documentazione Funzionale

## Programma di riferimento: ZLOR0016 ("Controllo Calcolo Costi")

> **Ambito del documento**: questa documentazione descrive **esclusivamente** il contenuto del file `Input/ZLOR0016.txt`. Nessun altro file presente nella cartella `Input` è stato consultato o utilizzato come fonte. Tutte le informazioni riportate provengono dall'analisi diretta di quanto scritto in tale programma (incluse le intestazioni descrittive e i commenti presenti nel codice); dove qualcosa non risultava chiaro o verificabile, è stato riportato nell'ultimo capitolo di questo documento invece di essere ipotizzato.

---

## 1. Che cos'è questo programma

Questo programma è uno strumento di **verifica e controllo di un Calcolo Costi già esistente** (in azienda si utilizzano anche le sigle CCST/CK40N per indicare l'attività di calcolo costi standard di un articolo). Non crea un nuovo calcolo costi e non modifica in alcun modo la ricetta/distinta base o il ciclo di lavorazione di un articolo: legge un calcolo costi già presente in azienda e lo scompone in un elenco leggibile, per permettere di verificarne il contenuto voce per voce.

Il titolo che il programma stesso assume quando viene eseguito conferma questo scopo. A seconda di cosa si è scelto di analizzare, la finestra dei risultati si intitola infatti, testualmente:

- "CONTROLLO MATERIALI (DETTAGLIO) calcolo costi"
- "CONTROLLO MATERIALI (TOTALI) calcolo costi"
- "CONTROLLO ORE (DETTAGLIO) calcolo costi"
- "CONTROLLO ORE (TOTALI) calcolo costi"
- "CONTROLLO ORE + CONTROLLO MATERIALI (DETTAGLIO) calcolo costi"
- "CONTROLLO ORE + CONTROLLO MATERIALI (TOTALI) calcolo costi"

In altre parole, il programma prende il calcolo costi di un articolo (o di un intero insieme di articoli collegati a un progetto/commessa) e lo "apre", mostrando in dettaglio:

- quali **materiali e servizi esterni** lo compongono, con le relative quantità e i relativi valori;
- quante **ore di manodopera/lavorazione** sono previste, per quale reparto/centro di lavorazione e a quale centro di costo sono attribuite, distinguendo il tempo di attrezzaggio (setup) dal tempo di lavorazione vera e propria (run);
- il tutto sia a **livello di dettaglio component per componente**, sia in forma di **totali aggregati**.

## 2. A cosa serve nella pratica

Un calcolo costi standard, una volta generato con gli strumenti SAP dedicati, resta "chiuso" all'interno delle transazioni di calcolo costi e non è semplice da consultare in modo analitico, confrontare o esportare per ulteriori verifiche. Questo programma risolve tale esigenza permettendo di:

1. **Verificare la correttezza e la completezza** di un calcolo costi già effettuato, controllando che tutti i componenti attesi siano stati effettivamente inclusi nel calcolo (il programma segnala esplicitamente eventuali materiali attesi ma non trovati in un calcolo costi valido, tramite un apposito elenco denominato "Materiali non previsti").
2. **Confrontare le ore di lavorazione calcolate** con dei tempi standard di riferimento già noti in azienda, evidenziando graficamente (con un semaforo verde/giallo/rosso) se il tempo trovato nel calcolo costi coincide, differisce oppure non ha un riferimento standard con cui confrontarlo.
3. **Classificare i materiali per importanza economica** (analisi A/B/C, spiegata al capitolo 8.6) in modo da individuare rapidamente quali componenti pesano maggiormente sul costo complessivo.
4. **Analizzare in blocco tutti i componenti collegati a un progetto/commessa tecnica**, invece di dover interrogare un articolo alla volta.
5. **Conservare una fotografia dei risultati** relativa ad un determinato anno, in modo da poterla richiamare in futuro senza dover rifare da capo l'elaborazione (utile, ad esempio, per confronti storici o per congelare una situazione di riferimento).
6. **Consultare rapidamente**, con un solo clic, altre informazioni collegate a ciascun componente: la relativa scheda articolo, la situazione di stock/fabbisogni, il ciclo di lavorazione, la distinta base e il centro di lavoro coinvolto.

## 3. Chi utilizza il programma e quando

Il programma è pensato per le funzioni aziendali che si occupano di **controllo di gestione e consuntivazione dei costi industriali** (in particolare per la verifica dei calcoli costi standard dei prodotti), tipicamente dopo che un calcolo costi è già stato elaborato con gli strumenti standard di calcolo costi (CK40N/CKW1), quando si desidera:

- controllarne il contenuto in dettaglio prima di validarlo o utilizzarlo per altre finalità gestionali;
- effettuare un confronto puntuale tra ore calcolate e tempi standard di riferimento;
- produrre un elenco dei materiali e delle ore da poter analizzare, ordinare, filtrare, stampare o esportare;
- avere una vista d'insieme su tutti i componenti che afferiscono a un determinato progetto/commessa.

## 4. Le informazioni da preparare prima di avviare l'analisi

Prima di eseguire il programma occorre avere a disposizione le seguenti informazioni:

| Informazione richiesta | Obbligatorietà | Significato |
|---|---|---|
| **Società** | Sempre obbligatoria | La società/divisione aziendale a cui appartiene il calcolo costi da controllare. Se una determinata funzionalità aziendale non risulta attiva, il programma propone automaticamente, all'apertura, la società "AER1"; il campo resta comunque modificabile. |
| **Cod. Programma** | Necessaria per individuare correttamente il calcolo costi da leggere | Codice che identifica il programma/commessa industriale di appartenenza. Se non è coerente con l'articolo/progetto scelto, il sistema non troverà l'elaborazione di riferimento e segnalerà un errore (si veda il capitolo 5). Quando si sceglie di analizzare tramite "Progetto Tecnico" (vedi sotto), questo codice viene dedotto automaticamente dalle prime tre posizioni del Progetto Tecnico inserito. |
| **Materiale** | Obbligatoria solo nella modalità "Materiale e Commessa" | Il codice dell'articolo/componente di cui si vuole controllare il calcolo costi. |
| **Commessa (WBS)** | Obbligatoria solo nella modalità "Materiale e Commessa" | L'elemento di struttura di progetto (commessa) a cui il calcolo costi dell'articolo è collegato. |
| **Progetto Tecnico** | Obbligatorio solo nella modalità "Progetto Tecnico" | Un codice tecnico che identifica un intero progetto/programma industriale, in alternativa alla coppia Materiale + Commessa. Inserendolo, il programma individua da solo **tutti** gli articoli collegati a quel progetto e li elabora in un'unica soluzione. |
| **Tipo di versione da analizzare** | Sempre richiesta (una delle tre) | Indica se analizzare la versione **Effettivo**, **Simulato** oppure **Riferimento** del calcolo costi (dettagli al capitolo 6). |
| **Tipo di elenco da produrre** | Sempre richiesta (almeno una casella) | Le caselle di scelta che stabiliscono quali elenchi produrre: Materiali Dettaglio, Ore Dettaglio (con eventuale confronto), Materiali Totali, Ore Totali (con eventuale confronto) — dettagli al capitolo 7. |

## 5. Le due modalità con cui indicare "cosa analizzare"

Il programma prevede **due modalità alternative e non combinabili** per indicare l'oggetto dell'analisi. Il sistema effettua un controllo automatico all'atto della conferma dei dati inseriti e, se le informazioni non sono coerenti con una delle due modalità previste, blocca l'elaborazione con un messaggio di avviso, chiedendo di correggere la selezione.

### Modalità A — "Progetto Tecnico"

Si compila **soltanto** il campo Progetto Tecnico, lasciando vuoti sia il campo Materiale sia il campo Commessa. Questa modalità:

- individua automaticamente **tutti** gli articoli associati a quel progetto tecnico secondo le anagrafiche aziendali;
- elabora ciascuno di questi articoli in sequenza;
- produce, alla fine dell'elaborazione, **un unico elenco complessivo** che riunisce i risultati di tutti gli articoli trovati (non un elenco separato per ciascun componente).

Se, oltre al Progetto Tecnico, si compila anche il campo Materiale e/o il campo Commessa, il programma mostra un messaggio di errore e non procede: le tre informazioni non possono essere fornite insieme.

### Modalità B — "Materiale e Commessa"

Si lasciano vuoto il campo Progetto Tecnico e si compilano **entrambi** i campi Materiale e Commessa (non è sufficiente indicarne solo uno dei due: se si compila solo il Materiale senza la Commessa, o solo la Commessa senza il Materiale, il programma segnala un errore). Questa modalità:

- analizza **un solo articolo specifico**, quello indicato, in abbinamento alla commessa indicata.

### Casi non ammessi

- Non compilare nessuno dei tre campi (Progetto Tecnico, Materiale, Commessa): il programma richiede di indicare almeno un criterio di selezione.
- Compilare il Progetto Tecnico insieme a Materiale e/o Commessa: le due modalità sono alternative, non cumulabili.
- Compilare solo il Materiale oppure solo la Commessa, senza l'altro dato e senza il Progetto Tecnico: entrambi i valori sono necessari nella modalità B.

## 6. Quale versione del calcolo costi analizzare

Il programma richiede di scegliere, tramite tre opzioni alternative, quale "fotografia" del calcolo costi analizzare:

- **Effettivo**: la versione operativa/ufficiale del calcolo costi, calcolata al momento della richiesta.
- **Simulato**: una versione di simulazione del calcolo costi (utilizzata, ad esempio, per valutazioni ipotetiche non ancora ufficiali), anch'essa calcolata al momento della richiesta.
- **Riferimento**: non viene rifatta alcuna elaborazione al momento della richiesta. Il programma richiede invece di indicare un **anno** (tramite una finestra di richiesta dedicata) e recupera i risultati **già calcolati e salvati in precedenza** per quell'anno (si veda il capitolo 9 per il funzionamento del salvataggio). Se per la combinazione di articolo/commessa e anno indicati non risulta nessun dato precedentemente salvato, il programma lo segnala subito con un avviso.

## 7. Quali elenchi si possono richiedere

Nella parte inferiore della schermata di avvio sono disponibili delle caselle di scelta che determinano quali elenchi produrre. Le combinazioni ammesse dal programma sono le seguenti:

| Combinazione scelta | Elenco prodotto |
|---|---|
| Solo "Materiali Dettaglio" | Elenco dettagliato dei soli materiali e servizi esterni |
| Solo "Ore Dettaglio" | Elenco dettagliato delle sole ore di lavorazione |
| Solo "Materiali Totali" | Elenco dei materiali aggregato per gruppo merceologico |
| Solo "Ore Totali" | Elenco delle ore aggregato per centro di costo |
| "Materiali Dettaglio" + "Ore Dettaglio" insieme | Un unico elenco combinato che riporta sia il dettaglio dei materiali sia il dettaglio delle ore |
| "Materiali Totali" + "Ore Totali" insieme | Un unico elenco combinato che riporta sia i totali dei materiali sia i totali delle ore |

Accanto alla casella "Ore Dettaglio" ed accanto alla casella "Ore Totali" è presente anche una casella aggiuntiva per richiedere il **confronto con i tempi standard di riferimento** (si veda il capitolo 8.3). A questo proposito è stata rilevata un'incongruenza descritta nel capitolo finale "Punti da chiarire", relativa alla possibilità o meno di selezionare questa casella insieme alla relativa casella "Ore Dettaglio"/"Ore Totali".

Non è possibile selezionare altre combinazioni oltre a quelle indicate in tabella (ad esempio non è previsto combinare "Materiali Dettaglio" con "Ore Totali"): qualsiasi combinazione diversa da quelle ammesse viene rifiutata con un messaggio d'errore.

## 8. Cosa succede durante l'elaborazione e cosa mostrano i risultati

### 8.1 Le fasi dell'elaborazione

1. Il programma verifica che i dati inseriti siano coerenti (si vedano i capitoli 5, 6 e 7) e che esista effettivamente un calcolo costi corrispondente a quanto richiesto; in assenza di un'elaborazione di calcolo costi corrispondente, l'attività viene interrotta con un messaggio esplicativo.
2. Se si sta analizzando un Progetto Tecnico, viene determinato l'intero elenco degli articoli ad esso collegati.
3. Per ciascun articolo da analizzare (uno soltanto nella modalità "Materiale e Commessa", eventualmente più d'uno nella modalità "Progetto Tecnico"), se non si sta lavorando in modalità Riferimento, il programma scompone il calcolo costi corrispondente in tutte le sue componenti elementari (materiali, servizi esterni, manodopera, spese generali), esplorando anche gli eventuali sotto-livelli della distinta base. Durante questa fase compare a video un indicatore di avanzamento.
4. Se è stato richiesto anche il confronto ore, viene recuperato in questa fase anche il tempo standard di riferimento per ciascuna lavorazione.
5. Se si sta analizzando più di un articolo (Progetto Tecnico con più componenti), i risultati di ciascun articolo vengono via via accumulati; l'elenco finale a video viene mostrato **soltanto dopo aver terminato l'analisi dell'ultimo articolo**, riportando i risultati di tutti gli articoli elaborati in un'unica soluzione (e non un elenco separato per ciascun articolo).
6. Viene quindi presentato a video l'elenco (o gli elenchi) richiesti, in una forma interattiva che consente di ordinare, filtrare, raggruppare ed esportare i dati, oltre a effettuare le azioni di approfondimento descritte al capitolo 8.7.

Per progetti/commesse con un numero molto elevato di componenti collegati, l'elaborazione può richiedere diversi minuti, poiché ciascun componente viene analizzato singolarmente prima di presentare il risultato complessivo.

### 8.2 Elenco "Materiali Dettaglio"

Riporta una riga per ciascun materiale, servizio esterno o quota di spese generali che compone il calcolo costi analizzato, con le seguenti informazioni principali:

- Codice e descrizione del componente (con un'indicazione tra parentesi quando si tratta di un servizio esterno "(PR.)" oppure di una quota di spese generali "(CG.)");
- Divisione/stabilimento produttivo;
- Livello della distinta base a cui si trova il componente;
- Componente "padre" a cui è direttamente collegato;
- Quantità, unità di misura, prezzo unitario, unità di riferimento del prezzo e valore totale;
- Il prezzo medio ponderato dell'articolo (se disponibile) e il relativo valore totale calcolato con tale prezzo, utile per un confronto con il valore risultante dal calcolo costi;
- Il gruppo merceologico di appartenenza;
- Quando applicabile, la classe di classificazione A/B/C (si veda il capitolo 8.6);
- Se l'analisi riguarda un Progetto Tecnico: il codice del progetto, la commessa (WBS) e l'articolo "capostipite" (l'articolo di livello più alto a cui il componente appartiene) ed il codice programma.
- Le righe relative a servizi esterni il cui valore risulta pari a zero vengono evidenziate con un colore differente, per richiamare l'attenzione su una possibile anomalia di valorizzazione.

### 8.3 Elenco "Ore Dettaglio"

Riporta una riga per ciascuna fase/operazione di lavorazione prevista dal calcolo costi, con le seguenti informazioni principali:

- Codice del componente a cui la lavorazione si riferisce e relativa descrizione;
- Divisione/stabilimento, centro di lavoro e centro di costo coinvolti;
- Il tipo di approvvigionamento e il tipo di sotto-fornitura del componente (indicatori che segnalano, ad esempio, se il componente viene prodotto internamente oppure acquistato);
- Il livello nella distinta base e un indicatore di configurazione/complessità del componente;
- Le ore di attrezzaggio (setup) e le ore di lavorazione vera e propria (run), con i relativi valori economici e il prezzo unitario applicato;
- Se non si tratta della società con regole particolari (si veda il capitolo 8.8): la quantità totale di impiego del componente e la dimensione del lotto utilizzata nel calcolo costi;
- Un numero progressivo (contatore) e la voce di costo/tipo di attività contabile associata;
- Se è stato richiesto anche il confronto con i tempi standard di riferimento: una colonna con la differenza tra il tempo calcolato e il tempo standard, oltre ad un'icona a semaforo (si veda sotto);
- Se l'analisi riguarda un Progetto Tecnico: il codice del progetto, la commessa (WBS), l'articolo capostipite e il codice programma.

**Significato del semaforo di confronto** (visibile solo se è stata richiesta la casella di confronto):

- 🟢 Verde: il tempo di lavorazione calcolato coincide con il tempo standard di riferimento noto in azienda;
- 🟡 Giallo: il tempo di lavorazione calcolato è diverso dal tempo standard di riferimento (la differenza viene riportata in un'apposita colonna);
- 🔴 Rosso: non è stato trovato alcun tempo standard di riferimento con cui confrontare il dato calcolato.

### 8.4 Elenco "Materiali Totali"

Riporta i valori dei materiali aggregati per gruppo merceologico (non più articolo per articolo), con le seguenti informazioni:

- Gruppo merceologico e relativa descrizione (breve ed estesa);
- Valore totale complessivo dei materiali appartenenti a quel gruppo;
- Se l'analisi riguarda un Progetto Tecnico: codice progetto, commessa (WBS), articolo capostipite e codice programma.

### 8.5 Elenco "Ore Totali"

Riporta i valori delle ore aggregati per centro di lavoro/centro di costo (non più operazione per operazione), con le seguenti informazioni:

- Prezzo unitario applicato, centro di lavoro, centro di costo, divisione/stabilimento e relativa descrizione;
- Ore di attrezzaggio (setup) e ore di lavorazione (run) totali, con i rispettivi valori economici;
- Se l'analisi riguarda un Progetto Tecnico: codice progetto, commessa (WBS), articolo capostipite e codice programma.

### 8.6 Classificazione A/B/C dei materiali

Dall'elenco "Materiali Dettaglio" è disponibile un'azione che ordina i materiali per valore decrescente e li suddivide in tre classi in base al loro peso economico cumulato sul totale dei soli materiali (i servizi esterni e le spese generali non partecipano a questa classificazione):

- **Classe A**: i materiali che, sommati in ordine di valore decrescente, costituiscono complessivamente il primo 70% del valore totale;
- **Classe B**: i materiali successivi, fino a raggiungere complessivamente il 90% del valore totale (cioè il 20% successivo);
- **Classe C**: i restanti materiali, fino al 100% del valore totale (l'ultimo 10%).

Questa è la classica analisi "A/B/C" (o analisi di Pareto) utilizzata per individuare rapidamente i componenti più significativi in termini di valore.

### 8.7 Azioni di approfondimento disponibili sui risultati

Sugli elenchi di **dettaglio** (Materiali Dettaglio, Ore Dettaglio) è possibile, selezionando una riga, richiamare direttamente:

- La scheda anagrafica del materiale;
- La situazione di stock e fabbisogni del materiale;
- Il ciclo di lavorazione del materiale (disponibile nell'elenco Ore Dettaglio);
- Il centro di lavoro coinvolto (disponibile nell'elenco Ore Dettaglio);
- La distinta base del materiale, in diverse varianti di visualizzazione (disponibile nell'elenco Materiali Dettaglio);
- L'elenco dei "Materiali non previsti", cioè dei componenti che risultavano attesi ma per i quali non è stato possibile reperire un calcolo costi valido (disponibile su entrambi gli elenchi di dettaglio);
- La classificazione A/B/C descritta al punto 8.6 (disponibile sull'elenco Materiali Dettaglio);
- Il salvataggio di una fotografia dei dati correnti, descritto al capitolo 9 (disponibile su entrambi gli elenchi di dettaglio).

Sugli elenchi di **totali** (Materiali Totali, Ore Totali), poiché i dati sono aggregati e non si riferiscono più a un singolo componente, la maggior parte di queste azioni di approfondimento non è disponibile.

### 8.8 Comportamenti particolari legati alla società

Per una specifica società del gruppo sono previste alcune regole di elaborazione differenti rispetto alle altre, tra cui:

- una gestione dedicata dei co-prodotti (componenti che vengono riclassificati e trattati come materiali);
- l'esposizione di un gruppo merceologico esteso, non presente per le altre società;
- l'omissione, negli elenchi totali, delle colonne relative alla quantità di impiego e alla dimensione del lotto di calcolo costi;
- una diversa gestione della valorizzazione alternativa di alcuni materiali, se abilitata da un apposito parametro di configurazione.

Per due ulteriori società del gruppo, quando si utilizza la modalità "Progetto Tecnico", il codice della commessa (WBS) individuata automaticamente viene fatto precedere da un prefisso specifico per ciascuna delle due società.

Il significato organizzativo esatto delle società coinvolte (a quale entità aziendale corrispondano i relativi codici) non è deducibile dal solo codice del programma: si veda il capitolo "Punti da chiarire".

## 9. La funzione di "fotografia" dei risultati (salvataggio per la modalità Riferimento)

Dagli elenchi di dettaglio (Materiali Dettaglio, Ore Dettaglio) è disponibile un pulsante che permette di **salvare una fotografia dei dati correnti**, relativa all'anno in corso, per l'articolo e la commessa analizzati. Questa fotografia potrà in seguito essere richiamata selezionando la modalità "Riferimento" (capitolo 6) e indicando l'anno desiderato, senza dover ripetere l'elaborazione completa.

Se per l'articolo, la commessa e l'anno in corso una fotografia risulta già salvata in precedenza, il programma lo segnala con un avviso ("Attenzione!! Dati anno in corso già registrati") e non ne salva una seconda: il salvataggio può quindi avvenire **una sola volta per anno** per ciascuna combinazione di articolo e commessa.

## 10. Utilizzo automatico da parte di altri programmi aziendali

Oltre all'utilizzo diretto e interattivo appena descritto, il programma dispone anche di una modalità pensata per essere richiamata automaticamente da altri programmi aziendali, che riutilizzano così lo stesso motore di calcolo senza dover mostrare a video la schermata di avvio o gli elenchi risultanti: in questo caso i risultati (e gli eventuali messaggi di errore) vengono resi disponibili al programma chiamante invece di essere mostrati direttamente. Questa modalità non è visibile né selezionabile dall'utente nella schermata di avvio standard.

## 11. Cose da sapere e limiti noti

- Il campo "Cod. Programma" non è formalmente obbligatorio in fase di immissione, ma è comunque necessario indicarlo correttamente affinché il programma possa individuare il calcolo costi da analizzare: se non coerente, l'elaborazione viene interrotta con un messaggio d'errore.
- Nella vista combinata "Materiali Dettaglio + Ore Dettaglio" (e analogamente in "Materiali Totali + Ore Totali") non è possibile richiedere anche il confronto con i tempi standard di riferimento: tale confronto è disponibile solo quando "Ore Dettaglio"/"Ore Totali" viene richiesto da solo.
- È presente, nella barra dei pulsanti degli elenchi di dettaglio, un pulsante aggiuntivo ("READ") che, sulla base della sola analisi del codice del programma, non risulta collegato ad alcuna azione: si veda il capitolo "Punti da chiarire".
- Per progetti tecnici collegati a un numero molto elevato di componenti, l'elaborazione può richiedere tempi di attesa significativi, in quanto ogni componente viene analizzato singolarmente prima che il risultato complessivo venga presentato.

## 12. Glossario dei termini di processo utilizzati in questo documento

| Termine | Significato |
|---|---|
| Calcolo costi (CCST) | L'elaborazione, effettuata con gli strumenti standard aziendali di calcolo costi, che determina il costo pieno di un articolo scomponendolo nei materiali, servizi e lavorazioni che lo compongono. |
| Distinta base | L'elenco strutturato, organizzato su più livelli, dei materiali e semilavorati che compongono un articolo. |
| Ciclo di lavorazione | La sequenza delle operazioni/lavorazioni necessarie per realizzare un articolo, con l'indicazione dei centri di lavoro coinvolti e dei tempi previsti. |
| Centro di lavoro | Il reparto o la risorsa produttiva presso cui viene svolta un'operazione di lavorazione. |
| Centro di costo | L'unità organizzativa a cui vengono attribuiti i costi di una lavorazione. |
| Gruppo merceologico | La categoria/famiglia commerciale a cui un materiale appartiene, utilizzata per raggruppare i materiali nei totali. |
| Ore di attrezzaggio (setup) | Il tempo necessario per predisporre una lavorazione, indipendentemente dalla quantità da produrre. |
| Ore di lavorazione (run) | Il tempo di lavorazione effettiva, proporzionale alla quantità da produrre. |
| Commessa / WBS | L'elemento della struttura di progetto a cui un'attività o un calcolo costi viene collegato. |
| Progetto Tecnico | Codice tecnico che identifica un intero progetto/programma industriale, comprendente più articoli collegati tra loro. |
| Prezzo medio ponderato | Un valore di prezzo alternativo del materiale, calcolato come media dei valori di carico, utilizzato come termine di confronto rispetto al prezzo utilizzato nel calcolo costi. |

## 13. Punti da chiarire

Come richiesto, questo capitolo elenca in modo trasparente gli aspetti che **non è stato possibile determinare con certezza** dalla sola analisi del programma, e per i quali servirebbe un chiarimento da parte di chi conosce il processo aziendale, prima di considerare completa questa documentazione:

1. **Testi e messaggi a video**: le etichette dei blocchi della schermata iniziale, il testo esatto delle caselle di scelta, i messaggi d'errore mostrati all'utente e i messaggi dell'indicatore di avanzamento sono richiamati dal programma tramite un riferimento simbolico, ma il loro contenuto testuale effettivo non è incluso in questo estratto di programma. È stato quindi possibile dedurre il significato delle caselle di scelta e delle regole di errore dal comportamento del programma e dai commenti presenti nel codice, ma andrebbe verificato a video il testo realmente mostrato agli utenti.
2. **Significato organizzativo delle società coinvolte**: il programma distingue un comportamento specifico per alcune società (identificate con dei codici) rispetto alle altre, ma dal solo codice non è possibile risalire con certezza a quale entità/stabilimento aziendale ciascun codice corrisponda. Andrebbe chiarito, per ciascuna delle società con regole particolari, a quale realtà organizzativa corrisponde e per quale motivo di business le regole di elaborazione differiscono.
3. **Combinazione tra "Ore Dettaglio"/"Ore Totali" e la relativa casella di confronto**: la casella di confronto è collocata, nella schermata di avvio, sulla stessa riga della relativa casella "Ore Dettaglio"/"Ore Totali", lasciando intendere che le due opzioni siano pensate per essere utilizzate insieme; le regole di controllo interne al programma sembrano tuttavia ammettere la casella di confronto solo se selezionata da sola, senza la corrispondente casella "Ore Dettaglio"/"Ore Totali" contemporaneamente selezionata. Andrebbe chiarito con il proprietario del processo se questo comportamento è quello effettivamente voluto, oppure se ci si aspetta che le due caselle possano essere selezionate insieme.
4. **Pulsante "READ"**: è presente nella barra dei pulsanti degli elenchi di dettaglio un pulsante aggiuntivo che, in base alla sola analisi del codice, non risulta associato ad alcuna azione. Andrebbe verificato direttamente a video se il pulsante produce un qualche effetto e, in tal caso, quale sia la funzione prevista.
5. **Significato preciso del codice "Progetto Tecnico"**: il codice è composto da più segmenti (una parte iniziale che individua il "Cod. Programma", una parte centrale che rappresenta un riferimento tecnico e una parte finale che sembra rappresentare un ulteriore sotto-codice), ma il significato di business preciso di ciascun segmento non è pienamente ricavabile dal solo codice del programma.
6. **Contenuto delle tabelle di configurazione**: alcuni comportamenti del programma dipendono da tabelle di configurazione aziendale (ad esempio quella che associa il "Cod. Programma" all'elaborazione di calcolo costi da utilizzare, o quella che consente di attivare/disattivare specifiche varianti di elaborazione). Il programma mostra come queste tabelle vengono interrogate, ma non il loro contenuto attuale: pertanto le regole di business che ne derivano (ad esempio quali valori validi esistano per il "Cod. Programma") non sono deducibili dal solo codice.
7. **Transazioni aziendali specifiche richiamate dai pulsanti "CS03" e "CS15"**: due dei pulsanti di approfondimento disponibili sull'elenco Materiali Dettaglio richiamano delle transazioni aziendali specifiche (diverse dalle corrispondenti transazioni standard SAP), il cui comportamento non è incluso in questo estratto di programma e non può quindi essere descritto con certezza in questa sede.
