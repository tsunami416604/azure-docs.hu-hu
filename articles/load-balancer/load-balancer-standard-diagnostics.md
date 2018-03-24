---
title: Az Azure szabványos terheléselosztó - diagnosztika |} Microsoft Docs
description: Az elérhető metrikák és állapotfigyelő információk segítségével diagnosztika Azure Standard terheléselosztóhoz
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
ms.openlocfilehash: 1d39cdc13e69740dc99e67f935b60db218536044
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>Standard terheléselosztóhoz metrikák és állapotfigyelő diagnosztika

Az Azure szabványos terheléselosztó erőforrások a következő diagnosztikai lehetőségeket biztosítja:
* Többdimenziós metrikák: A szabványos terheléselosztó a nyilvános és a belső terheléselosztó konfigurációk új többdimenziós diagnosztikai képességeket biztosít. Ez lehetővé teszi a figyeléséhez, kezeléséhez és a terheléselosztó erőforrások hibaelhárítása.

* Erőforrás állapota: A Load Balancer lap az Azure portálon, és a Resource Health lap (figyelő) teszi közzé az erőforrás állapota a szabványos terheléselosztó nyilvános terheléselosztó-konfigurációhoz.

Ez a cikk ismerteti a Kalauz ezen képességek, és ezek szabványos terheléselosztóhoz módját.

## <a name = "MultiDimensionalMetrics"></a>A többdimenziós metrikák

Azure Load Balancer a portál az új Azure metrikák (előzetes verzió) segítségével új többdimenziós metrikákat biztosít, és segíti, hogy a terheléselosztó erőforrások valós idejű diagnosztikai betekintést. 

Következő metrikák manapság elérhető különböző szabványos Load Balancer (LB) konfigurációk:

| Metrika | Erőforrás típusa | Leírás | Ajánlott aggregáció |
| --- | --- | --- | --- |
| VIP rendelkezésre állási (adathozzáférést elérési útja) | Nyilvános LB | Standard terheléselosztó folyamatosan gyakorolja az adatok útvonalat régión belül a terheléselosztó előtérbeli egészen a SDN-készlet, amely támogatja a virtuális gép számára. Mindaddig, amíg megfelelő példányok marad, a mérési követi az alkalmazás terheléselosztott forgalom az adott elérési úton. Az elérési útja az ügyfelek által használt is van hitelesítve. A mérték nem látható, hogy az alkalmazást, és nem ütközik más műveleteket.| Átlag |
| DIP rendelkezésre állási (állapot mintavételi) |  Nyilvános és a belső Terheléselosztó | Szabványos terheléselosztót használ egy elosztott szolgáltatás, amely figyeli a konfigurációs beállítások alapján az alkalmazás végpontjának állapotát probing állapotát. Ez a metrika összesítő biztosít, vagy egy végpont szűrt-nézetben minden egyes példány végpont a terheléselosztó-készlet.  Láthatja, hogyan terheléselosztó megtekinti a mintavételi állapotkonfiguráció jelöli az alkalmazás állapotát. |  Átlag |
| Szinkronizálás a mi csomagok |  Nyilvános LB | Standard Load Balancer nem állítsa le a TCP-kapcsolatok vagy TCP vagy UDP-csomagok adatfolyamok kommunikál. Adatfolyamok és azok kézfogások mindig a forrás- és a Virtuálisgép-példány közé esnek. Jobb elhárításában a TCP protokollt, hogy SZIN használatát csomagok számlálók tudni, hogy hány TCP-kapcsolat kísérletet tesz. A metrika a fogadott TCP SZIN csomagok számát jelenti.| Átlag |
| SNAT kapcsolatok |  Nyilvános LB |Standard terheléselosztó jelentések számára a nyilvános IP-cím előtérbeli rendszer masqueraded kimenő adatfolyamok száma. SNAT portjait egy kimeríthető erőforrás. Ez a metrika biztosíthat utalhat, hogy hogyan fokozottan az alkalmazás SNAT kimenő kezdeményezésű folyamatok függő van.  Sikeres és sikertelen kimenő SNAT adatfolyamok számlálói jelenti és hibaelhárításához, és megismerheti a kimenő forgalom állapotának használható.| Átlag |
| Bájt számlálók |  Nyilvános és a belső Terheléselosztó | Standard terheléselosztó előtér / feldolgozott adatokat jelenti.| Átlag |
| Csomag számlálók |  Nyilvános és a belső Terheléselosztó | Standard terheléselosztót a csomagokat egy előtér feldolgozott jelentések.| Átlag |

### <a name="view-load-balancer-metrics-in-the-portal"></a>Terheléselosztó metrikák megtekintése a portálon

