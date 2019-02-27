---
title: Csatlakoztathatja az Excelt a Hive ODBC-illesztő – Azure HDInsight az Apache Hadoop
description: Ismerje meg, hogyan beállítása és használata a Microsoft Hive ODBC-illesztő az Excel használatával adatokat lekérdezni a HDInsight-fürtök a Microsoft Excel-fájlból.
keywords: hadoop excel-, hive-excel-, hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: hrasheed
ms.openlocfilehash: fe1ac091b1aecc6f6242cdeaf6b886bf67d2a91b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56878234"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Excel csatlakoztatása az Azure HDInsight az Apache Hadoop és a Microsoft Hive ODBC-illesztő

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

A Microsoft Big Data-megoldás a Microsoft üzleti intelligencia (BI) összetevők integrálható az alkalmazott Azure HDInsight az Apache Hadoop-fürtöket. Ilyen például, ezt az integrációt, lehetővé teszi az Excel összekapcsolása a Hive data warehouse-ba, a Microsoft Hive megnyitott adatbázis kapcsolat (ODBC illesztőprogrammal) HDInsight egy Hadoop-fürtöt.

Az is lehet kapcsolódni az adatokat egy HDInsight-fürt és az egyéb adatforráshoz, beleértve a más (nem HDInsight) Hadoop-fürtök, az Excel használatával a Microsoft Power Query beépülő modul az Excel programhoz társított. Telepítés és a Power Query használatával kapcsolatos tudnivalókat lásd: [Excel csatlakoztatása a HDInsight a Power Query][hdinsight-power-query].


## <a name="prerequisites"></a>Előfeltételek

Ez a cikk elkezdéséhez a következőkkel kell rendelkeznie:

* Egy HDInsight Hadoop-fürtöt. Hozzon létre egyet, tekintse meg [Azure HDInsight – első lépések](apache-hadoop-linux-tutorial-get-started.md).
* Egy munkaállomás, Office 2010 Professional Plus rendszert vagy újabb, vagy az Excel 2010 vagy újabb.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft Hive ODBC-illesztőprogram telepítése
Töltse le és telepítse a [a Microsoft Hive ODBC-illesztő] [ hive-odbc-driver-download] olyanra, amely megfelel az alkalmazás hol fogja használni az ODBC-illesztő verziójának.  Ebben az oktatóanyagban az illesztőprogram Office Excel szolgál.

## <a name="create-apache-hive-odbc-data-source"></a>Az Apache Hive ODBC-adatforrás létrehozása
A következő lépések bemutatják, hogyan hozhat létre egy Hive ODBC-adatforrásból.

1. A Windows, navigáljon a Start > Windows felügyeleti eszközök > ODBC adatforrások (32-bit)/(64-bit).  Ekkor megnyílik a **ODBC Data Source Administrator** ablak.
   
    ![OBDC az adatforrás rendszergazdájához](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "ODBC Data Source Administrator használatával Adatforrásnevet konfigurálása")

2. Az a **felhasználói DSN** lapon jelölje be **Hozzáadás** megnyitásához a **új adatforrás létrehozása** ablak.

3. Válassza ki **a Microsoft Hive ODBC-illesztő**, majd válassza ki **Befejezés** megnyitásához a **a Microsoft Hive ODBC DSN illesztőinek** ablak.

4. Írja be vagy válassza ki az alábbi értékeket:
   
   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Host(s) |Adja meg &lt;HDInsightClusterName&gt;. azurehdinsight.NET formátumban van. Például: sajatHDICluster.azurehdinsight.net |
   |  Port |Használja a <strong>443</strong> számú portot. (Ez a port megváltozott a 563-ról 443-ra.) |
   |  Adatbázis |Használat <strong>alapértelmezett</strong>. |
   |  Mechanizmus |Válassza ki az <strong>Azure HDInsight szolgáltatást</strong> |
   |  Felhasználónév |Adja meg a HDInsight fürt HTTP-felhasználó felhasználóneve. Az alapértelmezett felhasználónév az <strong>admin</strong>. |
   |  Jelszó |Adja meg a HDInsight-fürt felhasználói jelszót. |

   
