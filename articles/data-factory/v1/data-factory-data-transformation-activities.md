---
title: 'Adatátalakítás: Adatok feldolgozása & átalakítása '
description: Ismerje meg, hogyan alakíthatja át az adatokat vagy dolgozhat fel adatokat az Azure Data Factoryban a Hadoop, a Machine Learning vagy az Azure Data Lake Analytics használatával.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 5b3e2db9b9769dee7599a2446b272e04cc0bedf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703395"
---
# <a name="transform-data-in-azure-data-factory"></a>Adatok átalakítása az Azure Data Factoryben
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Tárolt eljárás](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET egyéni](data-factory-use-custom-activities.md)

## <a name="overview"></a>Áttekintés
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [adatátalakítási tevékenységeket a Data Factory alkalmazásban.](../transform-data.md)

Ez a cikk ismerteti az Azure Data Factory adatátalakítási tevékenységeit, amelyek segítségével átalakíthatja és feldolgozhatja a nyers adatokat előrejelzésekké és elemzési információkká. Az átalakítási tevékenység végrehajtása egy számítógépes környezetben, például az Azure HDInsight-fürtvagy egy Azure Batch. Az egyes átalakítási tevékenységekre vonatkozó részletes információkat mutató cikkekre mutató hivatkozásokat tartalmaz.

A Data Factory a következő adatátalakítási tevékenységeket támogatja, amelyek egyenként vagy más tevékenységgel láncolhatók a [folyamatokhoz.](data-factory-create-pipelines.md)

> [!NOTE]
> Forgatókönyv lépésenkénti útmutató, [lásd: Folyamat létrehozása hive transzformációs cikket.](data-factory-build-your-first-pipeline.md)  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A DATA Factory-folyamat HDInsight-struktúra működése hive-lekérdezéseket hajt végre a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön. A tevékenységgel kapcsolatos részletekért tekintse meg a [Hive-tevékenység](data-factory-hive-activity.md) cikkét. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig tevékenység
A Data Factory-folyamat HDInsight Pig-tevékenysége saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Pig-lekérdezéseket. A tevékenységgel kapcsolatos részletekért lásd a [Sertéstevékenység](data-factory-pig-activity.md) című cikket. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce tevékenység
A DATA Factory-folyamat HDInsight MapReduce tevékenysége a MapReduce programokat saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. A tevékenységgel kapcsolatos részletekért tekintse meg a [MapReduce Activity](data-factory-map-reduce.md) cikket.

## <a name="hdinsight-streaming-activity"></a>HDInsight-streamelési tevékenység
A DATA Factory-folyamat HDInsight-streamelési tevékenysége saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Hadoop-streamelési programokat. A tevékenységgel kapcsolatos részletekért tekintse meg a [HDInsight streamelési tevékenységet.](data-factory-hadoop-streaming-activity.md)

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A Data Factory-folyamat HDInsight Spark-tevékenysége spark-programokat hajt végre a saját HDInsight-fürtén. További információt a [Spark-programok meghívása az Azure Data Factoryból című témakörben talál.](data-factory-spark.md) 

## <a name="machine-learning-activities"></a>Gépi tanulási tevékenységek
Az Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre folyamatokat, amelyek egy közzétett Azure Machine Learning webszolgáltatást használnak a prediktív elemzéshez. A [batch-végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) egy Azure Data Factory-folyamat használatával egy Machine Learning-webszolgáltatás meghívásával előrejelzéseket készíthet a kötegben lévő adatokról.

Idővel a machine learning-pontozási kísérletek prediktív modelljeit újra kell képezni az új bemeneti adatkészletek használatával. Miután végzett az átképzés, frissíteni szeretné a pontozási webszolgáltatás az átedzett Machine Learning modell. [Az erőforrás-tevékenység frissítése](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) segítségével frissítheti a webszolgáltatást az újonnan betanított modellel.  

A [Machine Learning-tevékenységek használata](data-factory-azure-ml-batch-execution-activity.md) a Machine Learning-tevékenységek használatával kapcsolatos részletekért. 

## <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
Az SQL Server tárolt eljárás tevékenység egy Data Factory folyamat ban meghívhat egy tárolt eljárást a következő adattárak: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database a vállalatnál, vagy egy Azure virtuális gép. A részleteket lásd [a Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) című cikkben.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL tevékenység
A Data Lake Analytics U-SQL Activity egy U-SQL-parancsfájlt futtat egy Azure Data Lake Analytics-fürtön. A részleteket a [Data Analytics U-SQL Activity](data-factory-usql-activity.md) cikkben találja. 

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
Ha olyan módon kell átalakítania az adatokat, amelyet a Data Factory nem támogat, létrehozhat egy egyéni tevékenységet a saját adatfeldolgozási logikájával, és használhatja a folyamatban lévő tevékenységet. Az egyéni .NET-tevékenység konfigurálása azure Batch-szolgáltatás vagy egy Azure HDInsight-fürt használatával történő futtatásra konfigurálható. A részleteket az [Egyéni tevékenységek használata](data-factory-use-custom-activities.md) című cikkben találja. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

## <a name="compute-environments"></a>Számítási környezetek
Hozzon létre egy csatolt szolgáltatást a számítási környezethez, majd használja a csatolt szolgáltatást átalakítási tevékenység definiálásakor. A Data Factory kétféle számítási környezetet támogat. 

1. **Igény szerint:** Ebben az esetben a számítási környezetet a Data Factory teljes körűen kezeli. A Data Factory szolgáltatás automatikusan létrehozza, mielőtt egy feladatot elküldenének az adatok feldolgozásához, és a feladat befejezésekor eltávolítanák. Az igény szerinti számítási környezet részletes beállításait konfigurálhatja és szabályozhatja a feladatvégrehajtáshoz, a fürtkezeléshez és a rendszerindítási műveletekhez. 
2. **Bring Your Own:** Ebben az esetben regisztrálhatja saját számítástechnikai környezet (például HDInsight-fürt) a Data Factory csatolt szolgáltatásként. A számítási környezetet Ön kezeli, és a Data Factory szolgáltatás ezt használja a tevékenységek végrehajtásához. 

A Data Factory által támogatott számítási szolgáltatásokról a [Compute Linked Services](data-factory-compute-linked-services.md) cikkben olvashat. 

## <a name="summary"></a>Összefoglalás
Az Azure Data Factory a következő adatátalakítási tevékenységeket és a tevékenységek számítási környezeteit támogatja. Az átalakítási tevékenységek hozzáadhatók a folyamatokhoz egyenként vagy más tevékenységgel láncolva.

| Adatátalakítási tevékenység | Számítási környezet |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning-tevékenységek: kötegelt végrehajtás és az erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Tárolt eljárás](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse vagy SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] vagy Azure Batch |

