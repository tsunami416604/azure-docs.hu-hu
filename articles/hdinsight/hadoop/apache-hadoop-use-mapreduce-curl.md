---
title: A MapReduce és a Curl használata az Apache Hadoop segítségével a HDInsightban – Azure
description: Ismerje meg, hogyan futtathat távolról MapReduce feladatokat az Apache Hadoop segítségével HDInsight curl használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302712"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>MapReduce feladatok futtatása az Apache Hadoop segítségével a HDInsight on HDInsight használatával rest használatával

Ismerje meg, hogyan használhatja az Apache Hive WebHCat REST API-t a MapReduce feladatok futtatásához egy Apache Hadoop hdinsight-fürtön. A Curl segítségével bemutatja, hogyan kommunikálhat a HDInsight-mal a MapReduce feladatok futtatásához használt nyers HTTP-kérelmek használatával.

> [!NOTE]  
> Ha már ismeri a Linux-alapú Hadoop-kiszolgálók használatát, de még nem ismeri a HDInsight-kiszolgálókat, olvassa el a Mit kell tudni a [Linux-alapú Apache Hadoop-ról a HDInsight dokumentumon.](../hdinsight-hadoop-linux-information.md)

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Lásd: [Apache Hadoop-fürtök létrehozása az Azure Portalon.](../hdinsight-hadoop-create-linux-clusters-portal.md)

Vagy:
  * Windows PowerShell vagy,
  * [Göndör](https://curl.haxx.se/) [a jq](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>MapReduce feladat futtatása

> [!NOTE]  
> Curl vagy bármely más REST-kommunikáció használatakor a WebHCat szolgáltatással, a HDInsight-fürt rendszergazdájának felhasználónevének és jelszavának megadásával hitelesítenie kell a kérelmeket. A fürtnevet a kérelmek kiszolgálónak történő küldéséhez használt URI részeként kell használnia.
>
> A REST API [alapszintű hozzáférés-hitelesítéssel](https://en.wikipedia.org/wiki/Basic_access_authentication)védett. A https használatával mindig kérjen kéréseket annak érdekében, hogy a hitelesítő adatok biztonságosan legyenek elküldve a kiszolgálónak.

### <a name="curl"></a>Curl

1. A könnyű használat érdekében állítsa be az alábbi változókat. Ez a példa egy Windows-környezeten alapul, amelyet a környezetnek megfelelően módosítanak.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Ezen parancs paraméterei a következők:

   * **-u**: A kérelem hitelesítéséhez használt felhasználónevet és jelszót jelzi.
   * **-G**: Azt jelzi, hogy ez a művelet get kérés

   Az URI `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`kezdete, minden kérésesetében megegyezik.

    A következő JSON-hoz hasonló választ kap:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. MapReduce feladat elküldéséhez használja a következő parancsot. Szükség szerint módosítsa a **jq** elérési utat.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Az URI (/mapreduce/jar) vége közli a WebHCat-tel, hogy a kérés mapreduce feladatot indít egy jar fájl egyik osztályából. Ezen parancs paraméterei a következők:

   * **-d** `-G` : nincs használatban, így a kérelem alapértelmezés szerint a POST metódus. `-d`a kéréssel együtt küldött adatértékeket adja meg.
     * **user.name**: A parancsot futtató felhasználó
     * **jar**: A hely a jar fájlt tartalmazó osztály kell solni
     * **class**: A MapReduce logikát tartalmazó osztály
     * **arg**: A MapReduce feladatnak átadandó argumentumok. Ebben az esetben a bemeneti szövegfájl és a kimeneti címhez használt könyvtár

    Ennek a parancsnak vissza kell adnia egy feladatazonosítót, amely a feladat állapotának ellenőrzésére használható:

       job_1415651640909_0026

1. A feladat állapotának ellenőrzéséhez használja a következő parancsot. Cserélje le `JOBID` az értéket az előző lépésben visszaadott **tényleges** értékre. Szükség szerint vizsgálja felül a **jq** helyét.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. A könnyű használat érdekében állítsa be az alábbi változókat. Cserélje `CLUSTERNAME` le a tényleges fürtnevét. A parancs végrehajtása és a fürt bejelentkezési jelszavának megadása, amikor a rendszer kéri.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. a következő paranccsal ellenőrizze, hogy tud-e csatlakozni a HDInsight-fürthöz:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    A következő JSON-hoz hasonló választ kap:

    ```output
    {"version":"v1","status":"ok"}
    ```

1. MapReduce feladat elküldéséhez használja a következő parancsot:

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

    Az URI (/mapreduce/jar) vége közli a WebHCat-tel, hogy a kérés mapreduce feladatot indít egy jar fájl egyik osztályából. Ezen parancs paraméterei a következők:

    * **user.name**: A parancsot futtató felhasználó
    * **jar**: A hely a jar fájlt tartalmazó osztály kell solni
    * **class**: A MapReduce logikát tartalmazó osztály
    * **arg**: A MapReduce feladatnak átadandó argumentumok. Ebben az esetben a bemeneti szövegfájl és a kimeneti címhez használt könyvtár

   Ennek a parancsnak vissza kell adnia egy feladatazonosítót, amely a feladat állapotának ellenőrzésére használható:

       job_1415651640909_0026

1. A feladat állapotának ellenőrzéséhez használja a következő parancsot:

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

### <a name="both-methods"></a>Mindkét módszer

1. Ha a feladat befejeződött, `SUCCEEDED`a visszaadott állapot .

1. Ha a feladat állapota a `SUCCEEDED`, a feladat eredményeit az Azure Blob storage-ból lekérheti. A `statusdir` lekérdezéssel átadott paraméter a kimeneti fájl helyét tartalmazza. Ebben a példában `/example/curl`a hely . Ez a cím tárolja a feladat kimenetét `/example/curl`a fürtök alapértelmezett tárolójában a helyen.

Ezeket a fájlokat az [Azure CLI](/cli/azure/install-azure-cli)segítségével listázhatja és töltheti le. Az Azure CLI azure Blob storage-használatával kapcsolatos további tudnivalókért olvassa el a [Gyorsútmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel című témakört.](../../storage/blobs/storage-quickstart-blobs-cli.md)

## <a name="next-steps"></a>További lépések

A Hadoop hdinsight-on való egyéb működéséről a következőket szeretné tudni:

* [A MapReduce használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-mapreduce.md)
* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-hive.md)

A cikkben használt REST-felületről a [WebHCat referencia című témakörben olvashat bővebben.](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)
