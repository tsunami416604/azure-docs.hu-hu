---
title: Az Azure Standard Load Balancer diagnosztikai |} A Microsoft Docs
description: Diagnosztika az Azure Standard Load Balancer a rendelkezésre álló metrikák és egészségügyi adatok használata.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/9/2018
ms.author: Kumud
ms.openlocfilehash: 0711b52b76a22e32d05f27e6aae6c981bd2c148a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902603"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>A Standard Load Balancer metrikák és egészségügyi diagnosztikája

Az Azure Standard Load Balancer az Azure Standard Load Balancer lehetővé teszi az erőforrások az alábbi diagnosztikai képességeket tesz elérhetővé:
* **Többdimenziós metrikák**: olyan új többdimenziós diagnosztikai funkciók révén [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) nyilvános, mind a belső terheléselosztó konfigurációjában betölteni. Figyelése, kezelése és a load balancer-erőforrások hibaelhárítása.

* **A Resource health**: A terheléselosztó oldal az Azure Portalon és a Resource Health lap (figyelő) teszi közzé a Resource Health szakaszban, a Standard Load Balancer nyilvános load balancer konfigurációját.

Ez a cikk végigvesszük az ezeket a képességeket nyújt, és azt használni őket a Standard Load Balancer lehetőséget kínál.

## <a name = "MultiDimensionalMetrics"></a>Többdimenziós metrikák

Az Azure Load Balancer biztosít új többdimenziós metrikák az új Azure metrikák (előzetes verzió) az Azure Portalon, és segít a valós idejű diagnosztikai elemzési adatokat nyerhet a load balancer-erőforrások. 

A Standard Load Balancer különböző konfigurációkban adja meg a következő metrikákat:

| Metrika | Erőforrás típusa | Leírás | Ajánlott összesítés |
| --- | --- | --- | --- |
| Virtuális IP-cím rendelkezésre állási (adatok elérési út elérhető) | Nyilvános load balancer | Standard Load Balancer folyamatosan gyakorol az adatok útvonalat egy adott régión belül a terheléselosztó előtérrendszerhez, egészen az SDN-verem, amely támogatja a virtuális Géphez való. Kifogástalan állapotú példányok továbbra is, a mérés követi az alkalmazás elosztott terhelésű forgalmat az adott elérési úton. Az adatok elérési útja, az ügyfelek által használt is ellenőrzi. A mérték az alkalmazás számára, és nem ütközik más műveleteket.| Átlag |
| Dedikált IP-CÍMMEL, rendelkezésre állás (mintavételi állapot) |  Nyilvános és a belső terheléselosztó | A standard Load Balancer állapot-ellenőrzés elvégzése egy elosztott szolgáltatás, amely figyeli az alkalmazás végponti állapotát a konfigurációs beállításoknak megfelelően használja. Ez a metrika megadja egy összesítés vagy a végpont szűrt nézete a load balancer készletben minden példány végpont. Láthatja hogyan a Load Balancer megtekinti az alkalmazás állapotát a állapot-mintavételi konfigurációt jelzett módon. |  Átlag |
| Szinkronizálás a Mi (szinkronizálás) csomagok |  Nyilvános load balancer | A standard Load Balancer nem megszakítja az Transmission Control Protocol (TCP), és együttműködik a TCP vagy UDP-csomag folyamatok. Folyamatok és azok kézfogások mindig a forrás- és a Virtuálisgép-példány között vannak. A TCP protokoll forgatókönyvek hatékonyabb elhárításához végezhet SZIN felhasználása csomagok számlálók tudni, hogy hány TCP-kapcsolat kísérletet tesz. A metrika a fogadott TCP SZIN csomagok számát jelenti.| Átlag |
| SNAT-kapcsolatok |  Nyilvános Load Balancer |A standard Load Balancer a kimenő forgalom, amely a nyilvános IP-cím előtérrendszerhez vannak masqueraded számát jelenti. Forrás hálózati cím címfordítás (SNAT) portjait egy kimeríthető erőforrást. Ez a metrika biztosíthat az arra utalhat, hogy hogyan érdemes az alkalmazás van szüksége az SNAT a kimenő folyamatokhoz. Sikeres és sikertelen kimenő SNAT folyamatok számlálói készül jelentés, és használható ismertetünk az elhárításukkal és a kimenő forgalom állapotának ismertetése.| Átlag |
| Bájt számlálók |  Nyilvános és a belső terheléselosztó | A standard Load Balancer az adatokat az előtérbeli feldolgozott jelentések.| Átlag |
| Csomag-számlálók |  Nyilvános és a belső terheléselosztó | A standard Load Balancer a csomagok előtér feldolgozott jelentések.| Átlag |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>A load balancer-metrikák megtekintése az Azure Portalon

