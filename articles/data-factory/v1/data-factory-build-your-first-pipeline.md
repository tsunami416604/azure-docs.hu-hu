---
title: 'Data Factory oktatóanyag: Első adatfolyamat '
description: Ez az Azure Data Factory-oktatóanyag bemutatja, hogyan hozhat létre és ütemezhet egy adat-gyárat, amely egy Hadoop-fürt Hive-parancsfájl használatával dolgozza fel az adatokat.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 80644ed2d655544fa176a7be92aec3c01aa3bf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966071"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Oktatóanyag: Az első folyamat létrehozása az adatok Hadoop-fürt használatával történő átalakításához
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Vizuális stúdió](data-factory-build-your-first-pipeline-using-vs.md)
> * [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Erőforrás-kezelő sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg [az adat-előállító Azure Data Factoryvel való létrehozását ismertető rövid útmutatót](../quickstart-create-data-factory-dot-net.md).

Ebben az oktatóanyagban az első Azure-adatfeldolgozó üzemet adatfolyamattal hozhatja létre. A folyamat átalakítja a bemeneti adatokat egy Azure HDInsight (Hadoop) fürtön lévő Hive-parancsfájl futtatásával kimeneti adatok előállításához.  

Ez a cikk áttekintést és előfeltételeit ismerteti az oktatóanyag. Az előfeltételek befejezése után elvégezheti az oktatóanyagot az alábbi eszközök/SDK-k használatával: Visual Studio, PowerShell, Resource Manager sablon, REST API. Válassza ki a cikk elején (vagy) a cikk végén található legördülő lista egyik beállítását az oktatóanyag hoz az alábbi lehetőségek egyikének használatával.    

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Az adat-előállító kondár tartalmazhat egy vagy több adatfolyamatot, amelyek adatokat helyeznek át és alakítanak át.

    Ebben az oktatóanyagban egy folyamatot hoz létre az adat-előállítóban.
2. Folyamat **létrehozása**. Egy folyamat egy vagy több tevékenységet (példák: Másolási tevékenység, HDInsight Hive-tevékenység). Ez a minta a HDInsight Hive-tevékenységet használja, amely egy HIV-parancsfájlt futtat egy HDInsight Hadoop-fürtön. A parancsfájl először létrehoz egy táblát, amely hivatkozik az Azure blob storage-ban tárolt nyers webnapló-adatokra, majd év és hónap szerint particionálja a nyers adatokat.

    Ebben az oktatóanyagban a folyamat a Hive-tevékenység használatával alakítja át az adatokat egy Hive-lekérdezés futtatásával egy Azure HDInsight Hadoop-fürtön.
3. **Csatolt szolgáltatások**létrehozása . Hozzon létre egy összekapcsolt szolgáltatást, amely egy adattárvagy egy számítási szolgáltatás és az adat-előállító összekapcsolására. Egy adattár, például az Azure Storage tárolja a folyamat on-és folyamattevékenységeinek bemeneti/kimeneti adatait. Egy számítási szolgáltatás, például a HDInsight Hadoop-fürt feldolgozza/átalakítja az adatokat.

    Ebben az oktatóanyagban két összekapcsolt szolgáltatást hoz létre: **az Azure Storage-ot** és az **Azure HDInsight-ot.** Az Azure Storage-kapcsolt szolgáltatás egy Azure Storage-fiókot kapcsol össze, amely a bemeneti/kimeneti adatokat az adat-előállítóba tartalmazza. Az Azure HDInsight-kapcsolt szolgáltatás egy Azure HDInsight-fürthöz kapcsolódik, amely az adatok adat-előállítóvá alakítására szolgál.
3. Bemeneti és kimeneti **adatkészletek**létrehozása. A bemeneti adatkészletek a folyamattevékenységek bemenetét jelölik, a kimeneti adatkészletek pedig a tevékenységek kimeneteit.

    Ebben az oktatóanyagban a bemeneti és kimeneti adatkészletek az Azure Blob Storage bemeneti és kimeneti adatok helyét határozza meg. Az Azure Storage-kapcsolt szolgáltatás határozza meg, hogy milyen Azure Storage-fiók használatban van. A bemeneti adatkészlet határozza meg, hogy a bemeneti fájlok hol találhatók, a kimeneti adatkészlet pedig meghatározza, hogy a kimeneti fájlok hol találhatók.


Az Azure Data Factory részletes áttekintését az Azure Data Factory – [Bevezetés az Azure Data Factory](data-factory-introduction.md) cikkében című témakörben találja.

Itt van a **diagram nézeta** a minta adatgyár ban épített ebben az oktatóanyagban. **A MyFirstPipeline** egy Hive típusú tevékenységet használ, amely **az AzureBlobInput-adatkészletet** bemenetként használja fel, és **az AzureBlobOutput** adatkészletet kimenetként állítja elő.

![Diagramnézet a Data Factory oktatóanyagában](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Ebben az oktatóanyagban az **adfgetstarted** Azure blob tároló **bemeneti adatok** mappája tartalmaz egy input.log nevű fájlt. Ez a naplófájl három hónapból tartalmaz bejegyzéseket: 2016 januárja, februárja és márciusa. Itt vannak a mintasorok minden hónapra a bemeneti fájlban.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Amikor a fájlt a folyamat hdinsight hive-tevékenységgel dolgozza fel, a tevékenység egy Hive-parancsfájlt futtat a HDInsight-fürtön, amely év és hónap szerint particionálja a bemeneti adatokat. A parancsfájl három kimeneti mappát hoz létre, amelyek minden hónapbejegyzéseit tartalmazó fájlt tartalmaznak.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

A fenti mintasorokközül az első (2016-01-01) a month=1 mappában lévő 000000_0 fájlba kerül. Hasonlóképpen, a második a month=2 mappában, a harmadik pedig a month=3 mappában található fájlba íródik.  

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag megkezdése előtt a következő előfeltételekkel kell rendelkeznie:

1. **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, néhány perc alatt létrehozhat egy ingyenes próbafiókot. Tekintse meg az [ingyenes próbaverziós](https://azure.microsoft.com/pricing/free-trial/) cikket arról, hogyan szerezhet ingyenes próbafiókot.
2. **Azure Storage** – Azure storage-fiók használatával tárolja az adatokat ebben az oktatóanyagban. Ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket. A tárfiók létrehozása után jegyezze fel a **fiók nevét** és a **hozzáférési kulcsot.** A tárfiók hozzáférési kulcsainak beolvasásáról a [Tárfiók hozzáférési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md)című témakörben talál további információt.
3. Töltse le és tekintse át a Hive [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql)lekérdezési fájlt **(HQL)** található: . Ez a lekérdezés átalakítja a bemeneti adatokat kimeneti adatok létrehozásához.
4. Töltse le és tekintse át a mintabemeneti fájlt (**input.log**) a következő helyen:[https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Hozzon létre egy **adfget** nevű blobtárolót az Azure Blob Storage-ban.
6. Töltsön fel **partitionweblogs.hql** fájlt az **adfgetstarted** tároló **parancsfájlmappájába.** Használja az olyan eszközöket, mint a [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
7. Töltsön fel **input.log** fájlt az **adfgetstarted** tároló **bemeneti adatai** mappájába.

Az előfeltételek befejezése után válasszon az alábbi eszközök/SDK-k közül az oktatóanyag elvégzéséhez:

- [Vizuális stúdió](data-factory-build-your-first-pipeline-using-vs.md)
- [Powershell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Erőforrás-kezelő sablon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

A Visual Studio grafikus felhasználói felületet biztosít az adatgyárak létrehozásához. Mivel a PowerShell, a Resource Manager Template és a REST API-beállítások parancsfájl-kezelési/programozási módot kínálnak az adatgyárak létrehozásához.

> [!NOTE]
> Az oktatóanyagban található adatfolyamat átalakítja a bemeneti adatokat, hogy ezzel kimeneti adatokat hozzon létre. A forrásadattár adatait nem másolja egy céladattárba. Az adatok Azure Data Factory használatával történő másolásának útmutatásáért olvassa el [az adatok Blob Storage-ból SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket.
