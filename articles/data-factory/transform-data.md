---
title: Adatok átalakítása
description: Ismerje meg, hogyan alakíthatja át az adatokat vagy dolgozhat fel adatokat az Azure Data Factoryban a Hadoop, a Machine Learning vagy az Azure Data Lake Analytics használatával.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 1f920f2672c19455a8e8ac979e8d6d1eb14d4c35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924300"
---
# <a name="transform-data-in-azure-data-factory"></a>Adatok átalakítása az Azure Data Factoryben

> [!div class="op_single_selector"]
> * [Adatfolyam leképezése](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight-adatfolyam](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Tárolt eljárás](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET egyéni](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti az Azure Data Factory adatátalakítási tevékenységeit, amelyek segítségével átalakíthatja és feldolgozhatja a nyers adatokat előrejelzésekké és nagy méretű elemzésekké. Az átalakítási tevékenység végrehajtása egy számítógépes környezetben, például az Azure Databricks vagy az Azure HDInsight. Az egyes átalakítási tevékenységekre vonatkozó részletes információkat mutató cikkekre mutató hivatkozásokat tartalmaz.

A Data Factory a következő adatátalakítási tevékenységeket támogatja, amelyek egyenként vagy más tevékenységgel láncolhatók a [folyamatokhoz.](concepts-pipelines-activities.md)

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Átalakítás natív módon az Azure Data Factoryban az adatfolyamokkal

### <a name="mapping-data-flows"></a>Adatfolyamok leképezése

Az adatfolyamok leképezése vizuálisan tervezett adatátalakítások az Azure Data Factoryban. Az adatfolyamok lehetővé teszik az adatmérnökök számára, hogy kód írása nélkül fejlesszenek ki grafikus adatátalakítási logikát. Az eredményül kapott adatfolyamok az Azure Data Factory-folyamatokon belüli tevékenységekként kerülnek végrehajtásra, amelyek kibővített Spark-fürtöket használnak. Az adatfolyam-tevékenységek a data factory ütemezési, vezérlési, folyamat- és figyelési képességeivel működőképessé tehetők. További információt az [adatfolyamatok feltérképezése](concepts-data-flow-overview.md)című témakörben talál.

### <a name="wrangling-data-flows"></a>Vonagló adatfolyamok

Az Azure Data Factory ban zajló adatfolyamatok lehetővé teszik, hogy a kódmentes adatelőkészítést felhőalapú, iteratív módon végezze el. A cigiás adatfolyamok integrálhatók a [Power Query Online-nal,](https://docs.microsoft.com/power-query/) és elérhetővé teszik a Power Query M funkciókat a szikravégrehajtás révén felhőszintű adatvonók számára. További információt az [adatfolyamok kibogozása című témakörben talál.](wrangling-data-flow-overview.md)

## <a name="external-transformations"></a>Külső átalakítások

Szükség esetén kézzel kódolhatja az átalakításokat, és saját kezűleg kezelheti a külső számítási környezetet.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive-tevékenység
A DATA Factory-folyamat HDInsight-struktúra működése hive-lekérdezéseket hajt végre a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön. A tevékenységgel kapcsolatos részletekért lásd a [Hive-tevékenységről](transform-data-using-hadoop-hive.md) szóló cikket. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig tevékenység
A Data Factory-folyamat HDInsight Pig-tevékenysége saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Pig-lekérdezéseket. A tevékenységgel kapcsolatos részletekért lásd a [Sertés tevékenységről](transform-data-using-hadoop-pig.md) szóló cikket. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce tevékenység
A DATA Factory-folyamat HDInsight MapReduce tevékenysége a MapReduce programokat saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. A tevékenységgel kapcsolatos részletekért tekintse meg a [MapReduce tevékenységről](transform-data-using-hadoop-map-reduce.md) szóló cikket.

### <a name="hdinsight-streaming-activity"></a>HDInsight-streamelési tevékenység
A Data Factory-folyamat HDInsight streamelési tevékenysége a Hadoop streaming programokat saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. A tevékenységgel kapcsolatos részletekért tekintse meg a [HDInsight streamelési tevékenységet.](transform-data-using-hadoop-streaming.md)

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A Data Factory-folyamat HDInsight Spark-tevékenysége spark-programokat hajt végre a saját HDInsight-fürtén. További információt a [Spark-programok meghívása az Azure Data Factoryból című témakörben talál.](transform-data-using-spark.md) 

### <a name="machine-learning-activities"></a>Gépi tanulási tevékenységek
Az Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre folyamatokat, amelyek egy közzétett Azure Machine Learning webszolgáltatást használnak a prediktív elemzéshez. A [batch-végrehajtási tevékenység](transform-data-using-machine-learning.md) egy Azure Data Factory-folyamat, meghívhatja a Machine Learning webszolgáltatás előrejelzéseket az adatok kötegben.

Idővel a machine learning-pontozási kísérletek prediktív modelljeit újra kell képezni az új bemeneti adatkészletek használatával. Miután végzett az átképzés, frissíteni szeretné a pontozási webszolgáltatás az átedzett Machine Learning modell. Az Erőforrás [frissítése tevékenység](update-machine-learning-models.md) segítségével frissítheti a webszolgáltatást az újonnan betanított modellel.  

A [Machine Learning-tevékenységek használata](transform-data-using-machine-learning.md) a Machine Learning-tevékenységek használatával kapcsolatos részletekért. 

### <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
Az SQL Server tárolt eljárás tevékenység egy Data Factory folyamat ban meghívhat egy tárolt eljárást a következő adattárak: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database a vállalatnál, vagy egy Azure virtuális gép. A részleteket a [Tárolt eljárás tevékenységről](transform-data-using-stored-procedure.md) szóló cikkben találja.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL tevékenység
A Data Lake Analytics U-SQL-tevékenység egy U-SQL-parancsfájlt futtat egy Azure Data Lake Analytics-fürtön. A részleteket a [Data Analytics U-SQL tevékenységről](transform-data-using-data-lake-analytics.md) szóló cikkében találja. 

### <a name="databricks-notebook-activity"></a>Databricks notebook tevékenység

Az Azure Databricks notebook tevékenység egy data factory folyamat fut egy Databricks notebook az Azure Databricks munkaterületen. Az Azure Databricks egy felügyelt platform az Apache Spark futtatásához. Lásd: [Adatok átalakítása Databricks-jegyzetfüzet futtatásával.](transform-data-databricks-notebook.md)

### <a name="databricks-jar-activity"></a>Databricks Jar tevékenység

Az Azure Databricks Jar tevékenység egy data factory folyamat egy Spark Jar az Azure Databricks-fürtben fut. Az Azure Databricks egy felügyelt platform az Apache Spark futtatásához. Lásd: [Adatok átalakítása jar tevékenység futtatásával az Azure Databricks.See Transform data by running a Jar activity in Azure Databricks.](transform-data-databricks-jar.md)

### <a name="databricks-python-activity"></a>Databricks Python-tevékenység

Az Azure Databricks Python-tevékenység egy Data Factory-folyamat egy Python-fájlt futtat az Azure Databricks-fürtben. Az Azure Databricks egy felügyelt platform az Apache Spark futtatásához. Lásd: [Adatok átalakítása python-tevékenység futtatásával az Azure Databricks-ben.](transform-data-databricks-python.md)

### <a name="custom-activity"></a>Egyéni tevékenység
Ha olyan módon kell átalakítania az adatokat, amelyet a Data Factory nem támogat, létrehozhat egy egyéni tevékenységet a saját adatfeldolgozási logikájával, és használhatja a folyamatban lévő tevékenységet. Az egyéni .NET-tevékenység konfigurálása azure Batch-szolgáltatás vagy egy Azure HDInsight-fürt használatával történő futtatásra konfigurálható. A részleteket az [Egyéni tevékenységek használata](transform-data-using-dotnet-custom-activity.md) című cikkben találja. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

### <a name="compute-environments"></a>Számítási környezetek
Hozzon létre egy csatolt szolgáltatást a számítási környezethez, majd használja a csatolt szolgáltatást átalakítási tevékenység definiálásakor. A Data Factory kétféle számítási környezetet támogat. 

- **Igény szerint:** Ebben az esetben a számítási környezetet a Data Factory teljes körűen kezeli. A Data Factory szolgáltatás automatikusan létrehozza, mielőtt egy feladatot elküldenének az adatok feldolgozásához, és a feladat befejezésekor eltávolítanák. Az igény szerinti számítási környezet részletes beállításait konfigurálhatja és szabályozhatja a feladatvégrehajtáshoz, a fürtkezeléshez és a rendszerindítási műveletekhez. 
- **Bring Your Own:** Ebben az esetben regisztrálhatja saját számítástechnikai környezet (például HDInsight-fürt) a Data Factory csatolt szolgáltatásként. A számítási környezetet Ön kezeli, és a Data Factory szolgáltatás ezt használja a tevékenységek végrehajtásához. 

A Data Factory által támogatott számítási szolgáltatásokról a [Compute Linked Services](compute-linked-services.md) cikkben olvashat. 

## <a name="next-steps"></a>További lépések
Tekintse meg a következő oktatóanyagot egy átalakítási tevékenység használatának példáját: [Oktatóanyag: Adatok átalakítása a Spark használatával](tutorial-transform-data-spark-powershell.md)
