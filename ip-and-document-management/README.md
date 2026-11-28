# IP & Document Management

*En praktisk tilgang til intellektuelle værdier, dokumentstyring og kontinuerlig udvikling.*

**Version:** 0.2 · 28. november 2026

## Formål

Dokumentation er en integreret del af udviklingsarbejdet og ikke blot en registrering af det udførte arbejde.

Et projekt skal kunne forstås, efterprøves, videreudvikles og overtages af andre uden at være afhængigt af de oprindelige deltagere, deres hukommelse eller individuelle viden.

Samtidig skal projektets intellektuelle værdier, informationsintegritet og udviklingshistorik beskyttes.

## 1. Intellektuelle værdier (IP)

Arbejdet med IP begynder, når udviklingen begynder – ikke først når et produkt er færdigt.

Tilgangen omfatter:

- Identifikation og registrering af intellektuelle værdier.
- Dokumentation af ophav, bidrag og ejerskab.
- Identifikation af tredjepartsmateriale og licensbetingelser.
- Sondring mellem menneskelige og AI-assisterede bidrag.
- Klassifikation af offentlig, intern og fortrolig information.
- Bevidst stillingtagen til offentliggørelse og distribution.

Dokumentation af ophav og udviklingshistorik understøtter rettighedsafklaring, men udgør ikke i sig selv et juridisk bevis for ejerskab.

## 2. Dokumentstyring

Dokumenter betragtes som levende informationsobjekter med en livscyklus og relationer til andre dokumenter.

Den enkelte registrering omfatter, hvor det er relevant:

- Entydig identifikation.
- Formål og anvendelsesområde.
- Version og dokumentstatus.
- Kildegrundlag.
- Ændringshistorik.
- Relationer og afhængigheder.
- Godkendelser.
- Fortrolighedsklassifikation.

Der tilstræbes én autoritativ kilde frem for flere konkurrerende versioner af samme information.

## 3. Dokumentets livscyklus

Et dokument kan oprettes, videreføres, suppleres, konsolideres, erstattes eller arkiveres.

Disse handlinger har forskellige betydninger og skal derfor registreres eksplicit.

Et nyt dokument erstatter ikke automatisk et tidligere dokument. Arkivering må ikke ødelægge historik, kildegrundlag eller eksisterende afhængigheder.

## 4. Evidens og informationskvalitet

Der skelnes mellem:

- Antagelser.
- Forslag.
- Beslutninger.
- Implementeringer.
- Observationer.
- Verificerede resultater.
- Åbne problemstillinger.

En godkendelse er ikke dokumentation for en gennemført handling. En gennemført handling er heller ikke nødvendigvis dokumentation for, at det ønskede resultat er opnået.

Negative resultater, fejl og afvigelser bevares som en del af projektets vidensgrundlag.

## 5. Git og GitHub

Git anvendes som fundament for versionsstyring og sporbarhed. GitHub understøtter samarbejde, faglig dialog og kontrolleret udvikling.

| Funktion | Anvendelse |
|---|---|
| Commits | Registrering af ændringer |
| Branches | Parallel og afgrænset udvikling |
| Issues | Problemstillinger og faglig dialog |
| Pull requests | Gennemgang af ændringsforslag |
| Commit-referencer | Præcis identifikation af kildeversioner |
| Merge | Kontrolleret integration |
| Releases | Identifikation af afgrænsede, færdiggjorte versioner |

Versionshistorikken skal gøre det muligt at undersøge, hvordan og hvorfor en løsning har udviklet sig.

## 6. Integritet og bevaring

Dokumenter og data skal kunne bevares og genskabes på et verificerbart grundlag.

Kontrolsummer anvendes, hvor det er relevant, til at kontrollere informationsintegritet.

Sikkerhedskopiering, arkivering, versionsstyring og gendannelse betragtes som forskellige funktioner. En eksisterende sikkerhedskopi er ikke i sig selv bevis for, at en fuldstændig gendannelse er mulig.

