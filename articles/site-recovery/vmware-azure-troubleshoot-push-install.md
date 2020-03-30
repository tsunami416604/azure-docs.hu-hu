---
title: Mobilitási szolgáltatás leküldéses telepítésének hibaelhárítása az Azure Site Recovery f szolgáltatással
description: Mobilitási szolgáltatások telepítési hibáinak elhárítása, amikor engedélyezi a replikációt az Azure Site Recovery-vel a vészhelyreállításhoz.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953782"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>A mobilitási szolgáltatás leküldéses telepítésével kapcsolatos hibák elhárítása 

A mobilitási szolgáltatás telepítése kulcsfontosságú lépés a replikáció engedélyezése során. Ennek a lépésnek a sikere kizárólag az előfeltételek teljesítésétől és a támogatott konfigurációk együttműködésétől függ. A Mobility szolgáltatás telepítése során előforduló leggyakoribb hibák a következők:

* [Hitelesítő adatok/jogosultsági hibák](#credentials-check-errorid-95107--95108)
* [Bejelentkezési hibák](#login-failures-errorid-95519-95520-95521-95522)
* [Csatlakozási hibák](#connectivity-failure-errorid-95117--97118)
* [Fájl- és nyomtatómegosztási hibák](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-hibák](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nem támogatott operációs rendszerek](#unsupported-operating-systems)
* [Nem támogatott rendszerindítási konfigurációk](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-telepítési hibák](#vss-installation-failures)
* [Eszköz neve GRUB konfigurációban az UUID eszköz helyett](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM-kötet](#lvm-support-from-920-version)
* [Újraindításra vonatkozó figyelmeztetések](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Ha engedélyezi a replikációt, az Azure Site Recovery megpróbálja leadni a telepítési szolgáltatásügynök a virtuális gépen. Ennek részeként a konfigurációs kiszolgáló megpróbál csatlakozni a virtuális géphez, és másolja az ügynököt. A sikeres telepítés hez kövesse az alábbi lépésről lépésre adott hibaelhárítási útmutatást.

## <a name="credentials-check-errorid-95107--95108"></a>Hitelesítő adatok ellenőrzése (Hibaazonosító: 95107 & 95108)

* Ellenőrizze, hogy a replikáció engedélyezése során kiválasztott felhasználói fiók **érvényes és pontos-e.**
* Az Azure Site Recovery **leküldéses** telepítés hez root-fiókra vagy **rendszergazdai jogosultságokkal** rendelkező felhasználói fiókra van szükség. Máskülönben a leküldéses telepítés blokkolva lesz a forrásgépen.
  * Windows rendszerben (**95107-es hiba**) ellenőrizze, hogy a felhasználói fiók rendelkezik-e rendszergazdai hozzáféréssel helyi vagy tartományi hozzáféréssel a forrásszámítógépen.
  * Ha nem tartományi fiókot használ, le kell tiltania a távoli felhasználói hozzáférés vezérlését a helyi számítógépen.
    * A távoli felhasználói hozzáférés-vezérlés letiltásához a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\CurrentVersion\Policies\Registry registry kulcs csoportban adjon hozzá egy új DWORD:LocalAccountTokenFilterPolicy parancsot. Az értéket állítsa be 1-re. A lépés végrehajtásához futtassa a következő parancsot a parancssorból:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux (**hiba 95108**), ki kell választania a gyökér fiókot a mobilitásügynök sikeres telepítéséhez. Ezenkívül az SFTP-szolgáltatásoknak futniuk kell. Az SFTP-alrendszer és a jelszóhitelesítés engedélyezése a sshd_config fájlban:
    1. Jelentkezzen be gyökér szintű felhasználóként.
    2. Nyissa meg az /etc/ssh/sshd_config fájlt, keresse meg a PasswordAuthentication programmal kezdődő sort.
    3. A sor megjegyzésének megjegyzése nélkül, és módosítsa az értéket igen értékre.
    4. Keresse meg az Alrendszerrel kezdődő sort, és vonja vissza a sor megjegyzését.
    5. Indítsa újra az sshd szolgáltatást.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)megadott utasításokat .

## <a name="insufficient-privileges-failure-errorid-95517"></a>Elégtelen jogosultsághiba (Hibaazonosító: 95517)

Ha a mobilügynök telepítését választott felhasználó nem rendelkezik rendszergazdai jogosultságokkal, a konfigurációs kiszolgáló/kibővített folyamatkiszolgáló nem másolhatja a mobilügynök szoftverét a forrásszámítógépre. Tehát ez a hiba a hozzáférés megtagadott hibájának eredménye. Győződjön meg arról, hogy a felhasználói fiók rendszergazdai jogosultságokkal rendelkezik.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)megadott utasításokat .

## <a name="insufficient-privileges-failure-errorid-95518"></a>Elégtelen jogosultsághiba (Hibaazonosító: 95518)

Ha az elsődleges tartomány és a munkaállomás közötti tartományi megbízhatósági kapcsolat létrehozása sikertelen, miközben megpróbál bejelentkezni a forrásgépre, a mobilügynök telepítése a 95518-as azonosítóval sikertelen. Ezért győződjön meg arról, hogy a mobilitási ügynök telepítéséhez használt felhasználói fiók rendszergazdai jogosultságokkal rendelkezik a forrásgép elsődleges tartományán keresztül történő bejelentkezéshez.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)megadott utasításokat .

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Bejelentkezési hibák (Hibaazonosító: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>A felhasználói fiók hitelesítő adatai le vannak tiltva (Hibaazonosító: 95519)

A replikáció engedélyezése során kiválasztott felhasználói fiók le van tiltva. A felhasználói fiók engedélyezéséhez olvassa el a cikket [itt,](https://aka.ms/enable_login_user) vagy futtassa a következő parancsot úgy, hogy a szöveges *felhasználónevet* a tényleges felhasználónévre cseréli.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Több sikertelen bejelentkezési kísérlet miatt zárolt hitelesítő adatok (ErrorID: 95520)

A számítógép elérésére irányuló többszörös sikertelen újrapróbálkozási kísérlet zárolja a felhasználói fiókot. A hiba oka lehet:

* A konfiguráció beállítása során megadott hitelesítő adatok helytelenek VAGY
* A replikáció engedélyezése során választott felhasználói fiók hibás

Módosítsa tehát az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) megadott utasításokat követően kiválasztott hitelesítő adatokat, majd próbálkozzon újra a művelettel valamikor.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>A bejelentkezési kiszolgálók nem érhetők el a forrásszámítógépen (ErrorID: 95521)

Ez a hiba akkor fordul elő, ha a bejelentkezési kiszolgálók nem érhetők el a forrásszámítógépen. A bejelentkezési kiszolgálók elérhetetlensége a bejelentkezési kérelem sikertelenségéhez vezet, így a mobilitási ügynök nem telepíthető. A sikeres bejelentkezéshez győződjön meg arról, hogy a bejelentkezési kiszolgálók elérhetők a forrásszámítógépen, és indítsa el a Bejelentkezési szolgáltatást. Részletes útmutatást a KB 139410 Err Msg: There Are Currently No Logon Servers available (Részletes utasítások: KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) Err Msg: There Are Currently No Logon Servers available) című témakörben talál.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>A bejelentkezési szolgáltatás nem fut a forrásgépen (ErrorID: 95522)

A bejelentkezési szolgáltatás nem fut a forrásgépen, és a bejelentkezési kérelem sikertelenségét okozta. Tehát a mobilitási ügynök nem telepíthető. A probléma megoldásához győződjön meg arról, hogy a bejelentkezési szolgáltatás fut a forrásgépen a sikeres bejelentkezéshez. A bejelentkezési szolgáltatás elindításához futtassa a "net start Logon" parancsot a parancssorból, vagy indítsa el a "NetLogon" szolgáltatást a feladatkezelőtől.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Kapcsolódási hiba (Hibaazonosító: 95117 & 97118)**

A konfigurációs kiszolgáló/kibővített folyamatkiszolgáló megpróbál csatlakozni a forrásvirtuális géphez a Mobilügynök telepítéséhez. Ez a hiba akkor fordul elő, ha a forrásgép hálózati kapcsolati problémák miatt nem érhető el. A megoldáshoz

* Győződjön meg arról, hogy a forrásgépet a konfigurációs kiszolgálóról pingelheti. Ha a replikáció engedélyezése során a horizontális felskálázási folyamatkiszolgálót választotta, győződjön meg arról, hogy a forrásgépet a folyamatkiszolgálóról pingelheti.
  * A Forráskiszolgáló gépének parancssorából a Telnet segítségével pingelje a konfigurációs kiszolgálót/ kibővített folyamatkiszolgálót https-porttal (135) az alábbiak szerint, és nézze meg, hogy vannak-e hálózati csatlakozási problémák vagy tűzfalport-blokkoló problémák.

     `telnet <CS/ scale-out PS IP address> <135>`
* Továbbá, a **Linux VM**,
  * Ellenőrizze, hogy a legújabb openssh, openssh-server és openssl csomagok vannak-e telepítve.
  * Ellenőrizze és győződjön meg arról, hogy a Secure Shell (SSH) engedélyezve van, és fut-e a 22-es porton.
  * Az SFTP-szolgáltatásoknak futniuk kell. Az SFTP-alrendszer és a jelszóhitelesítés engedélyezéséhez a sshd_config fájlban
    * Jelentkezzen be gyökér szintű felhasználóként.
    * Nyissa meg az /etc/ssh/sshd_config fájlt, keresse meg a PasswordAuthentication programmal kezdődő sort.
    * A sor megjegyzésének kimegjegyzéstása, és az érték módosítása igen értékre
    * Az Alrendszer rel kezdődő sor megkeresése és a sor megjegyzésének fésülése
    * Indítsa újra az sshd szolgáltatást.
* A csatlakozási kísérlet sikertelen lehet, ha egy idő után nincs megfelelő válasz, vagy a létrehozott kapcsolat nem sikerült, mert a csatlakoztatott állomás nem tudott válaszolni.
* Ez lehet egy kapcsolattal/hálózattal/tartománnyal kapcsolatos probléma. Ennek oka lehet a DNS-név feloldása vagy a TCP-port kimerülési problémája is. Ellenőrizze, hogy vannak-e ilyen ismert problémák a tartományban.

## <a name="connectivity-failure-errorid-95523"></a>Kapcsolódási hiba (Hibaazonosító: 95523)

Ez a hiba akkor fordul elő, ha a hálózat, amelyben a forrásgép található, nem található, vagy lehet, hogy törölték, vagy már nem érhető el. A hiba egyetlen megoldása a hálózat létezésének biztosításával történik.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Fájl- és nyomtatómegosztási szolgáltatások ellenőrzése (Hibaazonosító: 95105 & 95106)

A kapcsolat ellenőrzése után ellenőrizze, hogy a fájl- és nyomtatómegosztási szolgáltatás engedélyezve van-e a virtuális gépen. Ezek a beállítások szükségesek a Mobilügynök forrásgépre történő másolásához.

**Windows 2008 R2 és korábbi verziók esetén**

* A fájl- és nyomtatómegosztás engedélyezése a Windows tűzfalon keresztül,
  * Nyissa meg a vezérlőpultot -> Rendszer és biztonság –> Windows tűzfal -> a bal oldali ablaktáblán, kattintson a Speciális beállítások -> kattintson a konzolfán a Bejövő szabályok parancsra.
  * Keresse meg a fájl- és nyomtatómegosztás (NB-session-In) és a Fájl- és nyomtatómegosztás (SMB-in) szabályokat. Minden szabályhoz kattintson a jobb gombbal a szabályra, majd kattintson a **Szabály engedélyezése parancsra.**
* A fájlmegosztás engedélyezése a csoportházirenddel
  * Nyissa meg a kezdőképernyőt, írja be a gpmc.msc kifejezést, és keressen.
  * A navigációs ablakban nyissa meg a következő mappákat: Helyi számítógép-házirend, Felhasználói konfiguráció, Felügyeleti sablonok, Windows-összetevők és Hálózati megosztás.
  * A részletek ablaktáblán kattintson duplán **a Felhasználók profilon belüli fájlok megosztásának megakadályozása**elemre. A csoportházirend-beállítás letiltásához és a felhasználó fájlok megosztási képességének engedélyezéséhez kattintson a Letiltva gombra. Kattintson az OK gombra a módosítások mentéséhez. További információ: [Fájlmegosztás engedélyezése vagy letiltása csoportházirenddel című témakörben.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))

A **későbbi verziók**esetében kövesse [a Telepítse a Mobilitás szolgáltatást a VMware virtuális gépek és a fizikai kiszolgálók vészhelyreállítása](vmware-azure-install-mobility-service.md) érdekében a fájl- és nyomtatómegosztás engedélyezéséhez című témakörutasításait.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>A Windows Management Instrumentation (WMI) konfigurációjának ellenőrzése (hibakód: 95103)

A fájl- és nyomtatószolgáltatások ellenőrzése után engedélyezze a WMI-szolgáltatást magán-, nyilvános és tartományi profilokhoz tűzfalon keresztül. Ezek a beállítások szükségesek a távoli végrehajtás befejezéséhez a forrásszámítógépen. Az engedélyezéshez

* Nyissa meg a Vezérlőpultot, kattintson a Biztonság, majd a Windows tűzfal parancsra.
* Kattintson a Beállítások módosítása gombra, majd a Kivételek fülre.
* A Kivételek ablakban jelölje be a Windows Management Instrumentation (WMI) jelölőnégyzetét a WMI-forgalom tűzfalon keresztüli engedélyezéséhez. 

A WMI-forgalmat a tűzfalon keresztül is engedélyezheti a parancssorban. A következő parancs használata`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Egyéb WMI hibaelhárítási cikkek találhatók az alábbi cikkekben.

* [Alapvető WMI-vizsgálat](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI hibaelhárítás](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI-parancsfájlokkal és WMI-szolgáltatásokkal kapcsolatos problémák elhárítása](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nem támogatott operációs rendszerek

A hiba másik leggyakoribb oka a nem támogatott operációs rendszer lehet. Győződjön meg arról, hogy a támogatott operációs rendszer/kernel verziót használja a Mobility szolgáltatás sikeres telepítéséhez. Kerülje a privát javítás használatát.
Az Azure Site Recovery által támogatott operációs rendszerek és kernelverziók listájának megtekintéséhez tekintse meg [támogatási mátrixunk dokumentumát.](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nem támogatott rendszerindító lemezkonfigurációk (Hibaazonosító: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Boot és a rendszer partíciók / kötetek nem ugyanaz a lemez (ErrorID: 95309)

A 9.20-as verzió előtt a rendszerindító és a rendszerpartíciók/ kötetek különböző lemezeken nem támogatott konfigurációt tartalmaznak. A [9.20-as verziótól](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)ez a konfiguráció támogatott. A támogatáshoz használja a legújabb verziót.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>A rendszerindító lemez nem érhető el (ErrorID: 95310)

Rendszerindító lemez nélküli virtuális gép nem védhető. Ez biztosítja a virtuális gép zökkenőmentes helyreállítását a feladatátvételi művelet során. A rendszerindító lemez hiánya a feladatátvétel után a gép indításának sikertelenségét eredményezi. Győződjön meg arról, hogy a virtuális gép rendszerindító lemezt tartalmaz, és próbálja meg újra a műveletet. Vegye figyelembe azt is, hogy ugyanazon a számítógépen több rendszerindító lemez nem támogatott.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Több rendszerindító lemez található a forrásgépen (ErrorID: 95311)

A több rendszerindító lemezzel rendelkező virtuális gép nem [támogatott konfiguráció.](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Rendszerpartíció több lemezen (ErrorID: 95313)

A 9.20-as verzió előtt a több lemezre helyezett gyökérpartíció vagy kötet nem támogatott konfiguráció volt. A [9.20-as verziótól](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)ez a konfiguráció támogatott. A támogatáshoz használja a legújabb verziót.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Az engedélyezési védelem nem sikerült, mivel az UUID helyett a GRUB-konfigurációban említett eszköznév (ErrorID: 95320)

**Lehetséges ok:** </br>
A GRUB konfigurációs fájlok ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" vagy "/etc/default/grub") **resume** tartalmazhatják a paraméterek **gyökérértékét,** és az UUID helyett tényleges eszköznévként folytatódhatnak. A Site Recovery az UUID-megközelítést bízza meg, mivel az eszközök neve a virtuális gép újraindítása során változhat, mivel előfordulhat, hogy a virtuális gép nem jön létre ugyanazzal a névvel a feladatátvételkor, ami problémákat eredményez. Példa: </br>


- A következő sor a GRUB fájlból **/boot/grub2/grub.cfg**. <br>
  *linux /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2** ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- A következő sor a GRUB fájlból **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Ha betartja a fenti félkövér karakterláncot, a GRUB tényleges eszköznevekkel rendelkezik a "root" és a "resume" paraméterekhez az UUID helyett.
 
**Hogyan erősít:**<br>
Az eszközök nevét a megfelelő UUID-azonosítókra kell cserélni.<br>


1. Keresse meg az eszköz UUID azonosítóját a \<"blkid eszköz név>" parancs végrehajtásával. Példa:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Most cserélje le az eszköz nevét az UUID-jával olyan\<formátumban, mint a "root=UUID= UUID>". Például, ha kicseréljük a készülék nevét UUID a root és resume paraméter fent említett fájlok "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" vagy "/etc/default/grub: akkor a sorok a fájlokban néz ki. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID =6f614b444-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Indítsa újra a védelmet

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>A Mobility Service telepítése újraindításra figyelmeztetve (ErrorID: 95265 & 95266)

A Site Recovery mobilitási szolgáltatása számos összetevőből áll, amelyek közül az egyiket szűrő-illesztőprogramnak nevezik. A szűrő-illesztőprogram csak a rendszer újraindításakor töltődik be a rendszermemóriába. Ez azt jelenti, hogy a szűrőillesztő javításacsak akkor valósítható meg, ha új szűrőillesztő van betöltve; ami csak a rendszer újraindításakor fordulhat elő.

**Kérjük, vegye figyelembe,** hogy ez egy figyelmeztetés, és a meglévő replikáció az új ügynök frissítésután is működni fog. Bármikor újraindíthatja az új szűrőillesztő előnyeit, de ha nem indítja újra, a régi szűrőillesztő tovább működik. Tehát, után egy korszerűsíteni nélkül újraindít, eltekintve szűrő vezető, **jót tesz -ból más erősítés és megalszik -ban mozgékonyság szolgáltatás gets megvalósítás**. Tehát, bár ajánlott, nem kötelező minden frissítés után újraindítani. Ha további információt, ha egy újraindítás kötelező, állítsa be a [forrásgép újraindítása után a mobilitási ügynök frissítése](https://aka.ms/v2a_asr_reboot) szakasz szolgáltatásfrissítések az Azure Site Recovery.

> [!TIP]
>A karbantartási időszak ban a frissítések ütemezésével kapcsolatos gyakorlati tanácsok az Azure Site Recovery szolgáltatásfrissítéseinek [legújabb operációsrendszer-/kernelverzióinak támogatása](https://aka.ms/v2a_asr_upgrade_practice) című témakörben található.

## <a name="lvm-support-from-920-version"></a>LVM támogatás a 9.20-as verziótól

A 9.20-as verzió előtt az LVM csak adatlemezek esetében támogatott. A /boot kapcsolónak lemezpartíción kell lennie, nem LVM-kötetnek.

A [9.20-as verziótól](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery) [az LVM rendszeren lévő operációsrendszer-lemez](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) támogatott. A támogatáshoz használja a legújabb verziót.

## <a name="insufficient-space-errorid-95524"></a>Nincs elegendő hely (Hibaazonosító: 95524)

A Mobilügynök forrásgépre másolásakor legalább 100 MB szabad terület szükséges. Ezért győződjön meg arról, hogy a forrásgépnek szüksége van a szabad helyre, és próbálja meg újra a műveletet.

## <a name="vss-installation-failures"></a>VSS telepítési hibák

A VSS-telepítés a mobilitási ügynök telepítésének része. Ez a szolgáltatás az alkalmazáskonzisztens helyreállítási pontok létrehozásának folyamatában használatos. A VSS telepítése során több okból is előfordulhatnak hibák. A pontos hibák azonosításához olvassa el a **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log című témakört.** Néhány gyakori hiba és a megoldási lépések ki vannak emelve a következő szakaszban.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-hiba -2147023170 [0x800706BE] - kilépési kód 511

Ez a probléma többnyire akkor jelentkezik, ha a víruskereső szoftverek blokkolják az Azure Site Recovery-szolgáltatások műveleteit. A probléma megoldása:

1. Az [itt](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)említett mappák kizárása .
2. A DLL Windows rendszerben történő regisztrációjának feloldásához kövesse a víruskereső szolgáltató által közzétett irányelveket.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-hiba 7 [0x7] - kilépési kód 511

Ez egy futásidejű hiba, és a VSS telepítéséhez szükséges memória hiánya okozza. Győződjön meg arról, hogy növeli a lemezterületet a művelet sikeres befejezéséhez.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS hiba -2147023824 [0x80070430] - kilépési kód 517

Ez a hiba akkor fordul elő, ha az Azure Site Recovery VSS Provider szolgáltatás törlésre van [megjelölve.](https://msdn.microsoft.com/library/ms838153.aspx) Próbálja meg manuálisan telepíteni a VSS-t a forrásgépre a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-hiba -2147023841 [0x8007041F] - kilépési kód 512

Ez a hiba akkor fordul elő, ha az Azure Site Recovery VSS Provider szolgáltatás [adatbázisa zárolva](https://msdn.microsoft.com/library/ms833798.aspx)van. Próbálja meg manuálisan telepíteni a VSS-t a forrásgépre a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Hiba esetén ellenőrizze, hogy a víruskereső programok vagy más szolgáltatások "Indítás" állapotban vannak-e. Ez megőrizheti az adatbázis-szolgáltatások zárolását. Ez a VSS-szolgáltató telepítésének hibáihoz vezet. Győződjön meg arról, hogy nincs szolgáltatás "Indítás" állapotban, majd próbálkozzon újra a fenti művelettel.

### <a name="vss-exit-code-806"></a>VSS kilépési kód 806

Ez a hiba akkor fordul elő, ha a telepítéshez használt felhasználói fiók nem rendelkezik a CSScript parancs végrehajtásához szükséges engedélyekkel. Adja meg a szükséges engedélyeket a felhasználói fiókhoz a parancsfájl végrehajtásához és a művelet újrapróbálkozásához.

### <a name="other-vss-errors"></a>Egyéb VSS-hibák

Próbálja meg manuálisan telepíteni a VSS-szolgáltató szolgáltatást a forrásgépen a következő parancssorfuttatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS hiba - 0x8004E00F

Ez a hiba általában a dcom-i problémák miatt a mobilitási ügynök telepítése során merül fel, és a DCOM kritikus állapotban van.

A hiba okának meghatározásához kövesse az alábbi eljárást.

**A telepítési naplók vizsgálata**

1. Nyissa meg a c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log mappában található telepítési naplót.
2. A következő hiba jelenléte jelzi a problémát:

    A meglévő alkalmazás regisztrációjának megszüntetése...  A katalógusobjektum létrehozása Alkalmazások gyűjteményének begyűjtése 

    Hiba:

    - Hibakód: -2147164145 [0x8004E00F]
    - Kilépési kód: 802

A probléma megoldása:

A DCOM-probléma megoldásához forduljon a [Microsoft Windows platform csapatához.](https://aka.ms/Windows_Support)

A DCOM-probléma megoldása esetén telepítse újra manuálisan az Azure Site Recovery VSS-szolgáltatót a következő paranccsal:
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Ha az alkalmazás konzisztenciája nem kritikus a vész-helyreállítási követelmények, megkerülheti a VSS-szolgáltató telepítését. 

Az Azure Site Recovery VSS-szolgáltató telepítésének megkerülése és az Azure Site Recovery VSS-szolgáltató manuális telepítése a telepítés után:

1. Telepítse a mobilitási szolgáltatást. 
   > [!Note]
   > 
   > A telepítés sikertelen lesz a "Telepítés utáni konfiguráció" lépésnél. 
2. A VSS telepítésének megkerülése:
   1. Nyissa meg az Azure Site Recovery Mobility Service telepítési könyvtárát, amely a következő helyen található:
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. Módosítsa az Azure Site Recovery VSS-szolgáltató telepítési parancsfájljait **nMageVSSProvider_Install** és **InMageVSSProvider_Uninstall.cmd-t,** hogy mindig sikeres legyen a következő sorok hozzáadásával:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Futtassa újra a mobilügynök telepítését manuálisan. 
4. Ha a telepítés sikeres, és a következő lépésre lép, **a Configure**, távolítsa el a hozzáadott sorokat.
5. A VSS-szolgáltató telepítéséhez nyisson meg egy parancssort rendszergazdaként, és futtassa a következő parancsot:
   
    **C:\Program Files (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. Ellenőrizze, hogy az ASR VSS-szolgáltató szolgáltatásként van-e telepítve a Windows-szolgáltatásokban, és nyissa meg a Komponensszolgáltatás MMC-jét, és ellenőrizze, hogy az ASR VSS-szolgáltató szerepel-e a listában.
10. Ha a VSS-szolgáltató telepítése továbbra is sikertelen, a CX-szel együttműködve oldja fel a CAPI2-ben előforduló engedélyekkel kapcsolatos hibákat.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A VSS-szolgáltató telepítése sikertelen, mert a fürtszolgáltatás engedélyezve van a nem fürtgépen

A probléma hatására az Azure site recovery mobility agent telepítése sikertelen lesz az ASAzure Site RecoveryR VSS-szolgáltató telepítési lépése során a COM+ probléma miatt, amely megakadályozza a VSS-szolgáltató telepítését.
 
### <a name="to-identify-the-issue"></a>A probléma azonosítása

A C:\ProgramData\ASRSetupLogs\UploadedLogs\<dátum->UA_InstallLogFile.log konfigurációs kiszolgálón található naplóban a következő kivétel található:

Com+ nem tudott beszélni a Microsoft elosztott tranzakciók koordinátorával (Kivétel a HRESULT-ből: 0x8004E00F)

A probléma megoldása:

1.  Ellenőrizze, hogy ez a gép nem fürtgép-e, és hogy a fürtösszetevők nincsenek-e használatban.
3.  Ha az összetevők nincsenek használatban, távolítsa el a fürtösszetevőket a gépről.

## <a name="drivers-are-missing-on-the-source-server"></a>Hiányoznak az illesztőprogramok a forráskiszolgálón

Ha a mobilügynök telepítése nem sikerül, vizsgálja meg a C:\ProgramData\ASRSetupLogs alatti naplókat, és ellenőrizze, hogy egyes vezérlőkészletekből hiányzik-e néhány szükséges illesztőprogram.
 
A probléma megoldása:
  
1. A rendszerleíró adatbázis szerkesztőjével, például regedit.msc használatával nyissa meg a rendszerleíró adatbázist.
2. Nyissa meg a HKEY_LOCAL_MACHINE\SYSTEM csomópontot.
3. A RENDSZER csomópontban keresse meg a vezérlőkészleteket.
4. Nyissa meg az egyes vezérlőcsoportokat, és ellenőrizze, hogy a következő Windows-illesztőprogramok jelen vannak-e:

   - Atapi
   - Vmbus között
   - Storflt között
   - Storvsc között
   - intelide
 
Telepítse újra a hiányzó illesztőprogramokat.

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](vmware-azure-tutorial.md) állíthatja be a vészhelyreállítást a VMware virtuális gépekhez.
