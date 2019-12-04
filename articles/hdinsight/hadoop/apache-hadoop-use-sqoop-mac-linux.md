---
title: Apache Sqoop Apache Hadoop-Azure HDInsight
description: Ismerje meg, hogyan használható az Apache Sqoop a HDInsight és az Azure SQL Database közötti Apache Hadoop importálására és exportálására.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769387"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Az Apache Sqoop használatával importálhat és exportálhat adatApache Hadoop a HDInsight és a SQL Database között

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan használható az Apache Sqoop az Azure HDInsight-beli Apache Hadoop-fürt és az Azure SQL Database vagy Microsoft SQL Server-adatbázis közötti importáláshoz és exportáláshoz. A jelen dokumentumban ismertetett lépések közvetlenül a Hadoop-fürt átjárócsomóponthoz származó `sqoop` parancsot használják. Az SSH használatával csatlakozhat a fő csomóponthoz, és futtathatja a dokumentumban található parancsokat. Ez a cikk az [Apache Sqoop és a Hadoop HDInsight-ben való használatának](./hdinsight-use-sqoop.md)folytatása.

## <a name="prerequisites"></a>Előfeltételek

* A [tesztkörnyezet üzembe](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) állításának befejezése az [Apache Sqoop és a Hadoop használatával a HDInsight-ben](./hdinsight-use-sqoop.md).

* Egy SSH-ügyfél. További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Az Sqoop ismerete. További információ: [Sqoop felhasználói útmutató](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Beállítás

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az egyszerű használat érdekében állítsa be a változókat. Cserélje le a `PASSWORD`, `MYSQLSERVER`és `MYDATABASE` értéket a megfelelő értékekre, majd írja be az alábbi parancsokat:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop-exportálás

A kaptárból a SQL Serverba.

1. Annak ellenőrzéséhez, hogy a Sqoop látható-e a SQL Database, írja be az alábbi parancsot az Open SSH-kapcsolatban. Ez a parancs az adatbázisok listáját adja vissza.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. A következő parancs megadásával tekintheti meg a megadott adatbázis tábláinak listáját:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Ha az adatok a kaptár `hivesampletable` táblából a SQL Database `mobiledata` táblájába szeretné exportálni, írja be az alábbi parancsot az Open SSH-kapcsolatban:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Az adatok exportálásának ellenőrzéséhez használja az SSH-kapcsolatban az alábbi lekérdezéseket az exportált adatok megtekintéséhez:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop importálása

SQL Server az Azure Storage-ba.

1. Az Open SSH-kapcsolatban az alábbi parancs megadásával importálhatja az adatait a SQL Database `mobiledata` táblájából a HDInsight `wasbs:///tutorials/usesqoop/importeddata` könyvtárába. Az adatokban található mezőket tabulátor karakter választja el egymástól, a vonalakat pedig egy új sor karaktere állítja le.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Azt is megteheti, hogy egy kaptár táblát is megadhat:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Az importálás befejezése után írja be a következő parancsot az Open SSH-kapcsolatban, hogy kilistázza az új könyvtár adatait:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. A [Beeline](./apache-hadoop-use-hive-beeline.md) használatával ellenőrizze, hogy a tábla létre lett-e hozva a kaptárban.

    1. Kapcsolódás

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Hajtsa végre az egyes lekérdezéseket egy időben, és tekintse át a kimenetet:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Zárja be a `!exit`.

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – a Linux-alapú HDInsight a Sqoop-összekötő, amely az adatexportálást Microsoft SQL Server vagy Azure SQL Database nem támogatja a tömeges beszúrások használatát.

* Kötegelt feldolgozás – a Linux-alapú HDInsight, amikor a `-batch` kapcsolót használja a lapkák végrehajtásakor, a Sqoop több beszúrást tesz lehetővé az INSERT műveletekhez.

## <a name="important-considerations"></a>Fontos szempontok

* A HDInsight és a SQL Servernak ugyanazon az Azure-Virtual Network kell lennie.

    Példaként tekintse meg a [HDInsight összekapcsolása](./../connect-on-premises-network.md) a helyszíni hálózati dokumentumhoz című témakört.

    További információ a HDInsight Azure Virtual Network használatával történő használatáról: a [HDInsight kiterjesztése az azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) dokumentummal. További információ az Azure Virtual Networkről: [Virtual Network áttekintő](../../virtual-network/virtual-networks-overview.md) dokumentum.

* Az SQL Servert úgy kell konfigurálni, hogy engedélyezze az SQL-hitelesítést. További információ: a [hitelesítési mód kiválasztása](https://msdn.microsoft.com/ms144284.aspx) dokumentum.

* Előfordulhat, hogy a távoli kapcsolatok fogadásához konfigurálnia kell SQL Server. További információ: az SQL Server adatbázismotor-dokumentumhoz [való csatlakozás hibáinak megoldása](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Következő lépések

Most már megtanulta, hogyan használhatja a Sqoop-t. További tudnivalókért lásd:

* Az [Apache Oozie és a HDInsight használata](../hdinsight-use-oozie-linux-mac.md): Sqoop művelet használata Oozie-munkafolyamatokban.
* [Repülési késleltetési idő elemzése a HDInsight használatával](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): interaktív lekérdezés használatával elemezheti a repülési késleltetési adataikat, majd a Sqoop használatával exportálhatja az Azure SQL Database-be.
* [Adatok feltöltése a HDInsight-be](../hdinsight-upload-data.md): további módszerek az adatok HDInsight/Azure Blob Storage-ba való feltöltéséhez.
