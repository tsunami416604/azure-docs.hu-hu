---
title: Biztonságos átvitelű tárfiókokkal rendelkező Hadoop-fürt létrehozása az Azure HDInsightban
description: Megtudhatja, hogyan hozhat létre biztonságos átvitelű Azure-tárfiókokkal rendelkező HDInsight-fürtöket.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: 5ad49c8e28f0e91b18af6dc485489d11464fbc0d
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413387"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Az Apache Hadoop-fürt létrehozása biztonságos átvitellel rendelkező tárfiókok az Azure HDInsight

A [biztonságos átvitelt szükséges](../storage/common/storage-require-secure-transfer.md) funkció megnöveli az Azure Storage-fiók biztonságát azáltal, hogy minden, a fiókra beérkező kérést biztonságos kapcsolat használatára kényszerít. Ezt a funkciót és a wasbs sémát csak a HDInsight-fürt 3.6-os vagy újabb verziója támogatja.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elindításának feltétele:

* **Azure-előfizetés**: Egy ingyenes egy hónapos próbafiók létrehozásához keresse meg a [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Engedélyezett biztonságos átvitellel rendelkező Azure-tárfiók** Az utasításokért lásd a [Tárfiók létrehozása](../storage/common/storage-quickstart-create-account.md) és a [Biztonságos átvitel megkövetelése](../storage/common/storage-require-secure-transfer.md) szakaszokat.
* **Blobtároló a tárfiókon**.

## <a name="create-cluster"></a>Fürt létrehozása

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban egy [Azure Resource Manager-sablonnal](../azure-resource-manager/resource-group-template-deploy.md). A sablonban található [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Nem kell a Resource Manager-sablonok használatára vonatkozó tapasztalattal rendelkeznie az oktatóanyag követéséhez. Egyéb fürtlétrehozási módszerekhez és az oktatóanyagban használt tulajdonságok megértéséhez tekintse meg a [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight-fürtök létrehozása) című témakört.

1. Az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg a Resource Manager-sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Kövesse az utasításokat a fürt a következő specifikációkkal való létrehozásához: 

    - Adja meg a HDInsight 3.6-os verzióját. A 3.6-os vagy újabb verzió szükséges.
    - Adjon meg egy biztonságos átvitel használatára képes tárfiókot.
    - Rövid nevet adjon a tárfióknak.
    - Már korábban létre kell hozni a tárfiókot és a blobtárolót is.

      Az utasításokért lásd: [Fürt létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Ha szkriptműveleteket használ a saját konfigurációs fájlok megadásához, a wasbs a következő beállításokkal használandó:

- fs.defaultFS (core-site)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

További, biztonságos átvitel használatára képes tárfiókok hozzáadására számos lehetőség áll rendelkezésre:

- Az előző szakaszban ismertetett Azure Resource Manager-sablon módosítása.
- Egy fürt létrehozása az [Azure Portal](https://portal.azure.com) használatával és a kapcsolódó tárfiók megadása.
- Szkriptműveleteket használata további, biztonságos átvitel használatára képes tárfiókok meglévő HDInsight-fürthöz történő hozzáadásához. További információkért lásd: [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulhatta, hogyan hozhat létre HDInsight-fürtöt, és hogyan engedélyezheti a biztonságos átvitelt a tárfiókokba.

A HDInsight használatával történő adatelemzésről az alábbi cikkekben talál további információt:

* További információ a [Apache Hive](https://hive.apache.org/) a HDInsight, beleértve a Visual Studióban a Hive-lekérdezések végrehajtásához lásd: [Apache Hive használata a HDInsight][hdinsight-use-hive].
* További információ [Apache Pig](https://pig.apache.org/), adatok átalakítására szolgáló lásd [Apache Pig használata a HDInsight-][hdinsight-use-pig].
* További információ [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), tekintse meg a hadoopon adatokat feldolgozó programok írására úgy [Apache Hadoop MapReduce használata a HDInsight-][hdinsight-use-mapreduce].
* A HDInsight-adatok elemzésére a HDInsight Tools for Visual Studio használatával kapcsolatos további információkért lásd: [for HDInsight Visual Studio Apache Hadoop-eszközök használatának megkezdéséhez](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Ha szeretne többet megtudni a HDInsight adattárolási módszereiről, illetve arról, hogyan kerülnek az adatok a HDInsightba, tekintse meg a következő cikkeket:

* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

Ha többet szeretne megtudni a HDInsight-fürtök létréhozásáról vagy kezeléséről, tekintse meg a következő cikkeket:

* Információk a Linux-alapú HDInsight-fürtök kezeléséhez: [Manage HDInsight clusters using Ambari](hdinsight-hadoop-manage-ambari.md) (HDInsight-fürtök kezelése az Ambari segítségével).
* További információ a HDInsight-fürtök létrehozásakor kiválasztható beállításokról: [Creating HDInsight on Linux using custom options](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight létrehozása Linux rendszeren egyéni beállításokkal).
* Ha már ismeri a Linux- és Apache Hadoop, de konkrét részleteket a HDInsight a Hadoop tudni szeretné, tekintse meg [HDInsight használata Linux rendszeren](hdinsight-hadoop-linux-information.md). Ez a cikk többek között az alábbi információkat tartalmazza:

  * URL-címek, mint például a fürtön tárolt szolgáltatások [Apache Ambari](https://ambari.apache.org/) és [webhcat használatával](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * Helyét [Apache Hadoop](https://hadoop.apache.org/) fájlok és -példák a helyi fájlrendszerben
  * Az Azure Storage (WASB) helyett használatát [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) alapértelmezett tárolásához

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
