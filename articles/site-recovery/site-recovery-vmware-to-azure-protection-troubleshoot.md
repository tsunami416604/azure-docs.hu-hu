---
title: "Az Azure-bA VMware vagy fizikai védelmi hibák elhárítása |} Microsoft Docs"
description: "Ez a cikk ismerteti a közös VMware gép replikációs hibák, és azokat hibaelhárítása"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/26/2017
ms.author: asgang
ms.openlocfilehash: 6ebec2e06566b1e2d6834fdd81c0d8b2801b80b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>Helyszíni VMware vagy fizikai kiszolgáló replikációs problémák elhárítása
Specifikus hibaüzenet jelenhet meg, ha a VMware virtuális gépek vagy fizikai kiszolgálók Azure Site Recovery segítségével. Ez a cikk részletesen néhány, a hibát, és azok megoldását hibaelhárítási Gyakori hibaüzenetek.


## <a name="initial-replication-is-stuck-at-0"></a>A(z) 0 % elakadt a kezdeti replikálás
A kezdeti replikációs hibák, a Microsoft terméktámogatási előforduló többsége kiszolgálófolyamat forráskiszolgáló vagy folyamat kiszolgáló Azure közötti kapcsolat problémái miatt.
A legtöbb esetben is maga az alábbi lépéseket követve ezek a problémák elhárításához.

###<a name="check-the-following-on-source-machine"></a>Ellenőrizze a következőket a VÉDENDŐ GÉPEN
* A forráskiszolgáló gép parancssorból használja a Telnet pingelni a https-port (alapértelmezett 9443) a Folyamatkiszolgáló, megtekintéséhez, hogy vannak-e a hálózati kapcsolat hibái vagy a tűzfal port problémák elhárítását alább látható módon.
     
    `telnet <PS IP address> <port>`
> [!NOTE]
    > Használja a Telnet, ne használjon a PING való kapcsolat teszteléséhez.  Ha Telnet nincs telepítve, kövesse a lépéseket lista [Itt](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Ha nem lehet csatlakozni, engedélyezze a folyamatkiszolgáló 9443 a bejövő portot, és ellenőrizze, hogy ha a probléma továbbra is kilép. Egyes esetekben, ahol a folyamatkiszolgáló DMZ, amely a problémás volt mögött lett lett.

* A szolgáltatás állapotának ellenőrzése `InMage Scout VX Agent – Sentinel/OutpostStart` Ha még nem fut, valamint ellenőrizze, ha a probléma továbbra is létezik.   
 
###<a name="check-the-following-on-process-server"></a>Ellenőrizze a következőket folyamatkiszolgáló

* **Ellenőrizze, hogy ha a folyamatkiszolgáló aktívan küldését adatokat az Azure-bA** 

A Folyamatkiszolgáló gépről nyissa meg a Feladatkezelőt (nyomja meg a Ctrl-Shift-Esc). Nyissa meg a Teljesítmény fülre, és "Nyissa meg az erőforrás-figyelő" hivatkozásra. Az erőforrás-kezelő hálózati lap Ugrás. Ellenőrizze, hogy ha a "Hálózati tevékenységet folyamatokban" cbengine.exe aktívan küld nagy mennyiségű (MB-ban) adat.

![A replikáció engedélyezése](./media/site-recovery-protection-common-errors/cbengine.png)

Ha nem kövesse az alábbi lépéseket:

* **Annak ellenőrzése, hogy a folyamatkiszolgáló tud csatlakozni az Azure Blob**: válassza ki, és ellenőrizze a cbengine.exe megtekintéséhez a "TCP-kapcsolatok" a kiszolgáló közötti kapcsolatot folyamat Azure Storage-blob URL-címre van.

![A replikáció engedélyezése](./media/site-recovery-protection-common-errors/rmonitor.png)

Ha nem majd nyissa meg a Vezérlőpultot > szolgáltatások, ellenőrizze, hogy e a következő szolgáltatások működik, és:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     * 
(Újra) Indítsa el a szolgáltatást, amely nem fut, és ellenőrizze, hogy a probléma továbbra is léteznek.

* **Ellenőrizze, hogy tud csatlakozni az Azure nyilvános IP-cím 443-as porton-e a folyamatkiszolgáló**

Nyissa meg a legújabb CBEngineCurr.errlog `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` keresse meg a: 443-as és a kapcsolódási kísérlet sikertelen volt.

![A replikáció engedélyezése](./media/site-recovery-protection-common-errors/logdetails1.png)

Ha problémák vannak, majd a Folyamatkiszolgáló parancssorból, használja a telnet pingelni a 443-as porton CBEngineCurr.currLog található (maszkolva a fenti kép) Azure nyilvános IP-címét.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Ha nem lehet csatlakozni, majd ellenőrizze, ha a hozzáférési probléma okozza-e tűzfal- vagy proxybeállításokat a következő lépésben leírtak szerint.


* **Ellenőrizze, hogy ha a folyamatkiszolgáló IP cím alapú tűzfal nem blokkolja-e hozzáférési**: Ha egy IP-címeken alapuló tűzfalszabályok szabályokat használ a kiszolgálón, majd töltse le a Microsoft Azure Datacenter IP-címtartományok a teljes listáját [Itt](https://www.microsoft.com/download/details.aspx?id=41653) és a tűzfal konfigurációját, és győződjön meg arról, lehetővé teszik az Azure (és a HTTPS (443) port) való adja hozzá.  Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).

* **Ellenőrizze, hogy ha folyamatkiszolgáló URL-alapú tűzfal nem blokkolja a hozzáférést**: Ha egy URL-cím alapú tűzfalszabályok használ a kiszolgálón, győződjön meg arról, a következő URL-címek hozzáadódnak a tűzfal konfigurációját. 
     
  `*.accesscontrol.windows.net:` hozzáférés-vezérléshez és identitáskezeléshez

  `*.backup.windowsazure.com:` replikációs adatátvitelhez és vezényléshez

  `*.blob.core.windows.net:`A tárfiók, hogy a tároló replikált adatok elérésére használt

  `*.hypervrecoverymanager.windowsazure.com:` replikációkezelési műveletekhez és vezényléshez

  `time.nist.gov`és `time.windows.com`: rendszer és a globális időhöz közötti időszinkronizálást ellenőrizhető.

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

![A replikáció engedélyezése](./media/site-recovery-protection-common-errors/mab.png)

Nyissa meg azt, majd kattintson a művelet > tulajdonságainak módosítása. A proxykonfiguráció lapon meg kell jelennie a proxykiszolgáló címét, amely a beállításjegyzékben szereplő beállítások eredményobjektumokról azonosnak kell lennie. Ha nem, akkor módosítsa a címmel.

![A replikáció engedélyezése](./media/site-recovery-protection-common-errors/mabproxy.png)

* **Ellenőrizze, ha a sávszélesség-szabályozás nem korlátozott folyamatkiszolgáló**: növelje a sávszélességet, és ellenőrizze, hogy a probléma továbbra is léteznek.

##<a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége, majd indítsa el a lekérdezés [ASR fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Egy aktív közösségi van, és a mérnökök egyik fog tudni segíteni.