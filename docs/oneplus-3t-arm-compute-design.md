# OnePlus 3T – ARM-databehandler med fjernadministration

**Version:** 0.1 · offentlig designstatus · 27. november 2026  
**Status:** Forberedelse og kildeverifikation. Linux er endnu ikke installeret.

## 1. Formål og afgrænsning

Dette forsøg undersøger genbrug af en ARM-baseret enhed som generel databehandler. Enheden betragtes ud fra sine hardwareattributter, ressourcer og kapabiliteter, ikke dens oprindelige produktformål.

Målet er et minimalt Linux-system, der kan administreres fuldt ud over et sikret netværk, fungere uden en permanent tilsluttet værtscomputer og senere udføre forskellige databehandlingsopgaver. En lokal Git- og samarbejdstjeneste er en mulig første arbejdsbelastning, ikke en fast definition af enheden.

## 2. Forskel fra de tidligere OnePlus 6-forsøg

| Dimension | OnePlus 6: Android-forsøg | OnePlus 6: Mobian-forsøg | OnePlus 3T: dette design |
|---|---|---|---|
| Formål | Trådløs administration og afgrænset databehandling under Android | Native Linux og undersøgelse af hardwarefunktioner | Generel, selvstændig databehandler med fuld fjernadministration |
| Grundsystem | Android 11, Termux, valgfri Magisk-root | Mobian, Debian 13.1 | Minimal postmarketOS, foreslået |
| Kernel | Androids eksisterende kernel | 6.12-sdm845 | Enhedsspecifik MSM8996-kernel, 6.12-serien |
| Brugerflade | Android | Phosh | Headless; ingen permanent grafisk brugerflade |
| Administration | SSH som almindelig appbruger, separat root | Linux-opstart og hardwaretest dokumenteret; fjernadministration ikke kvalificeret i den offentlige status | SSH med fuld sudo-adgang for en autoriseret administrator, også over separate netværk |
| Driftskrav | Kortvarige forsøg over lokal Wi-Fi | Opstart uden USB og filintegritet efter genstart | Selvstændig opstart, automatisk netværksgenopkobling og vedvarende drift |
| Status | Afprøvet; langtidsstabilitet åben | Afprøvet; flere hardwarefunktioner fortsat åbne | Design og build-forberedelse; ingen Linux-opstart endnu |

De tidligere resultater er dokumenteret i [Android-forsøget](oneplus-6-lab-setup.md) og [Mobian-status](oneplus-6-mobian-status.md). Deres images, bootprocedurer og SoC-specifikke kernelkonfigurationer må ikke genbruges direkte på OnePlus 3T.

## 3. Verificeret hardwaregrundlag

| Attribut | Observation |
|---|---|
| Hardwarevariant | OnePlus 3T, A3003 |
| SoC og arkitektur | MSM8996pro (Snapdragon 821), AArch64 |
| CPU | Fire kerner |
| RAM | 6 GB nominelt |
| Lager | 128 GB UFS-variant; eksisterende datapartition rapporterede 113 GiB |
| Kommunikationsmuligheder | USB; Wi-Fi-interface registreret |
| Energi | Integreret batteri; fysisk restkapacitet ikke kvalificeret |
| Udgangspunkt | OxygenOS 9.0.6, Android 9, kernel 3.18.120, låst bootloader |
| Adgang ved forundersøgelse | ADB verificeret |

Observationerne er fra en konkret enhed, ikke et fuldstændigt hardwarebenchmark. Panelvariant og UFS-sektorgeometri er endnu ikke verificeret. Wi-Fi-interfacets tilstedeværelse er ikke bevis for fungerende Linux-Wi-Fi.

## 4. Foreslået teknisk design

| Lag | Designvalg |
|---|---|
| Distribution | postmarketOS v26.06, AArch64, enhedsprofil `oneplus-oneplus3t` |
| Boot | Original bootkæde med enhedsspecifik Linux-bootintegration; lk2nd som kandidat |
| Kernel | 6.12.95 som reproduktionsgrundlag, senere vedligeholdt 6.12 LTS-revision til drift |
| Rootfs | Minimal Linux-installation, ext4 som kandidat |
| Init | systemd, hvis valgt image og enhedspakker er kompatible |
| Netværk | Automatisk Wi-Fi og sekundær USB-adgang ved klargøring |
| Fjernadministration | OpenSSH, nøglebaseret autentificering, fuld sudo for autoriseret administrator |
| Adgang på tværs af netværk | Krypteret VPN; konkret løsning ikke valgt |
| Tjenester | Modulære; Git/Forgejo med SQLite er en mulig første belastning |
| Drift | Begrænset logvækst, temperatur- og batteriovervågning, kontrolleret genstart |

