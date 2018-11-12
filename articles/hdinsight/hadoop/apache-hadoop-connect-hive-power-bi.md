---
title: A Power bi használatával az Azure HDInsight big Data típusú adatok megjelenítése
description: Ismerje meg, hogyan dolgozza fel az Azure HDInsight Hive-adatok vizualizálása a Microsoft Power BI használatával.
keywords: a hdinsight hadoop-, hive, interaktív lekérdezés, interaktív hive, LLAP, odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: b094df367d4396533d368b9fdbcb4b78ec1673de
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008895"
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>A Microsoft Power BI ODBC segítségével az Azure HDInsight Hive-adatok vizualizálása

Ismerje meg, hogyan csatlakozhat Azure HDInsight ODBC segítségével a Microsoft Power BI és a Hive-adatok megjelenítése. 

>[!IMPORTANT]
> Kihasználhatja a Hive ODBC-illesztőprogram importálása a Power BI Desktop általános ODBC-összekötő használatával. Azonban nem ajánlott a Hive-lekérdezési motor nem interaktív természeténél BI számítási feladatokhoz. [HDInsight interaktív lekérdezés összekötője](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) és [HDInsight Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) a teljesítményük jobb döntéseket.

Ebben az oktatóanyagban betölteni az adatokat egy hivesampletable Hive-táblából a Power bi-bA. A Hive-tábla tartalmaz néhány mobiltelefon-használati adatokat. Ezután a használati adatok a világ térképen jeleníti meg:

![HDInsight Power bi-ban a térkép jelentésbe](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Az információk az új [interaktív lekérdezés](../interactive-query/apache-interactive-query-get-started.md) fürt típusa. HDInsight interaktív lekérdezés közvetlen lekérdezés használatával csatlakozhat, olvassa el a [adatok interaktív lekérdezéses Hive jelenítheti meg a közvetlen lekérdezés használatával Azure HDInsight a Microsoft Power BI](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).



## <a name="prerequisites"></a>Előfeltételek
Mielőtt végrehajtaná ezt a cikket, a következőkkel kell rendelkeznie:

* **HDInsight-fürt**. A fürt vagy a Hive használatával egy HDInsight-fürtöt, vagy egy újonnan kiadott interaktív lekérdezési fürt lehet. Fürtök létrehozása, lásd: [fürt létrehozása](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[A Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Letöltheti a másolatot a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Lásd: [létrehozása Hive ODBC-adatforrás](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>A HDInsight adatok betöltése

A hivesampletable Hive-tábla összes HDInsight-fürtöket tartalmaz.

1. Jelentkezzen be a Power BI Desktopban.
2. Kattintson a **kezdőlap** lapra, majd **adatok lekérése** származó a **külső adatok** menüszalagra, és válassza ki **további**.

    ![Nyissa meg HDInsight Power BI-adatok](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. A a **adatok lekérése** ablaktáblán kattintson **más** kattintson a bal oldali **ODBC** jobbról, és kattintson a **Connect** alján.
4. Az a **származó ODBC** panelen válassza ki az adatokat a forrás az előző szakaszban létrehozott nevet, és kattintson **OK**.
5. Az a **kezelő** ablaktáblán bontsa ki a **HIVE ODBC -> -> alapértelmezett**, jelölje be **hivesampletable**, és kattintson a **terhelés**.

## <a name="visualize-data"></a>Adatok vizualizációja

Továbbra is az előző eljárást.

1. A megjelenítések ablaktáblán válassza ki a **térkép**.  Egy földgömb ikon.

    ![HDInsight Power bi-ban a jelentés személyre szabható.](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. A mezők panelen válassza ki a **ország** és **devicemake**. Láthatja, hogy a térképen ábrázolt adatok.
3. Bontsa ki a térképet.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan HDInsight használata a Power BI adatok vizualizálásához.  További tudnivalókért tekintse meg a következő cikkeket:

* [Az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használatával](./../hdinsight-connect-hive-zeppelin.md).
* [Excel csatlakoztatása a Microsoft Hive ODBC illesztőprogram segítségével a HDInsight](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Az Excel összekapcsolása a Hadooppal a Power Query használatával](apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával Hive-lekérdezések futtatása](apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Upload Data to HDInsight](./../hdinsight-upload-data.md).
