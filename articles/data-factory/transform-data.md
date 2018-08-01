---
title: Adatok átalakítása az Azure Data Factory használatával |} A Microsoft Docs
description: Ismerje meg, hogyan alakíthat át adatokat vagy dolgozza fel az adatokat az Azure Data Factoryban Hadoop, a Machine Learning vagy az Azure Data Lake Analytics használatával való.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: douglasl
ms.openlocfilehash: e3aefc23cd0f4d8043232214591155aa893f4522
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390136"
---
# <a name="transform-data-in-azure-data-factory"></a>Az Azure Data Factoryban az adatok átalakítása
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Tárolt eljárás](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks-jegyzetfüzet](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET egyéni](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az Azure Data Factory segítségével átalakíthatja, és a nyers adatok feldolgozza az előrejelzési díjat és az elemzések adat-átalakítási tevékenységeket. Egy Adatátalakítási tevékenységgel hajt végre, például Azure HDInsight-fürt vagy egy Azure Batch számítási környezetben. Az egyes átalakítási tevékenységek részletes információt biztosít cikkekre mutató hivatkozások.

A Data Factory támogatja a következő az Adatátalakítási tevékenységeket is hozzáadhatók [folyamatok](concepts-pipelines-activities.md) vagy külön-külön vagy visszavezethetők más tevékenységekkel.

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A HDInsight Hive tevékenység, a Data Factory-folyamatok futtatják a Hive-lekérdezések saját maga, vagy igény szerinti Windows/Linux-alapú HDInsight-fürt. Lásd: [Hive-tevékenység](transform-data-using-hadoop-hive.md) cikket ezzel a tevékenységgel kapcsolatos részletekért. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A HDInsight Pig-tevékenység, a Data Factory-folyamatok Pig-lekérdezéseket a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. Lásd: [Pig-tevékenység alapú](transform-data-using-hadoop-pig.md) cikket ezzel a tevékenységgel kapcsolatos részletekért. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A HDInsight MapReduce-tevékenység, a Data Factory-folyamatok MapReduce-programok saját maga, vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. Lásd: [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md) cikket ezzel a tevékenységgel kapcsolatos részletekért.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming-tevékenység
A HDInsight Streaming-tevékenység a Data Factory-folyamatok Hadoop Streamelési programok saját maga vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. Lásd: [HDInsight Streaming-tevékenység](transform-data-using-hadoop-streaming.md) ezzel a tevékenységgel kapcsolatos részletekért.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A HDInsight Spark-tevékenység, Data Factory-folyamatok a Spark-programok saját HDInsight-fürtön hajtja végre. További információkért lásd: [Invoke Spark-programok Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning-tevékenységek
Az Azure Data Factory lehetővé teszi, hogy egyszerűen hozzon létre egy közzétett Azure Machine Learning webszolgáltatás prediktív elemzési használó folyamatok. Használatával a [kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md) az Azure Data Factory-folyamatot, az előrejelzéseket a batch szolgáltatásban az adatok egy Machine Learning webszolgáltatás hívhatók meg.

Az idő múlásával a prediktív modelleket a Machine Learning pontozási kísérletek kell kell retrained új bemeneti adatkészletek használatával. Miután elkészült, az átképezési, frissítse a pontozási webszolgáltatás retrained Machine Learning-modellhez szeretne. Használhatja a [Update-erőforrástevékenység](update-machine-learning-models.md) a webszolgáltatás újonnan betanított modell frissítésével.  

Lásd: [használata a Machine Learning-tevékenységek](transform-data-using-machine-learning.md) ezek a Machine Learning-tevékenységek részleteit. 

## <a name="stored-procedure-activity"></a>Tárolt eljárás tevékenysége
Használhatja az SQL Server tárolt eljárás tevékenység a Data Factory-folyamatot egy tárolt eljárást az egyik a következő adattárakat elindításához: Azure SQL Database, Azure SQL Data Warehouse, SQL Server-adatbázis a vállalati vagy egy Azure virtuális Gépen. Lásd: [Stored Procedure-tevékenység](transform-data-using-stored-procedure.md) részleteivel.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
Data Lake Analytics U-SQL-tevékenység egy U-SQL-szkriptet futtat egy Azure Data Lake Analytics-fürt. Lásd: [Data Analytics U-SQL-tevékenység](transform-data-using-data-lake-analytics.md) részleteivel. 

## <a name="databricks-notebook-activity"></a>Databricks-jegyzetfüzetek tevékenységeit

Az Azure Databricks-jegyzetfüzetek tevékenységeit a Data Factory-folyamatok Databricks-jegyzetfüzet fut az Azure Databricks-munkaterületen. Az Azure Databricks egy Apache Spark rendszert futtató felügyelt platform. Lásd: [adatok átalakítása a Databricks-jegyzetfüzet futtatásával](transform-data-databricks-notebook.md).

## <a name="databricks-jar-activity"></a>Databricks Jar-tevékenység

Az Azure Databricks Jar tevékenység a Data Factory-folyamatok futtat egy Spark-Jar az Azure Databricks-fürt. Az Azure Databricks egy Apache Spark rendszert futtató felügyelt platform. Lásd: [alakíthat át adatokat egy Jar tevékenység futtatja az Azure Databricksben](transform-data-databricks-jar.md).

## <a name="databricks-python-activity"></a>Databricks Python-tevékenység

A Data Factory-folyamatot az Azure Databricks Python tevékenység fut egy Python-fájlt az Azure Databricks-fürt. Az Azure Databricks egy Apache Spark rendszert futtató felügyelt platform. Lásd: [adatok átalakítása az Azure Databricks egy Python-tevékenység futtatásával](transform-data-databricks-python.md).

## <a name="custom-activity"></a>Egyéni tevékenység
Adatok átalakítása a Data Factory által nem támogatott módon van szüksége, ha egyéni tevékenységek létrehozása a saját adatok feldolgozási logikáját, és használja a tevékenységet a folyamat. Konfigurálhatja úgy az egyéni .NET-tevékenységet egy Azure Batch szolgáltatás vagy egy Azure HDInsight-fürt futtatásához. Lásd: [egyéni tevékenységek használata](transform-data-using-dotnet-custom-activity.md) részleteivel. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

## <a name="compute-environments"></a>Számítási környezetek
Ön létrehoz egy társított szolgáltatást a számítási környezet, majd a társított szolgáltatás egy Adatátalakítási tevékenységgel meghatározásakor. A Data Factory által támogatott számítási környezetek két típusa van. 

- **Igény szerinti**: Ebben az esetben a számítási környezet teljes körűen felügyelt adat-előállítók által. Automatikusan létrejön a Data Factory szolgáltatás előtt egy feladat küldött adatok feldolgozásához, és eltávolítja a feladat elvégzésekor. Konfigurálja, és a feladat-végrehajtási, a kezelő és a műveletek rendszerindítása igény szerinti számítási környezet részletes beállításainak kezeléséhez. 
- **Bring Your Own**: Ebben az esetben a Data Factory társított szolgáltatásként regisztrálhatja a saját számítási környezetben (például a HDInsight fürt). A számítási környezet Ön által felügyelt és a Data Factory szolgáltatás használ a tevékenységek végrehajtásához. 

Lásd: [számítási társított szolgáltatások](compute-linked-services.md) a cikkben megismerheti a Data Factory által támogatott számítási szolgáltatások. 

## <a name="next-steps"></a>További lépések
Tekintse meg a következő oktatóanyaggal egy példát egy Adatátalakítási tevékenységgel: [oktatóanyag: adatok átalakítása a Spark használatával](tutorial-transform-data-spark-powershell.md)
