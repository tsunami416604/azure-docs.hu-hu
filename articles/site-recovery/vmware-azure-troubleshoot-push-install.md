---
title: A mobilitási szolgáltatás leküldéses telepítési hibák elhárításához, ha a vész-helyreállítási replikáció engedélyezése |} A Microsoft Docs
description: A mobilitási szolgáltatás telepítési hibák elhárításához, ha a vész-helyreállítási replikáció engedélyezése
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 01/18/2019
ms.openlocfilehash: e397540d33df8a509e10f52fde41fc178cdba67e
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411747"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>A mobilitási szolgáltatás leküldéses telepítési problémák elhárítása

Mobilitási szolgáltatás telepítésének legfontosabb lépése replikálás engedélyezése során. Ez a lépés sikeres kizárólag Előfeltételek teljesítése és a támogatott konfigurációk használata attól függ. A mobilitási szolgáltatás telepítése során között leggyakoribb hibák a következők miatt:

* Hitelesítő adatok vagy jogosultsági hibák
* Sikertelen bejelentkezések
* Kapcsolódási hibák
* Nem támogatott operációs rendszerek
* VSS telepítési hibák

A replikáció engedélyezése az Azure Site Recovery megpróbálja küldje le a virtuális gép mobilitásiszolgáltatás-ügynök telepíthető. Ennek részeként konfigurációs kiszolgáló megkísérli a virtuális géppel csatlakozhat, és másolja az ügynököt. A sikeres telepítés engedélyezéséhez kövesse az alábbi részletes hibaelhárítási útmutatót.

## <a name="credentials-check-errorid-95107--95108"></a>Hitelesítő adatok ellenőrzése (ErrorID: 95107 & 95108)

* Győződjön meg arról, ha a felhasználói fiók, a replikáció engedélyezése közben kiválasztott, **érvényes, a pontos**.
* Az Azure Site Recovery igényel **legfelső szintű** fiók vagy felhasználói fiók **rendszergazdai jogosultságokkal** ügyfélleküldéses telepítés végrehajtásához. Más esetben ügyfélleküldéses telepítést le lesz tiltva a forrásgépen.
  * A Windows (**95107-es**), győződjön meg arról, ha a felhasználói fiók rendelkezik-e rendszergazdai hozzáféréssel, helyi vagy a tartományban, a forrásgépen.
  * Ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen szeretné.
    * Tiltsa le a távoli felhasználói hozzáférés-vezérlést, a localaccounttokenfilterpolicy beállításjegyzékbeli kulcs hozzáadása egy új DWORD: LocalAccountTokenFilterPolicy. Állítsa az értékét 1-re. Ez a lépés végrehajtásához futtassa a következő parancsot a parancssorba:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux (**95108-as**), ki kell választania a mobilitási ügynök sikeres telepítéséhez rendszergazdai fiók. Ezenkívül az SFTP-szolgáltatások kell futtatnia. Ahhoz, hogy az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban:
    1. Jelentkezzen be gyökérszintű felhasználóként.
    2. /Etc/ssh/sshd_config keresse meg, keresse meg a sort, amely PasswordAuthentication kezdődik.
    3. Állítsa vissza a sort, és módosítsa az Igen értéket.
    4. Keresse meg a sort, amely alrendszer kezdődik, és állítsa vissza a sort.
    5. Indítsa újra az sshd szolgáltatást.

