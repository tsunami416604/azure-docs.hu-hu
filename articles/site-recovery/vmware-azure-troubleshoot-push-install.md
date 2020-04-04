---
title: Mobilitási szolgáltatás leküldéses telepítésének hibaelhárítása az Azure Site Recovery szolgáltatással
description: Mobilitási szolgáltatások telepítési hibáinak elhárítása, amikor engedélyezi a replikációt az Azure Site Recovery-vel a vészhelyreállításhoz.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 1afd931249d4dbeda2b4b25f822837e2a564f959
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656324"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Mobilitási szolgáltatás leküldéses telepítésének hibaelhárítása

A Mobilitás szolgáltatás telepítése kulcsfontosságú lépés a replikáció engedélyezéséhez. A lépés sikere az előfeltételek teljesítésétől és a támogatott konfigurációk együttműködésétől függ. A Mobility szolgáltatás telepítése során előforduló leggyakoribb hibák a következők:

* [Hitelesítő adatok/jogosultsági hibák](#credentials-check-errorid-95107--95108)
* [Bejelentkezési hibák](#login-failures-errorid-95519-95520-95521-95522)
* [Csatlakozási hibák](#connectivity-failure-errorid-95117--97118)
* [Fájl- és nyomtatómegosztási hibák](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [A Windows Management Instrumentation (WMI) hibái](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nem támogatott operációs rendszerek](#unsupported-operating-systems)
* [Nem támogatott rendszerindítási konfigurációk](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Kötet árnyékmásolata szolgáltatás (VSS) telepítési hibák](#vss-installation-failures)
* [Eszköz neve GRUB konfigurációban az UUID eszköz helyett](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Logikai kötetkezelő (LVM) kötete](#lvm-support-from-920-version)
* [Újraindításra vonatkozó figyelmeztetések](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

A replikáció engedélyezésekor az Azure Site Recovery megpróbálja telepíteni a mobilszolgáltatás-ügynököt a virtuális gépen.When you enable replication, Azure Site Recovery tries to install the Mobility service agent on your virtual machine (VM). Ennek a folyamatnak a részeként a konfigurációs kiszolgáló megpróbál csatlakozni a virtuális géphez, és másolja az ügynököt. A sikeres telepítés hez kövesse a részletes hibaelhárítási útmutatót.

## <a name="credentials-check-errorid-95107--95108"></a>Hitelesítő adatok ellenőrzése (Hibaazonosító: 95107 & 95108)

Ellenőrizze, hogy a replikáció engedélyezése során kiválasztott felhasználói fiók érvényes és pontos-e. Az Azure Site Recovery megköveteli a **gyökérfiók** vagy a **rendszergazdai jogosultságokkal** rendelkező felhasználói fiók leküldéses telepítés végrehajtásához. Ellenkező esetben a leküldéses telepítés blokkolva lesz a forrásgépen.

Windows rendszerben (**95107-es hiba**) ellenőrizze, hogy a felhasználói fiók rendelkezik-e rendszergazdai hozzáféréssel a forrásszámítógépen, helyi fiókkal vagy tartományi fiókkal. Ha nem tartományi fiókot használ, le kell tiltania a távoli felhasználói hozzáférés vezérlését a helyi számítógépen.

* A távoli felhasználói hozzáférés vezérlőjét letiltó beállításkulcs manuális hozzáadása:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Új `DWORD`hozzáadása:`LocalAccountTokenFilterPolicy`
  * Az érték beállítása`1`

* A beállításkulcs hozzáadásához futtassa a következő parancsot egy parancssorból:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Linux (**hiba 95108**), ki kell választania a **root** fiókot a mobility service ügynök sikeres telepítéséhez. Ezenkívül az SSH Fájlátviteli protokoll (SFTP) szolgáltatásoknak futniuk kell. Az SFTP-alrendszer és a jelszóhitelesítés engedélyezése a _sshd_config_ fájlban:

1. Jelentkezzen be **gyökér** szintű felhasználóként.
1. Nyissa meg az _/etc/ssh/sshd_config fájlt,_ `PasswordAuthentication`keresse meg a programmal kezdődő sort.
1. A sor megjegyzésének kimegjegyzéstása és az érték módosítása . `yes`
1. Keresse meg a `Subsystem`, és ne fűzzön megjegyzést a sorhoz.
1. Indítsa `sshd` újra a szolgáltatást.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse [az alábbi utasításokat.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="insufficient-privileges-failure-errorid-95517"></a>Elégtelen jogosultsághiba (Hibaazonosító: 95517)

Ha a mobilügynök telepítését választott felhasználó nem rendelkezik rendszergazdai jogosultságokkal, a konfigurációs kiszolgáló/kibővített folyamatkiszolgáló nem másolhatja a Mobilügynök szoftverét a forrásgépre. Ez a hiba a hozzáférés megtagadása hibájából ered. Győződjön meg arról, hogy a felhasználói fiók rendszergazdai jogosultságokkal rendelkezik.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse [az alábbi utasításokat.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="insufficient-privileges-failure-errorid-95518"></a>Elégtelen jogosultsághiba (Hibaazonosító: 95518)

Ha az elsődleges tartomány és a munkaállomás közötti tartományi megbízhatósági kapcsolat létrehozása sikertelen, miközben megpróbál bejelentkezni a forrásgépre, a Mobilitási ügynök telepítése a 95518-as azonosítóval sikertelen lesz. Győződjön meg arról, hogy a Mobilitási ügynök telepítéséhez használt felhasználói fiók rendszergazdai jogosultságokkal rendelkezik a forrásgép elsődleges tartományán keresztül történő bejelentkezéshez.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [alábbi utasításokat.](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Bejelentkezési hibák (Hibaazonosító: 95519, 95520, 95521, 95522)

Ez a szakasz a hitelesítő adatokkal és bejelentkezési hibaüzenetekkel ismerteti a szükséges hibaüzeneteket.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>A felhasználói fiók hitelesítő adatai le vannak tiltva (Hibaazonosító: 95519)

A replikáció engedélyezése során kiválasztott felhasználói fiók le lett tiltva. A felhasználói fiók engedélyezéséhez olvassa el ezt a [cikket,](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) vagy futtassa a következő parancsot úgy, hogy a szöveges _felhasználónevet_ a tényleges felhasználónévre cseréli.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>A hitelesítő adatok több sikertelen bejelentkezési kísérlet miatt zárolva vannak (ErrorID: 95520)

A számítógép elérésére irányuló többszörös sikertelen újrapróbálkozási kísérlet zárolja a felhasználói fiókot. A hiba oka lehet:

* A konfiguráció beállítása során megadott hitelesítő adatok helytelenek.
* A replikáció engedélyezése során választott felhasználói fiók hibás.

Módosítsa a kiválasztott hitelesítő adatokat [az alábbi utasításokat](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) követve, majd próbálkozzon újra a művelettel.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>A bejelentkezési kiszolgálók nem érhetők el a forrásszámítógépen (Hibaazonosító: 95521)

Ez a hiba akkor fordul elő, ha a bejelentkezési kiszolgálók nem érhetők el a forrásszámítógépen. Ha a bejelentkezési kiszolgálók nem érhetők el, a bejelentkezési kérelmek sikertelenek lesznek, és a mobilitási ügynök nem telepíthető. A sikeres bejelentkezéshez győződjön meg arról, hogy a bejelentkezési kiszolgálók elérhetők a forrásszámítógépen, és indítsa el a Netlogon szolgáltatást. További információt a [Windows bejelentkezési forgatókönyvei című témakörben talál.](/windows-server/security/windows-authentication/windows-logon-scenarios)

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>A bejelentkezési szolgáltatás nem fut a forrásgépen (ErrorID: 95522)

A bejelentkezési szolgáltatás nem fut a forrásgépen, és a bejelentkezési kérelem sikertelenségét okozta. A mobilitási ügynök nem telepíthető. A hiba elhárításához használja az alábbi módszerek `Netlogon` egyikét a szolgáltatás indításához a forrásszámítógépen:

* Ha parancssorból szeretné elindítani `Netlogon` a `net start Netlogon`szolgáltatást, futtassa a parancsot.
* A Feladatkezelőből `Netlogon` indítsa el a szolgáltatást.

## <a name="connectivity-failure-errorid-95117--97118"></a>Kapcsolódási hiba (Hibaazonosító: 95117 & 97118)

A konfigurációs kiszolgáló/kibővített folyamatkiszolgáló megpróbál csatlakozni a forrásvirtuális géphez a mobilitási ügynök telepítéséhez. Ez a hiba akkor fordul elő, ha a forrásgép nem érhető el, mert hálózati kapcsolati problémák vannak.

A hiba elhárítása:

* Győződjön meg arról, hogy a konfigurációs kiszolgálóról pingelheti a forrásgépet. Ha a replikáció engedélyezése során a kibővített folyamatkiszolgálót választotta, győződjön meg arról, hogy pingelheti a forrásgépet a folyamatkiszolgálóról.

* A forráskiszolgáló gépének parancssorából a konfigurációs kiszolgáló vagy a kibővített folyamatkiszolgáló pingeléséhez használható `Telnet` a 135-ös HTTPS-porton, ahogy az a következő parancsban is látható. Ez a parancs ellenőrzi, hogy vannak-e hálózati kapcsolati problémák vagy tűzfalportblokkolással kapcsolatos problémák.

  `telnet <CS/ scale-out PS IP address> <135>`

* Ezenkívül linuxos virtuális gép esetén:
  * Ellenőrizze, hogy telepítve vannak-e a legújabb OpenSSH, OpenSSH Server és OpenSSL csomagok.
  * Ellenőrizze és győződjön meg arról, hogy a Secure Shell (SSH) engedélyezve van, és fut-e a 22-es porton.
  * Az SFTP-szolgáltatásoknak futniuk kell. Az SFTP-alrendszer és a jelszóhitelesítés engedélyezése a _sshd_config_ fájlban:

    1. Jelentkezzen be **gyökér** szintű felhasználóként.
    1. Nyissa meg az _/etc/ssh/sshd_config_ fájlt, `PasswordAuthentication`keresse meg a .
    1. A sor megjegyzésének kimegjegyzéstása és az érték módosítása . `yes`
    1. Keresse meg a `Subsystem`, és a sor megjegyzésének fésületét
    1. Indítsa `sshd` újra a szolgáltatást.

* Egy csatlakozási kísérlet sikertelen lehet, ha egy idő után nincsenek megfelelő válaszok, vagy egy létrehozott kapcsolat nem sikerült, mert egy csatlakoztatott állomás nem tudott válaszolni.
* Ez lehet egy kapcsolattal/hálózattal/tartománnyal kapcsolatos probléma. Ez azért is lehet, mert a DNS-név megoldja a problémát vagy a TCP-port kimerülési problémája. Ellenőrizze, hogy vannak-e ilyen ismert problémák a tartományban.

## <a name="connectivity-failure-errorid-95523"></a>Kapcsolódási hiba (Hibaazonosító: 95523)

Ez a hiba akkor fordul elő, ha a forrásgép által üzemeltetett hálózat nem található, lehet, hogy törölték, vagy már nem érhető el. A hiba megoldásának egyetlen módja a hálózat létezésének biztosítása.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Fájl- és nyomtatómegosztási szolgáltatások ellenőrzése (Hibaazonosító: 95105 & 95106)

A kapcsolat ellenőrzése után ellenőrizze, hogy a Fájl- és nyomtatómegosztási szolgáltatás engedélyezve van-e a virtuális gépen. Ezek a beállítások szükségesek a mobilügynök forrásgépre történő másolásához.

**Windows 2008 R2 és korábbi verziók esetén:**

* A fájl- és nyomtatómegosztás engedélyezése a Windows tűzfalon keresztül,
  1. Nyissa meg **a Vezérlőpult** > **rendszer- és biztonsági** > **windowsos tűzfalát**. A bal oldali ablaktáblában válassza a **Speciális beállítások** > **Bejövő szabályok** lehetőséget a konzolfán.
  1. Keresse meg a fájl- és nyomtatómegosztás (NB-session-In) és a Fájl- és nyomtatómegosztás (SMB-in) szabályokat.
  1. Minden szabályhoz kattintson a jobb gombbal a szabályra, majd kattintson a **Szabály engedélyezése parancsra.**

* Fájlmegosztás engedélyezése csoportházirenddel:
  1. Nyissa **Start**meg a `gpmc.msc` Kezdőképernyő t, írja be és keressen.
  1. A navigációs ablakban nyissa meg a következő mappákat: **Helyi számítógép-házirend** > **felhasználóinak konfigurálása** > Felügyeleti**sablonok** > **Windows-összetevők** > **hálózati megosztása**.
  1. A részletek ablaktáblán kattintson duplán **a Felhasználók profilon belüli fájlok megosztásának megakadályozása**elemre.

     A csoportházirend-beállítás letiltásához és a felhasználó fájlok megosztási képességének engedélyezéséhez válassza a **Letiltva**lehetőséget.

  1. A módosítások mentéséhez kattintson az **OK** gombra.

  További információ: [Fájlmegosztás engedélyezése vagy letiltása csoportházirenddel című témakörben.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))

A Windows vagy Linux későbbi verzióiban a fájl- és nyomtatómegosztás engedélyezéséhez kövesse [a Mobilszolgáltatás telepítése a VMware virtuális gépek és a fizikai kiszolgálók vészvisszatérítéséhez](vmware-azure-install-mobility-service.md) című témakör utasításait.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>A Windows Management Instrumentation (WMI) konfigurációjának ellenőrzése (hibakód: 95103)

A fájl- és nyomtatószolgáltatások ellenőrzése után engedélyezze a WMI-szolgáltatást magán-, nyilvános és tartományi profilokhoz a tűzfalon keresztül. Ezek a beállítások szükségesek a távoli végrehajtás befejezéséhez a forrásszámítógépen.

A WMI engedélyezése:

1. Nyissa meg a **Vezérlőpult** > **biztonságát,** és válassza a **Windows tűzfal**lehetőséget.
1. Válassza **a Beállítások módosítása,** majd a **Kivételek** lapot.
1. A **Kivételek** ablakban jelölje be a Windows Management Instrumentation (WMI) jelölőnégyzetét a WMI-forgalom tűzfalon keresztüli engedélyezéséhez.

A WMI-forgalmat a parancssorból is engedélyezheti a tűzfalon keresztül a következő paranccsal:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Egyéb WMI hibaelhárítási cikkek találhatók az alábbi cikkekben.

* [Alapvető WMI-vizsgálat](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI hibaelhárítás](/windows/win32/wmisdk/wmi-troubleshooting)
* [WMI-parancsfájlokkal és WMI-szolgáltatásokkal kapcsolatos problémák elhárítása](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Nem támogatott operációs rendszerek

A hiba másik gyakori oka lehet egy nem támogatott operációs rendszer. A Mobility szolgáltatás sikeres telepítéséhez használjon támogatott operációs rendszert és kernelverziót. Kerülje a privát javítások használatát.

Az Azure Site Recovery által támogatott operációs rendszerek és kernelverziók listájának megtekintéséhez tekintse meg a [támogatási mátrixot tartalmazó dokumentumot.](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nem támogatott rendszerindító lemezkonfigurációk (Hibaazonosító: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Boot és a rendszer partíciók / kötetek nem ugyanaz a lemez (ErrorID: 95309)

A 9.20-as verzió előtt a rendszerindító és a rendszerpartíciók/kötetek különböző lemezeken nem támogatott konfigurációt jelentettek. A [9.20-as verziótól](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve ez a konfiguráció támogatott.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>A rendszerindító lemez nem érhető el (ErrorID: 95310)

A rendszerindító lemez nélküli virtuális gépek nem védhetők. A rendszerindító lemez biztosítja a virtuális gép zökkenőmentes helyreállítását feladatátvételi művelet során. A rendszerindító lemez hiánya azt eredményezi, hogy a feladatátvétel után nem sikerül elindítani a gépet. Győződjön meg arról, hogy a virtuális gép tartalmaz egy rendszerindító lemezt, és próbálja meg újra a műveletet. Emellett ugyanazon a gépen több rendszerindító lemez sem támogatott.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Több rendszerindító lemez található a forrásgépen (ErrorID: 95311)

A több rendszerindító lemezzel rendelkező virtuális gép nem [támogatott konfiguráció.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Rendszerpartíció több lemezen (ErrorID: 95313)

A 9.20-as verzió előtt a gyökérpartíció vagy a kötet beállítása több lemezen nem támogatott konfiguráció volt. A [9.20-as verziótól](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve ez a konfiguráció támogatott.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Az engedélyezési védelem nem sikerült, mivel az UUID helyett a GRUB-konfigurációban említett eszköznév (ErrorID: 95320)

### <a name="possible-cause"></a>Lehetséges ok

A Grand Unified Bootloader (GRUB) konfigurációs fájlok ( /boot/grub/menu.lst , /boot/grub.cfg , /boot/grub2/grub.cfg , vagy /etc/default/grub) konfigurációs fájlok (_/boot/grub/menu.lst_, _/boot/grub.cfg_, _/boot/grub2/grub.cfg_, vagy _/etc/default/grub)_ tartalmazhatják a paraméterek **gyökér** értékét, és az univerzálisan egyedi azonosító (UUID) helyett tényleges eszköznévként **folytatódhatnak.** A Site Recovery megbízza az UUID-megközelítést, mivel az eszköznevek a virtuális gép újraindítása során változhatnak. Előfordulhat például, hogy a virtuális gép nem érkezik meg online ugyanazzal a névvel a feladatátvételkor, és ez problémákat eredményez.

Példa:

- A következő sor a GRUB fájlból _/boot/grub2/grub.cfg:_

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- A következő sor a GRUB fájlból _/boot/grub/menu.lst:_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> A GRUB sorok az UUID helyett a paraméterek **gyökér-** és **folytatási** paramétereinek tényleges eszközneveit tartalmazzák.

### <a name="how-to-fix"></a>Hogyan erősít

Az eszközök nevét a megfelelő UUID-azonosítókra kell cserélni.

1. Keresse meg az eszköz UUID azonosítóját a parancs `blkid \<device name>`végrehajtásával.

   Példa:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Most cserélje ki az eszköz nevét az UUID-val a formátumban, mint a `root=UUID=\<UUID>`. Például, ha kicseréljük az eszköz neveku-azonosító a root és resume paraméter említett fájlok _/boot/grub2/grub.cfg_, _/boot/grub2/grub.cfg_, vagy _/etc/default/grub_ akkor a sorok a fájlokat néz ki, mint a következő sorban:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Indítsa újra a védelmet.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>A Mobility szolgáltatás telepítése újraindításra figyelmeztetve (ErrorID: 95265 & 95266)

A Site Recovery Mobility szolgáltatás számos összetevőből áll, amelyek közül az egyiket szűrő-illesztőprogramnak nevezik. A szűrőillesztő csak a rendszer újraindítása során töltődik be a rendszermemóriába. A szűrőillesztések javítása csak akkor valósítható meg, ha a rendszer újraindításakor új szűrőillesztő töltődik be.

> [!IMPORTANT]
> Ez egy figyelmeztetés, és a meglévő replikáció az új ügynökfrissítés után is működni fog. Bármikor újraindíthatja az új szűrőillesztő előnyeit, de ha nem indítja újra, a régi szűrőillesztő tovább működik. Tehát, miután egy frissítés újraindítás nélkül, kivéve a szűrő driver, **előnyeit egyéb fejlesztések és javítások Mobilitás szolgáltatás kap rájött**. Bár ajánlott, nem kötelező minden frissítés után újraindítani. Ha további információt arról, hogy mikor van kötelező az újraindítás, állítsa be az [Újraindítás mobilitás után szolgáltatás frissítési](service-updates-how-to.md#reboot-after-mobility-service-upgrade) szakasza az Azure Site Recovery szolgáltatásfrissítései című szakaszban.

> [!TIP]
>A karbantartási időszak ban a frissítések ütemezésével kapcsolatos gyakorlati tanácsok érti a [legújabb operációs rendszer/kernel támogatását](service-updates-how-to.md#support-for-latest-operating-systemskernels) az Azure Site Recovery szolgáltatásfrissítéseinek témakörében.

## <a name="lvm-support-from-920-version"></a>LVM támogatás a 9.20-as verziótól

A 9.20-as verzió előtt a Logikai kötetkezelő (LVM) csak adatlemezeken támogatott. A `/boot` partíciónak lemezpartíción, nem LVM-köteten kell lennie.

A [9.20-as verziótól](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve az [LVM operációsrendszer-lemez](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) e támogatott.

## <a name="insufficient-space-errorid-95524"></a>Nincs elegendő hely (Hibaazonosító: 95524)

A Mobilügynök forrásgépre másolásakor legalább 100 MB szabad terület szükséges. Győződjön meg arról, hogy a forrásgép rendelkezik a szükséges szabad területtel, és próbálkozzon újra a művelettel.

## <a name="vss-installation-failures"></a>VSS telepítési hibák

A Kötet árnyékmásolat-szolgáltatás (VSS) telepítése a Mobility Agent telepítésének része. Ez a szolgáltatás a folyamat során alkalmazáskonzisztens helyreállítási pontok létrehozásához használatos. A VSS telepítése során több okból is előfordulhatnak hibák. A pontos hibák azonosításához olvassa el a _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log című témakört._ A gyakori hibák és a megoldási lépések egy része a következő szakaszban kerül kiemelésre.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-hiba -2147023170 [0x800706BE] - kilépési kód 511

Ez a probléma leggyakrabban akkor fordul elő, ha víruskereső szoftver blokkolja az Azure Site Recovery-szolgáltatások működését.

A probléma megoldása:

1. Tekintse át a [víruskereső programból származó mappakizárások](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)listáját.
1. A DLL Windows rendszerben történő regisztrációjának feloldásához kövesse a víruskereső szolgáltató által közzétett irányelveket.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-hiba 7 [0x7] - kilépési kód 511

Ez a hiba egy futásidejű hiba, amely azért van, mert nincs elegendő memória a VSS telepítéséhez. Növelje a művelet sikeres befejezéséhez szükséges lemezterületet.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS hiba -2147023824 [0x80070430] - kilépési kód 517

Ez a hiba akkor fordul elő, ha az Azure Site Recovery VSS Provider szolgáltatás törlésre van [megjelölve.](/previous-versions/ms838153(v=msdn.10)) Próbálja meg manuálisan telepíteni a VSS-t a forrásgépen a következő parancs futtatásával:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-hiba -2147023841 [0x8007041F] - kilépési kód 512

Ez a hiba akkor fordul elő, ha az Azure Site Recovery VSS Provider szolgáltatás [adatbázisa zárolva](/previous-versions/ms833798(v=msdn.10))van. Próbálja meg manuálisan telepíteni a VSS-t a forrásgépen a következő parancs futtatásával a parancssorból:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Hiba esetén ellenőrizze, hogy a víruskereső programok vagy más szolgáltatások beragadtak-e **indítási** állapotba. **A kezdő** állapotban lévő folyamatok megőrizhetik az adatbázis-szolgáltatások zárolását. Ez a VSS-szolgáltató telepítésének hibáihoz vezet. Győződjön meg arról, hogy nincs szolgáltatás **indítási** állapotban, majd próbálkozzon újra a fenti művelettel.

### <a name="vss-exit-code-806"></a>VSS kilépési kód 806

Ez a hiba akkor fordul elő, ha a telepítéshez `CSScript` használt felhasználói fiók nem rendelkezik a parancs végrehajtásához szükséges engedélyekkel. Adja meg a szükséges engedélyeket a felhasználói fiókhoz a parancsfájl végrehajtásához és a művelet újrapróbálkozásához.

### <a name="other-vss-errors"></a>Egyéb VSS-hibák

Próbálja meg manuálisan telepíteni a VSS-szolgáltató szolgáltatást a forrásgépen a következő parancs futtatásával a parancssorból:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS hiba - 0x8004E00F

Ez a hiba általában a Mobility ügynök telepítése során `DCOM` `DCOM` fordul elő, mert problémák vannak benne, és kritikus állapotban van.

A hiba okának meghatározásához kövesse az alábbi eljárást.

### <a name="examine-the-installation-logs"></a>A telepítési naplók vizsgálata

1. Nyissa meg a _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log mappában_található telepítési naplót.
2. A következő hiba jelenléte jelzi a problémát:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

A probléma megoldása:

A DCOM-probléma megoldásához forduljon a [Microsoft Windows platform csapatához.](https://aka.ms/Windows_Support)

Ha a DCOM-probléma megoldódott, telepítse újra manuálisan az Azure Site Recovery VSS-szolgáltatót a következő parancs használatával egy parancssorból:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Ha az alkalmazások konzisztenciája nem kritikus a vész-helyreállítási követelmények, megkerülheti a VSS-szolgáltató telepítése.

Az Azure Site Recovery VSS-szolgáltató telepítésének megkerülése és az Azure Site Recovery VSS-szolgáltató manuális telepítése a telepítés után:

1. Telepítse a Mobilitás szolgáltatást. A telepítés sikertelen lesz a következő lépésnél: **Telepítés utáni konfigurálás**.
1. A VSS telepítésének megkerülése:
   1. Nyissa meg az Azure Site Recovery Mobility Service telepítési könyvtárát, amely a következő helyen található:

      _C:\Program Files (x86)\Microsoft Azure Site Recovery\agent_

   1. Módosítsa az Azure Site Recovery VSS-szolgáltató telepítési parancsfájljait _InMageVSSProvider_Install_ és _InMageVSSProvider_Uninstall.cmd-t,_ hogy mindig sikeres legyen a következő sorok hozzáadásával:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Végezze el a mobilitási ügynök manuális telepítését.
1. Ha a telepítés sikeres, és a következő lépésre lép, **a Configure**, távolítsa el a hozzáadott sorokat.
1. A VSS-szolgáltató telepítéséhez nyisson meg egy parancssort rendszergazdaként, és futtassa a következő parancsot:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Ellenőrizze, hogy az Azure Site Recovery VSS-szolgáltató szolgáltatásként telepítve van-e a Windows-szolgáltatásokban. Nyissa meg a Komponensszolgáltatás MMC konzolját, és ellenőrizze, hogy a VSS-szolgáltató szerepel-e a listában.
1. Ha a VSS-szolgáltató telepítése továbbra is sikertelen, a technikai támogatással együttműködve oldja meg a kriptográfiai alkalmazásprogramozási felület (CAPI2) engedélyhibáit.

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A VSS-szolgáltató telepítése sikertelen, mert a fürtszolgáltatás engedélyezve van a nem fürtgépen

Ez a probléma okozza az Azure Site Recovery Mobility Agent telepítése sikertelen az Azure Site Recovery VSS-szolgáltató telepítése során. A hiba azért van, mert `COM+` van egy probléma, amely megakadályozza a VSS-szolgáltató telepítését.

### <a name="to-identify-the-issue"></a>A probléma azonosítása

A _C:\ProgramData\ASRSetupLogs\UploadedLogs\<dátum-idő>UA_InstallLogFile.log_ konfigurációs kiszolgálón található naplóban a következő kivételtalálható:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

A probléma megoldása:

1. Ellenőrizze, hogy ez a gép nem fürtgép-e, és hogy a fürtösszetevők nincsenek-e használatban.
1. Ha az összetevők nincsenek használatban, távolítsa el a fürtösszetevőket a gépről.

## <a name="drivers-are-missing-on-the-source-server"></a>Hiányoznak az illesztőprogramok a forráskiszolgálón

Ha a mobilügynök telepítése nem sikerül, vizsgálja meg a _C:\ProgramData\ASRSetupLogs alatti naplókat,_ és ellenőrizze, hogy egyes vezérlőkészletekből hiányzik-e néhány szükséges illesztőprogram.

A probléma megoldása:

1. A rendszerleíró adatbázis `regedit.msc`szerkesztőjével, például a , nyissa meg a rendszerleíró adatbázist.
1. Nyissa `HKEY_LOCAL_MACHINE\SYSTEM` meg a csomópontot.
1. A `SYSTEM` csomópontban keresse meg a vezérlőkészleteket.
1. Nyissa meg az egyes vezérlőcsoportokat, és ellenőrizze, hogy a következő Windows-illesztőprogramok jelen vannak-e:

   * Atapi
   * Vmbus között
   * Storflt között
   * Storvsc között
   * Intelide

1. Telepítse újra a hiányzó illesztőprogramokat.

## <a name="next-steps"></a>További lépések

[További információ](vmware-azure-tutorial.md) a rról, hogyan állíthat be vész-helyreállítási vmware virtuális gépekhez.
