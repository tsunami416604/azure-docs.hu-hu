---
title: A megfelelő adatátviteli ajánlat kiválasztása Azure Cosmos DB
description: Megtudhatja, hogyan választhat a standard (manuális) kiépített átviteli sebesség és az automatikus méretezés kiépített átviteli sebessége között a számítási feladatokhoz.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: fbe17d75ad809c54939624b1409e281b2f62a037
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605202"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>A standard (manuális) és az automatikus méretezés kiépített átviteli sebességének kiválasztása 

Azure Cosmos DB a kiépített átviteli sebesség két típusát vagy ajánlatát támogatja: Standard (manuális) és automatikus skálázás. Mindkét átviteli típus alkalmas olyan kritikus fontosságú számítási feladatokhoz, amelyek nagy teljesítményt és méretezést igényelnek, és ugyanazokat a Azure Cosmos DB SLA-kat támogatják, mint az átviteli sebesség, a rendelkezésre állás, a késés és a konzisztencia.

Ez a cikk azt ismerteti, hogyan választható a standard (manuális) és az automatikus méretezés kiosztott átviteli sebessége a számítási feladatokhoz. 

## <a name="overview-of-provisioned-throughput-types"></a>A kiépített átviteli sebesség típusok áttekintése
A standard (manuális) és az automatikus méretezés közötti különbség előtt fontos, hogy először is tisztában legyen azzal, hogyan működik a kiépített átviteli sebesség a Azure Cosmos DBban. 

Ha kiosztott átviteli sebességet használ, a számítási feladathoz a számítási egység/másodperc (RU/s) szerint mért átviteli sebességet kell megadnia. A szolgáltatás kiépíti az átviteli sebességre vonatkozó követelmények támogatásához szükséges kapacitást. A szolgáltatásra irányuló adatbázis-műveletek, például az olvasások, írások és lekérdezések bizonyos mennyiségű kérelmezési egységet (RUs) használnak. További információ a [kérelmek egységéről](request-units.md).

Az alábbi táblázat a standard (manuális) és az automatikus skálázás közötti magas szintű összehasonlítást mutatja be.

