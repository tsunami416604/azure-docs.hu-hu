---
title: 'Data Factory-oktatóanyag: Első adatfolyamat |} A Microsoft Docs'
description: Az Azure Data Factory-oktatóanyag bemutatja, hogyan hozhat létre és ütemezhet egy adat-előállítót, amely feldolgozza a Hive-szkript használata a Hadoop-fürtön az adatok.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d9d9e68b7e74ba7725e97162d01e1a35314fdd0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544954"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Oktatóanyag: Az adatok Hadoop-fürt segítségével történő átalakítására szolgáló első folyamat létrehozása
> [!div class="op_single_selector"]
> * [Áttekintés és előfeltételek](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a rövid útmutató: Hozzon létre egy data factoryt az Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Ebben az oktatóanyagban az első Azure data factoryját adatfolyamat hozhat létre. A folyamat bemeneti adatokat átalakítja a kimeneti adatok előállításához egy Azure HDInsight (Hadoop) fürtön Hive-szkript futtatásával.  

Ez a cikk áttekintése és az oktatóanyag előfeltételei. Miután végzett az előfeltételekkel, az oktatóanyag a következő eszközök/SDK-k egyikével teheti meg: Az Azure portal, a Visual Studio, a PowerShell, a Resource Manager-sablon, a REST API-t. Válassza ki a beállítások a legördülő lista elején (vagy) hajtsa végre az oktatóanyag ezen beállítások valamelyikét használja, ez a cikk végén található hivatkozásokat.    

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

1. Hozzon létre egy **adat-előállító**. Adat-előállító egy vagy több és adatok átalakítására szolgáló adatfolyamatokat is tartalmazhat. 

    Ebben az oktatóanyagban létrehozhat egy folyamatot az adat-előállítóban. 
2. Hozzon létre egy **folyamat**. Egy folyamatot egy vagy több tevékenység is rendelkezhet (példák: Másolási tevékenység, HDInsight Hive-tevékenység). Ebben a példában a HDInsight Hive tevékenység, amely egy Hive-szkriptet futtat egy HDInsight Hadoop-fürtöt. A szkript először létrehoz egy táblát, amely a nyers webes naplóadatokat, az Azure blob storage-ban tárolt hivatkozik, és majd particionálja a nyers adatok év és hónap szerint.

    Ebben az oktatóanyagban a folyamat használja a Hive-tevékenység az adatok átalakítása a Hive-lekérdezéseket futtat egy Azure HDInsight Hadoop-fürtöt. 
3. Hozzon létre **társított szolgáltatásokat**. Létrehozhat egy társított szolgáltatást, egy adattárat vagy számítási szolgáltatások adat-előállítóhoz. Egy adattár, például az Azure Storage a folyamat tevékenységek bemeneti és kimeneti adatokat tárolja. HDInsight Hadoop-fürt hasonló számítási szolgáltatásokhoz a folyamatok/átalakítások adatokat.

    Ebben az oktatóanyagban két társított szolgáltatást hoz létre: **Az Azure Storage** és **Azure HDInsight**. Az Azure Storage társított szolgáltatás egy Azure Storage-fiókot, amely a bemeneti és kimeneti adatokat tárol a data factoryhoz. Az Azure HDInsight társított szolgáltatás egy Azure HDInsight-fürtöt, amellyel alakíthat át adatokat a data factoryhoz. 
3. Hozzon létre bemeneti és kimeneti **adatkészletek**. A bemeneti adatkészletek a folyamattevékenységek bemenetét jelölik, a kimeneti adatkészletek pedig a tevékenységek kimeneteit.

    Ebben az oktatóanyagban a bemeneti és kimeneti adatkészleteket bemeneti és kimeneti adatokat az Azure Blob Storage helyét adja meg. A Azure Storage társított szolgáltatás határozza meg, mi az Azure Storage-fiókot használja. Bemeneti adatkészlet meghatározza, hol találhatók a bemeneti fájlok és a egy kimeneti adatkészletet adja meg, ahol a kimeneti fájlok kerülnek. 


Lásd: [az Azure Data Factory bemutatását](data-factory-introduction.md) cikk részletes áttekintést az Azure Data Factory.
  
Íme a **diagram nézet** ebben az oktatóanyagban létrehozhat minta adat-előállító. **MyFirstPipeline** típusú Hive, amely egyetlen tevékenységet tartalmaz **AzureBlobInput** állít elő és a egy bemeneti adatkészlet **AzureBlobOutput** adatkészletet pedig kimenetként. 

![A Data Factory az oktatóanyagban a diagram nézet](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Ebben az oktatóanyagban **inputdata** mappában található a **adfgetstarted** az Azure blob-tároló nevű input.log fájl tartalmazza. Ez a naplófájl három hónap bejegyzéseinek rendelkezik: Január február és 2016. március. Az alábbiakban a minta sorok minden hónapban a bemeneti fájl. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

A fájl feldolgozása a HDInsight Hive-tevékenységgel rendelkező folyamat, amikor a tevékenység fut egy Hive-szkriptet a HDInsight-fürtön, hogy a partíciók bemeneti adatok év és hónap szerint. A szkript létrehoz három kimeneti mappát, amely minden hónapban bejegyzéseinek egy olyan fájlt tartalmaznak.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

A fent látható mintában sorok, elsőt (2016-01-01) van a 000000_0 fájlba írni a hónap = 1 mappa. Hasonlóképpen, a másodikat íródik a fájl a hónap = 2, mappa és a harmadik egy íródik a fájlt a hónap = 3 mappa.  

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez az alábbi előfeltételekkel kell rendelkeznie:

1. **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, létrehozhat mindössze néhány perc alatt egy ingyenes próbafiókot. Tekintse meg a [ingyenes próba](https://azure.microsoft.com/pricing/free-trial/) szóló, hogyan szerezheti be egy ingyenes próbafiókot.
2. **Az Azure Storage** – ebben az oktatóanyagban az adatok tárolása Azure storage-fiókot használni. Ha nem rendelkezik Azure storage-fiókkal, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md) cikk. Miután létrehozta a tárfiókot, jegyezze fel a **fióknév** és **hívóbetű**. Lásd: [tárelérési kulcsok megtekintése, másolása és újragenerálása](../../storage/common/storage-account-manage.md#access-keys).
3. Töltse le és tekintse meg a Hive-lekérdezés fájlt (**HQL**) helyen található: [ https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql ](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Ez a lekérdezés átalakítja a bemeneti adatokat kimeneti adatok előállításához. 
4. Töltse le és tekintse meg a minta bemeneti fájlt (**input.log**) helyen található: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Hozzon létre egy blobtárolót **adfgetstarted** az Azure Blob Storage. 
6. Töltse fel **partitionweblogs.hql** fájlt a **parancsfájl** mappájában a **adfgetstarted** tároló. Például használja az eszközök [Microsoft Azure Storage Explorer](https://storageexplorer.com/). 
7. Töltse fel **input.log** fájlt a **inputdata** mappájában a **adfgetstarted** tároló. 

Miután végzett az előfeltételekkel, válassza ki az alábbi eszközök/SDK-k az oktatóanyag elvégzéséhez: 

- [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager-sablon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Az Azure portal és a Visual Studio adja meg a grafikus lehetőség az adat-előállítók. Mivel a PowerShell, a Resource Manager-sablon és a REST API-t az adat-előállítók létrehozásának parancsfájlok és programozás-megoldást kínál.

> [!NOTE]
> Az oktatóanyagban található adatfolyamat átalakítja a bemeneti adatokat, hogy ezzel kimeneti adatokat hozzon létre. A forrásadattár adatait nem másolja egy céladattárba. Az Azure Data Factory használata adatok másolása oktatóanyagért lásd: [oktatóanyag: Adatok másolása Blob Storage-ból az SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Összefűzhet két tevékenységet (vagyis egymás után futtathatja őket), ha az egyik tevékenység kimeneti adatkészletét a másik tevékenység bemeneti adatkészleteként állítja be. Lásd [a Data Factorybeli ütemezést és végrehajtást](data-factory-scheduling-and-execution.md) ismertető cikket. 





  
