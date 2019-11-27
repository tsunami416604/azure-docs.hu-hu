---
title: 'Áttekintés: Mi az Azure Time Series Insights? – Azure Time Series Insights | Microsoft Docs'
description: Bevezetés az idősorozat-adatok elemzéséhez és IoT-megoldásokhoz készült új Azure Time Series Insights szolgáltatás használatába.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: 042e969b4a0dfea0910a0ef81fae264792cfd85f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327667"
---
# <a name="what-is-azure-time-series-insights"></a>Mi az Azure Time Series Insights?

Azure Time Series Insights a nagy mennyiségű idősoros adatsorozat-információ tárolására, megjelenítésére és lekérdezésére szolgál, például a IoT-eszközök által generált. Ha idősorozat-adatokat szeretne a felhőben tárolni, kezelni, lekérdezni vagy vizualizálni, a Time Series Insights valószínűleg jó választás. 

[![Time Series Insights folyamatábrája](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

A Time Series Insights négy fő feladata:

- A Felhőbeli átjárók teljes mértékben integrálva vannak, például az Azure IoT Hub és az Azure Event Hubs. Könnyedén csatlakozik ezekhez az eseményforrásokhoz, és elemzi az adatokat egyszerű sorokban és oszlopokban tároló üzenetekből és struktúrákból származó JSON-t. A metaadatokat összeköti a telemetriával, és egy oszlopos tárban indexálja az adatait.
- Time Series Insights kezeli az adatai tárolását. Annak biztosítása érdekében, hogy az adatai mindig könnyen elérhetők legyenek, a memóriában és SSD-ben tárolja az adatait, akár 400 napig. A másodpercek alatt interaktív módon lekérdezheti az események milliárdjait – igény szerint.
- A Time Series Insights a Time Series Insights Explorerben elérhetővé teszi a beépített vizualizációkat. 
- A Time Series Insights egy lekérdezési szolgáltatást is biztosít, amely a Time Series Insights Explorerben és a könnyen integrálható API-kkal is használható az idősorozat-adathalmazok egyéni alkalmazásokba való beágyazásához.

Ha belső felhasználásra vagy külső ügyfelek használatára vonatkozó alkalmazást hoz létre, használhatja a Time Series Insightst háttérként. Használhatja az idősorozat-adatokat indexelni, tárolni és összesíteni. Az egyéni vizualizációk és felhasználói élmények létrehozásához használja az [ügyfél-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)-t. A Time Series Insights több [lekérdezési API](how-to-shape-query-json.md) -val is rendelkezik a testreszabott forgatókönyvek engedélyezéséhez.

Az idősorozat-adatok mutatják be, hogy hogyan változik az idők során egy adategység vagy folyamat. Az idősorozat-adatsorok az időbélyegzők szerint vannak indexelve, és az idő az a legértelmesebb tengely, amely az ilyen jellegű adatrendezést végzi. Az idősorozat-adatsorok általában szekvenciális sorrendben érkeznek, ezért a rendszer az adatbázis frissítése helyett beszúrásként kezeli.

Nagy mennyiségű idősoros adat tárolására, indexelésére, lekérdezésére, elemzésére és megjelenítésére is kihívást jelenthet.
Azure Time Series Insights rögzíti és tárolja az összes új eseményt sorra, és a változás hatékonyan mérhető az idő múlásával. Ennek eredményeképpen visszafelé is megtekintheti a múltbeli eredményeket a jövőbeli változások előrejelzéséhez.

## <a name="video"></a>Videó

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>További információ a felhőalapú IoT elemzési platformról: Azure Time Series Insights.</br>

[![videó](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

- Az idősorozat-adathalmazokat méretezhető módon tárolja. 

   A Time Series Insights lényegében egy idősorozat-adatokra tervezett adatbázis. Mivel méretezhető és teljes mértékben felügyelt, Time Series Insights kezeli az események tárolásának és kezelésének munkáját.

- A közel valós időben tárja fel az adatelemzést. 

   A Time Series Insights egy olyan tallózót biztosít, amely megjeleníti a környezetbe továbbított összes adatforgalmat. Röviddel azután, hogy kapcsolódott egy eseményforrás, megtekintheti, megtekintheti és lekérdezheti az eseményeket az Time Series Insightson belül. Az adatai segítségével ellenőrizheti, hogy az eszköz az elvárt módon bocsát-e ki adatmennyiséget, valamint hogy a IoT-eszközt az állapot, a termelékenység és az általános hatékonyság érdekében figyeli-e. 

- Hajtsa végre a kiváltó okok elemzését és a rendellenességek észlelését.

   Time Series Insights rendelkezik olyan eszközökkel, mint például a mintázatok és perspektívák nézetei, és így több lépésből álló kiváltó okok elemzését végezheti el. A Time Series Insights olyan riasztási szolgáltatásokat is használhat, mint például a Azure Stream Analytics, így a Time Series Insights Explorerben közel valós időben megtekintheti a riasztásokat és az észlelt rendellenességeket. 

- Globális áttekintést kaphat az idősoros adatokról, amelyek különböző helyekről streameket biztosítanak a több eszköz vagy a hely összehasonlításához.

   Egy Time Series Insights környezethez több eseményforrás is csatlakoztatható. Így megtekintheti a több, különböző helyekről származó adatfolyamokat közel valós időben. A felhasználók kihasználhatják ezt a láthatóságot az üzleti vezetőkkel való adatmegosztáshoz. Hatékonyabban dolgozhatnak olyan tartományi szakértőkkel, akik szakértelmet alkalmazhatnak a problémák megoldásához, az ajánlott eljárások alkalmazásához és a tanulás megosztásához.

- Hozzon létre egy Customer-alkalmazást Time Series Insightson. 

   A Time Series Insights REST lekérdezési API-kat tesz elérhetővé, amelyekkel idősorozat-adatokkal rendelkező alkalmazásokat hozhat létre.

## <a name="capabilities"></a>Funkciók

- **Gyors kezdés**: Azure Time Series Insights nem igényel előzetes adatelőkészítést, így gyorsan csatlakozhat több millió eseményhez az IoT hub vagy az Event hub szolgáltatásban. A kapcsolódást követően megjelenítheti és kezelheti az érzékelők adatait, hogy gyorsan érvényesítse IoT-megoldásait. Kód írása nélkül is dolgozhat az adataival, és nem kell új nyelvet tanulnia. Time Series Insights egy részletes, szabadszöveges lekérdezési felületet biztosít a speciális felhasználók számára, és rámutathat a feltárás lehetőségre.

- **Közel valós idejű**adatmennyiség: Time Series Insights naponta több millió szenzoros eseményt képes befogadni, egy perces késéssel. Time Series Insights segít betekintést nyerni az érzékelők adataiba. A trendek és rendellenességek észlelésére, a kiváltó okok elemzésére és a költséges állásidő elkerülésére használható. A valós idejű és a korábbi adatkapcsolatok közötti összefüggések révén a rejtett trendek is megtalálhatók az adathalmazban.

- **Egyéni megoldások készítése**: Azure Time Series Insights-adatait beágyazhatja meglévő alkalmazásaiba. Létrehozhat új egyéni megoldásokat is a Time Series Insights REST API-kkal. Személyre szabott nézeteket hozhat létre, hogy mások is megtekinthessék a felfedezett összefüggéseket.

- **Méretezhetőség**: A Time Series Insights támogatja a nagy mennyiségű IoT-adat kezelését. Napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. Közel valós időben jeleníthet meg és elemezheti az élő adatfolyamokat a korábbi adatelemzések mellett.

## <a name="get-started"></a>Első lépések

Az első lépésekhez kövesse az alábbi lépéseket.

1. Time Series Insights környezet kiépítése a Azure Portal.
1. Kapcsolódjon egy olyan eseményforrás, mint egy IoT hub vagy egy Event hub. 
1. Hivatkozási adatok feltöltése. Ez nem egy kiegészítő szolgáltatás.
1. A Time Series Insights Explorerrel néhány percen belül megtekintheti az adatait.

## <a name="explorer"></a>Explorer

A Azure Time Series Insights Explorer használatával megtekintheti, elemezheti és feltárhatja az adataiban mutatkozó trendeket.

![Time Series Insights Explorer](media/overview/time-series-insights-explorer-panel.png)

Megtudhatja, hogyan használhatja a [Azure Time Series Insights Explorert](time-series-insights-explorer.md) , és hogyan rajzolhatja meg az adatokból származó információkat.

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a Azure Time Series Insights általánosan elérhető [ingyenes bemutató környezettel](./time-series-quickstart.md).

- További információ a Time Series Insights környezet [megtervezéséről](time-series-insights-environment-planning.md) .
