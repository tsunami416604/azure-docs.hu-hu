---
title: Partnerközpont-rendelések irányítópult a Kereskedelmi Piactér elemzésében
description: Ismerje meg, hogyan érheti el a marketplace-ajánlati megbízásairól szóló analitikus jelentéseket grafikus és letölthető formátumban.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: d0db6553a774a69bb8a55538cbd2b4a333be9316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281408"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Rendelések irányítópult a Kereskedelmi piactér elemzésében

Ez a cikk a Partnerközpont **Rendelések irányítópultjáról** nyújt tájékoztatást. Ez az irányítópult grafikus és letölthető formátumban jeleníti meg a rendelésekkel kapcsolatos információkat.

A Partnerközpont elemzési eszközeinek **Rendelések irányítópultjának** eléréséhez nyissa meg az **[Elemzés irányítópultot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a Kereskedelmi piactér csoportban.

>[!NOTE]
> Az elemzési terminológia részletes meghatározását a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológiák](./faq-terminology.md)című témakörben található.

## <a name="orders-dashboard"></a>Rendelések irányítópult

**Az Elemzés** menü **Rendelések irányítópultja** megjeleníti az összes SaaS-ajánlat aktuális rendelését. A következő elemek grafikus ábrázolásait tekintheti meg:

- [Rendelés összegzése](#order-summary)
- [Rendelések földrajzi hely szerint](#orders-by-geography)
- [Megrendelések ajánlatok szerint](#orders-by-offers)
- [Rendelési trend oldalanként és ülőhelyenként](#orders-trend-per-site-versus-per-seat)
- [Rendelések a ski-k szerint](#orders-by-skus)
- [Megrendelések és ülőhelyek trend](#orders-and-seats-trend)
- [Rendelés részletei tábla](#order-details-table)

> [!NOTE]
> Különbségek vannak a között, ahogyan az elemzési jelentések megjelennek a Cloud Partner Portal (CPP) portálon és az új Kereskedelmi piactér program a Partnerközpontban. Az egyik konkrét módszer az, hogy a CPP **Eladói elemzési adatai** nak van **megrendelései & használat** lapja, amely a használatalapú ajánlatok és a nem használatalapú ajánlatok adatait jeleníti meg. A Partnerközpontban a **Rendelések** lapon külön lap található a SaaS-ajánlatok számára.

## <a name="order-dashboard-details"></a>Irányítópult részleteinek megrendelése

Ez a szakasz részletesebben ismerteti az elemzési jelentéseket.

### <a name="order-summary"></a>Rendelés összegzése

A Rendelés összegzése szakasz megjeleníti az összes megvásárolt rendelés (a visszavont rendelések kivételével), a visszavont rendelések és az ülőhelyek számát.

Az Összes rendelés melletti százalékos érték a kiválasztott dátumtartomány ban lévő növekedés összegét jelöli.

![Partnerközpont – Rendelés elemzése összegzése](./media/order-summary.png)

- A felfelé mutató zöld háromszög pozitív növekedési tendenciát jelez.
- A lefelé mutató piros háromszög az előző hónaphoz képest negatív növekedési tendenciát jelez.
- A növekedési trendeket mikrosávos grafikonok képviselik. Az egyes hónapok értékét úgy jelenítheti meg, hogy a diagram oszlopai fölé viszi az egérmutatót.
- A visszavont rendelések a korábban megvásárolt, majd a kiválasztott dátumtartományban visszavont rendelések száma.
- Az ülőhelyek száma a kiválasztott dátumtartományban létrehozott ülőhelyek száma.

### <a name="orders-by-geography"></a>Rendelések földrajzi hely szerint

A **Rendelések földrajzi** hőtérkép szerint megjeleníti a rendelések számát a világtérképen, és megjeleníti az Ügyfél országa alapján leképezett ülőhelyeket. Ez a hőtérkép ugyanúgy működik, mint az **[Ügyfél földrajzi hőtérkép szerint.](./customer-dashboard.md#customer-by-geography)**

![Partnerközpont Rendelések elemzése földrajzi hely szerint](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Megrendelések ajánlatok szerint

A **Rendelések ajánlat szerint** fánkdiagram a rendeléseket (beleértve a visszavont rendeléseket is) az ajánlatnevük szerint rendezi.

- A felső ajánlatok megjelennek a grafikonon, a többi ajánlat pedig "Rest All" (Összes pihenés) néven van csoportosítva.
- A jelmagyarázatban kiválaszthatja az okat, hogy csak azok jelenjenek meg a grafikonon.
- Ha a grafikonon egy szelet fölé viszi a mutatót, akkor az ajánlat megrendeléseinek száma és százalékos aránya az összes ajánlatban lévő rendelések teljes számához képest.
- Az **ajánlatok alapján idikta trend** havi növekedési trendeket jelenít meg. A hónap oszlop a rendelések számát jelöli ajánlatnév szerint. A vonaldiagram a z tengelyen ábrázolt növekedési százalékos trendet jeleníti meg.
- A diagram tetején lévő csúszkával jobbra és balra görgethet az x tengely mentén, és adott adatpontokra összpontosíthat.
- A trenddiagramot úgy jelenítheti meg, hogy kijelöl egy adott elemet a jelmagyarázaton.
- Kiválaszthatja a **visszavont rendelések**trendjeinek és adatainak megjelenítését is. A grafikon ugyanúgy fog működni, mint a **rendelések ajánlatdiagram szerint.**

### <a name="orders-trend-per-site-versus-per-seat"></a>Rendelések Trend webhelyenként és ülőhelyenként

A **webhelyenkénti és az ülőhelyenkénti** fánkdiagram a saas-i és az ülőhelyenkénti SaaS-rendelések ügyfélenkénti bontását mutatja (ez a diagram tartalmazza a törölt rendeléseket). Az oszlopdiagram az ügyfelek által vásárolt SaaS-és ülőhelyenkénti SaaS-rendelések trendjét mutatja (ez a diagram tartalmazza a visszavont rendeléseket).

### <a name="orders-by-skus"></a>Rendelések a ski-k szerint

A **Rendelések termékváltozatonként** diagram a rendelések trendjét mutatja a Raktározási egység (SKU) szintjén az összes ajánlatesetében (ez magában foglalja a visszavont rendeléseket is). A fánkdiagram az első öt Termékváltozat-rendelés bontását mutatja, az oszlopdiagram pedig az első öt termékváltozat rendeléseinek trendjét.

### <a name="orders-and-seats-trend"></a>Megrendelések és ülések trend

A **megrendelések és helyek trendtáblázata** a legtöbb megrendeléssel rendelkező 50 legjobb ajánlatot mutatja be. Ezek a ranglistán jelennek meg, és a legmagasabb rendelésszám és a rendelési százalék szerint vannak rendezve.

- **Rendelések ski-k szerint :** Válasszon egy ajánlatot a diagram első öt sk-jának rendelésszámának megtekintéséhez.
- **Helyek a suk-ok szerint**: Az első öt suk-ra vonatkozó ülőhelyek havi trendje. Ha a kiválasztott ajánlat nem ülőhelyenkénti ajánlat, akkor ezen a területdiagramon nem jelennek meg adatok.

### <a name="canceled-orders-by-offers"></a>Visszavont ajánlatok általi rendelések

A **Visszavont rendelések kördiagram alapján** az összes visszavont rendelést az ajánlatnevük szerint rendezi. A felső ajánlatok a grafikonon jelennek meg, a többi ajánlat pedig "Rest All" (Összes enszimény) néven van csoportosítva. A jelmagyarázatban megjelenítendő ajánlatokat kiválaszthatja a grafikonon.

- Ha a grafikonon egy szelet fölé viszi a mutatót, megjelenik a rendelések száma és a kiválasztott ajánlat százalékos aránya az összes ajánlatrendeléseinek teljes számához képest.
- Az oszlopdiagram havi trendeket jelenít meg. Az oszlopok a visszavont rendelések számát jelölik ajánlatnév szerint. A diagram tetején lévő csúszkával jobbra és balra görgethet az x tengely mentén, és adott adatpontokra összpontosíthat. A trenddiagramot úgy jelenítheti meg, hogy kijelöl egy adott elemet a jelmagyarázaton.

### <a name="order-details-table"></a>Rendelés részletei tábla

A Rendelés részletei tábla az 1000 legfontosabb rendelés számozott listáját jeleníti meg az akvizíció dátuma szerint rendezve.

- A rács minden oszlopa rendezhető.
- Az adatok kinyerhetők egy TSV fájlba, ha a rekordok száma kevesebb, mint 1000.
- Ha a rekordok száma 1000-nél több, az exportált adatok aszinkron módon kerülnek a letöltési oldalra a következő 30 napra.
- Szűrők alkalmazhatók a **Rendelés részletei táblára,** hogy csak azÖnt érdeklő adatok jelenjenek meg. Az adatok ország, Azure-licenctípus, piactéri licenctípus, ajánlattípus, rendelésállapota, ingyenes nyomvonalak, piactér-előfizetés-azonosító, ügyfélazonosító és vállalatnév szerint szűrhetők.

#### <a name="orders-page-filters"></a>Rendelések oldalszűrői

Ezek a szűrők az oldal szintjén kerülnek alkalmazásra.

Több szűrőt is kijelölhet a megtekinteni kívánt feltételek és a **Részletes rendelési adatok** rácsában/exportálása korként megjelenítő adatok diagramjának megjelenítéséhez. A szűrők a rendelési lap jobb felső sarkában kiválasztott adattartományhoz kinyert adatokra vonatkoznak.

- Az ajánlattípusok és az ajánlatnevek csak a kiválasztott dátumtartományban rendelésekkel rendelkező ajánlatokhoz vannak felsorolva. A listában szereplő ajánlatnevek a listában kiválasztott ajánlattípusokesetében jelennek meg.
- Az alkalmazott szűrők az egyes kiválasztott szűrők összes mutatóit jelenítik meg az egyes kiválasztott szűrőkön belül. Az alkalmazott szűrők nem jelennek meg, ha az alapértelmezett kijelölés van kiválasztva.
- Ha az **Összes** lehetőség van kiválasztva az egyik legördülő listához, akkor a kiválasztott oldal összes mutatója összesítve lesz. Például: "Minden" az ajánlattípusok szűrő beállítás azt jelenti, hogy az összes ajánlattípusok vannak kiválasztva. Ez a legördülő listák alapértelmezett kijelölése. Az alkalmazott szűrők megjelenítése nem jelenít meg **semmit,** ha az Összes ki van jelölve.
- **Többszörös értékválasztás:** Az oldal összes mutatója összesítve jelenik meg a legördülő lista összes kiválasztottja esetében. Ha több kijelölés történik, az alkalmazott szűrő az összes megadott kijelölés számát jeleníti meg. Lásd az alábbi képet referencia.

    ![Partnerközpont A rendelés elemzése több értékkel a szűréshez alkalmazva](./media/filters-applied.png)

- **Egyetlen érték választás:** Ha egy érték van kiválasztva, az alkalmazott szűrő a kijelölt szűrő számát jeleníti meg. Lásd az alábbi képet a referencia.

     ![Partnerközpont A rendelés elemzése egyetlen értékkel a szűrőre alkalmazva](./media/filters-applied-single.png)

## <a name="next-steps"></a>További lépések

- A Partnerközpont kereskedelmi piactéren elérhető elemzési jelentéseinek áttekintését [a Partnerközpont Kereskedelmi piactér Analytics szolgáltatása című témakörben találja.](./analytics.md)
- Az ajánlat marketplace-tevékenységét összegző összesített adatok grafikonjait, trendjeit és értékeit [lásd: Összefoglaló irányítópult a Kereskedelmi piactér elemzésében.](./summary-dashboard.md)
- A virtuális gép (VM) kínál használati és forgalmi díjas számlázási metrikák, lásd: [Használati irányítópult a Kereskedelmi Piactér analytics.](./usage-dashboard.md)
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket is, az [Ügyfél irányítópultja a Commercial Marketplace analytics című témakörben](./customer-dashboard.md)talál.
- Az elmúlt 30 nap letöltési kérelmeinek listáját a [Kereskedelmi piactér elemzése Letöltések irányítópultja című témakörben található.](./downloads-dashboard.md)
- Az Azure Marketplace-en és az AppSource-on az ajánlatokra vonatkozó ügyfél-visszajelzések összevont nézetét a [Kereskedelmi piactér elemzése minősítések és vélemények irányítópultja című témakörben tekintheti meg.](./ratings-reviews.md)
- A Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekről és az adatkifejezések átfogó szótáráról a [Kereskedelmi piactér elemzésével kapcsolatos gyakori kérdések és terminológiák](./faq-terminology.md)című témakörben található.
