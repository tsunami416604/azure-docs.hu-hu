---
title: Hadoop Hive használata a hdinsight - Azure Curl |} Microsoft Docs
description: Megtudhatja, hogyan távolról elküldeni a Pig-feladatokhoz a HDInsight használata Curl használatával.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: f602cf45165625ec252f2e29cb9b0e5ed878f3a8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170254"
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>A többi használatával HDInsight Hadoop Hive-lekérdezések futtatása

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat Hive-lekérdezéseket a Hadoop on Azure HDInsight-fürt a WebHCat REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

* A Linux-alapú Hadoop a HDInsight fürt 3.4 vagy újabb verziója.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* A többi ügyfél. Ez a dokumentum a Windows Powershellt használja és [Curl](http://curl.haxx.se/) példák.

    > [!NOTE]
    > Az Azure PowerShell használata a HDInsight Hive dedikált-parancsmagokat kínál. További információkért lásd: a [használja az Azure PowerShell Hive](apache-hadoop-use-hive-powershell.md) dokumentum.

Ez a dokumentum is használja a Windows PowerShell és [Jq](http://stedolan.github.io/jq/) JSON-adatokat adott vissza, a többi kérelmek folyamathoz.

## <a id="curl"></a>Hive-lekérdezések futtatása

> [!NOTE]
> Használata cURL vagy más REST kommunikációt használ a Webhcattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája a felhasználónév és jelszó megadásával.
>
> A REST API védelméről [alapszintű hitelesítés](http://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Győződjön meg arról, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgáló érdekében mindig végezzen kérelmek HTTP Secure (HTTPS).

1. A fürt bejelentkezési ebben a dokumentumban a parancsfájlok által használt beállításához használja a következő parancsok egyikét:

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

3. Győződjön meg arról, hogy tud-e csatlakozni a HDInsight-fürthöz, használja a következő parancsok egyikét:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Az alábbihoz hasonló választ kapnak:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Ezen parancs paraméterei a következők:

    * `-u` -A felhasználónevet és a kérés hitelesítéséhez használt jelszót.
    * `-G` -Azt jelzi, hogy a kérelem a GET műveletet.

   Az URL-cím elejére `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, minden olyan kérelem esetében megegyezik. Az elérési út `/status`, azt jelzi, hogy a kérelem vissza WebHCat (más néven Templeton) állapota a kiszolgálóhoz. A Hive verzióját is kérheti a következő paranccsal:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    A kérelem választ ad az alábbihoz hasonló:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Használja a következő nevű tábla létrehozása **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    A kérelem a POST metódussal, amelyhez a kérés részeként adatokat küld a REST API-t használ. A következő adatok értékek a kérelem küldésekor:

     * `user.name` -A a parancsot futtató felhasználónak.
     * `execute` -A HiveQL utasítás végrehajtásához.
     * `statusdir` – A könyvtár írt meg a feladat állapotát.

   Ezekre az utasításokra hajtsa végre a következő műveleteket:
   
   * `DROP TABLE` -Ha a tábla már létezik, akkor az törlődik.
   * `CREATE EXTERNAL TABLE` -Táblát hoz létre egy új "külső" struktúra. Külső táblák csak a tábladefiníció Hive tárolja. Az adatok marad az eredeti helyen.

     > [!NOTE]
     > Külső táblák kell használni, amikor külső forrásból frissítenie kell az alapul szolgáló adatokat várt. Például egy automatizált adatok feltöltési folyamat vagy egy másik MapReduce művelet.
     >
     > A külső tábla eldobása does **nem** törli az adatokat, csak a tábla definíciójában.

   * `ROW FORMAT` -Az adatok formázását. Az egyes naplókon mezők vannak szóközzel elválasztva.
   * `STORED AS TEXTFILE LOCATION` -Az adatok tárolására (a példa/adatkönyvtár) és szövegként tárolt.
   * `SELECT` -Választja ki az összes sorok számát ahol oszlop **t4** értéke **[hiba]**. A jelen nyilatkozat értéket ad vissza, **3** ahány három ezt az értéket tartalmazó sorok.

     > [!NOTE]
     > Figyelje meg, hogy a HiveQL utasítások szóközt helyébe a `+` karakter Curl használata esetén. Szóközt, például az elválasztó idézőjelek közé zárt értékek nem helyébe kell `+`.

      Ez a parancs visszaadja a Feladatazonosítót a feladat állapotának ellenőrzéséhez használható.

5. A feladat állapotának ellenőrzéséhez használja a következő parancsot:

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

    Ha a feladat befejeződött, az állapota **sikeres**.

6. Ha a feladat állapota módosult **sikeres**, a feladat eredményeinek lekérése Azure Blob Storage tárolóban. A `statusdir` a lekérdezés átadott paraméter tartalmazza a hely a kimeneti fájl; ebben az esetben az `/example/rest`. Ez a cím tárolja a kimenetét a `example/curl` könyvtárban lévő a fürtök alapértelmezett tárolót.

    Listáról, és ezek a fájlok letöltésére a [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). Az Azure Storage az Azure parancssori felület használatával kapcsolatos további információkért tekintse meg a [Azure CLI 2.0 használata Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentum.

## <a id="nextsteps"></a>Következő lépések

Általános információk a hdinsight Hive:

* [A Hive használata a hdinsight Hadoop](hdinsight-use-hive.md)

Az egyéb lehetőségeiről a HDInsight Hadoop dolgozhat:

* [A Pig használata a HDInsight Hadoop](hdinsight-use-pig.md)
* [A HDInsight Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Ha Tez Hive használ, tekintse meg a hibakeresési információk a következő dokumentumokat:

* [Az Ambari Tez nézetben a Linux-alapú HDInsight-on](../hdinsight-debug-ambari-tez-view.md)

A REST API-val a jelen dokumentum további információkért tekintse meg a [WebHCat hivatkozás](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentum.

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


