---
title: Interaktív lekérdezésstruktúra-adatok megtekintése a Power BI szolgáltatással az Azure HDInsightban
description: Interaktív lekérdezésstruktúra-adatok megjelenítése a Microsoft Power BI segítségével az Azure HDInsightból
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367992"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Interaktív lekérdezés Apache Hive-adatainak megjelenítése a Microsoft Power BI-val a HDInsight közvetlen lekérdezésével

Ez a cikk azt ismerteti, hogyan kapcsolhatja össze a Microsoft Power BI-t az Azure HDInsight interaktív lekérdezési fürtökkel, és hogyan jelenítheti meg az Apache Hive-adatokat közvetlen lekérdezéssel. A példa biztosított betölti `hivesampletable` az adatokat egy Hive-tábla a Power BI. A `hivesampletable` Hive-tábla tartalmaz néhány mobiltelefon-használati adatot. Ezután ábrázolja a használati adatokat egy világtérképen:

![HDInsight Power BI a térképjelentés](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Az Apache [Hive ODBC illesztőprogramsegítségével](../hadoop/apache-hadoop-connect-hive-power-bi.md) a Power BI Desktop általános ODBC-csatlakozóján keresztül importálhat. Azonban nem ajánlott a Bi számítási feladatok, mivel a Hive-lekérdezési motor nem interaktív jellege. [A HDInsight Interactive Query összekötő](./apache-hadoop-connect-hive-power-bi-directquery.md) és a [HDInsight Apache Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jobb választás a teljesítményükhöz.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt átnézi ezt a cikket, rendelkeznie kell a következő elemekkel:

* **HDInsight-fürt**. A fürt lehet egy HDInsight-fürt Apache Hive-vel vagy egy újonnan kiadott interaktív lekérdezési fürt. A fürtök létrehozásáról a [Fürt létrehozása című](../hadoop/apache-hadoop-linux-tutorial-get-started.md)témakörben látható.
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Egy példányt a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45331)tölthet le.

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsightból

A `hivesampletable` Hive-tábla az összes HDInsight-fürthöz tartozik.

1. Indítsa el a Power BI Desktopot.

2. A menüsorból keresse meg a **Home** > **Get Data** > **More lapot...**.

    ![HDInsight Power BI További adatok beszereznie](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Az **Adatok bekerülése** ablakban írja be a **hdinsight kifejezést** a keresőmezőbe.  

4. A keresési eredmények között válassza a **HDInsight interaktív lekérdezés**lehetőséget, majd a **Csatlakozás**lehetőséget.  Ha nem látja a **HDInsight Interaktív lekérdezést,** frissítenie kell a Power BI Desktopot a legújabb verzióra.

5. Válassza a **Folytatás** lehetőséget a Csatlakozás külső szolgáltatáshoz párbeszédpanel **bezárásához.**

6. A **HDInsight interaktív lekérdezése** ablakban adja meg a következő adatokat, majd kattintson az **OK gombra:**

    |Tulajdonság | Érték |
    |---|---|
    |Kiszolgáló |Írja be a fürt nevét, például *myiqcluster.azurehdinsight.net*.|
    |Adatbázis |Adja meg a cikk **alapértelmezett** megadását.|
    |Adatkapcsolati mód |A cikkhez válassza a **DirectQuery** elemet.|

    ![HDInsight interaktív lekérdezés Power BI DirectQuery csatlakozás](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Írja be a HTTP-hitelesítő adatokat, majd válassza a **Csatlakozás lehetőséget.** Az alapértelmezett felhasználónév **admin**.

8. A bal oldali ablak **Navigátor** ablakában válassza a **hivesampletale**lehetőséget.

9. Válassza a **Főablak Betöltés lehetőséget.**

    ![HDInsight interaktív lekérdezés Power BI-struktúramintatábla](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Adatok megjelenítése a térképen

Folytassa az utolsó eljárástól.

1. A Vizualizációk ablaktáblában válassza a **Térkép**lehetőséget, a Földgömb ikont. Ezután megjelenik egy általános térkép a főablakban.

    ![A HDInsight Power BI testre szabhatja a jelentést](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. A Mezők ablaktáblán válassza az **ország** és **az eszközmake**lehetőséget. Néhány pillanat múlva megjelenik egy világtérkép az adatpontokkal a főablakban.

3. Bontsa ki a térképet.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtudhatja, hogyan jelenítheti meg az adatokat a HDInsightból a Microsoft Power BI használatával.  Az adatmegjelenítésről az alábbi cikkekben talál további információt:

* [Az Apache Hive-adatok megjelenítése a Microsoft Power BI segítségével az ODBC használatával az Azure HDInsight ban.](../hadoop/apache-hadoop-connect-hive-power-bi.md) 
* [Az Apache Zeppelin segítségével apache hive-lekérdezéseket futtataz az Azure HDInsightban.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Az Excel csatlakoztatása a HDInsighthoz a Microsoft Hive ODBC illesztőprogrammal](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel csatlakoztatása az Apache Hadoophoz a Power Query használatával.](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Csatlakozzon az Azure HDInsighthoz, és futtasson Apache Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Használja az Azure HDInsight eszközt a Visual Studio-kódhoz.](../hdinsight-for-vscode.md)
* [Adatok feltöltése a HDInsight ba.](./../hdinsight-upload-data.md)
