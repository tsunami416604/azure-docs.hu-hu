---
title: 'Gyors útmutató: Lekérdezés Apache HBase az Azure HDInsight – Apache Phoenixhez'
description: Ebben a rövid útmutatóban elsajátíthatja az Apache Phoenix használata a HDInsight. Emellett ismerje meg, telepítése és beállítása az SQLLine a számítógépen szeretne csatlakozni a HDInsight HBase-fürtöt.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 20af6d32d03ae5d4fe37b1a37198ef1f2c50ec95
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137421"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Gyors útmutató: Lekérdezés az Apache HBase az Azure HDInsight az Apache Phoenixhez

Ez a rövid útmutatóban megismerheti, hogyan az Apache Phoenix használata az Azure HDInsight HBase-lekérdezések futtatásához. Az Apache Phoenix az Apache hbase SQL lekérdezési motorja. JDBC-illesztőként érhető el, és lehetővé teszi a HBase táblák SQL eszközzel végzett lekérdezését és kezelését. [Az SQLLine](http://sqlline.sourceforge.net/) parancssori segédprogram az SQL végrehajtásához.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az Apache HBase-fürtöt. Lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) hozhat létre HDInsight-fürtöt.  Győződjön meg arról, válassza ki a **HBase** fürt típusa.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>A ZooKeeper-csomópont azonosítása

HBase-fürt való csatlakozáskor kell csatlakoznia, az Apache ZooKeeper-csomópontok egyikére. Minden egyes HDInsight-fürt három ZooKeeper-csomóponttal rendelkezik. A curl segítségével gyorsan azonosíthatja a ZooKeeper-csomópont. Az alábbi curl-parancsot szerkesztése lecserélésével `PASSWORD` és `CLUSTERNAME` a megfelelő értékekkel, és adja meg a parancsot a parancssorba:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

A kimenet egy része hasonlóan néz ki:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Jegyezze fel az értéket `host_name` későbbi használatra.

## <a name="create-a-table-and-manipulate-data"></a>Hozzon létre egy táblát, és az adatok kezelése

SSH-val HBase-fürtökhöz csatlakozhat, és ezután használja az Apache Phoenix HBase táblákat hozhat létre, helyezze be az adatokat, és adatokat kérdezhet le.

1. Használat `ssh` paranccsal csatlakozhat a HBase-fürtöt. Az alábbi parancsot szerkesztése lecserélésével `CLUSTERNAME` a fürt nevét és adja meg a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Módosítsa a könyvtárat a Phoenix-ügyfél. Írja be a következő parancsot:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Indítsa el a [az SQLLine](http://sqlline.sourceforge.net/). Az alábbi parancsot szerkesztése lecserélésével `ZOOKEEPER` a korábban azonosított ZooKeeper-csomóponttal, majd adja meg a parancsot:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Egy HBase tábla létrehozásához. Írja be a következő parancsot:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Az SQLLine használata `!tables` parancs minden, a HBase táblák listázásához. Írja be a következő parancsot:

    ```sqlline
    !tables
    ```

6. Értékek a táblázat beillesztése. Írja be a következő parancsot:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. A lekérdezés a táblában. Írja be a következő parancsot:

    ```sql
    SELECT * FROM Company;
    ```

8. Rekord törlése. Írja be a következő parancsot:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Dobja el a táblát. Írja be a következő parancsot:

    ```hbase
    DROP TABLE Company;
    ```

10. Az SQLLine használata `!quit` az SQLLine exit parancsot. Írja be a következő parancsot:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan az Apache Phoenix használata az Azure HDInsight HBase-lekérdezések futtatásához. Az Apache Phoenix kapcsolatos további információkért a következő cikk nyújt részletesebb vizsgálata.

> [!div class="nextstepaction"]
> [A HDInsight Apache Phoenixhez](../hdinsight-phoenix-in-hdinsight.md)
