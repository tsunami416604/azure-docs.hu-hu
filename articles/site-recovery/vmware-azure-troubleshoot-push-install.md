---
title: A mobilitási szolgáltatás leküldéses telepítésének hibáinak megoldása a Azure Site Recovery f
description: A mobilitási szolgáltatások telepítési hibáinak elhárítása, amikor a Azure Site Recovery-vel történő vész-helyreállítási replikációt engedélyez.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953782"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>A mobilitási szolgáltatás leküldéses telepítésének hibáinak megoldása 

A mobilitási szolgáltatás telepítése kulcsfontosságú lépés a replikáció engedélyezése során. Ennek a lépésnek a sikere kizárólag a találkozó előfeltételeitől függ, és a támogatott konfigurációk használata. A mobilitási szolgáltatás telepítése során felmerülő leggyakoribb hibák a következők:

* [Hitelesítő adatok/jogosultsági hibák](#credentials-check-errorid-95107--95108)
* [Bejelentkezési hibák](#login-failures-errorid-95519-95520-95521-95522)
* [Csatlakozási hibák](#connectivity-failure-errorid-95117--97118)
* [Fájl-és nyomtatómegosztás – hibák](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI-hibák](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Nem támogatott operációs rendszerek](#unsupported-operating-systems)
* [Nem támogatott rendszerindítási konfigurációk](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS-telepítési hibák](#vss-installation-failures)
* [Eszköz neve a GRUB-konfigurációban az eszköz UUID helyett](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM-kötet](#lvm-support-from-920-version)
* [Újraindítási figyelmeztetések](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Ha engedélyezi a replikálást, Azure Site Recovery megpróbálja leküldeni a mobilitási szolgáltatási ügynököt a virtuális gépre. Ennek részeként a konfigurációs kiszolgáló megpróbál kapcsolódni a virtuális géphez, és átmásolja az ügynököt. A sikeres telepítés engedélyezéséhez kövesse az alábbi, részletes hibaelhárítási útmutatót.

## <a name="credentials-check-errorid-95107--95108"></a>Hitelesítő adatok keresése (ErrorID: 95107 & 95108)

* Ellenőrizze, hogy a replikáció engedélyezése során kiválasztott felhasználói fiók **érvényes-e, pontos-** e.
* Azure Site Recovery **rendszergazdai jogosultságokkal** rendelkező **root** fiók vagy felhasználói fiók szükséges a leküldéses telepítés elvégzéséhez. Máskülönben a leküldéses telepítés le lesz tiltva a forrásoldali gépen.
  * Windows esetén (**95107**-es hiba) ellenőrizze, hogy a felhasználói fiók rendelkezik-e helyi vagy tartományi rendszergazdai hozzáféréssel a forrásoldali gépen.
  * Ha nem tartományi fiókot használ, le kell tiltania a távoli felhasználói hozzáférés-vezérlést a helyi számítógépen.
    * A távoli felhasználói hozzáférés-vezérlés letiltásához a HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System beállításkulcs alatt adjon hozzá egy új DUPLASZÓt: LocalAccountTokenFilterPolicy. Állítsa az értéket 1-re. A lépés végrehajtásához futtassa a következő parancsot a parancssorból:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux esetén (**95108**-es hiba) a mobilitási ügynök sikeres telepítéséhez ki kell választania a legfelső szintű fiókot. Emellett az SFTP-szolgáltatásoknak is futniuk kell. Az SFTP alrendszer és a jelszó-hitelesítés engedélyezése a sshd_config fájlban:
    1. Jelentkezzen be root-ként.
    2. Nyissa meg a/etc/ssh/sshd_config fájlt, és keresse meg a PasswordAuthentication kezdetű sort.
    3. Állítsa vissza a sort, és módosítsa az értéket Igen értékre.
    4. Keresse meg az alrendszer kezdetű sorát, és a sor megjegyzését.
    5. Indítsa újra az sshd szolgáltatást.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)megadott utasításokat.

## <a name="insufficient-privileges-failure-errorid-95517"></a>Nem megfelelő jogosultságok hiánya (ErrorID: 95517)

Ha a mobilitási ügynök telepítésére kiválasztott felhasználó nem rendelkezik rendszergazdai jogosultságokkal, a konfigurációs kiszolgáló/kibővített folyamat-kiszolgáló nem fogja tudni átmásolni a mobilitási ügynök szoftverét a forrás számítógépre. Ezért ez a hiba a hozzáférés megtagadásának sikertelenségét eredményezi. Győződjön meg arról, hogy a felhasználói fiók rendszergazdai jogosultságokkal rendelkezik.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)megadott utasításokat.

## <a name="insufficient-privileges-failure-errorid-95518"></a>Nem megfelelő jogosultságok hiánya (ErrorID: 95518)

Ha a tartományi megbízhatósági kapcsolat létrehozása az elsődleges tartomány és a munkaállomás között meghiúsul, miközben a rendszer megpróbál bejelentkezni a forrásoldali gépre, a mobilitási ügynök telepítése a 95518-es AZONOSÍTÓJÚ hibával meghiúsul. Ezért győződjön meg arról, hogy a mobilitási ügynök telepítéséhez használt felhasználói fiók rendszergazdai jogosultságokkal rendelkezik a forrás-számítógép elsődleges tartományán keresztül történő bejelentkezéshez.

Ha módosítani szeretné a kiválasztott felhasználói fiók hitelesítő adatait, kövesse az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)megadott utasításokat.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Bejelentkezési hibák (ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>A felhasználói fiók hitelesítő adatai le lettek tiltva (ErrorID: 95519)

A replikáció engedélyezésekor kiválasztott felhasználói fiók le van tiltva. A felhasználói fiók engedélyezéséhez tekintse meg [a cikket,](https://aka.ms/enable_login_user) vagy futtassa a következő parancsot a szöveges *Felhasználónév* a tényleges felhasználónévvel való lecserélésével.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>A hitelesítő adatok több sikertelen bejelentkezési kísérlet miatt ki vannak zárva (ErrorID: 95520)

A számítógép elérésére irányuló sikertelen próbálkozások többször is zárolják a felhasználói fiókot. A hiba oka a következő lehet:

* A konfiguráció beállítása során megadott hitelesítő adatok helytelenek vagy
* A replikáció engedélyezésekor kiválasztott felhasználói fiók helytelen

Ezért módosítsa az [itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) megadott utasítások alapján kiválasztott hitelesítő adatokat, majd próbálja megismételni a műveletet.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>A bejelentkezési kiszolgálók nem érhetők el a forrásoldali gépen (ErrorID: 95521)

Ez a hiba akkor fordul elő, ha a bejelentkezési kiszolgálók nem érhetők el a forrásoldali gépen. A bejelentkezési kiszolgálók nem állnak rendelkezésre. a bejelentkezési kérelem sikertelen lesz, így a mobilitási ügynök nem telepíthető. A sikeres bejelentkezéshez győződjön meg arról, hogy a bejelentkezési kiszolgálók elérhetők a forrásoldali gépen, és indítsa el a bejelentkezési szolgáltatást. Részletes útmutatásért lásd: KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) err msg: jelenleg nincs elérhető bejelentkezési kiszolgáló.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>A bejelentkezési szolgáltatás nem fut a forrásoldali gépen (ErrorID: 95522)

A bejelentkezési szolgáltatás nem fut a forrásoldali gépen, és a bejelentkezési kérelem meghiúsult. Így a mobilitási ügynök nem telepíthető. A probléma megoldásához ellenőrizze, hogy a bejelentkezési szolgáltatás fut-e a forrásoldali gépen a sikeres bejelentkezéshez. A bejelentkezési szolgáltatás elindításához futtassa a "net start Logon" parancsot a parancssorból, vagy indítsa el a "NetLogon" szolgáltatást a Feladatkezelő segédprogramból.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Csatlakozási hiba (ErrorID: 95117 & 97118)**

A konfigurációs kiszolgáló/kibővített folyamat kiszolgálója megpróbál csatlakozni a forrás virtuális géphez a mobilitási ügynök telepítéséhez. Ez a hiba akkor fordul elő, ha a forrásszámítógép hálózati kapcsolati problémák miatt nem érhető el. A megoldáshoz

* Győződjön meg arról, hogy képes a forrásoldali gép pingelésére a konfigurációs kiszolgálóról. Ha a replikálás engedélyezése során kibővített feldolgozási kiszolgálót választott, győződjön meg arról, hogy képes a forráskiszolgáló pingelésére a feldolgozó kiszolgálóról.
  * A forráskiszolgáló számítógép parancssorában a Telnet használatával Pingelje a konfigurációs kiszolgáló/kibővítő folyamat kiszolgálóját HTTPS-porton (135) az alább látható módon, és ellenőrizze, hogy van-e hálózati kapcsolattal kapcsolatos probléma vagy tűzfal-port blokkolási probléma.

     `telnet <CS/ scale-out PS IP address> <135>`
* Emellett Linux rendszerű **virtuális gép**esetén
  * Ellenőrizze, hogy a legújabb OpenSSH, OpenSSH-Server és OpenSSL csomagok telepítve vannak-e.
  * Győződjön meg arról, hogy a Secure Shell (SSH) engedélyezve van, és a 22-es porton fut.
  * Az SFTP-szolgáltatásoknak futniuk kell. Az SFTP alrendszer és a jelszó-hitelesítés engedélyezése a sshd_config fájlban
    * Jelentkezzen be root-ként.
    * Nyissa meg a/etc/ssh/sshd_config fájlt, és keresse meg a PasswordAuthentication kezdetű sort.
    * Állítsa vissza a sort, és módosítsa az értéket Igen értékre.
    * Keresse meg az alrendszer kezdetű sorát, és a sor megjegyzését.
    * Indítsa újra az sshd szolgáltatást.
* Sikertelen volt egy csatlakozási kísérlet, ha nincs megfelelő válasz egy adott idő elteltével, vagy a kapcsolat létrejött, mert a csatlakoztatott gazdagép nem válaszolt.
* Lehet, hogy kapcsolat/hálózat/tartományhoz kapcsolódó probléma van. A probléma oka lehet a DNS-név feloldása vagy a TCP-port kimerülési problémája is. Ellenőrizze, hogy vannak-e ilyen ismert problémák a tartományban.

## <a name="connectivity-failure-errorid-95523"></a>Csatlakozási hiba (ErrorID: 95523)

Ez a hiba akkor fordul elő, ha a forrásszámítógép helyét tartalmazó hálózat nem található, vagy esetleg törölték, vagy már nem érhető el. A hiba elhárításának egyetlen módja a hálózat létezésének biztosítása.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Fájl-és nyomtatómegosztás-ellenőrzési szolgáltatások (ErrorID: 95105 & 95106)

A kapcsolat ellenőrzése után ellenőrizze, hogy a fájl-és nyomtatómegosztás szolgáltatás engedélyezve van-e a virtuális gépen. Ezek a beállítások szükségesek a mobilitási ügynöknek a forrásoldali gépre való másolásához.

**Windows 2008 R2 és korábbi verziók**esetén

* A fájl-és nyomtatómegosztás Windows tűzfalon való engedélyezéséhez
  * Nyissa meg a Vezérlőpultot – > rendszer és Biztonság – > Windows tűzfal – > a bal oldali ablaktáblán kattintson a speciális beállítások elemre, > kattintson a bejövő szabályok elemre a konzolfán.
  * Keresse meg a szabályok fájl-és nyomtatómegosztás (NetBIOS-munkamenet) és a fájl-és nyomtatómegosztás (SMB, bejövő) szakaszt. Mindegyik szabályhoz kattintson a jobb gombbal a szabályra, majd kattintson a **szabály engedélyezése**parancsra.
* A fájlmegosztást Csoportházirend használatával engedélyezheti.
  * Lépjen a Start menüre, és írja be a GPMC. msc parancsot, és keressen rá.
  * A navigációs ablaktáblán nyissa meg a következő mappákat: helyi számítógép-házirend, felhasználói konfiguráció, Felügyeleti sablonok, Windows-összetevők és hálózati megosztás.
  * A részleteket tartalmazó ablaktáblán kattintson duplán a **felhasználók a profilban lévő fájlok megosztásának megakadályozása**lehetőségre. Ha le szeretné tiltani a Csoportházirend beállítást, és engedélyezni szeretné a felhasználók számára a fájlok megosztását, kattintson a Letiltás lehetőségre. A módosítások mentéséhez kattintson az OK gombra. További információ: fájlmegosztás [engedélyezése vagy letiltása Csoportházirend](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))használatával.

A **későbbi verziókhoz**kövesse a [mobilitási szolgáltatás telepítése a VMWare virtuális gépek és fizikai kiszolgálók vész-helyreállításához](vmware-azure-install-mobility-service.md) című témakör utasításait a fájlok és a nyomtatók megosztásának engedélyezéséhez.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) konfigurációs vizsgálat (hibakód: 95103)

A fájl-és nyomtató-szolgáltatások ellenőrzését követően engedélyezze a WMI-szolgáltatást a tűzfalon keresztül a privát, nyilvános és tartományi profilokhoz. Ezek a beállítások a forrásszámítógép távoli végrehajtásának befejezéséhez szükségesek. Az engedélyezéshez

* Nyissa meg a Vezérlőpultot, kattintson a biztonság, majd a Windows tűzfal elemre.
* Kattintson a beállítások módosítása elemre, majd kattintson a kivételek lapra.
* A kivételek ablakban jelölje be Windows Management Instrumentation (WMI) jelölőnégyzetét a WMI-forgalom tűzfalon keresztüli engedélyezéséhez. 

A WMI-forgalmat a tűzfalon keresztül is engedélyezheti a parancssorban. Használja az alábbi parancsot `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Más WMI-hibaelhárítási cikkek a következő cikkekben találhatók.

* [Alapszintű WMI-tesztelés](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-hibaelhárítás](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [A WMI-parancsfájlokkal és a WMI-szolgáltatásokkal kapcsolatos problémák elhárítása](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nem támogatott operációs rendszerek

A hiba egy másik leggyakoribb oka a nem támogatott operációs rendszerek oka lehet. Győződjön meg arról, hogy a támogatott operációs rendszer/kernel verziója támogatja a mobilitási szolgáltatás sikeres telepítését. Kerülje a privát javítás használatát.
A Azure Site Recovery által támogatott operációs rendszerek és kernel-verziók listájának megtekintéséhez tekintse meg a [támogatási mátrix dokumentumát](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Nem támogatott rendszerindító lemez-konfigurációk (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>A rendszerindító és a rendszerpartíciók/kötetek nem azonos a lemezen (ErrorID: 95309)

Az 9,20-as verzió előtt a rendszerindító és a rendszerpartíciók, illetve a különböző lemezeken található kötetek nem támogatottak. A [9,20-es verziótól](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve ez a konfiguráció támogatott. A támogatáshoz használja a legújabb verziót.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>A rendszerindító lemez nem érhető el (ErrorID: 95310)

Rendszerindító lemezzel nem rendelkező virtuális gép nem védhető. Ezzel biztosítható a virtuális gép zökkenőmentes helyreállítása a feladatátvételi művelet során. A rendszerindító lemez hiánya miatt a gép nem indítható el a feladatátvételt követően. Győződjön meg arról, hogy a virtuális gép rendszerindító lemezt tartalmaz, majd próbálja megismételni a műveletet. Azt is vegye figyelembe, hogy ugyanazon a gépen több rendszerindító lemez sem támogatott.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Több rendszerindító lemez található a forrásoldali gépen (ErrorID: 95311)

Egy több rendszerindító lemezzel rendelkező virtuális gép nem [támogatott konfiguráció](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Rendszerpartíció több lemezen (ErrorID: 95313)

Az 9,20-as verzió előtt a több lemezen található főpartíció vagy kötet nem támogatott konfiguráció. A [9,20-es verziótól](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve ez a konfiguráció támogatott. A támogatáshoz használja a legújabb verziót.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>A védelem engedélyezése nem sikerült, mert a GRUB-konfigurációban az UUID helyett az eszköznév szerepel (ErrorID: 95320)

**Lehetséges ok:** </br>
A GRUB konfigurációs fájljai ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/GRUB2/grub.cfg" vagy "/etc/default/grub") tartalmazhatják a paraméterek **gyökerének** értékeit, **és a** tényleges eszköznév helyett az UUID értéket. Site Recovery a mandátumok UUID-alapú megközelítését, mivel az eszközök neve a virtuális gép újraindításakor változhat, mivel a virtuális gép nem fog ugyanazzal a névvel a feladatátvétel során felmerülni, ami problémákat okoz. Például: </br>


- A következő sor a GRUB-fájl **/boot/GRUB2/grub.cfg**származik. <br>
  *Linux/boot/vmlinuz-3.12.49-11-default **root =/dev/sda2** $ {extra_cmdline} **resume =/dev/sda1** Splash = csendes csendes showopts*


- A következő sor a GRUB fájl **/boot/grub/menu.lst**
  *kernel/boot/vmlinuz-3.0.101-63-default **root =/dev/sda2** **resume =/dev/sda1** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*

Ha betartja a fenti félkövér sztringet, a GRUB a "root" és a "Resume" paraméterekhez a "root" és a "Folytatás" paraméterrel rendelkezik.
 
**A javítás módja:**<br>
Az eszközök nevét a megfelelő UUID-val kell helyettesíteni.<br>


1. A "blkid \<eszköznév >" parancs végrehajtásával keresse meg az eszköz UUID-azonosítóját. Például:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Most cserélje le az eszköz nevét az UUID formátumára, például: "root = UUID =\<UUID >". Ha például a "/boot/GRUB2/grub.cfg", a "/boot/GRUB2/grub.cfg" vagy a "/etc/default/grub:" fájlokban a fentiekben említettek szerint az eszközök neveit az UUID értékre cseréljük, akkor a fájlok sorai a következőképpen néznek ki. <br>
   *kernel/boot/vmlinuz-3.0.101-63-default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** Splash = Silent crashkernel = 256M-: 128M showopts VGA = 0x314*
3. Indítsa újra a védelmet

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>A mobilitási szolgáltatás telepítése figyelmeztetéssel fejeződött be (ErrorID: 95265 & 95266)

Site Recovery mobilitási szolgáltatásnak számos összetevője van, amelyek közül az egyik neve szűrő-illesztőprogram. A szűrő-illesztőprogram csak a rendszer újraindításakor lesz betöltve a rendszermemóriába. Ez azt jelenti, hogy a szűrő-illesztőprogram javításait csak akkor lehet megvalósítani, ha új szűrő-illesztőprogram van betöltve; Ez csak a rendszer újraindításának időpontjában történhet.

Vegye **figyelembe** , hogy ez egy figyelmeztetés, és a meglévő replikáció az új ügynök frissítése után is működni fog. Dönthet úgy, hogy bármikor újraindítja az új szűrő-illesztőprogram előnyeit, de ha nem indul újra a régi szűrő-illesztőprogram, továbbra is működni fog. Tehát a frissítés újraindítása után a szűrő-illesztőprogramon kívül a **mobilitási szolgáltatás további fejlesztései és javításai**is elérhetővé válik. Ezért bár ajánlott, a frissítés után nem kötelező újraindulni. Ha az újraindítás kötelező, a Azure Site Recoveryban található szolgáltatási frissítések [mobilitási ügynök frissítése szakaszában állítsa be a forrásoldali gép újraindítását](https://aka.ms/v2a_asr_reboot) .

> [!TIP]
>A karbantartási időszakon belüli frissítések ütemezésével kapcsolatos ajánlott eljárásokért tekintse meg a Azure Site Recovery-ben a Service Updates című témakör [legújabb operációsrendszer-/kernel-verzióinak támogatását](https://aka.ms/v2a_asr_upgrade_practice) .

## <a name="lvm-support-from-920-version"></a>Az LVM 9,20-es verziójának támogatása

A 9,20-es verzió előtt az LVM csak adatlemezek esetében támogatott. a/boot lemezes partíción kell lennie, és nem lehet LVM-kötet.

A [9,20-es verziótól](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)kezdve az [operációsrendszer-lemez az LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) -ben támogatott. A támogatáshoz használja a legújabb verziót.

## <a name="insufficient-space-errorid-95524"></a>Nincs elég hely (ErrorID: 95524)

Ha a mobilitási ügynököt átmásolja a forrás gépre, legalább 100 MB szabad terület szükséges. Ezért győződjön meg arról, hogy a forrásszámítógép szükséges szabad területtel rendelkezik, majd próbálja megismételni a műveletet.

## <a name="vss-installation-failures"></a>VSS-telepítési hibák

A VSS-telepítés a mobilitási ügynök telepítésének része. Ez a szolgáltatás az alkalmazások konzisztens helyreállítási pontjainak létrehozási folyamatában használatos. A VSS telepítése során fellépő hibák több okból is előfordulhatnak. A pontos hibák azonosításához tekintse meg a **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Néhány gyakori hiba és a megoldás lépései a következő szakaszban vannak kiemelve.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-hiba-2147023170 [0x800706BE]-kilépési kód: 511

Ez a probléma többnyire akkor látható, ha a víruskereső szoftver blokkolja a Azure Site Recovery szolgáltatások műveleteit. A probléma megoldása:

1. Az [itt](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)említett összes mappa kizárása.
2. Kövesse a vírusvédelmi szolgáltató által közzétett irányelveket a DLL-fájlok Windows rendszerben való regisztrálásának feloldásához.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-hiba 7 [0x7] – 511-es kilépési kód

Ez egy futásidejű hiba, és a VSS-telepítés hiánya miatt nincs elég memória. Győződjön meg arról, hogy a művelet sikeres befejezéséhez szükséges lemezterület növekszik.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-hiba-2147023824 [0x80070430]-kilépési kód: 517

Ez a hiba akkor fordul elő, ha Azure Site Recovery VSS-szolgáltató szolgáltatás [törlésre van megjelölve](https://msdn.microsoft.com/library/ms838153.aspx). Próbálja meg manuálisan telepíteni a VSS-t a forrásoldali gépen a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-hiba-2147023841 [0x8007041F]-kilépési kód: 512

Ez a hiba akkor fordul elő, ha Azure Site Recovery VSS-szolgáltatói szolgáltatás adatbázisa [zárolva](https://msdn.microsoft.com/library/ms833798.aspx)van. Próbálja meg manuálisan telepíteni a VSS-t a forrásoldali gépen a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Hiba esetén ellenőrizze, hogy a víruskereső program vagy más szolgáltatás beragadt-e a "kezdő" állapotba. Ez megtarthatja a zárolást az adatbázis-szolgáltatásokban. A VSS-szolgáltató telepítése során fellépő hibákhoz vezet. Győződjön meg arról, hogy egyetlen szolgáltatás sem "Start" állapotban van, majd próbálja megismételni a fenti műveletet.

### <a name="vss-exit-code-806"></a>VSS kilépési kód 806

Ez a hiba akkor fordul elő, ha a telepítéshez használt felhasználói fiók nem rendelkezik a CSScript parancs végrehajtásához szükséges engedélyekkel. Adja meg a szükséges engedélyeket a felhasználói fióknak a parancsfájl végrehajtásához, majd próbálja megismételni a műveletet.

### <a name="other-vss-errors"></a>Egyéb VSS-hibák

Próbálja meg manuálisan telepíteni a VSS-szolgáltató szolgáltatást a forrásszámítógépen a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS-hiba – 0x8004E00F

Ez a hiba általában a mobilitási ügynök telepítése során fordul elő, mivel a DCOM és a DCOM problémái a kritikus állapotban vannak.

A hiba okának megállapításához kövesse az alábbi eljárást.

**A telepítési naplók vizsgálata**

1. Nyissa meg a c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log. címen található telepítési naplót
2. A probléma a következő hiba jelenlétét jelzi:

    Meglévő alkalmazás regisztrációjának törlése...  A katalógus objektum létrehozása az alkalmazások gyűjteményének beolvasása 

    HIBA

    - Hibakód:-2147164145 [0x8004E00F]
    - Kilépési kód: 802

A probléma megoldásához:

Forduljon a [Microsoft Windows platform csapatához](https://aka.ms/Windows_Support) , és kérjen segítséget a DCOM-probléma megoldásához.

Ha a DCOM-probléma megoldódott, telepítse újra a Azure Site Recovery VSS-szolgáltatót a következő parancs használatával:
 
**C:\Program Files (x86) \Microsoft Azure site Recovery\agent > "C:\Program Files (x86) \Microsoft Azure site Recovery\agent\ InMageVSSProvider_Install. cmd**
  
Ha az alkalmazás konzisztenciája nem kritikus a vész-helyreállítási követelmények esetében, megkerülheti a VSS-szolgáltató telepítését. 

A Azure Site Recovery VSS-szolgáltató telepítésének mellőzése és a Azure Site Recovery VSS-szolgáltató manuális telepítése a telepítés után:

1. Telepítse a mobilitási szolgáltatást. 
   > [!Note]
   > 
   > A telepítés a telepítés utáni konfiguráció lépése után sikertelen lesz. 
2. A VSS-telepítés megkerülése:
   1. Nyissa meg a Azure Site Recovery mobilitási szolgáltatás telepítési könyvtárát a következő helyen:
   
      C:\Program Files (x86) \Microsoft Azure site Recovery\agent
   2. Módosítsa a Azure Site Recovery VSS-szolgáltató telepítési parancsfájljait **nMageVSSProvider_Install** és a **InMageVSSProvider_Uninstall. cmd** fájlt, hogy mindig sikeres legyen a következő sorok hozzáadásával:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Futtassa újra manuálisan a mobilitási ügynök telepítését. 
4. Ha a telepítés sikeres, és a következő lépésre lép, **konfigurálja**a hozzáadott sorokat.
5. A VSS-szolgáltató telepítéséhez nyisson meg egy parancssort rendszergazdaként, és futtassa a következő parancsot:
   
    **C:\Program Files (x86) \Microsoft Azure site Recovery\agent >. \ InMageVSSProvider_Install. cmd**

9. Ellenőrizze, hogy az ASR VSS-szolgáltató szolgáltatásként van-e telepítve a Windows-szolgáltatásokban, majd nyissa meg a Komponensszolgáltatások MMC-t az ASR VSS-szolgáltató listájának ellenőrzéséhez.
10. Ha a VSS-szolgáltató telepítése továbbra is meghiúsul, a CAPI2-ben az engedélyek hibáinak elhárításához működjön együtt a CX-val.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A VSS-szolgáltató telepítése sikertelen volt, mert a Fürtszolgáltatás nem fürtözött gépen van engedélyezve

Ez a probléma azt eredményezi, hogy a Azure Site Recovery mobilitási ügynök telepítése meghiúsul a ASAzure site Recoveryer VSS-szolgáltató telepítési lépése során a VSS-szolgáltató telepítését megakadályozó COM+ hibája miatt.
 
### <a name="to-identify-the-issue"></a>A probléma azonosítása

A konfigurációs kiszolgálón található naplóban a C:\ProgramData\ASRSetupLogs\UploadedLogs\<dátum-idő > UA_InstallLogFile. log naplófájlban a következő kivétel jelenik meg:

A COM+ nem tudott kommunikálni a Microsoft Elosztott tranzakciók koordinátoratal (kivétel: HRESULT: 0x8004E00F)

A probléma megoldásához:

1.  Győződjön meg arról, hogy a gép nem fürtözött számítógép, és hogy a fürt összetevői nincsenek használatban.
3.  Ha az összetevők nincsenek használatban, távolítsa el a fürt összetevőit a gépről.

## <a name="drivers-are-missing-on-the-source-server"></a>Hiányoznak illesztőprogramok a forráskiszolgálón

Ha a mobilitási ügynök telepítése nem sikerül, vizsgálja meg a C:\ProgramData\ASRSetupLogs területen található naplókat annak megállapításához, hogy egyes vezérlők hiányoznak-e bizonyos szükséges illesztőprogramok.
 
A probléma megoldásához:
  
1. Nyisson meg egy Rendszerleíróadatbázis-szerkesztőt, például a Regedit. msc fájlt, és nyissa meg a beállításjegyzéket.
2. Nyissa meg a HKEY_LOCAL_MACHINE \SYSTEM csomópontot.
3. A rendszercsomópontban keresse meg a vezérlőelem-készleteket.
4. Nyissa meg az egyes vezérlőelem-készleteket, és ellenőrizze, hogy a következő Windows-illesztőprogramok találhatók-e:

   - ATAPI
   - VMBus
   - storflt
   - storvsc
   - Intelide
 
Telepítse újra a hiányzó illesztőprogramokat.

## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan](vmware-azure-tutorial.md) állíthatja be a vész-helyreállítást a VMWare virtuális gépekhez.
