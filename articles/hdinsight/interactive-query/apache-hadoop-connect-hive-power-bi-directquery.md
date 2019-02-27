---
title: Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése
description: A Microsoft Power BI segítségével az Azure HDInsight interaktív lekérdezéses Hive adatainak megjelenítése
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: d9639a4a116e06e17005ebddbb26379882491b33
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867830"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>A Microsoft Power BI közvetlen lekérdezés használatával az Azure HDInsight interaktív lekérdezés Apache Hive-adatok megjelenítése

Ez a cikk ismerteti a Microsoft Power BI csatlakoztatása az Azure HDInsight interaktív lekérdezési fürtökhöz, és a közvetlen lekérdezés használatával Apache Hive-adatok megjelenítése. A megadott példa betölti az adatokat egy `hivesampletable` Hive táblát a Power bi-bA. A `hivesampletable` Hive-tábla tartalmaz néhány mobiltelefon-használati adatokat. Ezután a használati adatok a világ térképen jeleníti meg:

![HDInsight Power bi-ban a térkép jelentésbe](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Kihasználhatja a [Apache Hive ODBC-illesztő](../hadoop/apache-hadoop-connect-hive-power-bi.md) importálása a Power BI Desktop általános ODBC-összekötő használatával. Azonban nem ajánlott a Hive-lekérdezési motor nem interaktív természeténél BI számítási feladatokhoz. [HDInsight interaktív lekérdezés összekötője](./apache-hadoop-connect-hive-power-bi-directquery.md) és [HDInsight az Apache Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) a teljesítményük jobb döntéseket.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt végrehajtaná ezt a cikket, a következőkkel kell rendelkeznie:

* **HDInsight-fürt**. Lehet, hogy a fürt egy HDInsight-fürtöt az Apache Hive, vagy egy újonnan kiadott interaktív lekérdezési fürt. Fürtök létrehozása, lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[A Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Letöltheti a másolatot a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>A HDInsight adatok betöltése

A `hivesampletable` Hive-tábla összes HDInsight-fürt is tartalmaz.

1. Indítsa el a Power BI Desktopban.

2. Lépjen a menüsoron **kezdőlap** > **adatok lekérése** > **több...** .

    ![Nyissa meg HDInsight Power BI-adatok](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Az a **adatok lekérése** ablakban adja meg **hdinsight** kifejezést a keresőmezőbe.  

4. A keresési eredmények közül válassza ki a **HDInsight interaktív lekérdezés**, majd válassza ki **Connect**.  Ha nem lát **HDInsight interaktív lekérdezés**, frissítenie kell a Power BI Desktopot a legújabb verzióra.

5. Válassza ki **Folytatás** gombra kattintva zárja be a **külső szolgáltatáshoz csatlakozó** párbeszédpanel.

6. Az a **HDInsight interaktív lekérdezés** ablakban adja meg a következőket, majd **OK**:

    |Tulajdonság | Érték |
    |---|---|
    |Kiszolgáló |Adja meg a fürt nevét, például *myiqcluster.azurehdinsight.net*.|
    |Adatbázis |Adja meg **alapértelmezett** ebben a cikkben.|
    |Adatkapcsolati mód |Válassza ki **DirectQuery** ebben a cikkben.|

    ![HDInsight interaktív lekérdezés a Power BI DirectQuery csatlakoztatása](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Adja meg a HTTP-hitelesítő adatait, és válassza ki **Connect**. Az alapértelmezett felhasználónév **rendszergazdai**.

8. Az a **kezelő** ablak bal oldali panelén válassza a **hivesampletale**.

9. Válassza ki **terhelés** a főablakból.

    ![HDInsight interaktív lekérdezés a Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>A térképen adatok megjelenítése

Továbbra is az előző eljárást.

1. A megjelenítések ablaktáblán válassza ki a **térkép**, a világ ikonra. Egy általános térkép ezután a fő ablakban jelenik meg.

    ![HDInsight Power bi-ban a jelentés személyre szabható.](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. A mezők panelen válassza ki a **ország** és **devicemake**. Egy világtérkép az adatpontokhoz néhány pillanat múlva megjelenik a fő ablakot.

3. Bontsa ki a térképet.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett a Microsoft Power BI használatával HDInsight adatok vizualizálásához.  Adatmegjelenítés további információkért lásd a következő cikkeket:

* [A Microsoft Power BI ODBC segítségével az Azure HDInsight az Apache Hive-adatok megjelenítése](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Az Apache a Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához](./../hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC illesztőprogram segítségével a HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Excel Power Query használatával csatlakozni az Apache Hadoop](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./../hdinsight-upload-data.md).
