# ZFM_LIQ_R

## Contesto

Definizione DDIC esportata dal file `STRUTTURA TABELLE.xlsx`.

Utilizzare questo file come riferimento per:

- reverse engineering del programma `ZFM_VISU_MANDATI`;
- progettazione di CDS View Entity;
- modellazione RAP;
- assessment Clean Core;
- generazione di documentazione tecnica.

## Riepilogo tecnico

- **Tabella / struttura**: `ZFM_LIQ_R`
- **Numero campi documentati**: 89
- **Campi chiave identificati**: 5

## Chiave primaria

| Campo | Elemento dati | Tipo | Lunghezza | Descrizione |
|---|---|---|---:|---|
| `MANDT` | `MANDT` | `CLNT` | 3 | Mandante |
| `FIKRS` | `FIKRS` | `CHAR` | 4 | Area finanziaria |
| `AA_LIQ` | `GJAHR` | `NUMC` | 4 | Esercizio |
| `N_LIQ` | `ZN_LIQ` | `NUMC` | 10 | Numero liquidazione |
| `N_RIGA_LIQ` | `ZN_RIGA_LIQ` | `NUMC` | 4 | Riga liquidazione |

## Campi

| CAMPO | CHIAVE | ELEMENTO DATI | TIPO | LUNGHEZZA | DESCRIZIONE |
| --- | --- | --- | --- | --- | --- |
| `MANDT` | X | `MANDT` | `CLNT` | 3 | Mandante |
| `FIKRS` | X | `FIKRS` | `CHAR` | 4 | Area finanziaria |
| `AA_LIQ` | X | `GJAHR` | `NUMC` | 4 | Esercizio |
| `N_LIQ` | X | `ZN_LIQ` | `NUMC` | 10 | Numero liquidazione |
| `N_RIGA_LIQ` | X | `ZN_RIGA_LIQ` | `NUMC` | 4 | Riga liquidazione |
| `FIPEX` |  | `FM_FIPEX` | `CHAR` | 24 | Posizione finanziaria |
| `FISTL` |  | `FISTL` | `CHAR` | 16 | Centro di responsabilità |
| `GEBER` |  | `BP_GEBER` | `CHAR` | 10 | Fondi |
| `IMPEGNO` |  | `KBLNR_DY` | `CHAR` | 10 | Numero documento: fondi accantonati |
| `RIGA_IMP` |  | `KBLPOS` | `NUMC` | 3 | Posizione documento: fondi accantonati |
| `ZSFCUP` |  | `ZZSFCUP` | `CHAR` | 15 | Codice CUP |
| `ZZIMP_PER` |  | `ZIMP_PER` | `CHAR` | 10 | Numero Impegno Perente |
| `RIGA_IMP_PER` |  | `KBLPOS` | `NUMC` | 3 | Posizione documento: fondi accantonati |
| `BUKRS` |  | `BUKRS` | `CHAR` | 4 | Società |
| `NUM_DOC_FI` |  | `BELNR_D` | `CHAR` | 10 | Numero del documento contabile |
| `AA_DOC_FI` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `IMP_DOC_FI` |  | `WRBTR` | `CURR` | 23 | Importo in divisa documento |
| `BLART` |  | `BLART` | `CHAR` | 2 | Tipo di documento |
| `BLDAT` |  | `BLDAT` | `DATS` | 8 | Data documento nel documento |
| `BUDAT` |  | `BUDAT` | `DATS` | 8 | Data di registrazione nel documento |
| `NUM_DOC_PAR` |  | `BELNR_C` | `CHAR` | 10 | Numero documento di pareggio |
| `AA_DOC_PAR` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `NUM_BEN` |  | `ZFM_BENEF` | `CHAR` | 10 | Beneficiario di pagamento |
| `MOD_PAG` |  | `SCHZW_BSEG` | `CHAR` | 1 | Modalità di pagamento |
| `INSTANT` |  |  | `CHAR` | 1 | SEPA INSTANT CREDIT TRANSFER |
| `BVTYP` |  | `BVTYP` | `CHAR` | 4 | Tipo banca partner |
| `IMPORTO_R` |  | `Z_IMP_RIGA_LIQ` | `CURR` | 15 | Importo riga liquidazione |
| `IMP_NET` |  | `ZIMP_NET` | `CURR` | 15 | Importo netto |
| `IMP_RIT` |  | `ZIMP_RIT` | `CURR` | 15 | Importo ritenute |
| `ZZREG_FISCALE` |  | `ZREG_FISCALE` | `CHAR` | 4 | Regime fiscale |
| `BEN_ALT` |  | `ZFM_BEN_ALT` | `CHAR` | 1 | Beneficiario alternativo (id) |
| `CODICE_BEN_ALT` |  | `ZFM_COD_BEN_ALT` | `CHAR` | 10 | Beneficiario alternativo (codice) |
| `CAUS_BEN_ALT` |  | `ZCAUS_BEN_ALT` | `CHAR` | 128 | Causale Beneficiario alternativo |
| `MOD_PAG_ALT` |  | `SCHZW_BSEG` | `CHAR` | 1 | Modalità di pagamento |
| `BVTYP_ALT` |  | `BVTYP` | `CHAR` | 4 | Tipo banca partner |
| `IMP_RIT_RIGA` |  | `ZIMP_RIT` | `CURR` | 15 | Importo ritenute |
| `IMP_NET_RIGA` |  | `ZIMP_NET` | `CURR` | 15 | Importo netto |
| `ZZSFCOD_SIOPE` |  | `ZSFCOD_SIOPE` | `CHAR` | 5 | Codice SIOPE (codice di bilancio) |
| `PAREGGIO_PARZIALE` |  | `KBLPAYPCCL` | `DEC` | 5 | Importo percentuale pareggiato (piano di pagamento) |
| `DOC_PAREGG_PARZ` |  | `ZDOC_PAREGG_PARZ` | `CHAR` | 10 | Doc. Pareggio Parziale |
| `ANNO_PAREGG_PARZ` |  | `ZANNO_PAREGG_PARZ` | `NUMC` | 4 | Anno Doc Paregg. Parz. |
| `NUMERO_QUIETANZA` |  | `ZNUM_QUIET` | `CHAR` | 22 | Numero quietanza |
| `DATA_QUIETANZA` |  | `ZDATA_QUIETANZA` | `DATS` | 8 | Data quietanza |
| `IMP_QUIET` |  | `ZIMP_QUIET` | `CURR` | 15 | Importo quietanzato |
| `Z_CONTO_FIN` |  | `Z_CONTO_FIN` | `CHAR` | 30 | Piano dei conti |
| `DATA_VALITA` |  | `ZDATAVALUTA` | `DATS` | 8 | Data Valuta |
| `ANNO_MANDATO` |  | `ZANNO_MNDT` | `NUMC` | 4 | Anno mandato |
| `NR_MANDATO` |  | `ZMANDATO` | `NUMC` | 10 | Mandato |
| `RIT_PREV` |  | `WT_WT1` | `CURR` | 23 | Importo ritenuta d'acconto in divisa documento |
| `RIT_ERAR` |  | `WT_WT1` | `CURR` | 23 | Importo ritenuta d'acconto in divisa documento |
| `RIT_VARIE` |  | `WT_WT1` | `CURR` | 23 | Importo ritenuta d'acconto in divisa documento |
| `N_PARTITA` |  | `ZNPARTITA` | `CHAR` | 10 | Numero partita |
| `NUM_DOC_MM` |  | `AWREF` | `CHAR` | 10 | Numero documento di riferimento |
| `PRENOTAZIONE` |  | `ZPRENOTAZIONE` | `CHAR` | 10 | Prenotazione |
| `POS_PRENOT` |  | `ZFM_POSIZIONE_PRENOTAZIONE` | `NUMC` | 3 | Posizione prenotazione |
| `ANNO_CERT` |  | `ZANNOCERT` | `NUMC` | 4 | Anno certificato |
| `NUM_CERT` |  | `ZNUM_CERT` | `CHAR` | 10 | Numero certificato |
| `XBLNR` |  | `XBLNR_ALT` | `CHAR` | 26 | Numero di rif. alternativo |
| `TIPO_IMPUTAZ` |  | `ZTIPO_IMPUTAZ` | `CHAR` | 3 | Tipo imputazione |
| `STATO_MAND` |  | `ZSTATO_MAND` | `CHAR` | 1 | Stato |
| `ZDIREZIONE_USNAM` |  | `Z_DIREZIONE` | `CHAR` | 8 | Codice Direzione |
| `GJAHR_DOC_IVA` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `NR_DOC_IVA` |  | `ZNPARTITA` | `CHAR` | 10 | Numero partita |
| `IMP_DOC_IVA` |  | `WRBTR` | `CURR` | 23 | Importo in divisa documento |
| `GJAHR_PRE` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `NUM_PRE` |  | `ZNPRE` | `CHAR` | 10 | Nr. preacquisizione |
| `NUM_RIGA` |  | `Z_NUM_RIGA` | `NUMC` | 4 | Numero riga |
| `EBELN` |  | `Z_EBELN` | `CHAR` | 10 | Numero del documento acquisti |
| `EBELP` |  | `Z_EBELP` | `NUMC` | 5 | Numero posizione documento d'acquisto |
| `ZZCOD_COMPLETA` |  | `ZCOD_COMPLETA` | `CHAR` | 6 | Codifica completa |
| `ZZSF_MISSIONI` |  | `ZSF_MISSIONI` | `CHAR` | 2 | Missione |
| `ZZSF_PROGRAMMI` |  | `ZSF_PROGRAMMI` | `CHAR` | 2 | Programma |
| `ZZ_TITOLO` |  | `Z_TITOLO` | `CHAR` | 1 | Titolo |
| `IBAN` |  | `IBAN` | `CHAR` | 34 | IBAN (International Bank Account Number) |
| `IBAN_ALT` |  | `IBAN` | `CHAR` | 34 | IBAN (International Bank Account Number) |
| `NR_AVVISO_PAGOPA` |  | `ZNR_AVVISO_PAGOPA` | `NUMC` | 18 | Numero avviso PAGOPA |
| `CODICE_FISCALE_ENTE` |  | `ZCOD_FISC_ENTE` | `CHAR` | 16 | Codice fiscale ente |
| `AA_GIROCONTO` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `NR_GIROCONTO` |  | `ZNR_GIROCONTO` | `CHAR` | 10 | Giroconto |
| `RIGA_ANNULLATA` |  | `Z_RIGA_ANNULLATA` | `CHAR` | 1 | Flag_riga_annullata |
| `USNAM_MOD` |  | `USNAM` | `CHAR` | 12 | Nome dell'utente |
| `CPUDT_MOD` |  | `CPUDT` | `DATS` | 8 | Giorno in cui è stato acquisito il documento contabile |
| `CPUTM_MOD` |  | `CPUTM` | `TIMS` | 6 | Ora di acquisizione |
| `CDR` |  | `ZZCOD_STR_CDR` | `CHAR` | 16 | Codice Struttura CdR |
| `NUOVO_DOC` |  | `CHAR1` | `CHAR` | 1 | Indicatore a una posizione |
| `DOC_SOSTITUITO` |  | `ZSOSTITUITO` | `CHAR` | 10 | Documento sostituito |
| `RLID` |  | `ZRLID` | `CHAR` | 20 | RLID |
| `ID_POSIZIONE_BES` |  | `ZDE_POSBES` | `CHAR` | 38 | Posizione BES |
| `ANNULLO_SINGOLO` |  | `ZANNULLO_SINGOLO` | `CHAR` | 1 | Annullo Singolo |

## Note per ICC

- La presenza di campi chiave deve essere verificata nel sistema SAP sorgente prima della generazione definitiva degli artefatti RAP.
- Per la generazione CDS, utilizzare i tipi e le lunghezze riportate come base di partenza, validando domini, conversion exits, currency/unit references e testi brevi in SE11/ADT.
- Per Clean Core, verificare se la tabella è custom persistente, struttura di output, tabella di appoggio o vista denormalizzata prima di decidere il pattern di modernizzazione.
