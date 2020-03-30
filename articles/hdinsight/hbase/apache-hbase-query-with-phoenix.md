---
title: 'Rövid útmutató: Apache HBase & Apache Phoenix – Azure HDInsight'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Apache Phoenixet a HDInsightban. Azt is megtudhatja, hogyan telepítheti és állíthatja be az SQLLine-t a számítógépen a HDInsight HBase-fürthöz való csatlakozáshoz.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370321"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Rövid útmutató: Az Apache HBase lekérdezése az Azure HDInsightban az Apache Phoenix segítségével

Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Apache Phoenixet hbase-lekérdezések futtatásához az Azure HDInsightban. Az Apache Phoenix az Apache HBase SQL-lekérdezési motorja. JDBC-illesztőként érhető el, és lehetővé teszi a HBase táblák SQL eszközzel végzett lekérdezését és kezelését. [Az SQLLine](http://sqlline.sourceforge.net/) egy parancssori segédprogram az SQL végrehajtásához.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Apache HBase fürt. A HDInsight-fürt létrehozásához lásd: [Fürt létrehozása.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)  Győződjön meg arról, hogy a **HBase** fürttípust választotta.

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="identify-a-zookeeper-node"></a>ZooKeeper csomópont azonosítása

Amikor csatlakozik egy HBase-fürthöz, csatlakoznia kell az Apache ZooKeeper egyik csomópontjához. Minden HDInsight-fürt három ZooKeeper-csomódat rendelkezik. Curl segítségével gyorsan azonosítani a ZooKeeper csomópont. Az alábbi curl parancs `PASSWORD` szerkesztése a megfelelő értékek cseréjével és `CLUSTERNAME` kiírásával, majd írja be a parancsot a parancssorba:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

A kimenet egy része a következőhöz hasonló lesz:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Vegye figyelembe a `host_name` későbbi használatra szánt értéket.

## <a name="create-a-table-and-manipulate-data"></a>Táblázat létrehozása és adatok kezelése

Az SSH segítségével csatlakozhat a HBase-fürtökhöz, majd az Apache Phoenix segítségével HBase táblákat hozhat létre, adatokat szúrhat be és lekérdezési adatokat.

1. A `ssh` Parancs segítségével csatlakozzon a HBase fürthöz. Az alábbi parancs szerkesztése a fürt nevének cseréjével, `CLUSTERNAME` majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Címtár módosítása a Phoenix ügyfélre. Írja be a következő parancsot:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Indítsa el [az SQLLine programot.](http://sqlline.sourceforge.net/) Az alábbi parancs szerkesztése a korábban azonosított ZooKeeper csomóponttal való cserével, `ZOOKEEPER` majd írja be a parancsot:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Hozzon létre egy HBase táblát. Írja be a következő parancsot:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Az SQLLine `!tables` paranccsal a HBase összes tábláját listázhassa. Írja be a következő parancsot:

    ```sqlline
    !tables
    ```

6. Értékek beszúrása a táblázatba. Írja be a következő parancsot:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. A tábla lekérdezése. Írja be a következő parancsot:

    ```sql
    SELECT * FROM Company;
    ```

8. Rekord törlése. Írja be a következő parancsot:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Dobd el az asztalt. Írja be a következő parancsot:

    ```hbase
    DROP TABLE Company;
    ```

10. Az SQLLine `!quit` paranccsal lépjen ki az SQLLine programból. Írja be a következő parancsot:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az Apache Phoenixet hbase-lekérdezések futtatásához az Azure HDInsightban. Ha többet szeretne megtudni az Apache Phoenixről, a következő cikk mélyebb vizsgálatot biztosít.

> [!div class="nextstepaction"]
> [Apache Phoenix a HDInsightban](../hdinsight-phoenix-in-hdinsight.md)
