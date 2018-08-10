---
title: A méretezési csoport az Azure Time Series Insights-környezet tervezése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan ajánlott eljárások követése az Azure Time Series Insights környezetekben, beleértve a tárolási kapacitás, az adatmegőrzés, betöltési kapacitás, figyelés és üzleti vészhelyreállítási (bcdr funkciók) tervezése során.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 2c06463d95467543a426079addf981aa42d53eb6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630636"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Az Azure Time Series Insights-környezet megtervezése

Ez a cikk ismerteti a várt bejövő forgalom és az adatmegőrzési követelmények alapján az Azure Time Series Insights-környezet megtervezése.

## <a name="best-practices"></a>Ajánlott eljárások

Ismerkedés a Time Series Insights, az a legcélszerűbb, ha tudja, hogy mennyi adatot tervez, hogy hogyan mennyi ideig kell tárolni az adatokat a percalapú leküldéses.  

Mindkét Time Series Insights termékváltozatok kapacitás és a megőrzési kapcsolatos további információkért lásd: [Time Series Insights díjszabása](https://azure.microsoft.com/pricing/details/time-series-insights/).

Vegye figyelembe a következő attribútumokat az igényeinek leginkább megfelelő csomagot a hosszú távú sikert környezetet: 
- Tárkapacitás
- Adatmegőrzés időtartama
- Betöltési kapacitás 
- Az események alakításra.
- Arról, hogy a referenciaadatok rendelkezik helyben

## <a name="understand-storage-capacity"></a>Megismerheti a tárolási kapacitás
Alapértelmezés szerint a Time Series Insights adatokat rendelkezik kiépített tárterület mérete alapján (egységek idő mennyisége tárhely / egység) a bejövő és kimenő megőrzi.

## <a name="understand-data-retention"></a>Az adatmegőrzés ismertetése
Konfigurálhatja a Time Series Insights-környezet **adatmegőrzési idő** beállítás, akár 400 napos adatmegőrzés engedélyezése.  A Time Series Insights szolgáltatásnak két módja van, a, amely optimalizálja a környezet biztosítása érdekében a lehető legfrissebb adatokat (az alapértelmezés szerint), és egy másikat, amely optimalizálja az, hogy az adatmegőrzési korlátok teljesülnek, ahol bejövő szünetel, ha a teljes tárolási kapacitásának a elérte a környezetben.  Módosíthatja a megőrzési és a környezet konfigurációs lapja az Azure Portalon a két mód közötti váltás.

A Time Series Insights környezetben legfeljebb 400 napos adatmegőrzés konfigurálhatja.

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Time Series Insights-környezetet.

2. Az a **Time Series Insights-környezetek lap**alatt a **beállítások** szakaszban kattintson **konfigurálása**. 

3. Az a **adatok megőrzési ideje (nap)** mezőben adjon meg egy értéket 1-től a 400-as.

   ![Megőrzés konfigurálása](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Betöltési kapacitás ismertetése

Az egyéb tervezési összpontosíthat területen betöltési kapacitás, amely leszármazottja, a percalapú foglalási. 

A sávszélesség-szabályozási szempontjából egy ingressed adatok csomagot a csomag mérete 32 KB-os 32 események számít, minden egyes 1 KB méretű. Engedélyezett események maximális mérete 32 KB; 32 KB-nál nagyobb adatcsomagok csonkolva lesznek.

A következő táblázat összefoglalja a belépő kapacitás esetében egyes Termékváltozat:

|SKU  |Események száma havonta egységenként  |Események / hónap, egységenkénti mérete  |Események száma / perc / egység  | / Perc / egység mérete   |
|---------|---------|---------|---------|---------|
|S1     |   30 millió     |  30 GB     |  700    |  700 KB   |
|S2     |   300 millió    |   300 GB   | 7,000   | 7000 KB  |

Az S1 vagy S2 Termékváltozat kapacitása 10 egység egyetlen környezetben lehet növelni. S2 szintű, S1 környezet vagy egy S1, S2 környezet nem telepíthetők át. 

Betöltési kapacitás, a havi alapon van szüksége a belépő elsőként azt kell meghatározni. Következő lépésként állapítsa meg, mi a percalapú igények kielégítéséhez, mivel ez a, ha szabályozás és a késés játszik szerepet.

Ha ugrásszerű a beérkező adatok 24 óránál rövidebb ideig tart, Time Series Insights "olvasottként", 2 x a fent felsorolt díjak a bejövő forgalom is. 

Például ha egy egyetlen S1 Termékváltozat és a bejövő adatok gyakorisággal 700 események perc, és a megnövekedett száma kevesebb mint 1 órája események 1400-as vagy annál kisebb gyakorisággal, lenne a környezetben nincs észrevehető késés. Azonban Ha elfogynak 1400-as események száma percenként több mint egy óráig, lenne valószínűleg tapasztal késést teszi és a lekérdezés a környezetben elérhető adatokat. 

Előfordulhat, hogy nem tudja előre mennyi adatot tervez leküldéses. Ebben az esetben annak telemetriai adatok [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) és [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) az Azure Portalon. A telemetriai adatok segítségével meghatározhatja, hogyan építheti ki a környezet. Használja a **metrikák** oldal az Azure Portalon a megfelelő esemény-adatforráshoz a telemetriájának megtekintéséhez. Ha megismeri az esemény az adatforrás mérőszámainak, hatékonyabban tervezhet és a Time Series Insights-környezet kiépítése.

### <a name="calculate-ingress-requirements"></a>Belépő kiszámítására

- Győződjön meg arról, hogy a környezet áll elég nagy legyen a 2-a kapacitás, kisebb, mint 1 órája egyenértékű várható bejövő forgalom kezelésére, és a belépő kapacitás fent az átlagos percalapú díját.

- Bejövő adatforgalmi csúcsokhoz fordulhat elő, ha, amely utoljára több mint 1 óra, használja a megnövekedett forgalom az átlag, és kiépítése a környezet kapacitása a megnövekedett forgalom kezelésére.
 
### <a name="mitigate-throttling-and-latency"></a>Szabályozás és a késés csökkentése

Szabályozás és a késleltetés elkerülése érdekében kapcsolatos információkért lásd: [csökkentheti a késést és a sávszélesség-szabályozási](time-series-insights-environment-mitigate-latency.md). 

## <a name="shaping-your-events"></a>Az események alakításra.
Fontos, hogy ellenőrizze a TSI eseményeket küldeni módon támogatja szeretne kiépíteni a környezet méretétől (ezzel szemben, leképezheti a hány eseményt TSI olvassa be a környezetet, és minden egyes esemény méretét).  Hasonlóan fontos gondolja át az attribútumokat, érdemes lehet szeletelni és szerinti szűrést, amikor az adatok lekérdezése.  Ezt szem, javasoljuk, hogy a JSON szakaszában alakításra áttekintése a *események küldése* dokumentáció [dokumentációja] (https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  A lap alsó van.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Arról, hogy a referenciaadatok rendelkezik helyben
Referencia-adatkészlet egy bővítésével kiolvassa az eseményeket az eseményforrás gyűjteménye. Time Series Insights bejövő forgalmat kezelő motorja a referencia-adatkészlet minden eseményt az eseményforrás a megfelelő adatok sor csatlakozik. Ez a kibővített esemény ezután lekérdezhető. A csatolás a referencia-adatkészlet meghatározott elsődleges kulcs oszlopoknak alapul.

Vegye figyelembe, hogy a referenciaadatok visszamenőlegesen nem csatlakozik tartományhoz. Ez azt jelenti, hogy csak a jelenlegi és jövőbeli bejövő adatainak matched és referencia meghatározott időpontban csatlakozik, konfigurálása és feltöltése után.  Ha azt tervezi, nagy mennyiségű előzményadatok küldeni a TSI és ne töltse fel, vagy hozzon létre referenciaadatok a TSI-ben először, akkor előfordulhat, hogy újra ezt a munkát (mutatót, nem szórakozást).  

Létrehozása, töltse fel és kezelheti a TSI-ben a referenciaadatok kapcsolatos további információkért látogasson el *referenciaadatok* dokumentáció [dokumentáció](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás
Azure-szolgáltatások, mint a Time Series Insights biztosít magas rendelkezésre ÁLLÁS redundanciát használatával az Azure-régióban szinten, a megoldás által igényelt minden további munka nélkül. A Microsoft Azure platform olyan funkciókat, hozzon létre megoldásokat a vész-helyreállítási lehetőségei vagy régiók rendelkezésre állása érdekében is. Ha azt szeretné, adja meg a globális, régiók közötti magas rendelkezésre állás az eszközöket vagy felhasználókat, ezeket az Azure-beli Vészhelyreállítási funkciók előnyeit. A cikk [Azure üzleti folytonossági műszaki útmutatóját](../resiliency/resiliency-technical-guidance.md) üzletmenet-folytonosság és Vészhelyreállítás az Azure-ban a beépített szolgáltatásait ismerteti. A [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások] [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások] tanulmány architektúra útmutatást nyújt az Azure-alkalmazások magas rendelkezésre ÁLLÁS és Vészhelyreállítás megvalósítása stratégiák.

A Time Series Insights nem rendelkezik beépített üzleti vészhelyreállítás (BCDR).  BCDR igénylő felhasználók azonban továbbra is valósíthat meg a helyreállítási stratégia. Hozzon létre egy második Time Series Insights-környezet biztonsági mentési Azure-régióban, és események küldése a másodlagos környezetbe történő elsődleges, a második dedikált fogyasztói csoportot és az adott eseményforrás BCDR-irányelvek.  

1.  Környezet létrehozása a második régióban.  Egy Time Series Insights-környezet létrehozásával kapcsolatos további [Itt](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-get-started).
2.  Hozzon létre az eseményforrás második dedikált fogyasztói csoportot, és az esemény-adatforráshoz csatlakozhat az új környezet.  Győződjön meg arról, hogy a második, dedikált fogyasztói csoportot kijelölje.  További információt a következő vagy [az IoT Hub dokumentációja](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) vagy [dokumentáció](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access).
3.  Ha az elsődleges régióban egy vészhelyreállítási incidens során leáll, váltson át a biztonsági mentési Time Series Insights-környezet műveletek.  

Az IoT Hub BCDR-szabályzatokkal kapcsolatos további tudnivalókért látogasson [Itt](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-ha-dr).  Az eseményközpont BCDR-szabályzatokkal kapcsolatos további tudnivalókért látogasson [Itt](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-geo-dr).  

## <a name="next-steps"></a>További lépések
- [Event Hub-eseményforrás hozzáadása](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [IoT Hub-eseményforrás hozzáadása](time-series-insights-how-to-add-an-event-source-iothub.md)
