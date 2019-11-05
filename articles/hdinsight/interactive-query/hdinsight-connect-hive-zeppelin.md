---
title: 'Gyors útmutató: az Azure HDInsight Apache Hive az Apache Zeppelin-vel'
description: Ebből a rövid útmutatóból megtudhatja, hogyan futtathat Apache Hive lekérdezéseket az Apache Zeppelin használatával.
keywords: hdinsight, Hadoop, struktúra, interaktív lekérdezés, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 36d9e9b34deb4bc6cd5f599cfe2d09a12f680730
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494284"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Gyors útmutató: Apache Hive lekérdezések végrehajtása az Azure HDInsight az Apache Zeppelin-vel

Ebből a rövid útmutatóból megtudhatja, hogyan futtathat [Apache Hive](https://hive.apache.org/) -lekérdezéseket az Azure HDInsight-ben az Apache Zeppelin használatával. A HDInsight interaktív lekérdezési fürtök olyan [Apache Zeppelin](https://zeppelin.apache.org/) -jegyzetfüzeteket tartalmaznak, amelyekkel interaktív struktúra-lekérdezéseket futtathat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

An méretű HDInsight interaktív lekérdezési fürt. Lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) HDInsight-fürt létrehozásához.  Győződjön meg arról, hogy az **interaktív lekérdezési** fürt típusa van kiválasztva.

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin-Megjegyzés létrehozása

1. Cserélje le a `CLUSTERNAME`t a fürt nevére a következő URL-`https://CLUSTERNAME.azurehdinsight.net/zeppelin`ban. Ezután írja be az URL-címet egy böngészőben.

2. Adja meg a fürt bejelentkezési felhasználónevét és jelszavát. A Zeppelin lapon létrehozhat egy új megjegyzést, vagy megnyithatja a meglévő megjegyzéseket. A **HiveSample** tartalmaz néhány minta kaptár-lekérdezést.  

    ![HDInsight interaktív lekérdezés Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Válassza az **új Megjegyzés létrehozása**lehetőséget.

4. Az **új Megjegyzés létrehozása** párbeszédpanelen írja be vagy válassza ki a következő értékeket:

    - Megjegyzés neve: adja meg a jegyzet nevét.
    - Alapértelmezett értelmező: válassza a **JDBC** elemet a legördülő listából.

5. Válassza a **Létrehozás Megjegyzés**lehetőséget.

6. Adja meg a következő kaptár-lekérdezést a Code (kód) szakaszban, majd nyomja le a **SHIFT + ENTER**billentyűkombinációt:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![A HDInsight interaktív lekérdezési Zeppelin lekérdezéseket futtat](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Az első sorban található **% JDBC (kaptár)** utasítás azt jelzi, hogy a jegyzetfüzet a kaptár JDBC-értelmező használatát mutatja be.

    A lekérdezés egy **hivesampletable**nevű struktúrás táblát ad vissza.

    A következő két további, a **hivesampletable**-ben futtatott kaptár-lekérdezést hajt végre:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    A hagyományos struktúrához képest a lekérdezés eredményeinek gyorsabban kell visszatérniük.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan futtathat Apache Hive lekérdezéseket az Azure HDInsight-ben az Apache Zeppelin használatával. A kaptár-lekérdezésekkel kapcsolatos további tudnivalókért a következő cikk bemutatja, hogyan hajthat végre lekérdezéseket a Visual Studióval.

> [!div class="nextstepaction"]
> [Kapcsolódás az Azure HDInsight és Apache Hive-lekérdezések futtatása a Visual studióhoz készült Data Lake Tools használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
