---
title: A Azure Cosmos DB beépített operatív elemzésének bemutatása Apache Spark
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB Apache Spark beépített támogatását a működési elemzések és AI-k futtatásához
ms.service: cosmos-db
ms.topic: overview
ms.date: 08/01/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 3fd7990043543b92eb3daf8ec699ec58db3387ec
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717567"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Beépített operatív Analitika Azure Cosmos DBban Apache Spark (előzetes verzió) 

A Azure Cosmos DB Apache Spark beépített támogatása lehetővé teszi az elemzések futtatását Apache Spark az Azure Cosmos-fiókban tárolt adatokkal. Natív támogatást nyújt Apache Spark feladatok végrehajtásához közvetlenül a globálisan elosztott Cosmos-adatbázisokon. Ezekkel a képességekkel a fejlesztők, adatmérnökök és adatszakértők a Azure Cosmos DB rugalmas, méretezhető és teljesítményű adatplatformként használhatják a **OLTP és az OLAP/HTAP** számítási feladatok futtatására is. 

A Spark-számítás automatikusan elérhető az Azure Cosmos-fiókhoz társított összes Azure-régióban. A Spark-feladatok az Azure Cosmos DB több főkiszolgálós képességeit használják, és az egyes régiókban a helyi replikák írására és lekérdezésére képesek. 

> [!NOTE]
> A Azure Cosmos DB Apache Spark beépített támogatása jelenleg korlátozott előzetes verzióban érhető el. Az előzetes verzióra való feliratkozáshoz navigáljon az [előnézet lapra](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB). 

A Azure Cosmos DB Apache Spark támogatása a következő előnyöket kínálja:

* A leggyorsabban betekintést nyerhet a földrajzilag elosztott felhasználók és információk adataiba.

* Egyszerűbbé teheti a megoldás architektúráját, és csökkentheti a [teljes tulajdonlási költséget](total-cost-ownership.md) (TCO). A rendszer a legkevesebb adatfeldolgozási összetevőt fogja tartalmazni, és elkerüli a szükségtelen adatáthelyezést.

* [Biztonsági](secure-access-to-data.md), megfelelőségi [](compliance.md)és naplózási határt hoz létre, amely magában foglalja a felügyelet alatt lévő összes adat körét.

* A "always on" vagy a [magasan elérhető](high-availability.md) végfelhasználói elemzéseket biztosít, amelyeket a szigorú SLA-kat támogat.

![Apache Spark támogatás Azure Cosmos DB vizualizációban](./media/spark-api-introduction/spark-api-visualization.png)
 
A Azure Cosmos DB Apache Spark támogatásával olyan megoldásokat építhet ki és helyezhet üzembe, mint például az AI és a Deep learning-modellek, a prediktív elemzések, a javaslatok, a IoT, az ügyfél 360, a csalások észlelése, a szöveg hangulata, a kattintássorozat-elemzés. Ezek a megoldások közvetlenül a Azure Cosmos DB adatain működnek.

Megadhatja a Batch és az ETL Azure Cosmos DB-feladatot, anélkül, hogy az adatbázis-szolgáltatáson kívül kellene lépnie, vagy további számítási szolgáltatásokat kellene hozzáadnia. A számítási környezet rugalmasan méretezhető, ha ETL-feladatot kell végrehajtania, és a feladatot a feladatok elvégzése után vissza kell méreteznie.

A Azure Cosmos DB Apache Spark támogatása beépített Machine Learning támogatást kínál a Apache Spark-futtatókörnyezetekben. A futtatókörnyezetek a Spark MLLib, a Microsoft Machine Learning a Spark, a Azure Machine Learning és a Cognitive Services esetében is használhatók. Ezekkel a funkciókkal az adatszakértők, adatmérnökök és adatelemzők közvetlenül a Azure Cosmos DB belül hozhatnak létre és működővé tenni gépi tanulási modelleket, az idő töredékében és alacsony költségek mellett.


## <a name="key-benefits"></a>Főbb előnyök

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globálisan elosztott, kis késleltetésű operatív elemzés és AI