5. Nem kötelező: Válassza ki **speciális beállítások...**  
   
   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC-illesztő nem meg HiveQL TSQL alakíthatja. Azt kell használni, csak ha 100 %-os meg arról, hogy a tiszta HiveQL utasítások küld be. Ha csatlakozik az SQL Server vagy az Azure SQL Database, akkor hagyja bejelölve. |
   |  Egy blokk lehívott sorok száma |Ha nagy számú rekord beolvasása, hangolása ezt a paramétert fiókdíjat optimális teljesítményének biztosítása érdekében. |
   |  Alapértelmezett karakterlánc oszlop hossza, a bináris oszlop hossza, a decimális oszlop skála |Az adattípus hosszúságok és hatással lehet szükséges, milyen adatokat ad vissza. Helytelen adatokat és a pontosság / csonkolása miatt adja vissza azok kárt. |

    ![Speciális beállítások](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN speciális konfigurációs lehetőségek")

5. Válassza ki **tesztelése** teszteléséhez az adatforrás. Ha az adatforrás megfelelően van konfigurálva, a teszt eredménye látható **sikeres!**.  

6. Válassza ki **OK** a Teszt ablak bezárásához.  

7. Válassza ki **OK** gombra kattintva zárja be a **a Microsoft Hive ODBC DSN illesztőinek** ablak.  

8. Válassza ki **OK** gombra kattintva zárja be a **ODBC Data Source Administrator** ablak.  

## <a name="import-data-into-excel-from-hdinsight"></a>Adatok importálása Excel formátumba a HDInsight-ból
Az alábbi lépések bemutatják az adatokat importálhat egy Excel-munkafüzet, amely az előző szakaszban létrehozott ODBC-adatforrásból használatával Hive-tábla módja.

1. Nyisson meg egy új vagy egy meglévő munkafüzetet Excelben.

2. Az a **adatok** lapot, navigáljon a **adatok lekérése** > **egyéb forrásokból származó** > **származó ODBC** elindíthatja a **Származó ODBC** ablak.
   
    ![Adatkapcsolat varázsló megnyitása](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Adatkapcsolat varázsló megnyitása")

3. A legördülő listából válassza ki az adatforrás nevét, amely az előző szakaszban létrehozott majd **OK**.

4. Adja meg (az alapértelmezett név az admin) a Hadoop-felhasználónevet és jelszót, és válassza ki **Connect** megnyitásához a **kezelő** ablak.

5. A **kezelő**, navigáljon a **HIVE** > **alapértelmezett** > **hivesampletable**, majd válassza ki a  **Betöltés**. Az adatok importálása Excelbe néhány percet vesz igénybe.

    ![HDInsight Hive ODBC-kezelő](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Adatkapcsolat varázsló megnyitása")

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett a HDInsight szolgáltatás az Excel-adatokat lekérni a Microsoft Hive ODBC-illesztő használatával. Ehhez hasonlóan lehet lekérdezni adatokat a HDInsight szolgáltatás az SQL Database-be. Akkor is feltölthet adatokat egy HDInsight szolgáltatásba. További tudnivalókért lásd:

* [Az Apache Hive-adatok vizualizálása a Microsoft Power bi-ban az Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Power BI segítségével az Azure HDInsight adatok interaktív lekérdezéses Hive megjelenítése](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Az Apache a Zeppelin használata Azure HDInsight az Apache Hive-lekérdezések futtatásához](./../hdinsight-connect-hive-zeppelin.md).
* [Excel Power Query használatával csatlakozni az Apache Hadoop](apache-hadoop-connect-excel-power-query.md).
* [Csatlakozás az Azure HDInsight és a Data Lake Tools for Visual Studio használatával, az Apache Hive-lekérdezések futtatása](apache-hadoop-visual-studio-tools-get-started.md).
* [Az Azure HDInsight-eszköz használata a Visual Studio Code](../hdinsight-for-vscode.md).
* [Adatok feltöltése a HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


