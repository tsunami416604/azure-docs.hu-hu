---
title: A partner Center Orders irányítópultja a kereskedelmi piactér elemzésében, Microsoft AppSource és az Azure Marketplace-en
description: Megtudhatja, hogyan érheti el a kereskedelmi Piactéri ajánlatokra vonatkozó analitikai jelentéseket grafikus és letölthető formában.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/22/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d5adc1bfe19de48568d0e77bb488bea0e5a02818
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327378"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>A Rendelések irányítópult a kereskedelmi piactér-elemzésekben

Ez a cikk a partner Center **orders (megrendelések) irányítópultján** tartalmaz információkat. Ez az irányítópult grafikus és letölthető formátumban jeleníti meg a rendelésekkel kapcsolatos információkat.

A partner Center Analytics-eszközök **orders (megrendelések) irányítópultjának** megnyitásához nyissa meg az **[elemzés irányítópultot](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a kereskedelmi piactéren.

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [a kereskedelmi piactér Analytics szolgáltatással kapcsolatos gyakori kérdések és fogalmak](./faq-terminology.md).

## <a name="orders-dashboard"></a>Rendelések irányítópult

Az **elemzés** menü **orders (megrendelések) irányítópultja** megjeleníti az összes SaaS-ajánlat aktuális rendeléseit. A következő elemek grafikus ábrázolásait tekintheti meg:

- [Megrendelés összegzése](#order-summary)
- [Megrendelések földrajz szerint](#orders-by-geography)
- [Megrendelések ajánlatok szerint](#orders-by-offers)
- [Orders trend per site/Seat](#orders-trend-per-site-versus-per-seat)
- [Megrendelések csomagok szerint](#orders-by-plans)
- [Megrendelések és ülőhelyek trendje](#orders-and-seats-trend)
- [Rendelés részletei tábla](#order-details-table)

A megrendelések létrehozása és a partner Centerben történő jelentéskészítés közötti maximális késés 48 óra.

## <a name="order-dashboard-details"></a>Rendelési irányítópult részletei

Ez a szakasz részletesebben ismerteti az elemzési jelentéseket.

### <a name="order-summary"></a>Megrendelés összegzése

Az Order Summary (megrendelés összegzése) szakasz az összes megvásárolt megrendelés (a megszakított megrendelések kivételével), a megszakított megrendelések és a helyek számát jeleníti meg.

Az összes rendelés melletti százalékos érték a kijelölt dátumtartomány növekedésének mértékét jelöli.

![A partneri központ elemzésének sorrendje – összefoglalás](./media/order-summary.png)

- A felfelé mutató zöld háromszög pozitív növekedési trendet jelez.
- Egy lefelé mutató piros háromszög jelzi az előző hónaphoz viszonyított negatív növekedési tendenciát.
- A növekedési trendeket a Micro Bar graphs jelképezi. Az egyes hónapok értékét a diagramon belüli oszlopok fölé húzva jelenítheti meg.
- A megszakított megrendelések a korábban megvásárolt és a kijelölt dátumtartomány során megszakított megrendelések száma.
- A helyek a kiválasztott dátumtartomány során létrehozott helyek száma.

### <a name="orders-by-geography"></a>Megrendelések földrajz szerint

A **földrajzi hőség által megrendelések szerint** a Térkép a megrendelések számát jeleníti meg a globális térképen, és megjeleníti az ügyfél országa/régiója alapján leképezett helyeket. Ez a hő ugyanúgy működik, mint az **[ügyfél földrajzi hő](./customer-dashboard.md#customer-by-geography)**.

![A partner Center elemzi a rendeléseket földrajz szerint](./media/orders-by-geography.png)

### <a name="orders-by-offers"></a>Megrendelések ajánlatok szerint

A **Orders by ajánlatok** a fánk-gráfok szerint rendezik a rendeléseket (beleértve a megszakított rendeléseket is) az ajánlat nevük alapján.

- A legfontosabb ajánlatok a gráfban jelennek meg, a többi ajánlat pedig "Rest all"-ként van csoportosítva.
- A jelmagyarázatban megadott ajánlatokat kiválasztva csak a gráfban található ajánlatokat jelenítheti meg.
- Ha a diagramon egy szelet fölé viszi az egérmutatót, a megrendelések száma és az ajánlat százalékos aránya az összes ajánlathoz képest teljes számú rendelésre hasonlít.
- A **Orders by ajánlatok trend** a havi havi növekedési trendeket jeleníti meg. A hónap oszlop a megrendelések számát jelöli az ajánlat neve alapján. A diagram egy z-tengelyen ábrázolt százalékos növekedési arányt jeleníti meg.
- A diagram tetején található csúszka segítségével jobbra és balra görgetheti az x tengelyt, és az adott adatpontokra koncentrálhat.
- A trend diagramot egy adott elem kiválasztásával jelenítheti meg a jelmagyarázatban.
- Dönthet úgy is, hogy a **megszakított megrendelésekhez**kapcsolódó trendeket és az adatfolyamatokat is megjeleníti. A gráf ugyanúgy fog működni, mint a **Orders by ajánlatok** gráf.

### <a name="orders-trend-per-site-versus-per-seat"></a>Orders trend per site/Seat

A **helyek közötti és az egyes ülések** közötti fánk-diagramok az ügyfelek által megvásárolt, munkaállomásonkénti SaaS-és az ügyfél által megvásárolt SaaS-megrendelések részletezését jelentik (ez a diagram a megszakított rendeléseket is tartalmazza). Az oszlopdiagram a site SaaS és az ügyfelek által vásárolt munkaállomásonkénti SaaS-megrendelések trendjét jelöli (ez a diagram a megszakított rendeléseket is tartalmazza).

### <a name="orders-by-plans"></a>Megrendelések csomagok szerint

A **Orders by Plans (megrendelések a tervek szerint** ) diagram az összes ajánlat (Ez magában foglalja a megszakított megrendelések) megrendelésének trendjét mutatja. A fánk diagram az első öt csomag megrendelésének részletezését jelöli, az oszlopdiagram pedig az első öt csomag rendeléseinek tendenciáját jelöli.

### <a name="orders-and-seats-trend"></a>Megrendelések és ülőhelyek trendje

A **Orders and seats trend** diagram a legmagasabb számú rendeléssel rendelkező első 50-ajánlatokat mutatja be. Ezek a ranglistán jelennek meg, és a legmagasabb rendelési szám és a sorrend százaléka szerint rendezve jelennek meg.

- **Megrendelések csomagok szerint** – válasszon egy ajánlatot a diagram első öt tervéhez tartozó sorrendek részletezésének megtekintéséhez.
- **Ülőhelyek csomagok szerint** – az első öt csomaghoz tartozó ülések havi trendje. Ha a kiválasztott ajánlat nem egy munkaállomásonkénti ajánlat, itt nem fog megjelenni adatmennyiség.

### <a name="canceled-orders-by-offers"></a>Megszakított megrendelések ajánlatok szerint

A **megszakított Orders by ajánlatok** kördiagrama az összes megszakított rendelést az ajánlat nevük alapján rendezi. A legfontosabb ajánlatok a gráfban jelennek meg, a többi ajánlat pedig "Rest all"-ként van csoportosítva. A jelmagyarázatban a diagramon megjelenő konkrét ajánlatokat is kiválaszthatja.

- Ha a diagramon egy szelet fölé viszi a mutatót, a megrendelések száma és a kiválasztott ajánlat százalékos aránya a megrendelések teljes számával összehasonlítva jelenik meg az összes ajánlat között.
- Az oszlopdiagram hónapról hónapra eső trendeket jelenít meg. Az oszlopok az ajánlat neve alapján megszakított megrendelések számát jelölik. A diagram tetején található csúszka segítségével jobbra és balra görgetheti az x tengelyt, és az adott adatpontokra koncentrálhat. A trend diagramot egy adott elem kiválasztásával jelenítheti meg a jelmagyarázatban.

### <a name="order-details-table"></a>Rendelés részletei tábla

Az Order Details (megrendelés részletei) tábla a 1000 Top orders (beszerzési dátum szerint rendezve) számozott listát jeleníti meg.

- A rács minden oszlopa rendezhető.
- Az adatokat egy TSV-fájlba lehet kibontani, ha a rekordok száma kisebb, mint 1000.
- Ha a 1000-es számú rekordok száma, az exportált adatok aszinkron módon lesznek elhelyezve a letöltési oldalon a következő 30 napban.
- Alkalmazzon szűrőket az **Order details táblára** , hogy csak azokat az adatokat jelenítse meg, amelyekre kíváncsi. Szűrés ország/régió szerint, Azure-licenc típusa, kereskedelmi piactér licencelési típusa, ajánlat típusa, megrendelés állapota, ingyenes nyomvonalak, kereskedelmi piactér-előfizetés azonosítója, ügyfél-azonosító, és a vállalat neve.
- Mivel az Azure Marketplace-en vagy AppSource keresztül vásárolt SaaS-ajánlatok nem igényelnek Azure-előfizetést, a piactér-előfizetés azonosítója 00000000-0000-0000-0000-000000000000-ként fog megjelenni a **részletes megrendelések** adatterületen.

#### <a name="orders-page-filters"></a>Megrendelések oldal szűrőinek

Ezeket a szűrőket az oldal szintjén alkalmazza a rendszer.

Több szűrőt is kiválaszthat, hogy a diagramot a megtekinteni kívánt feltételekhez és a **részletes rendelési** adatrácsban/exportáláshoz megadott feltételeknek megfelelően jelenítse meg. A szűrők a megrendelések oldal jobb felső sarkában kiválasztott adattartományra kinyert adattartományon lesznek alkalmazva.

- Az ajánlat típusa és az ajánlat neve csak azokra a ajánlatokra van felsorolva, amelyekhez a kiválasztott dátumtartomány esetében rendeléseket rendelt. A listában szereplő ajánlatok nevei megjelennek a listában kiválasztott ajánlati típusoknál.
- Az alkalmazott szűrők megjelenítik az egyes kijelöléseken belüli összes mérőszámot az egyes kiválasztott szűrőknél. Az alkalmazott szűrők nem jelennek meg, ha az alapértelmezett kijelölés van kiválasztva.
- Ha az egyik legördülő lista valamelyikére van kiválasztva, akkor a kiválasztott oldalon **lévő összes metrika** összesítésre kerül. Például: az "all" az ajánlat típusainak szűrése beállítás azt jelenti, hogy az összes ajánlat típus ki van választva. Ez a legördülő listák alapértelmezett kiválasztása. Az alkalmazott szűrők képernyőn nem jelenik meg semmi, ha az **összes** ki van választva.
- **Több érték kiválasztása**: a lapon lévő összes metrika összesítve lesz a legördülő lista alatti összes beállítás esetében. Ha több beállítás is megtörtént, az alkalmazott szűrő megjeleníti az összes kiválasztott beállítás számát. Az alábbi képen megtekintheti a hivatkozást.

    ![A partner Center elemzési sorrendje több, szűrésre alkalmazott értékkel](./media/filters-applied.png)

- **Egyetlen érték kiválasztása**: Ha egy érték van kiválasztva, akkor az alkalmazott szűrő a kiválasztott szűrő számát jeleníti meg. Tekintse meg az alábbi képet a hivatkozáshoz.

     ![A partneri központ elemzési sorrendje egyetlen, a szűrőre alkalmazott értékkel](./media/filters-applied-single.png)

## <a name="next-steps"></a>További lépések

- A partner Center kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [a partner Center kereskedelmi piacának elemzései](./analytics.md).
- Az ajánlathoz tartozó Piactéri tevékenységet összefoglaló összesített adatok grafikonok, trendek és értékek esetében lásd: [az összefoglaló irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](./usage-dashboard.md).
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket, lásd [a kereskedelmi piactér elemzésének ügyfél-irányítópultját](./customer-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a AppSource található ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a [kereskedelmi piactér elemzésének minősítések és értékelések irányítópultját](./ratings-reviews.md)
- A kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekre, valamint az adatkifejezések átfogó szótárát lásd: [Gyakori kérdések és terminológia a kereskedelmi piactér elemzéséhez](./faq-terminology.md).
