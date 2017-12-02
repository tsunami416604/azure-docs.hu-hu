---
title: "Használja a parancssori-Azure HDInsight Hadoop-fürtök létrehozása |} Microsoft Docs"
description: "Útmutató a platformok közötti Azure CLI 1.0 HDInsight-fürtök létrehozásához."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: 25dc37c10435e45bc56fd48488f9f238922ed6e3
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Az Azure parancssori felület használata a HDInsight-fürtök létrehozása

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Ez a dokumentum az útmutató egy HDInsight 3.5-ös verzióját az Azure CLI 1.0 fürtöt hoz létre lépéseit.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).


## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Azure parancssori felület (CLI)**. A jelen dokumentumban leírt lépések alapján utolsó történő teszteléskor az Azure CLI 0.10.14 verziója.

    > [!IMPORTANT]
    > A jelen dokumentumban leírt lépések az Azure CLI 2.0 nem működik. Az Azure CLI 2.0 nem támogatja a HDInsight-fürtök létrehozása.

## <a name="log-in-to-your-azure-subscription"></a>Bejelentkezés az Azure-előfizetésbe

Kövesse a [Csatlakozás Azure-előfizetéshez az Azure parancssori felületről](../xplat-cli-connect.md) lépéseit, és csatlakoztassa az előfizetését a **bejelentkezéses** módszerrel.

## <a name="create-a-cluster"></a>Fürt létrehozása

Az alábbi lépéseket kell elvégezni a parancssorból, például a PowerShell vagy a Bash.

1. A következő paranccsal, hogy az Azure-előfizetéshez hitelesítést:

        azure login

    A felhasználónevet és jelszót kéri. Ha több Azure-előfizetéssel rendelkezik, `azure account set <subscriptionname>` , amelyek az Azure parancssori felület parancsait használják az előfizetés beállításához.

2. Váltson Azure Resource Manager módra az alábbi paranccsal:

        azure config mode arm

3. Hozzon létre egy erőforráscsoportot. Ez az erőforráscsoport a HDInsight-fürtöt tartalmaz, és a kapcsolódó tárfiók.

        azure group create groupname location

    * Cserélje le `groupname` rendelkező egy egyedi nevet a csoport.

    * Cserélje le `location` az a földrajzi régiót, amelyet a csoport létrehozásához.

       Az érvényese helyek listáját, használja a `azure location list` parancsot, és használja a helyek egyikét a `Name` oszlop.

4. Hozzon létre egy tárfiókot. Ezt a tárfiókot az alapértelmezett tárolóként szolgál a HDInsight-fürthöz.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Cserélje le `groupname` nevű, a csoport az előző lépésben létrehozott.

    * Cserélje le `location` az ugyanazon a helyen az előző lépésben használt.

    * Cserélje le `storagename` a tárfiók egyedi névvel.

        > [!NOTE]
        > Ez a parancs paraméterei a további információért futtassa `azure storage account create -h` a parancs súgójának megtekintéséhez.

5. A tárfiók eléréséhez használt kulcs lekérését.

        azure storage account keys list -g groupname storagename

    * Cserélje le `groupname` és az erőforráscsoport neve.
    * Cserélje le `storagename` a tárfiók nevével.

     A visszaadott adatokat, mentse a `key` értékének `key1`.

6. HDInsight-fürtök létrehozása.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Cserélje le `groupname` és az erőforráscsoport neve.

    * Cserélje le `Hadoop` létrehozza a fürt típussal. Például `Hadoop`, `HBase`, `Kafka`, `Spark`, vagy `Storm`.

     > [!IMPORTANT]
     > HDInsight fürtök térjen különféle, a munkaterhelés vagy technológia, amely a fürt úgy van beállítva, a megfelelő. Nincs támogatott módszer, amely többféle, például a Storm és HBase egy fürtön fürt létrehozásához.

    * Cserélje le `location` az előző lépésben használt ugyanazon a helyen.

    * Cserélje le `storagename` fiók nevével.

    * Cserélje le `storagekey` az előző lépésben kapott a kulccsal.

    * Az a `--defaultStorageContainer` paraméter, a neve megegyezik, akkor a fürt használ.

    * Cserélje le `admin` és `httppassword` a nevét és a jelszavát szeretné használni, ha HTTPS keresztül fér hozzá a fürtöt.

    * Cserélje le `sshuser` és `sshuserpassword` a felhasználónévvel és SSH használ a fürt eléréséhez használandó jelszót

    > [!IMPORTANT]
    > Ebben a példában a fürt két munkavégző megjegyzéseket hoz létre. Skálázási műveletek elvégzésével fürt létrehozása után is módosíthatja a feldolgozó csomópontok száma. Ha több mint 32 munkavégző csomópontokhoz használatát tervezi, majd ki kell választania egy átjárócsomóponttal mérete legalább 8 maggal és 14 GB RAM. Beállíthatja az átjárócsomópont méret használatával a `--headNodeSize` paraméter fürt létrehozása során.
    >
    > A csomópont-méretek és a társuló költségeket további információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).

    A Fürtlétrehozási folyamat befejezéséhez több percig is eltarthat. Általában körülbelül 15.

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen létrehozta az Azure parancssori felület használatával HDInsight-fürtöt, használja a következő áttekintésével megismerheti, hogyan használható a fürt:

### <a name="hadoop-clusters"></a>Hadoop-fürtök

* [A Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hadoop/hdinsight-use-pig.md)
* [Use MapReduce with HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-fürtökkel

* [Az a HDInsight HBase első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [A HDInsight HBase Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-fürtök

* [Java-topológiák fejlesztése hdinsight alatt futó Storm](storm/apache-storm-develop-java-topology.md)
* [A HDInsight alatt futó Storm Python-összetevőket használnak](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight alatt futó Storm topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)
