---
title: Az adatok Azure Data Factory használatával |} Microsoft Docs
description: Megtudhatja, hogyan átalakítási adatok vagy az Azure Data Factory a Hadoop, a Machine Learning vagy az Azure Data Lake Analytics folyamat közé.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 90089c009d3515df3df2453c710bac143ee7d90f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619748"
---
# <a name="transform-data-in-azure-data-factory"></a>Az Azure Data Factoryben az adatok átalakítása
> [!div class="op_single_selector"]
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [Hadoop Streaming](transform-data-using-hadoop-streaming.md)
> * [Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Tárolt eljárás](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Egyéni .NET](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az Azure Data Factory használatával átalakító, és feldolgozza a nyers adatok előrejelzéseket és elemzések adatok átalakítása tevékenységek. Egy átalakítási tevékenységet hajtja végre, például az Azure HDInsight-fürt vagy egy Azure Batch számítási környezetben. Cikkek hivatkozásainak átalakítása tevékenységeiről részletes információkat biztosít.

Adat-előállító támogatja a következő adatok átalakítása tevékenységek is hozzáadhatók [folyamatok](concepts-pipelines-activities.md) vagy önállóan vagy egy másik tevékenységgel kapcsolt.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [átalakítása tevékenységek adat-előállítóban 1-es verziójú](v1/data-factory-data-transformation-activities.md).
 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive tevékenység
A HDInsight Hive tevékenység a Data Factory-folyamat saját Hive-lekérdezéseket vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [tevékenység Hive](transform-data-using-hadoop-hive.md) szóló cikkben olvashat ennek a tevékenységnek. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig tevékenység
A HDInsight Pig tevékenység a Data Factory-folyamat saját Pig lekérdezések vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [tevékenység sertésfelmérés](transform-data-using-hadoop-pig.md) szóló cikkben olvashat ennek a tevékenységnek. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce művelethez
A HDInsight MapReduce művelethez a Data Factory-folyamat saját MapReduce programok vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md) szóló cikkben olvashat ennek a tevékenységnek.

## <a name="hdinsight-streaming-activity"></a>HDInsight Streamelési tevékenységben
A HDInsight Streamelési tevékenységben a Data Factory-folyamat saját Hadoop Streamelési programok vagy igény szerinti Windows/Linux-alapú HDInsight-fürt hajt végre. Lásd: [HDInsight Streamelési tevékenységben](transform-data-using-hadoop-streaming.md) ezzel a tevékenységgel kapcsolatos részletekért.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A HDInsight Spark tevékenység a Data Factory-folyamat saját HDInsight-fürt végrehajtja a Spark programokat. További információkért lásd: [meghívása Spark programok az Azure Data Factory](transform-data-using-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning tevékenységek
Az Azure Data Factory lehetővé teszi, hogy könnyen létrehozhat egy közzétett Azure Machine Learning webszolgáltatás prediktív elemzési használó folyamatok. Használja a [kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md) egy Azure Data Factory-folyamat a hívhat meg a Machine Learning webszolgáltatás előrejelzéseket készítsen a kötegben lévő adatokat.

Az idő múlásával a Machine Learning scoring-kísérletek a prediktív modellek kell kell retrained új bemeneti adatkészletek használata. Miután elkészült, az átképezési, retrained gépi tanulási modell a pontozási webszolgáltatás frissíteni kívánt. Használhatja a [frissítése erőforrástevékenység](update-machine-learning-models.md) frissítheti a webszolgáltatás a újonnan betanított modell.  

Lásd: [használata a Machine Learning tevékenységek](transform-data-using-machine-learning.md) ezeket a Machine Learning tevékenységeket vonatkozó további információért. 

## <a name="stored-procedure-activity"></a>Tárolt eljárás tevékenysége
Használhatja az SQL Server tárolt eljárási tevékenység a Data Factory-folyamat a következő adatokat tárolja egyikében tárolt eljárás meghívása: Azure SQL Database, Azure SQL Data Warehouse szolgáltatásban a vállalat SQL Server-adatbázis vagy egy Azure virtuális Gépen. Lásd: [tárolt eljárási tevékenység](transform-data-using-stored-procedure.md) cikkben alább.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-tevékenység
Data Lake Analytics U-SQL-tevékenység egy Azure Data Lake Analytics-fürt U-SQL parancsfájlt futtat. Lásd: [adatok Analytics U-SQL-tevékenység](transform-data-using-data-lake-analytics.md) cikkben alább. 

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
Ha úgy, hogy nem támogatja a Data Factory adatok átalakítása van szüksége, hozzon létre egy egyéni tevékenység saját adatokat feldolgozó logika, és használja a tevékenységet a feldolgozási. Az egyéni .NET tevékenység segítségével futtatja, az Azure Batch szolgáltatás vagy az Azure HDInsight-fürtöt is konfigurálhat. Lásd: [egyéni tevékenységeket felhasználni](transform-data-using-dotnet-custom-activity.md) cikkben alább. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

## <a name="compute-environments"></a>Számítási környezetek
A számítási környezet társított szolgáltatás létrehozása, és kövesse a társított szolgáltatás, egy átalakítási tevékenységet meghatározásakor. A Data Factory által támogatott számítási környezetek két típusa van. 

- **Igény szerinti**: Ebben az esetben a számítógépes környezet teljes kezeli adat-Előállítóban. Az automatikusan hozta létre a Data Factory szolgáltatásnak egy feladat folyamata adatokat küldött, és eltávolítja a feladat befejezése előtt. Konfigurálja, és az igény szerinti számítási környezet feladat végrehajtási, a kiszolgálófürt-felügyelet és a műveletek rendszerindítása részletes beállításainak kezeléséhez. 
- **Bring Your Own**: Ebben az esetben regisztrálhatja a saját informatikai környezetben (például HDInsight-fürtök) a Data Factory kapcsolt szolgáltatásként. A számítógépes környezet által felügyelt, és a Data Factory szolgáltatásnak a tevékenységek végrehajtásához használja. 

Lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikkben tájékozódhat az adat-előállító által támogatott számítási szolgáltatások. 

## <a name="next-steps"></a>További lépések
Tekintse meg az alábbi példa egy átalakítási tevékenységgel: [oktatóanyag: adatok használata Spark átalakítása](tutorial-transform-data-spark-powershell.md)
