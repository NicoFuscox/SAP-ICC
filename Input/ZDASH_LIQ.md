# ZDASH_LIQ

## Contesto

Definizione DDIC esportata dal file `STRUTTURA TABELLE.xlsx`.

Utilizzare questo file come riferimento per:

- reverse engineering del programma `ZFM_VISU_MANDATI`;
- progettazione di CDS View Entity;
- modellazione RAP;
- assessment Clean Core;
- generazione di documentazione tecnica.

## Riepilogo tecnico

- **Tabella / struttura**: `ZDASH_LIQ`
- **Numero campi documentati**: 50
- **Campi chiave identificati**: 6

## Chiave primaria

| Campo | Elemento dati | Tipo | Lunghezza | Descrizione |
|---|---|---|---:|---|
| `MANDT` | `MANDT` | `CLNT` | 3 | Mandante |
| `ZANNO_TOKEN` | `GJAHR` | `NUMC` | 4 | Esercizio |
| `ZID_TOKEN` | `ZID_TOKEN` | `RAW` | 16 | ID token |
| `AA_LIQ` | `GJAHR` | `NUMC` | 4 | Esercizio |
| `N_LIQ` | `ZN_LIQ` | `NUMC` | 10 | Numero liquidazione |
| `N_RIGA_LIQ` | `ZN_RIGA_LIQ` | `NUMC` | 4 | Riga liquidazione |

## Campi

