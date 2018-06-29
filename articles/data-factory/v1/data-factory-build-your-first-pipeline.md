---
title: 'Data Factory-oktatóanyag: adatok első folyamatát |} Microsoft Docs'
description: Az Azure Data Factory oktatóanyag bemutatja, hogyan hozhat létre és dolgozza fel a Hive parancsfájl használata a Hadoop-fürthöz adatok adat-előállító ütemezni.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 951756d57441d175ccf8bab44bf00c3cb542f1b9
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050046"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Oktatóanyag: Felépítheti első folyamatát átalakítására adatok Hadoop-fürt használatával
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ez a cikk a Data Factory 1 verziójára vonatkozik. A Data Factory szolgáltatásnak aktuális verziójának használatakor lásd [gyors üzembe helyezés: hozzon létre egy Azure Data Factory használatával adat-előállító](../quickstart-create-data-factory-dot-net.md).

Ebben az oktatóanyagban a első Azure data factory és olyan adatokat hoz létre. A feldolgozási sor bemeneti adatok átalakítja az Azure HDInsight (Hadoop) eredményezett kimeneti adatokat fürtökön Hive parancsfájl futtatásával.  

Ez a cikk áttekintése és az oktatóanyag előfeltételei tartalmazza. Miután elvégezte az előfeltételeket, az oktatóanyag a következő eszközök/SDK-k egyikével teheti: Azure-portálon, a Visual Studio, a PowerShell, a Resource Manager-sablon, a REST API-t. Válassza ki a legördülő lista elején (vagy) az oktatóanyag segítségével ezen beállítások valamelyikét kell tennie a cikk végén hivatkozások a lehetőségek közül.    

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Egy adat-előállító tartalmazhat egy vagy több, helyezze át, és az adatok átalakítása adatok folyamatok. 

    Ebben az oktatóanyagban létrehoz egy folyamatot, az adat-előállítóban. 
2. Hozzon létre egy **csővezeték**. Egy folyamat rendelkezhet egy vagy több tevékenységet (Példa: másolási tevékenység során, a HDInsight Hive tevékenység). A példa a Hive parancsfájlok futtatására szolgál egy HDInsight Hadoop-fürt HDInsight Hive tevékenységet használja. A parancsfájl először táblázatot hoz létre, amely a nyers webes naplóadatokat, az Azure blob storage-ban tárolt hivatkozik, és majd particionálja a nyers adatok hónap és év szerint.

    Ebben az oktatóanyagban a folyamatot használja a Hive tevékenység adatok átalakítására futtatja a Hive-lekérdezések egy Azure HDInsight Hadoop-fürt. 
3. Hozzon létre **összekapcsolt szolgáltatások**. Adattároló csatolásához összekapcsolt szolgáltatás vagy egy számítási szolgáltatást, hogy a data factory létrehozása. Például az Azure Storage adattárat a feldolgozási tevékenységek bemeneti/kimeneti adatokat tartalmazza. A számítási szolgáltatás, például a HDInsight Hadoop-fürt folyamatok/átalakítások adatokat.

    Ebben az oktatóanyagban létrehoz két összekapcsolt szolgáltatások: **Azure Storage** és **Azure HDInsight**. Az Azure Storage társított szolgáltatás hivatkozások egy Azure Storage-fiókot, amely tárolja a bemeneti/kimeneti adatok data factoryval való. Az Azure HDInsight kapcsolódó szolgáltatás hivatkozások adatok data factoryval való átalakításához használt Azure HDInsight-fürtöt. 
3. Hozzon létre a bemeneti és kimeneti **adatkészletek**. A bemeneti adatkészletek a folyamattevékenységek bemenetét jelölik, a kimeneti adatkészletek pedig a tevékenységek kimeneteit.

    Ebben az oktatóanyagban a bemeneti és kimeneti adatkészletek adja meg a bemeneti és kimeneti adatok az Azure Blob Storage helyét. Az Azure tárolás társított szolgáltatásának határozza meg, mi az Azure Storage-fiókot használni. Egy bemeneti adatkészlet határozza meg, ha a bemeneti fájlok találhatók, és egy kimeneti adatkészlet határozza meg, ahol a kimeneti fájlok kerülnek. 


