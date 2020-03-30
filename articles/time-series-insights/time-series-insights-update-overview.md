---
title: 'Áttekintés: Mi az Azure Time Series Insights előzetes verzió? - Azure Time Series Insights | Microsoft dokumentumok'
description: Ismerje meg az Azure Time Series Insights előzetes verzió módosításait, fejlesztéseit és funkcióit.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1e4f72300752c93659db4edd4610464dbebf2503
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77014419"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Mi az Azure Time Series Insights előzetes verziója?

Az Azure Time Series Insights előzetes verziója egy végpontok között platform-szolgáltatásként (PaaS) ajánlat. Segítségével adatokat gyűjthet, dolgozhat fel, tárolhat, elemezhet és kérdezhet le az IoT-n – olyan adatokat, amelyek nagy mértékben környezetfüggők és idősorozatokra vannak optimalizálva. 

A Time Series Insights ad hoc adatok feltárására és operatív elemzésére készült. Ez egy bővíthető és személyre szabott szolgáltatás, amely megfelel az ipari IoT-telepítések széles körű igényeinek.

## <a name="video"></a>Videó

További információ az Azure Time Series Insights előzetes verziójáról.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Az IoT-adatok meghatározása

Az eszközigényes szervezetekipari IoT-adatai gyakran nem rendelkeznek szerkezeti konzisztenciával az eszközök és érzékelők ipari környezetben való eltérő jellege miatt. Az ezekből az adatfolyamokból származó adatokat jelentős hézagok és néha sérült üzenetek és téves olvasmányok jellemzik. Az IoT-adatok gyakran értelmezhetők az első vagy harmadik forrásokból származó további adatbevitelek, például a CRM vagy az ERP, amelyek a végpontok közötti munkafolyamatok környezetét bővítik. A külső adatforrásokból, például az időjárási adatokból származó bemenetek segíthetnek a telemetriai adatfolyamok bővítésében egy adott telepítésben. 

Mindez azt jelenti, hogy az adatoknak csak egy töredékét használja fel működési és üzleti célokra, és az elemzés környezetualizálást igényel. Az ipari adatokat gyakran történelmivé tették a részletesebb elemzéshez hosszabb időtartamokon keresztül, hogy megértsék és korrelálják a trendeket. Az összegyűjtött IoT-adatok működőképes elemzési adatokká alakítása a következőket igényli: 

* Adatfeldolgozás az adatok tisztításához, szűréséhez, interpolálásához, átalakításához és elemzésre való előkészítéséhez.
* Olyan struktúra, amely az adatok között navigálhat, és megértette az adatokat, azaz normalizálja és környezetfüggővé teszi az adatokat.
* Költséghatékony tárolás a feldolgozott (vagy származtatott) adatok és nyers adatok hosszú vagy végtelen megőrzéséhez.

Ezek az adatok konzisztens, átfogó, aktuális és helyes információkat szolgáltatnak az üzleti elemzéshez és jelentéskészítéshez.

Az alábbi képen egy tipikus IoT-adatfolyam látható.