A globálisan elosztott Azure Cosmos-adatbázis Apache Sparkával mostantól gyorsan betekintést nyerhet a világ minden pontjára. A Azure Cosmos DB lehetővé teszi a **globálisan elosztott, kis késleltetésű operatív elemzést** rugalmas méretezéssel három kulcsfontosságú módszerrel:

* Mivel az Azure Cosmos-adatbázis globálisan elosztott, az összes adatot helyileg betölti a rendszer, ahol az adatelőállítók (például felhasználók) találhatók. A lekérdezéseket a rendszer a gyártók és az adatfogyasztók számára legközelebb eső helyi replikák alapján kézbesíti, függetlenül attól, hogy hol találhatók a világon. 

* Az összes analitikus lekérdezést közvetlenül az adatpartíción belül tárolt indexelt adatsorokon hajtja végre anélkül, hogy szükségtelen adatáthelyezésre lenne szükség. 

* Mivel a Spark a Azure Cosmos DB, kevesebb közbenső fordítást és adatmozgást helyez el, ami jobb teljesítményt és méretezhetőséget eredményez.

### <a name="unified-serverless-experience-for-apache-spark"></a>Egységesített kiszolgáló nélküli felhasználói élmény Apache Spark számára

A többmodelles adatbázisok esetében a Azure Cosmos DB mostantól kiterjeszti az OSS API-k támogatását azáltal, hogy **egységes kiszolgáló nélküli felhasználói élményt biztosít az Apache Spark** számára a kulcs-érték, a dokumentum, a gráf és az oszlop család adatmodelljeivel. A MongoDB, Cassandra, Gremlin, Etcd és SQL API-k használatával különböző adatmodellek támogatottak – mindez ugyanazon alapul szolgáló adaton működik. 

