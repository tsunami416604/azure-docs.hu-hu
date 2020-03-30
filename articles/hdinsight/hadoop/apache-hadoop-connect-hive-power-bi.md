---
title: Az Apache Hive-adatok megjelenítése a Power BI-val – Azure HDInsight
description: Megtudhatja, hogy miként jelenítheti meg az Azure HDInsight által feldolgozott Hive-adatok at a Microsoft Power BI használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/02/2020
ms.openlocfilehash: d9b64785dbd82842479eb3f313b8394f9f25b40b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369998"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Apache Hive-adatok vizualizálása a Microsoft Power BI-jal és ODBC-vel az Azure HDInsightban

Megtudhatja, hogy miként csatlakoztathatja a Microsoft Power BI Desktopot az Azure HDInsighthoz az ODBC használatával, és hogyan jelenítheti meg az Apache Hive-adatokat.

> [!IMPORTANT]
> A Hive ODBC illesztőprogramsegítségével a Power BI Desktop általános ODBC-csatlakozóján keresztül hozhat importálásra. Azonban nem ajánlott a Bi számítási feladatok, mivel a Hive-lekérdezési motor nem interaktív jellege. [A HDInsight interaktív lekérdezésösszekötő](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) és a [HDInsight Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jobb választás a teljesítményükhöz.

Ebben a cikkben betölti `hivesampletable` az adatokat egy Hive-táblából a Power BI-ba. A Hive-tábla tartalmaz néhány mobiltelefon-használati adatot. Ezután ábrázolja a használati adatokat egy világtérképen:

![HDInsight Power BI a térképjelentés](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Az információ az új [interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürttípusra is vonatkozik. A HDInsight Interaktív lekérdezéshez közvetlen lekérdezéssel való csatlakozásról a [Visualize Interactive Query Hive-adatok Microsoft Power BI-val című témakörben, közvetlen lekérdezéssel az Azure HDInsight ban című témakörben található.](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt átnézi ezt a cikket, rendelkeznie kell a következő elemekkel:

* HDInsight-fürt. A fürt lehet hive-vel rendelkező HDInsight-fürt vagy egy újonnan kiadott interaktív lekérdezési fürt. A fürtök létrehozásáról a [Fürt létrehozása című](apache-hadoop-linux-tutorial-get-started.md)témakörben látható.

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Egy példányt a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45331)tölthet le.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Lásd: [Hive ODBC adatforrás létrehozása](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsightból

A **hivesampletable** Hive tábla az összes HDInsight-fürthöz tartozik.

1. Indítsa el a Power BI Desktopot.

1. A felső menüben keresse meg a **Home** > **Get Data** > **More lapot...**.

    ![HDInsight Excel Power BI – megnyitott adatok](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Az **Adatok bekerülése** párbeszédpanelen válassza a bal oldalon az **Egyéb** lehetőséget, válassza a jobb oldali **ODBC** lehetőséget, majd alul válassza a **Csatlakozás** lehetőséget.

1. Az **ODBC-ből** párbeszédpanelen válassza ki az utolsó szakaszban létrehozott adatforrásnevet a legördülő listából. Ezután kattintson az **OK** gombra.

1. Az első használathoz megnyílik egy **ODBC-illesztőprogram-párbeszédpanel.** Válassza a bal oldali menü **Alapértelmezett vagy Egyéni** parancsát. Ezután a **Csatlakozás gombra a Navigátor**megnyitásához válassza a **Csatlakozás** lehetőséget.

1. A **Navigátor** párbeszédpanelen bontsa ki az **ODBC > A HIVE > alapértelmezett ,** válassza **a hivesampletable**elemet, majd válassza **a Betöltés**lehetőséget.

## <a name="visualize-data"></a>Adatok vizualizációja

Folytassa az utolsó eljárástól.

1. A Vizualizációk ablaktáblán válassza a **Térkép**lehetőséget, ez egy földgömb ikon.

    ![A HDInsight Power BI testre szabhatja a jelentést](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. A **Mezők** ablaktáblán válassza az **ország** és **az eszközmake**lehetőséget. Láthatja a térképen ábrázolt adatokat.

1. Bontsa ki a térképet.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtudhatja, hogyan jelenítheti meg az adatokat a HDInsightból a Power BI használatával.  További információ: a következő cikkek:

* [Az Apache Zeppelin segítségével apache hive-lekérdezéseket futtataz az Azure HDInsightban.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Az Excel csatlakoztatása a HDInsighthoz a Microsoft Hive ODBC illesztőprogrammal](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel csatlakoztatása az Apache Hadoophoz a Power Query használatával.](apache-hadoop-connect-excel-power-query.md)
* [Csatlakozzon az Azure HDInsighthoz, és futtasson Apache Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával.](apache-hadoop-visual-studio-tools-get-started.md)
* [Használja az Azure HDInsight eszközt a Visual Studio-kódhoz.](../hdinsight-for-vscode.md)
* [Adatok feltöltése a HDInsight ba.](./../hdinsight-upload-data.md)
