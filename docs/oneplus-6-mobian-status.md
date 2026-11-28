# OnePlus 6 – status for Mobian

**Version** 1.1 · offentlig udgave · 28. november 2026

Efter det tidligere Android-forsøg blev en OnePlus 6 A6003 (enchilada) genbrugt som Linux-testenhed med Mobian og Phosh. [Tidligere Android-forsøg](oneplus-6-lab-setup.md)

| Komponent | Afprøvet udgave |
|---|---|
| Mobian-image | `mobian-sdm845-phosh-20251002.tar.xz` |
| Distribution og kernel | Debian 13.1 Trixie, kernel `6.12-sdm845` |
| Brugerflade | Phosh `0.46.0-3`, Phoc `0.46.0-1` |
| Kamerastak | libcamera `0.4.0-7` |

Skærm og touch fungerede, og Wi-Fi kunne nå Debians pakkearkiv. Telefonen startede med USB frakoblet. Efter genstart var testfilens SHA-256 uændret.

Frontkameraet og det bageste sekundærkamera leverede billeddata i en kort test uden at gemme optagelser. Det bageste hovedkamera blev fundet, men leverede ingen billeder inden for testens tidsgrænse. Debian Wiki angiver understøttelse af hovedkameraet fra kernel 6.18. Mobiltelefoni, SMS og mobildata blev ikke testet med SIM, og automatisk rotation blev ikke bekræftet. [Debians OnePlus 6-vejledning](https://wiki.debian.org/InstallingDebianOn/OnePlus/OnePlus6) · [Mobians officielle imagearkiv](https://images.mobian.org/)

## Opfølgning – 28. november 2026

Resultaterne ovenfor er daterede laboratorieobservationer. At Linux tidligere er startet uden USB, og at en testfil har overlevet en genstart, dokumenterer ikke vedvarende stabilitet eller automatisk genoprettelse af fjernadgang. Den aktuelle tilgængelighed er ikke genverificeret i denne offentlige dokumentationsopdatering.

Ved videre afprøvning bør fjernadministration, genstart, vedvarende filintegritet og individuelle hardwarefunktioner vurderes særskilt. Ved forbindelsestab bør faktisk enhedstilstand afklares før nye installationsforsøg. Det tidligere Android-forsøg er et separat historisk referencepunkt, ikke en beskrivelse af Mobians nuværende drift.

Se også [buildmiljøet](build-environment.md) og det selvstændige [OnePlus 3T-design](oneplus-3t-arm-compute-design.md). Ingen nye hardwaretest eller ændringer er foretaget som led i denne dokumentationsrevision.

| Version | Dato | Ændring |
| --- | --- | --- |
| 1.0 | 2026-11-26 | Offentlig beskrivelse af Mobian-opstart og hardwaretest. |
| 1.1 | 2026-11-28 | Præciseret historiske observationers gyldighed og relaterede arbejdsområder. |
