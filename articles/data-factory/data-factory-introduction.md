---
title: "A Data Factory nevű adatintegrációs szolgáltatás ismertetése | Microsoft Docs"
description: "A témakör ismerteti, hogy mi is az Azure Data Factory: egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása."
keywords: "adatintegrálás, felhőalapú adatintegráció, mi az az azure data factory"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/22/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 0175f2877a59db214a38bfe27eca74748518a498


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Az Azure Data Factory, egy adatintegrációs felhőalapú szolgáltatás ismertetése
## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok **továbbítása** és **átalakítása**. A Data Factoryval olyan adatintegrációs megoldásokat hozhat létre, amelyek különféle adattárakból származó adatokkal is képesek dolgozni, és amelyekkel az adatok átalakíthatók/feldolgozhatók, az eredmények pedig az adattárakban közzétehetők.

A Data Factoryval az adatok továbbítására és átalakítására szolgáló adatfolyamatokat hozhat létre, majd ütemezés szerint futtathatja a folyamatot (óránként, napi, heti stb. gyakorisággal). Ezenkívül látványos vizualizációkkal jelenítheti meg az adatfolyamatok közötti leszármaztatási és függőségi kapcsolatokat, valamint egyetlen, egységesített nézetben figyelheti az összes folyamatot, így egyszerűen kiszűrheti a problémákat és beállíthatja a figyelési riasztásokat.

![Ábra: A Data Factory áttekintése, adatintegrációs szolgáltatás](./media/data-factory-introduction/what-is-azure-data-factory.png)
**1. ábra.** Különböző adatforrásokból származó adatokkal is dolgozhat, előkészítheti, átalakíthatja, elemezheti azokat, és azonnal felhasználható adatokat tehet közzé.

## <a name="pipelines-and-activities"></a>Folyamatok és tevékenységek
A Data Factory-megoldásokkal egy vagy több **adatfolyamatot** is létrehozhat. A folyamatok a tevékenységek logikus csoportosításai. A használatukkal a tevékenységek egységgé csoportosíthatók, amelyben együttesen hajtanak végre egy feladatot.

A **tevékenységek** határozzák meg az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például az egyik adattárból a másikba másolhatja az adatokat. Hasonlóképpen, egy Hive-tevékenység használatával Hive-lekérdezést futtathat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket.

## <a name="data-movement-activities"></a>Adattovábbítási tevékenységek
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

