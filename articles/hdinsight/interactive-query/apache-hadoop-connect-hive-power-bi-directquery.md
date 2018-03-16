---
title: "Interaktív lekérdezés Hive-adatok ábrázolása a Power bi-ban az Azure HDInsight |} Microsoft Docs"
description: "Megtudhatja, hogyan dolgozza fel az Azure HDInsight Hive lekérdezés interaktív adatok megjelenítése a Microsoft Power BI használatával."
keywords: "hdinsight hadoop, struktúra, interaktív lekérdezéséhez interaktív struktúra, LLAP, directquery"
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
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: c74e02ea32a5aacf1a5208c0e194a25b0b6bcb48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>A Microsoft Power BI közvetlen lekérdezéssel Azure hdinsight Hive lekérdezés interaktív adatok megjelenítése

Útmutató a Microsoft Power BI összekapcsolása Azure HDInsight interaktív lekérdezés fürtök és közvetlen lekérdezéssel Hive adatok megjelenítéséhez. Ebben az oktatóanyagban betölteni az adatokat egy hivesampletable Hive tábla a Power bi-bA. A Hive tábla néhány mobiltelefon használati adatokat tartalmaz. Ezután a használati adatok egy globális térképen rajzolása:

![HDInsight a Power BI a térkép jelentés](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Kihasználhatja a [Hive ODBC-illesztőprogram](../hadoop/apache-hadoop-connect-hive-power-bi.md) a Power BI Desktop az általános ODBC-összekötőn keresztül importálandó. Azonban nem ajánlott a Hive lekérdezési motor nem interaktív jellegéből BI munkaterhelésekhez. [HDInsight interaktív lekérdezés összekötő](./apache-hadoop-connect-hive-power-bi-directquery.md) és [HDInsight Spark összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) a teljesítményük jobb döntéseket.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk keresztül haladó, mielőtt a következő elemeket kell rendelkeznie:

* **HDInsight-fürt**. A fürt vagy a Hive HDInsight-fürtöt, vagy egy újonnan kiadott interaktív lekérdezés fürt is lehet. Fürtök létrehozására, lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktopban](https://powerbi.microsoft.com/desktop/)**. Egy másolatot a programot letöltheti a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsight-ból

HDInsight-fürtök esetében a hivesampletable Hive táblát tartalmaz.

1. Jelentkezzen be a Power BI Desktopban.
2. Kattintson a **Home** lapra, majd **adatok beolvasása** a a **külső adatok** menüszalag, majd válassza ki **további**.

    ![Nyissa meg a HDInsight Power BI-adatokhoz](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Az a **adatok beolvasása** ablak, írja be **hdinsight** be a keresőmezőbe. Ha nem lát **HDInsight interaktív lekérdezés (béta)**, frissítenie kell a Power BI Desktop a legújabb verzióra.
4. Kattintson a **HDInsight interaktív lekérdezés (béta)**, és kattintson a **Connect**.
5. Kattintson a **Folytatás** bezárásához a **Preview összekötő** figyelmeztető párbeszédpanel.
6. A **HDInsight interaktív lekérdezés**, válassza ki vagy adja meg a következő adatokat:

    - **Kiszolgáló**: Adja meg az interaktív lekérdezés fürt nevét, például *myiqcluster.azurehdinsight.net*.
    - **Adatbázis**: A jelen oktatóanyag esetében adja meg a **alapértelmezett**.
    - **Adatok csatlakozási mód**: A jelen oktatóanyag esetében válassza ki a **DirectQuery**.

    ![HDInsight interaktív lekérdezés power üzleti intelligencia directquery-csatlakozás](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Kattintson az **OK** gombra.
8. Adja meg a HTTP-felhasználó hitelesítő adatai, és kattintson **OK**.  Az alapértelmezett felhasználónév az **rendszergazda**
9. A bal oldali ablaktáblán válassza ki a **hivesampletale**, és kattintson a **terhelés**.

    ![HDInsight interaktív lekérdezés power üzleti intelligencia hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Adatok megjelenítése

Továbbra is az előző eljárást.

1. A képi megjelenítések ablaktáblában jelölje ki a **térkép**.  Egy földgömb ikon.

    ![HDInsight a Power BI jelentés testreszabása](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. A mezők ablaktáblában jelölje ki **ország** és **devicemake**. Az adatok a térképen ábrázolt tekintheti meg.
3. Bontsa ki a térképen.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerte a HDInsight a Power BI-adatok ábrázolása.  További tudnivalókért tekintse meg a következő cikkeket:

* [A Microsoft Power BI ODBC használatával az Azure HDInsight Hive adato](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Zeppelin segítségével az Azure HDInsight Hive-lekérdezések futtatása](./../hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC-illesztőprogram HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel és a Hadoop csatlakoztatása a Power Query használatával](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight csatlakozzon és Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával futtassa](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [A HDInsight eszközzel Azure a Visual Studio Code](../hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./../hdinsight-upload-data.md).
