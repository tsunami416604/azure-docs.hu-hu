---
title: Apache Hive-alapú adatmegjelenítés Power BI-Azure HDInsight
description: Ismerje meg, hogyan jelenítheti meg az Azure HDInsight által feldolgozott kaptár-információkat a Microsoft Power BI használatával.
keywords: hdinsight, Hadoop, struktúra, interaktív lekérdezés, interaktív struktúra, LLAP, ODBC
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 0e8f0e6ff6ba4b280d6174b6cec231ddca782912
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058598"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Apache Hive-adatmegjelenítés a Microsoft Power BI az ODBC használatával az Azure HDInsight

Ismerje meg, hogyan csatlakoztathatók a Microsoft Power BI Desktop az Azure HDInsight az ODBC használatával, és Hogyan jeleníthető meg a Apache Hive adatai.

>[!IMPORTANT]
> A struktúra ODBC-illesztőjét kihasználhatja a Power BI Desktop-ben lévő általános ODBC-összekötőn keresztül történő importáláshoz. Azonban nem ajánlott a BI-munkaterhelések esetében a kaptár-lekérdezési motor nem interaktív természetét figyelembe venni. A [HDInsight interaktív lekérdezés-összekötő](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) és a [HDInsight Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jobb választás a teljesítményük szempontjából.

Ebben a cikkben egy `hivesampletable` struktúra-táblázat adatait Power BIba tölti be. A kaptár tábla néhány mobiltelefon-használati adatokat tartalmaz. Ezután kinyomtatja a használati adatokat a globális térképen:

![HDInsight Power BI a leképezési jelentés](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Az adatok az új [interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürt típusára is érvényesek. Ha közvetlen lekérdezéssel szeretne csatlakozni a HDInsight interaktív lekérdezéshez, tekintse meg az [interaktív lekérdezési struktúra adatai a Microsoft Power bi használatával közvetlen lekérdezéssel az Azure HDInsight-ben](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtása előtt a következő elemeket kell megadnia:

* **HDInsight-fürt**. A fürt lehet egy struktúra vagy egy újonnan kiadott interaktív lekérdezési fürttel rendelkező HDInsight-fürt. Fürtök létrehozásával kapcsolatban lásd: [fürt létrehozása](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power bi Desktop](https://powerbi.microsoft.com/desktop/)** . A másolást a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45331)töltheti le.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Lásd: [struktúra ODBC-adatforrás létrehozása](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsight

A hivesampletable-struktúra tábla minden HDInsight-fürtöt tartalmaz.

1. Power BI Desktop elindítása.

2. A felső menüben navigáljon a **Kezdőlap** >  > **adatok beolvasása** **továbbiak...** elemre.

    ![Az HDInsight Excel Power BI megnyitása](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Az **adatok lekérése** párbeszédpanelen válassza **az egyéb** lehetőséget a bal oldalon, válassza ki az **ODBC** elemet a jobb oldalon, majd válassza a lenti **kapcsolat** lehetőséget.

4. Az **ODBC-ből** párbeszédpanelen válassza ki az előző szakaszban létrehozott adatforrás nevét a legördülő listából, majd kattintson az **OK gombra**.

5. A **navigátor** párbeszédpanelen bontsa ki az **ODBC > struktúra > alapértelmezett**beállítást, válassza a **hivesampletable**lehetőséget, majd válassza a **Betöltés**lehetőséget.

6. Az **ODBC-illesztő** párbeszédpanelen válassza az **alapértelmezett vagy az egyéni**lehetőséget, majd kattintson a **kapcsolat**elemre.

## <a name="visualize-data"></a>Adatok vizualizációja

Folytassa az utolsó eljárással.

1. A vizualizációk ablaktáblán válassza a **Térkép**lehetőséget.  Ez egy földgömb ikon.

    ![HDInsight Power BI a jelentés testreszabása](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. A **mezők** panelen válassza az **ország** és a **devicemake**lehetőséget. Láthatja a térképen ábrázolt adatfeliratokat.
3. Bontsa ki a térképet.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan jelenítheti meg a HDInsight adatait a Power BI használatával.  További információt a következő cikkekben talál:

* [Apache Hive lekérdezések futtatása az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](apache-hadoop-connect-excel-power-query.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](apache-hadoop-visual-studio-tools-get-started.md).
* [Használja az Azure HDInsight eszközt a Visual Studio Code](../hdinsight-for-vscode.md)-hoz.
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).
