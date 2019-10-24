---
title: A Azure Cosmos DB beépített operatív elemzésének bemutatása Apache Spark
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB Apache Spark beépített támogatását a működési elemzések és AI-k futtatásához
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 45f70d9a9d2cb450cab1242a080077c771218ba2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754847"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Beépített operatív Analitika Azure Cosmos DBban Apache Spark

A Azure Cosmos DB segítségével globálisan elosztott, kis késésű elemzéseket és AI-t futtathat a tranzakciós adatforgalomban. A Apache Spark-és Jupyter notebookok natív támogatásával a Azure Cosmos DB segít csökkenteni a bepillantást az időt. Mivel az adatgyűjtés, a kiszolgálás és az elemzés az Azure-régió helyi adatbázis-replikáján fut. Közvetlenül is végrehajthat Apache Spark lekérdezéseket az adatpartíciókban tárolt indexelt többmodelles adattípusokon.

A Azure Cosmos DB Apache Spark beépített támogatása lehetővé teszi az elemzések futtatását Apache Spark az Azure Cosmos-fiókban tárolt adatokkal. Natív támogatást nyújt Apache Spark feladatok végrehajtásához közvetlenül a globálisan elosztott Cosmos-adatbázisokon. Ezekkel a képességekkel a fejlesztők, adatmérnökök és adatszakértők a Azure Cosmos DB rugalmas, méretezhető és teljesítményű adatplatformként használhatják a **OLTP és az OLAP/HTAP** számítási feladatok futtatására is.

A Azure Cosmos DB Apache Spark támogatása a következő előnyöket kínálja:

* A leggyorsabban betekintést nyerhet a földrajzilag elosztott felhasználók és információk adataiba.

* Egyszerűbbé teheti a megoldás architektúráját, és csökkentheti a [teljes tulajdonlási költséget](total-cost-ownership.md) (TCO). A rendszer a legkevesebb adatfeldolgozási összetevőt fogja tartalmazni, és elkerüli a szükségtelen adatáthelyezést.

* [Biztonsági](secure-access-to-data.md), [megfelelőségi](compliance.md)és naplózási határt hoz létre, amely magában foglalja a felügyelet alatt lévő összes adat körét.

* A "always on" vagy a [magasan elérhető](high-availability.md) végfelhasználói elemzéseket biztosít, amelyeket a szigorú SLA-kat támogat.

![Apache Spark támogatás Azure Cosmos DB vizualizációban](./media/spark-api-introduction/spark-api-visualization.png)
 
A Azure Cosmos DB Apache Spark támogatásával olyan megoldásokat építhet ki és helyezhet üzembe, mint például az AI és a Deep learning-modellek, a prediktív elemzések, a javaslatok, a IoT, az ügyfél 360, a csalások észlelése, a szöveg hangulata, a kattintássorozat-elemzés. Ezek a megoldások közvetlenül a Azure Cosmos DB adatain működnek.

Megadhatja a Batch és az ETL Azure Cosmos DB-feladatot, anélkül, hogy az adatbázis-szolgáltatáson kívül kellene lépnie, vagy további számítási szolgáltatásokat kellene hozzáadnia. A számítási környezet rugalmasan méretezhető, ha ETL-feladatot kell végrehajtania, és a feladatot a feladatok elvégzése után vissza kell méreteznie.

A Azure Cosmos DB Apache Spark támogatása beépített Machine Learning támogatást kínál a Apache Spark-futtatókörnyezetekben. A futtatókörnyezetek a Spark MLLib, a Microsoft Machine Learning a Spark, a Azure Machine Learning és a Cognitive Services esetében is használhatók. Ezekkel a funkciókkal az adatszakértők, adatmérnökök és adatelemzők közvetlenül a Azure Cosmos DB belül hozhatnak létre és működővé tenni gépi tanulási modelleket, az idő töredékében és alacsony költségek mellett.


## <a name="key-benefits"></a>Főbb előnyök

### <a name="low-latency-operational-analytics-and-ai"></a>Kis késleltetésű működési Analitika és AI

A globálisan elosztott Azure Cosmos-adatbázis Apache Sparkával mostantól gyorsan betekintést nyerhet a világ minden pontjára. A Azure Cosmos DB lehetővé teszi a **globálisan elosztott, kis késleltetésű operatív elemzést** rugalmas méretezéssel három kulcsfontosságú módszerrel:

* Mivel az Azure Cosmos-adatbázis globálisan elosztott, az összes adatot helyileg betölti a rendszer, ahol az adatelőállítók (például felhasználók) találhatók. A lekérdezéseket a rendszer a gyártók és az adatfogyasztók számára legközelebb eső helyi replikák alapján kézbesíti, függetlenül attól, hogy hol találhatók a világon.

* Az összes analitikus lekérdezést közvetlenül az adatpartíción belül tárolt indexelt adatsorokon hajtja végre anélkül, hogy szükségtelen adatáthelyezésre lenne szükség.

* Mivel a Spark a Azure Cosmos DB, kevesebb közbenső fordítást és adatmozgást helyez el, ami jobb teljesítményt és méretezhetőséget eredményez.

* Az Azure Cosmos DB alapvető funkciói, például a több főkiszolgálós, az automatikus feladatátvétel, a Availability Zones stb. elérhetők a beépített Apache Spark Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Egységesített kiszolgáló nélküli felhasználói élmény Apache Spark számára

A többmodelles adatbázisok esetében a Azure Cosmos DB mostantól kiterjeszti az OSS API-k támogatását azáltal, hogy **egységes kiszolgáló nélküli felhasználói élményt biztosít az Apache Spark** számára a kulcs-érték, a dokumentum, a gráf és az oszlop család adatmodelljeivel. A MongoDB, Cassandra, Gremlin, Etcd és SQL API-k használatával különböző adatmodellek támogatottak – mindez ugyanazon alapul szolgáló adaton működik. 

