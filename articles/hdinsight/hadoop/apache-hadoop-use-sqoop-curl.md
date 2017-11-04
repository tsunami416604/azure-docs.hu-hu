---
title: "Hadoop Sqoop használata a hdinsight - Azure Curl |} Microsoft Docs"
description: "Megtudhatja, hogyan távolról a Sqoop feladatok HDInsight használata Curl használatával való elküldéséhez."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: add96267f8afe6dcaf43e163f9d18bd7fb57682e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Sqoop feladatok futtatása a hadooppal a Hdinsightban a Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Megtudhatja, hogyan használja a Curl hdinsight Hadoop-fürthöz Sqoop feladatok futtatásához.

Curl használatával bemutatják, hogyan kezelheti a HDInsight nyers HTTP-kérelmek futtatásához, a figyelheti és a Sqoop feladatot az eredmények visszakeresésére használatával. Ez a módszer a WebHCat REST API használatával (korábbi nevén lépni a Templeton) a HDInsight-fürt által biztosított.

## <a name="prerequisites"></a>Előfeltételek
Ebben a cikkben szereplő lépések elvégzéséhez a következőkre lesz szüksége:

* Teljes [Sqoop használata a hadooppal a Hdinsightban](hdinsight-use-sqoop.md#create-cluster-and-sql-database) környezet konfigurálása a HDInsight-fürt és az Azure SQL-adatbázis.
* [Curl](http://curl.haxx.se/). Curl egy olyan eszköz, vagy egy HDInsight-fürthöz az adatok átviteléhez.
* [jq](http://stedolan.github.io/jq/). A jq segédprogram segítségével dolgozza fel a többi kérelem által visszaadott JSON-adatokat.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Sqoop feladatok elküldéséhez a Curl használatával
> [!NOTE]
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.
> 
> Ezen szakasz parancsaiban cserélje le a **USERNAME** elemet a fürthöz hitelesíteni kívánt felhasználóval, és a **PASSWORD** elemet pedig a felhasználói fiók jelszavával. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
> 
> A REST API védelméről [alapszintű hitelesítés](http://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.
> 
> 

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    A következőhöz hasonló választ kell kapnia:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Ezen parancs paraméterei a következők:
   
   * **-u** - A kérés hitelesítéséhez használt felhasználónév és jelszó.
   * **-G** - Jelzi, hogy ez egy GET kérés.
     
     Az URL-cím, az elején **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, minden olyan kérelem esetében megegyezik. Az elérési út **/status**, azt jelzi, hogy a kérelem vissza WebHCat (más néven Templeton) állapota a kiszolgálóhoz. 
2. Használja a következő sqoop feladat elküldése:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Ezen parancs paraméterei a következők:

    * **-d** - óta `-G` nem használ, akkor a kérelmet az alapértelmezett a POST metódussal. `-d`Megadja a küldött adatértékekkel mellékel a kérelemhez.

        * **User.name** – a parancsot futtató felhasználónak.

        * **a parancs** -a Sqoop parancs végrehajtásához.

        * **statusdir** -címtárban, ami a feladat állapotát a rendszer úgy tünteti fel.

    Ez a parancs visszaadja a Feladatazonosítót a feladat állapotának ellenőrzéséhez használható.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. A feladat állapotának ellenőrzéséhez használja a következő parancsot. Cserélje le **JOBID** az előző lépésben visszaadott értékkel. Például, ha a visszatérési érték volt `{"id":"job_1415651640909_0026"}`, majd **JOBID** lenne `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Ha a feladat befejeződött, az állapot nem **sikeres**.
   
   > [!NOTE]
   > A Curl kérelem egy JavaScript Object Notation (JSON) dokumentumot, a feladat; információkat ad vissza csak a állapotérték beolvasásához használt jq.
   > 
   > 
4. Ha a feladat állapota módosult **sikeres**, a feladat eredményeinek lekérése Azure Blob Storage tárolóban. A `statusdir` a lekérdezés átadott paraméter tartalmazza a hely a kimeneti fájl; ebben az esetben az **wasb: / / / Példa/data/sqoop/curl**. Ez a cím tárolja a feladat kimenete a **curl példa/data/sqoop** könyvtárába a HDInsight-fürt által használt alapértelmezett tárolót.
   
    Használhatja az Azure-portálon stderr és a stdout blobokhoz való hozzáférést.  Microsoft SQL Server Management Studio segítségével ellenőrizze az adatokat a log4jlogs tábla feltöltött.

## <a name="limitations"></a>Korlátozások
* Tömeges export - a Linux-alapú HDInsight, a Sqoop összekötő használt Microsoft SQL Server vagy az Azure SQL Database adatainak exportálása jelenleg nem támogatja a tömeges beszúrások.
* Kötegelés - és a Linux-alapú HDInsight együttes használata esetén a `-batch` beszúrása végrehajtásakor kapcsoló, a Sqoop több beszúrás helyett a beszúrási műveletek kötegelése hajt végre.

## <a name="summary"></a>Összefoglalás
Ahogyan az ebben a dokumentumban, használhatja a nyers HTTP-kérelmek futtatásához, a figyelheti és a Sqoop feladat eredményeinek megtekintéséhez a HDInsight-fürt.

A cikk ezt használja a REST-felület további információkért tekintse meg a <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API útmutató</a>.

## <a name="next-steps"></a>Következő lépések
Általános információk a hdinsight Hive:

* [Sqoop használata a HDInsight Hadoop](hdinsight-use-sqoop.md)

Az egyéb lehetőségeiről a HDInsight Hadoop dolgozhat:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)
* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

A más HDInsight vonatkozó cikkek curl:
 
* [Az Azure REST API használatával Hadoop-fürtök létrehozása](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [A többi használatával HDInsight Hadoop Hive-lekérdezések futtatása](apache-hadoop-use-hive-curl.md)
* [A HDInsight a többi hadooppal futtatási MapReduce-feladatok](apache-hadoop-use-mapreduce-curl.md)
* [Pig-feladatokhoz Hadoop on HDInsight használata cURL használatával futtassa](apache-hadoop-use-pig-curl.md)



