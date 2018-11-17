---
title: A mobilitási szolgáltatás leküldéses telepítési hibák elhárításához, ha a vész-helyreállítási replikáció engedélyezése |} A Microsoft Docs
description: A mobilitási szolgáltatás telepítési hibák elhárításához, ha a vész-helyreállítási replikáció engedélyezése
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: a9738f95ce8a0de750ffa348e167bce3b0e659f6
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821395"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>A mobilitási szolgáltatás leküldéses telepítési problémák elhárítása

Mobilitási szolgáltatás telepítésének legfontosabb lépése replikálás engedélyezése során. Ez a lépés sikeres kizárólag Előfeltételek teljesítése és a támogatott konfigurációk használata attól függ. A mobilitási szolgáltatás telepítése során között leggyakoribb hibák miatt

* Hitelesítő adatok vagy jogosultsági hibák
* Kapcsolódási hibák
* Nem támogatott operációs rendszerek
* VSS telepítési hibák

A replikáció engedélyezése az Azure Site Recovery megpróbálja küldje le a virtuális gép mobilitásiszolgáltatás-ügynök telepíthető. Ennek részeként konfigurációs kiszolgáló megkísérli a virtuális géppel csatlakozhat, és másolja az ügynököt. A sikeres telepítés engedélyezéséhez kövesse az alábbi részletes hibaelhárítási útmutatót.

## <a name="credentials-check-errorid-95107--95108"></a>Hitelesítő adatok ellenőrzése (ErrorID: 95107 & 95108)

* Győződjön meg arról, ha a felhasználói fiók, a replikáció engedélyezése közben kiválasztott, **érvényes, a pontos**.
* Az Azure Site Recovery igényel **rendszergazdai jogosultsággal** ügyfélleküldéses telepítés végrehajtásához.
  * Windows, ellenőrizze, ha a felhasználói fiók rendelkezik-e rendszergazdai hozzáféréssel, helyi vagy a tartományban, a forrásgépen.
  * Ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen szeretné.
    * Tiltsa le a távoli felhasználói hozzáférés-vezérlést, a localaccounttokenfilterpolicy beállításjegyzékbeli kulcs hozzáadása egy új DWORD: LocalAccountTokenFilterPolicy. Állítsa az értékét 1-re. Ez a lépés végrehajtásához futtassa a következő parancsot a parancssorba:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux esetén ki kell választania a mobilitási ügynök sikeres telepítéséhez rendszergazdai fiók.

