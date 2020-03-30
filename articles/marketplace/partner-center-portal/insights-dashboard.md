---
title: Marketplace Insights irányítópult a Kereskedelmi piactér elemzésében a Partnerközpontban
description: A marketplace-webanalitika összegzése, amely lehetővé teszi a közzétevők számára az ügyfelek elkötelezettségének mérését az AppSource és az Azure Marketplace kirakataiban.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: a547ced9df98298361360ecab88036599cd86027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275849"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Marketplace Insights irányítópult a Kereskedelmi piactér elemzésében

Ez a cikk a Partnerközpont Marketplace Insights irányítópultjáról nyújt tájékoztatást. Ez az irányítópult a marketplace-webanalitika összegzését jeleníti meg, amely lehetővé teszi a közzétevők számára, hogy mérjék az ügyfelek elkötelezettségét a marketplace-kirakatokban felsorolt termékadattárlapokon: AppSource és Azure Marketplace.

## <a name="marketplace-insights-dashboard"></a>Marketplace Insights irányítópult

A **Partnerközpont Marketplace Insights irányítópultjának** eléréséhez nyissa meg az **[Elemzés lapot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a Kereskedelmi piactér csoportban.

A következő elemek grafikus ábrázolásait tekintheti meg:  

- [Marketplace Insights összefoglaló](#marketplace-insights-summary)
- [Oldallátogatások földrajzi adatok szerint](#page-visits-by-geography)  
- [Oldallátogatások és egyedi látogatók tendenciája](#page-visits-versus-unique-visitors-trend)
- [Felhívás az egyedi látogatókkal szemben cta-kkal](#call-to-action-versus-unique-visitors-with-ctas)
- [Oldallátogatások és felhívás ajánlatok szerint](#page-visits-and-calls-to-action-by-offers)
- [Cselekvésre szólító százalékos trend](#call-to-action-percentage-trend)
- [Oldallátogatások és cselekvésre való felhívások hivatkozási tartományok szerint](#page-visits-and-calls-to-action-by-referral-domains)
- [Marketplace Insights részletek táblázat](#marketplace-insights-details-table)

>[!NOTE]
> Az elemzési terminológia részletes meghatározását a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológiák](./faq-terminology.md)című témakörben található.

### <a name="insights-dashboard-layout"></a>Az Insights irányítópult-elrendezése

A Marketplace-mutatókat többféleképpen tekintheti meg:

- Kirakatlapok
- Oldalszűrők
- Dátumszűrők

**Kirakati lapok:** Az AppSource & Azure Piactér lapon külön tekintheti meg az ajánlatok metrikákat. Válassza ki az ajánlat(oka)t a jobb oldali ajánlat legördülő listából a kiválasztott ajánlat(ok) metrikáinak vizualizációjának megtekintéséhez. Alapértelmezés szerint az összes ajánlat ki van jelölve.

![Partner Center Insights irányítópult-ajánlatlegördülő lista](./media/insights-offer-dropdown.png)

**Insights oldalszűrők:** Ezek a szűrők az ajánlat (termékinformációs oldal) szintjén kerülnek alkalmazásra. A megtekinteni kívánt feltételekhez több szűrőt is kiválaszthat. Ez a szűrő a Marketplace Insights teljes szakaszára vonatkozik, beleértve a diagramokat és a részleteket is.

![Partnerközpont elemzési adatainak irányítópult-szűrői](./media/insights-page-filter.png)

- Az ajánlatnevek csak a kiválasztott dátumtartományban oldallátogatásokat tartalmazó ajánlatokesetében jelennek meg.  
- Az alapértelmezett beállítás az "Összes" az egyes szűrési beállításokhoz
- Az alkalmazott szűrők a kiválasztott beállítások (kiválasztás) számát jelenítik meg. Az alkalmazott szűrők nem jelennek meg az alapértelmezett "Mind" beállításnál.

![A Partner központ elemzési szűrői alkalmazva](./media/insights-page-filter-two.png)

**Insights dátumszűrői:** Ez a szűrő a teljes Marketplace Insights szakaszra vonatkozik. A szűrők tartalmazhatnak előre meghatározott dátumtartományokat vagy egyéni dátumtartományokat.

![Partnerközpont elemzési dátumszűrői](./media/insights-date-range.png)

## <a name="marketplace-insights-summary"></a>Marketplace Insights összefoglaló

A Marketplace-elemzések összegzése szakasz megjeleníti az **oldallátogatások,** **a cselekvésre felhívások**és az **Egyedi látogatók** számát a kiválasztott dátumtartományhoz.

### <a name="page-visits"></a>Oldallátogatások

Ez a szám a kiválasztott dátumtartomány ajánlati oldalán (termékadatlapon) található különböző felhasználói munkamenetek számát jelöli. A piros/zöld százalékos mutató az oldallátogatások növekedési %-át jelenti. A trenddiagram az oldallátogatások havi számát mutatja.

### <a name="unique-visitors"></a>Egyedi látogatók

Ez a szám az oldalszűrőben kiválasztott ajánlat(ok) kiválasztott dátumtartományában lévő látogatók számát jelöli. Az a látogató, aki egy vagy több termékinformációs oldalt látogatott meg, egy egyedi látogatónak számít.

### <a name="call-to-action"></a>Cselekvésre való felhívás

Ez a szám az **Call to Action** ajánlati oldalon (termékinformációs oldalon) végrehajtott felhívásgombkattintások számát jelöli. **A cselekvésre irányuló hívásokat** a Rendszer a **Get it now**, Az ingyenes **próbaverzió**, **a Kapcsolatfelvétel**és a **Tesztmeghajtó** gombok kiválasztásakor számolja.

![Partnerközpont elemzési felhívása műveletösszehívása](./media/insights-summary.png)

## <a name="page-visits-by-geography"></a>Oldallátogatások földrajzi adatok szerint

Az alábbi hőtérkép az **oldallátogatások**, **a cselekvésre felhívások**és az Egyedi látogatók számát jeleníti meg az **ügyfélország szerint**. A magasabb oldallátogatásokat sötétebb térképszínek, az alacsonyabb oldallátogatásokat pedig a világosabb térképszínek jelölik.

![PartnerKözpont-elemzés földrajzi eloszlása](./media/insights-geography.png)

A hőtérkép a következő képességeket tartalmazza:

- A hőtérkép egy kiegészítő rács megtekintéséhez részleteit **oldal látogatások**, **cselekvésre felhívások** és egyedi **látogatók** egy adott helyen; szükség esetén ránagyíthat egy adott helyre.  
- **Az országok elosztva** azon országok száma, ahonnan az ügyfelek oldallátogatásokat jelentettek a kiválasztott dátumtartományban.
- Kereshet és kiválaszthat egy országot a rácsban a térképen lévő hely nagyításához. Visszatérés az eredeti nézetre a térkép **Kezdőlap** parancsára kattintva.

## <a name="page-visits-versus-unique-visitors-trend"></a>Oldallátogatások és egyedi látogatók tendenciája

Az alábbi oszlopok a havi oldallátogatások számát jelölik, amelyek az Y tengelyen (a diagram bal oldalán lévő tengelyen) jelennek meg. A trendvonal az egyedi látogatók havi trendjét jelöli, amely a másodlagos Y tengelyen (a diagram jobb oldalán lévő tengelyen) jelenik meg a kirakatokban közzétett ajánlatokhoz: Azure Marketplace és AppSource.

![Partnerközpont elemzési oldallátogatásai az egyedi látogatók trendjével szemben](./media/insights-page-vists-unique-visitors.png)

## <a name="call-to-action-versus-unique-visitors-with-ctas"></a>Felhívás az egyedi látogatókkal szemben cta-kkal

A halmozott oszlopok a havi cselekvésre irányuló felhívásokat (CTA) jelölik, amelyek CTA-típusok szerint vannak lebontva (**Get it now**, **Kapcsolat,** és **Ingyenes próbaverzió**), és az Y tengelyen ábrázolva (az oldal bal oldalán lévő tengely). A trendvonal az Egyedi látogatók havi trendjét képviseli a CTA-kkal, amely a másodlagos Y tengelyen (a diagram jobb oldalán lévő tengelyen) jelenik meg az Azure Marketplace-en és az AppSource-ban közzétett ajánlatokhoz.

![A Partnerközpont elemzési adatai cselekvésre szólítanak fel a CTA-kkal rendelkező egyedi látogatókkal szemben](./media/insights-call-to-action-unique-visitors.png)

## <a name="page-visits-and-calls-to-action-by-offers"></a>Oldallátogatások és felhívások ajánlatok szerint

A külső kördiagram az **oldallátogatások** bontását mutatja a piactéren közzétett és a szűrőben kiválasztott ajánlatok alapján. A belső diagram az ugyanazon ajánlatok **cselekvésre felhívások** bontását jelöli.

![A Partnerközpont elemzési adatainak oldalai látogatások és felhívások ajánlatok szerint](./media/insights-page-visits-and-cta-by-offer.png)

## <a name="call-to-action-percentage-trend"></a>Cselekvésre szólító százalékos trend

A **cselekvésre való felhívás százalékos tendenciája** a piacon közzétett ajánlatok CTA-százalékos arányát mutatja be. CTA % = (CTA/oldallátogatások) * 100.

![Partnerközpont elemzési felhívása cselekvésszázalékos trendre](./media/insights-call-to-action-percentage-trend.png)

## <a name="page-visits-and-calls-to-action-by-referral-domains"></a>Oldallátogatások és cselekvésre való felhívások hivatkozási tartományok szerint

Az alábbi grafikon bemutatja a top 50 átirányítási domaint. Egy adott hivatkozási tartomány kiválasztása az oldallátogatások és a cselekvésre való felhívások havi trendjét mutatja a jobb oldali diagramon.

![A Partnerközpont elemzési adatainak oldalai meglátogatása és cselekvésre való felhívása hivatkozási tartományok és kampányok szerint](./media/insights-page-visits-call-to-actions.png)

## <a name="marketplace-insights-details-table"></a>Marketplace Insights részletek táblázat

Ez a táblázat a kijelölt ajánlatok dátum szerint rendezve lévő oldallátogatások és cselekvésre irányuló felhívások listáját tartalmazza.

![Partnerközpont elemzési adataitáblázat](./media/insights-details-page.png)

- Az adatok csv-fájlba nyerhetők ki, ha a rekordok száma kevesebb, mint 1000.
- Ha a rekordok száma több mint 1000, az exportált adatok aszinkron módon kerülnek a letöltési oldalra a következő 30 napra.
- Szűrők alkalmazhatók az Önt érdeklő adatok megjelenítésére. Az adatok az ajánlatnevek és a kampánynevek szerint szűrhetők.  

## <a name="next-steps"></a>További lépések

- A Partnerközpont kereskedelmi piactéren elérhető elemzési jelentéseinek áttekintését [a Partnerközpont Kereskedelmi piactér Analytics szolgáltatása című témakörben találja.](./analytics.md)
- Az ajánlat marketplace-tevékenységét összegző összesített adatok grafikonjait, trendjeit és értékeit lásd: [Összefoglaló irányítópult a Kereskedelmi piactér elemzésében.](./summary-dashboard.md)
- A megrendelésekgrafikus és letölthető formátumban című témakörben talál további információt a [Rendelések irányítópultja a Kereskedelmi piactér elemzésében.](./orders-dashboard.md)
- A virtuális gép (VM) kínál használati és forgalmi díjas számlázási metrikák, lásd: [Használati irányítópult a Kereskedelmi Piactér analytics.](./usage-dashboard.md)
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket is, az [Ügyfél irányítópultja a Kereskedelmi piactér elemzésében](./customer-dashboard.md)című témakörben talál.
- Az elmúlt 30 nap letöltési kérelmeinek listáját a [Letöltések irányítópultja a Kereskedelmi piactér elemzésében című témakörben található.](./downloads-dashboard.md)
- Az Azure Marketplace-en és az AppSource-on az ajánlatokra vonatkozó ügyfél-visszajelzések összevont nézetét a [Kereskedelmi piactér elemzése minősítések és vélemények irányítópultja című témakörben tekintheti meg.](./ratings-reviews.md)
- A Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekről és az adatkifejezések átfogó szótáráról a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológiák](./faq-terminology.md)című témakörben található.
