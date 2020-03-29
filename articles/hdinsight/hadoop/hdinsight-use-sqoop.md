---
title: Apache Sqoop-feladatok futtatása az Azure HDInsight (Apache Hadoop) segítségével
description: Megtudhatja, hogy miként futtathatja a Sqoop-importálást és -exportálást egy Hadoop-fürt és egy Azure SQL-adatbázis között a munkaállomásról származó Azure PowerShell használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951853"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Apache Sqoop használata a Hadooppal a HDInsightban

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan importálhat és exportálhat adatokat egy HDInsight-fürt és egy Azure SQL-adatbázis között az Apache Sqoop használatával a HDInsight-fürtök és egy Azure SQL-adatbázis között.

Bár az Apache Hadoop természetes választás strukturálatlan és félig strukturált adatok, például naplók és fájlok feldolgozásához, szükség lehet a relációs adatbázisokban tárolt strukturált adatok feldolgozására is.

[Az Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) egy olyan eszköz, amely et a Hadoop-fürtök és a relációs adatbázisok közötti adatátvitelre terveztek. Segítségével adatokat importálhat egy relációs adatbázis-kezelő rendszerből (RDBMS), például SQL Serverből, MySQL-ből vagy Oracle-ből a Hadoop elosztott fájlrendszerbe (HDFS), átalakíthatja az adatokat a Hadoopban a MapReduce vagy az Apache Hive segítségével, majd exportálhatja az adatokat RDBMS-be. . Ebben a cikkben egy SQL Server-adatbázist használ a relációs adatbázishoz.

> [!IMPORTANT]  
> Ez a cikk egy tesztkörnyezetet állít be az adatátvitel végrehajtásához. Ezután válasszon egy adatátviteli módszert ehhez a környezethez a [Sqoop-feladatok futtatása](#run-sqoop-jobs)című szakaszegyik metódusa közül, az alábbiakban.

A HDInsight-fürtök által támogatott Sqoop-verziók ról a [HDInsight által biztosított fürtverziók újdonságai című témakörben talál.](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>A forgatókönyv megismerése

A HDInsight-fürt néhány mintaadatot is hoz. A következő két mintát használja:

* Apache Log4j naplófájl, amely `/example/data/sample.log`a helyen található. A következő naplók at bontják ki a fájlból:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* A (Hive) nevű `hivesampletable`tábla, amely `/hive/warehouse/hivesampletable`a helyen található adatfájlra hivatkozik. A táblázat tartalmaz néhány mobileszköz-adatot.
  
  | Mező | Adattípus |
  | --- | --- |
  | ügyfélazonosító |sztring |
  | lekérdezési idő |sztring |
  | piac |sztring |
  | eszközplatform |sztring |
  | eszközmake |sztring |
  | eszközmodell |sztring |
  | state |sztring |
  | ország |sztring |
  | querydwelltime |double |
  | Munkamenet |bigint |
  | sessionpagevieworder |bigint |

Ebben a cikkben ezt a két adatkészletet használja a Sqoop importálásának és exportálásának teszteléséhez.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Tesztkörnyezet beállítása

A fürt, az SQL-adatbázis és más objektumok az Azure-portálon keresztül jönnek létre egy Azure Resource Manager-sablon használatával. A sablon megtalálható az [Azure rövid útmutató sablonjaiban.](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/) Az Erőforrás-kezelő sablon bacpac csomagot hív meg a táblasémák SQL-adatbázisba való üzembe helyezéséhez.  A bacpac csomag egy nyilvános blobtárolóban található, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Ha a bacpac fájlokhoz privát tárolót szeretne használni, használja a sablon következő értékeit:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importálás sablon nal vagy az Azure portalon csak támogatja a BACPAC-fájl importálása az Azure blob storage.Import using a template or the Azure portal only supports importing a BACPAC file from Azure blob storage.

1. Válassza ki az alábbi képet az Erőforrás-kezelő sablon megnyitásához az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Adja meg a következő tulajdonságokat:

    |Mező |Érték |
    |---|---|
    |Előfizetés |Válassza ki az Azure-előfizetést a legördülő listából.|
    |Erőforráscsoport |Válassza ki az erőforráscsoportot a legördülő listából, vagy hozzon létre egy újat|
    |Hely |Válasszon ki egy területet a legördülő listából.|
    |Fürt neve |Adja meg a Hadoop-fürt nevét. Csak kisbetűt használjon.|
    |Fürt bejelentkezési felhasználóneve |Tartsa meg az `admin`előre kitöltött értéket .|
    |Fürt bejelentkezési jelszava |Adja meg a jelszót.|
    |Ssh felhasználónév |Tartsa meg az `sshuser`előre kitöltött értéket .|
    |Ssh jelszó |Adja meg a jelszót.|
    |Sql Admin bejelentkezés |Tartsa meg az `sqluser`előre kitöltött értéket .|
    |Sql rendszergazdai jelszó |Adja meg a jelszót.|
    |_artifacts helye | Használja az alapértelmezett értéket, hacsak nem saját bacpac fájlt szeretne használni egy másik helyen.|
    |_artifacts hely sas token |Hagyja üresen.|
    |Bacpac fájlnév |Használja az alapértelmezett értéket, hacsak nem saját bacpac fájlt szeretne használni.|
    |Hely |Használja az alapértelmezett értéket.|

    Az Azure SQL Server `<ClusterName>dbserver`neve lesz . Az adatbázis neve `<ClusterName>db`a . A tárfiók alapértelmezett `e6qhezrh2pdqu`neve a .

3. Válassza az **Elfogadom a fent meghatározott feltételeket.**

4. Válassza a **Beszerzés** lehetőséget. Megjelenik egy új csempe című küldése központi telepítés sablon központi telepítés. A fürt és az SQL-adatbázis létrehozása nagyjából 20 percet vesz igénybe.

## <a name="run-sqoop-jobs"></a>Sqoop-feladatok futtatása

A HDInsight számos módszerrel futtathatja a Sqoop-feladatokat. Az alábbi táblázat segítségével eldöntheti, hogy melyik módszer felel meg Önnek, majd kövesse a forgatókönyvhivatkozást.

| **Használja ezt,** ha akarja... | ... egy **interaktív** rendszerhéj | ... **kötegelt** feldolgozás | ... ebből az **ügyféloperációs rendszerből** |
|:--- |:---:|:---:|:--- |:--- |
| [Ssh](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (egyelőre) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – A Linux-alapú HDInsight, a Sqoop-összekötő adatok Microsoft SQL Server vagy az Azure SQL Database jelenleg nem támogatja a tömeges beszúrások.
* Kötegelés - A Linux-alapú HDInsight, Amikor a `-batch` kapcsolót, amikor a beszúrások végrehajtásakor, Sqoop végez több beszúrások helyett kötegelés a beszúrási műveleteket.

## <a name="next-steps"></a>További lépések

Most már megtanulta, hogyan kell használni Sqoop. További tudnivalókért lásd:

* [Az Apache Hive használata a HDInsight segítségével](../hdinsight-use-hive.md)
* [Adatok feltöltése a HDInsightba:](../hdinsight-upload-data.md)További módszereket találhat az adatok HDInsight/Azure Blob storage-ba való feltöltésére.
* [Adatok importálása és exportálása az Apache Sqoop használatával a HDInsight-alapú Apache Hadoop és az SQL Database között](./apache-hadoop-use-sqoop-mac-linux.md)