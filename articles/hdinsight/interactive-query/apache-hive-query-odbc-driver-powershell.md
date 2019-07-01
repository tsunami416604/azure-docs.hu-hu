---
title: Lekérdezés az Apache Hive ODBC-illesztőt és PowerShell – Azure HDInsight
description: A Microsoft Hive ODBC-illesztőt használja, és a PowerShell-lel az Apache Hive-lekérdezést az Azure HDInsight-fürtök.
keywords: Hive, a hive odbc, a powershell
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: hrasheed
ms.openlocfilehash: b02c865e953861b5ac396538fdd0f0623b0e5428
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486101"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Oktatóanyag: Lekérdezés az Apache Hive ODBC és a PowerShell segítségével

Microsoft ODBC-illesztőprogramok kommunikál a különböző típusú adatforrásokat, többek között az Apache Hive rugalmas lehetőséget biztosíthat. A PowerShell parancsfájl-kezelési nyelvet, amely megnyit egy kapcsolatot a Hive-fürthöz, szabadon lekérdezés adja át az ODBC-illesztőprogramok használatával kód írása, és megjeleníti az eredményeket.

Ebben az oktatóanyagban teheti meg a következő feladatokat:

> [!div class="checklist"]
> * Töltse le és telepítse a Microsoft Hive ODBC-illesztő
> * A fürthöz társított egy Apache Hive ODBC-adatforrás létrehozása
> * Lekérdezés minta adatait a fürthöz PowerShell használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* Egy HDInsight interaktív lekérdezési fürtöt. Hozzon létre egyet, tekintse meg [Azure HDInsight – első lépések](../hdinsight-hadoop-provision-linux-clusters.md). Válassza ki **interaktív lekérdezés** , a fürt típusát.

## <a name="install-microsoft-hive-odbc-driver"></a>A Microsoft Hive ODBC-illesztőprogram telepítése

Töltse le és telepítse a [a Microsoft Hive ODBC-illesztő](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Az Apache Hive ODBC-adatforrás létrehozása

A következő lépések bemutatják, hogyan hozhat létre egy Apache Hive ODBC-adatforrásból.

1. Lépjen a Windows, **Start** > **Windows felügyeleti eszközök** > **ODBC adatforrások (32-bit)/(64-bit)** .  Egy **ODBC Data Source Administrator** ablak nyílik meg.

    ![OBDC az adatforrás rendszergazdájához](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "ODBC Data Source Administrator használatával Adatforrásnevet konfigurálása")

1. Az a **felhasználói DSN** lapon jelölje be **Hozzáadás** megnyitásához a **új adatforrás létrehozása** ablak.

1. Válassza ki **a Microsoft Hive ODBC-illesztő**, majd válassza ki **Befejezés** megnyitásához a **a Microsoft Hive ODBC DSN illesztőinek** ablak.

1. Írja be vagy válassza ki az alábbi értékeket:

   | Tulajdonság | Leírás |
   | --- | --- |
   |  Adatforrás neve |Adjon nevet az adatforrásának |
   |  Host(s) |Írja be a `CLUSTERNAME.azurehdinsight.net` (igen) kifejezést. Például: `myHDICluster.azurehdinsight.net` |
   |  Port |Használja a **443** számú portot.|
   |  Adatbázis |Használat **alapértelmezett**. |
   |  Mechanizmus |Válassza ki **Windows Azure HDInsight szolgáltatást** |
   |  Felhasználónév |Adja meg a HDInsight fürt HTTP-felhasználó felhasználóneve. Az alapértelmezett felhasználónév az **admin**. |
   |  Jelszó |Adja meg a HDInsight-fürt felhasználói jelszót. Jelölje be a **(titkosított) jelszó mentése**.|

1. Nem kötelező: Válassza ki **speciális beállítások**.  

   | Paraméter | Leírás |
   | --- | --- |
   |  Natív lekérdezés használata |Ha be van jelölve, az ODBC-illesztő nem meg HiveQL TSQL alakíthatja. Használja ezt a beállítást, csak ha 100 %-os arról, hogy beküld tiszta HiveQL utasításokat. Ha csatlakozik az SQL Server vagy az Azure SQL Database, akkor hagyja bejelölve. |
   |  Egy blokk lehívott sorok száma |Ha nagy számú rekord beolvasása, hangolása ezt a paramétert fiókdíjat optimális teljesítményének biztosítása érdekében. |
   |  Alapértelmezett karakterlánc oszlop hossza, a bináris oszlop hossza, a decimális oszlop skála |Az adattípus hosszúságok és hatással lehet szükséges, milyen adatokat ad vissza. Helytelen adatokat vissza kell az pontosság csökkenése és csonkolása miatt azok kárt. |

    ![Speciális beállítások](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "DSN speciális konfigurációs lehetőségek")

1. Válassza ki **tesztelése** teszteléséhez az adatforrás. Ha az adatforrás megfelelően van konfigurálva, a teszt eredménye látható **sikeres**.  

1. Válassza ki **OK** a Teszt ablak bezárásához.  

1. Válassza ki **OK** gombra kattintva zárja be a **a Microsoft Hive ODBC DSN illesztőinek** ablak.  

1. Válassza ki **OK** gombra kattintva zárja be a **ODBC Data Source Administrator** ablak.  

## <a name="query-data-with-powershell"></a>Adatok lekérdezése a PowerShell-lel

A következő PowerShell-parancsfájlt az egy függvényt, hogy ODBC lekérdezni egy Hive-fürtöt.

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

A következő kódrészletet használja a fenti funkciót hajthat végre egy lekérdezést az interaktív lekérdezési fürt, az oktatóanyag kezdetén létrehozott. Cserélje le `DATASOURCENAME` együtt a **adatforrás neve** a megadott a **a Microsoft Hive ODBC DSN illesztőinek** képernyő. Amikor a rendszer kéri a hitelesítő adatokat, adja meg a felhasználónevet és jelszót, amely alatt a megadott **fürt bejelentkezési felhasználóneve** és **fürt bejelentkezési jelszavának** a fürt létrehozásakor.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a HDInsight-fürt és a storage-fiókot. Ehhez válassza ki az erőforráscsoportot, amelyben a fürt létrejött, és kattintson a **törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Microsoft Hive ODBC-illesztőt és a PowerShell-adatokat lekérni az Azure HDInsight interaktív lekérdezési fürt.

> [!div class="nextstepaction"]
> [Az Excel összekapcsolása a Apache Hive ODBC segítségével](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
