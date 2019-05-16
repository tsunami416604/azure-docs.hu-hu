---
title: A beépített operational analytics az Azure Cosmos DB az Apache Spark bemutatása
description: Ismerje meg, hogyan használhatja a beépített támogatással az Apache Spark az Azure Cosmos DB működési elemzések és a mesterséges Intelligencia futtatása
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/10/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: c62639feed7ced9d92e29715e350b952465a94a1
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517736"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Beépített működési elemzések az Azure Cosmos DB az Apache Spark (előzetes verzió) 

A beépített támogatással az Azure Cosmos DB Apache Spark lehetővé teszi elemzések futtatására az Apache Spark az Azure Cosmos-fiókban tárolt adatok alapján. Az Apache Spark-feladatok végrehajtására közvetlenül a globálisan elosztott Cosmos-adatbázis a natív támogatást biztosít. Ezekkel a lehetőségekkel fejlesztők, az adatmérnökök és az adatelemzők használható Azure Cosmos DB, egy rugalmas, méretezhető, és nagy teljesítményű adatplatform is futtatnia **OLTP és OLAP/HTAP** számítási feladatokhoz. 

A Spark számítási automatikusan elérhető összes Azure-régióban társítva van az Azure Cosmos-fiók. Spark-feladatok használata az Azure Cosmos DB több főkiszolgálós képesség, és írás vagy a helyi replikák az egyes régiókban lekérdezésének. 

> [!NOTE]
> A beépített támogatással az Azure Cosmos DB Apache Spark jelenleg korlátozott előzetes verzióban érhető el. Regisztráljon az előzetes verzióra, navigáljon a [regisztráljon az előzetes verzióra](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB) lapot. 

Az Apache Spark-támogatás az Azure Cosmos DB az alábbi előnyöket biztosítja:

* A földrajzilag elosztott felhasználók és az adatok a leggyorsabb Insight kérheti le.

* A megoldást és az alacsonyabb architektúrájának egyszerűsítheti a [teljes tulajdonosi költség](total-cost-ownership.md) (TCO). A rendszer a legkevésbé fog rendelkezni az adatfeldolgozás összetevők száma, és elkerülhetők a közöttük lévő bármely szükségtelen adatáthelyezés.

* Létrehoz egy [biztonsági](secure-access-to-data.md), [megfelelőségi](compliance.md), és a naplózási határ, amely felügyelet alá az összes adatot tartalmazza.

* "Az always on" biztosít vagy [magas rendelkezésre állású](high-availability.md) végfelhasználói analytics által szerint szigorúbb SLA-k.

![Az Apache Spark támogatása az Azure Cosmos DB-Vizualizáció](./media/spark-api-introduction/spark-api-visualization.png)
 
Az Azure Cosmos DB az Apache Spark-támogatás révén létrehozása és üzembe helyezése a megoldásokra, például a mesterséges Intelligencia és a deep learning-modellek, a prediktív elemzés, a javaslatokat, IoT, a customer 360, a csalások észlelése, a szöveg véleményeket, a kattintássorozat-elemzés. Ezek a megoldások közvetlenül az Azure Cosmos DB adatokon működnek.

Állítsa be a batch és ETL-feladat az Azure Cosmos DB, streaming, nyissa meg az adatbázis-szolgáltatás kívül, és adjon hozzá további nélkül számítási szolgáltatásokhoz is. Ha az ETL-feladat végrehajtása, és skálázza fel a feladat végrehajtása után le kell rugalmasan méretezheti a számítási környezetet.

Az Apache Spark-támogatás az Azure Cosmos DB nyújt az Apache Spark-modulok beépített Machine Learning támogatást. A modulok közé tartozik a Spark MLLib, a Microsoft gépi tanulás a Spark, Azure Machine Learning és a Cognitive Services. Az alábbi funkciók tartoznak az adatszakértők, adatmérnökök és az adatelemzők létrehozhat és üzembe helyezése a machine learning-modellek közvetlenül az Azure Cosmos DB, az idő és az alacsony költségű tört belül.


## <a name="key-benefits"></a>Főbb előnyök

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globálisan elosztott, alacsony késleltetésű működési elemzések és a mesterséges Intelligencia

Az Apache Spark on a globálisan elosztott Azure Cosmos-adatbázis most már beszerezheti a gyors piacra insight-all a világ különböző pontjain. Az Azure Cosmos DB lehetővé teszi, hogy **globálisan elosztott, alacsony késleltetésű működési elemzések** rugalmas méretek mellett három fő módszerek:

* Az Azure Cosmos database globálisan elosztott, mivel minden adat betöltött helyileg a gyártók (például users) adatok hol találhatók. A lekérdezések szolgálja ki, szemben a helyi replikák közelebb a gyártók és a felhasználói adatokat függetlenül attól, hol találhatók a világ számára. 

* Az elemzési lekérdezések közvetlenül az indexelt adatokat tárolja az adatokat a partíciók bármely a szükségtelen adatok áthelyezése nélkül hajtja végre. 

* A Spark az Azure Cosmos DB-vel ugyanarra a szalagra, mert kevesebb köztes fordítások és adatmozgást kerül sor, a jobb teljesítmény és méretezhetőség eredményez.

