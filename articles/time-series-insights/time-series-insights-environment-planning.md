---
title: A skála Azure idő adatsorozat Insights környezet tervezése |} Microsoft Docs
description: Ez a cikk egy Azure idő adatsorozat Insights környezetben, beleértve a tárolási kapacitás, az adatmegőrzés, érkező kapacitás, figyelés és üzleti vészhelyreállítási (BCDR) tervezése során kövesse a bevált gyakorlatokat ismerteti.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: jasonh
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: f0f414e43231fc6d873d639902fd4f71e48f1002
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751169"
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Az Azure idő adatsorozat Insights környezet megtervezése

Ez a cikk ismerteti a tervezze meg a várt érkező arányról és az adatmegőrzési követelmények alapján Azure idő adatsorozat Insights környezetéhez.

## <a name="best-practices"></a>Ajánlott eljárások

Idő adatsorozat Insights megkezdéséhez, célszerű Ha tudja, hogy mennyi adatot várt leküldéses perc is, hogy mennyi ideig kell tárolni az adatokat.  

Mindkét idő adatsorozat Insights termékváltozatok kapacitás és a megőrzési kapcsolatos további információk: [idő adatsorozat Insights árképzési](https://azure.microsoft.com/pricing/details/time-series-insights/).

Vegye figyelembe a következő attribútumok legjobb terv a hosszú távú sikert környezetet: 
- Tárkapacitás
- Adatmegőrzés időtartama
- Érkező kapacitás 
- Az események kialakításában.
- Így mindig a referenciaadatok helyen

## <a name="understand-storage-capacity"></a>Tárolási kapacitás
Alapértelmezés szerint a idő adatsorozat Insights adatokat ellátta tárolókapacitást alapján (egységenként tárolási egység alkalommal összeg) és a bejövő adatok megőrzi.

## <a name="understand-data-retention"></a>Az adatmegőrzés ismertetése
Konfigurálhatja a idő adatsorozat Insights környezet **adatmegőrzési időtartam** beállítás, legfeljebb 400 napos adatmegőrzési engedélyezése.  Idő adatsorozat Insights szolgáltatásnak két módja van, egy, a környezet biztosításáért optimalizálja a legfrissebb adatokat tartalmaz (az alapértelmezés szerint), és egy másik, ami optimalizálja a korlátok teljesülnek, ahol érkező szünetel, ha a megőrzési biztosításáért az általános tárolási kapacitását a Kattintson az környezetben.  Módosíthatja a megőrzési és a környezet konfigurálása lapon az Azure-portálon a két mód közötti váltás.

Legfeljebb 400 napig az adatok megőrzési idő adatsorozat Insights környezetében konfigurálhatja.

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki azt az időt adatsorozat Insights környezetet.

2. A a **idő adatsorozat Insights környezet lap**a a **beállítások** elemcsoportban válasszon **konfigurálása**. 

3. Az a **adatok megőrzési ideje (nap)** mezőben adjon meg egy értéket 1 400.

   ![Megőrzés konfigurálása](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Érkező kapacitás

Az egyéb területen tervezési összpontosítani érkező kapacitás, amely leszármazottja, a perc foglalási. 

A sávszélesség-szabályozási szempontjából egy 32 KB-os csomag méretű ingressed adatcsomag 32 eseményeket a rendszer, egyes 1 KB méretű. A maximális engedélyezett eseményről mérete 32 KB; csak adatcsomagok 32 KB-nál nagyobb.

A következő táblázat összefoglalja a érkező kapacitás minden termékváltozat:

|SKU  |Események száma havonta, egységenként  |Automatikus oszlopszélesség egy hónap, egység / események  |Események száma percenként egységenként  | Percenként, egység mérete   |
|---------|---------|---------|---------|---------|
|S1     |   30 millió     |  30 GB     |  700    |  700 KB   |
|S2     |   300 millió    |   300 GB   | 7,000   | 7000 KB  |

Egyetlen környezetben 10 egységek S1 vagy S2 SKU kapacitása növelhető. Egy S2 S1 környezet vagy egy S1 S2 környezet nem telepíthetők át. 

Érkező kapacitás a teljes hónap alapon szükséges belépési elsőként azt kell meghatározni. A következő meghatározására, hogy mi a percalapú igények, ez ugyanis, ha sávszélesség-szabályozás és a késleltetés szerepet játszanak.

Ha már rendelkezik egy csúcs a 24 óránál tovább tartó adatok érkező, idő adatsorozat Insights "olvasottként" a fent felsorolt díjszabás x 2 érkező ütemben is. 

Például ha van egy egyetlen S1 SKU és érkező adatok egy percet, és csúcs 700 események sebessége 1 400 esemény vagy kevesebb sebessége kisebb, mint 1 óra, nem lenne nincs észrevehető várakozási ideje a környezetben. Azonban ha 1 400 eseményt küld percenként több mint egy óráig túllépi volna valószínűleg tapasztal késési adatok feladatkonfigurációkat és lekérdezhetők a környezetben. 

Nem lehet felmérni előre mennyi adatot várt leküldéses. Ebben az esetben az adatok telemetriai található [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) és [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) az Azure-portálon. A telemetria segítségével meghatározhatja, hogy a környezet kiépítése. Használja a **metrikák** oldalon a megfelelő forrás a telemetriai adatok megtekintéséhez az Azure-portálon. Ha megismeri a esemény forrás metrikákat, hatékonyabb megtervezése és jogosultságok kiosztása a idő adatsorozat Insights környezet.

### <a name="calculate-ingress-requirements"></a>Érkező követelmények kiszámítása

- Ellenőrizze a perc átlagos túllépi ezt az értéket a bejövő adatok kapacitás, és, hogy elég nagy a kapacitás kisebb, mint 1 óra x 2 egyenértékű a várható érkező-e a környezetében.

- Érkező igényeiben jelentkező fordulhat elő, ha, amelyek utolsó több mint 1 óra csúcs sebessége használják az átlagos, és jogosultságok kiosztása a kapacitás a csúcs sebessége tartalmazó környezetben.
 
### <a name="mitigate-throttling-and-latency"></a>Sávszélesség-szabályozás és a késés csökkentése

Sávszélesség-szabályozás és a késés kapcsolatos információkért lásd: [csökkenthető a késleltetés és a sávszélesség-szabályozás](time-series-insights-environment-mitigate-latency.md). 

## <a name="shaping-your-events"></a>Az események kialakításában.
Fontos, hogy ellenőrizze, hogy a küldött események ÁME módszer támogatja kiépíteni a környezet mérete (ezzel szemben, leképezheti a hány események ÁME beolvassa a környezet és az egyes eseményeket mérete).  Hasonlóan fontos gondolja át az attribútumokat, érdemes lehet az adatok lekérdezésekor szűrés és többféle szempontból való.  Ennek tudatában, javasoljuk, hogy tekintse át a JSON kialakításában szakasza a *küldi az eseményeket* dokumentáció [dokumentáció] (https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).  A lap alján felé van.  

## <a name="ensuring-you-have-reference-data-in-place"></a>Így mindig a referenciaadatok helyen
A referencia-adatkészlet kiegészítheti az esemény forrásból származó események elemek gyűjteménye. Idő adatsorozat Insights érkező motor minden esemény az eseményforrás a megfelelő adatsor a csatlakozik a referencia-adatkészlet a. Ez a kibővített esemény ezután lekérdezhető. Ezt az összekapcsolást az elsődleges kulcs oszlop(ok) meghatározott a referencia-adatkészlet alapján történik.

Megjegyzés: a referenciaadatok visszamenőleges nincs tartományhoz csatlakoztatva. Ez azt jelenti, hogy csak a jelenlegi és jövőbeli érkező adatok egyező és referencia meghatározott időpontban csatlakozik, konfigurálása és feltöltése után.  Ha azt tervezi, hogy a nagy mennyiségű előzményadatokat küldeni ÁME és nem feltölteni, vagy hozzon létre referenciaadatok ÁME először, akkor lehet, hogy újra munkavégzésre (mutatót, nem szórakoztató).  

Hozzon létre, a referenciaadatok ÁME kezelését és feltöltését kapcsolatos további tudnivalókért keressen fel a *referenciaadatok* dokumentáció [dokumentáció](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set).

## <a name="business-disaster-recovery"></a>Üzleti katasztrófa utáni helyreállítás
Az Azure-szolgáltatások, mint a idő adatsorozat Insights biztosít magas rendelkezésre ÁLLÁS létszámcsökkentések használata az Azure-régiót szinten bármit tennie kellene a megoldás által igényelt nélkül. A Microsoft Azure platform olyan vész-helyreállítási képességek vagy kereszt-régiónkénti elérhetőség megoldások létrehozásához szolgáltatásokat is tartalmaz. Ha lehetővé szeretné tenni globális, kereszt-régió eszközöket vagy felhasználókat, a magas rendelkezésre állású Azure vész-Helyreállítási felhasználásokhoz előnyeit. A cikk [Azure üzleti folytonossági műszaki útmutatót](../resiliency/resiliency-technical-guidance.md) az Azure-ban az üzletmenet folytonosságát és vész-Helyreállítási beépített funkcióit mutatja be. A [vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások] [vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások] papír architektúra-útmutatót biztosít az Azure-alkalmazások elérése a magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási stratégiát.

Idő adatsorozat Insights nem rendelkezik beépített üzleti vészhelyreállítási (BCDR).  A felhasználóknak, akik BCDR azonban továbbra is valósíthat meg a helyreállítási stratégia. Hozzon létre egy második idő adatsorozat Insights környezet egy biztonsági mentési Azure-régiót, és a másodlagos környezet az elsődleges eseményforrás, a második dedikált fogyasztói csoportot és, hogy eseményforrás BCDR irányelveket az események küldése.  

1.  Környezet létrehozása második régióban.  Több idő adatsorozat Insights-környezetek létrehozása a [Itt](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-get-started).
2.  Hozzon létre az eseményforrás második dedikált fogyasztói csoportot, és csatlakozzon a forrás az új környezet.  Győződjön meg arról, hogy kijelölje a második, dedikált fogyasztói csoportot.  További a következő vagy [IoT-központ dokumentáció](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub) vagy [Event hub dokumentáció](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access).
3.  Ha az elsődleges régió egy katasztrófa utáni esemény során leáll, kapcsoljon át a biztonsági mentési idő adatsorozat Insights környezet műveletek.  

Az IoT-központ BCDR-házirendekkel kapcsolatos további tudnivalókért látogasson [Itt](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-ha-dr).  Az Event hubs BCDR-házirendekkel kapcsolatos további tudnivalókért látogasson [Itt](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-geo-dr).  

## <a name="next-steps"></a>További lépések
- [Az Event Hubs forrás hozzáadása](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Az IoT-központ forrás hozzáadása](time-series-insights-how-to-add-an-event-source-iothub.md)
