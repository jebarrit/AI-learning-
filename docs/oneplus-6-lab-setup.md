# OnePlus 6 – klargøring af en trådløst administreret testtelefon

**Version:** 1.0 · offentlig udgave · 25. november 2026  
**Status:** Laboratorieprocedure, ikke produktionsinstallation  
**Formål:** Dokumentere og gentage klargøringen af en ældre OnePlus 6 til trådløs administration, med reduceret Google-afhængighed og et lokalt arbejdsområde til tekniske forsøg.

## Resultat i korte træk

En OnePlus 6 blev klargjort som en genbrugelig testtelefon: bootloaderen blev oplåst, root etableret, lokal SSH-adgang sat op med nøglebaseret autentificering, og automatisk opstart af SSH afprøvet. Google-afhængigheden blev reduceret ved at deaktivere 32 pakker og skifte browser og tastatur. Der blev oprettet et lokalt arbejdsområde og gennemført trådløse funktionstest med syntetiske data.

Guiden er organiseret efter tekniske afhængigheder; den er ikke en minutiøs kronologisk log. Alle kommandoer skal kontrolleres mod den konkrete enhed før brug.

## 1. Omfang og forudsætninger

Den afprøvede telefon var en OnePlus 6 A6003 (enchilada), 8 GB RAM, Android 11 / OxygenOS 11.1.2.2, build ONEPLUS A6003_22_211125. Værten var en Lenovo ThinkPad P51 med Windows og OpenSSH. Telefonen var ejerens reserveenhed. Der blev anvendt USB til klargøring, derefter lokal Wi-Fi. Root er ikke en forudsætning for Termux og SSH i sig selv; det var et særskilt godkendt forsøg. Telefonen beholdt Android 11; der blev ikke installeret et nyt operativsystem.

**Risici:** Oplåsning af bootloader sletter brugerdata. En forkert boot-image kan forhindre opstart. Root og oplåst bootloader reducerer sikkerheden. Brug kun en enhed, som du ejer og har udtrykkelig tilladelse til at ændre. Sørg for fysisk adgang, stabil strøm, et dataegnet USB-kabel og en verificeret gendannelsesplan. Brug ikke arbejds- eller privattelefonens nødvendige tjenester som testgrundlag. Værtens operativsystem og partitioner blev ikke ændret.

**STOP-regel:** Stands ved ukendt model, firmware, boot-slot, partition, hash, manglende sikkerhedskopi eller fejl ved opstart. Ved Google-oprydning kan en enkelt pakke udskydes efter tilbageførsel; ved fejl i grundlæggende drift skal den berørte drift først gendannes. Kør aldrig hele guiden som et uovervåget script.

## 2. Etabler arbejdsområde og hent værktøjer

Opret en ny dateret mappe på værten. Gem journal, versionsoplysninger, originale boot-images, kontrolsummer, APK-filer, testresultater og kilder. Genbrug ikke gamle filer som output, og berør ikke andre backupdrev.

| Komponent | Afprøvet version / fil | Kilde |
|---|---|---|
| Android Platform Tools | 35.0.2, adb og fastboot | https://developer.android.com/tools/releases/platform-tools |
| TWRP | `twrp-3.7.0_11-0-enchilada.img` | https://twrp.me/oneplus/oneplus6.html |
| Magisk | `Magisk-v31.1.apk` | https://github.com/topjohnwu/Magisk/releases/tag/v31.1 |
| Termux | 0.118.3, `com.termux_1002.apk` | https://f-droid.org/packages/com.termux/ |
| Termux:Boot | 0.8.1, `com.termux.boot_1000.apk` | https://f-droid.org/packages/com.termux.boot/ |
| Simple Keyboard | 6.8, `rkr.simplekeyboard.inputmethod_149.apk` | https://f-droid.org/packages/rkr.simplekeyboard.inputmethod/ |
| Fennec | 160.0.1, `org.mozilla.fennec_fdroid_1600120.apk` | https://f-droid.org/packages/org.mozilla.fennec_fdroid/ |

Brug officielle kilder og kontroller integritet, signaturer og kompatibilitet. Termux og dens tilføjelser skal komme fra samme signeringskilde. Historiske versioner er til reproduktion, ikke en anbefaling om at køre forældet software permanent. Se bilag A for de historiske kontrolsummer.

I PowerShell: `Get-FileHash -Algorithm SHA256 -LiteralPath <fil>`.

Kommandoerne nedenfor antager, at de korrekte `adb.exe` og `fastboot.exe` findes på PATH, og at arbejdsbiblioteket er den nye forsøgsmappe. Brug ellers fulde filstier. `<...>` er værdier, du skal erstatte, ikke bogstavelige kommandoargumenter.

