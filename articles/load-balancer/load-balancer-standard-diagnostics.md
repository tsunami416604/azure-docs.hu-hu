---
title: Diagnosztika metrikákkal, riasztásokkal és erőforrás-állapottal – Azure standard terheléselosztó
description: Használja a rendelkezésre álló metrikák, riasztások és erőforrás-egészségügyi információk diagnosztizálására az Azure Standard Load Balancer.
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
ms.openlocfilehash: 861961bb66adc7ed9509eab973516a964cb67492
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521072"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Metrikák, riasztások és az erőforrások állapotának diagnosztikái a standard Load Balancerben

Az Azure Standard Load Balancer a következő diagnosztikai képességeket teszi elérhetővé:

* **Többdimenziós metrikák és riasztások: Többdimenziós**diagnosztikai képességeket biztosít az [Azure Monitoron](https://docs.microsoft.com/azure/azure-monitor/overview) keresztül a szabványos terheléselosztó konfigurációkhoz. A szabványos terheléselosztó-erőforrások figyelheti, kezelheti és elháríthatja az erőforrásokat.

* **Erőforrás állapota:** A terheléselosztó lap az Azure Portalon és az Erőforrás-állapot lap (a Figyelő) elérhetővé teszi az erőforrás-állapot szakasz standard terheléselosztó. 

Ez a cikk egy gyors bemutató tismerteti meg ezeket a képességeket, és lehetőséget kínál a standard terheléselosztó használatához. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>Többdimenziós metrikák

Az Azure Load Balancer többdimenziós metrikákat biztosít az Azure-metrikákon keresztül az Azure Portalon, és segít valós idejű diagnosztikai betekintést kapni a terheléselosztó erőforrásait. 

A standard terheléselosztó különböző konfigurációi a következő mutatókat biztosítják:

| Metrika | Erőforrás típusa | Leírás | Ajánlott összesítés |
| --- | --- | --- | --- |
| Az adatok elérési útja (VIP elérhetőség)| Nyilvános és belső terheléselosztó | Standard load Balancer folyamatosan gyakorolja az adatelérési utat egy régión belül a terheléselosztó előtér, egészen az SDN verem, amely támogatja a virtuális gép. Mindaddig, amíg a kifogástalan állapotú példányok maradnak, a mérés az alkalmazás terheléselosztásos forgalmat követi. Az ügyfelek által használt adatelérési út is érvényesítve van. A mérés nem látható az alkalmazás számára, és nem zavarja a többi műveletet.| Átlag |
| Állapotminta állapota(DIP elérhetősége) | Nyilvános és belső terheléselosztó | A standard terheléselosztó egy elosztott állapot-ellenőrző szolgáltatást használ, amely a konfigurációs beállításoknak megfelelően figyeli az alkalmazásvégpont állapotát. Ez a metrika egy összesített vagy végpontonkénti szűrt nézetet biztosít a terheléselosztó készlet minden példányvégpontjából. Láthatja, hogy a terheléselosztó hogyan tekinti meg az alkalmazás állapotát, amint azt az állapotminta konfigurációja jelzi. |  Átlag |
| SYN (szinkronizálási) csomagok | Nyilvános és belső terheléselosztó | A standard terheléselosztó nem szünteti meg a TCP-kapcsolatokat, és nem lép kapcsolatba a TCP- vagy UDP-csomagáramlásokkal. A folyamatok és a kézfogások mindig a forrás és a virtuálisgép-példány között. A TCP protokollforgatókönyvek jobb hibaelhárítása érdekében syn csomagszámlálókhasználatával megtudhatja, hogy hány TCP-csatlakozási kísérlet történt. A metrika a fogadott TCP SYN csomagok számát jelenti.| Átlag |
| SNAT-kapcsolatok | Nyilvános terheléselosztó |A standard terheléselosztó a nyilvános IP-cím előtér-végződésű kimenő folyamatainak számát jelenti. A forráshálózati címfordítás (SNAT) portjai kimeríthető erőforrások. Ez a metrika jelezheti, hogy az alkalmazás mennyire támaszkodik az SNAT-ra a kimenő származtatott folyamatok esetében. A sikeres és sikertelen kimenő SNAT-folyamatok számlálói a jelentések szerint vannak jelentve, és a kimenő folyamatok állapotának elhárítására és megértésére használhatók.| Átlag |
| Lefoglalt SNAT-portok | Nyilvános terheléselosztó | A standard terheléselosztó a háttérpéldányonként lefoglalt SNAT-portok számát jelenti | Átlagos. |
| Használt SNAT-portok | Nyilvános terheléselosztó | A standard terheléselosztó a háttérpéldányonként használt SNAT-portok számát jelenti. | Átlag | 
| Bájtszámlálók |  Nyilvános és belső terheléselosztó | A Standard Load Balancer az előtér-elvégzett adatokat jelenti. Észreveheti, hogy a bájtok nem egyenlően oszlanak el a háttérpéldányok között. Ez várható, mivel az Azure terheléselosztó algoritmusa a folyamatokon alapul | Átlag |
| Csomagszámlálók |  Nyilvános és belső terheléselosztó | A Standard Load Balancer az előtér-elvégzett csomagokat jelenti.| Átlag |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>A terheléselosztó-mérőszámok megtekintése az Azure Portalon

Az Azure Portalon a terheléselosztó metrikák a Metrikák oldalon keresztül, amely elérhető mind a terheléselosztó erőforrás-oldal egy adott erőforrás és az Azure Monitor oldalon elérhető. 

A standard terheléselosztó-erőforrások metrikáinak megtekintéséhez:
1. Nyissa meg a Metrikák lapot, és tegye az alábbiak egyikét:
   * A terheléselosztó erőforráslapján válassza ki a metrika típusát a legördülő listában.
   * Az Azure Monitor lapon válassza ki a terheléselosztó erőforrást.
2. Állítsa be a megfelelő metrikaösszesítési típust.
3. Szükség esetén konfigurálja a szükséges szűrést és csoportosítást.
4. Szükség esetén konfigurálja az időtartományt és az összesítést. Alapértelmezés szerint az idő utc-ben jelenik meg.

  >[!NOTE] 
  >Az időösszesítés fontos bizonyos metrikák értelmezésekor, mivel az adatok percenként egyszer mintavételezésre kerül. Ha az időösszesítés öt percre van állítva, és a metrikaösszesítés idomáraösszegzés összegzése összeg áll használatos olyan metrikákhoz, mint például az SNAT-allokáció, a grafikon a lefoglalt SNAT-portok ötszörösét jeleníti meg. 

![Metrikák standard terheléselosztóhoz](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*Ábra: Adatelérési út rendelkezésre állási mutatója a standard terheléselosztóhoz*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Többdimenziós metrikák beolvasása programozott módon API-kon keresztül

A többdimenziós metrikadefiníciók és értékek lekéréséhez az API-útmutatót az [Azure Monitoring REST API-forgatókönyvben talál.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api) Ezek a metrikák csak a "Minden metrika" lehetőségen keresztül írhatók egy tárfiókba. 

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>Gyakori diagnosztikai forgatókönyvek és ajánlott nézetek

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>Riasztások konfigurálása többdimenziós metrikákhoz ###

Az Azure Standard Load Balancer támogatja a többdimenziós metrikák könnyen konfigurálható riasztásokat. Konfigurálja az egyéni küszöbértékeket az adott metrikákhoz, hogy különböző súlyossági szintű riasztásokat indítson el, hogy képessé tegye az érintés nélküli erőforrás-figyelési élményt.

A riasztások konfigurálása:
1. Lépjen a terheléselosztó riasztási alpaneljéhez
1. Új riasztási szabály létrehozása
    1.  Riasztási feltétel konfigurálása
    1.  (Nem kötelező) Műveletcsoport hozzáadása az automatikus javításhoz
    1.  Riasztás súlyosságának, nevének és leírásának hozzárendelése, amely intuitív reakciót tesz lehetővé


  >[!NOTE]
  >A riasztási feltétel konfigurációs ablaka a jelelőzmények idősorozatait jeleníti meg. Van egy lehetőség, hogy ezt az idősort dimenziók szerint szűrje, például a háttér-IP. Ez szűri az idősorozat-grafikont, de magát a riasztást **nem.** Adott háttér-IP-címekhez nem állíthat be riasztásokat.
  
#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Az adatelérési út felfelé és elérhető a terheléselosztó VIP-em számára?

A VIP rendelkezésre állási metrika ismerteti az adatelérési út állapotát a régión belül a számítási gazdagép, ahol a virtuális gépek találhatók. A metrika tükrözi az Azure-infrastruktúra állapotát. A mérőszám segítségével:
- A szolgáltatás külső elérhetőségének figyelése
- Mélyebbre ásson, és ismerje meg, hogy a platform, amelyen a szolgáltatás telepítve van kifogástalan, vagy hogy a vendég operációs rendszer vagy az alkalmazáspéldány kifogástalan állapotú.Dig deeper and understand whether the platform, amelyen a szolgáltatás telepítve van kifogástalan, vagy hogy a vendég operációs rendszer vagy az alkalmazáspéldány kifogástalan.
- Elkülöníteni, hogy egy esemény kapcsolódik-e a szolgáltatáshoz vagy az alapul szolgáló adatsíkhoz. Ne tévessze össze ezt a metrikát az állapotminta állapotával ("DIP elérhetőség").

A szabványos terheléselosztó-erőforrások adatelérési útvonalának lekérni:
1. Győződjön meg arról, hogy a megfelelő terheléselosztó erőforrás van kiválasztva. 
2. A **Metrika** legördülő listában válassza az **Adatelérési út elérhetősége**lehetőséget. 
3. Az **Összesítés** legördülő listában válassza az **Átlagos**lehetőséget. 
4. Emellett adjon hozzá egy szűrőt az előtér-IP-címhez vagy az előtér-porthoz dimenzióként a szükséges előtér-IP-címmel vagy előtér-porttal, majd csoportosítsa őket a kiválasztott dimenzió szerint.

![VIP szondázás](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ábra: Terheléselosztó előtér-vizsgálat részletei*

A metrikát egy aktív, sávon kénti mérés hozza létre. A régión belüli szondázási szolgáltatás a mérés forgalmából származik. A szolgáltatás akkor aktiválódik, amint létrehoz egy nyilvános előtér-telepítést, és addig folytatódik, amíg el nem távolítja az előtér. 

A központi telepítés előtérének és szabályának megfelelő csomag rendszeres időközönként jön létre. Áthalad a régióa forrástól a gazdagép, ahol a virtuális gép a háttér-készletben található. A terheléselosztó infrastruktúra ugyanazokat a terheléselosztási és fordítási műveleteket hajtja végre, mint az összes többi forgalom esetében. Ez a minta sávon van a terheléselosztási végponton. Miután a mintavétel megérkezik a számítási gazdagép, ahol egy kifogástalan állapotú virtuális gép a háttér-készletben található, a számítási gazdagép választ hoz létre a szondázási szolgáltatás. A virtuális gép nem látja ezt a forgalmat.

A VIP elérhetősége a következő okok miatt sikertelen:
- A központi telepítés nem rendelkezik a háttér-készletben maradt kifogástalan állapotú virtuális gépek. 
- Infrastruktúra-kimaradás történt.

Diagnosztikai célokra használhatja az [adatelérési út rendelkezésre állási metrikáját az állapotminta állapotával együtt.](#vipavailabilityandhealthprobes)

A legtöbb eset összesítéseként használja az **Átlag** függvényt.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>A VIP-példányaim háttérpéldányai válaszolnak a szondákra?

Az állapotminta állapotmetrika ismerteti az alkalmazás üzembe helyezésének állapotát az Ön által konfigurált, amikor konfigurálja a terheléselosztó állapotminta. A terheléselosztó az állapotminta állapotát használja az új folyamatok küldésének helyének meghatározásához. Az állapotminta egy Azure-infrastruktúra-címről származik, és a virtuális gép vendég operációs rendszerében látható.

A standard terheléselosztó-erőforrások állapotának lekérni:
1. Válassza ki az **Állapotminta állapota** metrikát **avg** aggregációs típussal. 
2. Alkalmazzon szűrőt a szükséges előtér-IP-címre vagy portra (vagy mindkettőre).

Az állapotminta a következő okok miatt sikertelen:
- Az állapotminta olyan portra konfigurálható, amely nem figyel, vagy nem válaszol, vagy nem a megfelelő protokollt használja. Ha a szolgáltatás közvetlen kiszolgáló-visszatérési (DSR) vagy lebegő IP-szabályokat használ, győződjön meg arról, hogy a szolgáltatás figyeli a hálózati adapter IP-konfigurációjának IP-címét, és nem csak az előtér-IP-címmel konfigurált visszacsatolást.
- A mintavételt a hálózati biztonsági csoport, a virtuális gép vendég operációsrendszer-tűzfala vagy az alkalmazásréteg-szűrők nem engedélyezik.

A legtöbb eset összesítéseként használja az **Átlag** függvényt.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hogyan ellenőrizhetem a kimenő kapcsolat statisztikáit? 

Az SNAT-kapcsolatok metrikája a kimenő [folyamatok](https://aka.ms/lboutbound)sikeres és sikertelen kapcsolatainak mennyiségét írja le.

A nullánál nagyobb sikertelen kapcsolatok térfogata az SNAT-port kimerülését jelzi. További vizsgálatot kell vizsgálnia annak megállapítására, hogy mi okozhatja ezeket a hibákat. Az SNAT-port kimerülése a [kimenő áramlás](https://aka.ms/lboutbound)létrehozásának elmulasztásaként nyilvánul meg. Tekintse át a kimenő kapcsolatokról szóló cikket a munkahelyi forgatókönyvek és mechanizmusok megismeréséhez, valamint az SNAT-portok kimerülésének elkerüléséhez. 

SNAT-kapcsolati statisztikák beolvasása:
1. Válassza ki **az SNAT-kapcsolatok** metrika típusát és **az Összegzés** összesítést. 
2. Csoportosítás **csatlakozási állapot szerint** a különböző vonalak által képviselt sikeres és sikertelen SNAT-kapcsolatok száma érdekében. 

![SNAT-kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Ábra: Terheléselosztó SNAT-kapcsolatszáma*


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>Hogyan ellenőrizhetem az SNAT-port használatát és kiosztását?

Az SNAT-használati metrika azt jelzi, hogy hány egyedi folyamatok között létrehozott egy internetes forrás és a háttérvirtuális gép vagy virtuális gép méretezési csoport, amely mögött a terheléselosztó, és nem rendelkezik nyilvános IP-címmel. Összehasonlításával ezt az SNAT-foglalási metrika, meghatározhatja, ha a szolgáltatás tapasztalható, vagy fennáll a veszélye az SNAT-kimerültség és az ebből eredő kimenő folyamathiba. 

Ha a metrikák jelzik a [kimenő folyamathiba](https://aka.ms/lboutbound) kockázatát, hivatkozzon a cikkre, és tegyen lépéseket a szolgáltatás állapotának csökkentése érdekében.

Az SNAT-port használatának és kiosztásának megtekintése:
1. Állítsa a diagram időösszesítését 1 percre, hogy biztosan megjelenjenek a kívánt adatok.
1. Válassza az **SNAT-használat** és/vagy **SNAT-hozzárendelés lehetőséget** metrikus típusként, **és az Átlag** összesítést.
    * Alapértelmezés szerint ez az egyes háttér-virtuális gépekhez vagy vMSS-ekhez lefoglalt vagy általa használt SNAT-portok átlagos száma, amely megfelel a terheléselosztóhoz rendelt összes előtér-nyilvános IP-címnek, tcp- és UDP-n keresztül összesítve.
    * A terheléselosztó által használt vagy a terheléselosztószámára lefoglalt összes SNAT-port okának megtekintése metmetrikaösszesítési **összeg**
1. Szűrés egy adott **protokolltípusra**, a **háttérrendszer IP-k**készletére és/vagy **az előtér-IP-kre**.
1. A háttérprogramra vagy előtér-példányra jutó állapot figyeléséhez alkalmazza a felosztást. 
    * Megjegyzés felosztása csak lehetővé teszi, hogy egy metrika jelenik meg egy időben. 
1. Például a gépenkénti TCP-folyamatok SNAT-használatának figyeléséhez, **átlag**szerinti összesítéshez , **háttér-IP-címekkel** felosztva és **protokolltípus**szerinti szűréshez. 

![SNAT-allokáció és -használat](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*Ábra: Átlagos TCP SNAT-portfoglalás és-használat háttérrendszer-virtuális gépek hez*

![SNAT-használat háttérpéldány szerint](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*Ábra: TCP SNAT-port használat háttérpéldányonként*

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hogyan ellenőrizhetem a szolgáltatásom bejövő/kimenő csatlakozási kísérleteit?

A SYN csomagok metrikája az adott előtérhöz társított TCP SYN-csomagok mennyiségét írja le, amelyek egy adott előtérhöz társított vagy elküldött [(kimenő folyamatok](https://aka.ms/lboutbound)esetében) érkeztek vagy elküldték őket. Ezzel a metrikával megismerheti a TCP-kapcsolat imát a szolgáltatáshoz.

A legtöbb esetben az **Összesítés** t használja összesítésként.

![SYN kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ábra: Terheléselosztó SYN száma*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hogyan ellenőrizhetem a hálózati sávszélesség-felhasználást? 

A bájtok és a csomagszámlálók metrikája a szolgáltatás által előtér-alapon küldött vagy fogadott bájtok és csomagok mennyiségét írja le.

A legtöbb esetben az **Összesítés** t használja összesítésként.

Bájtok vagy csomagok számának statisztikáinak beírása:
1. Válassza ki a **Bájtok száma** és/vagy **a Csomagszám** metrika típust, amelynek összesítése **az Átlag.** 
2. A következő lehetőségek közül választhat:
   * Szűrő alkalmazása egy adott előtér-IP-re, előtér-portra, háttér-IP-re vagy háttérportra.
   * A terheléselosztó-erőforrás általános statisztikái szűrés nélkül szerezhető be.

![Bájtszáma](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ábra: Terheléselosztó bájtszáma*

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>Hogyan diagnosztizálhatom a terheléselosztó üzembe helyezését?

A VIP rendelkezésre állási és állapotminta metrikák kombinációjával egyetlen diagramon azonosíthatja, hogy hol keresse meg a problémát, és oldja meg a problémát. Bizonyosságot szerezhet arról, hogy az Azure megfelelően működik, és ezt a tudást felhasználhatja annak meggyőző meghatározásához, hogy a konfiguráció vagy az alkalmazás a kiváltó ok.

Az állapotminta-metrikák segítségével megismerheti, hogy az Azure hogyan tekinti meg a központi telepítés állapotát a megadott konfigurációnak megfelelően. Az egészségügyi szondák vizsgálata mindig nagyszerű első lépés a figyelésvagy az ok meghatározásában.

Egy lépéssel továbbléphet, és a VIP rendelkezésre állási metrikáival betekintést nyerhet abba, hogy az Azure hogyan tekinti meg az adott üzembe helyezésért felelős mögöttes adatsík állapotát. Ha mindkét mutatót kombinálja, elkülönítheti, hogy hol lehet a hiba, amint azt a példa is szemlélteti:

![Az adatelérési út rendelkezésre állásának és állapotvizsgálatának állapotmutatóinak egyesítése](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Ábra: Az adatelérési út rendelkezésre állásának és állapotvizsgálatának állapotmutatóinak egyesítése*

A diagram a következő információkat jeleníti meg:
- A virtuális gépeket üzemeltető infrastruktúra nem érhető el, és a diagram elején 0 százalék volt. Később az infrastruktúra kifogástalan állapotú volt, és a virtuális gépek elérhetők voltak, és egynél több virtuális gép került a háttérrendszerbe. Ezt az információt az adatelérési út rendelkezésre állásának (VIP elérhetősége) kék nyomkövetése jelzi, amely később 100 százalékvolt. 
- Az állapotminta állapota (DIP elérhetősége), amelyet a lila nyomkövetés jelez, 0 százalékon áll a diagram elején. A bekarikázott terület zöld kiemeli, ahol az állapotminta állapota (DIP rendelkezésre állás) kifogástalan lett, és ekkor az ügyfél üzembe helyezését tudta elfogadni az új folyamatok.

A diagram lehetővé teszi az ügyfelek számára, hogy saját maguk hárítsák el a központi telepítést anélkül, hogy ki kellene találniuk vagy támogatást kellene kérniük, hogy más problémák is előfordulnak-e. A szolgáltatás nem érhető el, mert az állapotminta konkvátúk hibás konfiguráció vagy sikertelen alkalmazás miatt sikertelenek voltak.

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Erőforrás állapota

A standard terheléselosztó erőforrások állapotának állapota a meglévő **erőforrás-állapoton** keresztül érhető el **> szolgáltatásállapotának figyelője**.

A nyilvános standard terheléselosztó erőforrások állapotának megtekintése:
1. Válassza a**Szolgáltatás állapotának** **figyelése** > lehetőséget.

   ![Figyelő lap](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ábra: A Szolgáltatásállapot hivatkozás az Azure Monitoron*

2. Válassza **az Erőforrás állapota**lehetőséget, majd győződjön meg arról, hogy az **előfizetés-azonosító** és **az erőforrástípus = terheléselosztó** ki van jelölve.

   ![Erőforrás állapota](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Ábra: Erőforrás kiválasztása az állapotnézethez*

3. A listában válassza ki a Terheléselosztó erőforrást a korábbi állapotának megtekintéséhez.

    ![Terheléselosztó állapota](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Ábra: Terheléselosztó erőforrás állapotnézete*
 
A különböző erőforrás-állapotok és azok leírásai az alábbi táblázatban találhatók: 

| Erőforrás állapota | Leírás |
| --- | --- |
| Elérhető | A szabványos terheléselosztó erőforrás kifogástalan állapotú és elérhető. |
| Nem érhető el | A szabványos terheléselosztó erőforrás nem kifogástalan. Az azure > **monitormetrikák**kiválasztásával diagnosztizálhatja az állapotot. **Azure Monitor**<br>(*A nem érhető el* állapot azt is jelentheti, hogy az erőforrás nem kapcsolódik a szabványos terheléselosztóhoz.) |
| Ismeretlen | A szabványos terheléselosztó erőforrás erőforrás-állapota még nem lett frissítve.<br>(*Az ismeretlen* állapot azt is jelentheti, hogy az erőforrás nincs csatlakoztatva a szabványos terheléselosztóhoz.)  |

## <a name="next-steps"></a>További lépések

- További információ a [Standard Load Balancer-](load-balancer-standard-overview.md)ről.
- További információ a [terheléselosztó kimenő kapcsolatáról.](https://aka.ms/lboutbound)
- További információ az [Azure Monitorról.](https://docs.microsoft.com/azure/azure-monitor/overview)
- Ismerje meg az [Azure Monitor REST API-t,](https://docs.microsoft.com/rest/api/monitor/) és [hogyan kérheti le a metrikákat a REST API-n keresztül.](/rest/api/monitor/metrics/list)
