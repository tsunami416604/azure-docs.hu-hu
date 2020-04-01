---
title: Apache Hive lekérdezése AZ ODBC illesztőprogrammal & A PowerShelllel – Azure HDInsight
description: A Microsoft Hive ODBC illesztőprogram és a PowerShell segítségével lekérdezheti az Apache Hive-fürtöket az Azure HDInsightban.
keywords: kaptár,struktúra odbc,powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494318"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Oktatóanyag: Apache Hive lekérdezése ODBC-vel és PowerShelllel

A Microsoft ODBC-illesztőprogramok rugalmas módot biztosítanak a különböző adatforrásokkal, köztük az Apache Hive-vel való kommunikációra. Kódot írhat parancsfájlnyelveken, például a PowerShellben, amelyek az ODBC-illesztőprogramokat használják a Hive-fürthöz való kapcsolat megnyitásához, átadhat egy általa választott lekérdezést, és megjelenítheti az eredményeket.

Ebben az oktatóanyagban a következő feladatokat fogja elvégezni:

> [!div class="checklist"]
> * A Microsoft Hive ODBC illesztőprogram letöltése és telepítése
> * A fürthöz kapcsolt Apache Hive ODBC adatforrás létrehozása
> * Mintaadatok lekérdezése a fürtből a PowerShell használatával

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* Interaktív lekérdezési fürt a HDInsighton. A létrehozásról az Azure HDInsight – Első lépések című [témakörben látható.](../hdinsight-hadoop-provision-linux-clusters.md) Válassza **az Interaktív lekérdezés lehetőséget** fürttípusként.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft Hive ODBC illesztőprogram telepítése

Töltse le és telepítse a [Microsoft Hive ODBC illesztőprogramot.](https://www.microsoft.com/download/details.aspx?id=40886)

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC adatforrás létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy Apache Hive ODBC adatforrást.

1. A Windows rendszerből nyissa meg a**Windows felügyeleti eszközök** >  **indítása** > **ODBC adatforrásokat (32 bites) /(64 bites)**.  Megnyílik **egy ODBC adatforrás-felügyelő** ablak.

    ![OBDC adatforrás-rendszergazda](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "DSN konfigurálása az ODBC adatforrás-felügyelővel")

1. A **Felhasználói adatforrások** ablak **megnyitásához** válassza a **Hozzáadás** gombot.

1. Válassza a **Microsoft Hive ODBC illesztőprogram**lehetőséget, majd a **Befejezés** gombra a **Microsoft Hive ODBC illesztőprogram dsn telepítőablakának** megnyitásához.

1. Írja be vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Állomás(ok) |Írja be a `CLUSTERNAME.azurehdinsight.net` (igen) kifejezést. Például: `myHDICluster.azurehdinsight.net` |
   |  Port |Használja a **443** számú portot.|
   |  Adatbázis |Használja **az alapértelmezett**et. |
   |  Mechanizmus |**Windows Azure HDInsight szolgáltatás** kiválasztása |
   |  Felhasználónév |Írja be a HDInsight fürt HTTP-felhasználónevét. Az alapértelmezett felhasználónév az **admin**. |
   |  Jelszó |Adja meg a HDInsight fürt felhasználói jelszavát. Jelölje be a **Jelszó mentése (titkosított) jelölőnégyzetet.**|

1. Nem kötelező: Válassza a **Speciális beállítások lehetőséget**.  

   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC illesztőprogram nem próbálja meg a TSQL-t HiveQL-lé konvertálni. Csak akkor használja ezt a beállítást, ha 100%-ig biztos abban, hogy tiszta HiveQL-állításokat küld. Az SQL Server vagy az Azure SQL Database csatlakozásakor hagyja bejelölve. |
   |  Blokkonként lekért sorok |Nagy számú rekord beolvasásakor szükség lehet erre a paraméterre az optimális teljesítmény biztosításához. |
   |  Alapértelmezett karakterláncoszlop hossza, Bináris oszlophosszak, Decimális oszloplépték |Az adattípusok hossza és pontossága befolyásolhatja az adatok visszaadásának módját. A pontosság és a csonkolás elvesztése miatt helytelen adatokat ad vissza. |

    ![Speciális dsn konfigurációs beállítások](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Speciális dsn konfigurációs beállítások")

1. Válassza a **Teszt** lehetőséget az adatforrás teszteléséhez. Ha az adatforrás megfelelően van konfigurálva, a teszt eredménye a **SUCCESS parancsot**mutatja.  

1. A Teszt ablak bezárásához válassza az **OK gombot.**  

1. A **Microsoft Hive ODBC illesztőprogram dsn telepítőablakának** bezárásához válassza az **OK gombot.**  

1. Az **ODBC adatforrás-felügyelő** ablak bezárásához válassza az **OK gombot.**  

## <a name="query-data-with-powershell"></a>Adatok lekérdezése a PowerShell használatával

A következő PowerShell-parancsfájl egy olyan függvény, amely et AZ ODBC egy Hive-fürt lekérdezéséhez.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

A következő kódrészlet a fenti függvény segítségével hajt végre egy lekérdezést az oktatóanyag elején létrehozott interaktív lekérdezési fürtön. Cserélje `DATASOURCENAME` le a **Microsoft Hive ODBC illesztőprogram dsn telepítőképernyőjén** megadott **adatforrásnévre.** Amikor hitelesítő adatokat kér, adja meg a **fürt felhasználói nevének** és a **fürt bejelentkezési jelszavának** megadását a fürt létrehozásakor megadott felhasználónevet és jelszót.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a HDInsight-fürtöt és a tárfiókot. Ehhez jelölje ki azt az erőforráscsoportot, amelyben a fürtöt létrehozták, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Microsoft Hive ODBC illesztőprogramot és a PowerShellt az Azure HDInsight interaktív lekérdezési fürtből származó adatok lekéréséhez.

> [!div class="nextstepaction"]
> [Az Excel csatlakoztatása az Apache Hive-hoz az ODBC használatával](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
