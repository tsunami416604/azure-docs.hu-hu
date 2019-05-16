---
title: 'Gyors útmutató: Lekérdezés az Apache HBase az Azure HDInsight - HBase-rendszerhéj'
description: Ismerje meg, az Apache HBase-rendszerhéj használata az Apache HBase-lekérdezések futtatásához.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: hrasheed
ms.openlocfilehash: 41b16e63522a02cc16eb4dac2cbcc8e6540aceaf
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65552085"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Gyors útmutató: Lekérdezés Apache HBase az Azure HDInsight-HBase-rendszerhéj

Ez a rövid útmutatóban megismerheti, hogyan Apache HBase-rendszerhéj használata egy HBase tábla létrehozásához, és adatok beszúrása, majd lekérdezheti a tábla.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az Apache HBase-fürtöt. Lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) hozhat létre HDInsight-fürtöt.  Győződjön meg arról, válassza ki a **HBase** fürt típusa.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Hozzon létre egy táblát, és az adatok kezelése

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight HBase táblázatos adatok](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

A HBase (megvalósítását [felhőalapú BigTable](https://cloud.google.com/bigtable/)), ugyanazokat az adatokat a következőhöz hasonló:

![HDInsight HBase BigTable-adatok](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

SSH-val HBase-fürtökhöz csatlakozhat, és majd a Apache HBase-rendszerhéjjal HBase-táblákat hozhat létre, helyezze be az adatokat, és adatokat kérdezhet le.

1. Használat `ssh` paranccsal csatlakozhat a HBase-fürtöt. Az alábbi parancsot szerkesztése lecserélésével `CLUSTERNAME` a fürt nevét és adja meg a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Használat `hbase shell` parancsot a HBase interaktív shell elindításához. Adja meg az SSH-kapcsolatot a következő parancsot:

    ```bash
    hbase shell
    ```

3. Használat `create` egy HBase tábla létrehozásához két oszlopcsaláddal parancsot. Írja be a következő parancsot:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Használat `list` parancs minden, a HBase táblák listázásához. Írja be a következő parancsot:

    ```hbase
    list
    ```

5. Használat `put` értékek beszúrása egy megadott oszlop, egy adott tábla egy megadott sorának a következő parancsot. Írja be a következő parancsot:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Használati `scan` parancs vizsgálata, és adja vissza a `Contacts` tábla adatait. Írja be a következő parancsot:

    ```hbase
    scan 'Contacts'
    ```

7. Használat `get` parancsot egy sor tartalmának beolvasása. Írja be a következő parancsot:

    ```hbase
    get 'Contacts', '1000'
    ```

    Hasonló eredményeket használja, tekintse meg a `scan` parancsot, mert csak egy sor van.

8. Használat `delete` parancsot egy táblázatban egy cella értékét törléséhez. Írja be a következő parancsot:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Használat `disable` paranccsal tiltsa le a táblázatban. Írja be a következő parancsot:

    ```hbase
    disable 'Contacts'
    ```

10. Használat `drop` dobja el a táblát a HBase parancsot. Írja be a következő parancsot:

    ```hbase
    drop 'Contacts'
    ```

11. Használat `exit` parancs használatával állítsa le a HBase interaktív kezelőfelület. Írja be a következő parancsot:

    ```hbase
    exit
    ```

A HBase táblasémáról további információért lásd: [Bevezetés az Apache HBase Schema Design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). További HBase parancsokért lásd: [Apache HBase referencia-útmutató](https://hbase.apache.org/book.html#quickstart).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan Apache HBase-rendszerhéj használata egy HBase tábla létrehozásához, és adatok beszúrása, majd lekérdezheti a tábla. A HBase-ban tárolt adatokkal kapcsolatos további információkért a következő cikk bemutatja, hogyan hajthat végre az Apache Spark-lekérdezéseket.

> [!div class="nextstepaction"]
> [Az Apache HBase-adatok olvasását és írását az Apache Spark használata](../hdinsight-using-spark-query-hbase.md)