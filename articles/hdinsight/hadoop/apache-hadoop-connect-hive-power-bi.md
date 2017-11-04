---
title: "Big-adatok ábrázolása a Power bi-ban az Azure HDInsight |} Microsoft Docs"
description: "Megtudhatja, hogyan dolgozza fel az Azure HDInsight Hive adatok megjelenítése a Microsoft Power BI használatával."
keywords: "hdinsight hadoop, struktúra, interaktív lekérdezéséhez interaktív struktúra, LLAP, odbc"
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
ms.openlocfilehash: 390342eb08ae970fa760b414674b1a6783404d80
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="visualize-hive-data-with-microsoft-power-bi-in-azure-hdinsight"></a>A Microsoft Power BI Azure hdinsight Hive-adatok ábrázolása

Útmutató a Microsoft Power BI összekapcsolása Azure HDInsight és a Hive-adatok ábrázolása. Power BI jelenleg csak HDInsight ODBC kapcsolatot támogat. Ebben az oktatóanyagban betölteni az adatokat egy hivesampletable Hive tábla a Power bi-bA. A Hive tábla néhány mobiltelefon használati adatokat tartalmaz. Ezután a használati adatok egy globális térképen rajzolása:

![HDInsight a Power BI a térkép jelentés](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Az információk az új [interaktív lekérdezés](../interactive-query/apache-interactive-query-get-started.md) fürt típusa.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk keresztül haladó, mielőtt a következő elemeket kell rendelkeznie:

* **HDInsight-fürt**. A fürt vagy egy HDInsight-fürtöt a Hive, vagy egy újonnan kiadott interaktív lekérdezés fürt is lehet. Fürtök létrehozására, lásd: [fürt létrehozása](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktopban](https://powerbi.microsoft.com/desktop/)**. Egy másolatot a programot letöltheti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Lásd: [létrehozása Hive ODBC-adatforrás](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsight-ból

HDInsight-fürtök esetében a hivesampletable Hive táblát tartalmaz.

1. Jelentkezzen be a Power BI Desktopban.
2. Kattintson a **Home** lapra, majd **adatok beolvasása** a a **külső adatok** menüszalag, majd válassza ki **további**.

    ![Nyissa meg a HDInsight Power BI-adatokhoz](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. A a **adatok beolvasása** ablaktáblában kattintson **más** bal oldalán kattintson **ODBC** jobbról, és kattintson a **Connect** alján.
4. Az a **az ODBC** panelen válassza ki az adatforrást az utolsó szakaszban létrehozott név, és kattintson a **OK**.
5. Az a **Navigator** ablaktáblán bontsa ki a **ODBC -> HIVE -> alapértelmezett**, jelölje be **hivesampletable**, és kattintson a **terhelés**.

## <a name="visualize-date"></a>Dátum megjelenítése

Továbbra is az előző eljárást.

1. A képi megjelenítések ablaktáblában jelölje ki a **térkép**.  Egy földgömb ikon.

    ![HDInsight a Power BI jelentés testreszabása](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. A mezők ablaktáblában jelölje ki **ország** és **devicemake**. Az adatok a térképen ábrázolt tekintheti meg.
3. Bontsa ki a térképen.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megismerte a HDInsight a Power BI-adatok ábrázolása.  További tudnivalókért tekintse meg a következő cikkeket:

* [Zeppelin segítségével az Azure HDInsight Hive-lekérdezések futtatása ](./../hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC-illesztőprogram HDInsight](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel és a Hadoop csatlakoztatása a Power Query használatával](apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight csatlakozzon és Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával futtassa](apache-hadoop-visual-studio-tools-get-started.md).
* [A HDInsight eszközzel Azure a Visual Studio Code](../hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./../hdinsight-upload-data.md).
