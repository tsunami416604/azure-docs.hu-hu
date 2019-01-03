---
title: Vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery replikációs problémák elhárítása |} A Microsoft Docs
description: Ez a cikk a VMware virtuális gépek vészhelyreállítása során általános replikációs problémákat hibaelhárítási információkat és fizikai kiszolgálók az Azure-bA az Azure Site Recovery nyújt.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 1c37b764b47856d3a369228d3f224f2a464029bb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790656"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware virtuális gépek és fizikai kiszolgálók replikációjával kapcsolatos problémák elhárítása

Egy adott hibaüzenet jelenhet meg a VMware virtuális gépek vagy fizikai kiszolgálók az Azure Site Recovery védelme esetén. Ez a cikk azt ismerteti, replikálja a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA során esetlegesen jelentkező gyakori problémákat [Azure Site Recovery](site-recovery-overview.md).


## <a name="initial-replication-issues"></a>Kezdeti replikációs problémákat

Sok esetben a kezdeti replikálási hibákat, a Microsoft terméktámogatási előforduló vannak kiszolgálói folyamat forráskiszolgáló vagy folyamat kiszolgáló – Azure közötti kapcsolódási problémák miatt. A legtöbb esetben ezek a problémák elhárításához az alábbi lépéseket követve.

### <a name="verify-the-source-machine"></a>A forrásgép ellenőrzése
* A forráskiszolgáló gép parancssorból a Telnet használatával pingelni a Folyamatkiszolgáló https-port (alapértelmezés: 9443-as), megtekintheti, ha vannak-e hálózati kapcsolat hibái vagy tűzfal port hátráltató alább látható módon.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > A Telnet használja, ne használja a PINGELÉS kapcsolat teszteléséhez.  Ha Telnet nincs telepítve, kövesse a lépéseket lista [Itt](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Nem lehet csatlakozni, ha engedélyezi a Folyamatkiszolgálón a 9443-as a bejövő portot, és ellenőrizze, hogy ha a probléma továbbra is kilép. Bizonyos esetekben, ahol a folyamatkiszolgáló DMZ-t, amely a problémás volt mögött lett lett.

* Szolgáltatás állapotának ellenőrzése `InMage Scout VX Agent – Sentinel/OutpostStart` Ha nem fut, és ellenőrizze, ha a probléma továbbra is létezik.   

### <a name="verify-the-process-server"></a>Ellenőrizze a folyamatkiszolgáló

* **Ellenőrizze, hogy ha a folyamatkiszolgáló van aktív küld-e adatokat az Azure-bA**

A Folyamatkiszolgáló gépről nyissa meg a Feladatkezelőt (nyomja meg a Ctrl-Shift-Esc). Nyissa meg a teljesítmény lapot, majd kattintson a "Nyissa meg az erőforrás-figyelő" hivatkozásra. Az erőforrás-kezelő, nyissa meg a hálózat lapot. Ellenőrizze, hogy ha a "Hálózati tevékenységgel rendelkező folyamat" cbengine.exe aktívan küld nagy adatmennyiség (a MB-ban).

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/cbengine.png)

Ha nem, kövesse az alábbi lépéseket:

* **Ellenőrizze, hogy tud csatlakozni az Azure Blob-e a folyamatkiszolgáló**: Válassza ki, és ellenőrizze, hogy van-e kapcsolat a folyamatkiszolgáló és az Azure Storage blob URL-címe "TCP-kapcsolatok" megtekintéséhez cbengine.exe.

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Ha nem majd lépjen a Vezérlőpult > szolgáltatások, ellenőrizze, hogy a következő szolgáltatások vannak-e működő:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Újra) Ha a probléma továbbra is fennáll, indítsa el a szolgáltatást, amely nem fut, és jelölje.

* **Ellenőrizze, hogy tud csatlakozni az Azure nyilvános IP-cím használatával a 443-as port-e a folyamatkiszolgáló**

Nyissa meg a a legújabb CBEngineCurr.errlog `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` és keresése: 443-as és a csatlakozási kísérlet nem sikerült.

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Probléma merül fel, majd a Folyamatkiszolgáló parancssorból, használja a telnet az Azure nyilvános IP-cím (maszkolva a fenti képen) megtalálható a 443-as porton CBEngineCurr.currLog ping.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Ha Ön nem lehet kapcsolódni, majd ellenőrizze, ha a hozzáférési probléma okozza-e tűzfal vagy Proxy a következő lépésben leírtak szerint.