További részletekért tekintse meg az [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikket.

## <a name="data-transformation-activities"></a>Adatátalakítási tevékenységek
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

További részletekért tekintse meg az [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) című cikket.

Ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, vagy saját logika szerint szeretne adatátalakítást végezni, hozzon létre egy **egyéni .NET-tevékenységet**. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

## <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások meghatározzák azokat az információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel (pl.: Azure Storage, helyszíni SQL Server, Azure HDInsight). A társított szolgáltatásokat két célból használjuk a Data Factoryban:

* Egy **adattár**, többek között például egy helyszíni SQL Server, Oracle-adatbázis, fájlmegosztás vagy egy Azure Blob Storage-fiók jelölésére. A támogatott adattárak listája az [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című részben található.
* Olyan **számítási erőforrás** jelölésére, amelyen végrehajtható a tevékenység. A HDInsightHive-tevékenység végrehajtása például egy HDInsight Hadoop-fürtön történik. A támogatott számítási környezetek listája az [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) szakaszban található.

## <a name="datasets"></a>Adathalmazok
A társított szolgáltatások adattárakat társítanak az Azure Data Factoryhoz. Az adatkészletek az adattárakon belüli adatstruktúrákat jelölik. Az Azure Storage társított szolgáltatása például kapcsolódási információkat szolgáltat a Data Factory számára az Azure Storage-fiókhoz való csatlakozáshoz. Az Azure Blob-adatkészlet meghatározza a blobtárolót és azt az Azure Blob Storage-mappát, amelyből a folyamat beolvassa az adatokat. Hasonlóképpen, az Azure SQL társított szolgáltatása kapcsolódási adatokat szolgáltat az Azure SQL Database számára, az Azure SQL-adatkészlet pedig az adatokat tartalmazó táblát határozza meg.   

## <a name="relationship-between-data-factory-entities"></a>Data Factory-entitások közötti kapcsolatok
A Data Factory néhány főbb entitásának együttes használatával meghatározhatók a bemeneti és kimeneti adatok, a feldolgozási események, valamint a kívánt adatfeldolgozási folyamat végrehajtásához szükséges ütemezés és erőforrások.

![Ábra: A Data Factory áttekintése, felhőalapú adatintegrációs szolgáltatás – főbb fogalmak](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**2. ábra.** Az adatkészlet, a tevékenység, a folyamat és a társított szolgáltatás közötti kapcsolatok

A társított szolgáltatás, az adatkészlet, a tevékenység, és a folyamat négy egyszerű fogalmának ismeretében készen áll a szolgáltatás használatának megkezdésére. Most [létrehozhatja első folyamatát](data-factory-build-your-first-pipeline.md).

## <a name="supported-regions"></a>Támogatott régiók
Jelenleg az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban hozhat létre data factoryt. A data factory azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében.

Maga az Azure Data Factory nem tárol adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) közötti adatmozgás és az adatok [számítási szolgáltatásokkal](data-factory-compute-linked-services.md) történő feldolgozása más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok figyelését és kezelését](data-factory-monitor-manage-pipelines.md) mind szoftveres, mind pedig felhasználói felületi mechanizmusokkal.

Bár az Azure Data Factory csak az **USA nyugati régiójában**, az **USA keleti régiójában** és az **észak-európai** régióban érhető el, az adatok Data Factoryval történő áthelyezését biztosító szolgáltatás [globálisan](data-factory-data-movement-activities.md#global) számos régióban elérhető. Ha az adattár tűzfal mögött található, akkor a helyszíni környezetben telepített [adatkezelési átjáró](data-factory-move-data-between-onprem-and-cloud.md) végzi az adatok áthelyezését.

Tegyük fel például, hogy számítási környezetei, mint például az Azure HDInsight-fürt és az Azure Machine Learning a nyugat-európai régión kívül futnak. Létrehozhat egy Azure Data Factory-példányt Észak-Európában, és felhasználhatja a Nyugat-Európában lévő számítási környezetein futtatott feladatok ütemezéséhez. A Data Factory néhány ezredmásodperc alatt aktiválja a feladatot a számítási környezetben, a feladatnak a számítási környezetben való futtatásához szükséges idő viszont nem változik.

A későbbiekben szeretnénk minden olyan régióra kiterjeszteni az Azure Data Factoryt, ahol támogatott az Azure használata.

## <a name="next-steps"></a>Következő lépések
A következő oktatóanyagok részletes utasításait követve megtudhatja, hogyan építhet ki adatfolyamatokkal rendelkező data factorykat.

| Oktatóanyag | Leírás |
| --- | --- |
| [Az adatokat Hadoop-fürttel feldolgozó adatfolyamat kiépítése](data-factory-build-your-first-pipeline.md) |Az oktatóanyag során kiépíti az első Azure data factoryját egy olyan adatfolyamattal, amely egy Azure HDInsight (Hadoop) fürtön futtatott Hive-parancsprogrammal **dolgozza fel az adatokat**. |
| [Adatfolyamat kiépítése két felhőalapú adattár közötti adatáthelyezéshez](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt hoz létre, amely Blob Storage-ból SQL-adatbázisba **helyez át adatokat**. |
| [Adatfolyamat kiépítése egy helyszíni és egy felhőalapú adattár közötti adatáthelyezéshez adatkezelési átjáró segítségével](data-factory-move-data-between-onprem-and-cloud.md) |Ebben az oktatóanyagban olyan folyamattal rendelkező data factoryt épít ki, amely egy **helyszíni** SQL Server-adatbázisból Azure-blobba **helyez át adatokat**. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen. |



<!--HONumber=Nov16_HO2-->


