---
title: Az Azure Time Series Insights előzetes verziója használati esetek |} A Microsoft Docs
description: Ismerje meg az Azure Time Series Insights előzetes verziója használati eseteket.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 27c79155a4630f49faf49f30b2d46bf1e0dffd90
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508312"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Az Azure Time Series Insights előzetes verzió használati esetek

Ez a cikk az Azure Time Series Insights előzetes verziója esetében számos gyakori alkalmazási helyzetek összegzi. Ebben a cikkben a javaslatok az alkalmazások és a Time Series Insights-megoldások fejlesztéséhez kiindulási pontként szolgál.

Pontosabban Ez a cikk az alábbi kérdéseket:

* Mik azok a Time Series Insights gyakori alkalmazási esetei?
* Mik azok a Time Series Insights használatával járó előnyöket [adatáttekintés és vizuális rendellenesség-észlelést](#data-exploration-and-visual-anomaly-detection)?
* Mik azok a Time Series Insights használatával járó előnyöket [működési elemzés és a folyamatok hatékonyságát](#operational-analysis-and-driving-process-efficiency)?
* Mik azok a Time Series Insights használatával járó előnyöket [advanced analytics](#advanced-analytics)?

Ezek áttekintése esetek használatakor az alábbiakban szerepel.

## <a name="introduction"></a>Bemutatás

Az Azure Time Series Insights egy teljes körű platform--szolgáltatásként ajánlat. Gyűjtése, feldolgozásához, tárolásához, elemzéséhez és lekérdezése rendkívül contextualized, a time series-optimalizált IoT-méretű adatokra szolgál. A Time Series Insights alkalmi adatfeltárás és működési elemzés ideális. A Time Series Insights egy olyan ajánlat, hogy megfelel-e a széles körű kell ipari IoT központi telepítések egyedileg bővíthető, testre szabott, szolgáltatás.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Adatfeltárás és vizuális anomáliadetektálás

Több milliárd eseményt vizsgálhat meg és elemezhet azonnal, és feltárhatja a rendellenességeket, és megismerheti az adatok rejtett trendjeit. A Time Series Insights közel valós idejű teljesítményt nyújt az IoT- és DevOps-alapú elemzési számítási feladatoknál.

[![adatkezelő](media/v2-update-use-cases/data-explorer.svg)](media/v2-update-use-cases/data-explorer.svg#lightbox)

Ügyfelek többsége egyetért abban, hogy az idő Insight Time Series Insights legerősebb eszközök között. A Time Series Insights használatához nem szükséges adatok előzetes előkészítése. Gyors csatlakozni tudjon az Azure IoT Hub vagy az Azure Event Hubs eseményeinek milliárdjait percek alatt működik. A csatlakozás után megjelenítése és elemzése, Észreveheti a rendellenességeket milliárd, és feltárhatja a rejtett trendeket az adatokban.

Time Series Insights rendkívül intuitív és könnyen használható. Egyetlen sor kód megírása nélkül kezelheti az adatait. Nincs új nyelvet is nem. A Time Series Insights biztosít pontos szövegalapú lekérdezéseket a haladó felhasználók, akik ismerik az SQL. Válassza ki és-részletes, kezdőnek is tartalmazza.

Ügyfeleink az eszközintelligencia szolgáltatással kapcsolatos problémák gyorsan diagnosztizálása érdekében a sebesség előnyeit. Fejlesztési és üzemeltetési egy IoT-megoldások hiba kiváltó okának való ellátására. Azok az is megállapítható területek az adatelemzési kezdeményezések vizsgálatára.  

A Time Series Insights-ban tárolt adatok interakcióba három elsődleges módja van:

- Az első és legegyszerűbb módszert kínálja a kezdéshez a Time Series Insights előzetes verziója Explorerrel van. Segítségével, gyorsan megjeleníteni az összes IoT-adatait egy helyen. Eszközök, mint például az intenzitástérkép segítséget biztosít az adatok Észreveheti a rendellenességeket. Perspektíva nézet is tartalmazza. Segítségével akár négy nézeteket egy vagy több egyetlen irányítópulton a Time Series Insights-környezetek összehasonlítása. Az irányítópulton megtekintheti az idősorozat-adatok minden hely között. Tudjon meg többet a [Time Series Insights előzetes verziója explorer](./time-series-insights-update-explorer.md). Tervezze meg a Time Series Insights-környezet, olvassa el [Time Series Insights tervezési](./time-series-insights-update-plan.md).

- Indítsa el a másik lehetőség, hogy a JavaScript SDK segítségével gyorsan hatékony diagramokat és ábrákat beágyazása a webalkalmazás. Mindössze néhány sornyi kóddal hozhat létre hatékony lekérdezések. Ezek segítségével feltölti a vonaldiagramot, tortadiagramot, sávdiagramot, intenzitástérképek, adatrácsok és egyéb. A-beépített az SDK-val található minden elemet. Az SDK-t is kivonatolja a Time Series Insights lekérdezési API-k. Hozzon létre SQL-szerű predikátumok meg kíván jeleníteni az irányítópulton az adatok lekérdezéséhez használhatja őket. A megjelenítési réteg hibrid megoldások a Time Series Insights kínál a paraméteres URL-címek. Zökkenőmentes kapcsolódási pontok a Time Series Insights előzetes verziója Explorer, az adatokat, részletes ismertetőinken nyújtanak.

    * Olvassa el a [Time Series Insights JS ügyféloldali kódtár](tutorial-explore-js-client-lib.md) és a [Time Series Insights-ügyfél](https://github.com/Microsoft/tsiclient) további információ a JavaScript SDK dokumentációját.

    * További információ az URL-címek és az új felhasználói felület megosztása áttekintésével [megjelenítheti az Intéző előzetes adatait](time-series-insights-update-explorer.md).

- Indítsa el a harmadik módszer, hogy a hatékony API-k a Time Series Insights lekérdezési adataihoz. A Time Series Insights tartozik például az ideiglenes operátorok `from`, `to`, `first`, és `last`. Rendelkezik összesítések és átalakításokat például `average`, `min`, `max`, `split by`, `order by`, és `DateHistogram`. Azt is rendelkezik, mint szűrési operátorok `has`, `in`, `and`, `or`, `greater than`, és `REGEX`. Minden operátor lehetővé teszik az alárendelt alkalmazások érdekes trendek és mintázatok gyorsan megtalálhatja az adatokban. Ezek segítségével azonosíthatja a rendellenességeket a saját készítésű Vizualizációk feltöltéséhez.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Üzemeltetéselemzés és a folyamatok hatékonyságának növelése

A Time Series Insights segítségével a állapotát, a használat és a méretezett berendezést teljesítményének figyelése. A Time Series Insights egy egyszerű módja annak mérésére a működési hatékonyságot biztosít. A Time Series Insights használatával azonnali és rugalmas skálázhatósággal kezelheti a változatos és kiszámíthatatlan IoT-terheléseket a betöltések és a lekérdezések teljesítményének csökkenése nélkül.

[![– Áttekintés](media/v2-update-use-cases/overview.svg)](media/v2-update-use-cases/overview.svg#lightbox)

Streamelés és folyamatos üzemeltetési folyamatokat származó adatok feldolgozása sikeresen alakíthatja át minden olyan cégnek, ha a megfelelő technológiai vagy kombinálva. Ezek a megoldások gyakran több rendszer kombinációját történik. Feltárása és elemzése különösen az IoT-tartomány, a folyamatosan változik, és megosztja a gyakori minta adatok lehetővé teszik.

Ezek a minták gyakran indítsa el a beolvasó eszközeit és érzékelőit, amelyek különböző területi beállításokat az események több milliárd IoT-kompatibilis platformokkal. Ezek a rendszerek feldolgozásához, és elemezheti a streamelt adatokat, hogy a valós idejű elemzések és műveletek. A meleg általában archivált adatokat, és a ritka elérésű tárolási közel valós időben, és a batch-elemzés.

Gyűjtött adatok halad végig tanfolyamunkra, és az alsóbb rétegbeli lekérdezési és elemzési forgatókönyvek contextualize, feldolgozási sorozata. Az Azure sokoldalú szolgáltatásai, például az eszköz karbantartási és a gyártási IoT-forgatókönyvek alkalmazhatók kínál. Ezek a szolgáltatások közé tartozik a Time Series Insights, az IoT Hub, Event Hubs, az Azure Stream Analytics, Azure Functions, Azure Logic Apps, az Azure Databricks, az Azure Machine Learning és a Power BI.

Megoldásarchitektúra érhető el a következő módon:

- Betöltési kategóriaelső a biztonság, az átviteli sebesség és a késleltetés az IoT Hub vagy az Eseményközpontok adatait.
- Hajtsa végre az adatok feldolgozására és számításokat. Tölcsér betöltött adatokat a szolgáltatások, például a Stream Analytics, a Logic Apps és az Azure Functions használatával. A szolgáltatás használata az adott adatfeldolgozási igényeitől függ.
- A feldolgozási folyamat számított jeleinek akkor lesznek leküldve a Time Series Insights tárolására és az analytics.

A Time Series Insights kínál közel valós idejű adatok feltárása és eszköz-alapú insights korábbi adatok alapján. Üzleti igényeitől függően a MapReduce és a Hive-feladatok a Time Series Insights csatlakozik az Azure HDInsight a Time Series Insights tárolt adatokat is futtathatja. A Time Series Insights-ban tárolt adatok a Power BI és egyéb ügyfélalkalmazások a Time Series Insights surface-lekérdezés API-n keresztül érhető el. Ezek az adatok magas szintű üzleti és működési intelligenciával forgatókönyvekhez használható.

## <a name="advanced-analytics"></a>Bővített analitika

Fejlett elemzési szolgáltatásokat, mint a Machine Learning és az Azure Databricks integrálható. A Time Series Insights ingresses nyers adatok eszközök millióiról érkező. Hozzáadja a kontextuális adatokat gyűjt, amelyek zökkenőmentesen felhasználhassák Azure elemzési szolgáltatáscsomag.

[![Elemzés](media/v2-update-use-cases/advanced-analytics.svg)](media/v2-update-use-cases/advanced-analytics.svg#lightbox)

Fejlett elemzési és gépi tanulási használják, és nagy mennyiségű adat feldolgozását. Ezeket az adatokat az adatvezérelt döntések és prediktív elemzéseket végezhet szolgál. IoT használati esetek speciális analitikai algoritmusok ismerje meg, az eszközök millióiról érkező gyűjtött adatok közül. Ezek az eszközök többször másodpercenként továbbítja az adatokat. Az IoT-eszközökről gyűjtött adatok a nyers. Környezeti információk – például a tartózkodási hely, az eszköz és az egység az érzékelők leolvasott nem rendelkezik. Ennek eredményeképpen a nyers adatok is nehézkes közvetlenül a fejlett analitika használata.

A Time Series Insights közötti szakadék IoT-adatok és fejlett analitikai két egyszerű és költséghatékony módon:

- Először a Time Series Insights összegyűjti a nyers telemetriaadatok eszközök millióiról érkező IoT Hub segítségével. Bővíti az adatok és környezeti adatokat és a egy parquet formátumra alakítja át az adatokat. Ez a formátum egyszerűen integrálható más speciális analitikai szolgáltatásokat, például a Machine Learning, az Azure Databricks és a harmadik féltől származó alkalmazások.

    A Time Series Insights az összes adat hiteles forrásaként szolgálhat a vállalaton belül. Hoz létre egy központi tárházban alsóbb rétegbeli elemzési számítási feladatok a felhasználni. Mivel a Time Series Insights közel valós idejű tárolási szolgáltatás, speciális analitikai modellek folyamatosan tanul bejövő IoT telemetriai adatokból. Ennek eredményeképpen a modellek végezhet előrejelzéseket pontosabb.

- Második a Time Series Insights is lehet adatkéréseket a kimenet a machine learning és az előrejelzési modellek jelenítheti meg, és tárolja az eredményeket. Ez az eljárás segítségével a szervezetek optimalizálása és a modellek a Teljesítménybeállítások. A Time Series Insights egyszerűen lehet megjelenítése a streamelési az azonos adatsík-telemetriai adatait, a betanított modell adja vissza. Ily módon az adatokat adattudománnyal foglalkozó csapatai rendellenességeket és a minták azonosításához nyújt segítséget.  

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [Time Series Insights előzetes verziója explorer](./time-series-insights-update-explorer.md).

- Tervezze meg a környezetben, olvassa el [Time Series Insights előzetes verziója tervezési](./time-series-insights-update-plan.md).

- Olvassa el a [Time Series Insights ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját.
