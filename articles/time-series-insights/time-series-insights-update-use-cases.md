---
title: Azure Time Series Insights előzetes verzió használati esetei | Microsoft Docs
description: Ismerje meg Azure Time Series Insights előzetes verzió használati eseteit.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: 437f93e35040a93fb0c3098d6ec53883c3f856a7
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553264"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights előzetes verzió használati esetei

Ez a cikk a Azure Time Series Insights előzetes verziójának számos gyakori felhasználási esetét összegzi. A cikkben szereplő javaslatok kiindulási pontként szolgálnak az alkalmazások és megoldások fejlesztéséhez Time Series Insights használatával.

Ez a cikk a következő kérdésekre ad választ:

* Melyek a Time Series Insights gyakori használati esetei?
* Milyen előnyökkel jár a Time Series Insights használata az [adatelemzési és a vizualizációs rendellenességek észleléséhez](#data-exploration-and-visual-anomaly-detection)?
* Milyen előnyökkel jár a Time Series Insights használata az [operatív elemzéshez és a folyamatok hatékonyságához](#operational-analysis-and-driving-process-efficiency)?
* Milyen előnyökkel jár a [speciális elemzések](#advanced-analytics)Time Series Insights használata?

A következő szakaszokban ismertetjük a használati forgatókönyvek áttekintését.

## <a name="introduction"></a>Introduction (Bevezetés)

Azure Time Series Insights egy teljes körű, szolgáltatásként nyújtott platformot kínál. A szolgáltatás a kifejezetten környezetfüggő, idősorozat-optimalizált IoT adatok gyűjtésére, feldolgozására, tárolására, elemzésére és lekérdezésére szolgál. Time Series Insights ideális az alkalmi adatfeltáráshoz és az operatív elemzésekhez. A Time Series Insights egy egyedileg bővíthető, testreszabott szolgáltatás, amely megfelel az ipari IoT üzemelő példányok széles körű igényeinek.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Adatfeltárás és vizuális anomáliadetektálás

Több milliárd eseményt vizsgálhat meg és elemezhet azonnal, és feltárhatja a rendellenességeket, és megismerheti az adatok rejtett trendjeit. A Time Series Insights közel valós idejű teljesítményt nyújt az IoT- és DevOps-alapú elemzési számítási feladatoknál.

[![Data Explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A legtöbb ügyfél egyetért azzal, hogy az információk beszerzéséhez szükséges minimális idő az Time Series Insights egyik kiemelkedő funkciója:

* Az Time Series Insights nem igényel előzetes adatelőkészítést. 
* Az Azure-IoT Hub vagy az Azure-Event Hubs percek alatt csatlakozhat több milliárd eseményhez. 
* A csatlakozást követően több milliárd eseményt jeleníthet meg és elemezheti a rendellenességek észlelése és az adatai rejtett trendjeinek felderítése érdekében.

A Time Series Insights intuitív és könnyen használható. Az adataival egyetlen sor kód írása nélkül is dolgozhat. Még nincs szükség új nyelvre, de a Time Series Insights részletes szöveges lekérdezési nyelvet biztosít az SQL-t ismerő haladó felhasználók számára. Emellett a kezdők számára kiválasztható és kattintásos feltárást is biztosít.

Az ügyfelek gyorsan kihasználhatják az eszközökhöz kapcsolódó problémák gyors diagnosztizálásához szükséges előnyöket. DevOps-elemzést végezhetnek a IoT-megoldásban fellépő hibák kiváltó okának eléréséhez. Emellett az adatelemzési kezdeményezéseik részeként is meghatározhatják a további vizsgálathoz szükséges területeket.  

A Time Series Insightsban tárolt adatkezelés három fő módja van:

- Első és legegyszerűbb módja a Time Series Insights Preview Explorer. A segítségével gyorsan megjelenítheti az összes IoT-adatait egy helyen. Olyan eszközöket biztosít, mint például a hő, amely segít az adatrendellenességek kiépítésében. Emellett perspektivikus nézetet is biztosít. Ezzel a beállítással akár négy nézetet is összehasonlíthat egy vagy több Time Series Insights környezetből egyetlen irányítópulton. Az irányítópulton megtekintheti az idősoros adatait az összes helyen. További információ a [Time Series Insights Preview Explorer böngészőről](./time-series-insights-update-explorer.md). Time Series Insights környezetének megtervezéséhez olvassa el [Time Series Insights megtervezését](./time-series-insights-update-plan.md).

- A második módszer a JavaScript SDK használata a hatékony diagramok és diagramok gyors beágyazásához a webalkalmazásban. A kód mindössze néhány sornyi kódot használhat a hatékony lekérdezések létrehozásához. A táblázatok, a tortadiagramok, a sávdiagramok, a intenzitástérképei és az adatrácsok feltöltésére használhatók. Ezen elemek mindegyike az SDK használatával létezik. Az SDK emellett absztrakt Time Series Insights lekérdezési API-kat is. Az SQL-hez hasonló predikátumok létrehozásához használhatja őket az irányítópulton megjeleníteni kívánt adatlekérdezéshez. Hibrid megjelenítési rétegbeli megoldások esetén a Time Series Insights paraméteres URL-címeket is kínál. Zökkenőmentes csatlakozást biztosítanak a Time Series Insights betekintő Explorerrel az adatelemzéshez.

    * A JavaScript SDK-val kapcsolatos további információkért olvassa el a [Time Series INSIGHTS js ügyféloldali kódtár](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) és a [Time Series Insights ügyfél](https://github.com/Microsoft/tsiclient) dokumentációja című témakört.

    * További információ az URL-címek és az új felhasználói felület megosztásáról: [a Azure Time Series Insights Preview Explorerben](time-series-insights-update-explorer.md)tekintheti meg az adatmegjelenítést.

- A harmadik módszer a hatékony API-k használata a Time Series Insights tárolt adatlekérdezéshez. Time Series Insights olyan időbeli operátorokkal rendelkezik, mint például a `from`, a `to`, a `first` és a `last`. Összesítések és átalakítások, például `average`, `min`, `max`, `split by`, `order by` és `DateHistogram`. Emellett olyan szűrési operátorokat is tartalmaz, mint például a `has`, `in`, `and`, `or`, `greater than` és `REGEX`. Ezek az operátorok lehetővé teszik, hogy az alárendelt alkalmazások gyorsan megtalálják az adataikban érdekes trendeket és mintákat. Felhasználhatja őket a saját készítésű vizualizációk kitöltésére a rendellenességek észlelése érdekében.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Üzemeltetéselemzés és a folyamatok hatékonyságának növelése

A Time Series Insights használatával figyelheti az állapotot, a használatot és a nagy méretű berendezések teljesítményét. Time Series Insights egyszerű módszert kínál a működési hatékonyság mérésére. A Time Series Insights használatával azonnali és rugalmas skálázhatósággal kezelheti a változatos és kiszámíthatatlan IoT-terheléseket a betöltések és a lekérdezések teljesítményének csökkenése nélkül.

[![Overview](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

A működési folyamatokból érkező adatok folyamatos átvitele és folyamatos feldolgozása a megfelelő technológiával vagy megoldással együtt sikeresen átalakíthatja a vállalatokat. Ezek a megoldások gyakran több rendszer kombinációját jelentik. Lehetővé teszik az állandóan módosult, különösen az IoT-tartományon belüli adatelemzést és elemzést, és közös mintát osztanak ki.

Ezek a minták gyakran olyan IoT-kompatibilis platformokkal kezdődnek, amelyek több milliárd eseményt is betöltenek az eszközöktől és érzékelőktől, amelyek különböző területi beállításokra terjednek ki. Ezek a rendszerek a valós idejű elemzések és műveletek elvégzése érdekében dolgozzák fel és elemzik a streaming-adatforrásokat. Az adatok archiválása általában a meleg és a hideg tárolóba történik a közel valós idejű és a kötegelt elemzésekhez.

Az összegyűjtött adatok több feldolgozási folyamaton keresztül megtisztíthatók és contextualize az alsóbb rétegbeli lekérdezési és elemzési forgatókönyvek esetében. Az Azure gazdag szolgáltatásokat kínál, amelyek alkalmazhatók olyan IoT-forgatókönyvek esetén, mint az eszközök karbantartása és a gyártás. Ezek a szolgáltatások többek között a következők: Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic apps, Azure Databricks, Azure Machine Learning és Power BI.

A megoldás architektúrája a következő módon érhető el:

- Adatok betöltése IoT Hub vagy Event Hubs használatával a legjobb biztonsági, átviteli sebesség és késés érdekében.
- Adatfeldolgozás és-számítások végrehajtása. A betöltött adatcsatornákat olyan szolgáltatásokon keresztül, mint például a Stream Analytics, a Logic Apps és a Azure Functions. Az Ön által használt szolgáltatás a konkrét adatfeldolgozási igényektől függ.
- A feldolgozási folyamatból származó számított jeleket a rendszer leküldi a tároláshoz és az elemzéshez Time Series Insights.

A Time Series Insights közel valós idejű adatfeltárást és adatelemzést kínál a múltbeli adatokkal kapcsolatban. Az üzleti igényektől függően a MapReduce és a Kaptári feladatok a Time Series Insights tárolt adatokon futhatnak Time Series Insights az Azure HDInsight-hoz való csatlakoztatásával. A Time Series Insightsban tárolt adatPower BIk és más ügyfélalkalmazások számára elérhetők a Time Series Insights nyilvános felületű lekérdezési API-kon keresztül. Ezek az adatok a Deep Business és az operatív intelligenciával kapcsolatos forgatókönyvekhez használhatók.

## <a name="advanced-analytics"></a>Bővített analitika

Integrálja a fejlett elemzési szolgáltatásokkal, például a Machine Learning és a Azure Databricksokkal. Time Series Insights több millió eszközről ingresses a nyers adatok mennyiségét. Olyan környezetfüggő adatok hozzáadására szolgál, amelyeket az Azure Analytics-szolgáltatások egy csomagja zökkenőmentesen felhasználhat.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

A fejlett elemzési és gépi tanulási szolgáltatás nagy mennyiségű adatmennyiséget használ fel és dolgoz fel. Ezek az adatok az adatvezérelt döntések végrehajtásához és a prediktív elemzések elvégzéséhez használatosak. A IoT-használati esetekben a fejlett elemzési algoritmusok a több millió eszközről gyűjtött adatokból tanulnak. Ezek az eszközök többször továbbítanak adatokat másodpercenként. A IoT-eszközökről gyűjtött adatok nyersek. Nem rendelkezik a kontextussal kapcsolatos információkkal, például az eszköz helyével és az érzékelő olvasásának egységével. Ennek eredményeképpen a nyers adatfeldolgozást nehéz közvetlenül a speciális elemzésekhez használni.

Time Series Insights áthidalja a IoT és a fejlett Analitika közötti szakadékot két egyszerű és költséghatékony módon:

- A Time Series Insights a IoT Hub használatával gyűjti össze a nyers telemetria-adatokat több millió eszközről. A szolgáltatás a környezetfüggő adatokkal gazdagítja az adatokat, és egy parketta formátumba alakítja át az adatokat. Ez a formátum egyszerűen integrálható más fejlett analitikai szolgáltatásokkal, például Machine Learningokkal, Azure Databricksekkel és külső gyártók alkalmazásaival.

    Time Series Insights az igazság forrásaként szolgálhat a szervezeten belüli összes adathoz. Létrehoz egy központi tárházat az alsóbb rétegbeli elemzési számítási feladatok felhasználásához. Mivel Time Series Insights közel valós idejű tárolási szolgáltatás, a fejlett elemzési modellek folyamatosan megismerhetik a bejövő IoT telemetria adatait. Ennek eredményeképpen a modellek pontosabb előrejelzéseket készíthetnek.

- Másodszor, a gépi tanulási és előrejelzési modellek kimenete Time Series Insightsba is helyezhető az eredmények megjelenítéséhez és tárolásához. Ez az eljárás segít a szervezeteknek a modelljeik optimalizálásában és finomhangolásában. A Time Series Insights megkönnyíti a folyamatos átviteli telemetria-adatokat a betanított modell kimenetével megegyező síkban ábrázolni. Így segít az adatelemzési csapatoknak észrevenni a rendellenességeket és azonosítani a mintákat.  

## <a name="next-steps"></a>Következő lépések

- További információ a [Time Series Insights Preview Explorer böngészőről](./time-series-insights-update-explorer.md).
- A környezet megtervezéséhez olvassa el [Time Series Insights előzetes verziójának tervezését](./time-series-insights-update-plan.md) .
- Olvassa el a [Time Series Insights-ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját.
