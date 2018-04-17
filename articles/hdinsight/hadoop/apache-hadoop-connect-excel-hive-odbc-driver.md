---
title: Excel csatlakoztatása a Hive ODBC-illesztőprogram - az Azure HDInsight Hadoop |} Microsoft Docs
description: Megtudhatja, hogyan állítson be és használja a Microsoft Hive ODBC-illesztőprogram az Excel programhoz HDInsight-fürtök lekérdezni adatokat a Microsoft Excel.
keywords: hadoop az excel, a hive az excel, a hive odbc
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 432fc630cad172255c6adcdce74291e3ec275c7b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Excel csatlakoztatása Azure HDInsight Hadoop a Microsoft Hive ODBC-illesztőprogram

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A Microsoft Big Data típusú adatok megoldás integrálódik a Microsoft üzleti Üzletiintelligencia-összetevők telepítve vannak az Azure hdinsightba Apache Hadoop-fürtök az. Ez az integráció például azt a képességet csatlakoztathatja az Excelt a Hive adatraktár a Microsoft Hive megnyitott adatbázis kapcsolat (ODBC illesztőprogrammal) hdinsight Hadoop-fürthöz.

Akkor is kapcsolódni HDInsight-fürtök és más adatforrások, például más (nem HDInsight) Hadoop-fürtök, az Excel alkalmazásban, a Microsoft Power Query beépülő modul az Excel programhoz kapcsolódó adatokat. Telepítés és a Power Query használatával kapcsolatos tudnivalókat lásd: [HDInsight a Power Query az Excel csatlakozás][hdinsight-power-query].



**Előfeltételek**:

Ez a cikk megkezdése előtt rendelkeznie kell a következő elemek:

