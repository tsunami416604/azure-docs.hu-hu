---
title: Hadoop - az Azure HDInsight az Apache Sqoop |} Microsoft Docs
description: Útmutató Apache Sqoop használatával importálása és exportálása a HDInsight Hadoop és egy Azure SQL-adatbázis között.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 38b1c57b9ac666bc908df69b29f72fbd5aea0495
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31403909"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop használatával a HDInsight Hadoop és SQL-adatbázis közötti adatok importálása és exportálása

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Útmutató Apache Sqoop használatával importálhat és exportálhat az Azure HDInsight Hadoop-fürthöz, és az Azure SQL Database vagy Microsoft SQL Server adatbázis között. A lépéseket, ez a dokumentum használatát a `sqoop` közvetlenül a Hadoop-fürt headnode parancsot. SSH segítségével a átjárócsomópontjához csatlakozik, és futtassa a parancsokat ebben a dokumentumban.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a HDInsight-fürtök Linux használó dolgozhat. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> A jelen dokumentumban leírt lépések azt feltételezik, hogy már létrehozta az Azure SQL Database nevű `sqooptest`.
>
> Ez a dokumentum a T-SQL utasítás létrehozásához és az SQL-adatbázis egy táblából használt nyújt. Használható ezekre az utasításokra az SQL Database sok ügyfél van. Azt javasoljuk, hogy a következő ügyfelekre:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * A [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) segédprogram

## <a name="create-the-table-in-sql-database"></a>A tábla az SQL-adatbázis létrehozása

> [!IMPORTANT]
> Ha a HDInsight-fürtöt használ, és SQL-adatbázis létrehozása [fürt és az SQL-adatbázis létrehozása](hdinsight-use-sqoop.md), hagyja ki a jelen szakaszban szereplő lépéseket. Az adatbázis és tábla lépéseit részeként jöttek létre a [fürt és az SQL-adatbázis létrehozása](hdinsight-use-sqoop.md) dokumentum.

Egy SQL-ügyfél használatával csatlakozhat a `sqooptest` adatbázis az SQL-adatbázisban. A következő T-SQL használatával hozzon létre egy táblát nevű `mobiledata`:

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

## <a name="sqoop-export"></a>Sqoop exportálása

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Például a következő parancs csatlakozik-e a fürt nevű elsődleges headnode `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Győződjön meg arról, hogy a Sqoop látja-e az SQL-adatbázis, használja a következő parancsot:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Amikor a rendszer kéri, adja meg a jelszót az SQL adatbázis-bejelentkezési adatokat.

    Ez a parancs visszaadja adatbázislistáinak, beleértve a **sqooptest** korábban használt adatbázist.

3. Adatok exportálása a Hive **hivesampletable** a tábla a **mobiledata** tábla az SQL-adatbázis, a következő paranccsal:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Győződjön meg arról, hogy az adatok exportálták, használja az SQL-ügyfél a következő lekérdezés az exportált adatok megtekintéséhez:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Ez a parancs felsorolja a táblába importált 50 sor.

## <a name="sqoop-import"></a>Sqoop importálása

1. Az alábbi parancs segítségével adatokat importálni a **mobiledata** a tábla az SQL-adatbázis, a **wasb: / / / oktatóanyagok/usesqoop/importeddata** HDInsight könyvtárába:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Az adatok mezőinek tabulátor választják el, és a sorok egy új sor karakter megszűnik.

    > [!IMPORTANT]
    > A `wasb:///` elérési út az alapértelmezett fürt tárolóként Azure Storage használó fürtök működik. Az Azure Data Lake Store használó fürtök esetén használjon `adl:///` helyette.

2. Az importálás befejezése után a következő paranccsal kimenő a listára új könyvtárban:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>SQL Server használata

Sqoop használatával adatok importálása és exportálása az SQL-kiszolgálóról. Az SQL Database és SQL Server közötti különbségek a következők:

* HDInsight és az SQL Server azonos Azure virtuális hálózaton kell lennie.

    Egy vonatkozó példáért lásd: a [a helyszíni hálózathoz való csatlakozás HDInsight](./../connect-on-premises-network.md) dokumentum.

    A HDInsight használata az Azure virtuális hálózat további információkért lásd: a [kiterjesztése HDInsight az Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentum. Azure-beli virtuális hálózatra vonatkozó további információkért lásd: a [virtuális hálózat áttekintése](../../virtual-network/virtual-networks-overview.md) dokumentum.

* SQL Server SQL-hitelesítés engedélyezéséhez meg kell adni. További információkért lásd: a [válassza ki a kívánt hitelesítési mód](https://msdn.microsoft.com/ms144284.aspx) dokumentum.

* Előfordulhat, hogy az SQL Server távoli kapcsolatokat fogadjon konfigurálása. További információkért lásd: a [hibaelhárítása a Kapcsolódás az SQL Server adatbázismotor](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentum.

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

* Ha az SQL Server a HDInsight-ból, előfordulhat, hogy az SQL Server az IP-cím használatára. Példa:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Korlátozások

* Tömeges export - a Linux-alapú HDInsight, a Sqoop összekötő segítségével az adatok exportálása Microsoft SQL Server vagy az Azure SQL Database nem támogatja a tömeges beszúrások.

* Kötegelés - és a Linux-alapú HDInsight együttes használata esetén a `-batch` beszúrása végrehajtásakor kapcsoló, a Sqoop lehetővé teszi több beszúrás helyett a beszúrási műveletek kötegelése.

## <a name="next-steps"></a>További lépések

Most megtanulhatta, hogyan használható a Sqoop. További tudnivalókért lásd:

* [Oozie használata a HDInsight](../hdinsight-use-oozie.md): egy Oozie munkafolyamat használja Sqoop műveletét.
* [HDInsight eszközzel repülési késleltetés adatok elemzése](../hdinsight-analyze-flight-delay-data.md): használja struktúra elemzése repülési késleltetés az adatok, és a Sqoop segítségével exportál adatokat az Azure SQL-adatbázis.
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md): található adatok feltöltése a HDInsight vagy az Azure Blob storage más módszerrel.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
