---
title: Apache Sqoop-feladatok futtatása az Azure HDInsight (Apache Hadoop)
description: Megtudhatja, hogyan használhatja a Azure PowerShell egy munkaállomásról a Sqoop importálásának és exportálásának futtatására egy Hadoop-fürt és egy Azure SQL Database között.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951853"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Az Apache Sqoop és a Hadoop használata a HDInsight-ben

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogy miként lehet az Apache Sqoop használatával HDInsight importálni és exportálni egy HDInsight-fürt és egy Azure SQL Database-adatbázis között.

Bár a Apache Hadoop a strukturálatlan és részben strukturált adatmennyiségek, például naplók és fájlok feldolgozásának természetes lehetősége, előfordulhat, hogy a kapcsolódó adatbázisokban tárolt strukturált adatmennyiségeket is fel kell dolgoznia.

Az [Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) egy olyan eszköz, amely a Hadoop-fürtök és a kapcsolati adatbázisok közötti adatátvitelre szolgál. Ezzel a beállítással adatok importálhatók a Hadoop elosztott fájlrendszerbe (HDFS) tartozó, a RDBMS-ből (például a SQL Server, a MySQL-ből vagy az Oracle-ből), és átalakíthatja a Hadoop-ben tárolt adatok MapReduce vagy Apache Hiveét, majd visszaexportálhatja az adatok RDBMS . Ebben a cikkben egy SQL Server adatbázist használ a kapcsolódó adatbázishoz.

> [!IMPORTANT]  
> Ez a cikk egy tesztkörnyezetben állítja be az adatátvitel végrehajtását. Ezután válasszon egy adatátviteli módszert ehhez a környezethez a [Sqoop-feladatok futtatása](#run-sqoop-jobs)szakaszban, a továbbiak részben.

A HDInsight-fürtökön támogatott Sqoop-verziókért lásd: [Újdonságok a HDInsight által biztosított fürtcsomópontok esetében?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>A forgatókönyv megismerése

A HDInsight-fürthöz egyes mintaadatok is tartozik. A következő két mintát használja:

* Egy Apache Log4j naplófájl, amely a következő helyen található: `/example/data/sample.log`. Az alábbi naplók a fájlból vannak kinyerve:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* `hivesampletable`nevű struktúra-tábla, amely a `/hive/warehouse/hivesampletable`címen található adatfájlra hivatkozik. A tábla tartalmaz néhány mobileszköz-adategységet.
  
  | Mező | Data type |
  | --- | --- |
  | ClientID |sztring |
  | querytime |sztring |
  | piac |sztring |
  | deviceplatform |sztring |
  | devicemake |sztring |
  | devicemodel |sztring |
  | state |sztring |
  | ország |sztring |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

Ebben a cikkben ezt a két adatkészletet használja a Sqoop importálásának és exportálásának teszteléséhez.

## <a name="create-cluster-and-sql-database"></a>Tesztkörnyezet beállítása

A fürt, az SQL Database és az egyéb objektumok a Azure Portalon keresztül jönnek létre Azure Resource Manager sablon használatával. A sablon az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)található. A Resource Manager-sablon egy bacpac-csomagot hív meg, hogy a tábla sémáit egy SQL-adatbázisba telepítse.  A bacpac csomag egy nyilvános blob-tárolóban található, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Ha privát tárolót szeretne használni a bacpac-fájlokhoz, használja a következő értékeket a sablonban:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Az importálás sablon használatával vagy a Azure Portal csak a BACPAC-fájlok importálását támogatja az Azure Blob Storage-ból.

1. Az alábbi képre kattintva nyissa meg a Resource Manager-sablont a Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Adja meg a következő tulajdonságokat:

    |Mező |Value (Díj) |
    |---|---|
    |Előfizetés |Válassza ki az Azure-előfizetését a legördülő listából.|
    |Erőforráscsoport |Válassza ki az erőforráscsoportot a legördülő listából, vagy hozzon létre egy újat|
    |Földrajzi egység |Válasszon ki egy régiót a legördülő listából.|
    |Fürt neve |Adja meg a Hadoop-fürt nevét. Csak kisbetűket használjon.|
    |Fürt bejelentkezési felhasználóneve |Tartsa meg az előre megadott értéket `admin`.|
    |Fürt bejelentkezési jelszava |Adjon meg egy jelszót.|
    |SSH-Felhasználónév |Tartsa meg az előre megadott értéket `sshuser`.|
    |SSH-jelszó |Adjon meg egy jelszót.|
    |SQL-rendszergazdai bejelentkezés |Tartsa meg az előre megadott értéket `sqluser`.|
    |SQL-rendszergazdai jelszó |Adjon meg egy jelszót.|
    |_artifacts helye | Használja az alapértelmezett értéket, ha egy másik helyen szeretné használni a saját bacpac-fájlját.|
    |_artifacts hely sas-tokenje |Hagyja üresen.|
    |Bacpac-fájl neve |Használja az alapértelmezett értéket, ha nem kívánja használni a saját bacpac-fájlját.|
    |Földrajzi egység |Használja az alapértelmezett értéket.|

    Az Azure SQL Server neve `<ClusterName>dbserver`lesz. Az adatbázis neve `<ClusterName>db`lesz. Az alapértelmezett Storage-fiók neve `e6qhezrh2pdqu`lesz.

3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket**.

4. Válassza a **Beszerzés** lehetőséget. Egy új csempe jelenik meg, amely a központi telepítés elküldését Template deployment. A fürt és az SQL-adatbázis létrehozása nagyjából 20 percet vesz igénybe.

## <a name="run-sqoop-jobs"></a>Sqoop-feladatok futtatása

A HDInsight számos módszer használatával futtathat Sqoop-feladatokat. A következő táblázat segítségével eldöntheti, hogy melyik módszer a legmegfelelőbb, majd kövesse a bemutató hivatkozását.

| **Használja ezt** , ha szeretné... | ... **interaktív** rendszerhéj | ... **kötegelt** feldolgozás | ... Ebből az **ügyféloldali operációs rendszerből** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, UNIX, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (egyelőre) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – a Linux-alapú HDInsight a Sqoop-összekötő, amellyel az adatexportálás Microsoft SQL Server vagy Azure SQL Database jelenleg nem támogatja a tömeges beszúrásokat.
* Kötegelt feldolgozás – a Linux-alapú HDInsight, amikor a `-batch` kapcsolót használja a lapkák végrehajtásakor, a Sqoop több beszúrást hajt végre az INSERT műveletekhez.

## <a name="next-steps"></a>Következő lépések

Most már megtanulta, hogyan használhatja a Sqoop-t. További tudnivalókért lásd:

* [Apache Hive használata a HDInsight](../hdinsight-use-hive.md)
* [Adatok feltöltése a HDInsight-be](../hdinsight-upload-data.md): további módszerek az adatok HDInsight/Azure Blob Storage-ba való feltöltéséhez.
* [Adatok importálása és exportálása az Apache Sqoop használatával a HDInsight-alapú Apache Hadoop és az SQL Database között](./apache-hadoop-use-sqoop-mac-linux.md)