* **HDInsight-fürtök**. Szeretne létrehozni egyet, lásd: [Ismerkedés az Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **A munkaállomás** Office 2013 Professional Plus, Office 365 Pro Plus, az Excel 2013 önálló vagy Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Telepítse a Microsoft Hive ODBC-illesztőprogram
Töltse le és telepítse a Microsoft Hive ODBC-illesztő a az [letöltőközpontból][hive-odbc-driver-download].

Az illesztőprogram telepíthető Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 és Windows Server 2012 32 bites vagy 64 bites verzióit. Az illesztőprogram lehetővé teszi, hogy az Azure HDInsight kapcsolatot. Telepítenie kell az alkalmazást, amelyek használják az ODBC-illesztőprogram verziójának megfelelő verziót. Ebben az oktatóanyagban az Office Excel a-illesztőprogram van alkalmazva.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása
A következő lépések bemutatják egy Hive ODBC-adatforrás létrehozására.

1. A Windows 8 vagy Windows 10, megnyitása a kezdőképernyőről a Windows billentyű megnyomása után írja be **adatforrások**.
2. Kattintson a **ODBC adatforrások (32 bites) beállítása** vagy **ODBC adatforrások (64 bites) beállítása** az Office verziójától függően. Ha Windows 7 használ, válassza a **ODBC adatforrások (32 bites)** vagy **ODBC adatforrások (64 bites)** a **felügyeleti eszközök**. Ekkor megjelenik a **ODBC-adatforrás felügyelő** párbeszédpanel.
   
    ![Adatforrás-felügyelő OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "adatforrás-felügyelő ODBC Adatforrásnevet konfigurálása")

3. A felhasználó DNS-ből, kattintson a **Hozzáadás** megnyitásához a **új adatforrás létrehozása** varázsló.
4. Válassza ki **Microsoft Hive ODBC-illesztő**, és kattintson a **Befejezés**. Ekkor megjelenik a **Microsoft Hive ODBC illesztőprogram DNS beállítások** párbeszédpanel.
5. Írja be vagy válassza ki az alábbi értékeket:
   
   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Gazdagép |Írja be a következő kifejezést: &lt;HDInsightClusterName>.azurehdinsight.net. Például: sajatHDICluster.azurehdinsight.net |
   |  Port |Használja a <strong>443</strong> számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
   |  Adatbázis |Használja az <strong>Alapértelmezett</strong> adatbázist. |
   |  Mechanizmus |Válassza ki az <strong>Azure HDInsight szolgáltatást</strong> |
   |  Felhasználónév |Adja meg a HDInsight fürt HTTP felhasználó felhasználóneve. Az alapértelmezett felhasználónév az <strong>admin</strong>. |
   |  Jelszó |A HDInsight fürt felhasználói jelszó. |
   
    </table>
   
    Néhány fontos paramétert kell ismernie, ha **speciális beállítások**:
   
   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC illesztőprogram nem próbálja TSQL átalakítása HiveQL. Azt kell használni, csak ha 100 %-os meg arról, hogy a tiszta HiveQL utasítások küld. Ha a csatlakozás az SQL Server vagy az Azure SQL Database, akkor hagyja bejelölve. |
   |  / Blokk lehívott sorok száma |A rekordok nagy száma lehívásakor hangolása ennek a paraméternek, előfordulhat, hogy kell optimális teljesítményének biztosítása. |
   |  Alapértelmezett karakterlánc oszlop hossza, a bináris oszlop, decimális oszlop méretezési |Adattípus-hosszúságok és szükséges hatással lehet, hogy adatokat ad vissza. Pontosság és/vagy csonkolása ered vissza kell adni a megfelelő információkat okoznak. |

    ![Speciális beállítások](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN speciális konfigurációs beállítások")

1. Kattintson a **tesztelése** tesztelése az adatforráshoz. Az adatforrás megfelelően van konfigurálva, mutat *teszt sikeresen befejeződött!*.
2. Kattintson a **OK** a teszt párbeszédpanel bezárásához. Az új adatforrás fel kell sorolni a **ODBC-adatforrás felügyelő**.
3. Kattintson a **OK** a varázslóból való kilépéshez.

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból
A következő lépések bemutatják a Hive tábla adatokat importálhat egy Excel-munkafüzet az ODBC-adatforrás az előző szakaszban létrehozott használata módja.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.
2. Az a **adatok** lapra, majd **adatok beolvasása**, kattintson a **egyéb forrásokból származó**, és kattintson a **az ODBC** elindíthatja a **Adatkapcsolat varázsló**.
   
    ![Nyissa meg az Adatkapcsolat varázsló](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "megnyitott kapcsolat varázsló")
4. Válassza ki az adatforrás nevét, amely az utolsó szakaszban létrehozott, és kattintson a **OK**.
5. Adja meg (az alapértelmezett név az admin) Hadoop felhasználónevét és jelszavát, és kattintson **Connect**.
6. A Navigátor, bontsa ki **HIVE**, bontsa ki a **alapértelmezett**, kattintson **hivesampletable**, és kattintson a **terhelés**. Az adatok importálása az Excelbe néhány másodpercet vesz igénybe.

    ![HDInsight Hive ODBC navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "megnyitott kapcsolat varázsló")


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használható a Microsoft Hive ODBC-illesztőprogram adatainak lekérése a HDInsight-szolgáltatás Excelbe. Ehhez hasonlóan is visszaállíthatja az adatokat a HDInsight szolgáltatásból az SQL-adatbázisba. Akkor is feltölteni az adatokat egy HDInsight-szolgáltatásba. További tudnivalókért lásd:

* [Hive-adatok ábrázolása a Microsoft Power bi-ban az Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Interaktív lekérdezés Hive-adatok ábrázolása a Power bi-ban az Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Zeppelin segítségével az Azure HDInsight Hive-lekérdezések futtatása ](./../hdinsight-connect-hive-zeppelin.md).
* [Az Excel és a Hadoop csatlakoztatása a Power Query használatával](apache-hadoop-connect-excel-power-query.md).
* [Azure HDInsight csatlakozzon és Hive-lekérdezéseket a Data Lake Tools for Visual Studio használatával futtassa](apache-hadoop-visual-studio-tools-get-started.md).
* [A HDInsight eszközzel Azure a Visual Studio Code](../hdinsight-for-vscode.md).
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


