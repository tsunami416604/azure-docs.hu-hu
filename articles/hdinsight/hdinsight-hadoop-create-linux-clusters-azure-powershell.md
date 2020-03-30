---
title: Apache Hadoop-fürtök létrehozása a PowerShell használatával – Azure HDInsight
description: Ismerje meg, hogyan hozhat létre Apache Hadoop, Apache HBase, Apache Storm vagy Apache Spark-fürtöket Linux for HDInsight-hoz az Azure PowerShell használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 3c93eca493275612ac14a995140b2e91cc40fe98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644664"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Linuxalapú fürtök létrehozása a HDInsightban az Azure PowerShell használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Az Azure PowerShell egy hatékony parancsfájl-kezelési környezet, amely segítségével szabályozhatja és automatizálhatja a számítási feladatok üzembe helyezését és felügyeletét a Microsoft Azure-ban. Ez a dokumentum aLinux-alapú HDInsight-fürt azure PowerShell használatával történő létrehozásáról nyújt tájékoztatást. Ez is tartalmaz egy példa script.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) Az modul.

## <a name="create-cluster"></a>Fürt létrehozása

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ha az Azure PowerShell használatával szeretne HDInsight-fürtöt létrehozni, az alábbi eljárásokat kell végrehajtania:

* Azure-erőforráscsoport létrehozása
* Azure Storage-fiók létrehozása
* Azure Blob-tároló létrehozása
* HDInsight-fürt létrehozása

> [!NOTE]
> A PowerShell használata HDInsight-fürt létrehozásához az Azure Data Lake Storage Gen2 használatával jelenleg nem támogatott.

A következő parancsfájl bemutatja, hogyan hozhat létre új fürtöt:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

A fürt bejelentkezéséhez megadott értékek a hadoop felhasználói fiók létrehozásához használatosak a fürthöz. Ezzel a fiókkal csatlakozhat a fürtön tárolt szolgáltatásokhoz, például a webes ui-khoz vagy a REST API-khoz.

Az SSH-felhasználó számára megadott értékek a fürt SSH-felhasználójának létrehozásához használatosak. Ezzel a fiókkal távoli SSH-munkamenetet indíthat a fürtön, és feladatokat futtat. További információ: [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Ha 32-nél több munkavégző csomópontot kíván használni (akár a fürt létrehozásakor, akár a fürt létrehozása után történő méretezéssel), meg kell adnia egy legalább 8 maggal és 14 GB RAM-mal rendelkező főcsomópontméretet is.
>
> További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

Fürt létrehozása akár 20 percet is igénybe vehet.

## <a name="create-cluster-configuration-object"></a>Fürt létrehozása: Konfigurációs objektum

HdInsight konfigurációs objektumot parancsmag használatával [`New-AzHDInsightClusterConfig`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) is létrehozhat. Ezután módosíthatja ezt a konfigurációs objektumot, hogy további konfigurációs beállításokat engedélyezze a fürthöz. Végül használja `-Config` a [`New-AzHDInsightCluster`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parancsmag paraméterét a konfiguráció használatához.

A következő parancsfájl létrehoz egy konfigurációs objektumot az R-kiszolgáló KONFIGURÁLásához a HDInsight fürttípuson. A konfiguráció lehetővé teszi egy peremhálózati csomópont, RStudio, és egy további tárfiók.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen lévő tárfiók használata nem támogatott. Ebben a példában hozza létre a további tárfiókot ugyanazon a helyen, mint a kiszolgáló.

## <a name="customize-clusters"></a>Fürtök testreszabása

* Lásd: [HDInsight-fürtök testreszabása a Bootstrap használatával című témakörben.](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)
* Lásd: [HDInsight-fürtök testreszabása parancsfájlművelettel.](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt, az alábbi erőforrások segítségével megtudhatja, hogyan dolgozhat a fürttel.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase-lel a HDInsight-on](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java alkalmazások fejlesztése Apache HBase alkalmazáshoz a HDInsight-on](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Viharklaszterek

* [Java topológiák fejlesztése stormhoz a HDInsighton](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata a Storm ban a HDInsighton](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése a Storm segítségével a HDInsighton](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark-fürtök

* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](spark/apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](spark/apache-spark-machine-learning-mllib-ipython.md)