| CAMPO | CHIAVE | ELEMENTO DATI | TIPO | LUNGHEZZA | DESCRIZIONE |
| --- | --- | --- | --- | --- | --- |
| `MANDT` | X | `MANDT` | `CLNT` | 3 | Mandante |
| `ZANNO_TOKEN` | X | `GJAHR` | `NUMC` | 4 | Esercizio |
| `ZID_TOKEN` | X | `ZID_TOKEN` | `RAW` | 16 | ID token |
| `AA_LIQ` | X | `GJAHR` | `NUMC` | 4 | Esercizio |
| `N_LIQ` | X | `ZN_LIQ` | `NUMC` | 10 | Numero liquidazione |
| `N_RIGA_LIQ` | X | `ZN_RIGA_LIQ` | `NUMC` | 4 | Riga liquidazione |
| `DT_LIQ` |  | `ZDT_LIQ` | `DATS` | 8 | Data Liquidazione |
| `TIPO_LIQ` |  | `ZTP_LIQ` | `CHAR` | 1 | Tipo Liquidazione |
| `FIPEX` |  | `FM_FIPEX` | `CHAR` | 24 | Posizione finanziaria |
| `ZFISTL` |  | `FISTL` | `CHAR` | 16 | Centro di responsabilità |
| `GEBER` |  | `BP_GEBER` | `CHAR` | 10 | Fondi |
| `IMPEGNO` |  | `KBLNR_DY` | `CHAR` | 10 | Numero documento: fondi accantonati |
| `RIGA_IMP` |  | `KBLPOS` | `NUMC` | 3 | Posizione documento: fondi accantonati |
| `ANNO_ASSU` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `ANNO_COMP` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `ZSFCIG` |  | `ZZSFCIG` | `CHAR` | 10 | Codice CIG |
| `ZSFCUP` |  | `ZZSFCUP` | `CHAR` | 15 | Codice CUP |
| `IMPORTO_R` |  | `Z_IMP_RIGA_LIQ` | `CURR` | 15 | Importo riga liquidazione |
| `IMP_NET` |  | `ZIMP_NET` | `CURR` | 15 | Importo netto |
| `IMP_RIT` |  | `ZIMP_RIT` | `CURR` | 15 | Importo ritenute |
| `CAUSALE_LIQ` |  | `Z_CAUSALE` | `CHAR` | 180 | Causale Liquidazione |
| `TIPO_BOLLO` |  | `ZTIPO_BOLLO` | `CHAR` | 1 | Tipo Bollo |
| `NUM_BEN` |  | `ZFM_BENEF` | `CHAR` | 10 | Beneficiario di pagamento |
| `STCD1` |  | `STCD1` | `CHAR` | 16 | Cod. Fisc. |
| `STCD2` |  | `STCD2` | `CHAR` | 11 | Partita IVA 2 |
| `ZNAME` |  | `ZNAME` | `CHAR` | 140 | Beneficiario Denominazione |
| `CODICE_BEN_ALT` |  | `ZFM_COD_BEN_ALT` | `CHAR` | 10 | Beneficiario alternativo (codice) |
| `STCD1_ALT` |  | `ZSTCD1` | `CHAR` | 16 | Codice fiscale beneficiario alternativo |
| `STCD2_ALT` |  | `ZSTCD2` | `CHAR` | 11 | Partita iva beneficiario alternativo |
| `ZNAMEA` |  | `ZNAME` | `CHAR` | 140 | Beneficiario Denominazione |
| `ZTIPOL_PIGNORAMENTO` |  | `ZTIPOL_PIGNORAMENTO` | `CHAR` | 4 | Tipologia pratica |
| `ZDATA_PIGNORAMENTO` |  | `ZDATA_PIGNORAMENTO` | `DATS` | 8 | Data Apertura Pratica |
| `NUM_DOC_FI` |  | `BELNR_D` | `CHAR` | 10 | Numero del documento contabile |
| `AA_DOC_FI` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `XBLNRE_ALT` |  | `XBLNR_ALT` | `CHAR` | 26 | Numero di rif. alternativo |
| `TRIPLETTA` |  | `ZID_TRIP` | `CHAR` | 10 | ID Tripletta |
| `ANNULLATO` |  | `FMRE_ERLKZ` | `CHAR` | 1 | Codice di conclusione per posizione documento in fondi acc. |
| `NO_DURC` |  | `ZNO_DURC` | `CHAR` | 1 | Gestione Esclusione DURC in Liquidazione |
| `COD_RIT` |  | `WITHT` | `CHAR` | 2 | Codice per tipo di ritenuta d'acconto |
| `IBAN` |  | `IBAN` | `CHAR` | 34 | IBAN (International Bank Account Number) |
| `GJAHR_PRE` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `NUM_PRE` |  | `ZNPRE` | `CHAR` | 10 | Nr. preacquisizione |
| `NUM_RIGA` |  | `Z_NUM_RIGA` | `NUMC` | 4 | Numero riga |
| `NUM_PROVVED` |  | `ZSXNUMPROVV` | `CHAR` | 10 | Numero provvedimento |
| `ZZANNPR` |  | `ZZANNPR` | `NUMC` | 4 | Anno Provvedimento |
| `ZSOSP_RAG` |  | `ZSOSP_RAG` | `CHAR` | 1 | Sospensione ragioneria in corso |
| `TIPOBOLLODESCRIZIONE` |  | `Z_DESCR` | `CHAR` | 250 | Descrizione |
| `ID_POSIZIONE_BES` |  | `ZDE_POSBES` | `CHAR` | 38 | Posizione BES |
| `DATA_ES_PAG` |  | `ZDATA_ES_PAG` | `DATS` | 8 | Data esecuzione ordine pagamento |
| `ANNULLO_SINGOLO` |  | `ZANNULLO_SINGOLO` | `CHAR` | 1 | Annullo Singolo |

## Note per ICC

- La presenza di campi chiave deve essere verificata nel sistema SAP sorgente prima della generazione definitiva degli artefatti RAP.
- Per la generazione CDS, utilizzare i tipi e le lunghezze riportate come base di partenza, validando domini, conversion exits, currency/unit references e testi brevi in SE11/ADT.
- Per Clean Core, verificare se la tabella è custom persistente, struttura di output, tabella di appoggio o vista denormalizzata prima di decidere il pattern di modernizzazione.