* **Ellenőrizze, hogy ha a folyamatkiszolgáló IP-címalapú tűzfal nem blokkolja a hozzáférést**: Ha egy IP-címeken alapuló tűzfalszabályok szabályok használja a kiszolgálón, majd töltse le a Microsoft Azure adatközpont IP-címtartományok a teljes listáját [Itt](https://www.microsoft.com/download/details.aspx?id=41653) és hozzáadhatja őket, ellenőrizze, hogy engedélyezik a kommunikációt a tűzfal-konfiguráció Azure (és a HTTPS (443) port).  Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).

* **Ellenőrizze, hogy ha a folyamatkiszolgáló URL-alapú tűzfal nem blokkolja a hozzáférést**:  Ha a kiszolgáló URL-alapú tűzfalszabályokat használ, győződjön meg arról, a következő URL-címek bekerülnek tűzfal-konfiguráció.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **Ellenőrizze, hogy ha a proxykiszolgáló beállításait a folyamatkiszolgálón nem blokkolja-e hozzáférési**.  Ha proxykiszolgálót használ, győződjön meg arról, a DNS-kiszolgáló megoldása, a proxykiszolgáló nevét.
Ellenőrizze, hogy a konfigurációs kiszolgáló telepítése időpontjában van megadva. Ugrás a beállításkulcs

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Most győződjön meg arról, hogy ugyanazokat a beállításokat használják-e az Azure Site Recovery-ügynök által adatküldéshez.
Keresés a Microsoft Azure Backup

Nyissa meg és kattintson a művelet > Tulajdonságok módosítása. Proxykonfiguráció lapon megtekintheti a proxykiszolgáló címét, amely ahogy azt a beállításjegyzék-beállításokat a azonosnak kell lennie. Ha nem, módosítsa a címmel.


* **Ellenőrizze, hogy ha a sávszélesség-szabályozás nem korlátozza a folyamatkiszolgáló**:  Növelje a sávszélességet, és ellenőrizze, hogy a probléma továbbra is.

## <a name="source-machine-to-be-protected-through-site-recovery-is-not-listed-on-azure-portal"></a>Site Recovery védett forrásgép nem szerepel az Azure Portalon

A forrásoldali gép számára engedélyezze a replikációt az Azure Site Recovery kiválasztása közben a gép előfordulhat, hogy továbbra is a következő okok miatt nem érhető el.

* Ha két virtuális gép alapján a vCenter-példánnyal azonos UUID azonosító, majd az első virtuális gép konfigurációs kiszolgáló által felderített jelennek meg a portálon. Háríthatja el biztosíthatja nem két virtuális gép ugyanezen példányában UUID azonosítója.
* Győződjön meg arról, hozzáadta a helyes vCenter hitelesítő adatok során a OVF-sablon vagy az egységes konfiguráció. Ellenőrizze a megadott hitelesítő adatokkal, tekintse meg a megosztott irányelvek [Itt](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* Ha a vCenter eléréséhez megadott engedélyek nem rendelkezik elegendő jogosultsággal, hiba történt a virtuális gépek felderítése vezethet. Győződjön meg, hogy a megadott engedélyeket [Itt](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) hozzáadódnak a vCenter-felhasználói fiókot.
* Ha a virtuális gép Site Recovery már védett, akkor azt nem fogja készíteni. Győződjön meg arról, hogy a virtuális gépet a portálon a keresett még nem védett semmilyen más felhasználó vagy az egyéb előfizetések.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Védett virtuális gépek itt kiszürkítve jelennek meg a portálon

A Site Recovery replikált virtuális gépek szürkén jelennek meg e hiba ismétlődő bejegyzéseket a rendszerben. Tekintse meg a megadott irányelveket [Itt](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) törli az elavult bejegyzések és a probléma megoldásához.

## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, majd ezeket a lekérdezés [Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Egy aktív Közösség van, és segíthet a mérnökeinknek egyik lesz.
