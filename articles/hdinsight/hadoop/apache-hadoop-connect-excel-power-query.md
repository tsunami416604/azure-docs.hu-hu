---
title: Az Excel csatlakoztatása az Apache Hadoophoz a Power Queryvel - Azure HDInsight
description: Megtudhatja, hogy miként használhatja ki az üzletiintelligencia-összetevőket, és hogyan használhatja az Excelhez készült Power Queryt a HDInsight Hadoopban tárolt adatok eléréséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435806"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Az Excel csatlakoztatása az Apache Hadoophoz a Power Query használatával

A Microsoft big data megoldásának egyik legfontosabb jellemzője a Microsoft üzletiintelligencia-összetevők integrálása az Apache Hadoop-fürtökkel az Azure HDInsightban. Egy elsődleges példa az Excel csatlakoztatása az Azure Storage-fiókhoz, amely a Hadoop-fürthöz társított adatokat tartalmazza a Microsoft Power Query for Excel bővítmény használatával. Ez a cikk bemutatja, hogyan állíthatja be és használhatja a Power Queryt a HDInsightdal kezelt Hadoop-fürthöz társított adatok lekérdezésére.

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)
* Windows 10, 7, Windows Server 2008 R2 vagy egy későbbi operációs rendszert futtató munkaállomás.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Önálló Excel 2013 vagy Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>A Microsoft Power Query telepítése

A Power Query importálhatja a kimeneten lévő vagy egy HDInsight-fürtön futó Hadoop-feladat által létrehozott adatokat.

Az Excel 2016-ban a Power Query integrálva van az Adatok menüszalagba a Get & Transform szakasz alatt. Régebbi Excel-verziók esetén töltse le a Microsoft Power Query for Excel alkalmazást a [Microsoft letöltőközpontból,](https://go.microsoft.com/fwlink/?LinkID=286689) és telepítse azt.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight-adatok importálása az Excelbe

Az Excel hez készült Power Query bővítmény megkönnyíti az adatok importálását a HDInsight-fürtről az Excelbe, ahol az üzletiintelligencia-eszközök, például a PowerPivot és a Power Map az adatok vizsgálatára, elemzésére és bemutatására használhatók.

1. Indítsa el az Excelt.

1. Hozzon létre egy új üres munkafüzetet.

1. Hajtsa végre az alábbi lépéseket az Excel-verzió alapján:

   * Excel 2016

     * Válassza ki > **adatok** > **bekéselése** > **az Azure-ból** > **HDInsight(HDFS)**.

       ![Hdi. PowerQuery.SelectHdiForrás.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Válassza a **Power Query** > **az Azure-ból** > **a Microsoft Azure HDInsight**ból.

       ![Hdi. PowerQuery.SelectHdiForrás](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Megjegyzés:** Ha nem látható a **Power Query** menü, nyissa meg a **Fájlbeállítások** > **Options** > **bővítmények**lehetőséget, és válassza a **COM-bővítmények** lehetőséget a lap alján található **Kezelés** legördülő listakezelő mezőjében. Jelölje be az **Ugrás...** gombot, és ellenőrizze, hogy az Excel-alapú Power Query bővítmény jelölőnégyzete be van-e jelölve.

       **Megjegyzés:** A Power Query lehetővé teszi adatok importálását a HDFS fájlból **a Más forrásokból lehetőséget**választva.

1. Az **Azure HDInsight(HDFS)** párbeszédpanelen a **Fiók neve vagy URL-cím** mezőjébe írja be a fürthöz társított Azure Blob tárfiók nevét. Ezután kattintson az **OK** gombra. Ez a fiók lehet az alapértelmezett tárfiók vagy egy csatolt tárfiók.  A formátum `https://StorageAccountName.blob.core.windows.net/`.

1. A **Fiókkulcs**csoportban adja meg a Blob-tárfiók kulcsát, majd válassza a **Csatlakozás**lehetőséget. (A fiókadatokat csak az áruház első megnyitásakor kell megadnia.)

1. A Lekérdezésszerkesztő bal oldalán található **Navigátor** ablaktáblán kattintson duplán a fürthöz társított Blob-tároló nevére. Alapértelmezés szerint a tároló neve megegyezik a fürt nevével.

1. Keresse meg a **HiveSampleData.txt fájlt** a **Név** oszlopban (a mappa elérési útja **.. /hive/warehouse/hivesampletable/**), majd válassza a **Binary** lehetőséget a HiveSampleData.txt fájl bal oldalán. A HiveSampleData.txt fájl az összes fürthöz tartozik. Opcionálisan használhatja a saját fájlját.

    ![HDI Excel power lekérdezés adatok importálása](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Ha szeretné, átnevezheti az oszlopneveket. Ha készen áll, válassza **a Bezárás & betöltése**lehetőséget.  Az adatok be lettek töltve a munkafüzetbe:

    ![HDI Excel power query importált tábla](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a Power Query t a HDInsightból az Excelbe történő adatok beolvasására. Hasonlóképpen adatokat kérhet be a HDInsightból az Azure SQL Database-be. Az is lehetséges, hogy adatokat feltölteni a HDInsight. További információ: a következő cikkek:

* [Az Apache Hive-adatok megjelenítése a Microsoft Power BI szolgáltatással az Azure HDInsightban.](apache-hadoop-connect-hive-power-bi.md)
* [Jelenítse meg az interaktív lekérdezéshive-adatokat az Azure HDInsight Power BI szolgáltatásában.](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Az Apache Zeppelin segítségével apache hive-lekérdezéseket futtataz az Azure HDInsightban.](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Az Excel csatlakoztatása a HDInsighthoz a Microsoft Hive ODBC illesztőprogrammal](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Csatlakozzon az Azure HDInsighthoz, és futtasson Apache Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával.](apache-hadoop-visual-studio-tools-get-started.md)
* [Használja az Azure HDInsight eszközt a Visual Studio-kódhoz.](../hdinsight-for-vscode.md)
* [Adatok feltöltése a HDInsight ba.](./../hdinsight-upload-data.md)
