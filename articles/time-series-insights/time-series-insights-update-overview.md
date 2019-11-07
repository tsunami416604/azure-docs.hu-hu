---
title: 'Áttekintés: Azure Time Series Insights előzetes verzió | Microsoft Docs'
description: Azure Time Series Insights előnézet áttekintése.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: 05f4c64adad184e761e2b5a01b8c033c024dbc49
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585257"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Mi az Azure Time Series Insights előzetes verziója?

A Azure Time Series Insights Preview egy teljes körű, szolgáltatásként nyújtott platformot kínál. Felhasználhatja az adatok összegyűjtését, feldolgozását, tárolását, elemzését és lekérdezését eszközök internetes hálózata (IoT) skálán, amely nagy mértékben kontextusban van, és az idősorozatra optimalizált. 

Time Series Insights az ad hoc adatfeltárási és-üzemeltetési elemzésekhez készült. Ez egy bővíthető és testreszabott szolgáltatás, amely megfelel az ipari IoT üzemelő példányok széles körű igényeinek.

## <a name="video"></a>Videó

További információ a Azure Time Series Insights előzetes verzióról.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>A IoT-adatmennyiség definíciója

Az IoT-alapú szervezetekben található ipari adatmennyiségek gyakran nem rendelkeznek strukturális konzisztencia-ellenőrzéssel az eszközök és érzékelők különböző természete miatt egy ipari környezetben. A streamek adatait jelentős hiányosságok jellemzik, és néha sérültek az üzenetek, illetve a hamis beolvasás. A IoT adatok gyakran hasznosak az olyan adatbevitelek kontextusában, amelyek az első féltől származó vagy harmadik forrásból származnak, mint például a CRM vagy az ERP, amely a végpontok közötti munkafolyamatok kontextusát adja hozzá. A harmadik féltől származó adatforrásokból, például időjárási adatokból származó bemenetek segíthetnek a telemetria-adatfolyamok bővítésében egy adott telepítésben. 

Mindez azt jelenti, hogy a rendszer csak az adat egy részét használja fel működési és üzleti célokra, és az elemzéshez contextualization szükséges. Az ipari jellegű adatelemzések gyakran a hosszabb ideig elhelyezkedő, részletes elemzésre szolgálnak a trendek megismerése és korrelálása érdekében. A begyűjtött IoT-adatok gyakorlatban használható információkhoz való bekapcsolásához a következőket kell tennie: 

* Adatfeldolgozás az elemzések tisztításához, szűréséhez, interpolációhoz, átalakításához és előkészítéséhez.
* Egy struktúra, amellyel navigálhat és megismerheti az adatmennyiséget, vagyis az adatnormalizálás és a contextualize.
* Költséghatékony tárolás a feldolgozott (vagy származtatott) adatok és nyers adatok hosszú vagy végtelen megőrzéséhez.

Ezek az adatok konzisztens, átfogó, aktuális és helyes információkat biztosítanak az üzleti elemzéshez és jelentéskészítéshez.

Az alábbi képen egy jellemző IoT-adatfolyam látható.