## 3. Identifikation, backup og oplåsning

1. Kopiér personlige data ud af telefonen, og kontrollér, at kopien kan læses. Ejeren skal udtrykkeligt godkende sletning. Notér model, firmware og batteriniveau.
2. Åbn udviklerindstillinger på telefonen (tryk gentagne gange på buildnummeret). Aktivér OEM-oplåsning og USB-fejlfinding, hvis telefonen tillader det. Godkend værtens USB-debugging-nøgle fysisk på telefonen.
3. Kontrollér præcis én autoriseret enhed:

```powershell
adb devices
adb shell getprop ro.product.model
adb shell getprop ro.build.display.id
adb shell getprop ro.build.version.release
adb shell getprop ro.boot.slot_suffix
adb shell getprop ro.boot.flash.locked
adb shell dumpsys battery
```

4. Hvis bootloaderen stadig er låst, genstart i bootloader og verificér identiteten. På den afprøvede OnePlus 6 blev følgende oplåsningskommando brugt med ejerens fysiske bekræftelse:

```powershell
adb reboot bootloader
fastboot devices
fastboot getvar product
fastboot getvar current-slot
# DESTRUKTIVT: kun efter godkendt backup og bekræftelse af den konkrete enhed
fastboot oem unlock
```

5. Bekræft oplåsning på telefonens egen skærm. Telefonen nulstilles. Gennemfør Androids grundopsætning, tilslut Wi-Fi og genaktivér USB-fejlfinding. Kontrollér igen `adb devices`, model og firmware. Oplåst tilstand blev verificeret med `ro.boot.flash.locked=0` og `ro.boot.verifiedbootstate=orange`.

**Accept:** Én korrekt enhed, data sikret, Android fungerer og bootloaderstatus er kendt. Hvis bootloaderen allerede er oplåst, skal oplåsningskommandoen ikke gentages.

## 4. Boot-backup og root (valgfri, særskilt godkendelse)

Officiel vejledning: https://topjohnwu.github.io/Magisk/install og https://twrp.me/oneplus/oneplus6.html. Magisk-image skal patches fra den konkrete enheds egen boot-image på den konkrete enhed. Brug ikke den gamle patchede image på en ny telefon, selv om modelnavnet matcher.

### 4.1 Midlertidig recovery og original backup

```powershell
adb reboot bootloader
fastboot devices
fastboot getvar current-slot
fastboot boot twrp-3.7.0_11-0-enchilada.img
```

TWRP blev kun startet midlertidigt; den blev ikke flashet. Undgå systemændringer i recovery. Kontrollér recovery-ADB og de faktiske partitionslinks, før noget læses:

```powershell
adb devices
adb shell ls -l /dev/block/bootdevice/by-name/boot_a /dev/block/bootdevice/by-name/boot_b
adb shell readlink -f /dev/block/bootdevice/by-name/boot_a
adb shell readlink -f /dev/block/bootdevice/by-name/boot_b
```

På den oprindelige telefon var boot_a `/dev/block/sde11` og boot_b `/dev/block/sde39`. Det er en observation, ikke universelle adresser. Følgende læser de verificerede boot-partitioner til midlertidige filer i recovery og kopierer dem til værten. Kontrollér, at begge destinationer er nye, at recovery har plads, og at `dd`-kommandoerne afsluttes uden fejl:

```powershell
adb shell dd if=/dev/block/bootdevice/by-name/boot_a of=/tmp/lab-stock-boot-a.img
adb shell dd if=/dev/block/bootdevice/by-name/boot_b of=/tmp/lab-stock-boot-b.img
adb pull /tmp/lab-stock-boot-a.img stock-boot-a.img
adb pull /tmp/lab-stock-boot-b.img stock-boot-b.img
adb shell sha256sum /tmp/lab-stock-boot-a.img /tmp/lab-stock-boot-b.img
Get-FileHash stock-boot-a.img,stock-boot-b.img -Algorithm SHA256
Get-Item stock-boot-a.img,stock-boot-b.img | Select-Object Name,Length
```

Begge images var 67.108.864 bytes. Sammenhold størrelse og hash på værten med recovery. Gem manifest for aktivt slot, firmware, størrelse og hash. Bevar begge originale kopier. Hvis den nye enhed allerede er ændret, er en aflæsning af dens boot-partition ikke automatisk en original stock-image.

### 4.2 Patch og prøvestart