A Azure Cosmos DB Apache Spark támogatásával natív módon támogathatja a Scala, a Python, a Java, és számos szorosan integrált kódtárat használhat az SQL-hez. Ezek a kódtárak ([Spark SQL](https://spark.apache.org/sql/)), gépi tanulás (Spark [MLlib](https://spark.apache.org/mllib/)), Stream Processing ([Spark Structured streaming](https://spark.apache.org/streaming/)) és Graph Processing (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Ezek az eszközök megkönnyítik a Apache Spark használatát különféle használati esetekben. Nem kell foglalkoznia a Spark-vagy Spark-fürtök kezelésével. Használhatja az ismerős Apache Spark API-kat és a **Jupyter** -jegyzetfüzeteket az elemzéshez és az SQL API-hoz, vagy bármely olyan OSS NoSQL API-hoz, mint a Cassandra a tranzakciós feldolgozáshoz ugyanazon a mögöttes adaton.

### <a name="no-schema-or-index-management"></a>Nincs séma-vagy indexelési felügyelet

A hagyományos analitikai adatbázisoktól eltérően a Azure Cosmos DB, az adatmérnököknek és az adatszakértőknek már nincs szükségük nehézkes séma-és indexelési felügyelet kezelésére. A Azure Cosmos DBban található adatbázismotor nem igényel explicit sémát vagy indexelést, és képes automatikusan indexelni az összes beolvasott adatot, hogy az Apache Spark lekérdezéseket gyorsan kiszolgálja. 

### <a name="consistency-choices"></a>Konzisztencia-beállítások

Mivel a Apache Spark feladatok végrehajtása az Azure Cosmos-adatbázis adatpartíciójában történik, a lekérdezések [öt jól meghatározott konzisztencia-lehetőséget](consistency-levels.md)kapnak. Ezek a konzisztencia-modellek rugalmasságot biztosítanak a szigorú konzisztencia kiválasztásához, hogy a lehető legpontosabb eredményeket nyújtsanak a gépi tanulási algoritmusok számára a késés és a magas rendelkezésre állás veszélyeztetése nélkül. 

### <a name="comprehensive-slas"></a>Átfogó SLA-k

A Apache Spark feladatok Azure Cosmos DB előnyökkel járnak, mint például az iparág vezető átfogó [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) -i (99,999), külön Apache Spark-fürtök kezelése nélkül. Ezek a SLA-kat az átviteli sebesség, a esetek 99% percentilis, a konzisztencia és a magas rendelkezésre állás miatti késés is magában foglalja. 

### <a name="mixed-workloads"></a>Vegyes számítási feladatok

A Apache Spark integrációja Azure Cosmos DB hidak a tranzakciós és analitikus elkülönítést, amely az egyik fő vásárlói fájdalom, amely a Felhőbeli natív alkalmazások globális méretekben való létrehozásakor volt. 

## <a name="scenarios-for-azure-cosmos-db-spark-support"></a>Azure Cosmos DB Spark-támogatás forgatókönyvei

### <a name="retail-and-consumer-goods"></a>Kiskereskedelmi és fogyasztói termékek

A Azure Cosmos DB Spark-támogatásával valós idejű ajánlásokat és ajánlatokat biztosíthat. Segítheti az ügyfeleket abban, hogy a valós idejű személyre szabással és a termékkel kapcsolatos javaslatok alapján felfedezzék a szükséges elemeket.

* A Apache Spark Runtime beépített Machine Learning támogatásával valós idejű ajánlásokat készíthet a termékkatalógusokban.

* A stream-adatforrások, az adatvásárlások és az ügyféladatok lehetőségre kattintva megadhatja az élettartamot megcélzó célként megadott javaslatokat.

* A Azure Cosmos DB globális terjesztési funkciójának használatával a különböző régiókba tartozó termékek nagy mennyisége ezredmásodpercben elemezhető.

* Gyorsan lekérheti a földrajzilag elosztott felhasználók és az adatelemzést. Az előléptetés átváltási arányának növeléséhez a megfelelő felhasználót a megfelelő időben kell kiszolgálni a megfelelő felhasználó számára.

* Kihasználhatja a beépített Spark streaming-képességet az élő adattartalom dúsításához, ha a statikus ügyféladatok használatával egyesíti azt. Így valós időben és az ügyfelek által megvalósított kontextusban teheti elérhetővé a személyre szabott és a célcsoportot mutató hirdetéseket.

Az alábbi képen látható, hogyan használhatók a Azure Cosmos DB Spark-támogatás a díjszabás és a promóciók optimalizálásához:

![Azure Cosmos DB Spark-támogatás a díjszabás és a promóció optimalizálásához](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


Az alábbi képen látható, hogyan használhatók a Azure Cosmos DB Spark-támogatás a valós idejű javaslatok motorjában:

![Azure Cosmos DB Spark-támogatás a valós idejű ajánlási motorban](./media/spark-api-introduction/real-time-recommendation-engine.png)

### <a name="manufacturing-and-iot"></a>Gyártási és IoT

Azure Cosmos DB beépített elemzési platformja lehetővé teszi, hogy a IoT-adatok valós idejű elemzését globális szinten több millió eszközről is engedélyezzék. Olyan modern innovációkat végezhet, mint például az időjárási minták, a prediktív elemzések és az energia-optimalizálások előrejelzése.

* Azure Cosmos DB használatával olyan adatokat is megadhat, mint például a valós idejű eszközök mérőszámai és az időjárási tényezők, majd az intelligens Grid Analytics alkalmazásával optimalizálhatja a kapcsolódó eszközök teljesítményét a mezőben. Az intelligens Grid Analytics a működési költségek szabályozásának kulcsa, a rács megbízhatóságának javítása és személyre szabott energiaellátási szolgáltatások biztosítása a fogyasztóknak.

Az alábbi képen látható, hogyan használhatók a Azure Cosmos DB Spark-támogatás a IoT-eszközök metrikáinak olvasásához és az intelligens Grid Analytics alkalmazásához:

![Azure Cosmos DB Spark-támogatás a IoT-eszközök metrikáinak olvasásához](./media/spark-api-introduction/read-metrics-from-iot-devices.png)


### <a name="predictive-maintenance"></a>Prediktív karbantartás

* Az olyan adategységek, mint például a kis fúrási tornyokban használt kompresszorok és a mélytengeri platformok fenntartása összetett törekvés. Ezek az eszközök a világ különböző részein találhatók, és petabájt hoznak. A Azure Cosmos DB használatával olyan teljes körű prediktív adatfolyamatot építhet ki, amely a Spark streaming használatával nagy mennyiségű érzékelő telemetria, tároló-alkatrészt és érzékelő-hozzárendelési adatfeldolgozást végez.

* Gépi tanulási modelleket hozhat létre és helyezhet üzembe, amelyekkel előre jelezheti az eszközök meghibásodását, és a hiba bekövetkezése előtt kiállíthatja a karbantartási munkavégzési rendeléseket.

Az alábbi képen látható, hogyan használható a Azure Cosmos DB Spark-támogatás a prediktív karbantartási rendszer létrehozásához:

![Azure Cosmos DB Spark-támogatás prediktív karbantartási rendszer létrehozásához](./media/spark-api-introduction/predictive-maintenance-system.png)

Az alábbi képen látható, hogyan használható a Azure Cosmos DB Spark-támogatás a valós idejű járművek diagnosztikai rendszerének létrehozásához:

![Azure Cosmos DB Spark-támogatás a valós idejű járművek diagnosztikai rendszerének létrehozásához](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

### <a name="gaming"></a>Játékok

* A beépített Spark-támogatással a Azure Cosmos DB percek alatt könnyedén hozhat létre, méretezheti és helyezhet üzembe fejlett elemzési és gépi tanulási modelleket, hogy a lehető legjobb játékélményt hozza létre.

* A játékosok, a vásárlások és a viselkedési adatokat elemezheti a megfelelő személyre szabott ajánlatok létrehozásához, hogy magas konverziós arányt lehessen elérni.

* A Spark Machine learning segítségével elemzéseket készíthet a játék telemetria adatairól, és betekintést nyerhet. A lassú betöltési idő és a játékon belüli problémák diagnosztizálására és megelőzésére is lehetőség van.

Az alábbi képen látható, hogyan használják a Azure Cosmos DB Spark-támogatását a Gaming Analyticsben:

![Azure Cosmos DB Spark-támogatás a Gaming Analyticsben](./media/spark-api-introduction/gaming-analytics.png)

## <a name="built-in-jupyter-notebooks-support"></a>Beépített Jupyter notebookok támogatása

Azure Cosmos DB támogatja a beépített Jupyter jegyzetfüzeteket minden olyan API-hoz, mint például a Cassandra, a MongoDB, az SQL, a Gremlin és a table. A Jupyter notebookok az Azure Cosmos-fiókokon belül futnak, és javítják a fejlesztői élményt. Az összes Azure Cosmos DB API-k és adatmodellek beépített notebook-támogatása lehetővé teszi a lekérdezések interaktív futtatását. Emellett gépi tanulási modelleket is végrehajthat, és elemezheti az Azure Cosmos-adatbázisokban tárolt adatait. A Jupyter notebook felületének használatával elemezheti a tárolt adatok elemzését, kiépítheti és betaníthatja a gépi tanulási modelleket, és elvégezheti a Azure Portal lévő adatokon való következtetést az alábbi képen látható módon:

![A Jupyter notebookok támogatása Azure Cosmos DB](./media/spark-api-introduction/jupyter-notebooks-portal.png)

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB előnyeinek megismeréséhez tekintse meg az [áttekintést](introduction.md) ismertető cikket.
* [Ismerkedés a MongoDB Azure Cosmos DB API-val](mongodb-introduction.md)
* [Ismerkedjen meg a Azure Cosmos DB Cassandra API](cassandra-introduction.md)
* [Ismerkedés a Azure Cosmos DB Gremlin API-val](graph-introduction.md)
* [Ismerkedjen meg a Azure Cosmos DB Table API](table-introduction.md)




