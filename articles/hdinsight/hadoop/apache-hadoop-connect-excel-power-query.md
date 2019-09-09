---
title: Az Excel és a Apache Hadoop összekötése Power Query-Azure HDInsight
description: Ismerje meg, hogyan használhatja ki az üzleti intelligencia összetevőit, és hogyan férhet hozzá az Excelhez Power Query az Hadoop-on tárolt adatokhoz a HDInsight-on.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: c1255296854fd28cca4427b6cac04de7dc7919d8
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810915"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Az Excel és a Apache Hadoop összekötése Power Query használatával
A Microsoft Big-adatok megoldásának egyik fő funkciója a Microsoft Business Intelligence-(BI-) összetevők integrálása Apache Hadoop-fürtökkel az Azure HDInsight-ben. Az elsődleges példa az Excel és az Azure Storage-fiók összekapcsolása, amely a Hadoop-fürthöz társított adatait tartalmazza a Excelhez készült Microsoft Power Query beépülő modullal. Ez a cikk bemutatja, hogyan állíthatja be és használhatja a Power Queryt a HDInsight-mel felügyelt Hadoop-fürtökhöz kapcsolódó adatlekérdezéshez.

### <a name="prerequisites"></a>Előfeltételek
A cikk elkezdése előtt a következő elemeket kell megadnia:

* **An méretű HDInsight-fürt**. A konfigurálásához tekintse meg az [első lépések az Azure HDInsight használatával] [HDInsight-Get-Started] című témakört.
* Windows 7, Windows Server 2008 R2 vagy újabb operációs rendszert futtató **munkaállomás** .
* **Office 2016, office 2013 Professional Plus, office 365 ProPlus, Excel 2013 önálló vagy office 2010 Professional Plus**.

## <a name="install-power-query"></a>Power Query telepítése
A Power Query képes importálni olyan adatokat, amelyek kimenete vagy egy HDInsight-fürtön futó Hadoop-feladatok által generált adatok.

Az Excel 2016-es verziójában a Power Query be lett építve az adatszalagba a beolvasás & átalakítás szakaszban. A régebbi Excel-verziók esetében töltse le Excelhez készült Microsoft Power Query a [Microsoft letöltőközpontból][powerquery-download] , és telepítse.

## <a name="import-hdinsight-data-into-excel"></a>HDInsight-adatimportálás az Excelbe
Az Excelhez készült Power Query beépülő modul megkönnyíti az adatok importálását az HDInsight-fürtről az Excelbe, ahol a BI-eszközök, például a PowerPivot és a Power Map használhatók az adatok vizsgálatára, elemzésére és bemutatására.

**Adatok importálása HDInsight-fürtből**

1. Nyissa meg az Excelt.
2. Hozzon létre egy új üres munkafüzetet.
3. Hajtsa végre az alábbi lépéseket az Excel-verziótól függően:

   - Excel 2016

     - Kattintson az **adatok** menüre, majd az **adatok** beolvasása lehetőségre az **adatok beolvasása a & átalakítása** menüszalagról, kattintson az **Azure**-ból lehetőségre, majd **Az Azure HDInsight (HDFS)** elemre.

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

   - Excel 2013/2010

     - Kattintson a **Power Query** menüre, majd **Az Azure**-ból, majd a **Microsoft Azure HDInsight**lehetőségre.
   
       ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
       **Megjegyzés:** Ha nem látja a **Power Query** menüt, lépjen a **file** > **Options** > **bővítmények**menüpontra, majd a lap alján található legördülő lista **kezelés** mezőjében válassza a **com-bővítmények** lehetőséget. Kattintson a **Go... (ugrás)** gombra, és ellenőrizze, hogy az Excel-bővítményhez tartozó Power Query jelölőnégyzet be van-e jelölve.
       
       **Megjegyzés:** A Power Query lehetővé teszi adatok importálását a HDFS az **egyéb forrásokból**való kattintással.
4. A **fiók neve**mezőben adja meg a fürthöz társított Azure Blob Storage-fiók nevét, majd kattintson **az OK**gombra. Ez a fiók lehet az alapértelmezett Storage-fiók vagy egy társított Storage-fiók.  A formátum a *https://&lt;StorageAccountName >. blob. Core. Windows. net/* .
5. A **fiók kulcsa**mezőben adja meg a blob Storage-fiók kulcsát, majd kattintson a **Save (Mentés**) gombra. (A fiók adatait csak akkor kell megadnia, amikor először fér hozzá ehhez a tárolóhoz.)
6. A lekérdezés-szerkesztő bal oldalán található **navigátor** ablaktáblán kattintson duplán a blob Storage-tároló nevére. Alapértelmezés szerint a tároló neve ugyanaz a neve, mint a fürt neve.
7. Keresse meg a **HiveSampleData. txt fájlt** a **Name (név** ) oszlopban (a mappa elérési útja: **.. /Hive/Warehouse/hivesampletable/** ), majd kattintson a HiveSampleData. txt fájl bal oldalán található **Binary (bináris** ) elemre. A HiveSampleData. txt fájl minden fürtöt tartalmaz. Igény szerint saját fájlt is használhat.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Ha szeretné, átnevezheti az oszlopnevek nevét. Ha elkészült, kattintson a **bezárás & betöltés**elemre.  A rendszer betöltötte az adatait a munkafüzetbe:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>További lépések
Ebből a cikkből megtudhatta, hogyan használhatja a Power Queryt adatok lekéréséhez a HDInsight-ből az Excelbe. Hasonlóképpen lekérheti a HDInsight adatait a Azure SQL Databaseba. Az adatok a HDInsight is feltölthetők. További információt a következő cikkekben talál:

* Az [Azure HDInsight-ban a Microsoft Power BI Apache Hivei az adatmegjelenítést](apache-hadoop-connect-hive-power-bi.md).
* [Interaktív lekérdezési struktúra-adatmegjelenítés az Azure HDInsight Power BIával](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Apache Hive lekérdezések futtatása az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Az Excel és a HDInsight összekötése a Microsoft kaptár ODBC-illesztővel](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](apache-hadoop-visual-studio-tools-get-started.md).
* [Használja az Azure HDInsight eszközt a Visual Studio Code](../hdinsight-for-vscode.md)-hoz.
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
