---
title: Az Azure HDInsight az Apache Sqoop adatainak exportálása a Curl használatával
description: Ismerje meg, hogyan lehet távolról a Curl használatával HDInsight az Apache Sqoop-feladatok elküldése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 345f492c5b2c754cbbcfa150561ee06b5a4154a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718685"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Az Apache Sqoop-feladatok futtatása a HDInsight a curl használatával
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan lehet Apache Hadoop-fürt a HDInsight Apache Sqoop-feladatok futtatása a Curl használatával. Ez a cikk bemutatja, hogyan adatok exportálása az Azure storage-ból, és a egy SQL Server-adatbázis a Curl használatával importálja. Ez a cikk a fenntartása [használata Apache sqoop használatával a HDInsight Hadoop-keretrendszerrel](./hdinsight-use-sqoop.md).

A curl használatával mutatja be, hogyan használhatja a HDInsight nyers HTTP-kérelmek futtatásához, figyelése, és kérje le az eredményeket, a Sqoop feladatok használatával szolgál. Ez a módszer a WebHCat REST API-val (korábbi nevén templeton eszközön keresztül végzett) a HDInsight-fürt által biztosított.

## <a name="prerequisites"></a>Előfeltételek

* Megvalósításának [tesztkörnyezet beállítása](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) a [használata Apache sqoop használatával a HDInsight Hadoop-keretrendszerrel](./hdinsight-use-sqoop.md).

* Egy ügyfél az Azure SQL-adatbázis lekérdezéséhez. Fontolja meg [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) vagy [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [curl](https://curl.haxx.se/). A curl egy olyan eszköz, vagy egy HDInsight-fürtön az adatok átviteléhez.

* [jq](https://stedolan.github.io/jq/). A jq segédprogram segítségével dolgozza fel a REST-kérelmeket által visszaadott JSON-adatokat.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Az Apache Sqoop-feladatok elküldése a Curl használatával

Az Apache Sqoop feladatok az Azure storage-ból az SQL Server használata az adatok exportálása a Curl használatával.

> [!NOTE]  
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.

Ezen szakasz parancsaiban cserélje `USERNAME` a fürtön, és cserélje le a felhasználóval `PASSWORD` a felhasználói fiókhoz tartozó jelszót. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.
 
A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    A következőhöz hasonló választ kell kapnia:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Cserélje le `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` az előfeltételeknek megfelelő értékeivel. Sqoop feladatok elküldéséhez használja a következő:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Ezen parancs paraméterei a következők:

   * **-d** – óta `-G` nem használ, akkor a kérelem az alapértelmezett a POST metódust. `-d` Megadja a küldött adatok értékeket a kérelemmel.

       * **User.name** – a parancsot futtató felhasználónak.

       * **a parancs** – a sqoop használatával a parancs végrehajtásához.

       * **statusdir** – a címtárból, amelyhez ez a feladat állapota lesz írva.

     Ez a parancs visszaadja Feladatazonosítót a feladat állapotának ellenőrzéséhez használható.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. A feladat állapotának ellenőrzéséhez használja a következő parancsot. Cserélje le `JOBID` adja vissza az előző lépésben értékkel. Ha például a visszaadott érték volt `{"id":"job_1415651640909_0026"}`, majd `JOBID` lenne `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Ha a feladat befejeződött, az állapota lesz **sikeres**.
   
   > [!NOTE]  
   > Ezt a kérelmet a Curl egy JavaScript Object Notation (JSON) dokumentumot, a feladat; információkat ad vissza. csak az állapot értékét a vizualizációhoz jq szolgál.

4. Miután a feladat állapota módosult az **sikeres**, a feladat eredményeinek kérheti le az Azure Blob storage-ból. A `statusdir` lekérdezése átadott paraméter tartalmazza a helyet, a kimeneti fájl; ebben az esetben `wasb:///example/data/sqoop/curl`. Ez a cím tárolja a feladat kimenete a `example/data/sqoop/curl` az alapértelmezett tárolót a HDInsight-fürt által használt könyvtárába.

    Az Azure portal segítségével stdout és az stderr blobok elérése.

5. Győződjön meg arról, hogy történt adatexportálás, az exportált adatok megtekintéséhez használja a következő lekérdezéseket az SQL-ügyfélről:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Korlátozások
* Tömeges exportálása – a Linux-alapú HDInsight, a Sqoop-összekötő segítségével a Microsoft SQL Server vagy az Azure SQL Database-adatok exportálása jelenleg nem támogatja a tömeges beszúrás.
* Kötegelés – a Linux-alapú HDInsight használatakor a `-batch` Beszúrások végrehajtásakor váltson, Sqoop több beszúrás helyett a beszúrási műveletek kötegelése hajt végre.

## <a name="summary"></a>Összefoglalás
Ahogyan ebben a dokumentumban, is használhatja a nyers HTTP-kérelmek futtatásához, figyelése és a Sqoop feladatok eredményeinek megtekintéséhez a HDInsight-fürtön.

A cikk ezt használja a REST-felület további információkért lásd: a <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API-útmutató</a>.

## <a name="next-steps"></a>További lépések
[Az Apache Hadoop on HDInsight az Apache Sqoop használata](hdinsight-use-sqoop.md)

Más HDInsight kapcsolatos cikkek curl:
 
* [Az Azure REST API használatával az Apache Hadoop-fürtök létrehozása](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Apache Hive-lekérdezések futtatásához az Apache hadooppal a HDInsight REST használatával](apache-hadoop-use-hive-curl.md)
* [Az Apache Hadoop MapReduce feladatok futtatása HDInsight REST használatával](apache-hadoop-use-mapreduce-curl.md)
* [Az Apache Hadoop Apache Pig-feladatok futtatása HDInsight a cURL használatával](apache-hadoop-use-pig-curl.md)
