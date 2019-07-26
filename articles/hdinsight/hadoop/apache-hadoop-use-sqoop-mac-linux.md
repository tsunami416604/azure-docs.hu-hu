---
title: Apache Sqoop Apache Hadoop-Azure HDInsight
description: Ismerje meg, hogyan használható az Apache Sqoop a HDInsight és az Azure SQL Database közötti Apache Hadoop importálására és exportálására.
keywords: Hadoop sqoop, sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406042"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Az Apache Sqoop használatával importálhat és exportálhat adatApache Hadoop a HDInsight és a SQL Database között

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan használható az Apache Sqoop az Azure HDInsight-beli Apache Hadoop-fürt és az Azure SQL Database vagy Microsoft SQL Server-adatbázis közötti importáláshoz és exportáláshoz. A jelen dokumentumban szereplő lépések közvetlenül a `sqoop` Hadoop-fürt átjárócsomóponthoz származó parancsot használják. Az SSH használatával csatlakozhat a fő csomóponthoz, és futtathatja a dokumentumban található parancsokat. Ez a cikk az [Apache Sqoop és a Hadoop HDInsight-ben való használatának](./hdinsight-use-sqoop.md)folytatása.

## <a name="prerequisites"></a>Előfeltételek

* A [tesztkörnyezet üzembe](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) állításának befejezése az [Apache Sqoop és a Hadoop használatával a HDInsight-ben](./hdinsight-use-sqoop.md).

* Az Azure SQL Database-t lekérdező ügyfél. Használjon [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) vagy [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)-ot.

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Sqoop-exportálás

A kaptárból a SQL Serverba.

1. Az SSH használatával csatlakozzon a HDInsight-fürthöz. Cserélje `CLUSTERNAME` le a nevet a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Cserélje `MYSQLSERVER` le a helyére a SQL Server nevét. Annak ellenőrzéséhez, hogy a Sqoop látható-e a SQL Database, írja be az alábbi parancsot az Open SSH-kapcsolatban. Ha a rendszer kéri, adja meg a SQL Server bejelentkezéshez használt jelszót. Ez a parancs az adatbázisok listáját adja vissza.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Cserélje `MYSQLSERVER` le a nevet a SQL Server nevére, és `MYDATABASE` az SQL-adatbázis nevével. Az adatok a kaptár `hivesampletable` táblából a `mobiledata` SQL Databaseba való exportálásához írja be az alábbi parancsot az Open SSH-kapcsolatban. Ha a rendszer kéri, adja meg a SQL Server-bejelentkezési jelszót

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Az adatok exportálásának ellenőrzéséhez használja az SQL-ügyfél következő lekérdezéseit az exportált adatok megtekintéséhez:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop importálása

SQL Server az Azure Storage-ba.

1. Cserélje `MYSQLSERVER` le a nevet a SQL Server nevére, és `MYDATABASE` az SQL-adatbázis nevével. Az Open SSH-kapcsolatban az alábbi parancs megadásával importálhatja az `mobiledata` adatait a SQL Database `wasb:///tutorials/usesqoop/importeddata` táblából a HDInsight lévő könyvtárba. Ha a rendszer kéri, adja meg a SQL Server bejelentkezéshez használt jelszót. Az adatokban található mezőket tabulátor karakter választja el egymástól, a vonalakat pedig egy új sor karaktere állítja le.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Az importálás befejezése után írja be a következő parancsot az Open SSH-kapcsolatban, hogy kilistázza az új könyvtár adatait:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – a Linux-alapú HDInsight a Sqoop-összekötő az adatexportálás Microsoft SQL Server vagy Azure SQL Database nem támogatja a tömeges beszúrásokat.

* Kötegelt feldolgozás – a Linux-alapú HDInsight, amikor a kapcsolót a `-batch` lapkák végrehajtásakor használja, a Sqoop több beszúrást tesz lehetővé a beszúrási műveletek kötegelt feldolgozása helyett.

## <a name="important-considerations"></a>Fontos szempontok

* A HDInsight és a SQL Servernak ugyanazon az Azure-Virtual Network kell lennie.

    Példaként tekintse meg a [HDInsight](./../connect-on-premises-network.md) összekapcsolása a helyszíni hálózati dokumentumhoz című témakört.

    További információ a HDInsight Azure Virtual Network használatával történő használatáról: a [HDInsight kiterjesztése az azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) dokumentummal. További információ az Azure Virtual Networkről: [Virtual Network](../../virtual-network/virtual-networks-overview.md) áttekintő dokumentum.

* Az SQL Servert úgy kell konfigurálni, hogy engedélyezze az SQL-hitelesítést. További információ: a [hitelesítési mód kiválasztása](https://msdn.microsoft.com/ms144284.aspx) dokumentum.

* Előfordulhat, hogy a távoli kapcsolatok fogadásához konfigurálnia kell SQL Server. További információ: az SQL Server adatbázismotor-dokumentumhoz [való csatlakozás hibáinak megoldása](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>További lépések

Most megtanulta, hogyan használhatja a Sqoop-t. További tudnivalókért lásd:

* [Apache Oozie használata a HDInsight](../hdinsight-use-oozie-linux-mac.md)-mel: Sqoop művelet használata Oozie-munkafolyamatokban.
* [Repülési késleltetési idő elemzése az HDInsight használatával](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Interaktív lekérdezéssel elemezheti a repülési késleltetési időt, majd a Sqoop használatával exportálhatja az Azure SQL Database-be.
* [Adatok feltöltése a HDInsight](../hdinsight-upload-data.md): Az HDInsight/Azure Blob Storage-ba való adatfeltöltés egyéb módszereinek megkeresése.
