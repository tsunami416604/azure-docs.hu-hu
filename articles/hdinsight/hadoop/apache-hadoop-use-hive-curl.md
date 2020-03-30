---
title: Az Apache Hadoop Hive használata a Curl segítségével a HDInsightban – Azure
description: Ismerje meg, hogyan küldhet távoli apache pig feladatokat az Azure HDInsight ba a Curl használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 10a2f413142124db7547e68280a0d5e9abac9b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298750"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Apache Hive-lekérdezések futtatása az Apache Hadoop segítségével a HDInsightban a REST használatával

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ismerje meg, hogyan futtathat Apache Hive-lekérdezéseket az Apache Hadoop segítségével a WebHCat REST API használatával az Azure HDInsight-fürtön.

## <a name="prerequisites"></a>Előfeltételek

* Apache Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./apache-hadoop-linux-tutorial-get-started.md)

* REST-ügyfél. Ez a dokumentum [az Invoke-WebRequest alkalmazást](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) használja a Windows PowerShell és a [Curl](https://curl.haxx.se/) a [Bash](https://docs.microsoft.com/windows/wsl/install-win10)rendszeren.

* Bash használata esetén a jq-ra, egy parancssori JSON processzorra is szüksége lesz.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Rest API alap URI-ja

A HDInsight REST API-jának alap egységes `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`erőforrás-azonosítója (URI) a , hol `CLUSTERNAME` található a fürt neve.  Az URI-kban a fürtnevek **ben a kis- és nagybetűket nem lehet figyelembe.**  Míg a fürt neve a teljesen minősített tartománynév (FQDN) része az URI (`CLUSTERNAME.azurehdinsight.net`) a kis- és nagybetűk megkülönböztetése, más előfordulások az URI-ban a kis- és nagybetűk megkülönböztetése.

## <a name="authentication"></a>Hitelesítés

A cURL vagy a WebHCat teli rest-kommunikációjának használatakor hitelesítenie kell a kérelmeket a HDInsight-fürt rendszergazdájának felhasználónevének és jelszavának megadásával. A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Annak érdekében, hogy a hitelesítő adatok biztonságosan legyenek elküldve a kiszolgálónak, mindig biztonságos HTTP (HTTPS) használatával kérjen kéréseket.

### <a name="setup-preserve-credentials"></a>Telepítés (hitelesítő adatok megőrzése)

Őrizze meg hitelesítő adatait, hogy ne adja meg újra őket az egyes példákban.  A fürt neve egy külön lépésben marad meg.

**A. Bash**  
Az alábbi parancsfájlt `PASSWORD` a tényleges jelszóval való helyettesítéssel szerkesztheti.  Ezután adja meg a parancsot.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Az alábbi kód végrehajtása és a hitelesítő adatok megadása az előugró ablakban:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Megfelelően kis- és nagybetűk alapján a fürt nevének azonosítása

A fürtnév tényleges burkolata a fürt létrehozásának módjától függően eltérhet a várttól.  Az itt leírt lépések a tényleges burkolatot jelenítik meg, majd egy változóban tárolják az összes későbbi példához.

Az alábbi parancsfájlok szerkesztésével cserélje le `CLUSTERNAME` a fürt nevét. Ezután adja meg a parancsot. (A teljes tartománynév fürtneve nem tartalmazza a kis- és nagybetűk et.)

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

1. Annak ellenőrzéséhez, hogy tud-e csatlakozni a HDInsight-fürthöz, használja az alábbi parancsok egyikét:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    A következő höz hasonló választ kap:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Ezen parancs paraméterei a következők:

    * `-u`- A kérelem hitelesítéséhez használt felhasználónév és jelszó.
    * `-G`- Azt jelzi, hogy ez a kérés get művelet.

1. Az URL-cím `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`eleje minden kérésnél megegyezik. Az elérési `/status`út , azt jelzi, hogy a kérelem a kiszolgáló WebHCat (más néven Templeton) állapotának visszaadása. A Hive verzióját a következő paranccsal is kérheti:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Ez a kérés a következő höz hasonló választ ad vissza:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. A **log4jLogs**nevű tábla az alábbiakban a következőkben hozhat létre:

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

    Ez a kérés a POST metódust használja, amely a kérelem részeként adatokat küld a REST API-nak. A rendszer a következő adatértékeket küldi el a kéréssel együtt:

     * `user.name`- A parancsot futtató felhasználó.
     * `execute`- A HiveQL kijelentések végrehajtása.
     * `statusdir`- Az a könyvtár, ahhoz a könyvtárhoz, amelybe a feladat állapota van írva.

   Ezek az utasítások a következő műveleteket hajtják végre:

   * `DROP TABLE`- Ha a tábla már létezik, akkor törlődik.
   * `CREATE EXTERNAL TABLE`- Létrehoz egy új "külső" táblát a Hive-ban. A külső táblák csak a tábladefiníciót tárolják a Hive-ban. Az adatok az eredeti helyen maradnak.

     > [!NOTE]  
     > Külső táblákat kell használni, amikor azt várja, hogy az alapul szolgáló adatokat egy külső forrás frissíti. Például egy automatikus adatfeltöltési folyamat vagy egy másik MapReduce művelet.
     >
     > A külső tábla eldobása **nem** törli az adatokat, csak a tábladefiníciót.

   * `ROW FORMAT`- Hogyan formázzák az adatokat. Az egyes naplók mezőit szóköz választja el egymástól.
   * `STORED AS TEXTFILE LOCATION`- Ahol az adatok at tárolják (a példa/adatkönyvtár), és hogy szövegként tárolják.
   * `SELECT`- A **t4** oszlopban a **[HIBA]** értéket tartalmazó sorok számát jelöli ki. Ez az utasítás **3** értéket ad vissza, mivel három sor van, amelyek tartalmazzák ezt az értéket.

     > [!NOTE]  
     > Figyelje meg, hogy a HiveQL utasítások `+` közötti szóközöket a Curl karakterrel való használat esetén a karakter helyettesíti. A szóközt tartalmazó idézett értékeket, például a határolószert nem szabad a következővel `+`helyettesíteni.

      Ez a parancs egy feladatazonosítót ad vissza, amely a feladat állapotának ellenőrzésére használható.

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

    Ha a feladat befejeződött, az állapot **sikeres.**

1. Miután a feladat állapota **sikeresre**változott, lekérheti a feladat eredményeit az Azure Blob storage-ból. A `statusdir` lekérdezéssel átadott paraméter tartalmazza a kimeneti fájl helyét; ebben az `/example/rest`esetben. Ez a cím tárolja `example/curl` a kimenetet a könyvtárban a fürtök alapértelmezett tárolójában.

    Ezeket a fájlokat az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)segítségével listázhatja és töltheti le. Az Azure CLI azure storage-tal való használatáról az [Azure CLI használata az Azure Storage-szal](https://docs.microsoft.com/azure/storage/storage-azure-cli) című dokumentumban talál további információt.

## <a name="next-steps"></a>További lépések

A Hadoop hdinsight-on való egyéb működéséről további információt talál:

* [Az Apache Hive használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-hive.md)
* [A MapReduce használata az Apache Hadoop segítségével a HDInsighton](hdinsight-use-mapreduce.md)

A jelen dokumentumban használt REST API-ról a [WebHCat referenciadokumentumban](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) talál további információt.