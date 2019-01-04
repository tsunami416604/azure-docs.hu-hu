---
title: Az Azure klasszikus parancssori felület használatával – Azure HDInsight az Apache Hadoop-fürtök létrehozása
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtöket a platformfüggetlen Azure klasszikus parancssori felület használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: c9b1f3570ae3f9b945c87c2ce316c3f72e32b4ad
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792050"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>A klasszikus Azure CLI-vel HDInsight-fürtök létrehozása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A klasszikus Azure CLI-vel HDInsight 3.5-ös fürtöt hoz létre a dokumentum útmutatóban leírt lépéseket.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Az Azure klasszikus parancssori felület**. A jelen dokumentumban leírt lépések az Azure klasszikus parancssori felület verziója 0.10.14 utolsó tesztelt.

## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

Kövesse a leírt lépéseket [csatlakozás az Azure-előfizetés az Azure parancssori felület](/cli/azure/authenticate-azure-cli) , és az előfizetés használatával kapcsolódhat a **bejelentkezési** metódus.

## <a name="create-a-cluster"></a>Fürt létrehozása

Például a PowerShell vagy a Bash-parancssorból a következő lépéseket kell végrehajtani.

1. A következő parancs segítségével hitelesíti az Azure-előfizetéshez:

        azure login

    Adja meg a nevét és jelszavát kéri. Ha több Azure-előfizetéssel rendelkezik, használja a `azure account set <subscriptionname>` beállítása az előfizetést, amely a klasszikus parancssori felület parancsai használni.

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

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>További lépések

Most, hogy sikeresen létrehozott egy HDInsight-fürtön a klasszikus parancssori felület, megtudhatja, hogyan használható a fürt a következő használatával:

### <a name="apache-hadoop-clusters"></a>Az Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Az Apache HBase-fürtök

* [A HDInsight Apache HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Az Apache HBase on HDInsight Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-fürtök

* [Java-topológiák fejlesztése a HDInsight az Apache stormmal](storm/apache-storm-develop-java-topology.md)
* [Az Apache Storm on HDInsight használata a Python-összetevők](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight Apache Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)