## 7. Samarbejde og overdragelse

Viden skal kunne bevæge sig mellem personer, systemer og arbejdsområder uden at miste sin kontekst.

En overdragelse skal gøre det muligt for modtageren at identificere:

- Det aktuelle kildegrundlag.
- Gældende beslutninger.
- Forudsætninger og afhængigheder.
- Kendte begrænsninger.
- Uafklarede problemstillinger.

Modtagelse af information er ikke nødvendigvis ensbetydende med forståelse. Ved væsentlige overdragelser skal forståelsen kunne efterprøves.

## 8. Proportionalitet

Dokumentationskrav og kontrolmekanismer skal tilpasses den konkrete aktivitets risiko, kompleksitet og betydning.

En begrænsning i ét arbejdsområde bør ikke automatisk blokere andre aktiviteter, hvis disse kan gennemføres forsvarligt og uafhængigt.

Dokumentstyring skal understøtte udvikling, ikke blive et mål i sig selv.

## 9. Kontinuerlig forbedring

Metoden bygger på principper fra Lean, Toyota Production System, systematisk problemløsning og løbende kvalitetsudvikling.

Også selve dokumentstyringsmetoden skal kunne udfordres, efterprøves og forbedres.

Ændringer skal udspringe af et identificeret behov, konkrete erfaringer eller ny viden.

## 10. Uafhængig kontrol af AI-assisteret arbejde

AI kan anvendes som faglig kontrollant, men et AI-genereret resultat bliver ikke verificeret alene ved, at en anden AI er enig.

En generel metode til bedømmelse er at:

- Afprøve bedømmernes kriterieforståelse med fiktive eksempler og på forhånd fastlagte forventninger.
- Anvende samme bedømmelsesgrundlag og så vidt muligt skjule svarenes oprindelse.
- Indsamle uafhængige vurderinger, inden bedømmerne får adgang til hinandens resultater.
- Kontrollere beregninger, logik og relevante kilder direkte, hvor det er muligt.
- Bevare afvigelser, usikkerhed og uenigheder. Flertalsenighed er ikke i sig selv sandhedsbevis.

En lille kalibrering viser alene, hvordan bedømmerne klarede de konkrete eksempler. Den dokumenterer ikke generel kompetence eller menneskelig validering. Ved ekstern AI-anvendelse skal tjenestens datavilkår og rettigheder til input vurderes særskilt. Anonymisering erstatter ikke en frigivelsesvurdering.

## 11. Bevaring af udviklingsgrundlaget

Skeln mellem materiale, der kan genanskaffes fra offentlige kilder, og unikke originaler, der ikke uden videre kan genskabes. Prioritér dokumenteret bevaring af unikke originaler og udviklingshistorik. En rekonstruktion af kildekode er ikke det samme som gendannelse af den faktiske arbejdstilstand.

Git-versioner og samarbejdsplatformens diskussioner er forskellige informationsobjekter. Ved planlægning af bevaring bør begge vurderes sammen med ikke-committede filer og relevante data. En integritetskontrol af en backup erstatter ikke en gendannelsesprøve.

Se [buildmiljøet](../docs/build-environment.md) for et praktisk eksempel på, hvordan installation, build, funktionsprøve og dokumentation behandles som forskellige milepæle.

## Ændringshistorik

| Version | Dato | Ændring |
| --- | --- | --- |
| 0.1 | 2026-11-25 | Første offentlige metodebeskrivelse. |
| 0.2 | 2026-11-28 | Suppleret med generelle principper for AI-bedømmelse og bevaring. De ni oprindelige metodeafsnit videreføres. |

## Status

Dette er en arbejdsmetode under løbende udvikling, baseret på praktisk anvendelse i et tværfagligt teknologisk forsknings- og udviklingsprojekt.

Spørgsmål, kritik, erfaringer og forbedringsforslag er velkomne.
