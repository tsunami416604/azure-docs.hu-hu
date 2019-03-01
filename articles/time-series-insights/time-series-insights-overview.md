---
title: 'Áttekintés: Mi az Azure Time Series Insights? | Microsoft Docs'
description: Bevezetés az idősorozat-adatok elemzéséhez és IoT-megoldásokhoz készült új Azure Time Series Insights szolgáltatás használatába.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: a6bfeb820b34d36fc370de7f81694086aeea8515
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193960"
---
# <a name="what-is-azure-time-series-insights"></a>Mi az Azure Time Series Insights?

A Time Series Insights feladata nagy mennyiségű (például az IoT-eszközök által létrehozott) idősorozat-adat tárolása, vizualizálása és lekérdezése.  Ha idősorozat-adatokat szeretne a felhőben tárolni, kezelni, lekérdezni vagy vizualizálni, a Time Series Insights valószínűleg jó választás.  

![Time Series Insights folyamatábra](media/overview/time-series-insights-flowchart.png)

A Time Series Insights négy fő feladata:

- Az első, hogy teljesen integrálható az olyan felhőátjárókkal, mint az Azure IoT Hub vagy az Azure Event Hubs. Könnyedén csatlakozik ezekhez az eseményforrásokhoz, és elemzi az adatokat egyszerű sorokban és oszlopokban tároló üzenetekből és struktúrákból származó JSON-t. A metaadatokat összeköti a telemetriával, és egy oszlopos tárban indexálja az adatait.
- A második, hogy a Time Series Insights felügyeli az adatai tárolását. Annak érdekében, hogy az adatok mindig könnyen hozzáférhetők legyenek, az adatokat a memóriában és SSD-n tárolja akár 400 napig. Interaktívan kérdezheti le események milliárdjait másodpercek alatt – amikor szükséges.
- A harmadik, hogy a Time Series Insights a TSI Explorer révén azonnali vizualizációt biztosít.  
- A negyedik pedig, hogy a Time Series Insights lekérdezési szolgáltatást biztosít a TSI Explorerben, valamint az idősorozat-adatok egyéni alkalmazásokba való beágyazásához könnyen integrálható API-k használatával is.  

Ha létrehoz egy alkalmazást akár belső használatra, akár külső ügyfeleknek, a Time Series Insights használható a háttérben az idősorozat-adatok indexálására, tárolására és összesítésére. Így egyéni vizualizációt és felhasználói élményt hozhat létre.  A Time Series Insights lekérdezési API-kat tesz elérhetővé ennek a forgatókönyvnek az engedélyezéséhez.  

Ha nem biztos abban, hogy az adatai idősorozatok-e, a következőket kell tudnia.  Az idősorozat-adatok mutatják be, hogy hogyan változik az idők során egy adategység vagy folyamat.  Ez abban egyedi, hogy időbélyeggel rendelkezik, és az idő tengelyként a legértelmezhetőbb.  Az idősorozat-adatok általában időrendben érkeznek, és rendszerint inkább beszúrásként vannak kezelve, mint adatbázis-frissítésként.  Mivel a Time Series Insights sorként rögzít és tárol minden új eseményt, a változások mérése idő alapján történik, így lehetőség van visszanézni és megjósolni a jövőbeli változásokat.  Az idősorozat-adatok tárolása, indexelése, lekérdezése, elemzése és vizualizálása nagy mennyiségben kihívást jelenthet.  

## <a name="video"></a>Videó

Ez a videó a felhőalapú IoT elemző platformot, a Time Series Insights-ot mutatja be.

