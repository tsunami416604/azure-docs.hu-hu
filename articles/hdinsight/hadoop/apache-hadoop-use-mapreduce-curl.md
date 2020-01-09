---
title: A MapReduce és a curl használata Apache Hadoop HDInsight-Azure-ban
description: Ismerje meg, hogyan futtathat MapReduce-feladatokat Apache Hadoop a HDInsight a curl használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/27/2018
ms.openlocfilehash: 274d8dc80d9318aa3ddf4a904a5b623319ea01f4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645004"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>MapReduce-feladatok futtatása a HDInsight Apache Hadoop a REST használatával

Megtudhatja, hogyan futtathat MapReduce-feladatokat a HDInsight-fürtön lévő Apache Hadoopon a Apache Hive Webhcaten REST API használatával. A curl használatával megtudhatja, hogyan kezelheti a HDInsight a MapReduce-feladatok futtatására szolgáló nyers HTTP-kérelmek használatával.

> [!NOTE]  
> Ha már ismeri a Linux-alapú Hadoop-kiszolgálókat, de most ismerkedik a HDInsight-mel, tekintse [meg a HDInsight-dokumentum Linux-alapú Apache Hadoop megismeréséhez szükséges tudnivalókat](../hdinsight-hadoop-linux-information.md) .


## <a id="prereq"></a>Előfeltételek

* Hadoop a HDInsight-fürtön
* Windows PowerShell vagy [curl](https://curl.haxx.se/) és [jQ](https://stedolan.github.io/jq/)

## <a id="curl"></a>MapReduce-feladatok futtatása

> [!NOTE]  
> Ha curl vagy bármilyen más REST-kommunikációt használ a Webhcaten-mel, a kérelmeket hitelesítenie kell a HDInsight-fürt rendszergazdai felhasználónevének és jelszavának megadásával. A fürt nevét a kérések kiszolgálónak való küldéséhez használt URI részeként kell használni.
>
> A REST API [alapvető hozzáférési hitelesítéssel](https://en.wikipedia.org/wiki/Basic_access_authentication)van védve. A kéréseket mindig HTTPS használatával kell elvégeznie, hogy a hitelesítő adatai biztonságosan legyenek elküldve a kiszolgálónak.

1. A dokumentumban található parancsfájlok által használt fürt bejelentkezésének beállításához használja a következő parancsok egyikét:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. A fürt nevének megadásához használja a következő parancsok egyikét:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    A következő JSON-hoz hasonló válasz érkezik:

        {"status":"ok","version":"v1"}

    Ezen parancs paraméterei a következők:

   * **-u**: megadja a kérelem hitelesítéséhez használt felhasználónevet és jelszót
   * **-G**: azt jelzi, hogy ez a művelet egy Get kérelem

   Az URI-azonosító (`https://CLUSTERNAME.azurehdinsight.net/templeton/v1`) kezdete minden kérelem esetében azonos.

4. MapReduce-feladatok elküldéséhez használja a következő parancsot:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Az URI (/MapReduce/jar) vége azt jelzi, hogy a kérelem Webhcaten elindít egy MapReduce-feladatot egy jar-fájlból. Ezen parancs paraméterei a következők:

   * **-d**: `-G` nincs használatban, ezért a kérelem alapértelmezett értéke a post metódus. `-d` megadja a kéréssel elküldhető adatértékeket.
     * **User.name**: a parancsot futtató felhasználó
     * **jar**: a futtatni kívánt osztályt tartalmazó jar-fájl helye
     * **osztály**: a MapReduce logikát tartalmazó osztály
     * **ARG**: a MapReduce-feladatoknak átadandó argumentumok. Ebben az esetben a bemeneti szövegfájl és a kimenethez használt könyvtár

   A parancsnak olyan AZONOSÍTÓJÚ feladatot kell visszaadnia, amely a feladatok állapotának vizsgálatára használható:

       job_1415651640909_0026

5. A feladatok állapotának megtekintéséhez használja a következő parancsot:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Ha a feladatok befejeződik, a visszaadott állapot `SUCCEEDED`.

   > [!NOTE]  
   > Ez a curl-kérelem egy JSON-dokumentumot ad vissza, amely a feladattal kapcsolatos információkkal szolgál. A jQ csak az állapot értékének beolvasására szolgál.

6. Ha a feladatainak állapota `SUCCEEDED`re módosult, lekérheti a feladatoknak az Azure Blob Storage-ból való lekérdezését. A lekérdezésben átadott `statusdir` paraméter tartalmazza a kimeneti fájl helyét. Ebben a példában a hely `/example/curl`. Ez a címe tárolja a feladatok kimenetét a fürtök alapértelmezett tárolójában a következő helyen: `/example/curl`.

Ezeket a fájlokat az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)használatával listázhatja és letöltheti. A Blobok Azure CLI-vel történő használatáról további információt az Azure [CLI és az Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) -dokumentum használata című témakörben talál.

## <a id="nextsteps"></a>Következő lépések

Általános információk a MapReduce-feladatokról a HDInsight-ben:

* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)

További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
* [Az Apache Pig használata a Apache Hadoop on HDInsight](hdinsight-use-pig.md)

A cikkben használt REST-felülettel kapcsolatos további információkért tekintse meg a Webhcaten- [referenciát](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