[![IoT-adatfolyam](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights ipari IoT-hez

Az IoT-környezet számos iparági szegmenst ölel fel, beleértve a gyártást, az autóipart, az energiát, a közműveket, az intelligens épületeket és a tanácsadást. Az ipari IoT-piac széles skálája között még mindig fejlődnek a felhőalapú natív megoldások, amelyek átfogó, nagy méretű IoT-adatokat célzó elemzéseket nyújtanak. 

Az Azure Time Series Insights úgy elégíti ki ezt a piaci igényt, hogy kulcsrakész, végpontok között iot-elemzési megoldást kínál gazdag szemantikai modellezéssel az idősorozat-adatok, az eszközalapú elemzések és a kategóriájában legjobb felhasználói élmény környezet-modellezéséhez a felderítéshez, trend, anomáliadetektálás és operatív intelligencia. 

Az interaktív adatfeltárási képességeinkkel kombinált, gazdag működési elemzési platform segítségével a Time Series Insights segítségével több értéket nyerhet ki az IoT-eszközökből gyűjtött adatokból. Az előnézeti ajánlat támogatja: 

* Többrétegű tárolási megoldás meleg és hideg elemzési támogatással, amely lehetővé teszi az ügyfelek számára, hogy az adatokat meleg és hideg között irányítsák az interaktív elemzések és a több évtizedes múltbeli adatok felett. 

    *   Rendkívül interaktív, meleg elemzési megoldás a gyakori és nagy számú lekérdezés rövidebb időtartamú adatok végrehajtására 
    *   Skálázható, teljesítmény- és költségoptimalizált idősorozat-adattó az Azure Storage alapján, amely lehetővé teszi az ügyfelek számára, hogy másodpercek alatt trendeljék az évek óta eltelt idősorozat-adatokat. 

* Szemantikai modell támogatása, amely leírja a tartomány és a metaadatok társított származtatott és nyers jeleket az eszközök és eszközök.

* Rugalmas elemzési platform a korábbi idősorozat-adatok tárolására az ügyfél tulajdonában lévő Azure Storage-fiókban, így lehetővé teszi az ügyfelek számára, hogy az IoT-adatok tulajdonjogát. Az adatok nyílt forráskódú Apache Parketta formátumban tárolódnak, amely lehetővé teszi a kapcsolatot és az interop-ot a különböző adatforgatókönyvekben, beleértve a prediktív elemzést, a gépi tanulást és más egyéni számításokat, amelyek et ismerős technológiákkal, például a Sparkkal végeznek, Databricks és Jupyter.

* Gazdag analitika továbbfejlesztett lekérdezési API-kkal és felhasználói felülettel, amely egyesíti az eszközalapú adatelemzéseket a gazdag, ad hoc adatelemzéssel az interpolációs, skaláris és összesítő függvények, a kategorikus változók, a scatter-parcellák és az időeltolódás támogatása segítségével sorozatjeleket a mélyreható elemzéshez.

*   Nagyvállalati szintű platform, amely támogatja nagyvállalati IoT-ügyfeleink méretezési, teljesítmény-, biztonsági és megbízhatósági igényeit.

* Bővíthetőség és integrációs támogatás a végpontok közötti elemzéshez. A Time Series Insights bővíthető elemzési platformot biztosít a különböző adatforgatókönyvekhez. A Time Series Insights Power BI-összekötő lehetővé teszi az ügyfelek számára, hogy a Time Series Insights-ban leadott lekérdezéseket közvetlenül a Power BI-ba vigyék, hogy egyetlen ablaktáblában egységes képet kapjanak üzletiintelligencia- és idősorozat-elemzésükről.

Az alábbi ábra a magas szintű adatfolyamot mutatja be.

  [![Főbb képességek](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Az Azure Time Series Insights skálázható használatalapú díjszabási modellt biztosít az adatfeldolgozáshoz, a tároláshoz (adatokhoz és metaadatokhoz) és a lekérdezéshez, lehetővé téve az ügyfelek számára, hogy az üzleti igényeiknek megfelelően hangolják be használatukat. 
 
Ezeknek a kulcsfontosságú ipari IoT-képességeknek a bevezetésével a Time Series Insights a következő kulcsfontosságú előnyöket is biztosítja.  

| | |
| ---| ---|
| Többrétegű tárolás Az IoT-skálán idősorozat-adatokhoz | Az adatok betöltéséhez megosztott adatfeldolgozási folyamattal az adatok at meleg és hideg tárolókba is beviheti. Használja a warm store-t az interaktív lekérdezésekhez és a hűtőtárolót a nagy mennyiségű adat tárolásához. Ha többet szeretne tudni arról, hogy miként használhatja ki a jól teljesítő eszközalapú lekérdezéseket, olvassa el a lekérdezések című [témakört.](./time-series-insights-update-tsq.md) |
| Time Series Model a nyers telemetria környezetezéséhez és eszközalapú elemzések származtatására | Az idősorozat-modell segítségével példányokat, hierarchiákat, típusokat és változókat hozhat létre az idősorozat-adatokhoz. Ha többet szeretne megtudni a Time Series Model modellről, olvassa el a Time Series Model című [témakört.](./time-series-insights-update-tsm.md)  |
| Zökkenőmentes és folyamatos integráció más adatmegoldásokkal | A Time Series Insights hűtőtárolóban lévő adatok nyílt forráskódú Apache Parquet-fájlokban [tárolódnak.](./time-series-insights-update-storage-ingress.md) Ez lehetővé teszi az adatok integrálását más adatmegoldásokkal, első vagy harmadik féllel, olyan forgatókönyvek esetén, mint az üzleti intelligencia, a fejlett gépi tanulás és a prediktív elemzés. |
| Közel valós idejű adatfeltárás | Az [Azure Time Series Insights előzetes verziójának kezelője](./time-series-insights-update-explorer.md) felhasználói felület e-betöltési folyamaton keresztül történő összes adatstreameléshez biztosít vizualizációt. Az eseményforrás csatlakoztatása után megtekintheti, felfedezheti és lekérdezheti az eseményadatokat. Ily módon ellenőrizheti, hogy egy eszköz a várt módon bocsát-e ki adatokat. Az IoT-eszközök az állapot, a termelékenység és az általános hatékonyság figyelheti. | 
| Bővíthetőség és integráció | Az Azure Time Series Insights Power BI Connector integrációközvetlenül elérhető a Time Series Explorer felhasználói élményében az **Exportálás** lehetőségen keresztül, így az ügyfelek a felhasználói élményben létrehozott idősorozat-lekérdezéseket közvetlenül a Power BI-asztalra exportálhatják, és más üzletiintelligencia-elemzésekkel együtt megtekinthetik az idősorozat-diagramjaikat. Ez megnyitja az utat a Power BI-ba beruházó ipari IoT-vállalatok számára a forgatókönyvek új osztálya előtt azáltal, hogy egyetlen üvegpanelt biztosít a különböző adatforrásokból, köztük az IoT-idősorokból származó elemzések felett. | 
| A Time Series Insights platformra épülő egyéni alkalmazások | A Time Series Insights támogatja a [JavaScript SDK-t.](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) Az SDK gazdag vezérlőket és egyszerűsített hozzáférést biztosít a lekérdezésekhez. Az SDK segítségével egyéni IoT-alkalmazásokat hozhat létre a Time Series Insights mellett, hogy megfeleljen az üzleti igényeinek. A Time Series Insights [query API-k](./time-series-insights-update-tsq.md) at közvetlenül is használhatja az egyéni IoT-alkalmazásokba való adathoz. |

## <a name="next-steps"></a>További lépések

Ismerkedés az Azure Time Series Insights előzetes verziójával:

> [!div class="nextstepaction"]
> [Rövid útmutató](./time-series-insights-update-quickstart.md)

További információ a használati esetekről:

> [!div class="nextstepaction"]
> [Az Azure Time Series Insights előzetes használati esetei](./time-series-insights-update-use-cases.md)