---
title: PowerShell – Azure HDInsight segítségével Hadoop-fürtök létrehozása
description: Ismerje meg, hogyan hozhat létre Hadoop, HBase, Storm vagy Spark-fürtök linuxon HDInsight az Azure PowerShell-lel.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: ed9c486dfa548e86e558c093113011cabfd3d63c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997420"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Linux-alapú fürtök létrehozása az Azure PowerShell használatával HDInsight

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Az Azure PowerShell az egy hatékony parancsfájl-kezelési környezet, amellyel szabályozhatja és automatizálhatja az üzembe helyezéséhez és felügyeletéhez a Microsoft Azure-ban a számítási feladatokat. Ez a dokumentum egy Linux-alapú HDInsight-fürt létrehozása az Azure PowerShell-lel kapcsolatos információkat nyújt. Példa parancsfájl is tartalmaz.

> [!NOTE]
> Az Azure PowerShell csak Windows-ügyfelek számára érhető el. Ha a Linux, Unix vagy Mac OS X-ügyfelet használ, tekintse meg [hozzon létre egy Linux-alapú HDInsight-fürtöt klasszikus Azure CLI-vel](hdinsight-hadoop-create-linux-clusters-azure-cli.md) fürt létrehozása a klasszikus parancssori felület használatával kapcsolatos információkat.

## <a name="prerequisites"></a>Előfeltételek
Ez az eljárás megkezdése előtt a következőket kell tartalmaznia:

* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnt. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > A lépések végrehajtásával [Azure PowerShell telepítése](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) telepítése az Azure PowerShell legújabb verzióját. Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új, az Azure Resource Managerrel működő parancsmagok használatához, tekintse meg az alábbi cikket: [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Az Azure Resource Manager-alapú fejlesztési eszközökre való áttérés HDInsight-fürtök esetén).

## <a name="create-cluster"></a>Fürt létrehozása

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Egy HDInsight-fürt létrehozása az Azure PowerShell-lel, az alábbi eljárásokat kell elvégeznie:

* Azure-erőforráscsoport létrehozása
* Azure Storage-fiók létrehozása
* Hozzon létre egy Azure Blob-tárolóba
* HDInsight-fürt létrehozása

Az alábbi parancsfájl bemutatja, hogyan hozzon létre egy új fürtöt:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

A fürtre való bejelentkezéshez megadott értékek a Hadoop-felhasználói fiókot a fürt létrehozásához használt. Ez a fiók használatával kapcsolódhat a szolgáltatások, például webes felület... vagy a REST API-k a fürtön található.

Az SSH-felhasználót a megadott értékek a fürthöz az SSH-felhasználó létrehozásához használt. Ez a fiók használatával indítson el egy távoli SSH-munkamenetet a fürtön, valamint a feladatok futtatását. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Ha azt tervezi, több mint 32 munkavégző csomópontok (vagy a fürt létrehozása vagy a fürt létrehozása után felskálázásával) használja, akkor is meg kell egy fő csomópont mérete legalább 8 maggal és 14 GB RAM.
>
> További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

Fürt létrehozása akár 20 percig is eltarthat.

## <a name="create-cluster-configuration-object"></a>Fürt létrehozása: konfigurációs objektum

Egy HDInsight konfigurációs objektum használatával is létrehozhat `New-AzureRmHDInsightClusterConfig` parancsmagot. Ezt követően módosíthatja ezt a konfigurációs objektumot ahhoz, hogy további konfigurációs lehetőségek a fürt számára. Végül a `-Config` paraméterében a `New-AzureRmHDInsightCluster` parancsmagot, hogy a konfigurációt használja.

A következő szkriptet az R Server HDInsight-fürt típusának konfigurálása egy konfigurációs objektumot hoz létre. A konfiguráció lehetővé teszi, az élcsomóponton, az RStudio és egy tárfiókot.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> A HDInsight-fürt, mint egy másik helyen lévő tárfiókok használata nem támogatott. Ebben a példában használatakor a kiszolgáló ugyanazon a helyen hozza létre a további tárfiókot.

## <a name="customize-clusters"></a>Fürtök személyre szabása

* Lásd: [Bootstrap használatával testre szabhatja a HDInsight-fürtök](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt, az alábbi forrásanyagokból megtudhatja, hogyan működnek a fürttel.

### <a name="hadoop-clusters"></a>Hadoop-fürtök

* [A Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-fürtök

* [A HDInsighton futó HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [A HDInsight hbase Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-fürtök

* [Java-topológiák fejlesztése HDInsight alatt futó Stormmal](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata a HDInsight alatt futó Stormmal](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>A Spark-fürtök

* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](spark/apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)

