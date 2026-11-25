# Tommelfinger Finans

| Dokument | Oplysning |
| --- | --- |
| Dokument-ID | TF-KILDER-001 |
| Version | 0.1 |
| Status | Gældende for de beskrevne beregninger |
| Klassifikation | Offentlig |
| Formål | Kildehenvisninger og beregningsgrundlag for pension og livsforsikring |
| Bidrag | Projektkrav fra projektejeren, dokumentation udarbejdet med AI-assistance |
| Dokumentationsmetode | [IP & Document Management v0.1](../../ip-and-document-management/README.md) |

## Kilder

| Emne | Kilde | Anvendelse |
| --- | --- | --- |
| Arbejdsmarkedspension | [Borger.dk · Arbejdsmarkedspensioner](https://www.borger.dk/pension-og-efterloen/Pensionssystemet-i-Danmark/Arbejdsmarkedspensioner) | Forklarer, at arbejdsgiver og lønmodtager normalt indbetaler via lønnen, og at bidragene typisk beregnes som en procentdel af lønnen. |
| Privatpension | [Borger.dk · Individuelle pensioner](https://www.borger.dk/pension-og-efterloen/pensionssystemet-i-danmark/individuelle-pensioner) | Beskriver den individuelle pensionsordning, som man selv kan oprette og indbetale til. |
| Livsforsikring | [Borger.dk · Pension er også forsikring](https://www.borger.dk/pension-og-efterloen/pensionssystemet-i-danmark/pension-er-ogsaa-forsikring) | Beskriver forsikringsdækning via pensionsordninger, herunder engangsbeløb ved død. |
| Egne dækninger | [PensionsInfo · Kontakt og vejledning](https://pensionsinfo.dk/Contact) | Viser, hvor man kan finde udbetalinger ved død og se, hvem der er registreret som begunstiget. |

## Beregningsgrundlag

- **Arbejdsmarkedspension.** Den årlige pensionsgivende løn ganges med henholdsvis arbejdsgiverens og medarbejderens sats. Beregningen bruger løn og satser fra brugerens lønseddel eller pensionsaftale. Bidragene vises før eventuelle fradrag for forsikringer og gebyrer.
- **Privatpension.** Brugerens årlige indbetaling omregnes til et gennemsnit pr. valgt periode. Beløbet hentes fra brugerens egen ordning.
- **Livsforsikring.** Beregneren summerer indtastede engangsbeløb ved død, der gælder for samme dato og scenarie. Hver police tælles én gang. En eventuel separat præmie vises som en udgift pr. år og pr. periode.
- **Perioder.** Årsbeløb fordeles over antallet af valgte perioder pr. år. Beløbene pr. periode er gennemsnit.

## Ændringshistorik

| Version | Dato | Ændring |
| --- | --- | --- |
| 0.1 | 2026-11-25 | Første offentlige kildeoversigt for arbejdsmarkedspension, privatpension og livsforsikring. |