Ha szeretné módosítani a kiválasztott felhasználói fiók hitelesítő adatait, hajtsa végre az adott utasítások [Itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="connectivity-check-errorid-95117--97118"></a>**Kapcsolat-ellenőrzés (ErrorID: 95117 & 97118)**

* Ellenőrizze, hogy pingelni a forrásgép a konfigurációs kiszolgálóról. Ha úgy döntött, hogy kibővíthető folyamatkiszolgáló a replikáció engedélyezése során, ellenőrizze, hogy a forrásgép folyamatkiszolgálóról pingelni.
  * A forráskiszolgáló gép parancssorból, a Telnet használatával a konfigurációs kiszolgáló pingelése / horizontális felskálázási folyamatkiszolgáló a https (135-ös port) megtekintéséhez, ha vannak-e hálózati kapcsolat hibái vagy tűzfal port hátráltató alább látható módon.

     `telnet <CS/ scale-out PS IP address> <135>`
  * Szolgáltatás állapotának ellenőrzése **InMage Scout VX Agent – Sentinel/Outpost**. Ha nem fut, indítsa el a szolgáltatást.
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
* Kapcsolat/hálózati/tartomány lehet okozza. Azt is okozhatja, hogy a probléma vagy a TCP-port Erőforrásfogyás probléma megoldásának DNS-név. Tekintse meg van-e olyan ismert problémákat a tartományban.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>A fájl- és nyomtatómegosztási szolgáltatások ellenőrzése (ErrorID: 95105 & 95106)

Kapcsolat-ellenőrzés után ellenőrizze a fájl- és nyomtatómegosztás szolgáltatás engedélyezésének a virtuális gépen.

A **windows 2008 R2 és korábbi verziók**,

* Fájl- és nyomtatómegosztás Windows tűzfalon, engedélyezése
  * Nyissa meg a Vezérlőpult -> rendszer és Biztonság -> Windows tűzfal -> bal oldali ablaktáblán, kattintson a Speciális beállítások -> konzolfán kattintson a bejövő szabályok.
  * Keresse meg a fájlt, és nyomtatómegosztás (NetBIOS-munkamenet-) és a fájl- és nyomtatómegosztás (SMB, bejövő) szabályok. Az egyes szabályokhoz, kattintson a jobb gombbal a szabályt, és kattintson **szabály engedélyezése**.
* A csoportházirenddel, fájlmegosztási engedélyezése
  * Ugrás a kezdő, írja be a gpmc.msc, és keressen.
  * A navigációs ablaktáblán nyissa meg a következő mappák: helyi számítógép-házirend, a felhasználói konfiguráció, a felügyeleti sablonok, a Windows-összetevők és a hálózati megosztás.
  * A részleteket tartalmazó ablaktáblán kattintson duplán a **megakadályozhatja a felhasználókat a profilon belül a fájlok megosztása**. Tiltsa le a csoportházirend-beállítást, és engedélyezze a felhasználó engedélyének fájlokat, kattintson a le van tiltva. Kattintson az OK gombra a módosítások mentéséhez. További tudnivalókért kattintson [Itt](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

A **újabb verzió**, kövesse a megjelenő utasításokat [Itt](vmware-azure-install-mobility-service.md) fájl- és nyomtatómegosztás engedélyezése.

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Windows Management Instrumentation (WMI) konfigurációjának ellenőrzése

Ellenőrizze a fájl- és nyomtatómegosztás szolgáltatás, engedélyezze a WMI-szolgáltatás tűzfalán keresztül.

* A Vezérlőpulton kattintson a biztonsági, és kattintson a Windows tűzfal.
* Kattintson a beállítások módosítása gombra, és kattintson a Kivételek lapon.
* A kivételek ablakban válassza ki a jelölőnégyzetet a Windows Management Instrumentation (WMI) a WMI-forgalom tűzfalon való engedélyezéséhez. 

WMI-forgalmat a parancssorban a tűzfalon keresztül is engedélyezheti. A következő paranccsal `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Az alábbi cikkekben talál további WMI hibaelhárítási cikkek található.

* [Alapszintű WMI tesztelése](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [A WMI-hibaelhárítás](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI-parancsfájlok és a WMI-szolgáltatásokkal kapcsolatos problémák](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Nem támogatott operációs rendszerek

Nem támogatott operációs rendszer egy másik Ennek leggyakoribb oka a hiba oka lehet. Győződjön meg arról, a sikeres telepítés a mobilitási szolgáltatást a támogatott operációs rendszer/Kernel verziója.

Ha szeretné megtudni, melyik operációs rendszerek támogatottak az Azure Site Recovery, tekintse meg a [támogatási mátrix dokumentum](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="vss-installation-failures"></a>VSS telepítési hibák

Telepíteni a VSS telepíteni a mobilitási ügynök része. Ez a szolgáltatás alkalmazás-konzisztens helyreállítási pontok létrehozása során használatos. VSS-telepítés során fellépő hibák több okok miatt fordulhat elő. A pontos hiba azonosításához tekintse meg **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Néhány gyakori hibák és a megoldási lépések ki vannak emelve a következő szakaszt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Kilépési kód 511 [0x800706BE] --2147023170 VSS-hiba

A probléma többnyire akkor látható, ha egy víruskereső szoftver blokkolja a műveletek az Azure Site Recovery services. A probléma megoldásához,

1. Említett összes mappákat [Itt](vmware-azure-set-up-source.md#exclude-antivirus-on-the-configuration-server).
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