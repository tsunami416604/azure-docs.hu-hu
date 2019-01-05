---
title: VMware virtuális gépek és fizikai kiszolgálók Azure-bA vész-helyreállítási replikációs problémák hibaelhárítása az Azure Site Recovery használatával |} A Microsoft Docs
description: Ez a cikk során a VMware virtuális gépek vészhelyreállítása az általános replikációs problémákat hibaelhárítási információkat és fizikai kiszolgálók Azure-bA az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 30f128e75feb149453b642739f57c3a16ade524f
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053091"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware virtuális gépek és fizikai kiszolgálók replikációjával kapcsolatos problémák elhárítása

A VMware virtuális gépek vagy fizikai kiszolgálók védelme az Azure Site Recovery használatával egy adott hiba általános jelentését üzenet előfordulhat, hogy jelenik meg. Ez a cikk ismerteti, a helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure használatával során esetlegesen jelentkező gyakori problémákat [Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Kezdeti replikációs problémákat

Kezdeti replikációs hibák gyakran okozza a forráskiszolgáló és a folyamatkiszolgáló között, vagy a folyamatkiszolgáló és az Azure közötti kapcsolódási problémák. A legtöbb esetben ezek a problémák elhárításához a következő szakaszokban ismertetett lépéseket követve.

### <a name="check-the-source-machine"></a>Ellenőrizze a forrásgépen

A következő listában látható módon ellenőrizheti, hogy a forrásgép:

*  A parancssorban a forráskiszolgálón használja a Telnet a folyamatkiszolgálót a HTTPS-portot (az alapértelmezett HTTPS-port a 9443-as) keresztül pingelni a következő parancs futtatásával. A parancs ellenőrzi a hálózati kapcsolattal fennálló problémákat, és a problémák, amelyek blokkolják a tűzfalport.

   `telnet <process server IP address> <port>`

   > [!NOTE]
   > A Telnet használatával kapcsolat tesztelése. Ne használjon `ping`. Ha a Telnet nincs telepítve, végezze el a felsorolt lépéseket [Telnet-ügyfél telepítése](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   A folyamatkiszolgáló nem tud csatlakozni, ha engedélyezi a bejövő 9443-as a folyamatkiszolgálón. Például előfordulhat, hogy a folyamatkiszolgálón a 9443-as a bejövő portot, ha a hálózaton szegélyhálózaton vagy alapos szűrésen átesett alhálózat. Ezután ellenőrizze, hogy a probléma továbbra is fennáll-e.

*  Ellenőrizze az állapotát a **InMage Scout VX Agent – Sentinel/OutpostStart** szolgáltatás. Ha a szolgáltatás nem fut, indítsa el a szolgáltatást, és ellenőrizze, hogy a probléma továbbra is jelentkezik.   

### <a name="check-the-process-server"></a>Ellenőrizze a folyamatkiszolgáló

A következő listában látható módon ellenőrizheti, hogy a folyamatkiszolgáló:

*  **Ellenőrizze, hogy a folyamatkiszolgáló van aktívan küld-e adatokat az Azure-bA**.

   1. A folyamatkiszolgáló nyissa meg a Feladatkezelőt (nyomja meg a Ctrl + Shift + Esc).
   2. Válassza ki a **teljesítmény** lapra, és válassza ki a **nyissa meg az erőforrás-figyelő** hivatkozásra. 
   3. Az a **erőforrás-figyelő** lapon válassza ki a **hálózati** fülre. Alatt **hálózati tevékenységgel rendelkező folyamat**, ellenőrizze hogy **cbengine.exe** aktívan küld nagy mennyiségű adat.

        ![A kötetek a hálózati tevékenységgel rendelkező folyamatot bemutató képernyőkép](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Ha cbengine.exe nagy mennyiségű adatot nem küld, hajtsa végre az alábbi szakaszokban található.

*  **Ellenőrizze, hogy a folyamatkiszolgáló csatlakozhat az Azure Blob storage**.

   Válassza ki **cbengine.exe**. A **TCP-kapcsolatok**, ellenőrizze, hogy van-e kapcsolat a folyamatkiszolgáló és az Azure Blog storage URL-címe.

   ![Cbengine.exe és az Azure Blob storage URL-cím közötti kapcsolatot bemutató képernyőkép](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   Ha nincs kapcsolat a folyamatkiszolgálóról az Azure Blog storage URL-címet, a Vezérlőpulton válassza **szolgáltatások**. Ellenőrizze, hogy futnak-e a következő szolgáltatásokat:

   *  cxprocessserver
   *  Az InMage Scout VX Agent – Sentinel/Outpost
   *  Microsoft Azure Recovery Services Agent
   *  Microsoft Azure Site Recovery szolgáltatás
   *  tmansvc

   Indítsa el, vagy bármely szolgáltatás nem fut, indítsa újra. Ellenőrizze, hogy a probléma továbbra is fennáll-e.

*  **Ellenőrizze-e a folyamatkiszolgáló csatlakozhat az Azure nyilvános IP-cím használatával a 443-as porton**.

   Az Azure Recovery Services Agent\Temp %programfiles%\Microsoft nyissa meg a legújabb CBEngineCurr.errlog fájlt. A fájlban keresse meg **443-as** vagy karakterláncnak **kapcsolódási kísérlet sikertelen**.

   ![Képernyőkép a hiba jelentkezik be a Temp mappa](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Ha problémák vannak feltüntetve, a parancssorban a folyamatkiszolgálón a Telnet használatával ping az Azure nyilvános IP-cím (az IP-cím van maszkolva az előző képen). 443-as porton keresztül az Azure nyilvános IP-címet a CBEngineCurr.currLog fájlban találja meg:

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   Ha nem sikerül, ellenőrizze, hogy a hozzáférési probléma miatt tűzfal vagy a proxykiszolgáló beállításait a következő lépésben leírtak szerint.

*  **Ellenőrizze, hogy a folyamatkiszolgáló IP cím-alapú tűzfala blokkolja a hozzáférést**.

   Ha a kiszolgáló IP-cím-alapú tűzfalszabályainak használja, töltse le a teljes listáját [Microsoft Azure adatközpont IP-címtartományait](https://www.microsoft.com/download/details.aspx?id=41653). Adja hozzá az IP-címtartományok a tűzfal beállításait a győződjön meg arról, hogy a tűzfal engedélyezi-e a kommunikációt az Azure-ba (és az alapértelmezett HTTPS-port, 443-as porton). Lehetővé teszik az IP-címtartományokat az előfizetés Azure-régió és Azure USA nyugati régiójában (hozzáférés-vezérléshez és identitáskezeléshez kezelés használatos).

*  **Ellenőrizze, hogy a folyamatkiszolgáló URL-alapú tűzfala blokkolja a hozzáférést**.

   Egy URL-cím és a kiszolgálóalapú tűzfalak szabály használatakor a kiszolgálón, adja hozzá az URL-címeket, a tűzfal-konfiguráció a következő táblázatban:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Ellenőrizze, hogy a proxykiszolgáló beállításait a folyamatkiszolgálón a hozzáférés letiltása**.

   Ha proxykiszolgálót használ, győződjön meg arról, hogy a proxykiszolgáló nevét feloldja a DNS-kiszolgáló. A konfigurációs kiszolgáló beállításakor a megadott érték ellenőrzéséhez nyissa meg a beállításkulcs **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure hely Recovery\ProxySettings**.

   Ezután ellenőrizze, hogy ugyanazokat a beállításokat az Azure Site Recovery-ügynök által használ is küldhet adatokat: 
      
   1. Keresse meg **a Microsoft Azure Backup**. 
   2. Nyissa meg **a Microsoft Azure Backup**, majd válassza ki **művelet** > **tulajdonságainak módosítása**. 
   3. Az a **proxykonfiguráció** lapon kell megjelennie a proxykiszolgáló címét. A proxykiszolgáló címét a proxykiszolgáló címét, a beállításjegyzék-beállításainak látható megegyezőnek kell lennie. Ha nem, akkor ugyanazt a címet módosítsa azt.

*  **Ellenőrizze, hogy a szabályozás sávszélesség korlátozott-e a folyamatkiszolgáló**.

   Növelje a sávszélességet, és ellenőrizze, hogy a probléma továbbra is fennáll-e.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Forrásgép nem szerepel a listán az Azure Portalon

Válassza ki a forrásoldali gép számára engedélyezze a replikációt a Site Recovery használatával megkísérlésekor a gép esetleg nem érhetők el a következő okok valamelyike:

*  Ha a vcenter-kiszolgáló mellett két virtuális gépet ugyanazon UUID azonosító, az első virtuális gépen észlelt a konfigurációs kiszolgáló által az Azure Portalon látható. A probléma megoldásához, biztosíthatja a nincs két virtuális gépet ugyanazon UUID azonosítója.
*  Győződjön meg arról, hogy hozzáadta a helyes vCenter hitelesítő adatok beállításakor a konfigurációs kiszolgáló OVF-sablon vagy egyesített telepítő használatával. A telepítés során hozzáadott hitelesítő adatok ellenőrzéséhez tekintse meg a [módosíthatják az automatikus felderítési hitelesítő adatait](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
*  Ha a vCenter eléréséhez megadott engedélyek nem rendelkezik a szükséges engedélyekkel, virtuális gépek felderítése sikertelen történhet meg. Győződjön meg arról, hogy az engedélyek ismertetett [fiók előkészítése automatikus felderítéshez](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) hozzáadódnak a vCenter-felhasználói fiókot.
*  Ha a virtuális gép Site Recovery már védett, a virtuális gép nem érhető el, válassza a védelemhez a portálon. Győződjön meg arról, hogy a virtuális gépet a portálon a keresett már nem védett, amelyet semmilyen más felhasználó vagy egy másik előfizetésben.

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>Védett virtuális gépek nem állnak rendelkezésre a portálon

A Site Recovery replikált virtuális gépek nem érhetők el az Azure Portalon, ha a rendszer ismétlődő bejegyzéseket tartalmaz. Törli az elavult bejegyzések és a probléma megoldásához kapcsolatban lásd: [Azure Site Recovery VMware – Azure: Eltávolítás az ismétlődő vagy elavult bejegyzés](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége, tegye közzé a kérdését a [Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Egy aktív Közösség van, és egyet a mérnökeinknek a segítségére lehessen.