Direkte offentlig eksponering af SSH og servertjenester indgår ikke i designet. Netværksadgang og administrative privilegier adskilles fra almindelige opgaver. Der offentliggøres ikke driftskonfigurationer, adgangsnøgler eller faktiske netværksadresser.

## 5. Kildespor og build-status

Den offentlige referenceimplementering [oneplus3t-pmos-server](https://github.com/arttttt/oneplus3t-pmos-server/tree/c759f298dc57ab20abeab7ff65a75efb820f7697) er undersøgt som teknisk reference, ikke som en godkendt installationsprocedure.

| Kilde | Verificeret revision |
|---|---|
| Referenceprojekt | `c759f298dc57ab20abeab7ff65a75efb820f7697` |
| Linux-fork, gren op3t/6.12 | [`4925b4e83993df143d6210fdb117d045a3bdc62e`](https://github.com/arttttt/linux-op3t/tree/4925b4e83993df143d6210fdb117d045a3bdc62e) |
| Linux-fork, gren op3t/6.12-cluster-idle | [`492a5cd37fb962aa47824d565d70ea16e92a10a8`](https://github.com/arttttt/linux-op3t/tree/492a5cd37fb962aa47824d565d70ea16e92a10a8) |
| pmaports, v26.06 | `b72b1699328f7ecb9aa171e29b755d5f02875e76` |

Kernelens Makefile ved begge fork-revisioner angiver version 6.12.95. Det dokumenterer kildens versionsangivelse, ikke en reproduceret binær eller en vellykket opstart.

Ved gennemgang af referenceprojektets [APKBUILD](https://github.com/arttttt/oneplus3t-pmos-server/blob/c759f298dc57ab20abeab7ff65a75efb820f7697/build/aports/linux-op3t/APKBUILD) blev der fundet en uoverensstemmelse mellem pakkeversion (7.2-rc1), kildecommit (6.12.95) og filnavnet i checksum-manifestet. Buildopskriften anvendes derfor ikke uændret. Kernelkonfiguration, patches, moduler, DTB, initramfs og kontrolsummer skal være indbyrdes konsistente før build og installation. Referenceprojektet rapporterer desuden opstartsproblemer med 6.19.5; dette er ikke reproduceret i forsøget.

## 6. Acceptkriterier

- Første native Linux-opstart og verificeret USB-administration.
- Stabil Wi-Fi, SSH og fuld administrativ adgang med kontrolleret autentificering.
- Fjernadgang via et andet netværk, herunder automatisk genopkobling efter netværksudfald.
- Bevaring af syntetiske testdata og automatisk tjenestestart efter genstart.
- Kontrolleret temperatur, batterihåndtering og lagerintegritet under længerevarende drift.
- Afgrænset databehandlingsopgave med uafhængig resultatverifikation.
- Afprøvet tilbageførsels- og gendannelsesvej før anvendelse af værdifulde data.

En vellykket fjernforbindelse er ikke i sig selv dokumentation for permanent drift eller fjernadgang ved et kernel- eller hardwarestop. Oplåsning og installation kan slette data eller forhindre opstart; dette er ikke en installationsvejledning.

## 7. Status og næste trin

| Arbejdsområde | Status 27. november 2026 |
|---|---|
| Hardwareidentifikation og ADB | Verificeret |
| Formål og målarkitektur | Design fastlagt |
| Offentlige kildecommits | Identificeret |
| Konsistent kernel-buildopskrift | Åben |
| Panelvariant og lagergeometri | Åben |
| Installationsimage | Ikke bygget |
| Native Linux og fjernadministration | Ikke afprøvet på denne enhed |
| Langtidsdrift | Ikke afprøvet |

Næste trin er at korrigere og verificere buildopskriften og fremstille et minimalt image til første Linux-opstart. Dokumentet opdateres med faktiske resultater efter hver relevant milepæl.

## Kilder

- [postmarketOS: OnePlus 3T](https://wiki.postmarketos.org/wiki/OnePlus_3T_(oneplus-oneplus3t))
- [postmarketOS v26.06](https://postmarketos.org/blog/2026/06/21/v26.06-release/)
- [Offentlig OnePlus 3T-serverreference](https://github.com/arttttt/oneplus3t-pmos-server)
- [Kernel.org](https://www.kernel.org/)
- [Forgejo – installationsdokumentation](https://forgejo.org/docs/latest/admin/installation/binary/)
- [IP & Document Management](../ip-and-document-management/README.md)

### Ændringshistorik

| Version | Dato | Ændring |
|---|---|---|
| 0.1 | 2026-11-27 | Formål, sammenligning med tidligere forsøg, verificeret hardware, teknisk design og buildstatus. |