Genstart Android. Installér den verificerede Magisk-APK. Kopiér den originale image fra det aktuelle slot til telefonen. Det oprindelige slot var A:

```powershell
adb reboot
# Vent på Android og kontrollér model, slot og USB-autorisation igen.
adb install Magisk-v31.1.apk
adb push stock-boot-a.img /sdcard/Download/stock-boot-a.img
```

På telefonen: Åbn Magisk, vælg installation via patch af en image-fil, og vælg stock-boot-a.img. Kopiér den genererede fil tilbage med `adb pull /sdcard/Download/<faktisk-patchet-filnavn> <ny-lokal-fil>`. Kontrollér størrelse og SHA-256 og gem patch-manifest. Brug B-image, hvis det verificerede aktive slot er B.

Prøvestart uden at flashe:

```powershell
adb reboot bootloader
fastboot getvar current-slot
fastboot boot <patchet-image>
```

Efter fuld Android-opstart: `adb shell su -c id`. Ejeren godkender eventuelt root til Androids shell i Magisk. Kontrollér `uid=0`, normal drift, korrekt slot og `adb shell getenforce` → `Enforcing`.

### 4.3 Permanent root i laboratoriet

Kun efter særskilt godkendelse, vellykket prøvestart og verificerede originalkopier: kontrollér, at aktivt slot stadig er det forventede, og at originalernes kontrolsummer og den patchede fils kontrolsum er korrekte. På den oprindelige telefon blev kun slot A flashet:

```powershell
adb reboot bootloader
fastboot devices
fastboot getvar current-slot
# Kun hvis slot A, firmware og patchet A-image er bekræftet:
fastboot flash boot_a <patchet-image>
fastboot reboot
```

Efter genstart verificeres model, slot, root og SELinux. Kontrollér boot_a og boot_b mod patch-manifest og stock-manifest med en read-only, autoriseret metode. Slot B blev efterladt uændret. Hvis noget ikke passer, stands. Brug ikke tilfældige recovery-images, og lås ikke bootloaderen igen på en modificeret telefon. Gendannelse planlægges ud fra den konkrete firmware og Magisks officielle afinstallationsvejledning; en eventuel tilbageføring af boot_a kræver verificeret matchende originalimage og særskilt beslutning.

**Accept:** Root efter normal genstart, SELinux Enforcing, kun tilsigtet boot-slot ændret. Ingen Magisk-moduler eller root til Termux kræves.

## 5. Trådløs administration uden Google-tjenester

Androids indbyggede wireless debugging kunne ikke anvendes på den afprøvede firmware. I stedet anvendtes Termux + OpenSSH som almindelig appbruger.

### 5.1 Installer og klargør

Installér Termux og Termux:Boot fra samme verificerede F-Droid-kilde. Åbn begge apps fysisk mindst én gang. I Termux:

```sh
pkg update
pkg install openssh
umask 077
mkdir -p ~/.ssh ~/.termux/boot ~/phone-lab/bin ~/phone-lab/work ~/phone-lab/logs
chmod 700 ~/.ssh ~/.termux/boot ~/phone-lab
ssh-keygen -A
id
ip -4 addr show wlan0
```

Hvis `ip` er begrænset på Android, aflæs Wi-Fi-IP i telefonens indstillinger. Registrér faktisk brugernavn (`id -un`), UID og IP. Bruger-ID, brugernavn og IP-adresser er enhedsspecifikke og skal aflæses ved hver installation. Brug isoleret lokalnet; ingen portforwarding til internet.

### 5.2 Nøgler og serverkonfiguration

På værten genereres et separat nøglepar til forsøget:

```powershell
ssh-keygen -t ed25519 -f "$env:USERPROFILE\.ssh\oneplus-lab"
```

Behold privatnøglen på værten, med adgang kun for den autoriserede bruger. Den oprindelige laboratorienøgle var uden passphrase for manuel programstyring; i en ny installation foretrækkes passphrase eller godkendt nøgleagent, hvis det passer til formålet. En nøgle uden passphrase kræver særskilt risikovurdering og restriktiv ACL. Kopiér kun den offentlige nøgle til telefonen (f.eks. over USB til Downloads og importér fysisk i Termux). Indsæt den i `~/.ssh/lab_authorized_keys`, med klient-IP og forwarding begrænset. Én linje:

```text
from="<værtens-IP>",no-agent-forwarding,no-port-forwarding,no-X11-forwarding ssh-ed25519 <offentlig-nøgles-base64> <kommentar>
```

