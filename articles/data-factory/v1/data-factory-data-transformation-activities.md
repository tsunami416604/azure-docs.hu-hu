---
title: "Adatok átalakítása: Folyamat & átalakítási adatok |} Microsoft Docs"
description: "Megtudhatja, hogyan átalakítási adatok vagy az Azure Data Factory a Hadoop, a Machine Learning vagy az Azure Data Lake Analytics folyamat közé."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: a631708ce755b07701c053e217dd658e5f7bb82b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="transform-data-in-azure-data-factory"></a>Az Azure Data Factoryben az adatok átalakítása
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Tárolt eljárás](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Egyéni .NET](data-factory-use-custom-activities.md)

## <a name="overview"></a>Áttekintés
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [adatok átalakítása tevékenységek 2-es verzióját a Data factoryban](../transform-data.md).

Ez a cikk ismerteti az Azure Data Factory használatával átalakító, és feldolgozza a nyers adatok előrejelzéseket és elemzések adatok átalakítása tevékenységek. Egy átalakítási tevékenységet hajtja végre, például az Azure HDInsight-fürt vagy egy Azure Batch számítási környezetben. Cikkek hivatkozásainak átalakítása tevékenységeiről részletes információkat biztosít.

Adat-előállító támogatja a következő adatok átalakítása tevékenységek is hozzáadhatók [folyamatok](data-factory-create-pipelines.md) vagy önállóan vagy egy másik tevékenységgel kapcsolt.

> [!NOTE]
> Lépésenkénti utasításokat talál útmutatást lásd: [hozzon létre egy folyamatot Hive átalakítása](data-factory-build-your-first-pipeline.md) cikk.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive tevékenység
A HDInsight Hive tevékenység a Data Factory-folyamat saját Hive-lekérdezéseket vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [Hive tevékenység](data-factory-hive-activity.md) szóló cikkben olvashat ennek a tevékenységnek. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig tevékenység
A HDInsight Pig tevékenység a Data Factory-folyamat saját Pig lekérdezések vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [Pig tevékenység](data-factory-pig-activity.md) szóló cikkben olvashat ennek a tevékenységnek. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce művelethez
A HDInsight MapReduce művelethez a Data Factory-folyamat saját MapReduce programok vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [MapReduce művelethez](data-factory-map-reduce.md) szóló cikkben olvashat ennek a tevékenységnek.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streamelési tevékenységben
A HDInsight Streamelési tevékenységben a Data Factory-folyamat saját Hadoop Streamelési programok vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [HDInsight Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md) ezzel a tevékenységgel kapcsolatos részletekért.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A HDInsight Spark tevékenység a Data Factory-folyamat saját HDInsight-fürt végrehajtja a Spark programokat. További információkért lásd: [meghívása Spark programok az Azure Data Factory](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning tevékenységek
Az Azure Data Factory lehetővé teszi, hogy könnyen létrehozhat egy közzétett Azure Machine Learning webszolgáltatás prediktív elemzési használó folyamatok. Használja a [kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) egy Azure Data Factory-folyamat a hívhat meg a Machine Learning webszolgáltatás előrejelzéseket készítsen a kötegben lévő adatokat.

Az idő múlásával a Machine Learning scoring-kísérletek a prediktív modellek kell kell retrained új bemeneti adatkészletek használata. Miután elkészült, az átképezési, retrained gépi tanulási modell a pontozási webszolgáltatás frissíteni kívánt. Használhatja a [erőforrás tevékenység frissítése](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) frissítheti a webszolgáltatás a újonnan betanított modell.  

Lásd: [használata a Machine Learning tevékenységek](data-factory-azure-ml-batch-execution-activity.md) ezeket a Machine Learning tevékenységeket vonatkozó további információért. 

## <a name="stored-procedure-activity"></a>A tárolt eljárási tevékenység
Használhatja az SQL Server tárolt eljárási tevékenység a Data Factory-folyamat a következő adatokat tárolja egyikében tárolt eljárás meghívása: Azure SQL Database, Azure SQL Data Warehouse szolgáltatásban a vállalat SQL Server-adatbázis vagy egy Azure virtuális Gépen. Lásd: [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) cikkben alább.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
Data Lake Analytics U-SQL-tevékenység egy Azure Data Lake Analytics-fürt U-SQL parancsfájlt futtat. Lásd: [Analytics U-SQL tevékenységet](data-factory-usql-activity.md) cikkben alább. 

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
Ha úgy, hogy nem támogatja a Data Factory adatok átalakítása van szüksége, hozzon létre egy egyéni tevékenység saját adatokat feldolgozó logika, és használja a tevékenységet a feldolgozási. Az egyéni .NET tevékenység segítségével futtatja, az Azure Batch szolgáltatás vagy az Azure HDInsight-fürtöt is konfigurálhat. Lásd: [egyéni tevékenységeket felhasználni](data-factory-use-custom-activities.md) cikkben alább. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

## <a name="compute-environments"></a>Számítási környezetek
A számítási környezet társított szolgáltatás létrehozása, és kövesse a társított szolgáltatás, egy átalakítási tevékenységet meghatározásakor. A Data Factory által támogatott számítási környezetek két típusa van. 

1. **Igény szerinti**: Ebben az esetben a számítógépes környezet teljes kezeli adat-Előállítóban. Az automatikusan hozta létre a Data Factory szolgáltatásnak egy feladat folyamata adatokat küldött, és eltávolítja a feladat befejezése előtt. Konfigurálja, és az igény szerinti számítási környezet feladat végrehajtási, a kiszolgálófürt-felügyelet és a műveletek rendszerindítása részletes beállításainak kezeléséhez. 
2. **Bring Your Own**: Ebben az esetben regisztrálhatja a saját informatikai környezetben (például HDInsight-fürtök) a Data Factory kapcsolt szolgáltatásként. A számítógépes környezet által felügyelt, és a Data Factory szolgáltatásnak a tevékenységek végrehajtásához használja. 

Lásd: [összekapcsolt szolgáltatások számítási](data-factory-compute-linked-services.md) cikkben tájékozódhat az adat-előállító által támogatott számítási szolgáltatások. 

## <a name="summary"></a>Összefoglalás
Az Azure Data Factory támogatja a következő adatok átalakítása tevékenységek és a számítási környezetek a tevékenységekhez. Az átalakítási tevékenységek hozzá folyamatok vagy külön-külön, vagy egy másik tevékenységgel kapcsolt.

| Adatátalakítási tevékenység | Számítási környezet |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning-tevékenységek: kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Tárolt eljárás](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse vagy SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] vagy Azure Batch |