Lásd: [Bevezetés az Azure Data Factory](data-factory-introduction.md) cikk részletes áttekintés az Azure Data Factory.
  
Itt a **diagram nézet** az adat-előállító minta ebben az oktatóanyagban készít. **MyFirstPipeline** egy tevékenysége, amely akkor Hive típusú **AzureBlobInput** bemeneti és előállított dataset **AzureBlobOutput** egy kimeneti adatkészletet. 

![A Data Factory oktatóanyag diagram nézet](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Ebben az oktatóanyagban **inputdata** mappában található a **adfgetstarted** Azure blob-tároló input.log nevű egyetlen fájlt tartalmaz. Ez a naplófájl a három hónapos bejegyzésekkel rendelkezik: január, február és a 2016. március. Az alábbiakban a minta sorok minden hónapban a bemeneti fájl. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

A fájl feldolgozása a folyamat HDInsight Hive tevékenységet, a tevékenység futtatása egy Hive-parancsfájl a HDInsight-fürt partíciókat, hogy évhez és hónaphoz által bemeneti adatai. A parancsfájl minden hónap bejegyzéseket tartalmazó fájl tartalmazó három kimeneti mappákat hoz létre.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

A fent látható minta sorok, az első másikat (2016-01-01) íródik a 000000_0 fájl az adott hónapban = 1 mappa. Hasonlóképpen, a második érték íródik a fájl az adott hónapban = 2 mappa és a harmadik egy íródik a fájl az adott hónapban = 3 mappa.  

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag megkezdése előtt rendelkeznie kell a következő előfeltételek teljesülését:

1. **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, is létrehozhat egy ingyenes próbafiók néhány percig. Tekintse meg a [ingyenes próba](https://azure.microsoft.com/pricing/free-trial/) foglalkozó hogyan beszerezhet egy ingyenes próbafiókot.
2. **Az Azure Storage** – ebben az oktatóanyagban az adatok tárolása egy Azure storage-fiókot használni. Ha egy Azure storage-fiók nem rendelkezik, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account) cikk. Miután létrehozta a tárfiókot, jegyezze fel a **fióknév** és **hozzáférési kulcs**. Lásd: [megtekintése, másolása és újragenerálása tárolási hívóbetűk](../../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Töltse le, és tekintse át a Hive lekérdezés fájlt (**HQL**) helyen: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Ez a lekérdezés átalakítja a bemeneti adatok eredményezett kimeneti adatokat. 
4. Töltse le, és tekintse át a minta bemeneti fájl (**input.log**) helyen: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Hozzon létre egy blob-tároló nevű **adfgetstarted** az Azure Blob Storage tárolóban. 
6. Töltse fel **partitionweblogs.hql** fájlt a **parancsfájl** mappájában a **adfgetstarted** tároló. Használjon például az eszközök [Microsoft Azure Tártallózó](http://storageexplorer.com/). 
7. Töltse fel **input.log** fájlt a **inputdata** mappájában a **adfgetstarted** tároló. 

Miután elvégezte az előfeltételeket, válasszon az alábbi eszközök/SDK-k az oktatóanyag elvégzéséhez: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Azure-portál és a Visual Studio adja meg az adat-előállítók kialakításának grafikus felhasználói Felülettel módot. Mivel a PowerShell, a Resource Manager-sablon és a REST API-beállítások az adat-előállítók kialakításának scripting/programozási megoldást kínál.

> [!NOTE]
> Az oktatóanyagban található adatfolyamat átalakítja a bemeneti adatokat, hogy ezzel kimeneti adatokat hozzon létre. A forrásadattár adatait nem másolja egy céladattárba. Az adatok Azure Data Factory használatával történő másolásának útmutatásáért olvassa el [az adatok Blob Storage-ból SQL Database-be történő másolását ismertető oktatóanyagot](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket. 





  