Sæt `chmod 600 ~/.ssh/lab_authorized_keys`. Nedenstående er en rekonstruktion af den tidligere konfiguration, som skal tilpasses og valideres på den nye telefon. Gem som `~/.ssh/lab_sshd_config`. Brug den faktiske telefon-IP, Termux-bruger og hjemmesti. Eksemplet bruger en almindelig Termux-sti:

```text
Port 8022
ListenAddress <telefon-IP>
HostKey /data/data/com.termux/files/usr/etc/ssh/ssh_host_ed25519_key
PidFile /data/data/com.termux/files/home/.ssh/lab_sshd.pid
AuthorizedKeysFile /data/data/com.termux/files/home/.ssh/lab_authorized_keys
PubkeyAuthentication yes
AuthenticationMethods publickey
PasswordAuthentication no
KbdInteractiveAuthentication no
PermitEmptyPasswords no
PermitRootLogin no
AllowUsers <termux-bruger>
DisableForwarding yes
```

Start manuelt i Termux:

```sh
sshd -t -f ~/.ssh/lab_sshd_config
sshd -f ~/.ssh/lab_sshd_config
ssh-keygen -lf "$PREFIX/etc/ssh/ssh_host_ed25519_key.pub"
```

Hent den offentlige server-hostkey over en betroet kanal (eller sammenlign fysisk vist fingerprint med en indhentet offentlig hostkey). Registrér den verificerede hostkey i en separat `oneplus-lab-known_hosts` på værten. En linje for port 8022 har format `[<telefon-IP>]:8022 ssh-ed25519 <offentlig-servernøgle>`. Brug ikke blind accept af en ukendt hostkey.

På Windows, med aktuelle værdier:

```powershell
$p = "$env:USERPROFILE\.ssh"
ssh -F NUL -o BatchMode=yes -o ConnectTimeout=5 -o IdentitiesOnly=yes -o StrictHostKeyChecking=yes -o "UserKnownHostsFile=$p\oneplus-lab-known_hosts" -i "$p\oneplus-lab" -p 8022 <termux-bruger>@<telefon-IP> 'id; getprop ro.product.model'
```

`BatchMode=yes` forudsætter, at nøglen er tilgængelig uden interaktiv indtastning, f.eks. via en godkendt agent. Kontrollér, at UID ikke er 0, at det er den rigtige telefon, og at en ukendt nøgle afvises. Termux skal ikke have root.

### 5.3 Automatisk SSH ved Android-opstart

I OxygenOS: fjern batterioptimering for både Termux og Termux:Boot; tillad baggrundsdrift. I det oprindelige forsøg var begge undtaget fra batterioptimering. Åbn Termux:Boot én gang. Opret `~/.termux/boot/10-lab-ssh` med nedenstående rekonstruerede boot-script (fra tidligere forsøgsnoter; det aktuelle eksemplar kunne ikke genlæses ved denne guides udarbejdelse):

```sh
#!/data/data/com.termux/files/usr/bin/sh
set -eu
export PREFIX=/data/data/com.termux/files/usr
export HOME=/data/data/com.termux/files/home
export PATH="$PREFIX/bin:/system/bin"
export TMPDIR="$PREFIX/tmp"
umask 077
exec >>"$HOME/.ssh/lab_boot.log" 2>&1
echo "START $(date -Iseconds)"
id
conf="$HOME/.ssh/lab_sshd_config"
pidfile="$HOME/.ssh/lab_sshd.pid"
if [ -s "$pidfile" ]; then
  pid="$(cat "$pidfile")"
  if [ "$(cat "/proc/$pid/comm" 2>/dev/null || true)" = sshd ] && kill -0 "$pid" 2>/dev/null; then
    echo ALREADY_RUNNING
    exit 0
  fi
fi
i=0
while [ "$i" -lt 12 ]; do
  if sshd -t -f "$conf" && sshd -f "$conf"; then
    echo SSH_STARTED
    exit 0
  fi
  i=$((i+1))
  sleep 5
done
echo SSH_START_FAILED
exit 1
```

`chmod 700 ~/.termux/boot/10-lab-ssh`. Test først manuelt med `sh ~/.termux/boot/10-lab-ssh`. Kontrollér `tail -n 12 ~/.ssh/lab_boot.log`. Genstart derefter kun telefonen, verificér nyt boot-ID (`cat /proc/sys/kernel/random/boot_id`) og SSH uden at åbne Termux manuelt. Test til sidst uden USB på vægoplader.

**Kendt begrænsning:** Android/OxygenOS kan stoppe Termux i baggrunden. En vellykket genstartstest er ikke bevis for vedvarende oppetid. Hvis IP ændres, skal listen-address, nøglebegrænsning og klientkonfiguration gennemgås kontrolleret. En wake-lock blev ikke aktiveret i det oprindelige forsøg.

