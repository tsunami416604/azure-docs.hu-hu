---
title: 'Rövid útmutató: Apache Hive az Azure HDInsightban az Apache Zeppelinnel'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Apache Zeppelint az Apache Hive-lekérdezések futtatásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 49b576fd511d17616880e5d981fd3f649de797df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79367924"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Rövid útmutató: Apache Hive-lekérdezések végrehajtása az Azure HDInsightban az Apache Zeppelinsegítségével

Ebben a rövid útmutatóban megtudhatja, hogyan futtathatja [az Apache Hive-lekérdezéseket](https://hive.apache.org/) az Azure HDInsightban az Apache Zeppelin használatával. A HDInsight interaktív lekérdezési fürtök [apache Zeppelin-jegyzetfüzeteket](https://zeppelin.apache.org/) tartalmaznak, amelyek segítségével interaktív Hive-lekérdezéseket futtathat.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

HDInsight interaktív lekérdezési fürt. A HDInsight-fürt létrehozásához lásd: [Fürt létrehozása.](../hadoop/apache-hadoop-linux-tutorial-get-started.md)  Győződjön meg arról, hogy az **Interaktív lekérdezés** fürttípusát választotta.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin note létrehozása

1. Cserélje `CLUSTERNAME` le a fürt nevét a `https://CLUSTERNAME.azurehdinsight.net/zeppelin`következő URL-címen . Ezután írja be az URL-címet egy webböngészőben.

2. Adja meg a fürt bejelentkezési felhasználónevét és jelszavát. A Zeppelin lapon létrehozhat egy új jegyzetet, vagy megnyithatja a meglévő jegyzeteket. **HiveSample** tartalmaz néhány minta Hive-lekérdezések.  

    ![HDInsight interaktív lekérdezés zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Válassza **az Új jegyzet létrehozása**lehetőséget.

4. Az **Új jegyzet létrehozása** párbeszédpanelen írja be vagy jelölje ki a következő értékeket:

    - Megjegyzés neve: Adja meg a jegyzet nevét.
    - Alapértelmezett értelmező: Válassza ki a **jdbc** elemet a legördülő listából.

5. Válassza **a Feljegyzés létrehozása lehetőséget.**

6. Írja be a következő Hive-lekérdezést a kódszakaszba, majd nyomja le a **Shift + Enter billentyűkombinációt:**

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![A HDInsight Interaktív lekérdezészeppelin lekérdezést futtat](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Az első sorban lévő **%jdbc(hive)** utasítás arra utasítja a jegyzetfüzetet, hogy használja a Hive JDBC értelmezőt.

    A lekérdezés egy Hive-táblát, **a hivesampletable-t**adja vissza.

    Az alábbiakban két további Hive-lekérdezést, amelyek futtathatók **hivesampletable:**

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Összehasonlítva a hagyományos Hive, a lekérdezés iab-eredmények gyorsabbnak kell lennie.

### <a name="additional-examples"></a>További példák

1. Hozzon létre egy táblázatot. Hajtsa végre az alábbi kódot a Zeppelin notebookban:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Adatok betöltése az új táblába. Hajtsa végre az alábbi kódot a Zeppelin notebookban:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Egyetlen rekord beszúrása. Hajtsa végre az alábbi kódot a Zeppelin notebookban:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Tekintse át a [Hive nyelvi kézikönyv](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) további szintaxis.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az Apache Zeppelint az Apache Hive-lekérdezések futtatásához az Azure HDInsightban. Ha többet szeretne megtudni a Hive-lekérdezésekről, a következő cikk bemutatja, hogyan hajthatja végre a lekérdezéseket a Visual Studióval.

> [!div class="nextstepaction"]
> [Csatlakozás az Azure HDInsighthoz, és Apache Hive-lekérdezések futtatása a Data Lake-eszközök a Visual Studio számára használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
