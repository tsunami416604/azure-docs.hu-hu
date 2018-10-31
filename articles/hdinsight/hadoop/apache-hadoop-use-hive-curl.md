---
title: A Hadoop Hive használata a HDInsight - Azure a curl használatával
description: Ismerje meg, hogyan lehet távolról a Curl használatával HDInsight Pig-feladatok elküldése.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: 3ff05640431f78d3f48942a6701bbc2b54da7c90
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250495"
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>Hive-lekérdezések futtatása a REST használatával HDInsight Hadoop-keretrendszerrel

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ismerje meg, hogyan használhatja a WebHCat REST API és a Hadoop Hive-lekérdezések futtatásához az Azure HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* Egy Linux-alapú Hadooppal a HDInsight-fürt verziója 3.4-es vagy nagyobb.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* REST-ügyféllel. Ez a dokumentum a Windows Powershellt használja, és [Curl](http://curl.haxx.se/) példákat.

    > [!NOTE]
    > Az Azure PowerShell használata a HDInsight Hive-val dedikált parancsokat tartalmaz. További információkért lásd: a [Hive használata az Azure PowerShell-lel](apache-hadoop-use-hive-powershell.md) dokumentumot.

Ez a dokumentum is használja a Windows PowerShell és [Jq](http://stedolan.github.io/jq/) JSON-adatokat ad vissza, a REST-kérelmeket feldolgozni.

## <a id="curl"></a>Hive-lekérdezés futtatása

> [!NOTE]
> Használatakor a cURL vagy más REST kommunikációt használ a Webhcattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája a felhasználónév és jelszó megadásával.
>
> A REST API védelméről [alapszintű hitelesítés](http://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Győződjön meg arról, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgáló érdekében mindig kérést HTTP Secure (HTTPS) használatával.

1. Állítsa be a fürtre való bejelentkezéshez, a jelen dokumentum a parancsfájlok által használt, használja a következő parancsok egyikét:

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

3. Győződjön meg arról, hogy képes-e csatlakozni a HDInsight-fürt, használja a következő parancsok egyikét:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Az alábbi szöveghez hasonló választ kap:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Ezen parancs paraméterei a következők:

    * `-u` – A felhasználónév és a kérés hitelesítésére használt jelszó.
    * `-G` – Azt jelzi, hogy a kérelem-LEKÉRÉSI művelet.

   Az URL-cím elején `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, összes kérelem esetében azonos. Az elérési út `/status`, azt jelzi, hogy a kérelem WebHCat (más néven templeton eszközön keresztül végzett) állapottal visszatéréshez a kiszolgálón. A Hive verzióját is kérheti a következő paranccsal:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    A kérelem választ az alábbi szöveghez hasonló:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Használja a következő nevű tábla létrehozásához **log4jLogs**:

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

    Ezt a kérést a POST metódussal, amely adatokat küld a kérés részeként a REST API-t használja. A következő adatok értékek lesznek elküldve a kérelem:

     * `user.name` -A a parancsot futtató felhasználónak.
     * `execute` -A hiveql végrehajtásához.
     * `statusdir` – A könyvtár, ez a feladat állapota írt.

   Ezek az utasítások hajtsa végre a következő műveleteket:
   
   * `DROP TABLE` – Ha a tábla már létezik, akkor az törlődik.
   * `CREATE EXTERNAL TABLE` – Egy új táblát hoz létre "külső" struktúrában. Külső táblák csak a tábla definíciójának Hive tárolja. Az adatok az eredeti helyén marad.

     > [!NOTE]
     > Külső táblák kell használni, amikor várhatóan az alapul szolgáló adatokat egy külső forrás frissíteni kell. Például az automatikus feltöltési folyamat vagy egy másik MapReduce művelet.
     >
     > A külső tábla elvetését does **nem** törölheti az adatokat, csak a tábla definícióját.

   * `ROW FORMAT` – Az adatok formázását. Minden napló mezőinek szóközzel vannak elválasztva.
   * `STORED AS TEXTFILE LOCATION` – Az adatok tárolására (a példaadatokat/directory), és hogy szövegként lesz tárolva.
   * `SELECT` -Az összes sor számát kiválasztja ahol oszlop **t4** értéket tartalmazza **[hiba]**. A jelen nyilatkozat egy értékét adja vissza **3** annyi három sort, amely tartalmazza ezt az értéket.

     > [!NOTE]
     > Figyelje meg, hogy a tárolóhelyek között HiveQL utasítások helyébe a `+` . amikor a curl használatával használt karakter. Határolójeles értékek, amelyek tartalmaznak egy szóközt, például az elválasztó nincs helyébe `+`.

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

    Ha a feladat befejeződött, az állapot az **sikeres**.

6. Miután a feladat állapota módosult az **sikeres**, a feladat eredményeinek kérheti le az Azure Blob storage-ból. A `statusdir` lekérdezése átadott paraméter tartalmazza a helyet, a kimeneti fájl; ebben az esetben `/example/rest`. Ez a cím a kimenetet a tárolja a `example/curl` könyvtárat a a fürt alapértelmezett tárolója.

    A listában, és ezeket a fájlokat le használatával a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli). További információ az Azure CLI használatával az Azure Storage: a [használata az Azure CLI az Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentumot.

## <a id="nextsteps"></a>Következő lépések

Általános információk a HDInsight Hive:

* [A Hive használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Információk az egyéb módon használhatja a Hadoop on HDInsight:

* [A Pig használata a HDInsight Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight Hadoop](hdinsight-use-mapreduce.md)

Ha Tez Hive-val használ, tekintse meg a hibakeresési információkat a következő dokumentumokat:

* [A Linux-alapú HDInsight az Ambari Tez nézet használata](../hdinsight-debug-ambari-tez-view.md)

Ebben a dokumentumban használt REST API további információkért lásd: a [WebHCat referencia](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentumot.

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