## 6. Reducér Google-afhængighed (reversibelt)

Målet er mindst mulig Google-software uden at ødelægge laboratoriets drift, ikke fysisk fjernelse af systemfiler. Der blev identificeret 60 Google-relaterede pakker og deaktiveret 32 på den afprøvede firmware. Den fulde slutliste er i bilag B. På en ny firmware kan pakkenavne og afhængigheder være anderledes.

### 6.1 Baseline

```powershell
adb shell pm list packages --user 0
adb shell pm list packages -d --user 0
adb shell dumpsys webviewupdate
adb shell settings get global device_provisioned
adb shell settings get secure user_setup_complete
adb shell settings get secure default_input_method
adb shell getenforce
```

Gem output. Undersøg hver kandidats tidligere tilstand med `adb shell dumpsys package <pakke>` (User 0, enabled). Registrér førtilstand og tilbageførselskommando. Systemets oprindelige opsætning var afsluttet (begge setup-flags = 1). Ændr ikke disse flags.

### 6.2 Deaktivér forbrugerapps først

Brug bilag B's første 26 pakker som historisk kandidatliste. I det faktiske forløb var disse allerede deaktiveret ved den senere oprydningsbaseline; placeringen her gør det muligt at gentage proceduren med SSH som ekstra driftskontrol. Start med almindelige forbrugerapps. Play Services, GSF og Play Butik har mange afhængigheder; behandl dem til sidst i denne gruppe og kontrollér især netværk, opsætning, installerede apps og loginbehov. Disse Google-tjenester var allerede deaktiveret ved den senere oprydningsbaseline. Deaktiver én ad gangen:

```powershell
adb shell pm disable-user --user 0 <pakke>
adb shell pm list packages -d --user 0
```

Efter hver ændring: bekræft opstartstilstand, Wi-Fi, SSH-identitet, arbejdsmappen, SELinux, relevante brugerfunktioner og journal. Ved fejl tilbagefør til præcis tidligere tilstand: `adb shell pm default-state --user 0 <pakke>` hvis førtilstanden var default (0), eller `adb shell pm enable --user 0 <pakke>` hvis den var eksplicit enabled (1). Undersøg før næste ændring. Undgå `pm uninstall`, sletning af APK-filer og ændringer på systempartitionen.

### 6.3 Erstat browser og tastatur

Installér Simple Keyboard og Fennec fra verificeret F-Droid-kilde:

```powershell
adb install rkr.simplekeyboard.inputmethod_149.apk
adb install org.mozilla.fennec_fdroid_1600120.apk
adb shell ime enable rkr.simplekeyboard.inputmethod/.latin.LatinIME
adb shell ime set rkr.simplekeyboard.inputmethod/.latin.LatinIME
adb shell cmd role add-role-holder --user 0 android.app.role.BROWSER org.mozilla.fennec_fdroid
adb shell settings get secure default_input_method
adb shell cmd package resolve-activity --brief -a android.intent.action.VIEW -d https://example.org
```

Test fysisk indtastning og indlæsning af en webside, før Gboard og Chrome deaktiveres. Den oprindelige test verificerede valgt tastatur og browser samt browseropstart, men ikke selvstændigt faktisk indtastning/internetindlæsning. Kontrollér særskilt at Android WebView er en fungerende separat provider (`dumpsys webviewupdate`). På den afprøvede telefon var `com.google.android.webview` den eneste installerede provider. Den blev bevaret, men version 92.0.4515.131 var gammel og et sikkerhedsproblem.

Deaktivér derefter Gboard og Chrome hver for sig, og test mellem dem. Første Chrome-forsøg blev rullet tilbage, da SSH forsvandt; årsagssammenhæng var ikke bevist. Senere forsøg bestod. Hotword-enrollment, printservice-anbefaling og NFC-tag-appen blev også deaktiveret. NFC-tag-læsning blev ikke funktionstestet efter ændringen, så behold tag-appen, hvis NFC skal anvendes.

Bevar de 28 resterende pakker (bilag B). Konfigurationsguiden er udskudt: den kan vise en opfordring om at aktivere Google-tjenester. Afvis eller luk meddelelsen, hvis muligt, uden at manipulere provisioning-flags. Bevar adgang til nødvendige tilgængeligheds- og nødvarslingsfunktioner.

### 6.4 Skærmlås (ikke nødvendig for almindelig SSH)

