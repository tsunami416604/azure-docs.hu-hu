---
title: 'Áttekintés: Azure Time Series Insights előzetes verzió | Microsoft Docs'
description: Azure Time Series Insights előnézet áttekintése.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: e4a57c6d4a9034d9145058e9137759c2cea19aa6
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978672"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Mi az Azure Time Series Insights előzetes verziója?

A Azure Time Series Insights Preview egy teljes körű, szolgáltatásként nyújtott platformot kínál. A szolgáltatás a kifejezetten környezetfüggő, idősorozat-optimalizált IoT adatok gyűjtésére, feldolgozására, tárolására, elemzésére és lekérdezésére szolgál. Time Series Insights ideális az alkalmi adatfeltáráshoz és az operatív elemzésekhez. A Time Series Insights egy egyedileg bővíthető és testreszabott szolgáltatás, amely megfelel az ipari IoT üzemelő példányok széles körű igényeinek.

> [!TIP]
> Az általánosan elérhető szolgáltatások (GA) esetében olvassa el a [Azure Time Series INSIGHTS GA áttekintését](time-series-insights-overview.md).

## <a name="video"></a>Videó

### <a name="learn-more-about-azure-time-series-insights-preview-br"></a>További információ a Azure Time Series Insights előzetes verzióról. </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>IoT-adathalmazok definiálása

A IoT-alapú adatforrások olyan ipari adatmennyiségek, amelyek az erőforrás-igényes szervezetekben érhetők el. A IoT-adatok gyakran strukturálatlan, mert olyan eszközökről küldenek, amelyek meglehetősen zajos méréseket rögzítenek. Ezek a mérések közé tartoznak a hőmérséklet, a mozgás és a páratartalom. Ezeket az adatfolyamokat gyakran a jelentős hiányosságok, a sérült üzenetek és a hamis olvasások jellemzik. Az ilyen adatfolyamokból származó adatoknak bármilyen elemzés előtt meg kell tisztítaniuk.

A IoT-adatok gyakran csak az első féltől származó forrásokból, például a CRM-ből vagy az ERP-ből származó adatbevitelek kontextusában értelmezhető. A bemenetek harmadik féltől származó adatforrásokból, például időjárásból vagy helyről is származnak.

Ennek eredményeképpen a rendszer csak az adattöredéket fogja használni működési és üzleti célokra. Az ilyen adatok üzleti jelentéskészítési és elemzési egységes, átfogó, jelenlegi és megfelelő információkat nyújt. A begyűjtött IoT-adatok gyakorlatban használható információkhoz való bekapcsolásához a következőket kell tennie:

* Adatfeldolgozás az elemzések tisztításához, szűréséhez, interpolációhoz, átalakításához és előkészítéséhez.
* Egy struktúra, amellyel megkeresheti és értelmezheti az adatmennyiséget, vagyis az adatnormalizálás és a contextualize.
* Költséghatékony tárolás a hosszú vagy végtelen adatmegőrzéshez a feldolgozott, vagy származtatott, adatokból és nyers adatokból álló évtizedek esetében.

