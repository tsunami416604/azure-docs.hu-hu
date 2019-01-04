---
title: 'Adatátalakítás: Adatok folyamata és átalakítása |} A Microsoft Docs'
description: Ismerje meg, hogyan alakíthat át adatokat vagy dolgozza fel az adatokat az Azure Data Factoryban Hadoop, a Machine Learning vagy az Azure Data Lake Analytics használatával való.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b1a99f2872a69e01232c69a73f36319552429ca0
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022444"
---
# <a name="transform-data-in-azure-data-factory"></a>Az Azure Data Factoryban az adatok átalakítása
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
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [Data Factory az Adatátalakítási tevékenységeket](../transform-data.md).

Ez a cikk ismerteti az Azure Data Factory segítségével átalakíthatja, és a nyers adatok feldolgozza az előrejelzési díjat és az elemzések adat-átalakítási tevékenységeket. Egy Adatátalakítási tevékenységgel hajt végre, például Azure HDInsight-fürt vagy egy Azure Batch számítási környezetben. Az egyes átalakítási tevékenységek részletes információt biztosít cikkekre mutató hivatkozások.

A Data Factory támogatja a következő az Adatátalakítási tevékenységeket is hozzáadhatók [folyamatok](data-factory-create-pipelines.md) vagy külön-külön vagy visszavezethetők más tevékenységekkel.

> [!NOTE]
> Lépésről lépésre egy bemutatóért lásd: [egy folyamat létrehozása Hive-átalakítás](data-factory-build-your-first-pipeline.md) cikk.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A HDInsight Hive tevékenység, a Data Factory-folyamatok futtatják a Hive-lekérdezések saját maga, vagy igény szerinti Windows/Linux-alapú HDInsight-fürt. Lásd: [Hive-tevékenység](data-factory-hive-activity.md) cikket ezzel a tevékenységgel kapcsolatos részletekért. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A HDInsight Pig-tevékenység, a Data Factory-folyamatok Pig-lekérdezéseket a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. Lásd: [Pig-tevékenység](data-factory-pig-activity.md) cikket ezzel a tevékenységgel kapcsolatos részletekért. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A HDInsight MapReduce-tevékenység, a Data Factory-folyamatok MapReduce-programok saját maga, vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. Lásd: [MapReduce-tevékenység](data-factory-map-reduce.md) cikket ezzel a tevékenységgel kapcsolatos részletekért.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-tevékenység
A HDInsight Streaming-tevékenység a Data Factory-folyamatok Hadoop Streamelési programok saját maga vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. Lásd: [HDInsight Streaming-tevékenység](data-factory-hadoop-streaming-activity.md) ezzel a tevékenységgel kapcsolatos részletekért.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A HDInsight Spark-tevékenység, Data Factory-folyamatok a Spark-programok saját HDInsight-fürtön hajtja végre. További információkért lásd: [Invoke Spark-programok Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning-tevékenységek
Az Azure Data Factory lehetővé teszi, hogy egyszerűen hozzon létre egy közzétett Azure Machine Learning webszolgáltatás prediktív elemzési használó folyamatok. Használatával a [kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) az Azure Data Factory-folyamatot, az előrejelzéseket a batch szolgáltatásban az adatok egy Machine Learning webszolgáltatás hívhatók meg.

Az idő múlásával a prediktív modelleket a Machine Learning pontozási kísérletek kell kell retrained új bemeneti adatkészletek használatával. Miután elkészült, az átképezési, frissítse a pontozási webszolgáltatás retrained Machine Learning-modellhez szeretne. Használhatja a [Erőforrástevékenység frissítése](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) a webszolgáltatás újonnan betanított modell frissítésével.  

Lásd: [használata a Machine Learning-tevékenységek](data-factory-azure-ml-batch-execution-activity.md) ezek a Machine Learning-tevékenységek részleteit. 

## <a name="stored-procedure-activity"></a>Tárolt eljárás tevékenysége
Az SQL Server tárolt eljárás tevékenység használhatja a Data Factory-folyamat a következő adattárakat egyikét a tárolt eljárás meghívása: Az Azure SQL Database, Azure SQL Data Warehouse, SQL Server-adatbázis a vállalati vagy egy Azure virtuális Gépen. Lásd: [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) részleteivel.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
Data Lake Analytics U-SQL-tevékenység egy U-SQL-szkriptet futtat egy Azure Data Lake Analytics-fürt. Lásd: [Data Analytics U-SQL-tevékenység](data-factory-usql-activity.md) részleteivel. 

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
Adatok átalakítása a Data Factory által nem támogatott módon van szüksége, ha egyéni tevékenységek létrehozása a saját adatok feldolgozási logikáját, és használja a tevékenységet a folyamat. Konfigurálhatja úgy az egyéni .NET-tevékenységet egy Azure Batch szolgáltatás vagy egy Azure HDInsight-fürt futtatásához. Lásd: [egyéni tevékenységek használata](data-factory-use-custom-activities.md) részleteivel. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

## <a name="compute-environments"></a>Számítási környezetek
Ön létrehoz egy társított szolgáltatást a számítási környezet, majd a társított szolgáltatás egy Adatátalakítási tevékenységgel meghatározásakor. A Data Factory által támogatott számítási környezetek két típusa van. 

1. **Igény szerinti**:  Ebben az esetben a számítási környezet teljes körűen felügyelt adat-előállítók által. Automatikusan létrejön a Data Factory szolgáltatás előtt egy feladat küldött adatok feldolgozásához, és eltávolítja a feladat elvégzésekor. Konfigurálja, és a feladat-végrehajtási, a kezelő és a műveletek rendszerindítása igény szerinti számítási környezet részletes beállításainak kezeléséhez. 
2. **Használhat sajátot**: Ebben az esetben a saját számítási környezetben (például a HDInsight fürt) regisztrálhat a Data Factory társított szolgáltatásként. A számítási környezet Ön által felügyelt és a Data Factory szolgáltatás használ a tevékenységek végrehajtásához. 

Lásd: [számítási társított szolgáltatások](data-factory-compute-linked-services.md) a cikkben megismerheti a Data Factory által támogatott számítási szolgáltatások. 

## <a name="summary"></a>Összegzés
Az Azure Data Factory támogatja a következő adat-átalakítási tevékenységeket és a számítási környezetek a tevékenységek. Az átalakítási tevékenységek hozzá a folyamatokhoz egyenként, vagy egy másik tevékenységgel láncolt is.

| Adatátalakítási tevékenység | Számítási környezet |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning-tevékenységek: Kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Tárolt eljárás](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse vagy SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] vagy Azure Batch |

