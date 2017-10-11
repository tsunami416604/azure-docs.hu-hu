---
title: Hadoop - az Azure HDInsight az Apache Sqoop |} Microsoft Docs
description: "Útmutató Apache Sqoop használatával importálása és exportálása a HDInsight Hadoop és egy Azure SQL-adatbázis között."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: larryfr
ms.openlocfilehash: 35dcbb91e6af1480685c9fd5b829c54277c1c605
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop használatával a HDInsight Hadoop és SQL-adatbázis közötti adatok importálása és exportálása

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Útmutató Apache Sqoop használatával importálhat és exportálhat az Azure HDInsight Hadoop-fürthöz, és az Azure SQL Database vagy Microsoft SQL Server adatbázis között. A lépéseket, ez a dokumentum használatát a `sqoop` közvetlenül a Hadoop-fürt headnode parancsot. SSH segítségével a átjárócsomópontjához csatlakozik, és futtassa a parancsokat ebben a dokumentumban.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések csak a HDInsight-fürtök Linux használó dolgozhat. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="install-freetds"></a>FreeTDS telepítése

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Például a következő parancs csatlakozik-e a fürt nevű elsődleges headnode `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. A következő paranccsal telepítse FreeTDS:

    ```bash
    sudo apt --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS számos lépést szerepel SQL adatbázishoz való kapcsolódáshoz.

## <a name="create-the-table-in-sql-database"></a>A tábla az SQL-adatbázis létrehozása

> [!IMPORTANT]
> Ha a HDInsight-fürtöt használ, és SQL-adatbázis létrehozása [fürt és az SQL-adatbázis létrehozása](hdinsight-use-sqoop.md), hagyja ki a jelen szakaszban szereplő lépéseket. Az adatbázis és tábla lépéseit részeként jöttek létre a [fürt és az SQL-adatbázis létrehozása](hdinsight-use-sqoop.md) dokumentum.

1. Az SSH-munkamenetet használja a következő parancsot az SQL Database-kiszolgálóhoz való kapcsolódáshoz.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    A kimenet az alábbihoz hasonló jelenhet meg:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. : A `1>` kéri, adja meg a következő lekérdezést:

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

    Ha a `GO` utasításban is meg kell adni, az előző utasítások kiértékelése. Első, a **mobiledata** tábla létrehozása, majd egy fürtözött index hozzáadni (SQL-adatbázis szükséges.)

    A következő lekérdezés segítségével győződjön meg arról, hogy a tábla jött létre:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    A kimenet az alábbihoz hasonló jelenik meg:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. Adja meg `exit` , a `1>` Rákérdezés a tsql segédprogram kilép.

## <a name="sqoop-export"></a>Sqoop exportálása

1. Az SSH-kapcsolat a fürthöz ellenőrizheti, hogy a Sqoop látja-e az SQL-adatbázis alkalmazás a következő parancsot:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Amikor a rendszer kéri, adja meg a jelszót az SQL adatbázis-bejelentkezési adatokat.

    Ez a parancs visszaadja adatbázislistáinak, beleértve a **sqooptest** korábban létrehozott adatbázis.

2. Az exportálandó adatokat **hivesampletable** számára a **mobiledata** table, használja a következő parancsot:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    Ez a parancs utasítja a Sqoop való kapcsolódáshoz a **sqooptest** adatbázis. Sqoop majd exportálja az adatokat a **wasb: / / / hive/adatraktár/hivesampletable** számára a **mobiledata** tábla.

    > [!IMPORTANT]
    > Használjon `wasb:///` Ha az alapértelmezett tároló a fürt számára egy Azure Storage-fiók. Használjon `adl:///` egy Azure Data Lake Store esetén.

3. A parancs befejezése után a következő paranccsal a TSQL használatával adatbázishoz való kapcsolódáshoz:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P -p 1433 -D sqooptest
    ```

    A csatlakozás után használja az alábbi utasításokat győződjön meg arról, hogy a korábban exportált adatok a **mobiledata** tábla:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata
    GO
    ```

    Meg kell jelennie a tábla adatainak listáját. Típus `exit` való kilépéshez a tsql segédprogramot.

## <a name="sqoop-import"></a>Sqoop importálása

1. Az alábbi parancs segítségével adatokat importálni a **mobiledata** a tábla az SQL-adatbázis, a **wasb: / / / oktatóanyagok/usesqoop/importeddata** HDInsight könyvtárába:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Az adatok mezőinek tabulátor választják el, és a sorok egy új sor karakter megszűnik.

2. Az importálás befejezése után a következő paranccsal kimenő a listára új könyvtárban:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>SQL Server használata

Sqoop használatával adatok importálása és exportálása az SQL Server, vagy az adatközpont, vagy az Azure-ban futó virtuális gépen. Az SQL Database és SQL Server közötti különbségek a következők:

* HDInsight és az SQL Server azonos Azure virtuális hálózaton kell lennie.

    Egy vonatkozó példáért lásd: a [a helyszíni hálózathoz való csatlakozás HDInsight](./connect-on-premises-network.md) dokumentum.

    A HDInsight használata az Azure virtuális hálózat további információkért lásd: a [kiterjesztése HDInsight az Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) dokumentum. Azure-beli virtuális hálózatra vonatkozó további információkért lásd: a [virtuális hálózat áttekintése](../virtual-network/virtual-networks-overview.md) dokumentum.

* SQL Server SQL-hitelesítés engedélyezéséhez meg kell adni. További információkért lásd: a [válassza ki a kívánt hitelesítési mód](https://msdn.microsoft.com/ms144284.aspx) dokumentum.

* Előfordulhat, hogy az SQL Server távoli kapcsolatokat fogadjon konfigurálása. További információkért lásd: a [hibaelhárítása a Kapcsolódás az SQL Server adatbázismotor](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentum.

* Hozzon létre a **sqooptest** egy segédprogram segítségével például SQL Server adatbázis **SQL Server Management Studio** vagy **tsql**. A lépéseket az Azure parancssori felület használatával csak az Azure SQL Database működik.

    A következő Transact-SQL-utasítások létrehozásához használja a **mobiledata** tábla:

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
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Korlátozások

* Tömeges export - a Linux-alapú HDInsight, a Sqoop összekötő használt Microsoft SQL Server vagy az Azure SQL Database adatainak exportálása jelenleg nem támogatja a tömeges beszúrások.

* Kötegelés - és a Linux-alapú HDInsight együttes használata esetén a `-batch` beszúrása végrehajtásakor kapcsoló, a Sqoop lehetővé teszi több beszúrás helyett a beszúrási műveletek kötegelése.

## <a name="next-steps"></a>Következő lépések

Most megtanulhatta, hogyan használható a Sqoop. További tudnivalókért lásd:

* [Oozie használata a HDInsight][hdinsight-use-oozie]: egy Oozie munkafolyamat használja Sqoop műveletét.
* [HDInsight eszközzel repülési késleltetés adatok elemzése][hdinsight-analyze-flight-data]: használja struktúra elemzése repülési késleltetés az adatok, és a Sqoop segítségével exportál adatokat az Azure SQL-adatbázis.
* [Adatok feltöltése a HDInsight][hdinsight-upload-data]: található adatok feltöltése a HDInsight vagy az Azure Blob storage más módszerrel.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
