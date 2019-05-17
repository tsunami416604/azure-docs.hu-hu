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
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 85ce1748ee6c68ac96436b353d9240a22f8a479a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826389"
---
# <a name="what-is-azure-time-series-insights"></a>Mi az Azure Time Series Insights?

Az Azure Time Series Insights tárolására, vizualizációja és lekérdezése az idősorozat-adatok, például az IoT-eszközök által generált nagy mennyiségű épül.  Ha idősorozat-adatokat szeretne a felhőben tárolni, kezelni, lekérdezni vagy vizualizálni, a Time Series Insights valószínűleg jó választás.  

![Time Series Insights folyamatábra](media/overview/time-series-insights-flowchart.png)

A Time Series Insights négy fő feladata:

- Teljes körűen integrálva van az felhőalapú átjárókkal, mint például az Azure IoT Hub és az Azure Event Hubs. Könnyedén csatlakozik ezekhez az eseményforrásokhoz, és elemzi az adatokat egyszerű sorokban és oszlopokban tároló üzenetekből és struktúrákból származó JSON-t. A metaadatokat összeköti a telemetriával, és egy oszlopos tárban indexálja az adatait.
- A Time Series Insights kezeli az adatok tárolására. Annak érdekében, hogy az adatok mindig könnyen hozzáférhetők legyenek, az adatokat a memóriában és SSD-n tárolja akár 400 napig. Interaktívan kérdezheti le események milliárdjait másodpercek alatt – amikor szükséges.
- A Time Series Insights-a-beépített vizualizációt a Time Series Insights explorer keresztül biztosít.  
- A Time Series Insights lekérdezési szolgáltatást, a Time Series Insights explorer és könnyen integrálhatja az idősoros adatokat egyéni alkalmazásokba történő beágyazáshoz API-k használatával is biztosít.  

Ha létrehoz egy alkalmazást akár belső használatra, akár külső ügyfeleknek, a Time Series Insights használható a háttérben az idősorozat-adatok indexálására, tárolására és összesítésére. A felső használatával hozhat létre egy egyéni vizualizációt és a felhasználói élmény a [ügyfél-SDK](tutorial-explore-js-client-lib.md). A Time Series Insights is ellátott több [lekérdezési API-k](how-to-shape-query-json.md) testreszabott ahhoz, hogy ezek a forgatókönyvek.  

Az idősorozat-adatok mutatják be, hogy hogyan változik az idők során egy adategység vagy folyamat. Idősorozat-adatok ezért indexeli időbélyegeket és idő a legtöbb jelentéssel bíró tengely mentén, amely az ilyen adatok vannak rendszerezve. Idősorozat-adatok általában egymást követő sorrendben érkeznek, és általában számít egy frissítést az adatbázis helyett a Beszúrás eredményeként.

Az idősorozat-adatok tárolása, indexelése, lekérdezése, elemzése és vizualizálása nagy mennyiségben kihívást jelenthet.
Azonban az Azure Time Series Insights rögzíti, és minden új esemény tárolja egy-egy sornak és változás hatékonyan mérése idővel, lehetővé téve a visszamenőleges keresse meg a hasznos információkhoz juthat az elmúlt és előre jelezni a jövőbeli módosítása.

## <a name="video"></a>Videó

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>További információ az Azure Time Series Insights, a felhőalapú IoT-elemzési platform.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

- Skálázható módon tárolhat idősorozat-adatokat.  

   A Time Series Insights lényegében egy idősorozat-adatokra tervezett adatbázis.  A Time Series Insights, mivel skálázható és teljes mértékben felügyelt, kezeli az események tárolásának és felügyeletének feladatát.

- Közel valós idejű adatfeltárás.  

   A Time Series Insights biztosít egy Explorer eszközt, amely megjelenít egy adott környezetbe streamelt minden adatot.  Egy eseményforrás csatlakoztatása után nem sokkal az eseményadatok megtekinthetők, feltárhatók és lekérdezhetők a Time Series Insightson belül.  Ezek az adatok hasznosak, akár azt kell értékelni, hogy egy eszköz az elvárt módon hoz-e létre adatokat, vagy monitorozni kell egy IoT-objektum állapotát, eredményességét és általános hatékonyságát.  

