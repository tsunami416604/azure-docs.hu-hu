---
title: 'Áttekintés: Mi az Azure Time Series Insights? | Microsoft Docs'
description: Bevezetés az idősorozat-adatok elemzéséhez és IoT-megoldásokhoz készült új Azure Time Series Insights szolgáltatás használatába.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 6ef965b9c22524df5893d5826548a0b07f077062
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237637"
---
# <a name="what-is-azure-time-series-insights"></a>Mi az Azure Time Series Insights?

Az Azure Time Series Insights tárolására, megjelenítését és lekérdezése az idősorozat-adatok, például az IoT-eszközök által generált nagy mennyiségű való használatra készült. Ha idősorozat-adatokat szeretne a felhőben tárolni, kezelni, lekérdezni vagy vizualizálni, a Time Series Insights valószínűleg jó választás. 

![Time Series Insights folyamatábra](media/overview/time-series-insights-flowchart.png)

A Time Series Insights négy fő feladata:

- Teljes körűen integrálva van az felhőalapú átjárókkal, mint például az Azure IoT Hub és az Azure Event Hubs. Könnyedén csatlakozik ezekhez az eseményforrásokhoz, és elemzi az adatokat egyszerű sorokban és oszlopokban tároló üzenetekből és struktúrákból származó JSON-t. A metaadatokat összeköti a telemetriával, és egy oszlopos tárban indexálja az adatait.
- A Time Series Insights kezeli az adatok tárolására. Győződjön meg arról, hogy az adatok mindig könnyen elérhető lesz, hogy tárolja az adatokat a memória- és SSD-k legfeljebb 400 napos. Interaktív módon lekérdezheti a másodperc – igény eseményeinek milliárdjait.
- A Time Series Insights-a-beépített vizualizációt a Time Series Insights explorer keresztül biztosít. 
- A Time Series Insights lekérdezési szolgáltatást, a Time Series Insights explorer és az API-k egyszerűen ágyazhat be az idősoros adatokat az egyéni alkalmazások integrálhatók is biztosít.

Ha belső használat vagy külső ügyfelek egy alkalmazást fejleszt, használhatja a Time Series Insights háttéradatbázisként. Index, tárolására és összesített idősorozat-adatok használhatja azt. Hozhat létre egy egyéni vizualizációt és a felhasználói élmény felül, használja a [ügyfél-SDK](tutorial-explore-js-client-lib.md). A Time Series Insights is ellátott több [lekérdezési API-k](how-to-shape-query-json.md) testreszabott ahhoz, hogy ezek a forgatókönyvek.

Az idősorozat-adatok mutatják be, hogy hogyan változik az idők során egy adategység vagy folyamat. Idősorozat-adatok időbélyegeket által indexelt, és időt a legtöbb jelentéssel bíró tengely mentén, amely az ilyen adatok vannak rendszerezve. Idősorozat-adatok általában egymást követő sorrendben érkezik, ezért általában az adatbázis frissítését, hanem egy insert kell kezelni.

Ez tárolni, index, lekérdezés, elemzése és megjelenítése nagy mennyiségű idősoros adatainak nagy kihívást jelenthet.
Az Azure Time Series Insights rögzíti, és minden új esemény tárolja egy-egy sornak, és idővel hatékonyan mérni a módosítás. Ennek eredményeképpen megkeresheti visszafelé a hasznos információkhoz juthat az elmúlt előre jelezni a jövőbeli módosítása érdekében.

## <a name="video"></a>Videó

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>További információ az Azure Time Series Insights, a felhőalapú IoT-elemzési platform.</br>