A Azure Cosmos DB Apache Spark támogatásával natív módon támogathatja a Scala, a Python, a Java, és számos szorosan integrált kódtárat használhat az SQL-hez. Ezek a kódtárak ([Spark SQL](https://spark.apache.org/sql/)), gépi tanulás (Spark [MLlib](https://spark.apache.org/mllib/)), Stream Processing ([Spark Structured streaming](https://spark.apache.org/streaming/)) és Graph Processing (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Ezek az eszközök megkönnyítik a Apache Spark használatát különféle használati esetekben. Nem kell foglalkoznia a Spark-vagy Spark-fürtök kezelésével. Használhatja az ismerős Apache Spark API-kat és a **Jupyter-jegyzetfüzeteket** az elemzéshez és az SQL API-hoz, vagy bármely olyan OSS NoSQL API-hoz, mint a Cassandra a tranzakciós feldolgozáshoz ugyanazon a mögöttes adaton.

### <a name="no-schema-or-index-management"></a>Nincs séma-vagy indexelési felügyelet

A hagyományos analitikai adatbázisoktól eltérően a Azure Cosmos DB, az adatmérnököknek és az adatszakértőknek már nincs szükségük nehézkes séma-és indexelési felügyelet kezelésére. A Azure Cosmos DBban található adatbázismotor nem igényel explicit sémát vagy indexelést, és képes automatikusan indexelni az összes beolvasott adatot, hogy az Apache Spark lekérdezéseket gyorsan kiszolgálja.

### <a name="consistency-choices"></a>Konzisztencia-beállítások

Mivel a Apache Spark feladatok végrehajtása az Azure Cosmos-adatbázis adatpartíciójában történik, a lekérdezések [öt jól meghatározott konzisztencia-lehetőséget](consistency-levels.md)kapnak. Ezek a konzisztencia-modellek rugalmasságot biztosítanak a szigorú konzisztencia kiválasztásához, hogy a lehető legpontosabb eredményeket nyújtsanak a gépi tanulási algoritmusok számára a késés és a magas rendelkezésre állás veszélyeztetése nélkül.

### <a name="comprehensive-slas"></a>Átfogó SLA-k

A Apache Spark feladatok Azure Cosmos DB előnyökkel járnak, mint például az iparág vezető átfogó [SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) -i (99,999), külön Apache Spark-fürtök kezelése nélkül. Ezek a SLA-kat az átviteli sebesség, a esetek 99% percentilis, a konzisztencia és a magas rendelkezésre állás miatti késés is magában foglalja. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Vegyes működési és analitikai feladatok teljes elkülönítéssel

A Apache Spark integrációja Azure Cosmos DB hidak a tranzakciós és analitikus elkülönítést, amely az egyik fő vásárlói fájdalom, amely a Felhőbeli natív alkalmazások globális méretekben való létrehozásakor volt. A OLTP és az OLAP munkaterhelések egymás mellett futnak, és nem akadályozzák egymást.

### <a name="low-latency-analytical-and-transactional-storage"></a>Kis késleltetésű analitikai és tranzakciós tárolás

Azure Cosmos DB natív módon tárolja az adatok két különböző tárolási rétegben: tranzakciós (sor-orientált) és analitikai tárterület (oszlop-orientált, Apache Parquet File Format). Minden szinten replikálja az adatszinteket, és lehetővé teszi a felhasználók számára, hogy az adatmegőrzési szabályzatok alapján egymástól függetlenül kezeljék az adategységeket.

Ez a tárolási architektúra a kritikus fontosságú tranzakciós és analitikai feladatokat is lehetővé teszi ugyanazon globálisan elosztott adatokon. A Cosmos DB nem igényel ETL-műveletet, vagy szükségtelen adatáthelyezést hajt végre. A tranzakciós és az analitikai számítási feladatokat egyszerűen futtathatja ugyanazon alapul szolgáló adatokon. A tranzakciós munkaterhelések használhatják a jól ismert tranzakciós hozzáférési API-kat, például az SQL, a Cassandra, a MongoDB, a Gremlin és a Etcd. Míg az elemzési és AI-munkaterhelések használhatják a beépített Apache Spark SQL, ML keretrendszerek és a teljes Apache Spark toolchain.

### <a name="snapshots-and-historical-analytical-queries"></a>Pillanatképek és korábbi analitikai lekérdezések

Az analitikai szinten tárolt oszlopos tömörített adatokhoz igény szerinti vagy ütemezett pillanatképeket hozhat létre, amelyek közvetlenül egy adott pillanatképen keresztül végeznek analitikai lekérdezéseket. Ez a funkció lehetővé teszi a flashback vagy az időutazásos analitikai lekérdezések, a visszaállítások, a teljes múltbeli naplózási nyomvonalak, valamint a reprodukálható gépi tanulási és AI-kísérletek használatát.

## <a name="next-steps"></a>Következő lépések

* Az Azure Cosmos DB előnyeinek megismeréséhez tekintse meg az [áttekintést](introduction.md) ismertető cikket.
* [Ismerkedés a MongoDB Azure Cosmos DB API-val](mongodb-introduction.md)
* [Ismerkedjen meg a Azure Cosmos DB Cassandra API](cassandra-introduction.md)
* [Ismerkedés a Azure Cosmos DB Gremlin API-val](graph-introduction.md)
* [Ismerkedjen meg a Azure Cosmos DB Table API](table-introduction.md)