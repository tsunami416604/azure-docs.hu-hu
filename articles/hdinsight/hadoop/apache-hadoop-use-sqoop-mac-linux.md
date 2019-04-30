---
title: Az Apache Hadoop – Azure HDInsight az Apache sqoop használatával
description: Ismerje meg, hogyan Apache Sqoop használatával importálása és exportálása a HDInsight Apache Hadoop és a egy Azure SQL Database között.
keywords: hadoop sqoop, a sqoop használatával
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128974"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Apache Hadoop on HDInsight és az SQL Database közötti adatok importálása és exportálása az Apache Sqoop használatával

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan használhatja az Apache Sqoop importálása és exportálása az Azure HDInsight az Apache Hadoop-fürt és Azure SQL Database vagy a Microsoft SQL Server-adatbázis közötti. A lépéseket, a jelen dokumentum-használat a `sqoop` közvetlenül a Hadoop-fürt az átjárócsomóponthoz parancsot. Az SSH használatával csatlakozhat az átjárócsomóponthoz, és futtassa a parancsokat ebben a dokumentumban. Ez a cikk a fenntartása [használata Apache sqoop használatával a HDInsight Hadoop-keretrendszerrel](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Előfeltételek

* Megvalósításának [tesztkörnyezet beállítása](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) a [használata Apache sqoop használatával a HDInsight Hadoop-keretrendszerrel](./hdinsight-use-sqoop.md).

* Egy ügyfél az Azure SQL-adatbázis lekérdezéséhez. Fontolja meg [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) vagy [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Exportálása a sqoop használatával

A Hive, az SQL Serverhez.

1. Az SSH használata a HDInsight-fürthöz való csatlakozáshoz. Cserélje le `CLUSTERNAME` a fürt nevére, majd adja meg a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Cserélje le `MYSQLSERVER` az SQL Server nevével. Annak ellenőrzéséhez, hogy látja-e az SQL-adatbázis a Sqoop, adja meg az alábbi parancsot a nyissa meg az SSH-kapcsolatot. Adja meg az SQL Server-bejelentkezés, ha a rendszer kéri a jelszót. Ez a parancs az adatbázisok listáját adja vissza.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Cserélje le `MYSQLSERVER` az SQL Server nevére, és `MYDATABASE` az SQL-adatbázis nevére. Adatok exportálása a Hive `hivesampletable` táblázatból a `mobiledata` táblát az SQL Database-ben, adja meg az alábbi parancsot a nyissa meg az SSH-kapcsolatot. Adja meg az SQL Server-bejelentkezés, ha a rendszer kéri a jelszót

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Győződjön meg arról, hogy történt adatexportálás, az exportált adatok megtekintéséhez használja a következő lekérdezéseket az SQL-ügyfélről:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop-importálás

Az SQL Serverről az Azure-tárolóba.

1. Cserélje le `MYSQLSERVER` az SQL Server nevére, és `MYDATABASE` az SQL-adatbázis nevére. Adja meg az alábbi parancsot az SSH-kapcsolatának megnyitása importálja az adatokat a `mobiledata` tábla az SQL Database-ben a `wasb:///tutorials/usesqoop/importeddata` HDInsight könyvtárába. Adja meg az SQL Server-bejelentkezés, ha a rendszer kéri a jelszót. Az adatok a mezők tabulátorkarakter el egymástól, és a sorokat egy új sor karakter megszűnik.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Az importálás befejezése után adja meg a következő parancsot a nyissa meg az SSH-kapcsolatot az új címtárban az adatok listázásához:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Korlátozások

* Tömeges exportálása – a Linux-alapú HDInsight, a használt Microsoft SQL Server vagy az Azure SQL Database-adatok exportálása a Sqoop-összekötő nem támogatja a tömeges beszúrás.

* Kötegelés – a Linux-alapú HDInsight használatakor a `-batch` Beszúrások végrehajtásakor váltson, a Sqoop, lehetővé teszi több beszúrás helyett a beszúrási műveletek kötegelése.

## <a name="important-considerations"></a>Fontos szempontok

* HDInsight és az SQL Server azonos Azure virtuális hálózaton kell lennie.

    Egy vonatkozó példáért tekintse meg a [HDInsight csatlakoztatása a helyszíni hálózathoz](./../connect-on-premises-network.md) dokumentumot.

    A HDInsight használata az Azure Virtual Network további információkért lásd: a [HDInsight kiterjesztése az Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentumot. Az Azure Virtual Network további információkért lásd: a [a Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md) dokumentumot.

* SQL-hitelesítés engedélyezése az SQL Server kell konfigurálni. További információkért lásd: a [hitelesítési mód kiválasztását](https://msdn.microsoft.com/ms144284.aspx) dokumentumot.

* Előfordulhat, hogy kell konfigurálnia az SQL Server távoli kapcsolatokat fogadjon. További információkért lásd: a [az SQL Server adatbázismotorral való csatlakozás hibaelhárítása](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) dokumentumot.

## <a name="next-steps"></a>További lépések

Most már megtanulhatta, hogyan használható a sqoop használatával. További tudnivalókért lásd:

* [Az Apache Oozie használata a HDInsight](../hdinsight-use-oozie-linux-mac.md): Az Oozie-munkafolyamatokkal Sqoop műveletet használja.
* [HDInsight használatával repülőjáratok késési adatainak elemzése](../hdinsight-analyze-flight-delay-data-linux.md): Repülőjáratok késési adatainak elemzése az Apache Hive használatával, és a Sqoop használatával egy Azure SQL database-adatok exportálása.
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md): Keresse meg a HDInsight vagy az Azure Blob storage-ba történő feltöltéséhez más módszerekkel.