[![VIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Elsődleges forgatókönyvek

- Idősorozat-adatok Store méretezhető módon. 

   A Time Series Insights lényegében egy idősorozat-adatokra tervezett adatbázis. Mivel a méretezhető és teljes körűen felügyelt szolgáltatás, a Time Series Insights kezeli a munkahelyi és események kezelése.

- Fedezze fel az adatok közel valós időben. 

   A Time Series Insights egy tallózót biztosít, amely megjeleníti az összes adatot, hogy az adatfolyamok környezetbe. Röviddel eseményforrás csatlakozik, megtekintheti, ismerje meg, és belül a Time Series Insights esemény adatokat kérdezhet le. Az adatok segít megállapítani, hogy egy eszköz bocsát ki adatokat várt módon-e, és figyelheti az IoT eszköz állapotát, hatékonyságot és általános hatékonyságát. 

- Alapvető okok elemzését, és észlelje a rendellenességeket.

   A Time Series Insights rendelkezik, minták és a viselkedési szabályzattal, és mentse a többlépéses alapvető okok elemzése szempontjából nézeteket. A Time Series Insights is működik, a szolgáltatások, például az Azure Stream Analytics riasztási, így megtekintheti a riasztásokat és észlelt rendellenességeket a Time Series Insights Explorer közel valós időben. 

- Több eszköz vagy a hely összehasonlítás különböző helyekről streamelő idősorozat-adatokat tartalmazó globális nézet szerezhet.

   Egy Time Series Insights környezethez több eseményforrás is csatlakoztatható. Ezzel a módszerrel, amely több, különböző helyein együtt, a közel valós idejű elemzésének lehetőségeit adatokat is megtekintheti. Felhasználók kihasználhatja a láthatóságát Cégvezetők megoszthatják az adatokat. Akkor is együttműködhet jobban tartomány szakértők segítségével a problémák megoldásában, alkalmazza az ajánlott eljárások és tapasztalatainkat megosztani szakértelmüket alkalmazhat.

- A Time Series Insights épülő ügyfél-alkalmazás létrehozása. 

   A Time Series Insights REST lekérdezési API-kat használhatja az idősoros adatokat használó alkalmazások közzététele.

## <a name="capabilities"></a>Funkciók

- **Gyorsan használatba vehető**: Az Azure Time Series Insights nincs szükség előzetes adat-előkészítésre, így gyorsan csatlakozhat több millió eseményt az IoT hub-vagy event hub. Miután csatlakozott, megjelenítheti és kezelheti az érzékelők adatait, így gyorsan ellenőrizheti IoT-megoldásai biztonságát. Kód írása nélkül az adatok kezelheti, és nem kell egy új nyelv elsajátítása. A Time Series Insights egy részletes, szabad szöveges lekérdezési felületet biztosít a tapasztalt felhasználók számára, és mutasson, és néhány kattintással.

- **Közel valós idejű elemzés**: A Time Series Insights betöltheti képes feldolgozni naponta egy perces késéssel. A Time Series Insights segít az érzékelők adatainak mélyebb betekintést. Ezzel számára trendeket és rendellenességeket, az alapvető okok elemzését, és elkerülheti a költséges állásidőt. Valós idejű és előzményadatok közötti összefüggések segít a rejtett trendek kimutatásához az adatokat.

- **Egyéni megoldások készítése**: Az Azure Time Series Insights-adatok beágyazása saját alkalmazásait. Új egyéni megoldásokat a Time Series Insights REST API-kat hozhat létre. Személyre szabott nézeteket hozhat létre, hogy mások is megtekinthessék a felfedezett összefüggéseket.

- **Méretezhetőség**: A Time Series Insights célja nagy mennyiségű IoT támogatja. Napi 1–100 millió bejövő eseményt képes kezelni, és alapértelmezés szerint 31 napig őrzi meg ezeket. Megjelenítheti és elemzése mellett a régebbi adatok közel valós időben az élő adatfolyamokat.

## <a name="get-started"></a>Bevezetés

A kezdéshez kövesse az alábbi lépéseket.

1. Egy Time Series Insights-környezet az Azure Portalon üzembe helyezhető.
1. Például egy IoT hubot vagy egy event hub-eseményforrás csatlakozni. 
1. Töltse fel a referenciaadatoknál. Ez nem egy további szolgáltatást.
1. A Time Series Insights Explorerrel néhány percen belül megtekintheti az adatait.

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

Ez az ábra szemlélteti a time series insights-adatok a Time Series Insights explorer tekinthetők meg.

![Time Series Insights Explorer](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>További lépések

- Ismerkedés az Azure Time Series Insights nyilvánosan [ingyenes bemutató környezetben](./time-series-quickstart.md).
- Ismerje meg, hogyan [megtervezése a Time Series Insights](time-series-insights-environment-planning.md) környezetben.
