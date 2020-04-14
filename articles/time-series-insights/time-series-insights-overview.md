---
title: 'Áttekintés: Mi az Azure Time Series Insights? - Azure Time Series Insights | Microsoft dokumentumok'
description: Bevezetés az idősorozat-adatok elemzéséhez és IoT-megoldásokhoz készült új Azure Time Series Insights szolgáltatás használatába.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 59149b2ca598104d8aca9b4e5e60194a8f6398bf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269958"
---
# <a name="what-is-azure-time-series-insights"></a>Mi az Azure Time Series Insights?

Az Azure Time Series Insights nagy mennyiségű idősorozat-adat tárolására, megjelenítésére és lekérdezésére készült, például az IoT-eszközök által létrehozott adatok tárolására, megjelenítésére és lekérdezésére. Ha idősorozat-adatokat szeretne a felhőben tárolni, kezelni, lekérdezni vagy vizualizálni, a Time Series Insights valószínűleg jó választás.

[![Time Series Insights folyamatábra](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

A Time Series Insights négy fő feladata:

- Teljes mértékben integrálva van a felhőbeli átjárókkal, például az Azure IoT Hub-mal és az Azure Event Hubs-szal. Könnyedén csatlakozik ezekhez az eseményforrásokhoz, és elemzi az adatokat egyszerű sorokban és oszlopokban tároló üzenetekből és struktúrákból származó JSON-t. A metaadatokat összeköti a telemetriával, és egy oszlopos tárban indexálja az adatait.
- A Time Series Insights kezeli az adatok tárolását. Annak érdekében, hogy az adatok mindig könnyen hozzáférhetők legyenek, akár 400 napig tárolja az adatokat a memóriában és az SSD-kben. Az események milliárdjait interaktívan lekérdezheti másodpercben – igény szerint.
- A Time Series Insights beépített megjelenítést biztosít a Time Series Insights kezelőn keresztül.
- A Time Series Insights lekérdezési szolgáltatást biztosít mind a Time Series Insights-kezelőben, mind pedig olyan API-k használatával, amelyek könnyen integrálhatók az idősorozat-adatok egyéni alkalmazásokba való beágyazásához.

Ha belső felhasználásra vagy külső ügyfelek számára hoz létre alkalmazást, a Time Series Insights háttérrendszerként is használható. Az idősorozat-adatok indexelésére, tárolására és összesítésére használható. Egyéni vizualizáció és felhasználói élmény létrehozásához használja az [Ügyfél SDK-t.](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) A Time Series Insights számos [lekérdezési API-val](how-to-shape-query-json.md) is rendelkezik, amelyek lehetővé teszik ezeket a testreszabott forgatókönyveket.

Az idősorozat-adatok mutatják be, hogy hogyan változik az idők során egy adategység vagy folyamat. Az idősorozat-adatokat időbélyegek indexelik, és az idő a legértelmesebb tengely, amely mentén az ilyen adatok vannak rendszerezve. Az idősorozat-adatok általában sorrendben érkeznek, így a rendszer beszúrásként kezeli őket, nem pedig az adatbázis frissítését.

Az idősorozat-adatok nagy kötetekben való tárolása, indexelése, lekérdezése, elemzése és megjelenítése kihívást jelent.
Az Azure Time Series Insights minden új eseményt egy sorként rögzít és tárol, és a változásokat az idő múlásával hatékonyan mérik. Ennek eredményeképpen hátrafelé tekintve betekintést nyerhet a múltból, hogy segítsen megjósolni a jövőbeli változásokat.

## <a name="video"></a>Videó

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>További információ az Azure Time Series Insights felhőalapú IoT-elemzési platformról.</br>

[![Videóinak](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

- Az idősorozat-adatokat méretezhető módon tárolhatja.

   A Time Series Insights lényegében egy idősorozat-adatokra tervezett adatbázis. Mivel méretezhető és teljes körűen felügyelt, a Time Series Insights kezeli az események tárolásának és kezelésének munkáját.

- Közel valós időben fedezheti fel az adatokat.

   A Time Series Insights egy olyan felfedezőt biztosít, amely megjeleníti a környezetbe adatfolyamként továbbító összes adatot. Röviddel azután, hogy csatlakozik egy eseményforráshoz, megtekintheti, felfedezheti és lekérdezheti az eseményadatokat a Time Series Insights alkalmazásban. Az adatok segítségével ellenőrizheti, hogy egy eszköz a várt módon bocsát-e ki adatokat, és figyelheti az IoT-eszközt az állapot, a termelékenység és az általános hatékonyság szempontjából.

- Gyökér-ok elemzés végrehajtása és anomáliák észlelése.

   A Time Series Insights olyan eszközökkel rendelkezik, mint a minták és a perspektívanézetek a többlépéses kiváltó ok elemzésének elvégzéséhez és mentéséhez. A Time Series Insights olyan riasztási szolgáltatásokkal is együttműködik, mint az Azure Stream Analytics, így közel valós időben megtekintheti a riasztásokat és észlelt rendellenességeket a Time Series Insights felfedezőjében.

- Az idősorozat-adatok globális nézetét tekintheti meg, amelyek különböző helyekről továbbítanak a többeszközből vagy webhelyösszehasonlításhoz.

   Egy Time Series Insights környezethez több eseményforrás is csatlakoztatható. Így közel valós időben tekintheti meg a több különböző helyről beérkező adatokat. A felhasználók kihasználhatják ezt a láthatóságot, hogy megosszák az adatokat az üzleti vezetőkkel. Jobban együttműködhetnek a tartományszakértőkkel, akik szakértelmüket a problémák megoldásához, a bevált gyakorlatok alkalmazásához és a tanulás megosztásához alkalmazhatják.

- Hozzon létre egy ügyfélalkalmazást a Time Series Insights-on felül.

   A Time Series Insights megjeleníti a REST-lekérdezési API-kat, amelyek segítségével idősorozat-adatokat használó alkalmazásokat hozhat létre.

## <a name="capabilities"></a>Funkciók

- **Gyors kezdés:** Az Azure Time Series Insights nem igényel előzetes adatelőkészítést, így gyorsan csatlakozhat az IoT-központ vagy az eseményközpont több millió eseményéhez. A csatlakozás után vizualizálhatja és kezelheti az érzékelőadatokat az IoT-megoldások gyors érvényesítéséhez. Az adatokat kód írása nélkül is kezelheti, és nem kell új nyelvet tanulnia. A Time Series Insights részletes, szabadszöveges lekérdezési felületet biztosít a tapasztalt felhasználók számára, és rámutatásos feltárást.

- **Közel valós idejű elemzések:** A Time Series Insights naponta több millió érzékelőeseményt tud bekezelni, egyperces késleltetéssel. A Time Series Insights segítségével betekintést nyerhet az érzékelőadataiba. Segítségével észlelheti a trendeket és anomáliákat, kiváltó okokat elemezhet, és elkerülheti a költséges állásidőt. A valós idejű és az előzményadatok közötti keresztkorreláció segít megtalálni az adatok rejtett trendjeit.

- **Egyéni megoldások at hozhat létre:** Az Azure Time Series Insights-adatok beágyazása a meglévő alkalmazásokba. A Time Series Insights REST API-kkal új egyéni megoldásokat is létrehozhat. Személyre szabott nézeteket hozhat létre, hogy mások is megtekinthessék a felfedezett összefüggéseket.

- **Méretezhetőség:** A Time Series Insights az IoT nagy méretekben való támogatására lett tervezve. Napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. Az élő adatfolyamokat közel valós időben jelenítheti meg és elemezheti az előzményadatok mellett.

## <a name="get-started"></a>Bevezetés

Első lépésként kövesse az alábbi lépéseket.

1. Egy Time Series Insights-környezet kiépítése az Azure Portalon.
1. Csatlakozzon egy eseményforráshoz, például egy IoT-központhoz vagy egy eseményközponthoz.
1. Referenciaadatok feltöltése. Ez nem egy kiegészítő szolgáltatás.
1. Tekintse át és jelenítse meg adatait percek alatt a Time Series Insights felfedezővel.

## <a name="explorer"></a>Explorer

Az Azure Time Series Insights explorer használatával megtekintheti, elemezheti és felfedezheti az adatok trendjeit.

![Time Series Insights Explorer](media/overview/time-series-insights-explorer-panel.png)

Ismerje meg, hogyan használhatja az [Azure Time Series Insights explorert,](time-series-insights-explorer.md) és hogyan nyerhet elemzéseket az adatokból.

## <a name="next-steps"></a>További lépések

- Fedezze fel az Azure Time Series Insights általános rendelkezésre [állásingyenes bemutatókörnyezetét.](./time-series-quickstart.md)

- További információ [a Time Series Insights-környezet megtervezéséről.](time-series-insights-environment-planning.md)
