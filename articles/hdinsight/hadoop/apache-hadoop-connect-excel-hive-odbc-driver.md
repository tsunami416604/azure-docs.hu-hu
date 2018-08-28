---
title: Csatlakoztathatja az Excelt a Hive ODBC-illesztő – Azure HDInsight hadoop keretrendszer
description: Ismerje meg, hogyan beállítása és használata a Microsoft Hive ODBC-illesztő az Excel használatával adatokat lekérdezni a HDInsight-fürtök a Microsoft Excel-fájlból.
keywords: hadoop excel-, hive-excel-, hive odbc
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: b21863d7a91c14f9795d72a13575e33485ba7d2b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041824"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Excel csatlakoztatása az Azure HDInsight Hadoop-a Microsoft Hive ODBC-illesztő

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A Microsoft Big Data-megoldás a Microsoft üzleti intelligencia (BI) összetevők integrálható az Apache Hadoop-fürtöket az Azure HDInsight által alkalmazott. Ilyen például, ezt az integrációt, lehetővé teszi az Excel összekapcsolása a Hive data warehouse-ba, a Microsoft Hive megnyitott adatbázis kapcsolat (ODBC illesztőprogrammal) HDInsight egy Hadoop-fürtöt.

Az is lehet kapcsolódni az adatokat egy HDInsight-fürt és az egyéb adatforráshoz, beleértve a más (nem HDInsight) Hadoop-fürtök, az Excel használatával a Microsoft Power Query beépülő modul az Excel programhoz társított. Telepítés és a Power Query használatával kapcsolatos tudnivalókat lásd: [Excel csatlakoztatása a HDInsight a Power Query][hdinsight-power-query].



**Előfeltételek**:

Ez a cikk elkezdéséhez a következőkkel kell rendelkeznie:

* **Egy HDInsight-fürt**. Hozzon létre egyet, tekintse meg [Azure HDInsight – első lépések](apache-hadoop-linux-tutorial-get-started.md).
* **Egy munkaállomás** az Office 2013 Professional Plus, Office 365 Pro Plus, az Excel 2013 Standalone vagy Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft Hive ODBC-illesztőprogram telepítése
Töltse le és telepítse a Microsoft Hive ODBC-illesztő a a [letöltőközpontból][hive-odbc-driver-download].

Az illesztőprogramot a Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 és Windows Server 2012 32 bites vagy 64 bites verzióin telepíthető. Az illesztőprogram lehetővé teszi, hogy az Azure HDInsight kapcsolatot. A verzió, amely megfelel az alkalmazás, ahol az ODBC-illesztőprogramját használja a verzióját kell telepítenie. Ebben az oktatóanyagban az illesztőprogramot használja az Office Excel-fájlból.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC-adatforrás létrehozása
A következő lépések bemutatják, hogyan hozhat létre egy Hive ODBC-adatforrásból.

1. A Windows 8 vagy Windows 10-es, a kezdőképernyőn nyissa meg a Windows billentyű lenyomásával, és írja be **adatforrások**.
2. Kattintson a **ODBC adatforrások (32 bites) beállítása** vagy **ODBC adatforrások (64 bites) beállítása** az Office-verziójától függően. Ha a Windows 7 használ, válassza a **ODBC adatforrások (32 bites)** vagy **ODBC adatforrások (64 bites)** a **felügyeleti eszközök**. Meg kell jelennie a **ODBC Data Source Administrator** párbeszédpanel.
   
    ![OBDC az adatforrás rendszergazdájához](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "ODBC Data Source Administrator használatával Adatforrásnevet konfigurálása")