Kun hvis ejeren særskilt ønsker det på en fysisk sikret reserveenhed, kan den allerede oplåste enheds skærmlås deaktiveres via Androids indstillinger. I det historiske forsøg blev `adb shell locksettings set-disabled true` anvendt og `adb shell locksettings get-disabled` verificeret. Ingen eksisterende PIN blev omgået. Bevar skærmlåsen som udgangspunkt ved ny installation. Den oprindelige test var ikke en produktionssikker konfiguration.

### 6.5 Slutkontrol

Genstart telefonen, og gentag baseline og SSH-kontrol. Kontrollér at deaktiveringer består. Frakobl USB og gentag via lokal Wi-Fi. Gem før-/efterliste og journal. Kør ikke opdatering af Android eller firmware under testen uden et nyt godkendt ændringsforløb.

## 7. Lokalt arbejdsområde og funktionstest

På telefonen oprettes `~/phone-lab/bin`, `work`, `logs` med almindelig Termux-bruger og restriktive rettigheder. Den oprindelige `bin/status` rapporterede tid, model, UID, arkitektur, oppetid, disk og RAM; batteri/temperatur var ikke tilgængelige med appens rettigheder.

En minimal ny statuskontrol (rekonstruktion, ikke den originale fil):

```sh
#!/data/data/com.termux/files/usr/bin/sh
set -eu
date -Iseconds
getprop ro.product.model
id -u
uname -m
uptime
df -k "$HOME"
grep -E '^(MemTotal|MemAvailable):' /proc/meminfo
```

Gem som `~/phone-lab/bin/status` og sæt `chmod 700`. Brug derefter den autentificerede SSH-forbindelse til at køre `"$HOME/phone-lab/bin/status"`.

### 7.1 Hash-opgave

Den oprindelige `hash-probe` var en fast SHA-256-funktion, som afviste ugyldigt opgave-ID og overskrivning. Den aktuelle fil kunne ikke hentes ved udarbejdelsen af guiden. Nedenfor er en ny, enkel funktion med samme grundprincip; den skal testes, før den bruges som erstatning. Gem som `~/phone-lab/bin/hash-probe` og sæt mode 700. Kør kun syntetiske laboratoriedata.

```sh
#!/data/data/com.termux/files/usr/bin/sh
set -eu
umask 077
[ "$#" -eq 1 ] || exit 2
id="$1"
printf '%s\n' "$id" | grep -Eq '^probe-[0-9a-f]{12}$' || exit 2
d="$HOME/phone-lab/work/$id"
[ -d "$d" ] && [ ! -L "$d" ] || exit 2
[ -f "$d/input.bin" ] && [ ! -L "$d/input.bin" ] || exit 2
bytes=$(wc -c < "$d/input.bin" | tr -d '[:space:]')
[ "$bytes" -le 1048576 ] || exit 2
hash=$(sha256sum "$d/input.bin" | cut -d ' ' -f 1)
# Noclobber: afvis eksisterende resultat. Ikke en transaktionel database.
set -C
printf '{"task":"%s","operation":"sha256","bytes":%s,"sha256":"%s"}\n' \
  "$id" "$bytes" "$hash" > "$d/result.json"
cat "$d/result.json"
```

Lav et nyt tilfældigt `probe-`-ID med 12 små hextegn. Opret en ny mappe og læg en syntetisk input.bin på højst 1 MiB i den med SSH's standardinput. Brug binærsikker overførsel fra f.eks. Python `subprocess.run(..., input=bytes)`; PowerShells almindelige tekstpipeline er ikke en pålidelig binær overførsel. Kør kun den faste hash-funktion, læs resultat-JSON og input retur, og sammenhold byteantal, ID, operation og SHA-256 med en uafhængig beregning på værten. Afvis afvigelser. Begræns SSH-kaldets ventetid (historisk 20 sekunder pr. kald; ikke dokumenteret som en absolut ressourcegrænse for fjernprocessen).

## 8. Fejlsøgning og accept

