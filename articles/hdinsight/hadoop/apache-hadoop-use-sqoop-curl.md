---
title: Adatok exportálása az Apache Sqoop segítségével az Azure HDInsightban
description: Ismerje meg, hogyan küldhet távoli apache Sqoop-feladatokat az Azure HDInsightba a Curl használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: da29785547d1b6eb4b38d07f020ba885dc5137ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767586"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Apache Sqoop-feladatok futtatása a HDInsightban a Curl segítségével

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan futtathat Acurl segítségével Apache Sqoop-feladatok futtatására egy Apache Hadoop-fürtön a HDInsightban. Ez a cikk bemutatja, hogyan exportálhatja az adatokat az Azure Storage-ból, és hogyan importálhatja őket egy SQL Server-adatbázisba a Curl használatával. Ez a cikk az Apache Sqoop használatának folytatása a [Hadoop-mal a HDInsight-ban.](./hdinsight-use-sqoop.md)

A Curl segítségével bemutatja, hogyan kommunikálhat a HDInsight-mal a Sqoop-feladatok futtatásához, figyeléséhez és lekéréséhez használt nyers HTTP-kérelmek használatával. Ez a HDInsight-fürt által biztosított WebHCat REST API (korábbi nevén Templeton) használatával működik.

## <a name="prerequisites"></a>Előfeltételek

* A [Tesztkörnyezet beállítása](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) az [Apache Sqoop használata a Hadoop segítségével a HDInsight ban](./hdinsight-use-sqoop.md).

* Az Azure SQL-adatbázis lekérdezéséhez egy ügyfél. Fontolja meg [az SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) vagy a Visual Studio Code [használatát.](../../sql-database/sql-database-connect-query-vscode.md)

* [Göndör](https://curl.haxx.se/). A Curl egy olyan eszköz, amellyel adatokat vihet át egy HDInsight-fürtről vagy egy HDInsight-fürtbe.

* [jq](https://stedolan.github.io/jq/). A jq segédprogram a REST-kérelmekből visszaadott JSON-adatok feldolgozására szolgál.

* A Sqoop ismerete. További információ: [Sqoop User Guide](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Apache Sqoop-feladatok beküldése a Curl használatával

A Curl használatával exportálhatja az adatokat az Apache Sqoop-feladatok használatával az Azure Storage-ból az SQL Serverkiszolgálóra.

> [!NOTE]  
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.

Az ebben a szakaszban `USERNAME` található parancsokesetében cserélje le a `PASSWORD` felhasználót a fürthitelesítéshez, és cserélje le a felhasználói fiók jelszavával. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.

A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.

1. A könnyű használat érdekében állítsa be az alábbi változókat. Ez a példa egy Windows-környezeten alapul, amelyet a környezetnek megfelelően módosítanak.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    A következőhöz hasonló választ kell kapnia:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Sqoop feladat elküldéséhez használja az alábbiakat:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Ezen parancs paraméterei a következők:

   * **-d** - `-G` Mivel a rendszer nem használja, a kérelem alapértelmezés szerint a POST metódus. `-d`a kéréssel együtt küldött adatértékeket adja meg.

       * **user.name** – A parancsot futtató felhasználó.

       * **command** - A végrehajtandó Sqoop parancs.

       * **statusdir** - Az a könyvtár, amelybe a feladat állapota írni fog.

     Ez a parancs egy feladatazonosítót ad vissza, amely a feladat állapotának ellenőrzésére használható.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. A feladat állapotának ellenőrzéséhez használja a következő parancsot. Cserélje `JOBID` le az előző lépésben visszaadott értékre. Ha például a visszatérési érték `{"id":"job_1415651640909_0026"}`a , akkor `JOBID` a . `job_1415651640909_0026` Szükség szerint `jq` vizsgálja felül a helyét.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Ha a feladat befejeződött, az állapot **sikeres**lesz.

   > [!NOTE]  
   > Ez a Curl-kérelem egy JavaScript-objektumnotúd (JSON) dokumentumot ad vissza a feladattal kapcsolatos információkkal; A jq csak az állapotérték beolvasására szolgál.

1. Miután a feladat állapota **sikeresre**változott, lekérheti a feladat eredményeit az Azure Blob storage-ból. A `statusdir` lekérdezéssel átadott paraméter tartalmazza a kimeneti fájl helyét; ebben az `wasb:///example/data/sqoop/curl`esetben. Ez a cím tárolja a `example/data/sqoop/curl` feladat kimenetét a hdinsight-fürt által használt alapértelmezett tárolótároló címtárában.

    Az Azure Portal használatával stderr és stdout blobok eléréséhez.

1. Az adatok exportálásának ellenőrzéséhez használja az SQL-ügyfél alábbi lekérdezéseit az exportált adatok megtekintéséhez:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – A Linux-alapú HDInsight, a Sqoop-összekötő adatok Microsoft SQL Server vagy az Azure SQL Database jelenleg nem támogatja a tömeges beszúrások.
* Kötegelés - A Linux-alapú HDInsight, Amikor a `-batch` kapcsolót, amikor a beszúrások végrehajtása, Sqoop hajt végre több beszúrások helyett kötegelés a beszúrási műveleteket.

## <a name="summary"></a>Összefoglalás

Ahogy azt ebben a dokumentumban is látható, használhatja a nyers HTTP-kérelem futtatásához, figyeléséhez és a Sqoop-feladatok eredményeinek megtekintéséhez a HDInsight-fürtön.

A cikkben használt REST-felületről az <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API útmutatójában olvashat bővebben.</a>

## <a name="next-steps"></a>További lépések

[Az Apache Sqoop használata az Apache Hadoop segítségével a HDInsight on](hdinsight-use-sqoop.md)

A curl-t érintő egyéb HDInsight-cikkek esetén:

* [Apache Hadoop-fürtök létrehozása az Azure REST API használatával](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Apache Hive-lekérdezések futtatása az Apache Hadoop segítségével a HDInsightban a REST használatával](apache-hadoop-use-hive-curl.md)
* [MapReduce feladatok futtatása az Apache Hadoop segítségével a HDInsight on HDInsight használatával rest használatával](apache-hadoop-use-mapreduce-curl.md)