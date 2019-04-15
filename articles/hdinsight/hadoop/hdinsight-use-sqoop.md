---
title: Az Apache Sqoop-feladatok futtatása az Azure HDInsight (Apache Hadoop)
description: Megtudhatja, hogyan használhatja az Azure Powershellt egy munkaállomásról futtatása Sqoop-importálás és exportálása egy Hadoop-fürtöt és a egy Azure SQL database között.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565852"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Az Apache Sqoop használata a HDInsight Hadoop-keretrendszerrel
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Útmutató a HDInsight az Apache Sqoop használatával importálása és exportálása az adatokat egy HDInsight-fürt és a egy Azure SQL database között.

Bár az Apache Hadoop feldolgozásának strukturálatlan és részben strukturált adatok, naplók és a fájlokat, például kézenfekvő lehet a is lehet szükség, amely a relációs adatbázisokban tárolt strukturált adatok feldolgozása.

[Az Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) eszközt úgy tervezték, Hadoop-fürtök és a relációs adatbázisok közötti adatátvitelhez. Adatokat importálhat egy relációsadatbázis-kezelő rendszerének (RDBMS), például az SQL Server, MySQL és a Hadoop elosztott fájlrendszer (HDFS), az Oracle az adatok átalakítása a Hadoop MapReduce-vagy Apache Hive, és ezután exportálhatja az adatokat egy RDBMS be újra használhatja . Ebben a cikkben használni kívánt SQL Server-adatbázis a relációs adatbázis.

> [!IMPORTANT]  
> Ez a cikk beállítja a tesztkörnyezetben, hogy az adatátvitelhez. Majd válassza az ebben a környezetben az adatok átvitel módját a szakaszban a módszerek [Sqoop futtatása feladatok](#run-sqoop-jobs), használja a lentebb.

A HDInsight-fürtökön támogatott verziói a Sqoop, lásd: [a HDInsight által biztosított fürtverziók újdonságai?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>A forgatókönyv megismerése

HDInsight-fürt mintaadatokat tartalmaz. A következő két mintát használja:

* Az Apache Log4j naplófájlt, amely a következő helyen található `/example/data/sample.log`. A fájl kinyert a következő naplók kapcsolódnak:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Hive-tábla nevű `hivesampletable`, hivatkozik az adatfájl található `/hive/warehouse/hivesampletable`. A tábla tartalmazza az egyes mobileszköz-adatok.
  
  | Mező | Adattípus |
  | --- | --- |
  | ClientID |sztring |
  | querytime |sztring |
  | piaci |sztring |
  | deviceplatform |sztring |
  | devicemake |sztring |
  | devicemodel |sztring |
  | state |sztring |
  | Ország |sztring |
  | querydwelltime |double |
  | munkamenet-azonosító |bigint |
  | sessionpagevieworder |bigint |

Ez a cikk a két adatkészlet használhatja tesztelése Sqoop-importálás és exportálása.

## <a name="create-cluster-and-sql-database"></a>Tesztkörnyezet beállítása
A fürt, az SQL database és más objektumok jönnek létre az Azure Portalon egy Azure Resource Manager-sablon használatával. A sablonban található [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). A Resource Manager-sablon üzembe helyezéséhez a táblasémákat az SQL-adatbázis bacpac csomag meghívja.  A bacpac-csomag egy nyilvános blob-tárolóban található https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Ha szeretne egy személyes tárolót használja a bacpac-fájlt, használja a sablonban a következő értékeket:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importálja a sablon használatával, vagy csak támogatja az Azure Portalon a BACPAC-fájl importálása az Azure blob storage-ból.

1. Válassza ki az alábbi képre kattintva nyissa meg a Resource Manager-sablon az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Adja meg a következő tulajdonságokat:

    |Mező |Érték |
    |---|---|
    |Előfizetés |A legördülő listából válassza ki az Azure-előfizetésében.|
    |Erőforráscsoport |A legördülő listában jelölje ki az erőforráscsoportot, vagy hozzon létre egy újat|
    |Földrajzi egység |A legördülő listából válassza ki a régiót.|
    |Fürt neve |Adja meg a Hadoop-fürt nevét. Csak kisbetű használja.|
    |Fürt bejelentkezési felhasználóneve |Tartsa az előre megadott értéket `admin`.|
    |Fürt bejelentkezési jelszava |Adja meg a jelszót.|
    |Ssh-felhasználónév |Tartsa az előre megadott értéket `sshuser`.|
    |Ssh jelszó |Adja meg a jelszót.|
    |SQL-rendszergazda felhasználóneve |Tartsa az előre megadott értéket `sqluser`.|
    |Sql Admin Password |Adja meg a jelszót.|
    |_artifacts helye | Használja az alapértelmezett értéket, hacsak nem szeretné használni a saját bacpac-fájlt egy másik helyen található.|
    |_artifacts hely Sas-jogkivonat |Hagyja üresen.|
    |Bacpac-fájl neve |Használja az alapértelmezett értéket, hacsak nem szeretné használni a saját bacpac-fájlba.|
    |Földrajzi egység |Használja az alapértelmezett értéket.|

    Az Azure SQL-kiszolgáló neve lesz `<ClusterName>dbserver`. Az adatbázis neve lesz `<ClusterName>db`. Az alapértelmezett tárfiók neve lesz `e6qhezrh2pdqu`.

3. Válassza ki **elfogadom a feltételeket és a fenti feltételeket**.

4. Válassza a **Beszerzés** lehetőséget. Megjelenik egy új csempe nevű elküldés központi telepítési sablon üzembe helyezéshez. A fürt és az SQL-adatbázis létrehozása nagyjából 20 percet vesz igénybe.

## <a name="run-sqoop-jobs"></a>Sqoop-feladatok futtatása

HDInsight számos módszer használatával Sqoop-feladatok futtatásához. A következő táblázat segítségével döntse el, melyik módszer a legmegfelelőbb Önnek, majd kövesse a hivatkozást bemutató.

| **Ezzel** Ha azt szeretné... | ...an **interaktív** rendszerhéj | ...**kötegelt** feldolgozása | ...from ez **ügyfél operációs rendszer** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X vagy Windows |
| [.NET SDK a Hadoophoz](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (egyelőre) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Korlátozások

* Tömeges exportálása – a Linux-alapú HDInsight, a Sqoop-összekötő segítségével a Microsoft SQL Server vagy az Azure SQL Database-adatok exportálása jelenleg nem támogatja a tömeges beszúrás.
* Kötegelés – a Linux-alapú HDInsight használatakor a `-batch` Beszúrások végrehajtásakor váltson, Sqoop több beszúrás helyett a beszúrási műveletek kötegelése hajt végre.

## <a name="next-steps"></a>További lépések
Most már megtanulhatta, hogyan használható a sqoop használatával. További tudnivalókért lásd:

* [Az Apache Hive használata a HDInsight](../hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](../hdinsight-use-pig.md)
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md): Keresse meg a HDInsight vagy az Azure Blob storage-ba történő feltöltéséhez más módszerekkel.
