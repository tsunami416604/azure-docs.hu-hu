---
title: Az Apache Hadoop – Azure HDInsight az Apache sqoop használatával
description: Ismerje meg, hogyan Apache Sqoop használatával importálása és exportálása a HDInsight Apache Hadoop és a egy Azure SQL Database között.
keywords: hadoop sqoop, a sqoop használatával
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: d7df1c65b8588b97a6beb0a4c2428b3c6430c3b2
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635697"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Apache Hadoop on HDInsight és az SQL Database közötti adatok importálása és exportálása az Apache Sqoop használatával

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan használhatja az Apache Sqoop importálása és exportálása az Azure HDInsight az Apache Hadoop-fürt és Azure SQL Database vagy a Microsoft SQL Server-adatbázis közötti. A lépéseket, a jelen dokumentum-használat a `sqoop` közvetlenül a Hadoop-fürt az átjárócsomóponthoz parancsot. Az SSH használatával csatlakozhat az átjárócsomóponthoz, és futtassa a parancsokat ebben a dokumentumban.

> [!IMPORTANT]  
> A jelen dokumentumban leírt lépések Linux használó HDInsight-fürtök csak dolgozhat. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]  
> Ez a dokumentum lépései azt feltételezik, hogy már létrehozott egy Azure SQL Database nevű `sqooptest`.
>
> A dokumentum létrehozása és lekérdezése az SQL Database egyik táblájába használt T-SQL-utasításokkal szolgál. Vannak, ezek az utasítások használható az SQL Database számos ügyfél. Azt javasoljuk, hogy a következő ügyfelekre:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * A [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) segédprogram

## <a name="create-the-table-in-sql-database"></a>A tábla létrehozása az SQL Database-ben

> [!IMPORTANT]  
> Ha a HDInsight-fürtöt használ, és az SQL-adatbázis létrehozása [létrehozása a fürt és az SQL database](hdinsight-use-sqoop.md), hagyja ki a jelen szakaszban ismertetett lépéseket. A lépések részeként létrehozott az adatbázis és tábla a [létrehozása a fürt és az SQL database](hdinsight-use-sqoop.md) dokumentumot.

Egy SQL-ügyfél használatával kapcsolódhat a `sqooptest` az SQL Database-adatbázis. A következő T-SQL használatával hozzon létre egy táblát nevű `mobiledata`:

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Exportálása a sqoop használatával

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Ha például a következő parancs csatlakozik-e nevű fürt elsődleges átjárócsomópontjával `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Annak ellenőrzéséhez, hogy látja-e az SQL-adatbázis a Sqoop, a következő paranccsal:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Amikor a rendszer kéri, adja meg az SQL Database bejelentkezés jelszavát.

    Ez a parancs az adatbázisok, beleértve a listáját adja vissza a **sqooptest** korábban használt adatbázist.

3. Adatok exportálása a Hive **hivesampletable** táblázatból a **mobiledata** SQL Database-ben táblában, használja a következő parancsot:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Győződjön meg arról, hogy történt adatexportálás, az exportált adatok megtekintéséhez használja az SQL-ügyfél a következő lekérdezést:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Ez a parancs felsorolja a táblába a korábban importált 50 sorból.

## <a name="sqoop-import"></a>Sqoop-importálás

1. A következő paranccsal importálja az adatokat a **mobiledata** tábla az SQL Database-ben a **wasb: / / / oktatóprogramok/usesqoop/importeddata** HDInsight könyvtárába:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Az adatok a mezők tabulátorkarakter el egymástól, és a sorokat egy új sor karakter megszűnik.

    > [!IMPORTANT]  
    > A `wasb:///` elérési út működik együtt az Azure Storage, az alapértelmezett fürttárolóhoz használó fürtök. Az Azure Data Lake Storage használó fürtök esetén használjon `adl:///` helyette.

2. Az importálás befejezése után használja az új címtárban adatok listája a következő parancsot:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Az SQL Server használata

A Sqoop használatával adatok importálása és exportálása az SQL Serverből. Az SQL Database és SQL Server használata közötti különbségek a következők:

* HDInsight és az SQL Server azonos Azure virtuális hálózaton kell lennie.

    Egy vonatkozó példáért tekintse meg a [HDInsight csatlakoztatása a helyszíni hálózathoz](./../connect-on-premises-network.md) dokumentumot.

    A HDInsight használata az Azure Virtual Network további információkért lásd: a [HDInsight kiterjesztése az Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentumot. Az Azure Virtual Network további információkért lásd: a [a Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md) dokumentumot.

* SQL-hitelesítés engedélyezése az SQL Server kell konfigurálni. További információkért lásd: a [hitelesítési mód kiválasztását](https://msdn.microsoft.com/ms144284.aspx) dokumentumot.

* Előfordulhat, hogy kell konfigurálnia az SQL Server távoli kapcsolatokat fogadjon. További információkért lásd: a [az SQL Server adatbázismotorral való csatlakozás hibaelhárítása](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentumot.

* A következő Transact-SQL-utasítások létrehozásához használja a **mobiledata** tábla:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Történő csatlakozás az SQL Server HDInsight, akkor előfordulhat, hogy SQL-kiszolgáló IP-címet használja. Példa:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Korlátozások

* Tömeges exportálása – a Linux-alapú HDInsight, a használt Microsoft SQL Server vagy az Azure SQL Database-adatok exportálása a Sqoop-összekötő nem támogatja a tömeges beszúrás.

* Kötegelés – a Linux-alapú HDInsight használatakor a `-batch` Beszúrások végrehajtásakor váltson, a Sqoop, lehetővé teszi több beszúrás helyett a beszúrási műveletek kötegelése.

## <a name="next-steps"></a>További lépések

Most már megtanulhatta, hogyan használható a sqoop használatával. További tudnivalókért lásd:

* [Az Apache Oozie használata a HDInsight](../hdinsight-use-oozie.md): Az Oozie-munkafolyamatokkal Sqoop műveletet használja.
* [HDInsight használatával repülőjáratok késési adatainak elemzése](../hdinsight-analyze-flight-delay-data.md): Repülőjáratok késési adatainak elemzése az Apache Hive használatával, és a Sqoop használatával egy Azure SQL database-adatok exportálása.
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md): Keresse meg a HDInsight vagy az Azure Blob storage-ba történő feltöltéséhez más módszerekkel.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