Az Azure Portalon a load balancer metrikái a metrikák (előnézet) lapon érhető el mindkét load balancer erőforrás-oldalon egy adott erőforráshoz, és az Azure Monitor oldal-n keresztül tesz elérhetővé. 

A metrikák, a Standard Load Balancer-erőforrások megtekintése:
1. A metrikák (előnézet) lapon, és a következő lehetőségek közül választhat:
   * A load balancer erőforrás lapon válassza ki a metrika típusát a legördülő listában.
   * Az Azure Monitor oldalon válassza ki a terheléselosztó-erőforráshoz.
2. Állítsa be a megfelelő összesítési típusát.
3. Szükség esetén konfigurálja a szükséges szűrési és a csoportosítási.

![A Standard Load Balancer metrikák megtekintése](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*. Ábra: DIP rendelkezésre állás és egészségügyi mintavételi állapot metrikát a Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API-k használatával programozott módon többdimenziós metrikák beolvasása

API-t többdimenziós metrikadefinícióinak és az értékek lekéréséhez útmutatóért lásd: [Azure Monitoring REST API-t bemutató](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Általános diagnosztikai forgatókönyvek és ajánlott nézetek

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Az adatok elérési útja, és elérhetők az a terheléselosztó virtuális IP-cím?

A virtuális IP-cím rendelkezésre állási metrika azt ismerteti, hogy a régióban a számítási gazdagépre, ahol a virtuális gépek találhatók az adatok elérési út állapotát. A metrika az egy másolatát az Azure-infrastruktúra állapotát. A metrika az használhatja:
- A szolgáltatás külső rendelkezésre állásának figyelése
- További ismeretek, és megismerheti a kifogástalan állapotban-e a platformot, amelyre a szolgáltatás telepítve van-e a vendég operációs rendszer vagy alkalmazás példánya kifogástalan állapotú.
- Különítse el egy eseményt a szolgáltatás vagy az alapul szolgáló adatsík kapcsolódik-e. Ez a metrika az állapot-mintavételi ellenőrzése ("DIP availability") a ne keverje össze.

A virtuális IP-cím rendelkezésre állását a Standard Load Balancer-erőforrások lekéréséhez:
1. Győződjön meg arról, hogy a megfelelő terheléselosztó-erőforráshoz van kiválasztva. 
2. Az a **metrika** legördülő listában válassza **virtuális IP-cím rendelkezésre állási**. 
3. Az a **összesítési** legördülő listában válassza **átlagos**. 
4. Emellett adja hozzá egy szűrőt a virtuális IP-címet, vagy mint a dimenzió szükséges előtér-IP-címmel rendelkező virtuális IP-port vagy előtérbeli portot, és majd a kijelölt dimenziók szerinti csoportosítás.

![VIP-tesztelés](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ábra: Terheléselosztói VIP-tesztelés részletei*

A metrika egy aktív, a sávon kívüli mérési hozza létre. Egy ellenőrzési szolgáltatás a régión belül a mérték a forgalom származik. A szolgáltatás aktiválva van, amint egy nyilvános előtéri egy központi telepítés létrehozásához, és hogy továbbra is fennáll, amíg el nem távolítja az előtér. 

>[!NOTE]
>Belső előtérrendszerek jelenleg nem támogatottak. 

A csomag megfelelő az üzemelő példány az előtérbeli és a szabály rendszeresen jön létre. Bejárja a régió a forrásból a gazdagépre ahol egy virtuális Gépet, a háttér-készletben megtalálható. A load balancer infrastruktúra azonos terhelés terheléselosztás és a fordítás műveleteket hajtja végre, mint az összes többi forgalom. Ez a Hálózatfigyelő a sávon kívüli az elosztott terhelésű végpont. A mintavétel érkezik a számítási gazdagépen, ahol a háttérkészlet megfelelő állapotú virtuális gép megtalálható, miután a számítási gazdagép állít elő, az ellenőrzési szolgáltatásnak választ. A virtuális gép nem látja ezt a forgalmat.

Virtuális IP-cím rendelkezésre állási meghiúsul a következő okok miatt:
- Az üzemelő példány nem megfelelő állapotú virtuális gépek a háttér-készlet fennmaradó rendelkezik. 
- Infrastruktúra-kimaradás történt.

Diagnosztikai célokra is használhatja a [az állapot-mintavételi együtt virtuális IP-cím rendelkezésre állási metrika](#vipavailabilityandhealthprobes).

Használat **átlagos** , az összesítés a legtöbb forgatókönyvhöz.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>A saját virtuális IP-cím a háttérkiszolgáló-példányokhoz való válaszadást sem válaszol?

Az egészségügyi mintavételi állapot metrika az alkalmazás központi telepítésének állapotát írja le, Ön által konfigurált, a terheléselosztó az állapotminta konfigurálásakor. A load balancer az állapotminta állapota alapján határozza meg, hova küldhetők új folyamatok. Állapot-mintavételei származnak egy Azure-infrastruktúra-címről, és láthatók a virtuális gép vendég operációs rendszeren belül.

A Standard Load Balancer-erőforrások rendelkezésre állásának DIP lekéréséhez:
1. Válassza ki a **DIP rendelkezésre állási** a metrika **átlagos** aggregáció típusa. 
2. A szükséges virtuális IP-címére vagy a port (vagy mindkettő) szűrőt alkalmazza.

![Dedikált IP-CÍMMEL rendelkezésre állása](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*. Ábra: Terheléselosztói VIP-elérhetősége*

Állapot-mintavételei sikertelen a következő okok miatt:
- Konfigurál egy állapotminta-porthoz, amely nem figyel, vagy nem válaszol, vagy a nem megfelelő protokollt használja. Ha a szolgáltatás közvetlen kiszolgálói válasz (DSR vagy a nem fix IP) használja a szabályok, győződjön meg arról, hogy a szolgáltatás figyel-e a hálózati adapter IP-konfigurációja IP-címét, és nem csak a visszacsatolási, amelynek része az előtérbeli IP-cím.
- A mintavétel nem engedélyezett a hálózati biztonsági csoport, a virtuális gép vendég operációs rendszer tűzfala vagy az alkalmazás réteg szűrők alapján.

Használat **átlagos** , az összesítés a legtöbb forgatókönyvhöz.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hogyan tudom ellenőrizni, a kimenő kapcsolatainak statisztikája? 

Az SNAT-kapcsolatok metrikát a kötetet a sikeres és sikertelen kapcsolatokat ismerteti [kimenő forgalom](https://aka.ms/lboutbound).

A nullánál nagyobb sikertelen kapcsolatok mennyiségű SNAT portfogyás jelzi. Meg kell kiterjedésének meghatározása céljából mi okozza-e ezek a hibák. SNAT portfogyás létesíteni hibaként jegyzékfájlok egy [kimenő folyam](https://aka.ms/lboutbound). Tekintse át a kimenő kapcsolatokat megtudhatja, hogy a forgatókönyvek és mechanizmusok az irodából, és ismerje meg, hogyan lehet mérséklésére és SNAT portfogyás elkerülését segítő kialakítás ismertető cikket. 

Az SNAT kapcsolat statisztikájának beolvasása:
1. Válassza ki **SNAT-kapcsolatok** metrika típusa és **Sum** , összesítést. 
2. Csoportosítás **kapcsolódási állapot** a sikeres és sikertelen SNAT kapcsolat számát, amelyek különböző sorokba képviseli. 

![SNAT kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Ábra: Load Balancer SNAT-kapcsolatok száma*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hogyan tudom ellenőrizni, bejövő/kimenő kapcsolódási kísérletek a szolgáltatásomhoz?

Egy külön csomagok metrika ismerteti a TCP SZIN csomagok, amelyek menüpontjára vagy tőlük a kötet (a [kimenő forgalom](https://aka.ms/lboutbound)), amelyek egy adott előtér társítva. Ez a metrika segítségével megismerheti a TCP-kapcsolódási kísérletek a szolgáltatáshoz.

Használat **teljes** , az összesítés a legtöbb forgatókönyvhöz.

![Szinkronizálás a mi kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ábra: Load Balancer SZIN száma*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hogyan tudom ellenőrizni, a hálózati sávszélesség-használatot? 

A bájtok száma és a csomag számlálók metrika mennyisége bájt és per-front-end történik a szolgáltatás által fogadott vagy küldött csomagokat ismerteti.

Használat **teljes** , az összesítés a legtöbb forgatókönyvhöz.

Bájt vagy a csomag száma statisztikák lekéréséhez:
1. Válassza ki a **bájtok száma** és/vagy **csomagok száma** metrikus típus a **átlagos** , az összesítést. 
2. A következő lehetőségek közül választhat:
   * Egy adott előtérbeli IP-címet, az előtérbeli portot, a háttér-IP vagy a háttér-port a szűrőt alkalmazhat.
   * A terheléselosztó-erőforrásra vonatkozó teljes statisztikájának beolvasása szűrés nélkül.

![Bájtok száma](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ábra: Terheléselosztó bájtok száma*

#### <a name = "vipavailabilityandhealthprobes"></a>Hogyan diagnosztizálhatom a load balancer az üzembe helyezés?

Egy diagramon a virtuális IP-cím rendelkezésre állása és a mintavételi mérőszámok együttes használatával azonosíthatja hol keresse meg a problémát, és a probléma megoldásához. Szerezhet, hogy az Azure megfelelően működik-e, és használja ezt a tudást von meghatározásához, hogy a konfigurációs vagy az alkalmazás a hiba okát.

Mintavétel mérőszámok segítségével megtudhatja, hogyan Azure jogosult megtekinteni a megadott konfiguráció szerint a központi telepítés állapotát. Állapot-mintavételei megnézzük, mindig egy tökéletes első lépés a figyelés, vagy egy okának meghatározásához.

Hajtsa végre ezt a lépést további, és betekintést nyerhet hogyan az Azure megtekinti az alapul szolgáló adatsík felelős az adott központi telepítés állapotát a virtuális IP-cím rendelkezésre állási metrikák használatával. Ha mindkét metrikák kombinálja, elkülönítheti hol lehet a hiba, ebben a példában szemléltetett módon:

![VIP-diagnosztika](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Ábra: Kombinálásával dedikált IP-CÍMMEL és virtuális IP-cím rendelkezésre állási metrikák*

A diagram az alábbi információkat jeleníti meg:
- Az infrastruktúra magát kifogástalan állapotú, az infrastruktúra, a virtuális gépeket üzemeltet volt elérhető, és egynél több virtuális Gépet helyezte a háttérben. A kék VIP rendelkezésre állást, amely 100 százalék-nyomkövetési jelzi ezt az információt. 
- Azonban az állapot-mintavételi ellenőrzése (DIP elérhető) van, a diagram elején 0 %-os a narancssárga nyomkövetési jelzett módon. A zöld emeli ki, ahol az állapot (DIP elérhető) vált, kifogástalan állapotú, és hogy ez a felhasználói telepítés bekarikázott területén volt képes fogadni az új folyamatok.

A diagram lehetővé teszi a telepítés saját hibáinak becslés alapján, vagy kérje a támogatási e más hibák is megjelenhetnek nélkül. A szolgáltatás nem érhető el, mert állapotadat-mintavételek vagy egy meghibásodott alkalmazást, vagy egy kiszolgálóhiba miatt nem került sor.

### <a name = "Limitations"></a>Korlátozások

Virtuális IP-cím rendelkezésre állási jelenleg csak a nyilvános előtérrendszerek esetében.

## <a name = "ResourceHealth"></a>Erőforrás-állapot

A Standard Load Balancer-erőforrások állapotinformációit elérhetővé a meglévő **a Resource health** alatt **figyelő > Service Health**.

>[!NOTE]
>Erőforrás-állapot Load Balancer csak a Standard Load Balancer nyilvános bővítménykonfiguráció jelenleg érhető el. Belső load balancer-erőforrások vagy alapszintű SKU-k a Load Balancer-erőforrások nem teszik elérhetővé a resource health.

A nyilvános Standard Load Balancer-erőforrások állapotának megtekintése:
1. Válassza ki **figyelő** > **szolgáltatás állapota**.

   ![A figyelő lap](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ábra: A Service Health-hivatkozás az Azure Monitor*

2. Válassza ki **Resource Health**, és győződjön meg arról, hogy **előfizetés-azonosító** és **erőforrástípus = terheléselosztó** ki van jelölve.

   ![Erőforrás állapotadatai](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Ábra: Válassza ki az erőforrás állapota nézetben*

3. A listában válassza ki a terheléselosztó erőforrás megtekintéséhez a korábbi megfelelő állapotot.

    ![A Load Balancer állapot állapota](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Ábra: Terheléselosztó erőforrás állapota nézetben*
 
A resource health különböző állapotok, és ezek leírását az alábbi táblázatban láthatók: 

| Erőforrás állapotadatai | Leírás |
| --- | --- |
| Elérhető | A nyilvános standard load balancer erőforrás, kifogástalan állapotú és elérhető. |
| Nem elérhető | A nyilvános standard load balancer-erőforrás állapota nem megfelelő. Az egészségügyi diagnosztizálása kiválasztásával **Azure Monitor** > **metrikák**.<br>(*Nem érhető el* állapota is jelezheti azt, hogy az erőforrás nem csatlakozik a nyilvános standard load balancer.) |
| Ismeretlen | Erőforrás-állapot az nyilvános standard load balancer-erőforrás még nem lett frissítve.<br>(*Ismeretlen* állapota is jelezheti azt, hogy az erőforrás nem csatlakozik a nyilvános standard load balancer.)  |

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
- Tudjon meg többet a [betölteni a terheléselosztó kimenő kapcsolat](https://aka.ms/lboutbound).
- Ismerje meg [az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- További információ a [REST API-t az Azure Monitor-metrikák](https://docs.microsoft.com/rest/api/monitor/metrics/).


