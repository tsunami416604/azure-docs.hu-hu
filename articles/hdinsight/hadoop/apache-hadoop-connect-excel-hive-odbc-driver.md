---
title: Excel & Apache Hadoop ODBC-illesztővel – Azure HDInsight
description: Ismerje meg, hogyan állíthatja be és használhatja az Excelhez készült Microsoft kaptár ODBC-illesztőt a Microsoft Excel HDInsight-fürtök adatainak lekérdezéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/02/2020
ms.openlocfilehash: f356009197c0446efa2ea2d7f0e90040229df47b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251064"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Az Excel és az Apache Hadoop összekötése az Azure HDInsight a Microsoft kaptár ODBC-illesztővel

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A Microsoft Big adatmegoldása integrálja a Microsoft Business Intelligence-(BI-) összetevőket az Azure HDInsight üzembe helyezett Apache Hadoop-fürtökkel. Ennek az integrációnak egy példája az Excel és a Hadoop-fürt HDInsight való összekapcsolásának lehetősége a Microsoft kaptár Open Database Connectivity (ODBC) illesztőprogram használatával.

A HDInsight-fürtökhöz és más adatforrásokhoz (például más (nem HDInsight) Hadoop-fürtökhöz kapcsolódó adatok is csatlakoztathatók az Excel programhoz készült Microsoft Power Query bővítmény használatával. További információ a Power Query telepítéséről és használatáról: az [Excel és a HDInsight összekapcsolása a Power Query](../hdinsight-connect-excel-power-query.md)használatával.

## <a name="prerequisites"></a>Előfeltételek

A cikk elkezdése előtt a következő elemeket kell megadnia:

* An méretű HDInsight Hadoop-fürt. Az első lépésekhez tekintse meg [Az Azure HDInsight megismerését](apache-hadoop-linux-tutorial-get-started.md)ismertető témakört.
* Egy Office 2010 Professional Plus vagy újabb, vagy Excel 2010 vagy újabb verziójú munkaállomás.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft kaptár ODBC-illesztő telepítése

Töltse le és telepítse a [Microsoft kaptár ODBC-illesztőprogram](https://www.microsoft.com/download/details.aspx?id=40886) verziószámát, amely megegyezik annak az alkalmazásnak a verziójával, amely az ODBC-illesztőt fogja használni.  Ebben a cikkben a-illesztőprogramot az Office Excelhez használja a rendszer.

## <a name="create-apache-hive-odbc-data-source"></a>ODBC-adatforrás létrehozása Apache Hive

A következő lépések bemutatják, hogyan hozhat létre egy struktúra ODBC-adatforrást.

1. A Windowsban navigáljon a Start > Windows felügyeleti eszközök > ODBC-adatforrások (32 bites)/(64 bites) elemre.  Ekkor megnyílik az **ODBC-adatforrás rendszergazdája** ablak.

    ![OBDC-adatforrás rendszergazdája](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "ADATFORRÁS konfigurálása az ODBC-adatforrás rendszergazdájával")

1. A **felhasználói DSN** lapon válassza a **Hozzáadás** lehetőséget az **új adatforrás létrehozása** ablak megnyitásához.

1. Válassza a **Microsoft kaptár ODBC-illesztő**lehetőséget, majd kattintson a **Befejezés** gombra a **Microsoft kaptár ODBC-illesztő DSN-telepítő** ablak megnyitásához.

1. Írja be vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Állomás (ok) |Írja be a `HDInsightClusterName.azurehdinsight.net` (igen) kifejezést. Például: `myHDICluster.azurehdinsight.net`. Megjegyzés: a `HDInsightClusterName-int.azurehdinsight.net` támogatott, feltéve, hogy az ügyfél virtuális gépe ugyanahhoz a virtuális hálózathoz van felhasználva. |
   |  Port |Használja a **443** számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
   |  Adatbázis |Használja az **alapértelmezett értéket**. |
   |  Mechanizmus |A **Windows Azure HDInsight szolgáltatás** kiválasztása |
   |  Felhasználónév |Adja meg a HDInsight-fürt HTTP-felhasználói felhasználónevét. Az alapértelmezett felhasználónév az **admin**. |
   |  Jelszó |Adja meg a HDInsight-fürt felhasználói jelszavát. Jelölje be a **Jelszó mentése (titkosított)** jelölőnégyzetet.|

1. Választható lehetőség: válassza a **Speciális beállítások lehetőséget...**  

   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC-illesztő nem próbálkozik a TSQL konvertálásával a HiveQL-be. Ezt csak akkor kell használni, ha 100%-ban biztos benne, hogy tiszta HiveQL-utasításokat küld. SQL Server vagy Azure SQL Databasehoz való csatlakozáskor nincs bejelölve. |
   |  Beolvasott sorok száma blokkban |Nagy számú rekord beolvasása esetén a paraméter finomhangolása az optimális teljesítmény biztosítása érdekében szükséges lehet. |
   |  Alapértelmezett karakterlánc-oszlop hossza, Bináris oszlop hossza, decimális oszlop mérete |Az adattípus hossza és pontossága befolyásolhatja az adatvisszaadás módját. Helytelen adatokat eredményeznek a pontosság és/vagy a csonkítás elvesztése miatt. |

    ![Speciális DSN-konfigurációs beállítások](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Speciális DSN-konfigurációs beállítások")

1. Válassza a **teszt** lehetőséget az adatforrás teszteléséhez. Ha az adatforrás megfelelően van konfigurálva, a teszt eredménye a **sikert**mutatja.  

1. A teszt ablak bezárásához kattintson **az OK gombra** .  

1. Kattintson az **OK** gombra a **Microsoft kaptár ODBC illesztőprogram-DSN telepítési** ablak bezárásához.  

1. Kattintson az **OK** gombra az **ODBC-adatforrás felügyeleti** ablakának bezárásához.  

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból

A következő lépések leírják, hogyan importálhat egy struktúra-táblázat adatait egy Excel-munkafüzetbe az előző szakaszban létrehozott ODBC-adatforrás használatával.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.

2. Az **adatok** lapon navigáljon a **más > forrásokból** származó **adatok LEkérése** > az **ODBC** -ből, hogy elindítsa a **from ODBC** ablakot.

    ![Az Excel-adatkapcsolatok varázsló megnyitása](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Az Excel-adatkapcsolatok varázsló megnyitása")

3. A legördülő listában válassza ki az előző szakaszban létrehozott adatforrás nevét, majd kattintson az **OK gombra**.

4. Az első használathoz egy **ODBC-illesztőprogram** párbeszédablak nyílik meg. A bal oldali menüben válassza a **Windows** lehetőséget. Ezután válassza a **Kapcsolódás** lehetőséget a **navigátor** ablak megnyitásához.

5. A **Navigátorban**navigáljon a **kaptár** > **alapértelmezett** > **hivesampletable**elemre, majd válassza a **Betöltés**lehetőséget. Néhány percet vesz igénybe, mielőtt az Excelbe importálja az adatimportálást.

    ![HDInsight Excel-struktúra ODBC-navigátor](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel-struktúra ODBC-navigátor")

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan használhatja a Microsoft kaptár ODBC-illesztőt a HDInsight szolgáltatás adatainak az Excelbe való lekéréséhez. Hasonlóképpen lekérheti az HDInsight szolgáltatás adatait a SQL Databaseba. Az adatok az HDInsight szolgáltatásba is feltölthetők. További tudnivalókért lásd:

* Az [Azure HDInsight-ban a Microsoft Power BI Apache Hivei az adatmegjelenítést](apache-hadoop-connect-hive-power-bi.md).
* [Interaktív lekérdezési struktúra-adatmegjelenítés az Azure HDInsight Power BIával](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Apache Hive lekérdezések futtatása az Azure HDInsight az Apache Zeppelin használatával](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* Az [Excel Apache Hadoop Power Query használatával csatlakoztatható](apache-hadoop-connect-excel-power-query.md).
* [Kapcsolódjon az Azure HDInsight-hez, és Apache Hive-lekérdezéseket futtathat a Visual studióhoz készült Data Lake Tools használatával](apache-hadoop-visual-studio-tools-get-started.md).
* [Használja az Azure HDInsight eszközt a Visual Studio Code](../hdinsight-for-vscode.md)-hoz.
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).
