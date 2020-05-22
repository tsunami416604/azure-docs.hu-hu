---
title: Használati irányítópult a Microsoft kereskedelmi piactér elemzésében
description: Ismerje meg, hogyan érheti el az összes virtuális gép használati és mért számlázási metrikákat. Nyissa meg a használati irányítópultot a partner Centerben a kereskedelmi piactéren.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: b60475c56b18e81b082e7552fec789b06adeff27
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746659"
---
# <a name="usage-dashboard-in-microsoft-commercial-marketplace-analytics"></a>Használati irányítópult a Microsoft kereskedelmi piactér elemzésében

Ez a cikk a partner Center használati irányítópultján tartalmaz információkat. Ez az irányítópult megjeleníti az összes virtuális gép használati és mért számlázási metrikáit két külön lapon: a virtuális gépek használatát és a mért számlázási használatot.

A használati irányítópult eléréséhez nyissa meg az **[elemzés](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** irányítópultot a **kereskedelmi piactéren**.

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [a kereskedelmi piactér Analytics szolgáltatással kapcsolatos gyakori kérdések és fogalmak](./faq-terminology.md).

## <a name="usage-dashboard"></a>Használat irányítópult

A használati irányítópult az összes virtuális gép (VM) metrikáját jelöli, amely a használatot és a mért számlázási használatot biztosítja. Ezek a metrikák két külön lapon találhatók: virtuális gépek használata és mért számlázás használata.

A VM-használat lapon a következő elemek grafikus ábrázolásai láthatók:

- [Használat összegzése](#usage-summary)
- [Használat földrajz szerint](#usage-by-geography)
- [Használat ajánlatok szerint](#usage-by-offers)
- [Használati trend ajánlatok és SKU-i alapján](#usage-trend-by-offers-and-skus)
- [Használat ajánlat típusa szerint](#usage-by-offer-type)
- [Használat VM-méret szerint](#usage-by-vm-size)
- [Használat értékesítési csatornák alapján](#usage-by-sales-channel)
- [Részletes használati adatok](#detailed-usage-data)

> [!NOTE]
> Az elemzési jelentések a Cloud Partner Portal (CPP) és a partner Centerben eltérő módon jelennek meg. A CPP-beli **értékesítői** elemzések egy Orders and használati lappal rendelkeznek, amely a használati és a nem használaton alapuló ajánlatok adatait jeleníti meg. A partner Centerben a használati metrikák külön lapon jelennek meg.

### <a name="usage-summary"></a>Használati adatok összegzése

A használat összegzése tábla az összes megvásárolt ajánlat ügyfél-használati idejét jeleníti meg.

- A normalizált használati idő a virtuálisgép-magok számának ([virtuálisgép-magok száma] x [órányi nyers használat]) fiókban normalizált használati órákat határozza meg. A "SHAREDCORE" jelölésű virtuális gépek a 1/6 (vagy 0,1666) értéket használják [virtuálisgép-magok száma] szorzóként.
- A nyers használati órák határozzák meg, hogy a virtuális gépek hány óra alatt futnak.
- A százalékos érték a kiválasztott dátumtartomány felhasználási növekedési változását jelöli ([utolsó hónap használat – első hónap használata])/az első hónap használata).
- A felfelé mutató zöld háromszögek növekedési változást jeleznek.
- A piros háromszög lefelé mutat, az előző hónaphoz viszonyítva negatív növekedési változást jelez.
- A Micro Bar gráfok havi értékeket képviselnek. Az egyes hónapok értékét a diagramon belüli oszlopok fölé húzva jelenítheti meg.

### <a name="usage-by-geography"></a>Használat földrajz szerint

A **földrajzi hő normalizált használata** az ügyfél országa/régiója szerint leképezett használati időt jeleníti meg. Az ország/régió színvariációja normalizált használati koncentrációt jelöl. A térképen a **Kezdőlap** gombra kattintva visszatérhet az eredeti nézethez.

### <a name="usage-by-offers"></a>Használat ajánlatok szerint

- Az **ajánlatok által normalizált használat szerint** a tortadiagram a normalizált használati órák részletezését jeleníti meg az ajánlatok alapján a kiválasztott dátumtartomány szerint. Az első öt ajánlat a gráfban jelenik meg, míg a REST az **összes** kategóriába van csoportosítva.
- A oszlopdiagram a kiválasztott dátumtartomány hónapról hónapra eső növekedési trendjét ábrázolja. A hónap oszlopai az ajánlatokban szereplő használati órákat jelölik az adott hónap legmagasabb használati idejével. A diagram a másodlagos Y tengelyen ábrázolt százalékos növekedési arányt ábrázolja.
- A diagram tetején található csúszka használatával görgessen jobbra az x tengely mentén, és/vagy az adott adatpontokra koncentrálhat.

### <a name="usage-trend-by-offers-and-skus"></a>Használati trend ajánlatok és SKU-i alapján

Ez a diagram az ajánlat kiválasztott SKU-pontjainak normalizált használatának tendenciáját jeleníti meg. Az ajánlat ranglistán a legmagasabb használatú, a használat óra szerint rendezett első 50-ajánlatok láthatók. Az SKU Leaderboard a legfelső 50 SKU-t jeleníti meg a kiválasztott ajánlat legmagasabb kihasználtságával.

### <a name="usage-by-offer-type"></a>Használat ajánlat típusa szerint

- Az **ajánlat típusa** tortadiagram használatával rendezi a használatot az ajánlat típusa szerint.
- A legfontosabb ajánlatok a diagramon jelennek meg, a többi ajánlat pedig "Rest-ként" van csoportosítva.
- A **trend** diagram a hónapról hónapra terjedő növekedési trendeket jeleníti meg. A hónap oszlop az adott hónapban leggyakrabban használt ajánlati típusok használatát jelöli.

### <a name="usage-by-vm-size"></a>Használat VM-méret szerint

Ez a diagram a kiválasztott virtuálisgép-méretek (max. 5) használati trendjét mutatja az összes ajánlat/SKU esetében. Az oszlopdiagram a kiválasztott virtuálisgép-méretek használati órájával van halmozva.

A ranglistán a legfontosabb 50-as virtuálisgép-méretek láthatók a legmagasabb használattal, és a használat óra szerint rendezve jelennek meg.

### <a name="usage-by-sales-channel"></a>Használat értékesítési csatornák alapján

- A Sales Channel kördiagram általi használat az értékesítési csatorna alapján rendezi a használatot.
- A legfontosabb értékesítési csatorna a legmagasabb szintű használattal jelenik meg a diagramon, a többi értékesítési csatorna pedig "Rest all"-ként van csoportosítva.
- A hónap oszlop az adott hónapban a Top Sales Channel általi használatot jelöli.
- A diagram funkciói megegyeznek a "használati ajánlatok" diagrammal

### <a name="detailed-usage-data"></a>Részletes használati adatok

A **használat részletei tábla** a legnépszerűbb 1000-használati rekordok számozott listáját jeleníti meg a használat alapján rendezve.

- A rács minden oszlopa rendezhető.
- Az adatokat CSV-fájlba lehet kibontani, ha a rekordok száma kisebb, mint 1000.
- Ha a rekordok száma meghaladja a 1000-et, az adatok exportálása aszinkron módon történik egy letöltési oldalon, amely a következő 30 napra elérhető lesz.
- A szűrők a **részletes használati adatokra** alkalmazhatók, hogy csak azokat az adatokat jelenítsék meg, amelyek érdeklik. Az adatok az ország/régió, az értékesítési csatorna, a Piactéri licenc típusa, a használati típus, az ajánlat neve, az ajánlat típusa, az ingyenes próbaverziók, a piactér-előfizetés azonosítója, az ügyfél azonosítója és a vállalat neve alapján szűrhetők.

> [!NOTE]
> Válassza ki a **használati típust** az oldal szűrőben a diagramok megjelenítéséhez a lapon a "normalizált nézet" vagy a "nyers nézet" nézetben. A diagramok alapértelmezett nézete a "normalizált nézet".

A **használati oldal szűrői** az oldal szintjén lesznek alkalmazva. Több szűrőt is kiválaszthat a diagram megjelenítéséhez a megtekinteni kívánt feltételek és a "részletes használati adatok" rács/exportálás mezőben megjelenített adatok megjelenítéséhez. A szűrők a megrendelések oldal jobb felső sarkában kiválasztott adattartományra kinyert adattartományon lesznek alkalmazva.

- Az **ajánlat típusa** és az **ajánlat neve** csak a kiválasztott dátumtartomány esetében beszerzett ajánlatokra van felsorolva. A listában szereplő ajánlatok nevei megjelennek a listából kiválasztott ajánlati típusoknál.
- Az alapértelmezett kijelölés az összes szűrési lehetőség esetében az "all" (a **használat típusának**kivételével). A **használat típusának** alapértelmezett kiválasztása normalizált használat. A diagramok nyers használatának megjelenítéséhez válassza a "nyers használat" lehetőséget.
- Az alkalmazott szűrők a kiválasztott szűrési beállítások darabszámát jelenítik meg. Az alkalmazott szűrők nem jelennek meg az alapértelmezett beállításokhoz.

> [!NOTE]
> A "részletes rendelési adat" rács, az oldal szűrők és az összes lehetséges beállítás részletes definíciója a [GYIK és a terminológiai](link needed) cikk adatszótár szakaszában van meghatározva.

A **mért számlázási használat** lap olyan használati adatokat jelenít meg, amelyekben a használat mérése mérési dimenzió alapján történik. Jelenleg az SaaS-ajánlat típusa meghaladja a következőt:. A lapon láthatók a túlterhelési trendek grafikus ábrázolása a SaaS által mért számlázási használat esetében:

- Túlhasználati **trend mérési dimenzió szerint**: az ajánlat kiválasztott mérési dimenziójához tartozó havi túlhasználati trendet jeleníti meg. Az X tengely a hónapot jelöli, és az Y tengely a használat mennyiségét jelöli. Az egyéni fogyasztásmérő mértékegysége az Y tengelyen is megjelenik.
- Túlterhelési **trend SKU szerint**: a kiválasztott mérési dimenzió kihasználtsági mennyiségét adja meg SKU alapján. A megjelenő SKU az 5 legnagyobb SKU-t jelöli a kiválasztott ajánlat legmagasabb használati szintjével.
- **Top 50-ügyfelek általi**túlterhelési Trend: az első 50-ajánlat a legmagasabb használati idővel jelenik meg a ***ranglistán*** , és az egyéni fogyasztásmérő legmagasabb használata alapján rangsorolva van. Válasszon ki egy ügyfelet a ranglistán a kiválasztott mérési dimenzió használati trendjeinak megtekintéséhez.
- Legfelső **szintű ügyfelektől**származó túlterhelési Trend: a legfontosabb ügyfelek százalékos értékének (ek) a teljes használati értékhez való hozzájárulását mutatja be. A Top Customer percentilis az X tengely mentén jelenik meg, amelyet az ügyfél használati mennyisége határoz meg. Az Y tengely megjeleníti a használati mennyiséget. A részletek megjelenítéséhez vigye az egérmutatót a vonalak fölé a diagram mentén.

> [!NOTE]
> A lap szűréséhez a használati adatok és az ezen a lapon található összes diagram meg van adva.

## <a name="next-steps"></a>Következő lépések

- A partner Center kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [a partner Center kereskedelmi piacának elemzései](./analytics.md).
- Az ajánlathoz tartozó Piactéri tevékenységet összefoglaló összesített adatok grafikonok, trendek és értékek esetében lásd: [az összefoglaló irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](./orders-dashboard.md).
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket, lásd [a kereskedelmi piactér elemzésének ügyfél-irányítópultját](./customer-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./downloads-dashboard.md).
- Ha szeretné megtekinteni a Microsoft AppSource és az Azure piactéren elérhető ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a [kereskedelmi piactér Analytics minősítések és áttekintések irányítópultját](./ratings-reviews.md).
- A kereskedelmi piactér elemzésével kapcsolatos gyakori kérdésekre, valamint az adatkifejezések átfogó szótárát lásd: [Gyakori kérdések és terminológia a kereskedelmi piactér elemzéséhez](./faq-terminology.md).
