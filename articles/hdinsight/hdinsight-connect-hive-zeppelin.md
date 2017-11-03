---
title: "Zeppelin segítségével az Azure HDInsight Hive-lekérdezések futtatása |} Microsoft Docs"
description: "Ismerje meg, hogyan futtathat Hive-lekérdezéseket Zeppelin segítségével."
keywords: "hdinsight, hadoop, struktúra, interaktív lekérdezés, LLAP"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: b44321619f2aa94a6d98624ab1ee35a598fb6fc8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Zeppelin használja az Azure HDInsight Hive-lekérdezések futtatásához 

Interaktív lekérdezés HDInsight fürtök Zeppelin notebookok Hive interaktív lekérdezések futtatására használható. Ebből a cikkből megismerheti, hogyan Zeppelin használata az Azure HDInsight Hive-lekérdezések futtatásához. 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk keresztül haladó, mielőtt a következő elemeket kell rendelkeznie:

* **Interaktív lekérdezés HDInsight fürt**. Lásd: [fürt létrehozása](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster) egy HDInsight-fürt létrehozásához.  Ügyeljen arra, hogy válassza ki az interaktív lekérdezési módot. 

## <a name="create-a-zeppelin-note"></a>Zeppelin jegyzet létrehozása

1. Keresse meg a következő URL-címe:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Cserélje le a **CLUSTERNAME** elemet a fürt nevére.

2. Adja meg a Hadoop-felhasználónevet és jelszót. Zeppelin oldalon új jegyzet létrehozása, vagy nyissa meg a meglévő megjegyzések. HiveSample néhány minta Hive-lekérdezéseket tartalmaz.  

    ![HDInsight interaktív lekérdezés zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Kattintson a **új jegyzet létrehozása**.
4. Írja be vagy válassza ki az alábbi értékeket:

    - Megjegyzés: név: Adjon meg egy nevet a megjegyzés.
    - Alapértelmezett parancsértelmező: válasszon **JDBC**.

5. Kattintson a **jegyzet létrehozása**.
6. A következő Hive-lekérdezések futtatása:

        %jdbc(hive)
        show tables

    ![HDInsight interaktív lekérdezés zeppelin lekérdezés futtatása](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    A **%jdbc(hive)** utasítás az első sorban közli a notebook használatához a Hive JDBC értelmező.

    A lekérdezés kell visszaadni egy Hive táblát *hivesampletable*.

    A következőkben két további Hive-lekérdezések, amelyek a hivesampletable is futtathatók. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Összehasonlítja a hagyományos struktúra, a lekérdezési eredmények térjen vissza kell gyorsabb.


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megismerte a HDInsight a Power BI-adatok ábrázolása.  További tudnivalókért tekintse meg a következő cikkeket:

* [Hive-adatok ábrázolása a Microsoft Power bi-ban az Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC-illesztőprogram HDInsight](./hdinsight-connect-excel-hive-odbc-driver.md).
* [Az Excel és a Hadoop csatlakoztatása a Power Query használatával](./hdinsight-connect-excel-power-query.md).
* [Azure HDInsight csatlakozzon és Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával futtassa](./hdinsight-hadoop-visual-studio-tools-get-started.md).
* [A HDInsight eszközzel Azure a Visual Studio Code](hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./hdinsight-upload-data.md).
