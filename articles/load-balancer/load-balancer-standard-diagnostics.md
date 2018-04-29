---
title: Az Azure szabványos terheléselosztó diagnosztika |} Microsoft Docs
description: Olvassa el elérhető metrikák és állapotfigyelő diagnosztikai Azure Standard terheléselosztóhoz.
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
ms.date: 03/21/2018
ms.author: Kumud
ms.openlocfilehash: 9d5d596254f673b86650e8d9754dacdb70be0666
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Standard terheléselosztóhoz metrikák és állapotfigyelő diagnosztika

Azure Standard terheléselosztót biztosít az erőforrások a következő diagnosztikai képességek:
* **A többdimenziós metrikák**: új többdimenziós diagnosztikai képességeket biztosít a nyilvános és a belső terhelést terheléselosztó konfigurációjában. Figyelheti, kezelése és a betöltés terheléselosztó erőforrások hibaelhárítása.

* **Erőforrás állapota**: A terheléselosztó oldal az Azure portálon, és a Resource Health lap (figyelő) teszi közzé a Resource Health szakasz a nyilvános terheléselosztó-konfigurációhoz a szabványos terheléselosztó.

Ez a cikk ismerteti a Kalauz képességek, és a közöttük való szabványos terheléselosztóhoz kínál.

## <a name = "MultiDimensionalMetrics"></a>A többdimenziós metrikák

Az Azure terheléselosztó biztosít az új Azure metrikák (előzetes verzió) Azure-portálon keresztül új többdimenziós metrikákat, és segíti, hogy a terhelés valós idejű diagnosztikai betekintést terheléselosztó erőforrások. 

A különböző szabványos terheléselosztó konfigurációi adja meg a következő metrikákat:

| Metrika | Erőforrás típusa | Leírás | Ajánlott aggregáció |
| --- | --- | --- | --- |
| VIP rendelkezésre állási (adathozzáférést elérési útja) | Nyilvános terheléselosztó | Standard terheléselosztó folyamatosan gyakorolja az adatok útvonalat régión belül a terhelés terheléselosztó előtér, egészen a SDN-készlet, amely támogatja a virtuális gép számára. Mindaddig, amíg megfelelő példányok marad, a mérési követi az alkalmazás terheléselosztott forgalom az adott elérési úton. Az elérési útja a felhasználók által használt is van hitelesítve. A mérték nem látható, hogy az alkalmazást, és nem ütközik más műveleteket.| Átlag |
| DIP rendelkezésre állási (állapot mintavételi) |  Nyilvános és a belső terheléselosztó | Standard terheléselosztók állapot-ellenőrzés egy elosztott szolgáltatás, amelyet figyeli az alkalmazás végpontjának állapotát a konfigurációs beállítások szerint. Ez a metrika biztosít egy összesítés vagy a végpont szűrt nézet minden példány végpont a terhelés terheléselosztó-készlet. Láthatja hogyan terheléselosztó megtekinti az alkalmazás állapotának a mintavételi állapotkonfiguráció jelöli. |  Átlag |
| Szinkronizálás a Mi (szinkronizálás) csomagok |  Nyilvános terheléselosztó | Szabványos Load Balancer nem állítsa le a Transmission Control Protocol (TCP) kapcsolatok vagy TCP vagy UDP-csomagok adatfolyamok interakciót. Adatfolyamok és azok kézfogások mindig a forrás- és a Virtuálisgép-példány közé esnek. Jobb elhárításában a TCP protokollt, hogy SZIN használatát csomagok számlálók tudni, hogy hány TCP-kapcsolat kísérletet tesz. A metrika a fogadott TCP SZIN csomagok számát jelenti.| Átlag |
| SNAT kapcsolatok |  Nyilvános terheléselosztó |Standard terheléselosztó jelentések vannak masqueraded, hogy a nyilvános IP-cím előtér kimenő adatfolyamok száma. Hálózati cím fordítási (SNAT) forrásportok egy kimeríthető erőforrás. Ez a metrika biztosíthat utalhat, hogy hogyan fokozottan az alkalmazás SNAT kimenő kezdeményezésű folyamatok függő van. Sikeres és sikertelen kimenő SNAT adatfolyamok számlálói jelenti és hibaelhárításához, és megismerheti a kimenő forgalom állapotának használható.| Átlag |
| Bájt számlálók |  Nyilvános és a belső terheléselosztó | Standard terheléselosztó előtér / feldolgozott adatokat jelenti.| Átlag |
| Csomag számlálók |  Nyilvános és a belső terheléselosztó | Standard terheléselosztót a csomagokat egy előtér feldolgozott jelentések.| Átlag |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>A load balancer metrikákat, tekintse meg az Azure-portálon