- Kiváltó okokat kereső elemzések és rendellenesség-észlelés.

   A Time Series Insights olyan eszközökkel visz végbe és ment többlépéses kiváltóok-kereső elemzést, mint a minták és a perspektívanézetek.  Ezen felül a Time Series Insights együttműködik olyan riasztási szolgáltatásokkal, mint az Azure Stream Analytics, így a riasztások és az észlelt rendellenességek közel valós időben tekinthetők meg a Time Series Insights Explorerben.  

- Különböző helyekről streamelt idősorozat-adatok globális nézete több objektum/hely összehasonlításához.

   Egy Time Series Insights környezethez több eseményforrás is csatlakoztatható.  Ez azt jelenti, hogy a több, különböző helyről streamelt adatok egyszerre tekinthetők meg közel valós időben.  A felhasználók kihasználhatják ezt a láthatóságot, és megoszthatják az adatokat a vezetőkkel, hatékonyabb együttműködést alakíthatnak ki tartományszakértőkkel, akik a szakértelmükkel segíthetnek a problémák megoldásában, az ajánlott eljárások alkalmazásában és az eredmények megosztásában.

- Ügyfélalkalmazás létrehozása a Time Series Insightson. 

   A Time Series Insights REST lekérdezési API-kat tesz elérhetővé annak érdekében, hogy idősorozat-adatokat használó alkalmazásokat hozhasson létre.

## <a name="capabilities"></a>Képességek

- **Gyorsan használatba vehető**: Az Azure Time Series Insights használatához nem szükséges adatok előzetes előkészítése. Percek alatt csatlakozhat több millió Azure IoT Hub- vagy Event Hub-eseményhez. A csatlakozás után megjelenítheti és kezelheti az érzékelők adatait, így gyorsan ellenőrizheti IoT-megoldásait. Az adatok kezeléséhez egyáltalán nem szükséges kódolás.
Nem kell megtanulnia egy új programozási nyelvet; a Time Series Insights részletes, szabad szöveges lekérdezési felületet biztosít a tapasztalt felhasználóknak, illetve egy néhány kattintással felfedezhető felületet kínál.

- **Közel valós idejű elemzés**: A Time Series Insights is képes feldolgozni képes feldolgozni naponta egy perces késéssel. A Time Series Insights segítségével betekintést nyerhet az érzékelőadatokba, mivel lehetővé teszi a trendek és rendellenességek észlelését, a kiváltó okokat kereső elemzések elvégzését és a költséges leállási idő elkerülését. A valós idejű és az előzményadatok összevetésének biztosításával a Time Series Insights segít felderíteni az adataiban észlelhető rejtett trendeket.

- **Egyéni megoldások készítése**: Az Azure Time Series Insights-adatok beágyazása saját alkalmazásait vagy új egyéni megoldásokat hozhat létre Time Series Insights REST API-k. Személyre szabott nézeteket hozhat létre, hogy mások is megtekinthessék a felfedezett összefüggéseket.

- **Méretezhetőség**: A Time Series Insights célja nagy mennyiségű IoT támogatja. Napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. Közel valós időben jelenítheti meg és elemezheti az élő adatstreameket és az előzményadatokat. A későbbi verziókban a bejövőadat-kezelési és adatmegőrzési kapacitást növelni fogjuk a nagyvállalati igényeknek megfelelően.

## <a name="getting-started"></a>Első lépések

Első lépések kevesebb mint öt percet vesz igénybe.

1. Első lépésként építsen ki egy Time Series Insights-környezetet az Azure Portalon.
1. Csatlakoztasson egy olyan eseményforrást, mint az Azure IoT Hub vagy Event Hub.  
1. Töltsön fel referenciaadatokat (ez nem egy további szolgáltatás).
1. A Time Series Insights Explorerrel néhány percen belül megtekintheti az adatait.

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

Ez az ábra szemlélteti a time series insights-adatok a explorer tekinthetők meg: ![Time Series Insights explorer](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>További lépések

- Ismerkedés az Azure Time Series Insights nyilvánosan [ingyenes bemutató környezetben](./time-series-quickstart.md).

- Tudjon meg többet [tervezése a Time Series Insights](time-series-insights-environment-planning.md) környezetben.
