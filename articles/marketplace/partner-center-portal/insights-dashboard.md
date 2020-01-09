---
title: Marketplace-elemzések irányítópultja a kereskedelmi piactéren a partner Centerben
description: Hozzáférés a piactér webes elemzési szolgáltatásához, amely lehetővé teszi a kiadók számára, hogy a AppSource és az Azure piactéren kirakatokban is mérjék az ügyfelek részvételét.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: edfdf7bbfaa5fa5abb00aa7efcb35bfbcd6e68a4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480540"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Marketplace-elemzések irányítópultja a kereskedelmi piactéren

Ez a cikk a partner Center Piactéri információk irányítópultján nyújt információt. Ez az irányítópult a piactér webes elemzési szolgáltatásának összegzését jeleníti meg, amely lehetővé teszi a kiadók számára, hogy a piactéren kirakatokban felsorolt termék részletes oldalain mérjék fel az ügyfelek részvételét: AppSource és Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Marketplace Insights irányítópult

A Marketplace-elemzések **irányítópultjának** a partner Centerben való eléréséhez nyissa meg az **[elemzés lapot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a kereskedelmi piactéren.

A következő elemek grafikus ábrázolásait tekintheti meg:  

- [Marketplace-információk összegzése](#marketplace-insights-summary)
- [Oldalak látogatása földrajz szerint](#page-visits-by-geography)  
- [Oldal-látogatások és egyedi látogatói trend](#page-visits-versus-unique-visitors-trend)
- [A CTAs-szel rendelkező egyedi látogatók és a művelet meghívása](#call-to-action-versus-unique-visitors-with-ctas)
- [Oldalak látogatása és a művelet meghívása ajánlatok alapján](#page-visits-and-calls-to-action-by-offers)
- [A művelet százalékos trendjeinek meghívása](#call-to-action-percentage-trend)
- [Az átirányítási tartományok által felkeresett és kezdeményezett műveletek](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace-információk részleteinek táblázata](#marketplace-insights-details-table)

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [a kereskedelmi piactér Analytics szolgáltatással kapcsolatos gyakori kérdések és fogalmak](./faq-terminology.md).

### <a name="insights-dashboard-layout"></a>Az áttekintések irányítópultjának elrendezése

A piactér metrikáit többféleképpen is megtekintheti:

- Kirakat lapok
- Oldal szűrők
- Dátumszűrők

**Kirakat lapok**: az ajánlatok metrikáit külön tekintheti meg a AppSource & Azure Marketplace lapjain keresztül. Válassza ki az ajánlat (oka) t a jobb oldalon található ajánlat legördülő listából, és tekintse meg a kijelölt ajánlat (ok) metrikáinak vizualizációját. Alapértelmezés szerint az összes ajánlat ki van választva.

![A partner Center-bepillantást tartalmazó irányítópult ajánlat legördülő lista](./media/insights-offer-dropdown.png)

Az információk kiértékelése – **szűrők**: ezeket a szűrőket az ajánlat (Product Details oldal) szintjén kell alkalmazni. A megtekinteni kívánt feltételekhez több szűrőt is kiválaszthat. Ez a szűrő a teljes Piactéri információk szakaszra vonatkozik, beleértve a diagramokat és a részleteket is.

![A partner Center-adatáttekintések irányítópultjának szűrői](./media/insights-page-filter.png)

- Az ajánlatok nevei csak azokra az ajánlatokra vannak felsorolva, amelyeknek a kijelölt dátumtartomány a lapjainak látogatása.  
- Az alapértelmezett kijelölés az összes szűrési lehetőség esetében az "all".
- Az alkalmazott szűrők megjelenítik a kiválasztott beállítások (ok) számát. Az alkalmazott szűrők nem jelennek meg az alapértelmezett "all" (összes) kijelölésnél.

![A partner Center-beli bepillantást alkalmazó szűrők](./media/insights-page-filter-two.png)

**Bepillantások dátum szűrői**: Ez a szűrő a teljes Piactéri adatokra vonatkozó szakaszra vonatkozik. A szűrők tartalmazhatnak előre meghatározott dátumtartományt vagy egyéni dátumtartományt is.

![A partneri központ bepillantást a szűrők dátumára](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace-információk összegzése

A Piactéri elemzések összegzése szakasz megjeleníti a **látogatások**számát, a **végrehajtandó hívásokat**és a kijelölt dátumtartomány **egyedi látogatóit** .

### <a name="page-visits"></a>Oldalak meglátogatása

Ez a szám a kiválasztott dátumtartomány esetében az ajánlat oldalon (a termék részletei lapon) lévő különböző felhasználói munkamenetek számát jelöli. A piros/zöld százalékos mutató a lapok közötti látogatások növekedési arányát jelöli. A trend diagram a látogatások hónap és hónap közötti számát jelöli.

### <a name="unique-visitors"></a>Egyedi látogatók

Ez a szám az oldal szűrőben kiválasztott ajánlat (ok) esetében a kiválasztott dátumtartomány esetében a különböző látogatók számát jelöli. Egy vagy több termékspecifikus oldalt felkereső látogató egy egyedi látogatónak számít.

### <a name="call-to-action"></a>Művelet meghívása

Ez a szám azt jelzi, **hogy a művelet meghívása** gombra kattintások száma az ajánlat oldalon (a termék részletes lapján) befejeződött. A **művelet meghívása** akkor számít, ha a **Letöltés most**, az **ingyenes próbaverzió**, a **Kapcsolatfelvétel**és a **Test Drive** gomb van kiválasztva.

![A partner Center-elemzések hívása a művelet összegzésére](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Oldalak látogatása földrajz szerint

Az alábbi hő a **látogatások**számát, a **művelet meghívását**és az **ügyfelek országa szerinti egyedi látogatókat**jeleníti meg. A nagyobb lapokon megjelenő látogatások a sötétebb leképezési színek és az alacsonyabb oldal-látogatások a világosabb Térkép színeiben jelennek meg.

![A partneri központ bepillantást nyújt a földrajzi eloszlásra](./media/insights-geography.png)

A hő a következő képességeket tartalmazza:

- A hő egy kiegészítő rácstal rendelkezik, ahol megtekintheti a **látogatások**részleteit, a **műveleti hívásokat** és az **egyedi látogatókat** egy adott helyen. Ha kívánja, nagyíthat egy adott helyet.  
- Az **országok spreadje** az összes olyan ország számát jelenti, amelyből az ügyfelek a kiválasztott Dátumtartomány alapján jelentettek oldal-látogatásokat.
- Megkeresheti és kiválaszthatja a rács egyik országát, hogy nagyítani lehessen a térképen található helyet. A Térkép **kezdőlapjának** kiválasztásával visszaállíthatja az eredeti nézetet.

## <a name="page-visits-versus-unique-visitors-trend"></a>Oldal-látogatások és egyedi látogatói trend

Az alábbi oszlopok a havi oldal meglátogatások számát jelölik, amelyek az Y tengelyen jelennek meg (tengely a diagram bal oldalán). A trendvonal az egyedi látogatók havi trendjét jelöli, amely a másodlagos Y tengelyen (a diagram jobb oldalán található tengely) jelenik meg, a kirakatokban közzétett ajánlatokhoz: az Azure Marketplace és a AppSource.

![A partneri központ bepillantást nyerhet az egyedi látogatói trendekkel szemben](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>A CTAs-szel rendelkező egyedi látogatók és a művelet meghívása

A halmozott oszlopok a művelet (CTA) által lebontott havi hívásokat jelentik, amelyeket a CTA-típusok (**azonnali letöltés**, **Kapcsolatfelvétel**és **ingyenes próbaverzió**) alapján rajzolnak, és az Y tengelyen (a lap bal oldalán található tengely) ábrázolják. A trendvonal a CTAs rendelkező egyedi látogatók havi trendjét jelöli, amely a másodlagos Y tengelyen (a diagram jobb oldalán található tengely) jelenik meg, az Azure Marketplace-en és a AppSource-ben közzétett ajánlatokban.

![A partner Center-elemzések a CTAs-vel való egyedi látogatók ellen](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Oldalak látogatásai és az ajánlatok műveletre irányuló hívások

A külső tortadiagram a piactéren közzétett és a szűrőben kiválasztott ajánlatok alapján jeleníti meg az **oldalakon végzett látogatások** részletezését. A belső diagram az azonos ajánlatokra vonatkozó műveletek lebontására irányuló **hívásokat** jelöli.

![A partneri központ bepillantást nyerhet, és meghívja az ajánlatokat](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>A művelet százalékos trendjeinek meghívása

A **művelet százalékos trendjeinek meghívása** a piactéren közzétett ajánlatok CTA-százalékos arányát mutatja be. CTA% = (CTAs/oldal-látogatások) * 100.

![A partner Center-elemzések meghívása a művelet százalékos trendjeire](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Az átirányítási tartományok által felkeresett és kezdeményezett műveletek

Az alábbi gráf a legfontosabb 50 hivatkozó tartományokat mutatja be. Egy adott hivatkozó tartomány kiválasztásakor a diagramon a jobb oldalon található oldal-látogatások és a műveletekre irányuló hívások havi trendje látható.

![A partneri központ bepillantást nyerhet az átirányítási tartományok és kampányok által végrehajtott műveletekre](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace-információk részleteinek táblázata

Ez a táblázat a látogatások oldalának listáját, valamint a kiválasztott ajánlatok műveletének meghívását dátum szerint rendezve jeleníti meg.

![A partner Center-információk részleteinek táblázata](./media/insights-details-page.png)

- Az adatokat CSV-fájlba lehet kibontani, ha a rekordok száma kisebb, mint 1000.
- Ha a rekordok száma meghaladja a 1000-et, az exportált adatok aszinkron módon lesznek elhelyezve a letöltési oldalon a következő 30 napban.
- A szűrők alkalmazhatók az Önt érdeklő információk megjelenítésére. Az adathalmazok az ajánlatok nevei és a kampányok neve alapján szűrhetők.  

## <a name="next-steps"></a>Következő lépések

- A partner Center kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [a partner Center kereskedelmi piacának elemzései](./analytics.md).
- Az ajánlathoz tartozó Piactéri tevékenységet összefoglaló összesített adatok grafikonok, trendek és értékek esetében lásd: [az összefoglaló irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](./orders-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](./usage-dashboard.md).
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket, lásd [a kereskedelmi piactér elemzésének ügyfél-irányítópultját](./customer-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a AppSource található ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a [kereskedelmi piactér elemzésének minősítések és értékelések irányítópultját](./ratings-reviews.md)
- A kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekre, valamint az adatkifejezések átfogó szótárát lásd: [Gyakori kérdések és terminológia a kereskedelmi piactér elemzéséhez](./faq-terminology.md).
