---
title: 'Rövid útmutató: Apache HBase lekérdezése az Azure HDInsightban – HBase rendszerhéj'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Apache HBase rendszerhéjat az Apache HBase-lekérdezések futtatásához.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371069"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Rövid útmutató: Az Apache HBase lekérdezése az Azure HDInsightban a HBase rendszerhéjjal

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre HBase-táblát az Apache HBase rendszerhéj segítségével, hogyan szúrhat be adatokat, majd hogyan kérdezheti le a táblát.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Apache HBase fürt. A HDInsight-fürt létrehozásához lásd: [Fürt létrehozása.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)  Győződjön meg arról, hogy a **HBase** fürttípust választotta.

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="create-a-table-and-manipulate-data"></a>Táblázat létrehozása és adatok kezelése

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight Apache HBase táblázatos adatok](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

A HBase (végrehajtása [Cloud BigTable](https://cloud.google.com/bigtable/)), ugyanazokaz adatok néz ki:

![HDInsight Apache HBase BigTable adatok](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Az SSH segítségével csatlakozhat a HBase-fürtökhöz, majd az Apache HBase Shell segítségével HBase táblákat hozhat létre, adatokat szúrhat be és lekérdezési adatokat.

1. A `ssh` Parancs segítségével csatlakozzon a HBase fürthöz. Az alábbi parancs szerkesztése a fürt nevének cseréjével, `CLUSTERNAME` majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A `hbase shell` HBase interaktív rendszerhéj indításához használja a parancsot. Írja be a következő parancsot az SSH-kapcsolatba:

    ```bash
    hbase shell
    ```

3. A `create` parancs segítségével hozzon létre egy HBase táblát kétoszlopos családokkal. Írja be a következő parancsot:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. A `list` parancs segítségével a HBase összes tábláját listázhassa. Írja be a következő parancsot:

    ```hbase
    list
    ```

5. A `put` parancs segítségével értékeket szúrhat be egy adott tábla megadott sorának megadott oszlopába. Írja be a következő parancsot:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. A `scan` parancs segítségével beszkandelheti és visszaadhatja a `Contacts` táblaadatait. Írja be a következő parancsot:

    ```hbase
    scan 'Contacts'
    ```

7. A `get` parancs segítségével lekéri egy sor tartalmát. Írja be a következő parancsot:

    ```hbase
    get 'Contacts', '1000'
    ```

    Hasonló eredményeket lát, `scan` mint a parancs használata, mert csak egy sor van.

8. A `delete` parancs segítségével törölheti a táblázat cellaértékét. Írja be a következő parancsot:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. A `disable` táblázat letiltásához használja a parancsot. Írja be a következő parancsot:

    ```hbase
    disable 'Contacts'
    ```

10. A `drop` parancs segítségével eldobjon egy táblát a HBase parancsból. Írja be a következő parancsot:

    ```hbase
    drop 'Contacts'
    ```

11. A `exit` Parancs segítségével állítsa le a HBase interaktív rendszerhéjat. Írja be a következő parancsot:

    ```hbase
    exit
    ```

A HBase táblasémáról további információt az [Apache HBase sématervezés – bevezetés című témakörben talál.](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf) További Hbase-parancsokért lásd: [Apache HBase reference guide](https://hbase.apache.org/book.html#quickstart) (Apache HBase referencia-útmutató).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Az Apache HBase Rendszerhéjat egy HBase-tábla létrehozásához, az adatok beszúrásához, majd a tábla lekérdezéséhez. Ha többet szeretne megtudni a HBase-ben tárolt adatokról, a következő cikk bemutatja, hogyan hajthatja végre a lekérdezéseket az Apache Sparksegítségével.

> [!div class="nextstepaction"]
> [Az Apache Spark használata Apache HBase-adatok írására és olvasására](../hdinsight-using-spark-query-hbase.md)