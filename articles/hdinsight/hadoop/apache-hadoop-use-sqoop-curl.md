---
title: Az Apache Sqoop használata a HDInsight - Azure Curl
description: Ismerje meg, hogyan lehet távolról a Curl használatával HDInsight az Apache Sqoop-feladatok elküldése.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: ad716e2ef5e597424c860378e7a63d5c2de53f54
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834557"
---
# <a name="run-apache-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Az Apache Sqoop-feladatok futtatása a curl használatával HDInsight a Hadoop-keretrendszerrel
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan lehet Apache Hadoop-fürt a HDInsight Apache Sqoop-feladatok futtatása a Curl használatával.

A curl használatával mutatja be, hogyan használhatja a HDInsight nyers HTTP-kérelmek futtatásához, figyelése, és kérje le az eredményeket, a Sqoop feladatok használatával szolgál. Ez a módszer a WebHCat REST API-val (korábbi nevén templeton eszközön keresztül végzett) a HDInsight-fürt által biztosított.

## <a name="prerequisites"></a>Előfeltételek
A jelen cikkben ismertetett lépések elvégzéséhez a következőkre lesz szüksége:


* Teljes [használata Apache sqoop használatával a HDInsight Hadoop-keretrendszerrel](hdinsight-use-sqoop.md#create-cluster-and-sql-database) -környezet konfigurálása a HDInsight-fürt és a egy Azure SQL database.
* [curl](https://curl.haxx.se/). A curl egy olyan eszköz, vagy egy HDInsight-fürtön az adatok átviteléhez.
* [jq](https://stedolan.github.io/jq/). A jq segédprogram segítségével dolgozza fel a REST-kérelmeket által visszaadott JSON-adatokat.


## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Az Apache Sqoop-feladatok elküldése a Curl használatával
> [!NOTE]  
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.
> 
> Ezen szakasz parancsaiban cserélje le a **USERNAME** elemet a fürthöz hitelesíteni kívánt felhasználóval, és a **PASSWORD** elemet pedig a felhasználói fiók jelszavával. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
> 
> A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.
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
     
     Az URL-cím elején **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, összes kérelem esetében azonos. Az elérési út **/status**, azt jelzi, hogy a kérelem WebHCat (más néven templeton eszközön keresztül végzett) állapottal visszatéréshez a kiszolgálón. 
2. Sqoop feladatok elküldéséhez használja a következő:

    ```bash
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

3. A feladat állapotának ellenőrzéséhez használja a következő parancsot. Cserélje le **JOBID** adja vissza az előző lépésben értékkel. Ha például a visszaadott érték volt `{"id":"job_1415651640909_0026"}`, majd **JOBID** lenne `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Ha a feladat befejeződött, az állapota lesz **sikeres**.
   
   > [!NOTE]  
   > Ezt a kérelmet a Curl egy JavaScript Object Notation (JSON) dokumentumot, a feladat; információkat ad vissza. csak az állapot értékét a vizualizációhoz jq szolgál.

4. Miután a feladat állapota módosult az **sikeres**, a feladat eredményeinek kérheti le az Azure Blob storage-ból. A `statusdir` lekérdezése átadott paraméter tartalmazza a helyet, a kimeneti fájl; ebben az esetben **wasb: / / / például/data/sqoop/curl**. Ez a cím tárolja a feladat kimenete a **curl példa/data/sqoop** az alapértelmezett tárolót a HDInsight-fürt által használt könyvtárába.
   
    Az Azure portal segítségével stdout és az stderr blobok elérése.  A Microsoft SQL Server Management Studio segítségével ellenőrizze az adatokat a log4jlogs táblához feltöltött.

## <a name="limitations"></a>Korlátozások
* Tömeges exportálása – a Linux-alapú HDInsight, a Sqoop-összekötő segítségével a Microsoft SQL Server vagy az Azure SQL Database-adatok exportálása jelenleg nem támogatja a tömeges beszúrás.
* Kötegelés – a Linux-alapú HDInsight használatakor a `-batch` Beszúrások végrehajtásakor váltson, Sqoop több beszúrás helyett a beszúrási műveletek kötegelése hajt végre.

## <a name="summary"></a>Összegzés
Ahogyan ebben a dokumentumban, is használhatja a nyers HTTP-kérelmek futtatásához, figyelése és a Sqoop feladatok eredményeinek megtekintéséhez a HDInsight-fürtön.

A cikk ezt használja a REST-felület további információkért lásd: a <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API-útmutató</a>.

## <a name="next-steps"></a>További lépések
Általános információk a HDInsight Hive:

* [Az Apache Hadoop on HDInsight az Apache Sqoop használata](hdinsight-use-sqoop.md)

Információk az egyéb módon használhatja a Hadoop on HDInsight:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Más HDInsight kapcsolatos cikkek curl:
 
* [Az Azure REST API használatával az Apache Hadoop-fürtök létrehozása](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Apache Hive-lekérdezések futtatásához az Apache hadooppal a HDInsight REST használatával](apache-hadoop-use-hive-curl.md)
* [Az Apache Hadoop MapReduce feladatok futtatása HDInsight REST használatával](apache-hadoop-use-mapreduce-curl.md)
* [Az Apache Hadoop Apache Pig-feladatok futtatása HDInsight a cURL használatával](apache-hadoop-use-pig-curl.md)