Azure-portálon keresztül a metrikák (előzetes verzió) lapon, amely elérhető a terheléselosztó erőforrást lapját az adott terheléselosztó erőforrást és az Azure-figyelő lapon a Load Balancer metrikák mutatja. 

A metrikák megtekintéséhez, a Standard terheléselosztó erőforrásokat, tallózással keresse meg a mérőszámok (előzetes verzió), vagy ezeket a helyeket, válasszon ki a (terheléselosztó erőforrást figyelő oldal alatt) metrika típusát a legördülő listában állítsa be a megfelelő összesítési típusát, és szükség esetén Konfigurálja a szükséges szűrést és a csoportosítás, és tekintse meg a megadott feltételek alapján metrikáihoz ügyfélállapotainak lesz.

![Standard terheléselosztóhoz metrikák preview](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*. Ábra – DIP rendelkezésre állási / állapotfigyelő állapot metrika mintavételi terheléselosztóhoz*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API-k szoftveres használatával többdimenziós metrikák beolvasása

API-útmutató a Multi-Dimensional metrika definíciók és értékek érhető el: [REST API Walkthrouh figyelés > Multi-Dimensional API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)

### <a name = "DiagnosticScenarios"></a>Diagnosztikai szabhatják és ajánlott nézetek

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Az elérési útja az naprakész, és elérhető legyen a saját Terheléselosztói VIP?

VIP rendelkezésre állási metrikára ismerteti az elérési útja abban a régióban a számítási gazdagépre, ahol a virtuális gépek találhatók állapotát. A reflexió állapotát az Azure-infrastruktúra is. Ez a mérőszám a használhatja:
- A szolgáltatás külső rendelkezésre állásának figyelése
- Mélyebben elmerülne a rendszer, és megértése, hogy kifogástalan-e a platformot, amelyre a szolgáltatás telepítve van, vagy hogy a vendég operációs rendszer vagy alkalmazás példány állapota kifogástalan
- Különítse el, hogy a szolgáltatás vagy a mögöttes adatok vezérlősík kapcsolódó esemény. Ne tévessze össze ennek az az állapot-mintavételi ellenőrzése ("DIP availability").

A VIP rendelkezésre állási szabványos terheléselosztó erőforrások eléréséhez:
- Győződjön meg arról, hogy a megfelelő terheléselosztót erőforrás van jelölve. 
- Válassza ki **VIP rendelkezésre állási** a a **metrika** legördülő menüből. 
- Válassza ki **Avg** a **összesítési**. 
- Emellett szűrő hozzáadása a virtuális IP-cím vagy virtuális IP-port, mint a dimenzió a szükséges előtérbeli IP-címmel vagy elülső rétegbeli portot, és a csoport által a kiválasztott dimenzió.

![Probing VIP](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Ábra - részletek probing Terheléselosztói VIP*

A metrika egy aktív, a sávon kívüli mérési állítja elő. A vizsgálathoz használt szolgáltatás abban a régióban a mérést a forgalom származik, amint a központi telepítés létrehozásához engedélyezi egy nyilvános front-end aktiválva van, és továbbra is fennáll, amíg el nem távolítja az előtér. 

>[!NOTE]
>Belső frontends jelenleg nem támogatottak. 

Egy a központi telepítés előtér- és a szabály megfelelő csomag rendszeresen jön létre. Az halad át a régió a forráskiszolgálóról a gazdagéphez ahol egy virtuális Gépet, a háttérkészletben. A terheléselosztó infrastruktúra azonos terhelés és a fordítás műveleteket hajtja végre, ugyanúgy, mint az összes többi forgalom. Ez a Hálózatfigyelő sávon a betöltéskor elosztott terhelésű végpont. Miután a mintavétel a számítási gazdagépen megérkeznek, ahol a megfelelő virtuális gépek háttérkészlethez, a számítási állomás a vizsgálathoz használt szolgáltatás választ hoz létre. A virtuális gép nem látja ezt a forgalmat.
VIP rendelkezésre állási meghiúsul a következő okok miatt:
- A központi telepítés nem kifogástalan állapotú virtuális gépeket háttérkészlet idővel rendelkezik. 
- Infrastruktúra kimaradás VIP rendelkezésre állási sikertelenségét okozó történt.

Használhatja a [VIP rendelkezésre állási metrikára és a mintavételi állapot diagnosztikai célra](https://aka.ms/lbdiagnostics#vipavailabilityandhealthprobes).

Használjon **átlagos** , az összesítés a legtöbb forgatókönyvhöz.

#### <a name="are-the-backend-instances-for-my-vip-responding-to-probes"></a>A VIP háttér példányainak reagál a mintavételt?

Állapotfigyelő mintavételi állapot metrika ismerteti az alkalmazások központi telepítésének állapotát, a terheléselosztó állapotmintáihoz konfigurálásakor az Ön által megadott. Terheléselosztó hova küldje a új adatfolyamok meghatározására használja a állapotmintáihoz állapotát. Állapotteljesítmény az Azure-infrastruktúra címet származnak, és látható a virtuális gép vendég operációs rendszer belül.

A DIP rendelkezésre állási lekérése, a Standard terheléselosztó erőforrások
- Válassza ki a **DIP rendelkezésre állási** a metrika **Avg** összesítési típusát. 
- Szűrőt alkalmaz a szükséges VIP IP-cím és port (vagy mindkettőt).

![DIP rendelkezésre állása](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*. Ábra – a terheléselosztó VIP rendelkezésre állása*

Állapotteljesítmény sikertelen a következő okok miatt:
- Ha egy porthoz nem figyel, vagy nem válaszol vagy a megfelelő protokoll állapotmintáihoz konfigurálja. Ha a szolgáltatás DSR (fix IP-Címek) szabályokat használ, győződjön meg arról, hogy a szolgáltatás figyeli a hálózati adapter IP-konfigurációja IP-címét, és nem csupán a a visszacsatolási konfigurálva az előtérbeli IP-címet szeretné.
- Ha a mintavételi nem engedélyezett a hálózati biztonsági csoport, a virtuális gép vendég operációs rendszer tűzfal, vagy az alkalmazási rétegre szűrők.

Használjon **átlagos** , az összesítés a legtöbb forgatókönyvhöz.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Hogyan ellenőrizhető a kimenő kapcsolat statisztika? 

SNAT kapcsolatok metrika ismerteti a sikeres és sikertelen mennyiségét (a [kimenő forgalom](https://aka.ms/lboutbound)).

Csak nullánál nagyobb sikertelen kapcsolatok kötet SNAT port Erőforrásfogyás jelzi. További vizsgálat kell, és határozza meg, mi okozza-e hibák. SNAT port Erőforrásfogyás akkor jelentkezik, hibák létrehozásához, egy [kimenő forgalom](https://aka.ms/lboutbound). Tekintse át a kimenő kapcsolatok használata esetén a forgatókönyveket, a mechanizmusok, és hogyan mérséklése / SNAT port Erőforrásfogyás elkerülése érdekében tervezési megértése. 

A beolvasandó SNAT kapcsolat statisztika,
- Válassza ki **SNAT kapcsolatok** mérőszám típusa és **Sum** összesítési szerint. 
- csoportosítási **kapcsolat állapota** sikeres és sikertelen SNAT kapcsolat száma különböző kapcsolatokon kezeli őket. 

![SNAT kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Ábra - terheléselosztóhoz SNAT kapcsolatok száma*


#### <a name="how-do-i-check-inbound--outbound-connection-attempts-for-my-service"></a>Hogyan ellenőrzi a bejövő / kimenő próbálnak meg kapcsolódni a szolgáltatáshoz?

SZIN csomagok metrika ismerteti a TCP SZIN csomagok, amely beérkezett vagy távozó mennyiségét (a [kimenő forgalom](https://aka.ms/lboutbound)) egy adott háttértűzfal társított. Ez a metrika megértéséhez TCP próbálnak meg kapcsolódni a szolgáltatáshoz használható.
Teljes használják az összesítés a legtöbb esetben.

![Szinkronizálás a mi kapcsolat](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Ábra - terheléselosztó SZIN száma*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Hogyan ellenőrizhető a hálózati sávszélesség-használat? 

Bájt / csomag számlálók metrika bájt és a szolgáltatás előtérbeli / alapon által küldött vagy fogadott csomagok ismerteti.
Használjon **teljes** , az összesítés a legtöbb forgatókönyvhöz.

Bájt vagy a csomag statisztikai eléréséhez
- Válassza ki a **bájtok száma** és / vagy **csomagok száma** metrika típus **Avg** , az összesítés. 
- A megadott előtér-IP-, az elülső rétegbeli portot vagy háttér IP-címet vagy az adott port szűrőt. 
- vagy általános statisztika lekérésére terheléselosztó erőforrást szűrés nélkül.


Néhány példa nézeteket különböző konfigurációt - metrikák

![Bájtok száma](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Ábra - terheléselosztóhoz bájtok száma*

#### <a name = "vipavailabilityandhealthprobes"></a>Hogyan diagnosztizálhatja a saját terheléselosztó üzembe helyezése?

A VIP rendelkezésre állási és állapot-mintavételi csomagjai többek közt a mérőszámait egyetlen diagram kombinációja lehetővé teszi határozza meg, hol keresse meg a problémát, és a probléma megoldásához. Kapnak, hogy Azure megfelelően működik-e, és hogy kétséget határozza meg a konfigurációs vagy az alkalmazás okozza.

Állapot-mintavételi metrikák használatával megérthetik, hogy hogyan Azure megtekinti a megadott konfiguráció szerint a központi telepítés állapotát. Megnézi állapotteljesítmény mindig kiváló első lépés a figyelés, vagy egy okának meghatározásához.

További lépéssel, és hogyan az Azure megtekinti a mögöttes adatok vezérlősík felelős az adott központi telepítési állapotának betekintést VIP rendelkezésre állási metrikák használatával. Mindkét metrikák kombinálásával, elkülönítheti, ahol a hiba lehet, hogy ebben a példában ismertetett módon:

![VIP-diagnosztika](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*Ábra - kombinálásával DIP-t és a VIP rendelkezésre állási metrikák*

A diagram bemutatja a következőket:
- Maga az infrastruktúra volt kifogástalan, az infrastruktúra, a virtuális gépeket üzemeltet volt elérhető, és egynél több virtuális gép a háttérrendszer lett helyezve. Ez a kék VIP rendelkezésre állását jeleníti meg a 100 %-nyomkövetési jelzi. 
- Azonban állapotfigyelő mintavételi (DIP rendelkezésre állási) állapota a diagram elején 0 % narancssárga nyomkövetés jelöli. A piros emeli ki, ahol egészségügyi mintavétel (DIP rendelkezésre állási) bekarikázott területén vált kifogástalan, és ekkor az ügyfél telepítése tud fogadni új adatfolyamok volt.

A diagramon az ügyfél a központi telepítést a saját anélkül, hogy kitalálja, vagy forduljon a támogatási hogy történtek-e más előforduló problémák elhárításához engedélyezett. Az ügyfélszolgálat nem érhető el, mert állapotteljesítmény volt sikertelen pingeket vagy egy helytelen konfiguráció, vagy a hibás alkalmazást.

### <a name = "Limitations"></a>Korlátozások

- VIP rendelkezésre állási érhető el jelenleg csak nyilvános frontends.

## <a name = "ResourceHealth"></a>Erőforrás-állapot

A szabványos terheléselosztó erőforrások állapot tesz elérhetővé a meglévő keresztül **erőforrás állapota** alatt **figyelő > szolgáltatásának állapota**.

>[!NOTE]
>Az erőforrás állapota terheléselosztóhoz érhető el jelenleg csak a Standard terheléselosztó nyilvános konfigurációja. Belső terheléselosztó erőforrásokhoz vagy alapszintű Termékváltozat a Load Balancer erőforrások erőforrás állapota nem teszik ki.

A szabványos nyilvános terheléselosztót erőforrások állapotának megtekintése
 - Keresse meg a **figyelő > szolgáltatás állapotát**.

  ![A figyelő lap](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Ábra - szolgáltatás állapotát az Azure-figyelő*

 - Válassza ki **Resource Health** , és győződjön meg arról, hogy helyes-e **előfizetés-azonosító** és **erőforrástípus = terheléselosztó** van kiválasztva.

  ![Erőforrás-állapot](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *. Ábra – válassza ki az erőforrás állapota nézet*

 - Kattintson a Load Balancer erőforráson korábbi állapota megtekintéséhez a listából.

    ![Terheléselosztó állapot betöltése](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Ábra - terheléselosztó erőforrás állapotának megtekintése*
 
A következő táblázat a különböző erőforrás-állapot és azok leírásait tartalmazza. 

| Erőforrás-állapot | Leírás |
| --- | --- |
| Elérhető | A szabványos nyilvános terheléselosztót erőforrás egy kifogástalan és elérhető |
| Nem érhető el | A szabványos nyilvános terheléselosztót erőforrás állapota nem kifogástalan. Diagnosztizálja keresztül Azure figyelő állapotának > metrikákat. (*Ez előfordulhat, hogy is jelenti, hogy erőforrás nem nyilvános szabványos terheléselosztó*) |
| Ismeretlen | A nyilvános szabványos terheléselosztóhoz erőforrás állapota még nem lett frissítve. (*Ez előfordulhat, hogy is jelenti, hogy erőforrás nem nyilvános szabványos terheléselosztó*)  |

## <a name="next-steps"></a>További lépések

- További információ [szabványos terheléselosztó](load-balancer-standard-overview.md)
- További információ [Load Balancer kimenő kapcsolat](https://aka.ms/lboutbound)


