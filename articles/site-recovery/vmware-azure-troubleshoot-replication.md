---
title: "A VMware virtuális gép és a fizikai kiszolgáló replikálás az Azure-bA az Azure Site Recovery replikációjával kapcsolatos problémák elhárítása |} Microsoft Docs"
description: "Ez a cikk ismerteti a VMware virtuális gépek és fizikai kiszolgálók replikálása Azure-bA az Azure Site Recovery szolgáltatással kapcsolatos gyakori replikációs problémák elhárítása."
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: asgang
ms.openlocfilehash: 9291840428c9a8d7ba5d65bc94ce5964728316f3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware virtuális gépek és fizikai kiszolgálók replikációjával kapcsolatos problémák elhárítása

ou egy adott hibaüzenet jelenhet meg, ha a VMware virtuális gépek vagy fizikai kiszolgálók Azure Site Recovery segítségével. Ez a cikk ismerteti replikálása a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure használata során felmerülő gyakori problémákat [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Kezdeti replikációs problémákat.

Sok esetben a kezdeti replikációs hibák, a Microsoft terméktámogatási előforduló olyan miatt problémák kiszolgálófolyamat forráskiszolgáló vagy folyamat kiszolgáló Azure között. A legtöbb esetben ezek a problémák elhárításához az alábbi lépéseket követve.

### <a name="verify-the-source-machine"></a>A forrásgép ellenőrzése
* A forráskiszolgáló gép parancssorból használja a Telnet pingelni a https-port (alapértelmezett 9443) a Folyamatkiszolgáló, megtekintéséhez, hogy vannak-e a hálózati kapcsolat hibái vagy a tűzfal port problémák elhárítását alább látható módon.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Használja a Telnet, ne használjon a PING való kapcsolat teszteléséhez.  Ha Telnet nincs telepítve, kövesse a lépéseket lista [Itt](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Ha nem lehet csatlakozni, engedélyezze a folyamatkiszolgáló 9443 a bejövő portot, és ellenőrizze, hogy ha a probléma továbbra is kilép. Egyes esetekben, ahol a folyamatkiszolgáló DMZ, amely a problémás volt mögött lett lett.

* A szolgáltatás állapotának ellenőrzése `InMage Scout VX Agent – Sentinel/OutpostStart` Ha még nem fut, valamint ellenőrizze, ha a probléma továbbra is létezik.   

## <a name="verify-the-process-server"></a>Ellenőrizze a folyamatkiszolgáló

* **Ellenőrizze, hogy ha a folyamatkiszolgáló aktívan küldését adatokat az Azure-bA**

A Folyamatkiszolgáló gépről nyissa meg a Feladatkezelőt (nyomja meg a Ctrl-Shift-Esc). Nyissa meg a Teljesítmény fülre, és "Nyissa meg az erőforrás-figyelő" hivatkozásra. Az erőforrás-kezelő hálózati lap Ugrás. Ellenőrizze, hogy ha a "Hálózati tevékenységet folyamatokban" cbengine.exe aktívan küld nagy mennyiségű (MB-ban) adat.

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/cbengine.png)

Ha nem, kövesse az alábbi lépéseket:

* **Annak ellenőrzése, hogy a folyamatkiszolgáló tud csatlakozni az Azure Blob**: válassza ki, és ellenőrizze a cbengine.exe megtekintéséhez a "TCP-kapcsolatok" a kiszolgáló közötti kapcsolatot folyamat Azure Storage-blob URL-címre van.

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Ha nem majd nyissa meg a Vezérlőpultot > szolgáltatások, ellenőrizze, hogy e a következő szolgáltatások működik, és:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Újra) Ha a probléma továbbra is fennáll, indítsa el a bármely szolgáltatás, amely nem fut és ellenőrzése.

* **Ellenőrizze, hogy tud csatlakozni az Azure nyilvános IP-cím 443-as porton-e a folyamatkiszolgáló**

Nyissa meg a legújabb CBEngineCurr.errlog `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` keresse meg a: 443-as és a kapcsolódási kísérlet sikertelen volt.

![A replikáció engedélyezése](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Ha problémák vannak, majd a Folyamatkiszolgáló parancssorból, használja a telnet pingelni a 443-as porton CBEngineCurr.currLog található (maszkolva a fenti kép) Azure nyilvános IP-címét.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Ha nem lehet csatlakozni, majd ellenőrizze, ha a hozzáférési probléma okozza-e tűzfal- vagy proxybeállításokat a következő lépésben leírtak szerint.


* **Ellenőrizze, hogy ha a folyamatkiszolgáló IP cím alapú tűzfal nem blokkolja a hozzáférést**: Ha egy IP-címeken alapuló tűzfalszabályok szabályokat használ a kiszolgálón, majd töltse le a Microsoft Azure Datacenter IP-címtartományok a teljes listáját [Itt](https://www.microsoft.com/download/details.aspx?id=41653) és a tűzfal konfigurációját, és győződjön meg arról, lehetővé teszik az Azure (és a HTTPS (443) port) való adja hozzá.  Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).

* **Ellenőrizze, hogy ha folyamatkiszolgáló URL-alapú tűzfal nem blokkolja a hozzáférést**: Ha egy URL-alapú tűzfal-szabályokat használ a kiszolgálón, győződjön meg arról, a következő URL-címek hozzáadódnak a tűzfal-konfiguráció.

  `*.accesscontrol.windows.net:` hozzáférés-vezérléshez és identitáskezeléshez

  `*.backup.windowsazure.com:` replikációs adatátvitelhez és vezényléshez

  `*.blob.core.windows.net:` A tárfiók, hogy a tároló replikált adatok elérésére használt

  `*.hypervrecoverymanager.windowsazure.com:` replikációkezelési műveletekhez és vezényléshez

  `time.nist.gov` és `time.windows.com`: rendszer és a globális időhöz közötti időszinkronizálást ellenőrizhető.

URL-címéből **Azure Government felhő**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Ellenőrizze, hogy ha a proxykiszolgáló beállításait a folyamatkiszolgáló nem blokkolja-e hozzáférési**.  Ha proxykiszolgálót használ, győződjön meg arról, a DNS-kiszolgáló megoldása, a proxykiszolgáló nevét.
Ellenőrizze, hogy a konfigurációs kiszolgáló telepítése során van megadva. Ugrás a beállításkulcs

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Most ellenőrizze, hogy ugyanazokat a beállításokat szolgáltatás alatt álló Azure Site Recovery-ügynök adatküldéshez.
Keresés a Microsoft Azure Backup szolgáltatás

Nyissa meg azt, majd kattintson a művelet > tulajdonságainak módosítása. A proxykonfiguráció lapon meg kell jelennie a proxykiszolgáló címét, amely a beállításjegyzékben szereplő beállítások eredményobjektumokról azonosnak kell lennie. Ha nem, akkor módosítsa a címmel.


* **Ellenőrizze, ha a sávszélesség-szabályozás nem korlátozott folyamatkiszolgáló**: növelje a sávszélességet, és ellenőrizze, hogy a probléma továbbra is léteznek.

## <a name="next-steps"></a>További lépések
Ha további segítségre van szüksége, majd indítsa el a lekérdezés [Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Egy aktív közösségi van, és a mérnökök egyik fog tudni segíteni.
