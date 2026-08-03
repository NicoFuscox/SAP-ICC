# ZFM_LIQ_T

## Contesto

Definizione DDIC esportata dal file `STRUTTURA TABELLE.xlsx`.

Utilizzare questo file come riferimento per:

- reverse engineering del programma `ZFM_VISU_MANDATI`;
- progettazione di CDS View Entity;
- modellazione RAP;
- assessment Clean Core;
- generazione di documentazione tecnica.

## Riepilogo tecnico

- **Tabella / struttura**: `ZFM_LIQ_T`
- **Numero campi documentati**: 90
- **Campi chiave identificati**: 4

## Chiave primaria

| Campo | Elemento dati | Tipo | Lunghezza | Descrizione |
|---|---|---|---:|---|
| `MANDT` | `MANDT` | `CLNT` | 3 | Mandante |
| `FIKRS` | `FIKRS` | `CHAR` | 4 | Area finanziaria |
| `AA_LIQ` | `GJAHR` | `NUMC` | 4 | Esercizio |
| `N_LIQ` | `ZN_LIQ` | `NUMC` | 10 | Numero liquidazione |

## Campi

| CAMPO | CHIAVE | ELEMENTO DATI | TIPO | LUNGHEZZA | DESCRIZIONE |
| --- | --- | --- | --- | --- | --- |
| `MANDT` | X | `MANDT` | `CLNT` | 3 | Mandante |
| `FIKRS` | X | `FIKRS` | `CHAR` | 4 | Area finanziaria |
| `AA_LIQ` | X | `GJAHR` | `NUMC` | 4 | Esercizio |
| `N_LIQ` | X | `ZN_LIQ` | `NUMC` | 10 | Numero liquidazione |
| `DT_LIQ` |  | `ZDT_LIQ` | `DATS` | 8 | Data Liquidazione |
| `TIPO_LIQ` |  | `ZTP_LIQ` | `CHAR` | 1 | Tipo Liquidazione |
| `TIPO_IMPUTAZ` |  | `ZTIPO_IMPUTAZ` | `CHAR` | 3 | Tipo imputazione |
| `ANNO_MANDATO` |  | `ZANNO_MNDT` | `NUMC` | 4 | Anno mandato |
| `NR_MANDATO` |  | `ZMANDATO` | `NUMC` | 10 | Mandato |
| `DT_MANDATO` |  | `ZDT_MANDATO` | `DATS` | 8 | Data numerazione del mandato |
| `DT_INVIO_TESO` |  | `ZDT_INVIO_TESO` | `DATS` | 8 | Data invio tesoreria |
| `NOTE_MNDT` |  | `ZNOTE_MNDT` | `CHAR` | 128 | Note mandato |
| `IMP_LIQ` |  | `ZIMP_LIQ` | `CURR` | 15 | Importo Liquidazione |
| `IMP_NET` |  | `ZIMP_NET` | `CURR` | 15 | Importo netto |
| `IMP_RIT` |  | `ZIMP_RIT` | `CURR` | 15 | Importo ritenute |
| `TESTO_BREVE` |  | `ZTESTOLIQ` | `CHAR` | 50 | Testo breve causale liquidazione |
| `CAUSALE_LIQ` |  | `Z_CAUSALE` | `CHAR` | 180 | Causale Liquidazione |
| `ANNO_PROP` |  | `ZANNOPROP` | `NUMC` | 4 | Anno proposta |
| `NR_PROPOSTA` |  | `ZPROPOSTA` | `CHAR` | 10 | Numero Proposta |
| `DIR_PROP` |  | `ZDIR_PROP` | `CHAR` | 16 | Direzione Proposta |
| `NOTA_PROP` |  | `ZNOTA_PROPOSTA` | `CHAR` | 128 | Note Proposta |
| `TIPO_PROP` |  | `ZZTIPO_PROP` | `CHAR` | 20 | Tipo Provvedimento |
| `ZZANNO_PROV` |  | `ZANNOPROVV` | `NUMC` | 4 | Anno provvedimento |
| `ZZNUM_PROV` |  | `ZNUMPROVV` | `CHAR` | 16 | Numero provvedimento |
| `ZZTIPO_PROV` |  | `ZSTIPOPROVV` | `CHAR` | 20 | Tipo provvedimento |
| `ZZDIR_PROVV` |  | `ZDIR_PROV` | `CHAR` | 16 | Direzione Provvedimento |
| `ZZNOTE_PROVV` |  | `ZNOTE_PROVV` | `CHAR` | 128 | Note Provvedimento |
| `ZZDATA_PROVV` |  | `ZDATA_PROVVED` | `DATS` | 8 | Data provvedimento |
| `ZZANNO_PROV_PROR` |  | `ZANNOPROVVPROR` | `NUMC` | 4 | Anno provvedimento proroga |
| `ZZNUM_PROV_PROR` |  | `ZNUMPROVVPROR` | `NUMC` | 4 | Numero provvedimento proroga |
| `ZZTIPO_PROV_PROR` |  | `ZSTIPOPROVV` | `CHAR` | 10 | Tipo provvedimento |
| `ZZDIR_PROVV_PROR` |  | `ZDIRPROVVPROR` | `CHAR` | 20 | Direzione Provvedimento di proroga |
| `ZZNOTE_PROVV_PROR` |  | `ZNOTEPROVVPROR` | `CHAR` | 16 | Note Provvedimento di proroga |
| `ZZDATA_PROVV_PROR` |  | `ZDATAPRPROROG` | `CHAR` | 128 | Data provvedimento di proroga |
| `MAND_COPE` |  | `ZMAND_COPE` | `DATS` | 8 | Mandato a copertura |
| `STATO` |  | `Z_STATO` | `CHAR` | 1 | Stato della liquidazione |
| `TOT_PAG_CAP` |  | `ZTOTPAGCAP` | `CHAR` | 1 | Totale pagamenti sul Capitolo |
| `DISP_CAS_CAP` |  | `ZDISP_CAS_CAP` | `CURR` | 15 | Disponibilità di cassa sul Capitolo |
| `ZZI_IDOC` |  | `ZID_DOC` | `CURR` | 15 | ID documentale |
| `ZLIVELLO_TER` |  | `ZLIVELLO_TER` | `CHAR` | 255 | Livello territoriale. |
| `ZLOCALIZZAZIONE` |  | `ZLOCALIZZAZIONE` | `CHAR` | 12 | Localizzazione |
| `USNAM_INS` |  | `USNAM` | `CHAR` | 25 | Nome dell'utente |
| `CPUDT_INS` |  | `CPUDT` | `CHAR` | 12 | Giorno in cui è stato acquisito il documento contabile |
| `CPUTM_INS` |  | `CPUTM` | `DATS` | 8 | Ora di acquisizione |
| `USNAM_MOD` |  | `USNAM` | `TIMS` | 6 | Nome dell'utente |
| `CPUDT_MOD` |  | `CPUDT` | `CHAR` | 12 | Giorno in cui è stato acquisito il documento contabile |
| `CPUTM_MOD` |  | `CPUTM` | `DATS` | 8 | Ora di acquisizione |
| `AUTOMATICA` |  | `ZFLAG_AUTO` | `CHAR` | 1 | Creazione automatica |
| `DT_QUIET` |  | `ZDT_QUIET` | `DATS` | 8 | Data quietanzamento |
| `TIPO_BOLLO` |  | `ZTIPO_BOLLO` | `CHAR` | 1 | Tipo Bollo |
| `MANDATO_COPERTURA` |  | `ZMANDACOP` | `CHAR` | 1 | Mandato a copertura |
| `ZSFSTLISTA` |  | `ZFMSTLISTA` | `CHAR` | 2 | Stato lista |
| `DATA_ES_PAG` |  | `ZDATA_ES_PAG` | `DATS` | 8 | Data esecuzione ordine pagamento |
| `ZCONTR_GENERICI` |  | `ZCONTR_GEN` | `CHAR` | 1 | Soggetto a contributi generici |
| `ZZID_DOC` |  | `ZID_DOC` | `CHAR` | 255 | ID documentale |
| `ZUNAME_EDMA` |  | `ZUNAME_EDMA` | `CHAR` | 12 | Autore EDMA. |
| `ZZGRUPPO_FOR` |  | `ZCOD_GRUPPO` | `CHAR` | 10 | Codice Gruppo Beneficiari |
| `ZSFCUP` |  | `ZZSFCUP` | `CHAR` | 15 | Codice CUP |
| `APQ` |  | `ZAPQ` | `CHAR` | 10 | Codice Accordo Programma Quadro |
| `PAGSPESESSR` |  | `ZPAGSPESESSR` | `CHAR` | 1 | Pagamento spese SSR |
| `EXART13` |  | `ZDET_TUIR` | `CHAR` | 1 | Soggetto a Detrazione ex art.13 del TUIR |
| `RITINAIL` |  | `ZRITINAIL` | `CHAR` | 1 | Ritenuta INAIL |
| `ANNO_MANDATO_SOST` |  | `ZANNO_MNDT_SOST` | `NUMC` | 4 | Anno Mandato Sostituito |
| `NR_MANDATO_SOST` |  | `ZMANDATO_SOST` | `NUMC` | 10 | Mandato Sostituito |
| `ZCDCOMP` |  | `ZCDCOMP` | `CHAR` | 1 | Codice Mancata compensazione |
| `COD_DIR` |  | `Z_DIREZIONE` | `CHAR` | 8 | Codice Direzione |
| `ZSERVIZIO` |  | `Z_CODICE_STRUTTURA` | `CHAR` | 12 | Codice Struttura |
| `TIPO_PROT` |  | `Z_TIPO_PROTOCOLLO` | `CHAR` | 5 | Tipo Protocollo |
| `ZZANNOPROT` |  | `ZANNOPROT` | `NUMC` | 4 | Anno di Protocollo |
| `ZNUM_PROT` |  | `ZNUM_PROT` | `CHAR` | 7 | Numero protoccolo |
| `ZZCODASS` |  | `ZASSESSORATO` | `CHAR` | 2 | Assessorato |
| `ZIDARCHIVIO` |  | `ZIDARCHIVIO` | `CHAR` | 3 | Id archivio |
| `FLAG_VALID` |  | `ZFLAG_VISTO` | `CHAR` | 1 | Flag visto |
| `DATA_TRASM_RAG` |  | `Z_DT_TRASM_RAG` | `DATS` | 8 | Data trasmissione in ragioneria |
| `DATA_LIMITE_PAG` |  | `ZDT_LIMITE_PAG` | `DATS` | 8 | Data limite pagamento |
| `ZNUM_REINTEGRO` |  | `ZNUM_REINT` | `NUMC` | 10 | Numero flusso reintegro |
| `ANNO_MANDATO_IN` |  | `ZANNO_MNDT_IN` | `NUMC` | 4 | Anno Mandato Inesitato |
| `NR_MANDATO_IN` |  | `ZMANDATO_IN` | `NUMC` | 10 | Mandato Inesitato |
| `MANDATO_SPLIT` |  | `ZFM_MANDATO_SPLIT` | `CHAR` | 1 | Mandato generato da Fatt. Att. con Split Payment |
| `DATA_FIRMA` |  | `ZDATA_FIRMA` | `DATS` | 8 | Data firma |
| `USNAM_FIRMA` |  | `USNAM` | `CHAR` | 12 | Nome dell'utente |
| `ESITO_FIRMA` |  | `ICON_D` | `CHAR` | 4 | Icona in campi testo (rappres. sostitutiva, alias) |
| `CAMBIO_VALUTA` |  | `ZCAMBIO_VALUTA` | `CHAR` | 1 | Cambio valuta |
| `ID_FLUSSO` |  | `ZIDENTIFICATIVO_FLUSSO` | `CHAR` | 70 | Identificativo flusso |
| `ZID_TOKEN` |  | `ZID_TOKEN` | `RAW` | 16 | ID token |
| `REVI` |  | `ZREVISIONE` | `CHAR` | 1 | Revisione |
| `GSBER` |  | `GSBER` | `CHAR` | 4 | Settore contabile |
| `ANNO_R` |  | `GJAHR` | `NUMC` | 4 | Esercizio |
| `NUM_R` |  | `ZNUM_RENDICONTO` | `CHAR` | 10 | Numero rendiconto |
| `NO_DURC` |  | `ZNO_DURC` | `CHAR` | 1 | Gestione Esclusione DURC in Liquidazione |

## Note per ICC

- La presenza di campi chiave deve essere verificata nel sistema SAP sorgente prima della generazione definitiva degli artefatti RAP.
- Per la generazione CDS, utilizzare i tipi e le lunghezze riportate come base di partenza, validando domini, conversion exits, currency/unit references e testi brevi in SE11/ADT.
- Per Clean Core, verificare se la tabella è custom persistente, struttura di output, tabella di appoggio o vista denormalizzata prima di decidere il pattern di modernizzazione.
