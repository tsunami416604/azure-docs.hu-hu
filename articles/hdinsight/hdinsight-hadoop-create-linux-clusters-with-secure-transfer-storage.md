---
title: Apache Hadoop & biztonságos átvitelű tároló – Azure HDInsight
description: Megtudhatja, hogyan hozhat létre biztonságos átvitelű Azure-tárfiókokkal rendelkező HDInsight-fürtöket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/22/2020
ms.openlocfilehash: a8176cc07296b7de7b6aba5356485280ef5ebde1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548815"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Biztonságos átvitelű Storage-fiókkal rendelkező Apache Hadoop-fürt létrehozása az Azure HDInsight

A [biztonságos átvitelt szükséges](../storage/common/storage-require-secure-transfer.md) funkció megnöveli az Azure Storage-fiók biztonságát azáltal, hogy minden, a fiókra beérkező kérést biztonságos kapcsolat használatára kényszerít. Ezt a funkciót és a wasbs sémát csak a HDInsight-fürt 3.6-os vagy újabb verziója támogatja.

**A Biztonságos tár átvitelének engedélyezése a fürt létrehozása után hibákat eredményezhet a Storage-fiókkal, és nem ajánlott. Jobb, ha új fürtöt hoz létre, amelynek a tulajdonsága engedélyezve van.**

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt a következőket kell tennie:

* Azure-előfizetés: egy hónapos ingyenes próbaverziós fiók létrehozásához keresse meg a [Azure.microsoft.com/Free](https://azure.microsoft.com/free).
* Egy biztonságos átvitelt engedélyező Azure Storage-fiók. Az utasításokért lásd a [Tárfiók létrehozása](../storage/common/storage-account-create.md) és a [Biztonságos átvitel megkövetelése](../storage/common/storage-require-secure-transfer.md) szakaszokat. 
* Egy blob-tároló a Storage-fiókban.

## <a name="create-cluster"></a>Fürt létrehozása

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban egy [Azure Resource Manager-sablonnal](../azure-resource-manager/templates/deploy-powershell.md). A sablon a [githubon](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/)található. A cikk végrehajtásához nincs szükség Resource Manager-sablonra. A további fürtök létrehozási módszereivel és a cikkben használt tulajdonságok megismerésével kapcsolatban lásd: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

1. Az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg a Resource Manager-sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Kövesse az utasításokat a fürt a következő specifikációkkal való létrehozásához:

    * Adja meg a HDInsight 3.6-os verzióját. A 3.6-os vagy újabb verzió szükséges.
    * Adjon meg egy biztonságos átvitel használatára képes tárfiókot.
    * Rövid nevet adjon a tárfióknak.
    * Már korábban létre kell hozni a tárfiókot és a blobtárolót is.

      Az utasításokért lásd: [Fürt létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Ha szkriptműveleteket használ a saját konfigurációs fájlok megadásához, a wasbs a következő beállításokkal használandó:

* fs.defaultFS (core-site)
* spark.eventLog.dir
* spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

További, biztonságos átvitel használatára képes tárfiókok hozzáadására számos lehetőség áll rendelkezésre:

* Az előző szakaszban ismertetett Azure Resource Manager-sablon módosítása.
* Egy fürt létrehozása az [Azure Portal](https://portal.azure.com) használatával és a kapcsolódó tárfiók megadása.
* Szkriptműveleteket használata további, biztonságos átvitel használatára képes tárfiókok meglévő HDInsight-fürthöz történő hozzáadásához. További információkért lásd: [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre egy HDInsight-fürtöt, és hogyan engedélyezheti a biztonságos átvitelt a Storage-fiókokra.

A HDInsight használatával történő adatelemzésről az alábbi cikkekben talál további információt:

* Ha többet szeretne megtudni a [Apache Hive](https://hive.apache.org/) és a HDInsight használatáról, többek között a kaptár-lekérdezések Visual studióból történő végrehajtásáról, tekintse meg a [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md).
* Ha szeretne többet megtudni [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), hogyan írhat olyan programokat, amelyek a Hadoop adatait dolgozzák fel, tekintse meg a [Apache Hadoop MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)-mel című témakört.
* Ha szeretné megtudni, hogyan használhatja a Visual Studio HDInsight-eszközeit a HDInsight lévő információk elemzéséhez, tekintse meg a [Visual studio Apache Hadoop HDInsight-eszközök használatának első lépései](hadoop/apache-hadoop-visual-studio-tools-get-started.md)című témakört.

Ha szeretne többet megtudni a HDInsight adattárolási módszereiről, illetve arról, hogyan kerülnek az adatok a HDInsightba, tekintse meg a következő cikkeket:

* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md).

Ha többet szeretne megtudni a HDInsight-fürtök létréhozásáról vagy kezeléséről, tekintse meg a következő cikkeket:

* A Linux-alapú HDInsight-fürt kezelésével kapcsolatban lásd: [HDInsight-fürtök kezelése az Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md).
* További információ a HDInsight-fürtök létrehozásakor kiválasztható beállításokról: [Creating HDInsight on Linux using custom options](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight létrehozása Linux rendszeren egyéni beállításokkal).
* Ha már ismeri a Linuxot, és Apache Hadoop, de szeretné tudni, hogy a HDInsight Hadoop kapcsolatos részletekről van szó, tekintse [meg a HDInsight Linux rendszeren való](hdinsight-hadoop-linux-information.md)használatát ismertető témakört. Ez a cikk többek között az alábbi információkat tartalmazza:

  * A fürtön üzemeltetett szolgáltatások URL-címei, például az [Apache Ambari](https://ambari.apache.org/) és a [webhcaten](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * [Apache Hadoop](https://hadoop.apache.org/) fájlok és példák helye a helyi fájlrendszerben
  * Az Azure Storage (WASB) használata [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) helyett alapértelmezett adattárként
