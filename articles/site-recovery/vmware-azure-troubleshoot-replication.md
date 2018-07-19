---
title: A VMware virtuális gépek és fizikai kiszolgálók replikálásához az Azure-bA az Azure Site Recovery replikációs problémák elhárítása |} A Microsoft Docs
description: Ez a cikk ismerteti, amikor a VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure-bA az Azure Site Recovery közös replikációs problémáinak elhárítása.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: c2100ee2388ae09bd309167b1be77f7bdbe32f69
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126017"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware virtuális gépek és fizikai kiszolgálók replikációjával kapcsolatos problémák elhárítása

Egy adott hibaüzenet jelenhet meg a VMware virtuális gépek vagy fizikai kiszolgálók az Azure Site Recovery védelme esetén. Ez a cikk azt ismerteti, replikálja a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA során esetlegesen jelentkező gyakori problémákat [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Kezdeti replikálási problémákat.

Sok esetben a kezdeti replikálási hibákat, a Microsoft terméktámogatási előforduló vannak kiszolgálói folyamat forráskiszolgáló vagy folyamat kiszolgáló – Azure közötti kapcsolódási problémák miatt. A legtöbb esetben ezek a problémák elhárításához az alábbi lépéseket követve.

### <a name="verify-the-source-machine"></a>A forrásgép ellenőrzése
* A forráskiszolgáló gép parancssorból a Telnet használatával pingelni a Folyamatkiszolgáló https-port (alapértelmezés: 9443-as), megtekintheti, ha vannak-e hálózati kapcsolat hibái vagy tűzfal port hátráltató alább látható módon.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > A Telnet használja, ne használja a PINGELÉS kapcsolat teszteléséhez.  Ha Telnet nincs telepítve, kövesse a lépéseket lista [Itt](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Nem lehet csatlakozni, ha engedélyezi a Folyamatkiszolgálón a 9443-as a bejövő portot, és ellenőrizze, hogy ha a probléma továbbra is kilép. Bizonyos esetekben, ahol a folyamatkiszolgáló DMZ-t, amely a problémás volt mögött lett lett.

* Szolgáltatás állapotának ellenőrzése `InMage Scout VX Agent – Sentinel/OutpostStart` Ha nem fut, és ellenőrizze, ha a probléma továbbra is létezik.   

## <a name="verify-the-process-server"></a>Ellenőrizze a folyamatkiszolgáló

* **Ellenőrizze, hogy ha a folyamatkiszolgáló van aktív küld-e adatokat az Azure-bA**

A Folyamatkiszolgáló gépről nyissa meg a Feladatkezelőt (nyomja meg a Ctrl-Shift-Esc). Nyissa meg a teljesítmény lapot, majd kattintson a "Nyissa meg az erőforrás-figyelő" hivatkozásra. Az erőforrás-kezelő, nyissa meg a hálózat lapot. Ellenőrizze, hogy ha a "Hálózati tevékenységgel rendelkező folyamat" cbengine.exe aktívan küld nagy adatmennyiség (a MB-ban).

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/cbengine.png)

Ha nem, kövesse az alábbi lépéseket:

* **Ellenőrizze, hogy tud csatlakozni az Azure Blob-e a folyamatkiszolgáló**: válassza ki, és ellenőrizze, hogy van-e kapcsolat a folyamatkiszolgáló és az Azure Storage blob URL-címe "TCP-kapcsolatok" megtekintéséhez cbengine.exe.

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

Nyissa meg a a legújabb CBEngineCurr.errlog `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` és keressen rá a: 443-as és a csatlakozási kísérlet nem sikerült.

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Probléma merül fel, majd a Folyamatkiszolgáló parancssorból, használja a telnet az Azure nyilvános IP-cím (maszkolva a fenti képen) megtalálható a 443-as porton CBEngineCurr.currLog ping.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Ha Ön nem lehet kapcsolódni, majd ellenőrizze, ha a hozzáférési probléma okozza-e tűzfal vagy Proxy a következő lépésben leírtak szerint.


* **Ellenőrizze, hogy ha a folyamatkiszolgáló IP-címalapú tűzfal nem blokkolja a hozzáférést**: Ha egy IP-címeken alapuló tűzfalszabályok szabályok használja a kiszolgálón, majd töltse le a Microsoft Azure adatközpont IP-címtartományok a teljes listáját [Itt](https://www.microsoft.com/download/details.aspx?id=41653) adja hozzá a tűzfal beállításait a ellenőrizze, hogy engedélyezik a kommunikációt az Azure (és a HTTPS (443) port).  Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).

* **Ellenőrizze, hogy ha a folyamatkiszolgáló URL-alapú tűzfal nem blokkolja a hozzáférést**: Ha a kiszolgáló URL-alapú tűzfalszabályokat használ, győződjön meg arról, a következő URL-címek bekerülnek tűzfal-konfiguráció.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **Ellenőrizze, hogy ha a proxykiszolgáló beállításait a folyamatkiszolgálón nem blokkolja-e hozzáférési**.  Ha proxykiszolgálót használ, győződjön meg arról, a DNS-kiszolgáló megoldása, a proxykiszolgáló nevét.
Ellenőrizze, hogy a konfigurációs kiszolgáló telepítése időpontjában van megadva. Ugrás a beállításkulcs

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Most győződjön meg arról, hogy ugyanazokat a beállításokat használják-e az Azure Site Recovery-ügynök által adatküldéshez.
Keresés a Microsoft Azure Backup

Nyissa meg és kattintson a művelet > Tulajdonságok módosítása. Proxykonfiguráció lapon megtekintheti a proxykiszolgáló címét, amely ahogy azt a beállításjegyzék-beállításokat a azonosnak kell lennie. Ha nem, módosítsa a címmel.


* **Ellenőrizze, hogy ha a sávszélesség-szabályozás nem korlátozza a folyamatkiszolgáló**: növelje a sávszélességet, és ellenőrizze, hogy a probléma továbbra is.

## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, majd ezeket a lekérdezés [Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Egy aktív Közösség van, és segíthet a mérnökeinknek egyik lesz.
