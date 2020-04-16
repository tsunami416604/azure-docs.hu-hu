---
title: Mi az Apache Spark az Azure Synapse Analytics?
description: Ez a cikk bemutatja az Apache Spark ot az Azure Synapse Analytics szolgáltatásban, valamint a különböző forgatókönyveket, amelyekben használhatja a Sparkot.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4155e419bb03613abad808ca09d84bcb6583291f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423648"
---
# <a name="what-is-apache-spark-in-azure-synapse-analytics"></a>Mi az Apache Spark az Azure Synapse Analytics szolgáltatásban?

Az Apache Spark egy párhuzamos feldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data-analitikus alkalmazások teljesítményének növelése érdekében. Az Apache Spark az Azure Synapse Analytics szolgáltatásban az Apache Spark felhőbeli microsoftos implementációinak egyike. Az Azure Synapse megkönnyíti a Spark-készlet (előzetes verzió) létrehozását és konfigurálását az Azure-ban. Az Azure Synapse Spark-készletei kompatibilisek az Azure Storage és az Azure Data Lake Generation 2 Storage szolgáltatással. Így az Azure Spark-készletek segítségével feldolgozhatja az Azure-ban tárolt adatokat.

![Spark: egyesített keretrendszer](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>Mi az Apache Spark

Az Apache Spark primitíveket biztosít a memórián belüli fürtös használathoz. A Spark-feladatokkal az adatok betölthetők és gyorsítótárazhatók a memóriába, majd ismétlődő jelleggel lekérdezhetők. A memórián belüli számítástechnika sokkal gyorsabb, mint a lemezalapú alkalmazások. A Spark több programozási nyelvvel is integrálható, így kezelheti az elosztott adatkészleteket, például a helyi gyűjteményeket. Nem kell mindent térképként rendszerezni és csökkenteni a műveletek számát.

![Hagyományos MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Spark-készletek az Azure Synapse kínál teljes körűen felügyelt Spark-szolgáltatás. A Spark-készlet synapse Analytics-ben való létrehozásának előnyei itt találhatók.

| Szolgáltatás | Leírás |
| --- | --- |
| Sebesség és hatékonyság |Spark-példányok indítása körülbelül 2 perc alatt kevesebb, mint 60 csomópontok és körülbelül 5 perc alatt több mint 60 csomópontok. A példány alapértelmezés szerint leáll, 5 perccel az utolsó végrehajtott feladat után, kivéve, ha egy jegyzetfüzet-kapcsolat tartja életben. |
| Könnyű létrehozás |Az Azure Portal, az Azure PowerShell vagy a Synapse Analytics .NET SDK használatával percek alatt létrehozhat egy új Spark-készletet az Azure Synapse-ben. Lásd: [A Spark-készletek első lépései a Synapse Analytics szolgáltatásban.](apache-spark-notebook-create-spark-use-sql.md) |
| Könnyű használat |A Synapse Analytics a [Nteract](https://nteract.io/)programból származó egyéni jegyzetfüzetet tartalmaz. Ezeket a notebookokat interaktív adatfeldolgozásra és -vizualizációra használhatja.|
| REST API-k |A Spark in Synapse Analytics tartalmazza az [Apache Livy-t,](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)egy REST API-alapú Spark-feladatkiszolgálót a feladatok távolról történő elküldéséhez és figyeléséhez. |
| Az Azure Data Lake Storage 2.| Az Azure Synapse Spark-készletei használhatják az Azure Data Lake Storage Generation 2 és a BLOB storage-ot. A Data Lake Storage szolgáltatásról az [Azure Data Lake Storage áttekintése című témakörben olvashat bővebben.](../../data-lake-store/data-lake-store-overview.md) |
| Integráció külső integrált fejlesztői környezetekkel (IDE) | Az Azure Synapse egy IDE-beépülő modult biztosít a [Jetbrains IntelliJ IDEA számára,](https://www.jetbrains.com/idea/) amely hasznos lehet alkalmazások létrehozásához és a Spark-készletbe való beküldéséhez. |
| Előre betöltött Anaconda-könyvtárak |Az Azure Synapse Spark-készletei előre telepített Anaconda-kódtárakat is hoznak létre. Az [Anaconda](https://docs.continuum.io/anaconda/) közel 200 könyvtárat biztosít például a Machine Learning szolgáltatáshoz, az adatok elemzéséhez vagy a megjelenítéshez. |
| Méretezhetőség | Az Apache Spark az Azure Synapse-készletekben engedélyezve lehet az automatikus méretezés, így a készletek szükség szerint fel-le skálázhatók. A Spark-készletek adatvesztés nélkül is leállíthatók, mivel az összes adat az Azure Storage vagy a Data Lake Storage tárolja. |

Az Azure Synapse Spark-készletei a következő összetevőket tartalmazzák, amelyek alapértelmezés szerint elérhetők a készletekben.

- [Spark mag](https://spark.apache.org/docs/latest/). Tartalmazza a Spark Core, spark SQL, GraphX és MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract notebook](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Spark-medence architektúrája

A Spark összetevői könnyen érthetők, ha tisztában van azzal, hogy a Spark hogyan fut a Synapse Analytics szolgáltatásban.

A Spark-alkalmazások független folyamatkészletként futnak egy készleten, amelyet a fő programban (az illesztőprogram-programban) lévő SparkContext objektum koordinál.

A SparkContext csatlakozhat a fürtkezelőhöz, amely erőforrásokat oszt ki az alkalmazások között. A fürtkezelő az [Apache Hadoop YARN.](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) A csatlakoztatás után a Spark beszerzi a végrehajtókat a készlet csomópontjain, amelyek számításokat futtató és az alkalmazás adatait tároló folyamatok. Ezt követően a Spark elküldi az alkalmazás kódját a végrehajtóknak. A kódot a SparkContext objektum számára átadott JAR- vagy Python-fájlok határozzák meg. Végül a SparkContext objektum elküldi futtatásra a feladatokat a végrehajtóknak.

A SparkContext futtatja a felhasználó fő függvényét, és végrehajtja a különböző párhuzamos műveleteket a csomópontokon. Ezután a SparkContext objektum összegyűjti a műveletek eredményeit. A csomópontok adatokat olvasnak és írnak a fájlrendszerből és a fájlrendszerbe. A csomópontok is cache átalakított adatok a memóriában rugalmas elosztott adatkészletek (RDDs).

A SparkContext csatlakozik a Spark-készlet, és felelős egy alkalmazás átalakítása egy irányított gráf (DAG). A grafikon olyan egyedi feladatokból áll, amelyek végrehajtása a csomópontokon egy végrehajtó folyamaton belül történik. Mindegyik alkalmazás saját végrehajtó folyamatokkal rendelkezik, amelyek az alkalmazás teljes időtartamáig működnek, és több szálon futtatnak feladatokat.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark a Synapse Analytics használati eseteiben

A Synapse Analytics spark-készletei a következő kulcsfontosságú forgatókönyveket teszik lehetővé:

### <a name="data-engineeringdata-preparation"></a>Adattervezés/adatelőkészítés

Az Apache Spark számos nyelvi funkciót tartalmaz a nagy mennyiségű adat előkészítésének és feldolgozásának támogatására, hogy értékesebbé lehessen tenni, majd a Synapse Analytics más szolgáltatásai is feltudják használni. Ez több nyelven (C#, Scala, PySpark, Spark SQL) keresztül engedélyezett, és a feldolgozáshoz és a kapcsolathoz biztosított könyvtárak.

### <a name="machine-learning"></a>Machine Learning

Az Apache Spark az [MLlib-et](https://spark.apache.org/mllib/), a Spark ra épülő gépi tanulási könyvtárat is bevezeti, amelyet a Synapse Analytics Spark-készletéből használhat. A Synapse Analytics Spark-készletei közé tartozik az Anaconda, egy Python-disztribúció, amely számos adatelemzési csomaggal, többek között gépi tanulással rendelkezik. A jegyzetfüzetek beépített támogatásával kombinálva gépi tanulási alkalmazások létrehozására alkalmas környezet áll.

## <a name="where-do-i-start"></a>Hol kezdjem

Az alábbi cikkekből többet is megtudhat az Apache Spark ról a Synapse Analytics szolgáltatásban:

- [Rövid útmutató: Spark-készlet létrehozása az Azure Synapse-ban](./apache-spark-notebook-create-spark-use-sql.md)
- [Oktatóanyag: Gépi tanulás az Apache Spark használatával](./apache-spark-machine-learning-mllib-notebook.md)
- [Az Apache Spark hivatalos dokumentációja](https://spark.apache.org/docs/latest/)

> [!NOTE]
> Néhány hivatalos Apache Spark dokumentáció támaszkodik a spark-konzol, ez nem érhető el az Azure Synapse Spark, használja a notebook vagy IntelliJ tapasztalatok helyett

## <a name="next-steps"></a>További lépések

Ebben az áttekintésben az Apache Spark alapvető ismereteket kaphat az Azure Synapse Analytics szolgáltatásban. A következő cikkből megtudhatja, hogyan hozhat létre Spark-készletet az Azure Synapse Analytics szolgáltatásban:

- [Spark-készlet létrehozása az Azure Synapse-ban](./apache-spark-notebook-create-spark-use-sql.md)