| Problem | Handling |
|---|---|
| USB-enhed unauthorized | Godkend fysisk på telefonen; verificér enhedens identitet. |
| USB-enhed mangler | Kontrollér kabel, USB-mode og driver. Gæt ikke partitioner. |
| Midlertidig boot fejler | Stop; undersøg model og image. Flash ikke som forsøg. |
| SSH connection refused | Kontrollér faktisk IP og at Termux/sshd kører. Åbn Termux fysisk og kør `sh ~/.termux/boot/10-lab-ssh`; læs boot-log. |
| SSH hostkey mismatch | Stop; verificér serverens nøgle fysisk. Slet ikke kendt hostkey blindt. |
| SSH permission denied | Kontrollér brugernavn, kilde-IP, offentlig nøgle og filrettigheder. Aktivér ikke password eller root som genvej. |
| SSH dør i baggrunden | Kontrollér begge apps' batteriundtagelser, Androids baggrundsstyring og IP. Dokumentér varighed; langtidsstabilitet skal måles. |
| Google-oprydning bryder funktion | Tilbagefør den seneste pakke til dokumenteret førtilstand. Udskyd kandidaten. |
| Browser/tastatur virker ikke | Brug USB eller fysisk adgang, og tilbagefør den relevante gamle app. |
| Batteri/temperatur kan ikke aflæses | Dokumentér som ukendt; undersøg mindst privilegeret målemetode. Termux:API var ikke installeret i forsøget. |

**Acceptskema for en gentagelse** (udfyld med dato, firmware, test-ID og faktisk resultat):

- [ ] Identitet og dokumenteret ejerautorisation
- [ ] Data-backup og original boot-backup kontrolleret
- [ ] Root efter genstart (kun hvis valgt), SELinux Enforcing
- [ ] SSH med verificeret hostkey, nøglebaseret login og almindelig UID
- [ ] Automatisk opstart og trådløs test efter frakoblet USB
- [ ] Nye tastatur- og browserfunktioner fysisk testet
- [ ] Google-pakkeliste og tilbageførselsjournal bevaret
- [ ] Hash-opgave og uafhængig verifikation bestået
- [ ] Ugyldigt opgave-ID og forsøg på overskrivning afvist
- [ ] Langtidsmåling af SSH, temperatur og batteri gennemført særskilt

**Historisk resultat:** Kortvarige kontroller og genstartstest bestod den 25. november 2026. Ved udarbejdelsen af denne guide blev en ny SSH-forbindelse til den tidligere telefonadresse afvist (banner exchange / Connection refused). Årsagen er ikke fastslået, og der er ikke foretaget nye ændringer på telefonen. Dette er yderligere grund til at holde langtidsstabilitet åben.

## 9. Dokumentation og resultat ved afslutning

Gem et installationsmanifest med model, firmware, softwareversioner, aktivt boot-slot, kontrolsummer, ændringsjournal, tilbageførselsplan og testresultater. Bevar originale boot-images sikkert og separat fra offentlig dokumentation. Offentliggør ikke privatnøgler, personlige data eller enhedsspecifikke konfigurationsfiler.

Følgende resultater blev dokumenteret i det gennemførte forsøg:

| Funktion | Resultat |
|---|---|
| Operativsystem | Android 11 / OxygenOS 11.1.2.2, uændret |
| Bootloader | Oplåst |
| Root | Magisk 31.1, verificeret efter genstart |
| Boot-partitioner | Kun aktivt slot A ændret; slot B bevaret |
| TWRP | Midlertidigt startet, ikke permanent installeret |
| SELinux | Enforcing |
| Google-relaterede pakker | 32 af 60 deaktiveret, 27 bevaret, 1 udskudt |
| Browser og tastatur | Fennec og Simple Keyboard valgt som standard |
| Skærmlås | Deaktiveret på ejerens reserveenhed |
| Trådløs administration | SSH med almindelig appbruger, verificeret uden USB |
| Automatisk opstart | Bestået efter genstart |
| Arbejdsområde | Bevaret efter genstart |
| SHA-256-opgave | Bestået med syntetiske data, herunder en test på 1 MiB |
| Langtidsstabilitet | Ikke dokumenteret; senere SSH-kontrol blev afvist |
| Batteri- og temperaturmåling via SSH | Ikke tilgængelig med de anvendte apprettigheder |

**Afgrænsning:** Dette er en historisk teknisk laboratorieprocedure, ikke en sikkerhedsgodkendt permanent installation. Den gamle WebView-version, oplåst bootloader og deaktiveret skærmlås er væsentlige risici. Telefonen er ikke fuldstændig Google-fri.

Guiden bygger på det oprindelige installationsmanifest, pakkemanifest, genstartsrapport, kontrolsummer og forsøgsnoter. Lokale filnavne og stier er neutraliseret. SSH-konfigurationen, boot-scriptet, status-scriptet og hash-funktionen er rekonstruerede eksempler, ikke byteidentiske kopier af originalerne; de er ikke efterprøvet på en ny installation. Den senere SSH-fejl forhindrede genlæsning af de installerede scripts.

## Bilag A – historiske SHA-256-kontrolsummer

