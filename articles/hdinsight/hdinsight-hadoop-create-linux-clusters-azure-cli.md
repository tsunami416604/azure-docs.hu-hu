---
title: Használja a parancssori – Azure HDInsight Hadoop-fürtök létrehozása
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtök a platformfüggetlen Azure CLI 1.0 használatával.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: jasonh
ms.openlocfilehash: 7185e492a2cc42835ce1fa7043a963c7d5d0afb4
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594797"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Az Azure CLI használatával HDInsight-fürtök létrehozása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ez a dokumentum az útmutató egy HDInsight 3.5-fürtöt az Azure CLI 1.0 használatával lépéseit.

> [!IMPORTANT]
> Ez a témakör ismerteti, hogyan hozhat létre HDInsight-fürtöt az Azure CLI 1.0 használatával. Ez a CLI verziója elavult, és a HDInsight-fürtök létrehozása során nem már támogatja az Azure CLI 2.0-t.
>
> Azure PowerShell használatával HDInsight-fürtök létrehozása és felügyelete. További információkért lásd: a [létre HDInsight-fürtök Azure PowerShell-lel](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) dokumentumot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure parancssori felület (CLI)**. A jelen dokumentumban leírt lépések az Azure CLI-vel verzió 0.10.14 utolsó tesztelt.

    > [!IMPORTANT]
    > Az Azure CLI 1.0 elavult, és a HDInsight-fürtök létrehozása során nem már támogatja az Azure CLI 2.0-t.

## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

Kövesse a [Csatlakozás Azure-előfizetéshez az Azure parancssori felületről](/cli/azure/authenticate-azure-cli) lépéseit, és csatlakoztassa az előfizetését a **bejelentkezéses** módszerrel.

## <a name="create-a-cluster"></a>Fürt létrehozása

Például a PowerShell vagy a Bash-parancssorból a következő lépéseket kell végrehajtani.

1. A következő parancs segítségével hitelesíti az Azure-előfizetéshez:

        azure login

    Adja meg a nevét és jelszavát kéri. Ha több Azure-előfizetéssel rendelkezik, használja a `azure account set <subscriptionname>` beállítása az előfizetést, amelyhez az Azure CLI-parancsok használata.

2. Váltson Azure Resource Manager módra az alábbi paranccsal:

        azure config mode arm

3. Hozzon létre egy erőforráscsoportot. Ez az erőforráscsoport a HDInsight-fürt található, és a társított storage-fiókot.

        azure group create groupname location

    * Cserélje le `groupname` a csoport, egyedi névvel.

    * Cserélje le `location` az a földrajzi régióban, amely a csoport létrehozásához.

       Érvényes helyek listájának megtekintéséhez használja a `azure location list` parancsot, és használja a helyek a `Name` oszlop.

4. Tárfiók létrehozása. Ezt a tárfiókot az alapértelmezett tárolóként szolgál a HDInsight-fürt.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Cserélje le `groupname` a csoport az előző lépésben létrehozott nevét.

    * Cserélje le `location` az ugyanazon a helyen az előző lépésben használt.

    * Cserélje le `storagename` a tárfiók egy egyedi névvel.

        > [!NOTE]
        > Ez a parancs paraméterei a további információkat `azure storage account create -h` a parancs súgójának megtekintéséhez.

5. A tárfiók eléréséhez használt kulcs lekéréséhez.

        azure storage account keys list -g groupname storagename

    * Cserélje le `groupname` az az erőforráscsoport nevét.
    * Cserélje le `storagename` a tárfiók nevére.

     A visszaadott adatok mentése a `key` értékét `key1`.

6. Hozzon létre egy HDInsight-fürtön.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Cserélje le `groupname` az az erőforráscsoport nevét.

    * Cserélje le `Hadoop` a létrehozni kívánt fürt típus. Ha például `Hadoop`, `HBase`, `Kafka`, `Spark`, vagy `Storm`.

     > [!IMPORTANT]
     > HDInsight fürtöket a különféle, amelyek megfelelnek a számítási feladatok vagy technológia, amely a fürt van hangolva származnak. Nincs támogatott módszer, amely ötvözi az több típus, például a Storm és a egy fürtön a HBase-fürt létrehozásához.

    * Cserélje le `location` az előző lépésekben használt ugyanazon a helyen.

    * Cserélje le `storagename` az a tárfiók nevét.

    * Cserélje le `storagekey` az előző lépésben kimásolt kulccsal.

    * Az a `--defaultStorageContainer` paraméter, a neve megegyezik, a fürt használ.

    * Cserélje le `admin` és `httppassword` nevére, illetve a fürt HTTPS-en keresztül elérésekor használni kívánt jelszót.

    * Cserélje le `sshuser` és `sshuserpassword` a felhasználónévvel és a jelszót kívánja használni, amikor a fürthöz SSH használatával éri el

    > [!IMPORTANT]
    > Ebben a példában egy fürtöt hoz létre két feldolgozó csomóponttal. A méretezési műveletek végrehajtásával fürt létrehozása után is módosíthatja a munkavégző csomópontok számát. Ha több mint 32 munkavégző csomópontok használatával, majd jelöljön ki egy fő csomópont méretének legalább 8 maggal és 14 GB RAM. Beállíthatja a fő csomópont méretét használatával a `--headNodeSize` paraméter a fürt létrehozásakor.
    >
    > További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

    A Fürtlétrehozási folyamat befejezéséhez több percig is eltarthat. Általában körülbelül 15.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtöt az Azure CLI-vel, megtudhatja, hogyan használható a fürt a következő használatával:

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
