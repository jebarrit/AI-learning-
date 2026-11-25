# Folketing-spillet

| Dokument | Oplysning |
| --- | --- |
| Dokument-ID | FT-PROJEKT-001 |
| Version | 0.2 |
| Status | Spilbar prototype |
| Klassifikation | Offentlig |
| Kildegrundlag | Projektejerens konceptoplæg og Folketingets Åbne Data |
| Bidrag | Projektidé fra projektejeren, dokumentation udarbejdet med AI-assistance |
| Dokumentationsmetode | [IP & Document Management v0.1](../../ip-and-document-management/README.md) |

## Formål

Folketing-spillet er et koncept for et politisk spil til voksne, hvor offentlige oplysninger om folketingsmedlemmer og partier danner grundlag for historiske sammenligninger og fiktive politiske scenarier.

## Aftalt koncept

- **Politikermatch.** Spilleren tager stilling til politiske spørgsmål og ser, hvilke folketingsmedlemmer der ligger tættest på svarene ud fra deres registrerede stemmer.
- **Blindmatch.** Spørgsmålene besvares, før politikernes navne og partier afsløres.
- **Politisk strategispil.** Spilleren kan sammensætte hold og afprøve flertal og alliancer i fiktive scenarier. Politikernes egenskaber tager udgangspunkt i deres offentlige parlamentariske virke.
- **Partier.** Partitilhørsforhold indgår i profilerne. Ved historiske afstemninger er målet at vise partiet på afstemningstidspunktet.

## Spilbar prototype

[Spil Fantasy Folketing på dansk](https://fantasy-folketing-blind-match.exuberantdoor16.chatgpt.site). Den korte runde har 12 forslag, og den fulde runde har 23. De registrerede stemmer i datasættet omfatter 192 folketingsmedlemmer og stedfortrædere. Spillet viser politikernes navn og partitilhørsforhold efter stemmematchet eller efter valg af en anonym allieret.

## Kilder og metode

| Data | Kilde | Anvendelse |
| --- | --- | --- |
| Forslag og afstemninger | [Folketingets Åbne Data](https://www.ft.dk/dokumenter/aabne_data) | Udvalgte endelige afstemninger fra folketingssamlingen 2024–25. Hvert forslag i spillet linker til den officielle tekst og de registrerede stemmer. |
| Individuelle stemmer | [Folketingets afstemningsdata](https://oda.ft.dk/api/Stemme) | Stemmematchet sammenligner for, imod og hverken for eller imod. Fravær, manglende registreringer og oversprungne forslag udelades. Mindst fem sammenlignelige stemmer kræves. |
| Partitilhørsforhold | [Folketingets aktørrelationer](https://oda.ft.dk/api/Akt%C3%B8rAkt%C3%B8r) | Profilen bruger gruppemedlemskabet ved personens seneste afgivne stemme i det fulde sæt. For personer uden afgivne stemmer bruges senest registrerede fravær. |
| Politikerprofiler | [Folketingets aktørdata](https://oda.ft.dk/api/Akt%C3%B8r) | Biografierne bruges til at vise valgkredse og, hvor det er relevant, perioder som stedfortræder og ministerposter. |

Forslagsteksterne i spillet er korte danske resuméer. Koalitionsspillet bruger registrerede stemmer som udgangspunkt, mens mandater og indrømmelser følger spillets regler.

## Idéliste

- **Temarunder.** Vælg afstemninger om fx klima, sundhed, bolig eller uddannelse.
- **Partimatch.** Sammenlign dine stemmer med partiernes og se, hvor medlemmerne stemte forskelligt.
- **Gæt udfaldet.** Forudsig, om et forslag blev vedtaget, og se derefter de faktiske stemmetal.
- **Partihistorik.** Vis, hvornår et medlem skiftede folketingsgruppe i den periode, spillet dækker.

## Åbne spørgsmål

Ambitionen er at dække folketingsmedlemmernes afstemninger fra den relevante grundlovsændring og frem. Startåret og den faktiske dækning skal afklares. Offentlige afstemninger, mandatperioder og historisk partitilhørsforhold er de første oplysninger, der skal undersøges. Udvalgsarbejde og fremsatte forslag er mulige supplementer.

Før datadækningen udvides, skal det afklares, hvilke samlinger der har tilstrækkeligt dækkende individuelle stemmeregistreringer.

## Ændringshistorik

| Version | Dato | Ændring |
| --- | --- | --- |
| 0.1 | 2026-11-25 | Første offentlige projektbeskrivelse med aftalt koncept og åbne spørgsmål. |
| 0.2 | 2026-11-25 | Tilføjede den danske prototype, datakilder, matchmetode og idéliste. |
