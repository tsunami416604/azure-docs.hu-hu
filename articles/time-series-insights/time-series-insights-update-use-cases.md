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
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: f4e14435c2ccab98619bec57fef0e121e2df4403
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431606"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Az Azure Time Series Insights előzetes verzió használati esetek

Ez a cikk számos gyakori alkalmazási helyzetek áttekintést nyújt az Azure Time Series Insights előzetes verziója. Ebben a cikkben szereplő ajánlások szolgálhat a kiindulási pontként, alkalmazások és a Time Series Insights-megoldások fejlesztése.

Miután ez a cikk a következő kérdésekre képes választ adni:

* Mik azok a Time Series Insights gyakori alkalmazási esetei?
* Milyen előnyökkel jár az adatok feltárása és vizuális rendellenesség-észlelést a Time Series Insights?
* Mik a működési elemzés és a folyamatok hatékonyságát a Time Series Insights használatának előnyei?
* Milyen előnyökkel jár a Time Series Insights használata speciális elemzésekre?

Ez a dokumentum áttekintést a használati esetek, amely az Azure Time Series Insights előzetes verziója lett tervezve.

## <a name="introduction"></a>Bevezetés

A Time Series Insights egy teljes körű platform--szolgáltatásként ajánlat. Betöltési, feldolgozásához, tárolásához és magas contextualized, time series-optimalizált IoT-méretű adatok lekérdezésére szolgál. A Time Series Insights alkalmi adatfeltárás és működési elemzés ideális. A Time Series Insights egy olyan ajánlat, hogy megfelel-e a széles körű kell ipari IoT központi telepítések egyedileg bővíthető, testre szabott, szolgáltatás.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Adatfeltárás és vizuális anomáliadetektálás

Több milliárd eseményt vizsgálhat meg és elemezhet azonnal, és feltárhatja a rendellenességeket, és megismerheti az adatok rejtett trendjeit. A Time Series Insights közel valós idejű teljesítményt nyújt az IoT- és DevOps-alapú elemzési számítási feladatoknál.

![Adatkezelő][1]

Ügyfelek többsége egyetért abban, hogy az idő Insight Time Series Insights legerősebb eszközök között. A Time Series Insights használatához nem szükséges adatok előzetes előkészítése. Gyors csatlakozni tudjon az Azure IoT Hub vagy az Azure Event Hubs eseményeinek milliárdjait percek alatt működik. A csatlakozás után megjelenítése és elemzése, Észreveheti a rendellenességeket milliárd, és feltárhatja a rejtett trendeket az adatokban. 

Time Series Insights rendkívül intuitív és könnyen használható. Egyetlen sor kód megírása nélkül kezelheti az adatait. Nincs új nyelvet is nem. A Time Series Insights biztosít pontos szövegalapú lekérdezéseket a haladó felhasználók, akik ismerik az SQL. Válassza ki és-részletes, kezdőnek is tartalmazza.

Ügyfeleink az eszközintelligencia szolgáltatással kapcsolatos problémák gyorsan diagnosztizálása érdekében a sebesség előnyeit. Fejlesztési és üzemeltetési egy IoT-megoldások hiba kiváltó okának való ellátására. Azok az is megállapítható területek az adatelemzési kezdeményezések vizsgálatára.  

A Time Series Insights-ban tárolt adatok interakcióba három elsődleges módja van:

- Az első és legegyszerűbb módszert kínálja a kezdéshez a Time Series Insights előzetes verziója Explorerrel van. Segítségével, gyorsan megjeleníteni az összes IoT-adatait egy helyen. Eszközök, mint például az intenzitástérkép segítséget biztosít az adatok Észreveheti a rendellenességeket. Perspektíva nézet is tartalmazza. Segítségével akár négy nézeteket egy vagy több egyetlen irányítópulton a Time Series Insights-környezetek összehasonlítása. Az irányítópulton megtekintheti az idősorozat-adatok minden hely között. Tudjon meg többet a [Time Series Insights előzetes verziója explorer](./time-series-insights-update-explorer.md). Tervezze meg a Time Series Insights-környezet, olvassa el [Time Series Insights tervezési](./time-series-insights-update-plan.md).