[![IoT-adatfolyam](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights ipari IoT

A IoT-környezet különböző iparági szegmenseket ölel fel, többek között a gyártást, az autóipart, az energiát, a segédprogramokat, az intelligens épületeket és a tanácsadást. Az ipari IoT-piac ezen széles skáláján olyan Felhőbeli natív megoldások jelennek meg, amelyek átfogó elemzéseket biztosítanak a nagy léptékű IoT-adatmennyiségekről. 

Azure Time Series Insights a piaci igényeket úgy, hogy kulcsrakész, teljes körű IoT elemzési megoldást biztosítanak, amely részletes szemantikai modellezést biztosít az idősoros adatmennyiségek, az contextualization-elemzések és a felderítési legjobb felhasználói élmény érdekében, trendek, anomáliák észlelése és operatív intelligencia. 

Az interaktív adatfeltárási képességekkel kombinálva gazdag működési elemzési platformot használhat Time Series Insights segítségével a IoT-eszközökről gyűjtött adatokból több értéket is Kinyer. Az előzetes verzió a következőket támogatja: 

* A többrétegű tárolási megoldás a meleg és a hideg elemzés támogatásával lehetővé teszi az ügyfelek számára, hogy az adatok a meleg és a hideg közötti interaktív elemzéshez, valamint az operatív intelligencia több évtizedes adathoz való átirányítására is lehetőséget biztosítanak. 

    *   Egy nagymértékben interaktív, meleg elemzési megoldás, amely gyakori és nagy számú lekérdezést hajt végre a rövidebb idő alatt. 
    *   Az Azure Storage-on alapuló skálázható, teljesítményű és költséges idősorozat-adathalmaz, amely lehetővé teszi az ügyfelek számára, hogy másodpercek alatt is elvégezzék az idősorozat-adatok időbeli alakulását. 

* Szemantikai modell támogatása, amely az eszközök és eszközök származtatott és nyers jeleihez társított tartományt és metaadatokat ismerteti.

* Rugalmas elemzési platform a korábbi idősorozat-adatmennyiségek tárolásához az ügyfél tulajdonában lévő Azure Storage-fiókban, ami lehetővé teszi, hogy az ügyfelek tulajdonjogot IoT. Az adatok tárolása a nyílt forráskódú Apache Parquet formátumban történik, amely lehetővé teszi a különböző adatforgatókönyvek, például a prediktív elemzés, a gépi tanulás és más, ismerős technológiákkal, például a Spark használatával végzett egyéni számítások használatát. Databricks és Jupyter.

* Gazdag elemzési funkciókkal bővített lekérdezési API-kkal és felhasználói élménysel, amely az eszközökön alapuló adatelemzéseket gazdag, ad hoc adatelemzéssel ötvözi a interpoláció, a skaláris és az összesítő függvények, a kategorikus változók, a pontdiagram és az időbeli eltolódások támogatásával. adatsorozat-jelek részletes elemzéséhez.

*   Nagyvállalati szintű platform, amely támogatja a nagyvállalati IoT-ügyfelek méretezési, teljesítménybeli, biztonsági és megbízhatósági igényeit.

* Bővíthetőség és integrációs támogatás a végpontok közötti elemzésekhez. A Time Series Insights bővíthető elemzési platformot biztosít számos különböző típusú adatforgatókönyvhöz. A Time Series Insights Power BI-összekötő lehetővé teszi, hogy az ügyfelek a Time Series Insights közvetlenül a Power BIba hajtsák végre a BI-és idősorozat-elemzések egységes áttekintését egyetlen ablaktáblán.

A következő ábrán a magas szintű adatfolyam látható.

  [![kulcsfontosságú képességek](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

A Azure Time Series Insights méretezhető utólagos elszámolású díjszabási modellt biztosít az adatfeldolgozáshoz, a tároláshoz (adatokhoz és metaadatokhoz) és a lekérdezésekhez, így az ügyfelek az üzleti igényeknek megfelelően hangolják be a használatukat. 
 
Ezeknek a kulcsfontosságú ipari IoT képességeknek a bevezetésével a Time Series Insights a következő kulcsfontosságú előnyöket nyújtja.  

| | |
| ---| ---|
| Többrétegű tárolás IoT-méretezési idősorozat-adatsorokhoz | Egy megosztott adatfeldolgozási folyamattal az adatbevitelhez a meleg és a hűtőházi tárolóba is betöltheti az adatmennyiséget. A nagyméretű adatmennyiségek tárolásához használjon meleg tárolót interaktív lekérdezésekhez és hideg tároláshoz. Ha többet szeretne megtudni arról, hogyan használhatja ki a nagy teljesítményű eszközökön alapuló lekérdezéseket, tekintse meg a [lekérdezéseket](./time-series-insights-update-tsq.md). |
| Idősorozat-modell a nyers telemetria contextualize, és az eszköz-alapú adatfelismerések származtatása | Az idősorozat-modell használatával példányokat, hierarchiákat, típusokat és változókat hozhat létre az idősoros adataihoz. Ha többet szeretne megtudni a Time Series-modellről, tekintse meg a [Time Series Model](./time-series-insights-update-tsm.md)című témakört.  |
| Zökkenőmentes és folyamatos integráció más adatmegoldásokkal | Time Series Insights hűtőházi tárolóban tárolt adattárakat a nyílt forráskódú Apache Parquet-fájlok [tárolják](./time-series-insights-update-storage-ingress.md) . Ez lehetővé teszi az adatintegrációt más adatmegoldásokkal (1. vagy harmadik felekkel) olyan forgatókönyvek esetén, amelyek üzleti intelligenciát, speciális gépi tanulást és prediktív elemzést tartalmaznak. |
| Közel valós idejű adatelemzés | A [Azure Time Series Insights Preview Explorer](./time-series-insights-update-explorer.md) felhasználói élmény vizualizációt biztosít a betöltési folyamaton keresztüli összes adatfolyamhoz. Az eseményforrás összekapcsolását követően megtekintheti, feltárhatja és lekérdezheti az események adatforrásait. Ily módon ellenőrizheti, hogy az eszköz az elvárt módon bocsát-e ki adatforrásokat. Egy IoT-eszközt is nyomon követheti az állapot, a termelékenység és az általános hatékonyság érdekében. | 
| Bővíthetőség és integráció | Az Azure Time Series Insights Power BI Connector integrációja közvetlenül a Time Series Explorer felhasználói felületén érhető el az **Exportálás** lehetőséggel, így az ügyfelek a felhasználói élményben létrehozott idősorozat-lekérdezések exportálását is lehetővé teszik. a Power BI asztalra, és megtekintheti az idősorozat-diagramokat más BI-elemzések mellett. Ez megnyitja az ajtót egy új forgatókönyvnek az olyan ipari IoT vállalatok számára, akik befektettek a Power BIba a különböző adatforrásokból, például a IoT-sorozatokból származó elemzések egyetlen paneljét biztosítva. | 
| A Time Series Insights platformra épülő egyéni alkalmazások | A Time Series Insights támogatja a [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)-t. Az SDK sokoldalú vezérlőket és egyszerűsített hozzáférést biztosít a lekérdezésekhez. Az SDK használatával egyéni IoT-alkalmazásokat hozhat létre Time Series Insights az üzleti igényeinek megfelelően. A Time Series Insights [lekérdezési API-kat](./time-series-insights-update-tsq.md) közvetlenül is használhatja az egyéni IoT-alkalmazásokba történő adattovábbításhoz. |

## <a name="next-steps"></a>További lépések

Ismerkedés a Azure Time Series Insights előzetes verziójával:

> [!div class="nextstepaction"]
> [Útmutató a gyors üzembe helyezéshez](./time-series-insights-update-quickstart.md)

Tudnivalók a használati esetekről:

> [!div class="nextstepaction"]
> [Azure Time Series Insights előzetes verzió használati esetei](./time-series-insights-update-use-cases.md)