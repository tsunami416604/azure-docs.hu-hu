---
title: Apache Sqoop és Apache Hadoop – Azure HDInsight
description: Ismerje meg, hogyan importálhat és exportálhat az Apache Hadoop és egy Azure SQL-adatbázis között az Apache Sqoop használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769387"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Adatok importálása és exportálása az Apache Sqoop használatával a HDInsight-alapú Apache Hadoop és az SQL Database között

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan importálhat és exportálhat Apache Hadoop-fürtöt az Apache Hadoop-fürtökkel az Azure HDInsight ban és az Azure SQL Database vagy a Microsoft SQL Server adatbázisban. A jelen dokumentum lépései a `sqoop` parancsot közvetlenül a Hadoop-fürt fejnófokáról használják. Az SSH segítségével csatlakozhat a főcsomóponthoz, és futtathatja a parancsokat ebben a dokumentumban. Ez a cikk az Apache Sqoop használatának folytatása a [Hadoop-mal a HDInsight-ban.](./hdinsight-use-sqoop.md)

## <a name="prerequisites"></a>Előfeltételek

* A [Tesztkörnyezet beállítása](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) az [Apache Sqoop használata a Hadoop segítségével a HDInsight ban](./hdinsight-use-sqoop.md).

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

* A Sqoop ismerete. További információ: [Sqoop User Guide](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Beállítás

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A könnyű használat érdekében állítsa be a változókat. Cserélje `PASSWORD` `MYSQLSERVER`ki `MYDATABASE` a , és a megfelelő értékeket, majd írja be az alábbi parancsokat:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop export

A Hive-tól az SQL Serverkiszolgálóig.

1. Annak ellenőrzéséhez, hogy a Sqoop láthatja-e az SQL-adatbázist, írja be az alábbi parancsot a nyitott SSH-kapcsolatba. Ez a parancs az adatbázisok listáját adja vissza.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. A megadott adatbázis tábláinak listájának megtekintéséhez írja be a következő parancsot:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Ha adatokat szeretne `hivesampletable` exportálni `mobiledata` a Hive táblából az SQL Database táblájába, írja be az alábbi parancsot a megnyitott SSH-kapcsolatba:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Az adatok exportálásának ellenőrzéséhez használja az alábbi lekérdezéseket az SSH-kapcsolatból az exportált adatok megtekintéséhez:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop import

Az SQL Servertől az Azure-tárhelyig.

1. Írja be az alábbi parancsot a nyitott `mobiledata` SSH-kapcsolatba, `wasbs:///tutorials/usesqoop/importeddata` és importálja az adatokat az SQL Database táblájából a HDInsight könyvtárába. Az adatok mezőit tabulátorkarakter választja el egymástól, a sorokat pedig egy új sorkarakter szakítja meg.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Másik lehetőségként megadhat egy Hive-táblát is:

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

1. Miután az importálás befejeződött, írja be a következő parancsot a nyitott SSH-kapcsolatba az új könyvtárban lévő adatok listázásához:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Használja [a beeline-t](./apache-hadoop-use-hive-beeline.md) annak ellenőrzésére, hogy a táblázat hive-ban készült-e.

    1. Kapcsolódás

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Egyszerre minden lekérdezést egyenként hajtson végre, és tekintse át a kimenetet:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Kilépés a beeline-ból. `!exit`

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – A Linux-alapú HDInsight, a Sqoop-összekötő adatok Microsoft SQL Server vagy az Azure SQL Database adatok exportálásához használt nem támogatja a tömeges beszúrások.

* Kötegelés - A Linux-alapú HDInsight, Amikor a `-batch` kapcsolót, amikor a beszúrások végrehajtásakor, Sqoop teszi több lapkák helyett kötegelés a beszúrási műveleteket.

## <a name="important-considerations"></a>Fontos szempontok

* A HDInsightnak és az SQL Servernek is ugyanazon az Azure virtuális hálózaton kell lennie.

    Például tekintse meg a [HDInsight csatlakoztatása a helyszíni hálózati](./../connect-on-premises-network.md) dokumentumhoz.

    A HDInsight Azure virtuális hálózattal való használatáról a HDInsight kiterjesztése az [Azure virtuális hálózattal](../hdinsight-plan-virtual-network-deployment.md) című dokumentumban talál további információt. Az Azure virtuális hálózatról további információt a virtuális hálózat áttekintése című [dokumentumban talál.](../../virtual-network/virtual-networks-overview.md)

* Az SQL Server t úgy kell konfigurálni, hogy engedélyezze az SQL-hitelesítést. További információt a [Hitelesítési mód kiválasztása](https://msdn.microsoft.com/ms144284.aspx) dokumentumban talál.

* Előfordulhat, hogy az SQL Server t be kell állítania a távoli kapcsolatok fogadására. További információt az [SQL Server adatbázis-motordokumentumhoz való csatlakozás sal kapcsolatos hibaelhárítása](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) című témakörben talál.

## <a name="next-steps"></a>További lépések

Most már megtanulta, hogyan kell használni Sqoop. További tudnivalókért lásd:

* [Az Apache Oozie használata a HDInsight segítségével:](../hdinsight-use-oozie-linux-mac.md)Sqoop művelet használata Oozie-munkafolyamatokban.
* [A járatkésleltetési adatok elemzése a HDInsight használatával:](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)Az Interaktív lekérdezés segítségével elemezheti a járatkésési adatokat, majd a Sqoop segítségével exportálhatja az adatokat egy Azure SQL-adatbázisba.
* [Adatok feltöltése a HDInsightba:](../hdinsight-upload-data.md)További módszereket találhat az adatok HDInsight/Azure Blob storage-ba való feltöltésére.