3. A felhasználó DNS, kattintson az **Hozzáadás** megnyitásához a **új adatforrás létrehozása** varázsló.
4. Válassza ki **a Microsoft Hive ODBC-illesztő**, és kattintson a **Befejezés**. Meg kell jelennie a **a Microsoft Hive ODBC illesztőprogram DNS beállítások** párbeszédpanel.
5. Írja be vagy válassza ki az alábbi értékeket:
   
   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Gazdagép |Írja be a következő kifejezést: &lt;HDInsightClusterName>.azurehdinsight.net. Például: sajatHDICluster.azurehdinsight.net |
   |  Port |Használja a <strong>443</strong> számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
   |  Adatbázis |Használja az <strong>Alapértelmezett</strong> adatbázist. |
   |  Mechanizmus |Válassza ki az <strong>Azure HDInsight szolgáltatást</strong> |
   |  Felhasználónév |Adja meg a HDInsight fürt HTTP-felhasználó felhasználóneve. Az alapértelmezett felhasználónév az <strong>admin</strong>. |
   |  Jelszó |Adja meg a HDInsight-fürt felhasználói jelszót. |
   
    </table>
   
    Érdemes figyelembe vennie kattintás után néhány fontos paraméterei **speciális beállítások**:
   
   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC-illesztő nem meg HiveQL TSQL alakíthatja. Azt kell használni, csak ha 100 %-os meg arról, hogy a tiszta HiveQL utasítások küld be. Ha csatlakozik az SQL Server vagy az Azure SQL Database, akkor hagyja bejelölve. |
   |  Egy blokk lehívott sorok száma |Ha nagy számú rekord beolvasása, hangolása ezt a paramétert fiókdíjat optimális teljesítményének biztosítása érdekében. |
   |  Alapértelmezett karakterlánc oszlop hossza, a bináris oszlop hossza, a decimális oszlop skála |Az adattípus hosszúságok és hatással lehet szükséges, milyen adatokat ad vissza. Helytelen adatokat és a pontosság / csonkolása miatt adja vissza azok kárt. |

    ![Speciális beállítások](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN speciális konfigurációs lehetőségek")

1. Kattintson a **tesztelése** teszteléséhez az adatforrás. Ha az adatforrás megfelelően van konfigurálva, bemutatja *teszt sikeresen befejeződött!*.
2. Kattintson a **OK** a teszt párbeszédpanel bezárásához. Az új adatforrás fel kell sorolni a **ODBC Data Source Administrator**.
3. Kattintson a **OK** a varázslóból való kilépéshez.

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból
Az alábbi lépések bemutatják az adatokat importálhat egy Excel-munkafüzet, amely az előző szakaszban létrehozott ODBC-adatforrásból használatával Hive-tábla módja.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.
2. Az a **adatok** lapra, majd **adatok lekérése**, kattintson a **egyéb forrásokból származó**, és kattintson a **származó ODBC** elindításához a **adatok Kapcsolat varázsló**.
   
    ![Adatkapcsolat varázsló megnyitása](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Adatkapcsolat varázsló megnyitása")
4. Válassza ki az adatforrás nevét, amely az előző szakaszban létrehozott, és kattintson a **OK**.
5. Adja meg (az alapértelmezett név az admin) Hadoop-felhasználónevet és jelszót, és kattintson **Connect**.
6. A kezelőben bontsa ki **HIVE**, bontsa ki a **alapértelmezett**, kattintson a **hivesampletable**, és kattintson a **terhelés**. Az adatok importálása az Excelbe néhány másodpercet vesz igénybe.

    ![HDInsight Hive ODBC-kezelő](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Adatkapcsolat varázsló megnyitása")


## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett a HDInsight szolgáltatás az Excel-adatokat lekérni a Microsoft Hive ODBC-illesztő használatával. Ehhez hasonlóan lehet lekérdezni adatokat a HDInsight szolgáltatás az SQL Database-be. Akkor is feltölthet adatokat egy HDInsight szolgáltatásba. További tudnivalókért lásd:

* [A Microsoft Power BI az Azure HDInsight Hive-adatok vizualizálása](apache-hadoop-connect-hive-power-bi.md).
* [Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Az Azure HDInsight Hive-lekérdezések futtatása a Zeppelin használatával ](./../hdinsight-connect-hive-zeppelin.md).
* [Az Excel összekapcsolása a Hadooppal a Power Query használatával](apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával Hive-lekérdezések futtatása](apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