Disse er referenceværdier for de konkrete filer fra forsøget, ikke universelle checksums for alle versioner. De enhedsspecifikke boot-image-kontrolsummer er udeladt af den offentlige guide. Tag og verificér egne kopier; genbrug ikke patchede boot-images fra en anden telefon.

```text
adb.exe (35.0.2)
0e606318957baac81b997ccd8ee4bcdff79964a9921da07c716aea3e8d856af7
fastboot.exe (35.0.2)
e1b1537a9f5e73c746a96f6f2fe3461477320867a4206b7ece138ac1aeef5300
twrp-3.7.0_11-0-enchilada.img
8fa82612ea70608013fe123a53ed273f128cad0b061f8ceb1dfa7022c1ec8316
Magisk-v31.1.apk
89cefac0a318afa39fb3880c45c87b036c05bc1ebf02a7a715b7f8d7c26ac6e7
com.termux_1002.apk
e6265a57eb5ca363808488e3b01955958bed93bc0c8a0d281849b363b11027ec
com.termux.boot_1000.apk
6f7cf9b94f539d3efd4af3544ff819947b49395275d8cfa7e5f80de14f3d9cf8
rkr.simplekeyboard.inputmethod_149.apk
a6224924010bd4f169d2317064ccd0c307b9560bc7d8f500551d179d4f94918a
org.mozilla.fennec_fdroid_1600120.apk
657e5cdc11fae8e14676a4f96c82f665bac792f6f989e2838700e7356656e0d8
```

## Bilag B – dokumenteret Google-pakkeliste

### Første 26 deaktiveringer (historisk manifest)

```text
com.google.android.youtube
com.google.android.googlequicksearchbox
com.google.android.apps.googleassistant
com.google.android.gm
com.google.android.apps.tachyon
com.google.android.apps.wellbeing
com.google.android.apps.docs
com.google.android.apps.maps
com.google.android.apps.photos
com.google.android.calendar
com.google.android.apps.youtube.music
com.google.android.videos
com.google.android.apps.walletnfcrel
com.google.android.projection.gearhead
com.google.android.apps.turbo
com.google.android.feedback
com.google.android.gms.location.history
com.google.android.syncadapters.contacts
com.google.android.apps.restore
com.google.android.partnersetup
com.google.android.onetimeinitializer
com.google.ar.core
com.google.mainline.telemetry
com.android.vending
com.google.android.gms
com.google.android.gsf
```

### Yderligere 6 deaktiveringer

```text
com.android.hotwordenrollment.okgoogle
com.android.hotwordenrollment.xgoogle
com.google.android.printservice.recommendation
com.google.android.inputmethod.latin
com.android.chrome
com.google.android.tag
```

### Bevarede 27 og én udskudt (28 i alt)

```text
# Netværk
com.google.android.captiveportallogin
com.google.android.networkstack
com.google.android.networkstack.permissionconfig
com.google.android.networkstack.tethering
com.oneplus.commonoverlay.com.google.android.networkstack
com.oneplus.commonoverlay.com.google.android.networkstack.cn
# Nødvarsling
com.google.android.cellbroadcastreceiver
com.google.android.cellbroadcastservice
# Kernefunktioner
com.google.android.documentsui
com.google.android.ext.services
com.google.android.ext.shared
com.google.android.modulemetadata
com.google.android.packageinstaller
com.google.android.permissioncontroller
com.google.android.providers.media.module
# Overlays
com.google.android.overlay.gmsconfig.common
com.google.android.overlay.gmsconfig.gsa
com.google.android.overlay.gmsconfig.photos
com.google.android.overlay.modules.documentsui
com.google.android.overlay.modules.ext.services
com.google.android.overlay.modules.modulemetadata.forframework
com.google.android.overlay.modules.permissioncontroller
com.google.android.overlay.modules.permissioncontroller.forframework
# Sikkerhedskonfiguration, WebView og tilgængelighed
com.google.android.configupdater
com.google.android.webview
com.google.android.marvin.talkback
com.google.android.tts
# Udskudt – opsætningsguide
com.google.android.setupwizard
```

## Bilag C – tekniske referencer

- Android Platform Tools: https://developer.android.com/tools/releases/platform-tools
- TWRP OnePlus 6: https://twrp.me/oneplus/oneplus6.html
- Magisk installation: https://topjohnwu.github.io/Magisk/install
- Termux installation: https://github.com/termux/termux-app#installation
- Termux:Boot: https://github.com/termux/termux-boot
- Android modular system: https://source.android.com/docs/core/ota/modular-system
- Android CellBroadcast: https://source.android.com/docs/core/ota/modular-system/cellbroadcast

**Slut på offentlig guide v1.0.**