|Leírás|Standard (manuális)|Automatikus méretezés|
|-------------|------|-------|
|Legmegfelelőbb a következőhöz:|Állandó vagy kiszámítható forgalommal rendelkező számítási feladatok|Változó vagy kiszámíthatatlan forgalommal rendelkező számítási feladatok. Lásd: [az autoscale használatának esetei](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Működés|Kiépítheti a statikus RU/s mennyiségét az `T` idő függvényében, hacsak nem módosítja őket manuálisan. Másodpercenként akár `T` ru/s átviteli sebességet is használhat. <br/><br/>Ha például a standard (manuális) 400 RU/s érték van megadva, akkor az átviteli sebesség a 400 RU/s-ben marad.|Állítsa be a legmagasabb vagy a maximális RU/mp értéket, `Tmax` Ha nem szeretné, hogy a rendszeren túllépjek. A rendszer automatikusan méretezi az átviteli sebességet `T` `0.1* Tmax <= T <= Tmax` . <br/><br/>Ha például az 4000 RU/s értékre állítja be a maximálisan engedélyezett RU/s értéket, a rendszer a 400-4000 RU/s-t fogja méretezni.|
|Mikor lehet használni|Manuálisan szeretné felügyelni az átviteli kapacitást (RU/s), és a skálázást.<br/><br/>A kiépített RU/s-k magas, konzisztens kihasználtsággal rendelkeznek. Ha a hónap összes óráját kiépített RU/s értékre állítja, `T` és a teljes összeget az órák 66%-ában használja, a becslést a standard (manuális) kiépített ru/s használatával fogja menteni.<br/><br/>Ez a `T` Standard (manuális) beállítás és az automatikus skálázási egység közötti összehasonlításon alapul `Tmax` . |Az adatátviteli kapacitás (RU/s) és a méretezés a használat alapján felügyelhető Azure Cosmos DB.<br/><br/>Az RU/s használata változó vagy nehezen kiszámítható. Egy hónap összes órájában, ha az autoscale Max RU/s értéket állítja be, `Tmax` és a teljes összeget az `Tmax` órák 66%-ában használja, a becsült érték az autoscale.<br/><br/>Ez az automatikus skálázás `Tmax` és a `T` Standard (manuális) átviteli sebesség beállítása közötti összehasonlításon alapul.|
|Számlázási modell|A számlázás óránként, a felhasznált RU/s alapján történik, függetlenül attól, hogy hány RUs lett felhasználva.<br/><br/>Példa: <li>400 RU/s kiépítése</li><li>1. óra: nincsenek kérelmek</li><li>Óra 2:400 RU/s értékű kérelmek</li><br/><br/>Az 1. és a 2. órában egyaránt 400 RU/s díjat számítunk fel mindkét órára a [Standard (manuális) díjszabás](https://azure.microsoft.com/pricing/details/cosmos-db/)alapján.|A számlázás óránként történik, a legmagasabb RU/s esetében pedig a rendszer az órán belül méretezhető. <br/><br/>Példa: <li>A 4000 RU/s értékre való autoskálázás maximális száma (400 – 4000 RU/s)</li><li>1. óra: a rendszerek a legmagasabb 3500 RU/s értékre lettek méretezve</li><li>2. óra: a rendszerek a használat hiánya miatt legalább 400 RU/s (mindig 10%-a) méretűek. `Tmax`</li><br/><br/>A 3500 RU/s óradíjat az 1. órában, a 400 RU/s pedig a 2. órában, az automatikusan [kiosztott átviteli sebességnél](https://azure.microsoft.com/pricing/details/cosmos-db/). Az automatikus skálázási sebesség (RU/s) 1,5 * a standard (manuális) sebesség.
|Mi történik, ha túllépi a kiosztott RU/mp-t|Az RU/s statikus marad a kiépítés során. A másodpercben a kiépített RUs-on kívül felhasználható kérések száma korlátozott lesz, és egy olyan válasz, amely az újrapróbálkozások előtt időt ajánl. Ha szükséges, manuálisan növelheti vagy csökkentheti az RU/mp-t.| A rendszer az RU/s-t az autoscale Max RU/s értékre fogja méretezni. A másodpercek alatt az autoscale Max RU/mp-re felhasználható kérések száma korlátozott lesz, és egy olyan válasz, amely az újrapróbálkozást követően időt ajánl fel.|

## <a name="understand-your-traffic-patterns"></a>A forgalmi minták megismerése

### <a name="new-applications"></a>Új alkalmazások ###

Ha új alkalmazást hoz létre, és még nem ismeri a forgalmi mintát, érdemes lehet az RU/s (vagy a minimum RU/s) belépési ponttal kezdeni a kiépítés elkerülését az elején. Ha azonban olyan kis alkalmazásra van szüksége, amely nem igényel nagy méretet, érdemes csak a minimálisan szükséges RU/s-t kiépíteni a költségeket optimalizálni. Az alacsony várható adatforgalommal rendelkező kisméretű alkalmazások esetében érdemes lehet a [kiszolgáló](throughput-serverless.md) nélküli kapacitás üzemmódot is figyelembe venni.

Függetlenül attól, hogy standard (manuális) vagy automatikus méretezést szeretne használni, a következő szempontokat kell figyelembe vennie:

Ha 400 RU/s belépési pontján standard (manuális) RU/s-t hoz létre, akkor nem fogja tudni használni a 400 RU/s-t, hacsak nem módosítja manuálisan az átviteli sebességet. Az 400 RU/s esetében a standard (manuális) kiosztott átviteli sebesség (óránként) alapján számítjuk fel a díjat.

Ha az autoskálázási sebességet a 4000 RU/s maximális RU/s értékre való belépési ponton helyezi üzembe, az erőforrás a 400 és 4000 RU/s között lesz méretezhető. Mivel az automatikus skálázási átviteli sebesség (RU/s) a standard (manuális) díjszabás 1,5 x-es értéke, az óra, ahol a rendszer a minimálisan 400 RU/s értékre van méretezve, a számla magasabb lesz, mint ha manuálisan kiosztotta a 400 RU/s értéket. Ha azonban az alkalmazás adatforgalmának tüskékkel jár, az autoskálázással bármikor akár 4000 RU/s-t is felhasználhat, és nincs szükség felhasználói beavatkozásra. Általánosságban mérlegelje, hogy a maximális RU/mp-t bármikor felhasználhatja, ha az adatmennyiség 1,5 x sebességét használja.

Az átviteli sebességre vonatkozó követelmények becsléséhez használja a Azure Cosmos DB [Capacity kalkulátort](estimate-ru-with-capacity-planner.md) . 

### <a name="existing-applications"></a>Meglévő alkalmazások ###

Ha egy meglévő alkalmazás standard (manuális) kiosztott átviteli sebességgel rendelkezik, [Azure monitor metrikák](../azure-monitor/insights/cosmosdb-insights-overview.md) használatával meghatározhatja, hogy a forgalmi minta alkalmas-e az automatikus skálázásra. 

Először keresse meg az adatbázis vagy tároló [normalizált kérelmi egységének felhasználási metrikáját](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) . A normalizált kihasználtság azt méri, hogy jelenleg milyen mértékben használja a standard (manuális) kiépített átviteli sebességet. Minél közelebb van a számhoz, a 100%-ot, annál többet a kiépített RU/s-k használatával. [További](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) információ a metrikáról.

Ezután határozza meg, hogy a normalizált kihasználtság hogyan változik az idő múlásával. Ha úgy látja, hogy a normalizált kihasználtsága változó vagy kiszámíthatatlan, érdemes lehet az adatbázison vagy a tárolón is engedélyezni az autoskálázást. Ezzel szemben, ha az állandó és kiszámítható, érdemes megfontolni a standard (manuális) kiépített átviteli sebesség megmaradása esetén. 

> [!TIP]
> A normál (manuális) átviteli sebesség használatával a normalizált kihasználtsági metrikával becsülheti meg a tényleges RU/mp-t, ha automatikus méretezésre vált. Az aktuálisan kiosztott standard (manuális) RU/mp használatával szorozzuk meg a normalizált kihasználtságot egy adott időpontban. Ha például 5000 RU/s lett kiépítve, és a normalizált kihasználtság értéke 90%, az RU/s használata 0,9 * 5000 = 4500 RU/s. Ha úgy látja, hogy a forgalmi minta változó, de Ön vagy a kiépítés alatt áll, érdemes engedélyezni az autoskálázást, és ennek megfelelően módosítania kell az autoscale Max RU/s beállítást.

## <a name="measure-and-monitor-your-usage"></a>A használat mérése és figyelése
Idővel, az átviteli sebesség típusának kiválasztása után figyelje az alkalmazást, és szükség szerint végezze el a szükséges módosításokat. 

Ha autoskálázást használ, a Azure Monitor használatával megtekintheti a kiépített autoskálázási Max RU/s (az**autoskálázás maximális átviteli sebessége**) és a rendszer jelenleg (**kiépített átviteli sebesség**) méretét. Az alábbi példa egy változót vagy kiszámíthatatlan munkaterhelést mutat be az autoscale használatával. Vegye figyelembe, hogy ha nincs forgalom, a rendszer az RU/s-t a maximális RU/s érték legalább 10%-át méretezi, ami ebben az esetben 5000 RU/s és 50 000 RU/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Példa a munkaterhelés használatára az autoscale használatával":::

> [!NOTE]
> Ha standard (manuális) kiosztott átviteli sebességet használ, a **kiosztott átviteli sebesség** mérőszáma arra utal, hogy mit állított be a felhasználó. Ha az autoscale átviteli sebességet használja, ez a metrika a rendszer aktuálisan méretezhető RU/s-re vonatkozik.

## <a name="next-steps"></a>További lépések
* Az [ru-kalkulátor](https://cosmos.azure.com/capacitycalculator/) használatával becsülheti meg az új számítási feladatok átviteli sebességét.
* A meglévő számítási feladatok figyeléséhez használja a [Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) .
* Megtudhatja, hogyan építhet ki az [Azure Cosmos-adatbázison vagy-tárolón az adatméretezési sebességet](how-to-provision-autoscale-throughput.md).
* Tekintse át az [autoscale – gyakori kérdések](autoscale-faq.md)című szakaszt.