- Indítsa el a másik lehetőség, hogy a JavaScript SDK segítségével gyorsan hatékony diagramokat és ábrákat beágyazása a webalkalmazás. Mindössze néhány sornyi kóddal hozhat létre hatékony lekérdezések. Ezek segítségével feltölti a vonaldiagramot, tortadiagramot, sávdiagramot, intenzitástérképek, adatrácsok és egyéb. A-beépített az SDK-val található minden elemet. Az SDK-t is kivonatolja a Time Series Insights lekérdezési API-k. Hozzon létre SQL-szerű predikátumok meg kíván jeleníteni az irányítópulton az adatok lekérdezéséhez használhatja őket. A megjelenítési réteg hibrid megoldások a Time Series Insights kínál a paraméteres URL-címek. Zökkenőmentes kapcsolódási pontok a Time Series Insights előzetes verziója Explorer, az adatokat, részletes ismertetőinken nyújtanak. További információ a JavaScript SDK-t, olvassa el a [Time Series Insights JS ügyféloldali kódtár](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) és a [Time Series Insights-ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját. További információ a paraméteres URL-címek, olvassa el a [paraméteres URL-címek](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).

- Indítsa el a harmadik módszer, hogy a hatékony API-k a Time Series Insights lekérdezési adataihoz. A Time Series Insights rendelkezik, mint például az ideiglenes operátorok, a első és utolsó. Összesítések rendelkezik, és az átlagos, minimális és maximális érték, például az átalakítások felosztás, rendezési és DateHistogram. Is rajta szűrési operátorok például rendelkezik, az és, vagy nagyobb, mint és reguláris KIFEJEZÉST. Minden operátor lehetővé teszik az alárendelt alkalmazások érdekes trendek és mintázatok gyorsan megtalálhatja az adatokban. Ezek segítségével azonosíthatja a rendellenességeket a saját készítésű Vizualizációk feltöltéséhez.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Üzemeltetéselemzés és a folyamatok hatékonyságának növelése

A Time Series Insights segítségével a állapotát, a használat és a méretezett berendezést teljesítményének figyelése. A Time Series Insights egy egyszerű módja annak mérésére a működési hatékonyságot biztosít. A Time Series Insights használatával azonnali és rugalmas skálázhatósággal kezelheti a változatos és kiszámíthatatlan IoT-terheléseket a betöltések és a lekérdezések teljesítményének csökkenése nélkül.

![Áttekintés][2]

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

![Elemzés][3]

Fejlett elemzési és gépi tanulási használják, és nagy mennyiségű adat feldolgozását. Ezeket az adatokat az adatvezérelt döntések és prediktív elemzéseket végezhet szolgál. IoT használati esetek speciális analitikai algoritmusok ismerje meg, az eszközök millióiról érkező gyűjtött adatok közül. Ezek az eszközök többször másodpercenként továbbítja az adatokat. Az IoT-eszközökről gyűjtött adatok a nyers. Környezeti információk – például a tartózkodási hely, az eszköz és az egység az érzékelők leolvasott nem rendelkezik. Ennek eredményeképpen a nyers adatok is nehézkes közvetlenül a fejlett analitika használata.

A Time Series Insights közötti szakadék IoT-adatok és fejlett analitikai két egyszerű és költséghatékony módon: 

- Először a Time Series Insights összegyűjti a nyers telemetriaadatok eszközök millióiról érkező IoT Hub segítségével. Bővíti az adatok és környezeti adatokat és a egy parquet formátumra alakítja át az adatokat. Ez a formátum egyszerűen integrálható más speciális analitikai szolgáltatásokat, például a Machine Learning, az Azure Databricks és a harmadik féltől származó alkalmazások. 

    A Time Series Insights az összes adat hiteles forrásaként szolgálhat a vállalaton belül. Hoz létre egy központi tárházban alsóbb rétegbeli elemzési számítási feladatok a felhasználni. Mivel a Time Series Insights közel valós idejű tárolási szolgáltatás, speciális analitikai modellek folyamatosan tanul bejövő IoT telemetriai adatokból. Ennek eredményeképpen a modellek végezhet előrejelzéseket pontosabb.

- Második a Time Series Insights is lehet adatkéréseket a kimenet a machine learning és az előrejelzési modellek jelenítheti meg, és tárolja az eredményeket. Ez az eljárás segítségével a szervezetek optimalizálása és a modellek a Teljesítménybeállítások. A Time Series Insights egyszerűen lehet megjelenítése a streamelési az azonos adatsík-telemetriai adatait, a betanított modell adja vissza. Ily módon az adatokat adattudománnyal foglalkozó csapatai rendellenességeket és a minták azonosításához nyújt segítséget.  

## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [Time Series Insights előzetes verziója explorer](./time-series-insights-update-explorer.md).
- Tervezze meg a környezetben, olvassa el [Time Series Insights előzetes verziója tervezési](./time-series-insights-update-plan.md).
- Olvassa el a [Time Series Insights ügyfél](https://github.com/Microsoft/tsiclient) dokumentációját.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
