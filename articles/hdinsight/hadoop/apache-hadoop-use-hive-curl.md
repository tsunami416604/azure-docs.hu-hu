---
title: Apache Hadoop kaptár használata a HDInsight-Azure-beli curl használatával
description: Ismerje meg, hogyan küldhet távolról az Apache Pig-feladatokat az Azure HDInsight a curl használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: hrasheed
ms.openlocfilehash: e1fbeb48acdfd9d09cad2616aed9793e2ff513ad
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736090"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Apache Hive-lekérdezések futtatása a HDInsight Apache Hadoop a REST használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat Apache Hive-lekérdezéseket az Azure HDInsight-fürtön lévő Apache Hadoopokkal a Webhcaten REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* REST-ügyfél. Ez a dokumentum a Windows PowerShellben [és a](https://curl.haxx.se/) [bash](https://docs.microsoft.com/windows/wsl/install-win10)-on futó [-webkérést](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) használja.

* Ha bash-et használ, a parancssori JSON-processzorra is szüksége lesz a jQ.  Lásd [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/):.

## <a name="base-uri-for-rest-api"></a>Alapszintű URI a REST API-hoz

A HDInsight `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`lévő REST API alapszintű Uniform Resource Identifier (URI), ahol `CLUSTERNAME` a a fürt neve.  Az URI-k fürtjének nevei **megkülönböztetik a kis-és nagybetűket**.  Míg a fürt neve az URI (`CLUSTERNAME.azurehdinsight.net`) teljes tartományneve (FQDN) része, a kis-és nagybetűk megkülönböztetése, az URI-n belüli más előfordulások megkülönböztetik a kis-és nagybetűket.

## <a name="authentication"></a>Authentication

Ha cURL vagy bármilyen más REST-kommunikációt használ a Webhcaten-mel, a kérelmeket a HDInsight-fürt rendszergazdájához tartozó Felhasználónév és jelszó megadásával kell hitelesítenie. A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Annak érdekében, hogy a hitelesítő adatok biztonságosan legyenek továbbítva a kiszolgálónak, mindig a biztonságos HTTP (HTTPS) protokoll használatával végezze el a kérelmeket.

### <a name="setup-preserve-credentials"></a>Beállítás (hitelesítő adatok megőrzése)
Őrizze meg a hitelesítő adatait, hogy elkerülje az egyes példák újbóli beírását.  A fürt nevét külön lépésben megőrzi a rendszer.

**EGY. Bash**  
Szerkessze az alábbi szkriptet `PASSWORD` úgy, hogy lecseréli a tényleges jelszavát.  Ezután adja meg a parancsot.

```bash
export password='PASSWORD'
```  

**B. A** PowerShell végrehajtja az alábbi kódot, és az előugró ablakban adja meg a hitelesítő adatait:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>A megfelelő tokozású fürt nevének azonosítása
A fürt nevének tényleges burkolata különbözhet attól függően, hogy a fürt hogyan lett létrehozva.  Az itt látható lépések megjelenítik a tényleges burkolatot, majd egy változóban tárolják az összes további példát.

Szerkessze az alábbi parancsfájlokat `CLUSTERNAME` a fürt nevével való lecseréléséhez. Ezután adja meg a parancsot. (Az FQDN fürt neve nem megkülönbözteti a kis-és nagybetűket.)

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

## <a id="curl"></a>Struktúra-lekérdezés futtatása

1. Annak ellenőrzéséhez, hogy tud-e csatlakozni a HDInsight-fürthöz, használja a következő parancsok egyikét:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Az alábbi szöveghez hasonló válasz érkezik:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Ezen parancs paraméterei a következők:

    * `-u`– A kérelem hitelesítéséhez használt Felhasználónév és jelszó.
    * `-G`– Azt jelzi, hogy ez a kérelem lekéréses művelet.

1. Az URL-cím `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`elejének megegyeznek az összes kérelemnél. Az elérési `/status`út azt jelzi, hogy a kérés visszaadja a kiszolgáló webhcaten (más néven Templeton) állapotát. A kaptár verzióját a következő parancs használatával is kérheti:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Ez a kérelem az alábbi szöveghez hasonló választ ad vissza:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. A **log4jLogs**nevű tábla létrehozásához használja a következőt:

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

    Ez a kérelem a POST metódust használja, amely az adatokat a kérelem részeként küldi el a REST API. A kérelem a következő adatértékeket küldi el:

     * `user.name`– A parancsot futtató felhasználó.
     * `execute`– A végrehajtandó HiveQL-utasítások.
     * `statusdir`– Az a könyvtár, amelyre a feladatokhoz tartozó állapot íródik.

   Ezek az utasítások a következő műveleteket hajtják végre:

   * `DROP TABLE`-Ha a tábla már létezik, a rendszer törli.
   * `CREATE EXTERNAL TABLE`-Új "külső" táblát hoz létre a kaptárban. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.

     > [!NOTE]  
     > Külső táblákat kell használni, ha várható, hogy a mögöttes adatokat külső forrás frissíti. Például egy automatizált adatfeltöltési folyamat vagy egy másik MapReduce művelet.
     >
     > Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját.

   * `ROW FORMAT`-Az adat formázása. Az egyes naplók mezői szóközzel vannak elválasztva.
   * `STORED AS TEXTFILE LOCATION`– Az adattárolás helye (a példa/adatkönyvtár), valamint a szövegként tárolt tároló.
   * `SELECT`– Kiválasztja az összes olyan sor számát, ahol a **T4** oszlop tartalmazza a **[hiba]** értéket. Ez az utasítás **3** értéket ad vissza, mert három sor tartalmazza ezt az értéket.

     > [!NOTE]  
     > Figyelje meg, hogy a HiveQL utasítások közötti szóközöket a `+` karakter váltja fel a curl használatával. A szóközt (például az elválasztót `+`) tartalmazó idézőjeles értékeket nem szabad lecserélni.

      Ez a parancs egy olyan AZONOSÍTÓJÚ feladatot ad vissza, amely a feladatok állapotának vizsgálatára használható.

1. A feladatok állapotának megtekintéséhez használja a következő parancsot:

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

    Ha a feladatot befejezte, az állapot **sikeres**lesz.

1. Ha a művelet állapota **sikeresen**módosult, az Azure Blob Storage-ból kérheti le a feladatok eredményeit. A `statusdir` lekérdezéssel átadott paraméter tartalmazza a kimeneti fájl helyét; ebben az `/example/rest`esetben:. Ez a címe tárolja a `example/curl` könyvtárban lévő kimenetet a fürtök alapértelmezett tárolójában.

    Ezeket a fájlokat az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)használatával listázhatja és letöltheti. Az Azure CLI és az Azure Storage használatával kapcsolatos további információkért lásd az Azure [CLI használata](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs) az Azure Storage-ban című dokumentumot.

## <a id="nextsteps"></a>Következő lépések

A kaptárral kapcsolatos általános információk a HDInsight:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)

További információ a HDInsight-beli Hadoop használható egyéb módszerekről:

* [Az Apache Pig használata a Apache Hadoop on HDInsight](hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)

A dokumentumban használt REST API kapcsolatos további információkért tekintse meg a [webhcaten dokumentációját](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) .