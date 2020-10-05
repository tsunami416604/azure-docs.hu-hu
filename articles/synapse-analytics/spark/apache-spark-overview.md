---
title: Mi az Apache Spark?
description: Ez a cikk bevezetést nyújt a Apache Spark az Azure szinapszis Analyticsben, valamint a különböző forgatókönyvekben, amelyekben használhatja a Sparkot.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 3a2ba65fcef2b6481835cb45243449870361c062
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87498922"
---
# <a name="apache-spark-in-azure-synapse-analytics"></a>Apache Spark az Azure szinapszis Analyticsben

A Apache Spark egy párhuzamos feldolgozási keretrendszer, amely támogatja a memóriabeli feldolgozást a Big-adatanalitikai alkalmazások teljesítményének növelése érdekében. Az Azure szinapszis Analytics Apache Spark a Microsoft egyike a Felhőbeli Apache Spark megvalósításának. Az Azure szinapszis megkönnyíti a Spark-készlet (előzetes verzió) létrehozását és konfigurálását az Azure-ban. Az Azure Szinapszisban található Spark-készletek kompatibilisek az Azure Storage szolgáltatással és Azure Data Lake 2. generációs tárolóval. Az Azure Spark-készletek használatával feldolgozhatja az Azure-ban tárolt adatait.

![Spark: egyesített keretrendszer](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>Mi az Apache Spark?

A Apache Spark primitíveket biztosít a memóriabeli fürtök számítástechnikaához. A Spark-feladatokkal az adatok betölthetők és gyorsítótárazhatók a memóriába, majd ismétlődő jelleggel lekérdezhetők. A memóriában történő feldolgozás sokkal gyorsabb, mint a lemezalapú alkalmazások. A Spark több programozási nyelvet is integrálva van, hogy lehetővé tegye az elosztott adatkészletek, például a helyi gyűjtemények kezelését. Nem kell mindent térképként rendszerezni és csökkenteni a műveletek számát.

![Hagyományos MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Az Azure Szinapszisban a Spark-készletek teljes körűen felügyelt Spark szolgáltatást biztosítanak. Itt találja a a szinapszis Analyticsben a Spark-készlet létrehozásának előnyeit.

| Szolgáltatás | Leírás |
| --- | --- |
| Gyorsaság és hatékonyság |A Spark-példányok körülbelül 2 percen belül kezdődnek, kevesebb mint 60 csomópontot, és körülbelül 5 percet vesznek igénybe több mint 60 csomópont. A példány alapértelmezés szerint 5 perccel az utolsó feladatot követően leáll, kivéve, ha egy jegyzetfüzet-kapcsolatban nem tartja életben. |
| Könnyű létrehozás |Az Azure Szinapszisban létrehozhat egy új Spark-készletet percek alatt a Azure Portal, Azure PowerShell vagy a szinapszis Analytics .NET SDK használatával. Lásd: Ismerkedés [a Spark-készletekkel a szinapszis Analyticsben](../quickstart-create-apache-spark-pool-studio.md). |
| Egyszerű használat |A szinapszis Analytics a [Nteract](https://nteract.io/)származtatott egyéni jegyzetfüzetet tartalmaz. Ezeket a notebookokat interaktív adatfeldolgozásra és -vizualizációra használhatja.|
| REST API-k |A a szinapszis Analyticsben az [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), egy REST API-alapú Spark-feladat, amely a feladatok távoli elküldésére és figyelésére szolgál. |
| A 2. generációs Azure Data Lake Storage támogatása| Az Azure Szinapszisban található Spark-készletek a 2. generációs és a BLOB Storage-t is Azure Data Lake Storage használhatják. További információ a Data Lake Storageről: [Azure Data Lake Storage áttekintése](../../data-lake-store/data-lake-store-overview.md). |
| Integráció külső integrált fejlesztői környezetekkel (IDE) | Az Azure szinapszis egy IDE beépülő modult biztosít a [JetBrains "INTELLIJ ötletéhez](https://www.jetbrains.com/idea/) , amely hasznos lehet alkalmazások létrehozására és beküldésére egy Spark-készletbe. |
| Előre betöltött Anaconda-könyvtárak |Az Azure Szinapszisban a Spark-készletek előre telepített anaconda-kódtárakkal jönnek. Az [Anaconda](https://docs.continuum.io/anaconda/) közel 200 könyvtárat biztosít például a Machine Learning szolgáltatáshoz, az adatok elemzéséhez vagy a megjelenítéshez. |
| Méretezhetőség | Az Azure szinapszis-készletekben lévő Apache Sparkek lehetővé teszik az automatikus méretezést, így a készletek méretezése szükség szerint a csomópontok hozzáadásával vagy eltávolításával végezhető el. A Spark-készletek emellett adatvesztés nélkül leállíthatók, mivel minden adatot az Azure Storage vagy a Data Lake Storage tárol. |

Az Azure Szinapszisban található Spark-készletek a következő összetevőket tartalmazzák, amelyek alapértelmezés szerint a készletekben elérhetők.

- [Spark mag](https://spark.apache.org/docs/latest/). Tartalmazza a Spark Core, a Spark SQL, a GraphX és a MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract notebook](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Spark Pool-architektúra

Könnyen megismerheti a Spark összetevőit azzal, hogy megérti, hogyan fut a Spark a szinapszis Analyticsen.

A Spark-alkalmazások önálló folyamatokként futnak egy készleten, amelyet a fő program SparkContext objektuma (az illesztőprogram-program) koordinál.

A SparkContext csatlakozhat a fürtfelügyelőhoz, amely erőforrásokat foglal le az alkalmazások között. A cluster Manager [Apache HADOOP fonalat](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). A csatlakozás után a Spark a készlet csomópontjain hajtja végre a végrehajtókat, amelyek számítási és adattárolási folyamatokat futtatnak az alkalmazás számára. Ezt követően a Spark elküldi az alkalmazás kódját a végrehajtóknak. A kódot a SparkContext objektum számára átadott JAR- vagy Python-fájlok határozzák meg. Végül a SparkContext objektum elküldi futtatásra a feladatokat a végrehajtóknak.

A SparkContext futtatja a felhasználó fő funkcióját, és végrehajtja a különböző párhuzamos műveleteket a csomópontokon. Ezután a SparkContext objektum összegyűjti a műveletek eredményeit. A csomópontok a és a fájlrendszerből származó adatok olvasását és írását írják le. A csomópontok a memóriában található átalakított adatokat rugalmasan elosztott adatkészletek (RDD) formájában is gyorsítótárazzák.

A SparkContext összeköti a Spark-készletet, és felelős az alkalmazások irányított aciklikus gráfba (DAG) való átalakításához. A gráf a csomópontokon futó végrehajtó folyamaton belül végrehajtott egyes feladatokból áll. Mindegyik alkalmazás saját végrehajtó folyamatokkal rendelkezik, amelyek az alkalmazás teljes időtartamáig működnek, és több szálon futtatnak feladatokat.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark a szinapszis Analytics használati eseteiben

A szinapszis Analyticsben a Spark-készletek a következő főbb forgatókönyveket teszik lehetővé:

### <a name="data-engineeringdata-preparation"></a>Adattervezés/adatfeldolgozás

Az Apache Spark számos nyelvi funkciót biztosít nagy mennyiségű adat előkészítésének és feldolgozásának elősegítéséhez, így értékesebbé teheti az adatokat, amelyeket azután más szolgáltatások felhasználhatnak a Synapse Analyticsen belül. Ez több nyelven is engedélyezve van (C#, Scala, PySpark, Spark SQL) és a rendelkezésre bocsátott kódtárak a feldolgozáshoz és a csatlakozáshoz.

### <a name="machine-learning"></a>Machine Learning

A Apache Spark a [MLlib](https://spark.apache.org/mllib/), a Spark-ra épülő gépi tanulási kódtár, amelyet a szinapszis Analyticsben használhat a Spark-készletből. A Synapse Analytics Spark-készletei tartalmazzák az Anacondát, amely egy Python-disztribúció számos adatelemzési, köztük gépi tanulási csomaggal. A jegyzetfüzetek beépített támogatásával ötvözve ez egy gépi tanulási alkalmazások létrehozásához ideális környezetet teremt.

## <a name="where-do-i-start"></a>Hol kezdjem el

A következő cikkekben további tudnivalókat talál a szinapszis Analytics Apache Sparkáról:

- [Rövid útmutató: Spark-készlet létrehozása az Azure Szinapszisban](../quickstart-create-apache-spark-pool-portal.md)
- [Rövid útmutató: Apache Spark jegyzetfüzet létrehozása](../quickstart-apache-spark-notebook.md)
- [Oktatóanyag: gépi tanulás Apache Spark használatával](./apache-spark-machine-learning-mllib-notebook.md)
- [Apache Spark hivatalos dokumentáció](https://spark.apache.org/docs/latest/)

> [!NOTE]
> A hivatalos Apache Spark dokumentációja a Spark-konzollal való használatra támaszkodik, ez az Azure szinapszis Sparkon nem érhető el, hanem a notebook-vagy IntelliJ-élmények használatával

## <a name="next-steps"></a>További lépések

Ebben az áttekintésben bemutatjuk az Azure szinapszis Analytics Apache Sparkának alapvető ismereteit. A következő cikkből megtudhatja, hogyan hozhat létre egy Spark-készletet az Azure szinapszis Analyticsben:

- [Spark-készlet létrehozása az Azure Szinapszisban](../quickstart-create-apache-spark-pool-portal.md)