> [!VIDEO https://youtube.com/embed/GaARrFfjoss]

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

- Skálázható módon tárolhat idősorozat-adatokat.  
  - A Time Series Insights lényegében egy idősorozat-adatokra tervezett adatbázis.  A Time Series Insights, mivel skálázható és teljes mértékben felügyelt, kezeli az események tárolásának és felügyeletének feladatát.

- Közel valós idejű adatfeltárás.  
  - A Time Series Insights biztosít egy Explorer eszközt, amely megjelenít egy adott környezetbe streamelt minden adatot.  Egy eseményforrás csatlakoztatása után nem sokkal az eseményadatok megtekinthetők, feltárhatók és lekérdezhetők a Time Series Insightson belül.  Ezek az adatok hasznosak, akár azt kell értékelni, hogy egy eszköz az elvárt módon hoz-e létre adatokat, vagy monitorozni kell egy IoT-objektum állapotát, eredményességét és általános hatékonyságát.  

- Kiváltó okokat kereső elemzések és rendellenesség-észlelés.
  - A Time Series Insights olyan eszközökkel visz végbe és ment többlépéses kiváltóok-kereső elemzést, mint a minták és a perspektívanézetek.  Ezen felül a Time Series Insights együttműködik olyan riasztási szolgáltatásokkal, mint az Azure Stream Analytics, így a riasztások és az észlelt rendellenességek közel valós időben tekinthetők meg a Time Series Insights Explorerben.  

- Különböző helyekről streamelt idősorozat-adatok globális nézete több objektum/hely összehasonlításához.
  - Egy Time Series Insights környezethez több eseményforrás is csatlakoztatható.  Ez azt jelenti, hogy a több, különböző helyről streamelt adatok egyszerre tekinthetők meg közel valós időben.  A felhasználók kihasználhatják ezt a láthatóságot, és megoszthatják az adatokat a vezetőkkel, hatékonyabb együttműködést alakíthatnak ki tartományszakértőkkel, akik a szakértelmükkel segíthetnek a problémák megoldásában, az ajánlott eljárások alkalmazásában és az eredmények megosztásában.

- Ügyfélalkalmazás létrehozása a Time Series Insightson. 
  - A Time Series Insights REST lekérdezési API-kat tesz elérhetővé annak érdekében, hogy idősorozat-adatokat használó alkalmazásokat hozhasson létre.

## <a name="capabilities"></a>Funkciók

- **Gyors kezdés:** Az Azure Time Series Insights használatához nem szükséges adatok előzetes előkészítése. Percek alatt csatlakozhat több millió Azure IoT Hub- vagy Event Hub-eseményhez. A csatlakozás után megjelenítheti és kezelheti az érzékelők adatait, így gyorsan ellenőrizheti IoT-megoldásait. Az adatok kezeléséhez egyáltalán nem szükséges kódolás.
Nem kell megtanulnia egy új programozási nyelvet; a Time Series Insights részletes, szabad szöveges lekérdezési felületet biztosít a tapasztalt felhasználóknak, illetve egy néhány kattintással felfedezhető felületet kínál.
- **Közel valós idejű információkat:** A Time Series Insights is képes feldolgozni képes feldolgozni naponta egy perces késéssel. A Time Series Insights segítségével betekintést nyerhet az érzékelőadatokba, mivel lehetővé teszi a trendek és rendellenességek észlelését, a kiváltó okokat kereső elemzések elvégzését és a költséges leállási idő elkerülését. A valós idejű és az előzményadatok összevetésének biztosításával a Time Series Insights segít felderíteni az adataiban észlelhető rejtett trendeket.
- **Egyéni megoldásokat:** Az Azure Time Series Insights-adatok beágyazása saját alkalmazásait vagy új egyéni megoldásokat hozhat létre Time Series Insights REST API-k. Személyre szabott nézeteket hozhat létre, hogy mások is megtekinthessék a felfedezett összefüggéseket.
- **Méretezhetőség:** A Time Series Insights célja nagy mennyiségű IoT támogatja. Napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. Közel valós időben jelenítheti meg és elemezheti az élő adatstreameket és az előzményadatokat. A későbbi verziókban a bejövőadat-kezelési és adatmegőrzési kapacitást növelni fogjuk a nagyvállalati igényeknek megfelelően.

## <a name="getting-started"></a>Első lépések
Az első lépések 5 percnél kevesebb időt vesznek igénybe. 

1.  Első lépésként építsen ki egy Time Series Insights-környezetet az Azure Portalon. 
2.  Csatlakoztasson egy olyan eseményforrást, mint az Azure IoT Hub vagy Event Hub.  
3.  Töltsön fel referenciaadatokat (ez nem egy további szolgáltatás).
4.  A Time Series Insights Explorerrel néhány percen belül megtekintheti az adatait.

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer
Ez az ábra szemlélteti a time series insights-adatok a explorer tekinthetők meg: ![Time Series Insights explorer](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>További lépések
 - [Adatok feltárása a Time Series Insights Explorerrel egy bemutató környezetben](./time-series-quickstart.md)
 - [Saját Time Series Insights-környezet tervezése](time-series-insights-environment-planning.md)