### <a name="unified-serverless-experience-for-apache-spark"></a>Az Apache Spark egységes, kiszolgáló nélküli felhasználói élményt

Egy többmodelles adatbázis, mint az Azure Cosmos DB mostantól kibővíti OSS API-k támogatása azáltal, hogy egy **egyesített kiszolgáló nélküli felhasználói élményt az Apache Spark** kulcs-érték, dokumentum, diagram, oszlop családi adatmodelleket. Különböző adatmodellek támogatottak, MongoDB, Cassandra, Gremlin, Etcd és az SQL API-k – ugyanazokat az alapul szolgáló adatokat minden működő használatával. 

Az Apache Spark támogatása az Azure Cosmos DB is natív módon támogatja a Scala, Python, Java nyelven írt alkalmazások, és számos szorosan integrált kódtárak használata SQL. Ezek a könyvtárak tartalmazzák ([Spark SQL](https://spark.apache.org/sql/)), machine learning-(Spark [MLlib](https://spark.apache.org/mllib/)), adatfolyam-feldolgozás ([Spark strukturált Stream](https://spark.apache.org/streaming/)), és gráffeldolgozási (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Ezek az eszközök megkönnyítik a különböző használati esetek az Apache Sparkot használhatja. Nem kell foglalkozni a Spark kezelése, illetve Spark-fürtök. Használhatja a jól ismert Apache Spark API-k és **Jupyter notebookok** analytics és az SQL API-t vagy bármely a nyílt Forráskódú nosql-alapú API-k, például Cassandra, a tranzakciós feldolgozást ugyanazokat az alapul szolgáló adatokat egy időben.

### <a name="no-schema-or-index-management"></a>Nincs séma vagy index kezelése

Az Azure Cosmos DB a hagyományos analitikai adatbázisokkal ellentétben az adatmérnökök és az adatelemzők már nem kell foglalkozni nehézkes séma- és index kezelését. Az Azure Cosmos DB adatbázis-kezelő nem igényel explicit séma vagy index felügyeleti, és képes automatikusan indexeli az összes adatot, feltölti, gyorsan az Apache Spark-lekérdezések kiszolgálása érdekében. 

### <a name="consistency-choices"></a>Konzisztenciaszint

Az Apache Spark-feladatok az adatok a partíciók az Azure Cosmos-adatbázis a rendszer végrehajtja, mivel a lekérdezések fog kapni a [öt jól definiált konzisztenciaszintet](consistency-levels.md). Ezek konzisztencia modellek révén rugalmasan választhatja ki a szigorú konzisztenciát a legpontosabb eredmények biztosít a machine learning-algoritmusok a késést és magas rendelkezésre állású veszélyeztetése nélkül. 

### <a name="slas"></a>SLA-k

Az Apache Spark-feladatok fog rendelkezni az Azure Cosmos DB előnyeit, például az iparágvezető átfogó [SLA-k](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) anélkül, hogy bármilyen terhelést külön Apache Spark-fürtök kezelése... Ezen SLA-k foglalnia az átviteli sebességet a késés a 99. percentilis, a konzisztencia és a magas rendelkezésre állás. 

### <a name="mixed-workloads"></a>Vegyes számítási feladatok

Az integráció az Apache Spark az Azure Cosmos DB hidak a tranzakciós és analitikai elkülönítése, amelyről a fő ügyfelei panaszait egyik globális szintű felhőbeli natív alkalmazások készítése során. 

## <a name="built-in-jupyter-notebooks-support"></a>A Jupyter notebookok beépített támogatását

Az Azure Cosmos DB Jupyter notebookok például Cassandra, MongoDB, SQL, Gremlin és tábla minden API támogatja. A Jupyter notebookok futtathatók az Azure Cosmos DB-fiókokhoz, és azokat a fejlesztői környezet minőségét. Az összes Azure Cosmos DB API-k és adatmodelleket beépített notebook támogatása lehetővé teszi interaktív lekérdezések futtatása. Hajtsa végre a machine learning-modellek, és elemezheti az Azure Cosmos-adatbázisokban tárolt adatok is. A Jupyter jegyzetfüzet-megoldást használ, a tárolt adatok elemzése, hozhat létre és gépi tanulási modellek betanítását és következtetési végezni az adatokon, az Azure Portalon a következő képen látható módon:

![Az Azure Cosmos DB támogatja a Jupyter notebookok](./media/spark-api-introduction/jupyter-notebooks-portal.png)

## <a name="next-steps"></a>További lépések

* Az Azure Cosmos DB előnyeit kapcsolatos további információkért tekintse meg a [áttekintése](introduction.md) cikk.
* [Ismerkedés az Azure Cosmos DB API a mongodb-hez](mongodb-introduction.md)
* [Bevezetés az Azure Cosmos DB Cassandra API használatába](cassandra-introduction.md)
* [Bevezetés az Azure Cosmos DB Gremlin API használatába](graph-introduction.md)
* [Bevezetés az Azure Cosmos DB Table API használatába](table-introduction.md)




