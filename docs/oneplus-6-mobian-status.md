# OnePlus 6 – status for Mobian

**Version** 1.0 · offentlig udgave · 26. november 2026

Efter det tidligere Android-forsøg blev en OnePlus 6 A6003 (enchilada) genbrugt som Linux-testenhed med Mobian og Phosh. [Tidligere Android-forsøg](oneplus-6-lab-setup.md)

| Komponent | Afprøvet udgave |
|---|---|
| Mobian-image | `mobian-sdm845-phosh-20251002.tar.xz` |
| Distribution og kernel | Debian 13.1 Trixie, kernel `6.12-sdm845` |
| Brugerflade | Phosh `0.46.0-3`, Phoc `0.46.0-1` |
| Kamerastak | libcamera `0.4.0-7` |

Skærm og touch fungerede, og Wi-Fi kunne nå Debians pakkearkiv. Telefonen startede med USB frakoblet. Efter genstart var testfilens SHA-256 uændret.

Frontkameraet og det bageste sekundærkamera leverede billeddata i en kort test uden at gemme optagelser. Det bageste hovedkamera blev fundet, men leverede ingen billeder inden for testens tidsgrænse. Debian Wiki angiver understøttelse af hovedkameraet fra kernel 6.18. Mobiltelefoni, SMS og mobildata blev ikke testet med SIM, og automatisk rotation blev ikke bekræftet. [Debians OnePlus 6-vejledning](https://wiki.debian.org/InstallingDebianOn/OnePlus/OnePlus6) · [Mobians officielle imagearkiv](https://images.mobian.org/)

