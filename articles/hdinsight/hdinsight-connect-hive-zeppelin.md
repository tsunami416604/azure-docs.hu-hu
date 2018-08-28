---
title: Az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használata
description: Útmutató a Hive-lekérdezések futtatása a Zeppelin használatával.
keywords: hdinsight, hadoop, hive, LLAP interaktív lekérdezés
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jasonh
ms.openlocfilehash: 3064c9cd141458307891f666bd5af9aa738cc021
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43093168"
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használata 

HDInsight interaktív lekérdezési fürtökhöz interaktív Hive-lekérdezések futtatásához használhatja Zeppelin-jegyzetfüzetek tartalmazza. Ebből a cikkből elsajátíthatja az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használatával. 

## <a name="prerequisites"></a>Előfeltételek
Mielőtt végrehajtaná ezt a cikket, a következőkkel kell rendelkeznie:

* **HDInsight interaktív lekérdezési fürt**. Lásd: [fürt létrehozása](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) egy HDInsight-fürt létrehozásához.  Ellenőrizze, hogy válassza ki az interaktív lekérdezés. 

## <a name="create-a-zeppelin-note"></a>A Zeppelin Megjegyzés létrehozása

1. Keresse meg a következő URL-címe:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Cserélje le a **CLUSTERNAME** elemet a fürt nevére.

2. Adja meg a Hadoop-felhasználónevet és jelszót. A Zeppelin lapon hozzon létre egy új megjegyzés, vagy nyissa meg a meglévő megjegyzéseket. HiveSample bizonyos Hive-mintalekérdezések tartalmazza.  

    ![HDInsight interaktív lekérdezés zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Kattintson a **létrehozása új feljegyzés**.
4. Írja be vagy válassza ki az alábbi értékeket:

    - Megjegyzés: név: Adja meg a jegyzet nevét.
    - Alapértelmezett interpretu: válasszon **JDBC**.

5. Kattintson a **jegyzet létrehozása**.
6. A következő Hive-lekérdezések futtatásához:

        %jdbc(hive)
        show tables

    ![HDInsight interaktív lekérdezés zeppelin lekérdezés futtatása](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    A **%jdbc(hive)** első sorában utasítás arra utasítja a notebook használata a Hive JDBC értelmezője számára készült.

    A lekérdezés egy Hive-tábla neve visszaküldik *hivesampletable*.

    A következőkben két további Hive-lekérdezések, amelyek a hivesampletable vonatkozóan futtathat le. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Mint a hagyományos Hive, a lekérdezés eredményeinek térjen vissza kell gyorsabban.


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan HDInsight használata a Power BI adatok vizualizálásához.  További tudnivalókért tekintse meg a következő cikkeket:

* [A Microsoft Power BI az Azure HDInsight Hive-adatok vizualizálása](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC illesztőprogram segítségével a HDInsight](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel összekapcsolása a Hadooppal a Power Query használatával](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával Hive-lekérdezések futtatása](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./hdinsight-upload-data.md).