Az Azure-portálon elérhetővé teszi a terhelés terheléselosztó metrikák keresztül a metrikák (előzetes verzió) lap, amely mindkét a terheléselosztó erőforrás oldal betöltése egy adott erőforráshoz, az Azure-figyelő lapon található. 

A szabványos terheléselosztó erőforrások mutatóit megtekintése:
1. A metrikák (előzetes verzió) lapra, és a következő lehetőségek közül választhat:
   * A betöltési terheléselosztó erőforrás lapon válassza ki a metrika a legördülő listában.
   * Az Azure-figyelő oldalon válassza ki a terheléselosztó erőforrást.
2. Állítsa be a megfelelő összesítési típusát.
3. Nem kötelező lépésként beállíthatja a szükséges szűrést és a csoportosítást.

![Standard terheléselosztóhoz metrikák preview](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*. Ábra: DIP rendelkezésre állás és a rendszerállapot mintavételi állapot metrika szabványos terheléselosztóhoz*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API-k szoftveres használatával többdimenziós metrikák beolvasása

A többdimenziós metrikai meghatározásainak és értékek beolvasása API útmutatóért lásd: [Azure figyelési REST API-forgatókönyv](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Diagnosztikai szabhatják és ajánlott nézetek

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Az az elérési útja és elérhető a terheléselosztó VIP?

A VIP rendelkezésre állási metrikára ismerteti az elérési útja abban a régióban a számítási gazdagépre, ahol a virtuális gépek találhatók állapotát. A metrika az Azure-infrastruktúra állapotának tükre. A metrikát használhatja:
- A szolgáltatás külső rendelkezésre állásának figyelése
- Mélyebben elmerülne a rendszer, és megismerheti a megfelelő-e a platformot, amelyre a szolgáltatás telepítve van-e a vendég operációs rendszer vagy alkalmazás példány kifogástalan.
- Különítse el, hogy a szolgáltatás vagy a mögöttes adatok vezérlősík kapcsolódó esemény. Ez a metrika az állapot-mintavételi ellenőrzése ("DIP availability") a ne tévessze össze.

A VIP rendelkezésre állási szabványos terheléselosztó erőforrások eléréséhez:
1. Győződjön meg arról, hogy a helyes terheléselosztó erőforrást van jelölve. 
2. Az a **metrika** legördülő listában válassza **VIP rendelkezésre állási**. 
3. Az a **összesítési** legördülő listában válassza **Avg**. 
4. Továbbá adjon hozzá egy szűrőt az a virtuális IP-cím vagy a virtuális IP-port, mint a dimenzió a szükséges előtér-IP-címmel vagy az előtér-port, és majd a kiválasztott dimenzió szerint kell csoportosítani őket.

![Probing VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ábra: Részletek számlálása Terheléselosztói VIP*

A metrika egy aktív, a sávon kívüli mérési állítja elő. A vizsgálathoz használt szolgáltatás a régión belül a mérték forgalom származik. A szolgáltatás aktiválása megtörtént, amint egy nyilvános előtér egy központi telepítés létrehozásához, és hogy továbbra is fennáll, amíg el nem távolítja az előtér. 

>[!NOTE]
>Belső előtér-webkiszolgálóinak jelenleg nem támogatottak. 

Egy a központi telepítés előtér- és a szabály megfelelő csomag rendszeresen jön létre. Bejárja a régió a forráskiszolgálóról a gazdagéphez ahol egy virtuális Gépet, a háttér-készletben. A load balancer infrastruktúra végzett a terhelés és a fordítás műveletek azonban nem az összes többi forgalom. Ez a Hálózatfigyelő sávon az elosztott terhelésű végpont. Után a mintavétel érkezik a számítási állomáson, ahol a háttér-készletben található megfelelő virtuális gép, a számítási gazdagép állít elő, a vizsgálathoz használt szolgáltatás választ. A virtuális gép nem látja ezt a forgalmat.

VIP rendelkezésre állási meghiúsul a következő okok miatt:
- A telepítés nem kifogástalan állapotú virtuális gépeket a háttér-készlet fennmaradó rendelkezik. 
- Infrastruktúra kimaradás történt.

Diagnosztikai célokra is használhatja a [VIP rendelkezésre állási metrikára együtt az állapot-mintavételi](#vipavailabilityandhealthprobes).

Használjon **átlagos** , az összesítés a legtöbb forgatókönyvhöz.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>A VIP-háttér példányainak reagál a mintavételt?

Az állapotfigyelő mintavételi állapot metrika ismerteti az alkalmazások központi telepítésének állapotát, a terheléselosztó a állapotmintáihoz konfigurálásakor az Ön által megadott. A terheléselosztó a állapotmintáihoz állapotának használatával új adatfolyamok küldési helyének meghatározásához. Állapotteljesítmény az Azure-infrastruktúra címet származnak, és látható a virtuális gép vendég operációs rendszer belül.

A beolvasandó DIP rendelkezésre állásának szabványos terheléselosztó erőforrások:
1. Válassza ki a **DIP rendelkezésre állási** a metrika **Avg** összesítési típusát. 
2. Szűrőt alkalmaz a szükséges VIP IP-cím és port (vagy mindkettőt).

![DIP rendelkezésre állása](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*. Ábra: Terheléselosztói VIP-elérhetősége*

Állapotteljesítmény sikertelen a következő okok miatt:
- Konfigurál egy állapotmintáihoz porthoz nem figyel, vagy nem válaszol vagy a megfelelő protokollt használja. Ha a szolgáltatás közvetlen kiszolgálói válasz (DSR vagy fix IP) használja a szabályokat, győződjön meg arról, hogy a szolgáltatás figyeli a hálózati adapter IP-konfigurációja IP-címét, és nem csak a visszacsatolási, amelynek része az előtér-IP-cím a címek.
- A mintavétel nem engedélyezett a hálózati biztonsági csoport, a virtuális gép vendég operációs rendszer tűzfal vagy az alkalmazás réteg szűrőket.

Használjon **átlagos** , az összesítés a legtöbb forgatókönyvhöz.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hogyan ellenőrizhető a kimenő kapcsolat statisztika? 

A SNAT kapcsolatok metrika ismerteti a sikeres és sikertelen kapcsolatok mennyisége [kimenő forgalom](https://aka.ms/lboutbound).

Csak nullánál nagyobb sikertelen kapcsolatok kötet SNAT port Erőforrásfogyás jelzi. Ki kell vizsgálni további meghatározni, hogy mi okozza-e hibák. SNAT port Erőforrásfogyás akkor jelentkezik, hibaként létrehozásához egy [kimenő folyam](https://aka.ms/lboutbound). Tekintse át a kimenő kapcsolatokról megtudhatja, hogy a forgatókönyvek és mechanizmusok a munkahelyi hálózatban, és megtudhatja, hogyan csökkentheti, és kialakítása SNAT port Erőforrásfogyás elkerülése érdekében. 

A beolvasandó SNAT kapcsolat statisztika:
1. Válassza ki **SNAT kapcsolatok** mérőszám típusa és **Sum** összesítési szerint. 
2. Csoportosítási szempont **kapcsolat állapota** a sikeres és sikertelen SNAT kapcsolat összesítése, amelyek különböző vonalak jelölik. 

![SNAT kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Ábra: Load Balancer SNAT kapcsolatok száma*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Hogyan ellenőrzi a bejövő/kimenő próbálnak meg kapcsolódni a szolgáltatáshoz?

SZIN csomagok metrika ismerteti a TCP SZIN csomagok, amely beérkezett vagy távozó mennyiségét (a [kimenő forgalom](https://aka.ms/lboutbound)), amelyek egy adott előtér. Ez a metrika használatával megérthetik, hogy a TCP próbálnak meg kapcsolódni a szolgáltatáshoz.

Használjon **teljes** , az összesítés a legtöbb forgatókönyvhöz.

![Szinkronizálás a mi kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ábra: Load Balancer SZIN száma*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hogyan ellenőrizhető a hálózati sávszélesség-használat? 

A bájtok és csomag számlálók metrika bájt és /-első-a befejezési alapon a szolgáltatás által fogadott vagy elküldött csomagok ismerteti.

Használjon **teljes** , az összesítés a legtöbb forgatókönyvhöz.

Bájt vagy a csomag statisztikai beolvasása:
1. Válassza ki a **bájtok száma** és/vagy **csomagok száma** típust, a **Avg** , az összesítés. 
2. A következő lehetőségek közül választhat:
   * Egy adott előtér-IP, az előtér-port, a háttér IP vagy a háttér-port szűrőt alkalmaz.
   * Statisztikai adatait a terheléselosztó erőforrást beolvasni szűrés nélkül.

![Bájtok száma](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ábra: Terheléselosztó bájtok száma*

#### <a name = "vipavailabilityandhealthprobes"></a>Hogyan diagnosztizálhatja a saját terhelés terheléselosztó telepítési?

Egy diagramon a VIP rendelkezésre állását és állapot-mintavételi metrikák együttes használatával azonosíthatja, hol keresse meg a problémát, és megoldhatja a problémát. Kapnak, hogy Azure megfelelően működik-e, és a Tudásbázis segítségével kétséget annak megállapítása, hogy a konfigurációs vagy az alkalmazás az alapvető okát.

Állapot-mintavételi metrikák használatával megérthetik, hogy hogyan Azure megtekinti a megadott konfiguráció szerint a központi telepítés állapotát. Megnézi állapotteljesítmény mindig kiváló első lépés a figyelés, vagy egy okának meghatározásához.

Lépéssel azt egy további, és hogyan Azure megtekinti a mögöttes adatok vezérlősík, amely felelős az adott központi telepítési állapotának betekintést VIP rendelkezésre állási metrikák használatával. Mindkét metrikák kombinálásával elkülönítheti ahol a hiba lehet, ebben a példában ismertetett módon:

![VIP-diagnosztika](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Ábra: Kombinálásával DIP-t és a VIP rendelkezésre állási metrikák*

A diagram az alábbi információkat jeleníti meg:
- Maga az infrastruktúra volt megfelelő, a virtuális gépeket üzemeltet a infrastruktúra volt elérhető, és egynél több virtuális gép lett helyezve a háttérben. A kék VIP rendelkezésre állás érdekében, 100 százalék-nyomkövetési jelzi ezt az információt. 
- Azonban az állapot-mintavételi ellenőrzése (DIP rendelkezésre állása) jelenleg 0 százalék a diagramon, elején a narancssárga nyomkövetési jelöli. A zöld emeli ki, ahol a állapota (DIP rendelkezésre állási) vált kifogástalan állapotú, és hogy ez az ügyfél-telepítési bekarikázott területén tudta új forgalom fogadására.

A diagram lehetővé teszi az ügyfelek a központi telepítést a saját elhárítása nélkül kitalálja, vagy forduljon a támogatási e egyéb problémákat is megjelenhetnek. A szolgáltatás nem érhető el, mert állapotteljesítmény vagy egy helytelen konfiguráció, vagy a hibás alkalmazást miatt volt sikertelen.

### <a name = "Limitations"></a>Korlátozások

VIP rendelkezésre állási érhető el jelenleg csak nyilvános előtér-webkiszolgálóinak.

## <a name = "ResourceHealth"></a>Erőforrás-állapot

A szabványos terheléselosztó erőforrások állapot tesz elérhetővé a meglévő keresztül **erőforrás állapota** alatt **figyelő > szolgáltatásának állapota**.

>[!NOTE]
>Erőforrás-állapot terheléselosztóhoz érhető el jelenleg csak szabványos terheléselosztó nyilvános konfigurációt. Belső terheléselosztó erőforrások vagy alapszintű termékváltozat a terheléselosztó erőforrások erőforrás állapota nem teszik ki.

A nyilvános szabványos terheléselosztó erőforrások állapotának megjelenítése:
1. Válassza ki **figyelő** > **szolgáltatás állapotát**.

   ![A figyelő lap](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ábra: A szolgáltatás állapotát hivatkozásra kattintva Azure figyelő*

2. Válassza ki **erőforrás állapota**, majd ellenőrizze, hogy **előfizetés-azonosító** és **erőforrástípus terheléselosztó =** van kiválasztva.

   ![Erőforrás-állapot](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *. Ábra: Válassza ki az erőforrás állapota nézet*

3. A listában válassza ki a terheléselosztó erőforrás az előzményadatok állapotadatok megtekintéséhez.

    ![Terheléselosztó állapot betöltése](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *. Ábra: Terheléselosztó erőforrás állapotának megtekintése*
 
A különböző erőforrás állapotának állapotok és azok leírásait tartalmazza a következő táblázatban láthatók: 

| Erőforrás-állapot | Leírás |
| --- | --- |
| Elérhető | A nyilvános szabványos terheléselosztó erőforrást egy kifogástalan és elérhető. |
| Nem érhető el | A nyilvános szabványos terheléselosztó erőforrást állapota nem kifogástalan. A health diagnosztizálása kiválasztásával **Azure figyelő** > **metrikák**.<br>(*Nem érhető el* állapota is jelentheti, hogy az erőforrás nem csatlakozik a szabványos nyilvános terheléselosztót.) |
| Ismeretlen | A nyilvános szabványos terheléselosztó erőforrást az erőforrás-állapot még nem lett frissítve.<br>(*Ismeretlen* állapota is jelentheti, hogy az erőforrás nem csatlakozik a szabványos nyilvános terheléselosztót.)  |

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md).
- További információ a [terheléselosztó kimenő kapcsolat betöltése](https://aka.ms/lboutbound).