A következő képen látható egy tipikus IoT-adatfolyam.

  ![IoT-adatfolyam][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Az Azure Time Series Insights az ipari IoT

Az aktuális IoT fekvő változatos. Az ügyfelek a gyártás, a gépjármű, az energia, a segédprogramok, az intelligens épületek és a tanácsadó iparágak számára is kiterjedhetnek. A forgatókönyvek olyan ad hoc adatfeltárást tartalmaznak, amelyben az adatok alakja ismeretlen. A forgatókönyvek a sematikus-on keresztüli operatív elemzést, vagy kifejezetten modellezett, a működési hatékonyságot eredményező adatokra is kiterjednek. Ezek a forgatókönyvek általában egymás mellett találhatók, és támogatják a különböző használati eseteket. Az ipari IoT vállalatok és a digitális forradalom sikeréhez kulcsfontosságú platform-képességek a következők:

- Többrétegű tárolás, meleg és hideg.
- Lehetővé teszi az évtizedek közötti idősorozat-adatmennyiségek tárolását.
- Lehetővé teszi a lekérdezések explicit módon történő modellezését és optimalizálását az Asset-alapú operatív intelligenciához.

A Time Series Insights egy átfogó, teljes körű, IoT-alapú, az adatfeltárásra és az operatív elemzésre szolgáló, részletesen felkínált, teljes körűen használható A Time Series Insights teljes körűen felügyelt felhőalapú szolgáltatást kínál a IoT idősorozat-adatok elemzéséhez.

A nyers adatmennyiséget séma nélküli, memóriában tárolt tárolóban tárolhatja. Ezután interaktív ad hoc lekérdezéseket hajthat végre elosztott lekérdezési motoron és API-n keresztül. Használja ki a gazdag felhasználói élményt több milliárd esemény megjelenítéséhez másodpercek alatt. További információ az [adatfeltárási lehetőségekről](./time-series-insights-overview.md).

A Time Series Insights a jelenleg előzetes verzióban üzemelő, operatív bepillantást biztosító funkciókat is kínál. Az interaktív adatfeltárással és az operatív intelligenciával együtt a Time Series Insights segítségével több értéket is kinyerheti a IoT-eszközökről gyűjtött adatokból. Az előzetes verzió a következőket támogatja:

* Skálázható és teljesítmény-és költséghatékony idősorozat-adattár. Ebben a felhőalapú IoT-megoldásban másodpercek alatt elvégezhető az idősoros adatmennyiség.
* Szemantikai modell támogatása, amely leírja a származtatott és nem származtatott jelekhez kapcsolódó tartományokat és metaadatokat az eszközök és eszközök között.
* Fejlett felhasználói élmény, amely az eszköz-alapú adatelemzéseket gazdag, ad hoc adatelemzéssel ötvözi. Ez a kombináció az üzleti és operatív intelligenciát vezérli.
* Integráció a fejlett gépi tanulási és elemzési eszközökkel. Az eszközök közé tartoznak a Azure Databricks, a Apache Spark, a Azure Machine Learning, a Jupyter notebookok és a Power BI. Ezek az eszközök segítenek az idősoros adatokkal kapcsolatos problémák megoldásában és a működési hatékonyság növelésében.

Az adatfeldolgozáshoz, a tároláshoz és a lekérdezéshez egy egyszerű utólagos elszámolású díjszabási modell áll rendelkezésre az operatív elemzések és az adatfeltárási szolgáltatásokkal együtt. Ez a számlázási modell alkalmazkodik a változó üzleti igényekhez.

Ez a magas szintű Adatfolyam-diagram a frissítéseket mutatja.

  ![Főbb képességek][2]

Ezeknek a kulcsfontosságú ipari IoT képességeknek a bevezetésével a Time Series Insights a következő kulcsfontosságú előnyöket biztosítja.

| | |
| ---| ---|
| Többrétegű tárolás IoT-méretezési idősorozat-adatsorokhoz | Az adatfeldolgozáshoz használt közös adatfeldolgozási folyamattal az interaktív lekérdezések esetében a meleg tárolóban tárolhatja az adattárolást. Nagy mennyiségű adattal is tárolhatók az adattárolási tárolók. Használja ki a nagy teljesítményű, objektumon alapuló [lekérdezések](./time-series-insights-update-tsq.md)előnyeit. |
| Idősorozat-modell a nyers telemetria contextualize, és az eszköz-alapú adatfelismerések származtatása | Nyers telemetria-contextualize a leíró [Idősorozat-modellel](./time-series-insights-update-tsm.md). Hatékony operatív intelligenciát biztosít a nagyteljesítményű és költséghatékony eszköz-alapú lekérdezések alapján. |
| Zökkenőmentes és folyamatos integráció más adatmegoldásokkal | A Time Series Insightsban lévő adatforrások nyílt forráskódú Apache Parquet-fájlokban vannak [tárolva](./time-series-insights-update-storage-ingress.md) . Ez az integráció más adatmegoldásokkal – akár az első, akár a harmadik fél esetében – egyszerű a végpontok közötti forgatókönyvek esetében. Ilyen forgatókönyvek például az üzleti intelligencia, a fejlett gépi tanulás és a prediktív elemzések. |
| Közel valós idejű adatelemzés | A [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) felhasználói élmény vizualizációt biztosít a betöltési folyamaton keresztüli összes adatfolyamhoz. Röviddel az eseményforrás összekapcsolását követően megtekintheti, feltárhatja és lekérdezheti az események adatforrásait. Ily módon ellenőrizheti, hogy az eszköz az elvárt módon bocsát-e ki adatforrásokat. Egy IoT-eszközt is nyomon követheti az állapot, a termelékenység és az általános hatékonyság érdekében. |
| Kiváltó okok elemzése és anomália észlelése | A [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) támogatja a minták és perspektívák nézeteit is a többlépéses és a kiváltó okok elemzésének végrehajtásához és mentéséhez. A Azure Stream Analytics használatával kombinálva a riasztások és rendellenességek közel valós időben történő észleléséhez Time Series Insights használható. |
| Time Series Insights platformra épülő egyéni alkalmazások | A Time Series Insights támogatja a [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)-t. Az SDK-t biztosít a gazdag vezérlők és egyszerűen hozzáférhet a lekérdezéseket. Az SDK használatával egyéni IoT-alkalmazásokat hozhat létre Time Series Insights az adott üzleti igényeknek megfelelően. A Time Series Insights [lekérdezési API-kat](./time-series-insights-update-tsq.md) közvetlenül is használhatja az egyéni IoT-alkalmazásokba történő adattovábbításhoz. |

## <a name="next-steps"></a>További lépések

Ismerkedés a Azure Time Series Insights előzetes verziójával:

> [!div class="nextstepaction"]
> [A rövid útmutató elolvasása](./time-series-insights-update-quickstart.md)

További információ a használati esetek:

> [!div class="nextstepaction"]
> [Azure Time Series Insights előzetes verzió használati esetei](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview-one.png
[2]: media/v2-update-overview/overview-two.png
