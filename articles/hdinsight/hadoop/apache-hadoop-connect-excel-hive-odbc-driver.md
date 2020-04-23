---
title: Az Excel & Apache Hadoop open database connectivity (ODBC) driverrel – Azure HDInsight
description: Megtudhatja, hogy miként állíthatja be és használhatja az Excelhez készült Microsoft Hive ODBC illesztőprogramot a Microsoft Excel HDInsight-fürtökben lévő adatok lekérdezésére.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/22/2020
ms.openlocfilehash: 5aefd2c344565bf6dcb384996c42c1bc30e7291d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024931"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Az Excel és az Apache Hadoop csatlakoztatása az Azure HDInsightban a Microsoft Hive ODBC-illesztővel

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A Microsoft Big Data-megoldása integrálja a Microsoft Business Intelligence (BI) összetevőit a HDInsightban telepített Apache Hadoop-fürtökkel. Ilyen például az Excel csatlakoztatása a Hadoop-fürt Hive-adattárházához. Csatlakozás a Microsoft Hive Open Database Connectivity (ODBC) illesztőprogramsegítségével.

Az Excelből az Excelből egy HDInsight-fürthöz társított adatokat összekapcsolhatja az Excel Microsoft Power Query bővítményével. További információt az [Excel csatlakoztatása a HDInsighthoz a Power Queryvel](../hdinsight-connect-excel-power-query.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

A cikk megkezdése előtt a következő elemekkel kell rendelkeznie:

* Egy HDInsight Hadoop-fürt. A létrehozásról az Azure HDInsight – Első lépések című [témakörben látható.](apache-hadoop-linux-tutorial-get-started.md)
* Munkaállomás az Office 2010 Professional Plus vagy újabb verzióval, illetve az Excel 2010-es vagy újabb verzióval.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft Hive ODBC illesztőprogram telepítése

Töltse le és telepítse a [Microsoft Hive ODBC drivert.](https://www.microsoft.com/download/details.aspx?id=40886) Válassza ki azt a verziót, amely megfelel annak az alkalmazásnak, amelyen az ODBC-illesztőprogramot használni fogja.  Ebben a cikkben az illesztőprogramot az Office Excel ben használják.

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC adatforrás létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy Hive ODBC adatforrást.

1. Windows rendszerből nyissa meg **a Windows felügyeleti eszközök > > az ODBC adatforrások (32 bites) /(64 bites).**  Ez a művelet megnyitja az **ODBC adatforrás-felügyelő** ablakot.

    ![OBDC adatforrás-rendszergazda](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "DSN konfigurálása az ODBC adatforrás-felügyelővel")

1. A **Felhasználói adatforrások** ablak **megnyitásához** válassza a **Hozzáadás** gombot.

1. Válassza a **Microsoft Hive ODBC illesztőprogram**lehetőséget, majd a **Befejezés** gombra a **Microsoft Hive ODBC illesztőprogram dsn telepítőablakának** megnyitásához.

1. Írja be vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Állomás(ok) |Írja be a `HDInsightClusterName.azurehdinsight.net` (igen) kifejezést. Például: `myHDICluster.azurehdinsight.net`. Megjegyzés: `HDInsightClusterName-int.azurehdinsight.net` támogatott mindaddig, amíg az ügyfél virtuális gép társviszonyban van az azonos virtuális hálózathoz. |
   |  Port |Használja a **443** számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
   |  Adatbázis |Használja **az alapértelmezett**et. |
   |  Mechanizmus |**Windows Azure HDInsight szolgáltatás** kiválasztása |
   |  Felhasználónév |Írja be a HDInsight fürt HTTP-felhasználónevét. Az alapértelmezett felhasználónév az **admin**. |
   |  Jelszó |Adja meg a HDInsight fürt felhasználói jelszavát. Jelölje be a **Jelszó mentése (titkosított) jelölőnégyzetet.**|

1. Nem kötelező: Válassza a **Speciális beállítások lehetőséget...**  

   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC illesztőprogram nem próbálja meg a TSQL-t HiveQL-lé konvertálni. Csak akkor használhatod, ha 100%-ig biztos vagy benne, hogy tiszta HiveQL nyilatkozatokat nyújtasz be. Az SQL Server vagy az Azure SQL Database csatlakozásakor hagyja bejelölve. |
   |  Blokkonként lekért sorok |Nagy számú rekord beolvasásakor szükség lehet erre a paraméterre az optimális teljesítmény biztosításához. |
   |  Alapértelmezett karakterláncoszlop hossza, Bináris oszlophosszak, Decimális oszloplépték |Az adattípusok hossza és pontossága befolyásolhatja az adatok visszaadásának módját. Hibás adatokat ad vissza a pontosság és a csonkolás elvesztése miatt. |

    ![Speciális dsn konfigurációs beállítások](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Speciális dsn konfigurációs beállítások")

1. Válassza a **Teszt** lehetőséget az adatforrás teszteléséhez. Ha az adatforrás megfelelően van konfigurálva, a teszt eredménye a **SUCCESS!**

1. A Teszt ablak bezárásához válassza az **OK gombot.**  

1. A **Microsoft Hive ODBC illesztőprogram dsn telepítőablakának** bezárásához válassza az **OK gombot.**  

1. Az **ODBC adatforrás-felügyelő** ablak bezárásához válassza az **OK gombot.**  

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból

Az alábbi lépések azt ismertetik, hogyan importálhat adatokat egy Hive-táblából egy Excel-munkafüzetbe az előző szakaszban létrehozott ODBC adatforrás használatával.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.

2. Az **Adatok** lapon válassza az **Adatok becsatornázása** > **más forrásokból** > **az ODBC-ből** lehetőséget az **ODBC-ből** ablak elindításához.

    ![Az Excel adatkapcsolatának varázslója megnyitása](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Az Excel adatkapcsolatának varázslója megnyitása")

3. A legördülő listában jelölje ki az utolsó szakaszban létrehozott adatforrásnevet, majd kattintson az **OK gombra.**

4. Az első használathoz megnyílik egy **ODBC-illesztőprogram-párbeszédpanel.** Válassza a Bal oldali menü **Windows** parancsát. Ezután a Csatlakozás gombra a **Navigátor** ablak megnyitásához válassza a **Csatlakozás** lehetőséget.

5. A **Navigátor**ban keresse meg a **HIVE** > **alapértelmezett** > **struktúramintatáblájának navigálását,** és válassza a **Betöltés**lehetőséget. Néhány percet vesz igénybe, amíg az adatok importálása az Excelbe történik.

    ![HDInsight Excel Hive ODBC navigátor](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel Hive ODBC navigátor")

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a Microsoft Hive ODBC illesztőprogramot a HDInsight szolgáltatásból az Excelbe történő adatok beolvasására. Hasonlóképpen a HDInsight-szolgáltatásból is bekérhet adatokat az SQL Database szolgáltatásba. Az is lehetséges, hogy adatokat feltölteni egy HDInsight-szolgáltatásba. További tudnivalókért lásd:

* [Az Apache Hive-adatok megjelenítése a Microsoft Power BI szolgáltatással az Azure HDInsightban.](apache-hadoop-connect-hive-power-bi.md)
* [Jelenítse meg az interaktív lekérdezéshive-adatokat az Azure HDInsight Power BI szolgáltatásában.](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Az Excel csatlakoztatása az Apache Hadoophoz a Power Query használatával.](apache-hadoop-connect-excel-power-query.md)
* [Csatlakozzon az Azure HDInsighthoz, és futtasson Apache Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával.](apache-hadoop-visual-studio-tools-get-started.md)
