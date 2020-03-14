---
title: Apache Hadoop kaptár használata a HDInsight-Azure-beli curl használatával
description: Ismerje meg, hogyan küldhet távolról az Apache Pig-feladatokat az Azure HDInsight a curl használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298750"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Apache Hive-lekérdezések futtatása a HDInsight Apache Hadoop a REST használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Megtudhatja, hogyan futtathat Apache Hive-lekérdezéseket az Azure HDInsight-fürtön lévő Apache Hadoopokkal a Webhcaten REST API használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](./apache-hadoop-linux-tutorial-get-started.md).

* REST-ügyfél. Ez a dokumentum a Windows PowerShellben [és a](https://curl.haxx.se/) [bash](https://docs.microsoft.com/windows/wsl/install-win10)-on futó [-webkérést](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) használja.

* Ha bash-et használ, a parancssori JSON-processzort is jQ kell.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Alapszintű URI a REST API-hoz

A HDInsight REST API alapszintű Uniform Resource Identifier (URI) `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, ahol a `CLUSTERNAME` a fürt neve.  Az URI-k fürtjének nevei **megkülönböztetik a kis-és nagybetűket**.  Míg a fürt neve az URI (`CLUSTERNAME.azurehdinsight.net`) teljes tartományneve (FQDN) része, a kis-és nagybetűk megkülönböztetése, az URI-n belüli más előfordulások megkülönböztetik a kis-és nagybetűket.

## <a name="authentication"></a>Hitelesítés

Ha cURL vagy bármilyen más REST-kommunikációt használ a Webhcaten-mel, a kérelmeket a HDInsight-fürt rendszergazdájához tartozó Felhasználónév és jelszó megadásával kell hitelesítenie. A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Annak érdekében, hogy a hitelesítő adatok biztonságosan legyenek továbbítva a kiszolgálónak, mindig a biztonságos HTTP (HTTPS) protokoll használatával végezze el a kérelmeket.

### <a name="setup-preserve-credentials"></a>Beállítás (hitelesítő adatok megőrzése)

Őrizze meg a hitelesítő adatait, hogy elkerülje az egyes példák újbóli beírását.  A fürt nevét külön lépésben megőrzi a rendszer.

**A. bash**  
Szerkessze az alábbi szkriptet úgy, hogy lecseréli `PASSWORD`t a tényleges jelszavával.  Ezután adja meg a parancsot.

```bash
export password='PASSWORD'
```  

**B. a PowerShell** végrehajtja az alábbi kódot, és az előugró ablakban adja meg a hitelesítő adatait:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>A megfelelő tokozású fürt nevének azonosítása

A fürt nevének tényleges burkolata különbözhet attól függően, hogy a fürt hogyan lett létrehozva.  Az itt látható lépések megjelenítik a tényleges burkolatot, majd egy változóban tárolják az összes későbbi példát.

Az alábbi szkriptek szerkesztésével cserélje le a `CLUSTERNAME`t a fürt nevére. Ezután adja meg a parancsot. (Az FQDN fürt neve nem megkülönbözteti a kis-és nagybetűket.)

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

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

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

    * `-u` – a kérelem hitelesítéséhez használt Felhasználónév és jelszó.
    * `-G` – azt jelzi, hogy ez a kérelem lekéréses művelet.

1. Az URL-cím (`https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`) kezdete minden kérelem esetében azonos. Az elérési út (`/status`) azt jelzi, hogy a kérés a kiszolgáló Webhcaten (más néven Templeton) állapotának visszaküldésére szolgál. A kaptár verzióját a következő parancs használatával is kérheti:

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

     * `user.name` – a parancsot futtató felhasználó.
     * `execute` – a végrehajtandó HiveQL-utasítások.
     * `statusdir` – a feladatokhoz tartozó állapotot tartalmazó könyvtár.

   Ezek az utasítások a következő műveleteket hajtják végre:

   * `DROP TABLE` – ha a tábla már létezik, törölve lett.
   * `CREATE EXTERNAL TABLE` – új "külső" táblát hoz létre a kaptárban. A külső táblák csak a struktúra tábla definícióját tárolják. Az adatmező az eredeti helyen marad.

     > [!NOTE]  
     > Külső táblákat kell használni, ha várható, hogy a mögöttes adatokat külső forrás frissíti. Például egy automatizált adatfeltöltési folyamat vagy egy másik MapReduce művelet.
     >
     > Egy külső tábla eldobása **nem** törli az adatforrást, csak a tábla definícióját.

   * `ROW FORMAT` – az adat formázása. Az egyes naplók mezői szóközzel vannak elválasztva.
   * `STORED AS TEXTFILE LOCATION` – az adattárolási hely (a példa/az adatkönyvtár) és a szövegként tárolt tárolása.
   * `SELECT` – kiválasztja az összes olyan sor számát, ahol a **T4** oszlop tartalmazza a **[hiba]** értéket. Ez az utasítás **3** értéket ad vissza, mert három sor tartalmazza ezt az értéket.

     > [!NOTE]  
     > Figyelje meg, hogy a HiveQL utasítások közötti szóközöket a `+` karakter váltja fel a curl használatakor. A szóközt, például az elválasztó karaktert tartalmazó idézőjeles értékeket nem szabad lecserélni `+`.

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

1. Ha a művelet állapota **sikeresen**módosult, az Azure Blob Storage-ból kérheti le a feladatok eredményeit. A lekérdezéssel átadott `statusdir` paraméter tartalmazza a kimeneti fájl helyét; Ebben az esetben `/example/rest`. Ez a címe tárolja a kimenetet a fürtök alapértelmezett tárolójában lévő `example/curl` könyvtárban.

    Ezeket a fájlokat az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)használatával listázhatja és letöltheti. Az Azure CLI és az Azure Storage használatával kapcsolatos további információkért lásd az Azure [CLI használata](https://docs.microsoft.com/azure/storage/storage-azure-cli) az Azure Storage-ban című dokumentumot.

## <a name="next-steps"></a>További lépések

További információ a HDInsight-beli Hadoop használható egyéb módszerekről:

* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)

A dokumentumban használt REST API kapcsolatos további információkért tekintse meg a [webhcaten dokumentációját](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) .