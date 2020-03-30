---
title: Használati esetek előzetes megtekintése – Azure Time Series Insights | Microsoft dokumentumok
description: További információ az Azure Time Series Insights előzetes használati eseteiről.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac2a728750c6b01dfc57fa7e20df25c856395a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087393"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Az Azure Time Series Insights előzetes használati esetei

Ez a cikk az Azure Time Series Insights előzetes verziójának számos gyakori használati esetét foglalja össze. Az ebben a cikkben található javaslatok kiindulópontként szolgálnak az alkalmazások és megoldások fejlesztéséhez a Time Series Insights segítségével.

Ez a cikk konkrétan a következő kérdésekre ad választ:

* Melyek a Time Series Insights gyakori használati esetei?
* Milyen előnyökkel jár a Time Series Insights használata [az adatok feltárásához és a vizuális anomáliaészleléshez?](#data-exploration-and-visual-anomaly-detection)
* Milyen előnyökkel jár a Time Series Insights használata az operatív elemzésekhez és a [folyamatok hatékonyságához?](#operational-analysis-and-driving-process-efficiency)
* Milyen előnyökkel jár a Time Series Insights [speciális elemzésekhez](#advanced-analytics)való használata?

Ezek a használati forgatókönyvek áttekintése a következő szakaszokban található.

## <a name="introduction"></a>Bevezetés

Az Azure Time Series Insights egy végpontok között, platform-szolgáltatásként kínál. Nagy környezetfüggő, idősorozatra optimalizált IoT-léptékű adatok gyűjtésére, feldolgozására, tárolására, elemzésére és lekérdezésére szolgál. A Time Series Insights ideális az ad hoc adatok feltárásához és az operatív elemzéshez. A Time Series Insights egy egyedülállóan bővíthető, személyre szabott szolgáltatásajánlat, amely megfelel az ipari IoT-telepítések széles körű igényeinek.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Adatok feltárása és vizuális anomáliadetektálás

Azonnal felfedezheti és elemezheti az események milliárdjait, hogy észlelje az anomáliákat, és felfedezhesse az adatok rejtett trendjeit. A Time Series Insights közel valós idejű teljesítményt nyújt az IoT és a DevOps elemzési számítási feladataihoz.

[![Adatkezelő](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

A legtöbb ügyfél egyetért abban, hogy a betekintéshez szükséges minimális idő a Time Series Insights egyik kiemelkedő jellemzője:

* A Time Series Insights nem igényel előzetes adatelőkészítést. 
* Gyorsan együttműködik, hogy percek alatt több milliárd eseményhez kapcsolódjon az Azure IoT Hub vagy az Azure Event Hubs-példányokban. 
* A csatlakozást követően események milliárdjait jelenítheti meg és elemezheti, hogy észlelje az anomáliákat, és felderítse az adatok rejtett trendjeit.

A Time Series Insights intuitív és egyszerűen használható. Az adatokat egyetlen kódsor írása nélkül is kezelheti. Nincs olyan új nyelv sem, amelyet meg kell tanulnia, bár a Time Series Insights részletes szövegalapú lekérdezési nyelvet biztosít az SQL-t jól ismerő tapasztalt felhasználók számára. Azt is előírja, select-and-click feltárása a kezdők számára.

Az ügyfelek kihasználhatják a gyors analazott problémák gyors diagnosztizálására. DevOps-elemzés végrehajtásával megtudják, hogy az IoT-megoldásban egy hiba kiváltó oka. Emellett adatelemzési kezdeményezéseik részeként megjelölhetik azokat a területeket is, amelyeket további vizsgálatra jelölhetnek meg. 

A Time Series Insights ban tárolt adatokkal három fő módon lehet interakcióba lépni:

* Az első és legegyszerűbb módja a kezdésnek a Time Series Insights előzetes kezelője. Segítségével gyorsan vizualizálhatja az összes IoT-adatot egy helyen. Olyan eszközöket biztosít, mint a hőtérkép, amely segít az adatok anomáliáinak észlelésében. Azt is előírja, hogy a perspektíva nézet. Segítségével akár négy nézetet is összehasonlíthat egy vagy több Time Series Insights-környezetből egyetlen irányítópulton. Az irányítópulton megtekintheti az idősorozatok adatait az összes helyszínen. További információ a [Time Series Insights előzetes verziójának kezelőjéről.](./time-series-insights-update-explorer.md) A Time Series Insights-környezet megtervezéséhez olvassa el a [Time Series Insights-tervezés című részt.](./time-series-insights-update-plan.md)

* A második módja annak, hogy indítsa el, hogy használja a JavaScript SDK gyorsan beágyazni nagy diagramok és grafikonok a webes alkalmazás. Mindössze néhány sornyi kód, akkor a szerző hatékony lekérdezéseket. Ezekkel feltölthetvonal-diagramokat, kördiagramokat, sávdiagramokat, hőtérképeket, adatrácsokat stb. Az összes ilyen elem az SDK használatával már beépített. Az SDK is absztrakt Time Series Insights lekérdezési API-k. Ezeket sql-szerű predikátumok segítségével lekérdezheti az irányítópulton megjeleníteni kívánt adatokat. A hibrid bemutatóréteg-megoldások esetében a Time Series Insights paraméterezett URL-címeket kínál. Zökkenőmentes kapcsolódási pontokat biztosítanak a Time Series Insights előzetes kezelőjével az adatok mély merüléséhez.

  * A JavaScript SDK-ról a [Time Series Insights JS ügyféltárés](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) a [Time Series Insights ügyféldokumentációja](https://github.com/Microsoft/tsiclient) olvashat.

  * Az [Azure Time Series Insights előzetes verziójának kezelője a Visualize-adatok](time-series-insights-update-explorer.md)áttekintésével többet is megtudhat az URL-címek és az új felhasználói felület megosztásáról.

* A harmadik indítási mód a hatékony API-k használata a Time Series Insightsban tárolt adatok lekérdezéséhez. A Time Series Insights időbeli `from` `to`operátorokkal rendelkezik, például , , `first`és `last`. Összesítései és átalakulásai `average`vannak, mint például `min`, , `max` `split by`, , `order by`és `DateHistogram`. Szűrőoperátorokkal is rendelkezik, `has` `in`mint `and` `or`például , , , , `greater than`, és `REGEX`. Mindezek az operátorok lehetővé teszik az alsóbb rétegbeli alkalmazások számára, hogy gyorsan érdekes trendeket és mintákat találjanak az adatokban. Ezekkel feltölti a saját termesztésű vizualizációkat az anomáliák észleléséhez.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Működési elemzés és vezetési folyamat hatékonysága

A Time Series Insights használatával nagy méretekben figyelheti a berendezések állapotát, használatát és teljesítményét. A Time Series Insights segítségével egyszerűen mérheti a működési hatékonyságot. A Time Series Insights a betöltési vagy lekérdezési teljesítmény feláldozása nélkül segít a különböző és előre nem látható IoT-számítási feladatok kezelésében.

[![Áttekintés](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Az operatív folyamatokból származó adatok streamelése és folyamatos feldolgozása sikeresen átalakíthat bármely vállalkozást, ha a megfelelő technológiával vagy megoldással kombinálva van. Gyakran ezek a megoldások kombinációja több rendszer. Lehetővé teszik a folyamatosan változó adatok feltárását és elemzését, különösen az IoT-birodalomban, és közös mintázatot osztanak meg.

Ezek a minták gyakran az IoT-kompatibilis platformokkal kezdődnek, amelyek több milliárd eseményt fognak be a különböző területi beállításokat átfogó eszközökről és érzékelőkről. Ezek a rendszerek feldolgozzák és elemzik a streamelési adatokat, hogy valós idejű elemzéseket és műveleteket nyerjenek. Az adatok általában archiválva meleg és hűtőtároló közel valós idejű és kötegelt elemzés.

Az összegyűjtött adatok feldolgozássorozaton keresztül történik, hogy megtisztítsa és kontextusba foglalja azokat az alsóbb rétegbeli lekérdezési és elemzési forgatókönyvekhez. Az Azure olyan gazdag szolgáltatásokat kínál, amelyek alkalmazhatók az IoT-forgatókönyvekre, például az eszközök karbantartására és gyártására. Ezek a szolgáltatások közé tartozik a Time Series Insights, az IoT Hub, az Event Hubs, az Azure Stream Analytics, az Azure Functions, az Azure Logic Apps, az Azure Databricks, az Azure Machine Learning és a Power BI.

A megoldásarchitektúra a következő módon érhető el:

* Adatok betöltése az IoT Hubon vagy az Event Hubokon keresztül a kategóriájában legjobb biztonság, átviteli és késés érdekében.
* Végezze el az adatfeldolgozást és a számításokat. Csatorna betöltése adatokat szolgáltatásokon keresztül, például a Stream Analytics, logic apps és az Azure Functions. A használt szolgáltatás az adott adatfeldolgozási igényektől függ.
* A feldolgozási folyamat számított jeleit a Time Series Insights tárolja és elemzésre.

A Time Series Insights közel valós idejű adatfeltárást és eszközalapú elemzéseket kínál a korábbi adatokkal kapcsolatban. Az üzleti igényektől függően a MapReduce és a Hive-feladatok a Time Series Insights ban tárolt adatokon futtathatók a Time Series Insights azure HDInsight-hoz való csatlakoztatásával. A Time Series Insightsban tárolt adatok a Power Series Insights nyilvános felületi lekérdezési API-kon keresztül érhetők el a Power BI és más ügyfélalkalmazások számára. Ezek az adatok mély üzleti és operatív intelligencia forgatókönyvekhez használhatók.

## <a name="advanced-analytics"></a>Bővített analitika

Integrálható a fejlett elemzési szolgáltatásokkal, például a Machine Learning és az Azure Databricks. A Time Series Insights több millió eszköznyers adatait forgalmaz. Olyan környezetfüggő adatokat ad hozzá, amelyek et zökkenőmentesen használhat fel az Azure Analytics-szolgáltatások csomagja.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

A fejlett analitika és a gépi tanulás nagy mennyiségű adatot fogyaszt és dolgoz fel. Ezeket az adatokat adatvezérelt döntések meghozatalához és prediktív elemzés hez használjuk. Az IoT használati esetekben a fejlett elemzési algoritmusok több millió eszközről gyűjtött adatokból tanulnak. Ezek az eszközök másodpercenként többször továbbítják az adatokat. Az IoT-eszközökről gyűjtött adatok nyersek. Hiányzik belőle a kontextuális információ, mint például az eszköz helye és az érzékelő leolvasásának egysége. Ennek eredményeképpen a nyers adatokat nehéz közvetlenül felhasználni a speciális elemzésekhez.

A Time Series Insights két egyszerű és költséghatékony módon hidalja át az IoT-adatok és a fejlett elemzések közötti szakadékot:

* Először is a Time Series Insights az IoT Hub használatával gyűjti a nyers telemetriai adatokat több millió eszközről. Kontextusi információkkal gazdagítja az adatokat, és az adatokat parkettaformátumgá alakítja. Ez a formátum könnyen integrálható más fejlett elemzési szolgáltatásokkal, például a Machine Learning, az Azure Databricks és a külső alkalmazások.

    A Time Series Insights az igazság forrása lehet a szervezeten belüli összes adat esetében. Központi adattárat hoz létre a későbbi elemzési számítási feladatok felhasználásához. Mivel a Time Series Insights egy közel valós idejű tárolási szolgáltatás, a fejlett elemzési modellek folyamatosan tanulhatnak a bejövő IoT telemetriai adatokból. Ennek eredményeképpen a modellek pontosabb előrejelzéseket készíthetnek.

* Másodszor, a kimenet a gépi tanulási és előrejelzési modellek betáplálhatók a Time Series Insights megjelenítéséhez és az eredmények tárolásához. Ez az eljárás segít a szervezeteknek optimalizálni és módosítani a modelleket. A Time Series Insights egyszerűvé teszi a telemetriai adatok vizualizálását ugyanazon a síkon, mint a betanított modell kimenetei. Ily módon segít az adatelemzési csapatoknak az anomáliák felismerésében és a minták azonosításában.

## <a name="next-steps"></a>További lépések

* További információ a [Time Series Insights előzetes verziójának kezelőjéről.](./time-series-insights-update-explorer.md)
* Olvassa el [a Time Series Insights előzetes tervezését](./time-series-insights-update-plan.md) a környezet megtervezéséhez.
* Olvassa el a [Time Series Insights ügyféldokumentációját.](https://github.com/Microsoft/tsiclient)