Ha szeretné módosítani a kiválasztott felhasználói fiók hitelesítő adatait, hajtsa végre az adott utasítások [Itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Nincs megfelelő jogosultsága hiba (ErrorID: 95517)

Ha a mobilitási ügynök telepítéséhez választott felhasználó nem rendelkezik rendszergazdai jogosultságokkal, konfigurációs kiszolgáló vagy kibővíthető folyamatkiszolgáló nem engedélyezett a mobilitási ügynök szoftver forrásgép be másolni. Tehát ez a hiba nem a hozzáférés megtagadva hiba eredménye. Győződjön meg arról, hogy a felhasználói fiók rendelkezik-e rendszergazdai jogosultságokkal.

Ha szeretné módosítani a kiválasztott felhasználói fiók hitelesítő adatait, hajtsa végre az adott utasítások [Itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Nincs megfelelő jogosultsága hiba (ErrorID: 95518)

Tartomány megbízhatósági kapcsolat létrehozása az elsődleges tartomány és a munkaállomás közötti jelentkezzen be a forrásgép tett kísérlet során nem sikerül, a mobilitási ügynök telepítése sikertelen lesz, 95518 hiba azonosítója. Ezért győződjön meg arról, hogy a mobilitási ügynök telepítéséhez használt felhasználói fiók rendelkezik-e rendszergazdai jogosultságokkal a forrásgép elsődleges tartományon keresztül bejelentkezni.

Ha szeretné módosítani a kiválasztott felhasználói fiók hitelesítő adatait, hajtsa végre az adott utasítások [Itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failure-errorid-95519"></a>Bejelentkezési hiba történt (ErrorID: 95519)

A replikálás engedélyezése során kiválasztott felhasználói fiók le van tiltva. A felhasználói fiók engedélyezéséhez tekintse meg a cikk [Itt](https://aka.ms/enable_login_user) vagy futtassa a következő parancsot a szöveg cseréje *felhasználónév* tényleges felhasználónévvel.
`net user 'username' /active:yes`

## <a name="login-failure-errorid-95520"></a>Bejelentkezési hiba történt (ErrorID: 95520)

Több sikertelen újrapróbálkozási erőfeszítések eléréséhez egy gép zárolja a felhasználói fiókot. A hiba oka lehet:

* Konfigurációs beállítás során megadott hitelesítő adatok helytelenek, vagy
* A replikálás engedélyezése során kiválasztott felhasználói fiók nem megfelelő.

Ezért, módosítsa a kiválasztott megadott utasítások szerint hitelesítő adatok [Itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) , majd próbálja megismételni a műveletet később.

## <a name="login-failure-errorid-95521"></a>Bejelentkezési hiba történt (ErrorID: 95521)

Ez a hiba akkor fordul elő, ha a bejelentkezési kiszolgálók nem érhetők el a forrásgépen. Bejelentkezési kiszolgálók hiányában sikertelen bejelentkezési kérelem vezet, és így nem lehet telepíteni a mobilitási ügynök. Sikeres bejelentkezés győződjön meg arról, hogy a bejelentkezési kiszolgálók érhetők el a forrásgépen, és indítsa el a bejelentkezési szolgáltatás. Részletes utasításokért kattintson [Itt](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available).

## <a name="login-failure-errorid-95522"></a>Bejelentkezési hiba történt (ErrorID: 95522)

A bejelentkezési szolgáltatás a forrásgépen nem fut, és a bejelentkezési kérelem hibáját okozta. Így nem lehet telepíteni a mobilitási ügynök. Megoldásához, győződjön meg arról, hogy bejelentkezési szolgáltatás fut a forrásgépen a sikeres bejelentkezés. A bejelentkezési szolgáltatás elindításához futtassa a parancsot "net start bejelentkezési" parancssorból, vagy indítsa el a "NetLogon" szolgáltatást a Feladatkezelő.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Csatlakozási hiba (ErrorID: 95117 & 97118)**

Konfigurációs kiszolgáló / horizontális felskálázási folyamatkiszolgáló úgy próbál csatlakozni a forrás virtuális Gépen a mobilitási ügynök telepítése. Ez a hiba akkor fordul elő, ha a forrásgép hálózati kapcsolódási problémák miatt nem érhető el. Háríthatja el

* Ellenőrizze, hogy pingelni a forrásgép a konfigurációs kiszolgálóról. Ha úgy döntött, hogy kibővíthető folyamatkiszolgáló a replikáció engedélyezése során, ellenőrizze, hogy a forrásgép folyamatkiszolgálóról pingelni.
  * A forráskiszolgáló gép parancssorból, a Telnet használatával a konfigurációs kiszolgáló pingelése / horizontális felskálázási folyamatkiszolgáló a https (135-ös port) megtekintéséhez, ha vannak-e hálózati kapcsolat hibái vagy tűzfal port hátráltató alább látható módon.

     `telnet <CS/ scale-out PS IP address> <135>`
* Ezenkívül a **Linux rendszerű virtuális gép**,
  * Ellenőrizze, ha telepítve vannak-e a legfrissebb openssh, openssh-server és openssl csomagokat.
  * Ellenőrizze, és győződjön meg arról, hogy a Secure Shell (SSH) engedélyezve van és fut a 22-es portot.
  * Az SFTP-szolgáltatások kell futtatnia. Az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban engedélyezése
    * Jelentkezzen be gyökérszintű felhasználóként.
    * /Etc/ssh/sshd_config keresse meg, keresse meg a sort, amely PasswordAuthentication kezdődik.
    * Állítsa vissza a sort, és módosítsa az Igen értéket
    * Keresse meg azt a sort, alrendszer kezdődik, és állítsa vissza a sort
    * Indítsa újra az sshd szolgáltatást.
* Kapcsolódási kísérlet van nem sikerült, ha nem érkezik válasz megfelelő, egy idő után, vagy a kialakított kapcsolat meghibásodott, mert a csatlakoztatott állomás nem válaszolt.
* Kapcsolat/hálózati/tartomány lehet okozza. Azt is okozhatja, hogy a probléma vagy a TCP-port Erőforrásfogyás probléma megoldásának DNS-név. Ellenőrizze, hogy van-e olyan ismert problémákat a tartományban.

## <a name="connectivity-failure-errorid-95523"></a>Csatlakozási hiba (ErrorID: 95523)

Ez a hiba akkor fordul elő, ha a hálózatot, amelyben a forrásgépen található nem található, vagy lehet, hogy törölték, vagy már nem érhető el. A hiba megoldásához egyetlen módja, biztosítva, hogy létezik-e a hálózathoz.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>A fájl- és nyomtatómegosztási szolgáltatások ellenőrzése (ErrorID: 95105 & 95106)

Kapcsolat-ellenőrzés után ellenőrizze a fájl- és nyomtatómegosztás szolgáltatás engedélyezésének a virtuális gépen. Ezek a beállítások másolása a forrásgépen a mobilitási ügynök szükséges.

A **windows 2008 R2 és korábbi verziók**,

* Fájl- és nyomtatómegosztás Windows tűzfalon, engedélyezése
  * Nyissa meg a Vezérlőpult -> rendszer és Biztonság -> Windows tűzfal -> bal oldali ablaktáblán, kattintson a Speciális beállítások -> konzolfán kattintson a bejövő szabályok.
  * Keresse meg a fájlt, és nyomtatómegosztás (NetBIOS-munkamenet-) és a fájl- és nyomtatómegosztás (SMB, bejövő) szabályok. Az egyes szabályokhoz, kattintson a jobb gombbal a szabályt, és kattintson **szabály engedélyezése**.
* A csoportházirenddel, fájlmegosztási engedélyezése
  * Ugrás a kezdő, írja be a gpmc.msc, és keressen.
  * A navigációs ablakban nyissa meg a következő mappák: Helyi számítógép-házirend, felhasználói konfigurációs, felügyeleti sablonok, Windows-összetevők és hálózati megosztás.
  * A részleteket tartalmazó ablaktáblán kattintson duplán a **megakadályozhatja a felhasználókat a profilon belül a fájlok megosztása**. Tiltsa le a csoportházirend-beállítást, és engedélyezze a felhasználó engedélyének fájlokat, kattintson a le van tiltva. Kattintson az OK gombra a módosítások mentéséhez. További tudnivalókért kattintson [Itt](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

A **újabb verzió**, kövesse a megjelenő utasításokat [Itt](vmware-azure-install-mobility-service.md) fájl- és nyomtatómegosztás engedélyezése.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) konfigurációjának ellenőrzése (hibakód: 95103)

Ellenőrizze a fájl- és nyomtatómegosztás szolgáltatás, engedélyezze a WMI-szolgáltatás a privát, nyilvános és a tartomány profilok tűzfalon keresztül. Ezek a beállítások a forrásgépen távoli végrehajtás végrehajtásához szükségesek. Ha engedélyezni szeretné,

* Nyissa meg a Vezérlőpultot, kattintson a biztonsági és majd a Windows tűzfal elemre.
* Kattintson a beállítások módosítása gombra, és kattintson a Kivételek lapon.
* A kivételek ablakban válassza ki a jelölőnégyzetet a Windows Management Instrumentation (WMI) a WMI-forgalom tűzfalon való engedélyezéséhez. 

WMI-forgalmat a parancssorban a tűzfalon keresztül is engedélyezheti. A következő paranccsal `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Az alábbi cikkekben talál további WMI hibaelhárítási cikkek található.

* [Alapszintű WMI tesztelése](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [A WMI-hibaelhárítás](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI-parancsfájlok és a WMI-szolgáltatásokkal kapcsolatos problémák](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nem támogatott operációs rendszerek

Nem támogatott operációs rendszer egy másik Ennek leggyakoribb oka a hiba oka lehet. Győződjön meg arról, a sikeres telepítés a mobilitási szolgáltatást a támogatott operációs rendszer/Kernel verziója. Kerülje a privát javítás használatát.
Az operációs rendszerek és az Azure Site Recovery által támogatott kernel-verzióknál listájának megtekintéséhez, tekintse meg a [támogatási mátrix dokumentum](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Rendszerindító és a rendszerpartíciók / kötetek nem ugyanazon a lemezen (ErrorID: 95309)

Mielőtt 9.20 verzió, rendszerindító és a rendszerpartíciók / eltérő lemezeken lévő kötetek volt konfigurációja nem támogatott. A [9.20 verzió](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), ez a konfiguráció támogatott. A támogatási használja a legújabb verziót.

## <a name="boot-disk-not-found-errorid-95310"></a>Rendszerindító lemez nem található (ErrorID: 95310)

A rendszerindító lemez nem rendelkező virtuális gép nem védhető. Ez a virtuális gép zavartalan helyreállítási biztosítására a feladatátvételi művelet során. Rendszerindító lemez hiányában nem sikerült a feladatátvétel után indítsa el a gépet eredményez. Győződjön meg arról, hogy a virtuális gép rendszerindító lemezt tartalmaz, és próbálja megismételni a műveletet. Vegye figyelembe azt is, hogy ugyanazon a gépen több rendszerindító lemez nem támogatott.

## <a name="multiple-boot-disks-found-errorid-95311"></a>Több rendszerindító lemez található (ErrorID: 95311)

Egy több rendszerindító lemezzel rendelkező virtuális gép nincs olyan [támogatott konfigurációs](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Több lemezen található rendszerpartíció (ErrorID: 95313)

9.20 verziónál korábbi verziókban a legfelső szintű partíción vagy köteten több lemezen meghatározott volt konfigurációja nem támogatott. A [9.20 verzió](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), ez a konfiguráció támogatott. A támogatási használja a legújabb verziót.

## <a name="grub-uuid-failure-errorid-95320"></a>LÁRVAJÁRAT UUID-hiba (ErrorID: 95320)

Ha a forrásgépen lévő GRUB eszköznév UUID helyett használja, akkor a mobilitási ügynök telepítése sikertelen lesz. Kapcsolatba rendszergazdai módosítások GRUB-fájlba.

## <a name="lvm-support-from-920-version"></a>LVM támogatási 9.20 verzióról

Csak az adatlemezek 9.20 verziónál korábbi verziókban LVM volt támogatott. gyökérpartíció lemezpartíción kell és nem kell az LVM-kötet.

A [9.20 verzió](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), [operációsrendszer-lemez az LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) támogatott. A támogatási használja a legújabb verziót.

## <a name="insufficient-space-errorid-95524"></a>Nincs elég hely (ErrorID: 95524)

Ha a forrásgépen a mobilitási ügynök másolja, legalább 100 MB szabad terület megadása kötelező. Ezért győződjön meg arról, hogy a forrásgép szabad lemezterület szükséges, és próbálja megismételni a műveletet.

## <a name="vss-installation-failures"></a>VSS telepítési hibák

Telepíteni a VSS telepíteni a mobilitási ügynök része. Ez a szolgáltatás alkalmazás-konzisztens helyreállítási pontok létrehozása során használatos. VSS-telepítés során fellépő hibák több okok miatt fordulhat elő. A pontos hiba azonosításához tekintse meg **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Néhány gyakori hibák és a megoldási lépések ki vannak emelve a következő szakaszt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Kilépési kód 511 [0x800706BE] --2147023170 VSS-hiba

A probléma többnyire akkor látható, ha egy víruskereső szoftver blokkolja a műveletek az Azure Site Recovery services. A probléma megoldásához,

1. Említett összes mappákat [Itt](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Kövesse az útmutatásokat, a Windows a DLL regisztrációját tiltásának feloldásához a hagyományos vírusirtó szolgáltató által közzétett.

### <a name="vss-error-7-0x7---exit-code-511"></a>7 [0x7] - kilépési kód 511 VSS-hiba

Ez a futásidejű hiba, és telepíteni a VSS nincs elég memória oka Győződjön meg arról, ez a művelet sikeres befejezését a lemezterület növelése érdekében.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Kilépési kód 517 [0x80070430] --2147023824 VSS-hiba

Ez a hiba akkor fordul elő, ha az Azure Site Recovery VSS Provider szolgáltatás [törlésre](https://msdn.microsoft.com/en-us/library/ms838153.aspx). Próbálja ki a VSS manuális telepítése a forrásgépen a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-hiba-2147023841 [0x8007041F] - 512. kilépési kód

Ez a hiba akkor fordul elő, ha az Azure Site Recovery VSS Provider szolgáltatás adatbázisa a [zárolva](https://msdn.microsoft.com/en-us/library/ms833798.aspx). Próbálja ki a VSS manuális telepítése a forrásgépen a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS kilépési kód 806

Ez a hiba akkor fordul elő, amikor telepítéséhez használt felhasználói fiók nem rendelkezik engedélyekkel CSScript parancs végrehajtása. Adja meg a szükséges engedélyekkel a felhasználói fiók a szkript végrehajtásához, és próbálja megismételni a műveletet.

### <a name="other-vss-errors"></a>Más VSS-hibák

Próbálja ki a VSS-szolgáltató szolgáltatás manuális telepítése a forrásgépen a következő parancssor futtatásával

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](vmware-azure-tutorial.md) való vészhelyreállítás beállítása VMware virtuális gépekhez.
