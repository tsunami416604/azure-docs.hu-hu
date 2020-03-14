---
title: Apache Hive-alapú adatmegjelenítés Power BI-Azure HDInsight
description: Ismerje meg, hogyan jelenítheti meg az Azure HDInsight által feldolgozott kaptár-információkat a Microsoft Power BI használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/02/2020
ms.openlocfilehash: d9b64785dbd82842479eb3f313b8394f9f25b40b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369998"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Apache Hive-adatmegjelenítés a Microsoft Power BI az ODBC használatával az Azure HDInsight

Ismerje meg, hogyan csatlakoztathatók a Microsoft Power BI Desktop az Azure HDInsight az ODBC használatával, és Hogyan jeleníthető meg a Apache Hive adatai.

> [!IMPORTANT]
> A struktúra ODBC-illesztőjét kihasználhatja a Power BI Desktop-ben lévő általános ODBC-összekötőn keresztül történő importáláshoz. Azonban nem ajánlott a BI-munkaterhelések esetében a kaptár-lekérdezési motor nem interaktív természetét figyelembe venni. A [HDInsight interaktív lekérdezés-összekötő](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) és a [HDInsight Spark-összekötő](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jobb választás a teljesítményük szempontjából.

Ebben a cikkben az adatok betöltését egy `hivesampletable` struktúra-táblázatból Power BIba. A kaptár tábla néhány mobiltelefon-használati adatokat tartalmaz. Ezután kinyomtatja a használati adatokat a globális térképen:

![HDInsight Power BI a leképezési jelentés](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Az adatok az új [interaktív lekérdezési](../interactive-query/apache-interactive-query-get-started.md) fürt típusára is érvényesek. Ha közvetlen lekérdezéssel szeretne csatlakozni a HDInsight interaktív lekérdezéshez, tekintse meg az [interaktív lekérdezési struktúra adatai a Microsoft Power bi használatával közvetlen lekérdezéssel az Azure HDInsight-ben](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A cikk végrehajtása előtt a következő elemeket kell megadnia:

* HDInsight-fürt. A fürt lehet egy struktúra vagy egy újonnan kiadott interaktív lekérdezési fürttel rendelkező HDInsight-fürt. Fürtök létrehozásával kapcsolatban lásd: [fürt létrehozása](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power bi Desktop](https://powerbi.microsoft.com/desktop/). A másolást a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=45331)töltheti le.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása

Lásd: [struktúra ODBC-adatforrás létrehozása](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Adatok betöltése a HDInsight

A **hivesampletable** -struktúra tábla minden HDInsight-fürtöt tartalmaz.

1. Power BI Desktop elindítása.

1. A felső menüben navigáljon a **kezdőlap** > **adatok beolvasása** > **továbbiak...** .

    ![Az HDInsight Excel Power BI megnyitása](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. Az **adatok lekérése** párbeszédpanelen válassza **az egyéb** lehetőséget a bal oldalon, válassza ki az **ODBC** elemet a jobb oldalon, majd válassza a lenti **kapcsolat** lehetőséget.

1. Az az **ODBC-ből** párbeszédpanelen válassza ki a legördülő lista utolsó szakaszában létrehozott adatforrás nevét. Ezután kattintson az **OK** gombra.

1. Az első használathoz egy **ODBC-illesztőprogram** párbeszédablak nyílik meg. Válassza az **alapértelmezett vagy az egyéni** lehetőséget a bal oldali menüben. Ezután válassza a **Kapcsolódás** a **navigátor**megnyitásához lehetőséget.

1. A **navigátor** párbeszédpanelen bontsa ki az **ODBC > struktúra > alapértelmezett**beállítást, válassza a **hivesampletable**lehetőséget, majd válassza a **Betöltés**lehetőséget.

## <a name="visualize-data"></a>Adatok vizualizációja

Folytassa az utolsó eljárással.

1. A vizualizációk ablaktáblán válassza a **Térkép**lehetőséget, ez egy földgömb ikon.

    ![HDInsight Power BI a jelentés testreszabása](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. A **mezők** panelen válassza az **ország** és a **devicemake**lehetőséget. Láthatja a térképen ábrázolt adatfeliratokat.

1. Bontsa ki a térképet.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan jelenítheti meg a HDInsight adatait a Power BI használatával.  További információt a következő cikkekben talál:

* [Apache Hive lekérdezések futtatása az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](apache-hadoop-connect-excel-power-query.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](apache-hadoop-visual-studio-tools-get-started.md).
* [Használja az Azure HDInsight eszközt a Visual Studio Code](../hdinsight-for-vscode.md)-hoz.
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).
