---
title: Diagnosztika mérőszámokkal, riasztásokkal és erőforrás-állapottal – Azure standard Load Balancer
description: Az Azure-standard Load Balancer diagnosztizálásához használja az elérhető metrikákat, riasztásokat és erőforrás-állapotokra vonatkozó információkat.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: c362829b1babf954868452a3858da1f319008a9a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990776"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Metrikák, riasztások és az erőforrások állapotának diagnosztikái a standard Load Balancerben

Az Azure standard Load Balancer a következő diagnosztikai képességeket teszi elérhetővé:

* **Többdimenziós mérőszámok és riasztások**: többdimenziós diagnosztikai képességeket biztosít a standard Load Balancer-konfigurációk [Azure monitoron](https://docs.microsoft.com/azure/azure-monitor/overview) keresztül. A standard Load Balancer-erőforrások figyelésére, kezelésére és hibakeresésére van lehetőség.

* **Erőforrás állapota**: a Azure Portal Load Balancer lapja és a Resource Health lap (a figyelő alatt) tegye elérhetővé a standard Load Balancer Resource Health szakaszát. 

Ez a cikk gyors áttekintést nyújt ezekről a képességekről, és lehetőséget nyújt a standard Load Balancer használatára. 

## <a name = "MultiDimensionalMetrics"></a>Többdimenziós metrikák

Azure Load Balancer többdimenziós metrikákat biztosít az Azure-metrikák használatával a Azure Portalban, és segít valós idejű diagnosztikai információk beszerzésében a terheléselosztó erőforrásaiban. 

A különböző standard Load Balancer konfigurációk a következő metrikákat biztosítják:

| Metrika | Erőforrás típusa | Leírás | Ajánlott összesítés |
| --- | --- | --- | --- |
| Adatelérési út rendelkezésre állása (VIP rendelkezésre állása)| Nyilvános és belső terheléselosztó | Standard Load Balancer folyamatosan gyakorolja az adatelérési utat a régión belülről a terheléselosztó kezelőfelületére, egészen az SDN-veremig, amely támogatja a virtuális gépet. Amíg a kifogástalan állapotú példányok megmaradnak, a mérés ugyanazt az útvonalat követi, mint az alkalmazás elosztott terhelésű forgalma. Az ügyfelek által használt adatelérési út is érvényesítve lesz. A mérték láthatatlan az alkalmazás számára, és nem zavarja a többi műveletet.| Átlag |
| Állapot mintavételi állapota (DIP rendelkezésre állása) | Nyilvános és belső terheléselosztó | A standard Load Balancer egy elosztott állapot-ellenőrzési szolgáltatást használ, amely figyeli az alkalmazás-végpont állapotát a konfigurációs beállításoknak megfelelően. Ez a metrika a terheléselosztó készletében lévő minden példány végpontjának összesített vagy végponti szűrt nézetét biztosítja. Megtudhatja, hogyan tekintheti meg Load Balancer az alkalmazás állapotát, ahogy azt az állapot-mintavételi konfiguráció jelzi. |  Átlag |
| (Szinkronizált) csomagok | Nyilvános és belső terheléselosztó | A standard Load Balancer nem szakítja meg Transmission Control Protocol (TCP) kapcsolatait, vagy nem kommunikál a TCP-vagy UDP-csomagok forgalmával. A folyamatok és a kézfogások mindig a forrás és a virtuálisgép-példány között vannak. A TCP protokollal kapcsolatos forgatókönyvek jobb megoldásához használhatja az SYN-csomagok számlálóit, hogy megtudja, hány TCP-kapcsolati kísérletet hajt végre a rendszer. A metrika a fogadott TCP SYN-csomagok számát jelenti.| Átlag |
| SNAT-kapcsolatok | Nyilvános Load Balancer |Standard Load Balancer a nyilvános IP-címhez maszkolás kimenő folyamatok számát jelenti. A forrásoldali hálózati címfordítási (SNAT) portok kimeríthető erőforrások. Ez a metrika arra utalhat, hogy az alkalmazás milyen mértékben támaszkodik a SNAT a kimenő folyamatokból. A sikeres és sikertelen kimenő SNAT folyamatokra vonatkozó számlálókat jelentettek, és felhasználhatók a kimenő folyamatok állapotának hibakeresésére és megismerésére.| Átlag |
| Lefoglalt SNAT-portok | Nyilvános Load Balancer | standard Load Balancer a háttérbeli példányok által lefoglalt SNAT-portok számát jelenti | Átlagos. |
| Használt SNAT-portok | Nyilvános Load Balancer | Standard Load Balancer a háttérbeli példányok által használt SNAT-portok számát jelenti. | Átlag | 
| Bájtok számlálói |  Nyilvános és belső terheléselosztó | Standard Load Balancer az előtér által feldolgozott adatmennyiséget jelenti. Észreveheti, hogy a bájtok nincsenek egyenlően elosztva a háttérbeli példányok között. Ez várhatóan az Azure Load Balancer algoritmusa a folyamatokon alapul. | Átlag |
| Csomagok számlálói |  Nyilvános és belső terheléselosztó | Standard Load Balancer jelentést készít az előtér által feldolgozott csomagokról.| Átlag |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>A terheléselosztó metrikáinak megtekintése a Azure Portalban

A Azure Portal megjeleníti a terheléselosztó mérőszámait a metrikák lapon, amely egy adott erőforráshoz és a Azure Monitor laphoz is elérhető a terheléselosztó erőforrás oldalán. 

A standard Load Balancer erőforrások metrikáinak megtekintése:
1. Nyissa meg a metrikák lapot, és tegye a következők egyikét:
   * A terheléselosztó erőforrása lapon válassza ki a metrika típusát a legördülő listából.
   * A Azure Monitor lapon válassza ki a terheléselosztó erőforrását.
2. Állítsa be a megfelelő összesítési típust.
3. Szükség esetén a szükséges szűrést és csoportosítást is konfigurálhatja.

    ![standard Load Balancer metrikái](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Ábra: az adatelérési út rendelkezésre állási metrikája standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Többdimenziós mérőszámok programozott módon történő beolvasása API-kon keresztül

A többdimenziós metrikák definícióinak és értékeinek beolvasására szolgáló API-útmutatásért lásd: [Azure Monitoring REST API útmutató](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api). Ezek a metrikák csak az "összes metrika" lehetőségen keresztül írhatók a Storage-fiókba. 

### <a name = "DiagnosticScenarios"></a>Gyakori diagnosztikai forgatókönyvek és javasolt nézetek

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>A Load Balancer VIP-re vonatkozó adatelérési út fel van töltve és elérhető?

A virtuális IP-cím rendelkezésre állási mérőszáma a régión belüli adatelérési út állapotát mutatja azon számítási gazdagépen, ahol a virtuális gépek találhatók. A metrika az Azure-infrastruktúra állapotának áttekintése. A metrika a következőre használható:
- A szolgáltatás külső rendelkezésre állásának monitorozása
- Mélyebbre ássa, hogy az a platform, amelyen a szolgáltatás telepítve van, kifogástalan állapotú-e, illetve hogy a vendég operációs rendszer vagy alkalmazás példánya kifogástalan állapotú-e.
- Elkülöníti, hogy egy esemény kapcsolódik-e a szolgáltatáshoz vagy az alapul szolgáló adatsíkon. Ne tévesszük össze ezt a metrikát az állapot mintavételi állapotával ("DIP rendelkezésre állása").

Az adatelérési út rendelkezésre állásának beolvasása a standard Load Balancer erőforrásaihoz:
1. Győződjön meg arról, hogy a helyes terheléselosztó erőforrás van kiválasztva. 
2. A **metrika** legördülő listában válassza ki az **adatelérési út rendelkezésre állása**elemet. 
3. Az **Összesítés** legördülő listában válassza az **AVG**elemet. 
4. Emellett vegyen fel egy szűrőt a előtérbeli IP-cím vagy a frontend-port számára a szükséges előtér-IP-címmel vagy előtér-porttal rendelkező dimenzióként, majd csoportosítsa őket a kiválasztott dimenzió alapján.

![VIP-szondázás](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ábra: Load Balancer frontend-szondázás részletei*

A metrikát egy aktív, sávon belüli mérés hozza létre. A régión belüli szondázás-szolgáltatás a méréshez kapcsolódó forgalomból származik. A szolgáltatás akkor aktiválódik, amikor nyilvános előtérrel hoz létre egy központi telepítést, és addig folytatódik, amíg el nem távolítja az előtért. 

A rendszer rendszeres időközönként létrehoz egy csomagot, amely megfelel az üzembe helyezéshez tartozó előtérnek és szabálynak. A rendszer áthelyezi a régiót a forrásból a gazdagépre, ahol a háttérbeli készletben lévő virtuális gép található. A terheléselosztó infrastruktúra ugyanazt a terheléselosztási és fordítási műveletet hajtja végre, mint az összes többi forgalom esetében. Ez a mintavétel sávon kívül esik a terheléselosztási végponton. Miután a mintavétel megérkezik a (z) rendszerre, ahol egy kifogástalan virtuális gép található a háttér-készletben, a számítási gazdagép választ küld a szondázás szolgáltatásnak. A virtuális gép nem látja ezt a forgalmat.

A virtuális IP-cím elérhetősége a következő okok miatt sikertelen:
- Az üzemelő példány nem rendelkezik kifogástalan állapotú virtuális gépekkel a háttér-készletben. 
- Infrastruktúra-kimaradás történt.

Diagnosztikai célból használhatja az [adatelérési út rendelkezésre állási metrikáját az állapot mintavételi állapotával együtt](#vipavailabilityandhealthprobes).

A legtöbb forgatókönyv esetében az **átlagot** használja az összesítéshez.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>A virtuális IP-címekről érkező háttér-példányok választanak a mintavételre?

Az állapotadatok állapotának mérőszáma az alkalmazás központi telepítésének állapotát mutatja be a terheléselosztó állapotának beállításakor. A terheléselosztó a Health mintavétel állapota alapján határozza meg, hogy hová szeretné elküldeni az új folyamatokat. Az állapotadatok egy Azure-beli infrastruktúra-címről származnak, és a virtuális gép vendég operációs rendszerében láthatók.

Az standard Load Balancer-erőforrások állapot-mintavételi állapotának lekérése:
1. Válassza ki a **Health mintavételi állapot** mérőszámát **AVG** összesítési típussal. 
2. Alkalmazzon szűrőt a szükséges előtér-IP-címen vagy-porton (vagy mindkettőn).

Az állapot-mintavétel a következő okok miatt meghiúsul:
- Az állapot-mintavétel olyan portra konfigurálható, amely nem figyel vagy nem válaszol, vagy helytelen protokollt használ. Ha a szolgáltatás közvetlen kiszolgáló-visszaküldési (DSR-vagy úszó IP-) szabályokat használ, győződjön meg arról, hogy a szolgáltatás figyeli a hálózati adapter IP-konfigurációjának IP-címét, és nem csak az előtér-IP-címmel konfigurált visszacsatolást.
- A mintavételt nem engedélyezi a hálózati biztonsági csoport, a virtuális gép vendég operációs rendszerének tűzfala vagy az alkalmazás rétegének szűrői.

A legtöbb forgatókönyv esetében az **átlagot** használja az összesítéshez.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hogyan a kimenő kapcsolatok statisztikáit? 

A SNAT-kapcsolatok metrika a [kimenő folyamatok](https://aka.ms/lboutbound)sikeres és sikertelen kapcsolatainak mennyiségét írja le.

A sikertelen, nullánál nagyobb kapcsolatok mennyisége azt jelzi, hogy SNAT a portok kimerülése. Meg kell vizsgálnia, hogy mi okozza ezeket a hibákat. A SNAT-port kimerülésének jegyzékfájlja sikertelen a [kimenő folyamat](https://aka.ms/lboutbound)létrehozásakor. Tekintse át a kimenő kapcsolatokról szóló cikket, hogy megértsék a munkafolyamatokat és a mechanizmusokat, valamint azt, hogy miként lehet enyhíteni és megtervezni a SNAT-portok kimerülésének elkerülését. 

A SNAT-kapcsolatok statisztikáinak beolvasása:
1. Válassza ki a **SNAT-kapcsolatok** metrika típusát és a **Sum** értéket összesítésként. 
2. Csoportosítási **állapot** a sikeres és sikertelen SNAT-kapcsolatok száma alapján, amelyeket különböző sorok képvisel. 

![SNAT-kapcsolatok](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Ábra: Load Balancer SNAT kapcsolatok száma*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hogyan a szolgáltatáshoz tartozó bejövő/kimenő csatlakozási kísérletek ellenőrzését?

A SYN-csomagok mérőszáma az adott előtérhöz társított TCP SYN-csomagok mennyiségét írja le, amelyek elérkeztek vagy elküldhetők ( [kimenő forgalom](https://aka.ms/lboutbound)esetén). Ez a metrika a szolgáltatáshoz való TCP-kapcsolódási kísérletek megismerésére használható.

A legtöbb forgatókönyv **esetében használja az összesítést** .

![SYN-kapcsolatok](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ábra: Load Balancer SYN-szám*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hogyan a hálózat sávszélesség-felhasználását? 

A bájtok és a csomagméret mérőszáma a szolgáltatás által az előtér-alapon küldött vagy fogadott bájtok és csomagok mennyiségét írja le.

A legtöbb forgatókönyv **esetében használja az összesítést** .

A bájt vagy a csomagok számának statisztikáinak beolvasása:
1. Válassza ki a **bájtok számát** és/vagy a **csomagok darabszámának** metrikai típusát, az **átlagot** pedig összesítésként. 
2. A következő lehetőségek közül választhat:
   * Szűrő alkalmazása egy adott előtér-IP-címen, előtér-porton, háttérbeli IP-címen vagy háttér-porton.
   * Teljes statisztikát kaphat a terheléselosztó erőforrásaihoz szűrés nélkül.

![Bájtok száma](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ábra: Load Balancer bájtok száma*

#### <a name = "vipavailabilityandhealthprobes"></a>Hogyan a terheléselosztó üzembe helyezésének diagnosztizálása?

A virtuális IP-címek rendelkezésre állása és az állapot mintavételi metrikájának egyetlen diagramon való használatával meghatározhatja, hogy hol keresse meg a problémát, és oldja meg a problémát. Biztos lehet abban, hogy az Azure megfelelően működik, és ezzel az ismerettel meggyőzően megállapítja, hogy a konfiguráció vagy az alkalmazás a kiváltó ok.

Az állapot mintavételi mérőszámai segítségével megtudhatja, hogyan tekintheti meg az Azure a telepítés állapotát a megadott konfiguráció alapján. Az állapot-ellenőrzéseket mindig remekül megtekintheti a figyeléssel vagy a probléma okának megállapításával.

További lépésként igénybe veheti a VIP-elérhetőségi metrikákat, és megtudhatja, hogyan tekintheti át az Azure az adott telepítésért felelős mögöttes adatközpont állapotát. Ha mindkét mérőszámot kombinálja, elkülönítheti a hiba helyét, ahogy az a következő példában látható:

![Az adatelérési út rendelkezésre állásának és az állapot-mintavételi állapot metrikáinak egyesítése](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Ábra: az adatelérési út rendelkezésre állásának és az állapot-mintavételi állapot metrikáinak kombinálása*

A diagram a következő információkat jeleníti meg:
- A virtuális gépeket üzemeltető infrastruktúra nem volt elérhető, és a diagram elején 0% volt. Később az infrastruktúra Kifogástalan állapotba került, és a virtuális gépek elérhetők voltak, és több virtuális gépet helyeztek el a háttérbe. Ezt az információt az adatelérési utak rendelkezésre állása (VIP rendelkezésre állása) kék nyomkövetése jelzi, amely később 100%-os volt. 
- Az állapot-mintavételi állapot (DIP rendelkezésre állása), amelyet a lila nyomkövetés jelez, a diagram elején 0%. A zöld körben bekarikázott terület kiemeli, hogy az állapotadatok állapota (DIP rendelkezésre állása) Kifogástalan állapotba került, és ekkor az ügyfél üzembe helyezése képes volt új folyamatok fogadására.

A diagram lehetővé teszi, hogy az ügyfelek az üzembe helyezést a saját maguk is tudják elhárítani anélkül, hogy meg kellene állapítaniuk vagy fel kellene kérniük a más problémák előfordulását. A szolgáltatás nem volt elérhető, mert az állapot-mintavétel hibás konfiguráció vagy hibás alkalmazás miatt sikertelen volt.

## <a name = "ResourceHealth"></a>Erőforrás állapotának állapota

A standard Load Balancer erőforrások állapotának állapota a meglévő **erőforrás** -állapoton keresztül érhető el a **monitor > Service Health**.

A nyilvános standard Load Balancer erőforrások állapotának megtekintése:
1. Válassza a **figyelő** > **Service Health**lehetőséget.

   ![Figyelő oldal](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ábra: a Service Health hivatkozás a Azure Monitor*

2. Válassza a **Resource Health**lehetőséget, majd győződjön meg arról, hogy az **előfizetés azonosítója** és az **erőforrástípus = Load Balancer** van kiválasztva.

   ![Erőforrás állapotának állapota](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Ábra: válasszon erőforrást az állapot nézethez*

3. A listából válassza ki a Load Balancer erőforrást a korábbi állapotának megtekintéséhez.

    ![Load Balancer állapot](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Ábra: Load Balancer erőforrás-állapot nézet*
 
A különböző erőforrás-állapotokat és azok leírását az alábbi táblázat tartalmazza: 

| Erőforrás állapotának állapota | Leírás |
| --- | --- |
| Elérhető | A standard Load Balancer erőforrása kifogástalan és elérhető. |
| Nem érhető el | A standard Load Balancer erőforrás nem kifogástalan állapotú. A **Azure Monitor** > **metrikák**kiválasztásával diagnosztizálhatja az állapotot.<br>(A nem*elérhető* állapot azt is jelenti, hogy az erőforrás nem kapcsolódik a standard Load Balancerhez.) |
| Ismeretlen | A standard Load Balancer erőforrás erőforrás-állapotának állapota még nem frissült.<br>(Az*ismeretlen* állapot azt is jelentheti, hogy az erőforrás nem kapcsolódik a standard Load Balancerhez.)  |

## <a name="next-steps"></a>Következő lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
- További információ a [terheléselosztó kimenő kapcsolatáról](https://aka.ms/lboutbound).
- A [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)megismerése.
- Ismerje meg a [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) és a [metrikák beolvasását REST API használatával](/rest/api/monitor/metrics/list).
