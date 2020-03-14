---
title: 'Gyors útmutató: az Apache HBase lekérdezése az Azure HDInsight-HBase shellben'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható az Apache HBase-rendszerhéj az Apache HBase-lekérdezések futtatásához.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371069"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Gyors útmutató: az Apache HBase lekérdezése az Azure HDInsight az HBase Shellrel

Ebből a rövid útmutatóból megtudhatja, hogyan használható az Apache HBase shell egy HBase-tábla létrehozásához, az adatbeszúráshoz és a tábla lekérdezéséhez.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache HBase-fürt. Lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md) HDInsight-fürt létrehozásához.  Győződjön meg arról, hogy a **HBase** -fürt típusát választja.

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Tábla létrehozása és az adatkezelés

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight Apache HBase táblázatos adatokat](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

A HBase (a [Cloud BigTable](https://cloud.google.com/bigtable/)implementációja) ugyanazokat az adatmennyiségeket keresi:

![HDInsight Apache HBase BigTable-adatbázis](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

Az SSH-val HBase-fürtökhöz csatlakozhat, majd az Apache HBase-rendszerhéj használatával HBase-táblákat hozhat létre, és adatbeszúrási és Adatlekérdezési adatként is létrehozhat.

1. A HBase-fürthöz való kapcsolódáshoz használja a `ssh` parancsot. Szerkessze az alábbi parancsot úgy, hogy lecseréli `CLUSTERNAME` a fürt nevére, majd beírja a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. A HBase interaktív rendszerhéj elindításához használja a `hbase shell` parancsot. Adja meg az alábbi parancsot az SSH-kapcsolatban:

    ```bash
    hbase shell
    ```

3. A `create` parancs használatával hozzon létre egy HBase táblát kétoszlopos családokkal. Írja be a következő parancsot:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. `list` parancs használatával listázhatja az összes táblát a HBase. Írja be a következő parancsot:

    ```hbase
    list
    ```

5. A `put` parancs használatával szúrhat be értékeket egy adott tábla megadott sorában lévő adott oszlopba. Írja be a következő parancsot:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. `scan` parancs használatával ellenőrizze és visszaállíthatja az `Contacts` Table-táblát. Írja be a következő parancsot:

    ```hbase
    scan 'Contacts'
    ```

7. Egy sor tartalmának lekéréséhez használja a `get` parancsot. Írja be a következő parancsot:

    ```hbase
    get 'Contacts', '1000'
    ```

    A `scan` parancs használatával hasonló eredmények jelennek meg, mivel csak egy sor van.

8. `delete` parancs használatával törölheti a cella értékét egy táblában. Írja be a következő parancsot:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. A tábla letiltásához használja a `disable` parancsot. Írja be a következő parancsot:

    ```hbase
    disable 'Contacts'
    ```

10. `drop` parancs használatával elhúzhatja a táblákat a HBase. Írja be a következő parancsot:

    ```hbase
    drop 'Contacts'
    ```

11. `exit` parancs használatával állítsa le a HBase interaktív rendszerhéját. Írja be a következő parancsot:

    ```hbase
    exit
    ```

A HBase táblázatos sémával kapcsolatos további információkért lásd: [Bevezetés az Apache HBase Schema design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)használatába. További HBase-parancsokért lásd az [Apache HBase-referenciát](https://hbase.apache.org/book.html#quickstart)ismertető témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használható az Apache HBase shell egy HBase tábla létrehozásához, az adatbeszúráshoz és a tábla lekérdezéséhez. Ha többet szeretne megtudni a HBase-ben tárolt információkkal kapcsolatban, a következő cikk bemutatja, hogyan hajthat végre lekérdezéseket Apache Spark használatával.

> [!div class="nextstepaction"]
> [Apache HBase-információk olvasása és írása Apache Spark használatával](../hdinsight-using-spark-query-hbase.md)