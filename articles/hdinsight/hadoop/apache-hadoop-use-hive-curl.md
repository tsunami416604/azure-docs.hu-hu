---
title: Az Apache Hadoop Hive használata a HDInsight - Azure a curl használatával
description: Ismerje meg, hogyan lehet távolról a Curl használatával HDInsight Apache Pig-feladatok elküldése.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: 334d7b886aa4e2130a12f0c8a7919986fdac55d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508124"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Apache Hive-lekérdezések futtatásához az Apache hadooppal a HDInsight REST használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ismerje meg, hogyan használhatja a WebHCat REST API Apache Hive-lekérdezések futtatásához az Apache Hadoop Azure HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* Egy HDInsight az Apache Hadoop-fürtöt. Lásd: [HDInsight Linux első lépések](./apache-hadoop-linux-tutorial-get-started.md).

* REST-ügyféllel. A dokumentumban [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) a Windows PowerShell és [Curl](https://curl.haxx.se/) a [Bash](https://docs.microsoft.com/windows/wsl/install-win10).

* Ha használja a Bash, jq, egy parancssori JSON feldolgozó is szüksége lesz.  Lásd: [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Alap URI Rest API-ja

Az alap egységes erőforrás-azonosító (URI) a REST API-hoz a HDInsight van `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, ahol `CLUSTERNAME` a fürt neve.  Fürt neve, az URI-k **kis-és nagybetűket**.  Miközben a fürt nevét az URI-t teljesen minősített neve (FQDN) részében (`CLUSTERNAME.azurehdinsight.net`), kis-és, más előfordulások az URI-és nagybetűk.

## <a name="authentication"></a>Hitelesítés

Használatakor a cURL vagy más REST kommunikációt használ a Webhcattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája a felhasználónév és jelszó megadásával. A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Győződjön meg arról, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgáló érdekében mindig kérést HTTP Secure (HTTPS) használatával.

### <a name="setup-preserve-credentials"></a>A telepítő (Preserve hitelesítő adatok)
Őrzi meg a hitelesítő adatait, ismét be kell írni őket minden egyes például elkerülése érdekében.  A fürt nevét a rendszer egy külön lépésben megőrzi.

**EGY. Bash**  
Az alábbi parancsfájlt szerkesztése lecserélésével `PASSWORD` a tényleges jelszóval.  Ezután írja be a parancsot.

```bash
export password='PASSWORD'
```  

**B. PowerShell** hajtsa végre az alábbi kódot, és adja meg hitelesítő adatait az előugró ablakban:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Azonosíthatja a fürt megfelelően kisbetűsek neve
A fürt nevét, a tényleges kis-és várható, a fürt létrehozási módjától függően eltérő lehet.  A lépéseket a tényleges kis-és megjelenítése, és minden további példák egy változóban tárolja majd.

Cserélje le az alábbi parancsfájlok szerkesztése `CLUSTERNAME` a fürt nevére. Ezután írja be a parancsot. (A fürt teljes név nem kis-és nagybetűket.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a id="curl"></a>Hive-lekérdezés futtatása

1. Győződjön meg arról, hogy képes-e csatlakozni a HDInsight-fürt, használja a következő parancsok egyikét:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
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

1. Az URL-cím elején `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, összes kérelem esetében azonos. Az elérési út `/status`, azt jelzi, hogy a kérelem WebHCat (más néven templeton eszközön keresztül végzett) állapottal visszatéréshez a kiszolgálón. A Hive verzióját is kérheti a következő paranccsal:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    A kérelem választ az alábbi szöveghez hasonló:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Használja a következő nevű tábla létrehozásához **log4jLogs**:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
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
   * `SELECT` -Az összes sor számát kiválasztja ahol oszlop **t4** értéket tartalmazza **[hiba]** . A jelen nyilatkozat egy értékét adja vissza **3** annyi három sort, amely tartalmazza ezt az értéket.

     > [!NOTE]  
     > Figyelje meg, hogy a tárolóhelyek között HiveQL utasítások helyébe a `+` . amikor a curl használatával használt karakter. Határolójeles értékek, amelyek tartalmaznak egy szóközt, például az elválasztó nincs helyébe `+`.

      Ez a parancs visszaadja a Feladatazonosítót a feladat állapotának ellenőrzéséhez használható.

1. A feladat állapotának ellenőrzéséhez használja a következő parancsot:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

1. Miután a feladat állapota módosult az **sikeres**, a feladat eredményeinek kérheti le az Azure Blob storage-ból. A `statusdir` lekérdezése átadott paraméter tartalmazza a helyet, a kimeneti fájl; ebben az esetben `/example/rest`. Ez a cím a kimenetet a tárolja a `example/curl` könyvtárat a a fürt alapértelmezett tárolója.

    A listában, és ezeket a fájlokat le használatával a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli). További információ az Azure CLI használatával az Azure Storage: a [használata az Azure CLI az Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) dokumentumot.

## <a id="nextsteps"></a>Következő lépések

Általános információk a HDInsight Hive:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Információk az egyéb módon használhatja a Hadoop on HDInsight:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Ebben a dokumentumban használt REST API további információkért lásd: a [WebHCat referencia](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentumot.