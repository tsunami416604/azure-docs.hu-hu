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
ms.date: 11/06/2018
ms.openlocfilehash: 46b5b3436a33c3f83d85cfb02028759d53d214af
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245273"
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>A közvetlen lekérdezés használatával Azure HDInsight a Microsoft Power BI interaktív lekérdezéses Hive-adatok megjelenítése

Ez a cikk ismerteti a Microsoft Power BI csatlakoztatása az Azure HDInsight interaktív lekérdezési fürtökhöz, és a közvetlen lekérdezés használatával Apache Hive-adatok megjelenítése. A megadott példa betölti az adatokat a Power bi-bA egy hivesampletable Hive-táblából. A hivesampletable Hive tábla tartalmaz néhány mobiltelefon-használati adatok. Ezután a használati adatok a világ térképen jeleníti meg:

![HDInsight Power bi-ban a térkép jelentésbe](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Kihasználhatja a [Hive ODBC-illesztő](../hadoop/apache-hadoop-connect-hive-power-bi.md) importálása a Power BI Desktop általános ODBC-összekötő használatával. Azonban nem ajánlott a Hive-lekérdezési motor nem interaktív természeténél BI számítási feladatokhoz. [HDInsight interaktív lekérdezés összekötője](./apache-hadoop-connect-hive-power-bi-directquery.md) és [HDInsight Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) a teljesítményük jobb döntéseket.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt végrehajtaná ezt a cikket, a következőkkel kell rendelkeznie:

* **HDInsight-fürt**. A fürt vagy a Hive használatával egy HDInsight-fürtöt, vagy egy újonnan kiadott interaktív lekérdezési fürt lehet. Fürtök létrehozása, lásd: [fürt létrehozása](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[A Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Letöltheti a másolatot a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>A HDInsight adatok betöltése

A hivesampletable Hive-tábla összes HDInsight-fürtöket tartalmaz.

1. Jelentkezzen be a Power BI Desktopban.

2. Kattintson a **kezdőlap** lapra, majd **adatok lekérése** származó a **külső adatok** menüszalagra, és válassza ki **több...** .

    ![Nyissa meg HDInsight Power BI-adatok](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
    
3. Az a **adatok lekérése** panelen kattintson a jobb típus **hdinsight** kifejezést a keresőmezőbe. Ha nem lát **HDInsight interaktív lekérdezés (bétaverzió)**, frissítenie kell a Power BI Desktopot a legújabb verzióra.

4. Válassza ki **HDInsight interaktív lekérdezés (bétaverzió)**, majd válassza ki **Connect**.

5. Válassza ki **Folytatás** gombra kattintva zárja be a **összekötő előnézete** figyelmeztető párbeszédpanel.

6. A **HDInsight interaktív lekérdezés**, válassza ki vagy adja meg a következőket:

    - **Kiszolgáló**: Adja meg az interaktív lekérdezési fürt nevét, például *myiqcluster.azurehdinsight.net*.

    - **Adatbázis**: A jelen oktatóanyag esetében írja be a **alapértelmezett**.
    
    - **Adatkapcsolati mód**: A jelen oktatóanyag esetében válassza ki a **DirectQuery**.

    ![HDInsight interaktív lekérdezés a Power BI DirectQuery csatlakoztatása](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Kattintson az **OK** gombra.

8. Adja meg a HTTP felhasználói hitelesítő adatokat, és kattintson a **OK**. Az alapértelmezett felhasználónév az **rendszergazda**

9. A bal oldali panelen válassza ki a **hivesampletale**, és kattintson a **terhelés**.

    ![HDInsight interaktív lekérdezés a Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>A térképen adatok megjelenítése

Továbbra is az előző eljárást.

1. A megjelenítések ablaktáblán válassza ki a **térkép**.  Egy földgömb ikon.

    ![HDInsight Power bi-ban a jelentés személyre szabható.](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
    
2. A mezők panelen válassza ki a **ország** és **devicemake**. Láthatja, hogy a térképen ábrázolt adatok.

3. Bontsa ki a térképet.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan HDInsight használata a Power BI adatok vizualizálásához.  Adatmegjelenítés további információkért lásd a következő cikkeket:

* [Hive-adatok vizualizálása a Microsoft Power BI ODBC segítségével az Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használatával](./../hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC illesztőprogram segítségével a HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel összekapcsolása a Hadooppal a Power Query használatával](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával Hive-lekérdezések futtatása](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./../hdinsight-upload-data.md).
