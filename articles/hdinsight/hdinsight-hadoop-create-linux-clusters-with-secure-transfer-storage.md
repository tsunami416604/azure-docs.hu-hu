---
title: "Biztonságos átvitelű tárfiókokkal rendelkező Hadoop-fürt létrehozása az Azure HDInsightban | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre biztonságos átvitelű Azure-tárfiókokkal rendelkező HDInsight-fürtöket."
keywords: "hadoop első lépései,hadoop linux,hadoop gyorsútmutató,biztonságos átvitel,azure-tárfiók"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 9b537595fd8224536f67989d7529f6030347bfab
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="create-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Biztonságos átvitelű tárfiókokkal rendelkező Hadoop-fürt létrehozása az Azure HDInsightban

A [biztonságos átvitelt szükséges](../storage/common/storage-require-secure-transfer.md) funkció megnöveli az Azure Storage-fiók biztonságát azáltal, hogy minden, a fiókra beérkező kérést biztonságos kapcsolat használatára kényszerít. Ezt a funkciót és a wasbs sémát csak a HDInsight-fürt 3.6-os vagy újabb verziója támogatja. 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elindításának feltétele:

* **Azure-előfizetés**: egy ingyenes egy hónapos próbafiók létrehozásához keresse fel az [azure.microsoft.com/free](https://azure.microsoft.com/free) webhelyet.
* **Engedélyezett biztonságos átvitellel rendelkező Azure-tárfiók** Az utasításokért lásd a [Tárfiók létrehozása](../storage/common/storage-create-storage-account.md#create-a-storage-account) és a [Biztonságos átvitel megkövetelése](../storage/common/storage-require-secure-transfer.md) szakaszokat.
* **Blobtároló a tárfiókon**. 
## <a name="create-cluster"></a>Fürt létrehozása

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban egy [Azure Resource Manager-sablonnal](../azure-resource-manager/resource-group-template-deploy.md). A sablon a [GitHubon](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/) található. Nem kell a Resource Manager-sablonok használatára vonatkozó tapasztalattal rendelkeznie az oktatóanyag követéséhez. Egyéb fürtlétrehozási módszerekhez és az oktatóanyagban használt tulajdonságok megértéséhez tekintse meg a [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight-fürtök létrehozása) című témakört.

1. Az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg a Resource Manager-sablont az Azure Portalon. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Kövesse az utasításokat a fürt a következő specifikációkkal való létrehozásához: 

    - Adja meg a HDInsight 3.6-os verzióját.  Az alapértelmezett verzió a 3.5. A 3.6-os vagy újabb verzió szükséges.
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
- Szkriptműveleteket használata további, biztonságos átvitel használatára képes tárfiókok meglévő HDInsight-fürthöz történő hozzáadásához.  További információkért lásd: [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulhatta, hogyan hozhat létre HDInsight-fürtöt, és hogyan engedélyezheti a biztonságos átvitelt a tárfiókokba.

A HDInsight használatával történő adatelemzésről az alábbi cikkekben talál további információt:

* További információ a Hive és a HDInsight együttes használatáról, például a Hive-lekérdezések Visual Studióból történő végrehajtásáról: [A Hive használata a HDInsighttal][hdinsight-use-hive].
* További információ az adatok átalakítására szolgáló Pig nyelvről: [A Pig használata a HDInsighttal][hdinsight-use-pig].
* További információ a Hadoopon adatokat feldolgozó programok írására szolgáló MapReduce módszerről: [A MapReduce használata a HDInsighttal][hdinsight-use-mapreduce].
* A HDInsight-adatok elemzésére szolgáló HDInsight Tools for Visual Studio szolgáltatással kapcsolatos további információkért lásd: [Get started using Visual Studio Hadoop tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md) (A HDInsight Visual Studio Hadoop-eszközeinek használatára vonatkozó első lépések).

Ha szeretne többet megtudni a HDInsight adattárolási módszereiről, illetve arról, hogyan kerülnek az adatok a HDInsightba, tekintse meg a következő cikkeket:

* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

Ha többet szeretne megtudni a HDInsight-fürtök létréhozásáról vagy kezeléséről, tekintse meg a következő cikkeket:

* Információk a Linux-alapú HDInsight-fürtök kezeléséhez: [Manage HDInsight clusters using Ambari](hdinsight-hadoop-manage-ambari.md) (HDInsight-fürtök kezelése az Ambari segítségével).
* További információ a HDInsight-fürtök létrehozásakor kiválasztható beállításokról: [Creating HDInsight on Linux using custom options](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight létrehozása Linux rendszeren egyéni beállításokkal).
* Ha már ismeri a Linux és a Hadoop használatát, de szeretne konkrét részleteket megtudni a HDInsight-alapú Hadoopról, akkor tekintse meg a következő témakört: [Working with HDInsight on Linux](hdinsight-hadoop-linux-information.md) (A HDInsight használata Linux rendszeren). Ez a cikk többek között az alábbi információkat tartalmazza:
  
  * a fürtön tárolt szolgáltatások, például az Ambari és a WebHCat URL-címét;
  * a Hadoop-fájlok és- példák helyét a helyi fájlrendszerben;
  * az Azure Storage (WASB) alapértelmezett adattárként történő használatát a HDFS helyett.

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md


