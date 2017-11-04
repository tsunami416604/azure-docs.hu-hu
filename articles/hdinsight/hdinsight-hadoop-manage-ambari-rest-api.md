---
title: "Felügyeletéhez és kezeléséhez az Ambari REST API - Azure HDInsight Hadoop |} Microsoft Docs"
description: "Megtudhatja, hogyan figyelheti és kezelheti az Azure HDInsight Hadoop-fürtök az Ambari használatával. Ebben a dokumentumban, megtudhatja, hogyan használható az Ambari REST API-t a HDInsight-fürtök részét képező."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2017
ms.author: larryfr
ms.openlocfilehash: 33ff4b69a4a914e6c183b10bc47a077eea537e61
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>A HDInsight-fürtök kezelése az Ambari REST API használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Ismerje meg, hogy kezelni és megfigyelni az Azure HDInsight Hadoop-fürtök az Ambari REST API használatával.

Apache Ambari egyszerűbbé teszi a felügyeleti és a Hadoop-fürthöz, adja meg a webes felhasználói felület és a REST API használatát egy könnyen ellenőrzésére. Ambari szerepel-e a Linux operációs rendszer használata a HDInsight-fürtök. Ambari használatával figyelheti a fürt és a konfigurációs módosításokat.

## <a id="whatis"></a>Mi az az Ambari

[Apache Ambari](http://ambari.apache.org) biztosít a webes felhasználói felület, kezelni és megfigyelni a Hadoop-fürtök használható. A fejlesztők integrálható a ezeket a képességeket a alkalmazások használatával a [Ambari REST API-k](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Alapértelmezés szerint a Linux-alapú HDInsight-fürtök Ambari valósul meg.

## <a name="how-to-use-the-ambari-rest-api"></a>Az Ambari REST API használatával

> [!IMPORTANT]
> Információkat és példákat a jelen dokumentum igényelnek a Linux operációs rendszert használó HDInsight-fürtöt. További információkért lásd: [első lépései a hdinsight eszközzel](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Ebben a dokumentumban szereplő példák a Bourne rendszerhéj (bash) és a PowerShell-okat. A bash példák GNU 4.3.11 bash verziójával teszteltük, de más Unix ismertetése együtt kell működnie. A PowerShell-példák PowerShell 5.0 teszteltük, de a PowerShell 3.0-s vagy újabb verzióját kell működnie.

Ha használja a __Bourne rendszerhéj__ (Bash), rendelkeznie kell a következőkkel:

* [cURL](http://curl.haxx.se/): cURL egy segédprogram, amely a REST API-k használata a parancssorból is használható. Ebben a dokumentumban szolgál az Ambari REST API folytatott kommunikációhoz.

E Bash vagy a PowerShell használatával, rendelkeznie kell [jq](https://stedolan.github.io/jq/) telepítve. Jq olyan eszköz, amellyel a JSON-dokumentumok használata. Szerepel, **összes** Bash példák és **egy** a PowerShell-példák.

### <a name="base-uri-for-ambari-rest-api"></a>Alap URI-JÁNAK Ambari Rest API

Az Ambari REST API-t a HDInsight alap URI-azonosító https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, ahol **CLUSTERNAME** a fürt neve.

> [!IMPORTANT]
> Míg a fürt neve a teljesen minősített tartománynév (FQDN) része az URI (CLUSTERNAME.azurehdinsight.net) a nagybetűk között, más előfordulások URI azonosítójában nagybetűk között. Például, ha a fürt neve `MyCluster`, érvényes URI-azonosítók a következők:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> A következő URI-k egy hibaüzenetet adja vissza, mert a név a második előfordulása nincs kis-és nagybetűk.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Authentication

A HDInsight Ambari való csatlakozás igényel a HTTPS PROTOKOLLT. A rendszergazda fiók nevét használja (az alapértelmezett érték **admin**) és a fürt létrehozása során megadott jelszót.

## <a name="examples-authentication-and-parsing-json"></a>Példák: Hitelesítés és elemzése JSON

Az alábbi példák bemutatják, hogyan végezheti el az alap Ambari REST API egy GET kérelmet:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> Ebben a dokumentumban a Bash példák hajtsa végre a következő előfeltételek:
>
> * A bejelentkezési név a fürt az alapértelmezett értékének `admin`.
> * `$CLUSTERNAME`a fürt nevét tartalmazza. Használja ezt az értéket is megadhat`set CLUSTERNAME='clustername'`
> * Amikor a rendszer kéri, adja meg a jelszót a fürt bejelentkezési azonosítóhoz (rendszergazda).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> Ebben a dokumentumban a PowerShell-példák hajtsa végre a következő előfeltételek:
>
> * `$creds`a hitelesítő objektum, amely tartalmazza a rendszergazdai bejelentkezés és a jelszót a fürthöz van. Használja ezt az értéket is megadhat `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` , és adja meg a hitelesítő adatokat, amikor a rendszer kéri.
> * `$clusterName`egy olyan karakterlánc, amely a fürt nevét tartalmazza. Használja ezt az értéket is megadhat `$clusterName="clustername"`.

Mindkét példák adja vissza egy JSON-dokumentum információkat az alábbi példához hasonló karakterlánccal kezdődik:

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>JSON-adatok elemzése

Az alábbi példában `jq` elemezni a JSON-válasz dokumentum ki és jelenítheti meg a csak a `health_report` az eredmények adatait.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

A PowerShell 3.0-s és újabb rendszer biztosítja a `ConvertFrom-Json` parancsmagot, amely a JSON-dokumentum alakít át egy objektumot, amely egyszerűbbé teszik a Powershellből. Az alábbi példában `ConvertFrom-Json` megjelenítése csak a `health_report` az eredmények adatait.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Ez a dokumentum használatát a legtöbb példa során `ConvertFrom-Json` a válasz dokumentumból elemek megjelenítéséhez a [frissítés Ambari konfigurációs](#example-update-ambari-configuration) példában jq. Jq szerepel ebben a példában a JSON-válasz dokumentum az új sablon létrehozásához.

A REST API-t a teljes referenciáért lásd: [Ambari API-referencia V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Példa: Első fürtcsomópont teljes Tartományneve

A HDInsight használata, amikor szükség lehet tudni, hogy a teljesen minősített tartományneve (FQDN) egy fürt csomópontja. A különböző csomópontok használatával az alábbi példák a fürt teljes Tartománynevének könnyen le:

* **Minden csomópont**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **HEAD csomópontok**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Munkavégző csomópontokhoz**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Zookeeper csomópontok**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Példa: A belső IP-cím fürtcsomópontok beolvasása

> [!IMPORTANT]
> Az ebben a szakaszban szereplő példák által visszaadott IP-címek nem érhetők el közvetlenül az interneten keresztül. Csak azok a HDInsight-fürt tartalmazó Azure virtuális hálózaton belülről érhetők el.
>
> További információk a HDInsight és virtuális hálózatok: [kiterjesztése HDInsight képességek egyéni Azure virtuális hálózat használatával](hdinsight-extend-hadoop-virtual-network.md).

Az IP-címének ismernie kell a fürt csomópontjai belső teljesen minősített tartománynevét (FQDN). Miután a teljes Tartománynevét, majd érheti el a gazdagép IP-címét. Az alábbi példák először Ambari kereshet a gazdagép-csomópontok teljes Tartománynevét, majd Ambari lekérdezni az egyes állomások IP-címét.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> Előző példák kéri a jelszót. Ebben a példában a `curl` parancs többször, ezért a jelszó van megadva `$PASSWORD` több kér elkerülése érdekében.

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>Példa: Az alapértelmezett tároló beolvasása

HDInsight-fürtök létrehozásakor kell használnia az Azure Storage-fiók vagy a Data Lake Store az alapértelmezett tárolóként a fürthöz. Ambari segítségével ezt az információt lekérni a fürt létrehozása után. Ha például szeretné az adatokat a tárolót a HDInsight kívül olvasására vagy írására.

Az alábbi példák a alapértelmezett tárolási konfiguráció lekérése a fürt:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> Ezek a példák, térjen vissza az első konfiguráció, a kiszolgálón alkalmazott (`service_config_version=1`) amely tartalmazza ezt az információt. Ha beolvasni egy érték, amely a fürt létrehozása után módosítva lett, szükség lehet a konfiguráció listában, és töltik le a legújabb.

A visszatérési érték a következő példák hasonló:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net`-Ez az érték azt jelzi, hogy a fürt alapértelmezett tárolására használt Azure Storage-fiók. A `ACCOUNTNAME` értéke a tárfiók neve. A `CONTAINER` részét pedig a tárfiók a blob-tároló neve. A tároló a fürt HDFS-kompatibilis tárolási gyökérmappájában.

* `adl://home`-Ez az érték azt jelzi, hogy a fürt egy Azure Data Lake Store használt alapértelmezett tárolási.

    A Data Lake Store-fiók neve megkereséséhez használja az alábbi példákat:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    Az eredményül kapott értéket hasonlít `ACCOUNTNAME.azuredatalakestore.net`, ahol `ACCOUNTNAME` a Data Lake Store-fiók neve.

    A könyvtár belül, amely tartalmazza a fürt tárolóhelyét Data Lake Store megkereséséhez használja az alábbi példákat:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    Az eredményül kapott értéket hasonlít `/clusters/CLUSTERNAME/`. Ez az érték a Data Lake Store-fiókban út. Ez az elérési út a HDFS-kompatibilis fájlrendszer a fürt gyökérmappájában. 

> [!NOTE]
> A `Get-AzureRmHDInsightCluster` parancsmag által biztosított [Azure PowerShell](/powershell/azure/overview) is a a fürt tárolási információkat ad vissza.


## <a name="example-get-configuration"></a>Példa: Get-konfiguráció

1. A konfigurációk a fürt számára rendelkezésre álló beolvasása.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Ebben a példában a jelenlegi konfiguráció tartalmazó JSON-dokumentum adja vissza (azonosíthatók a *címke* érték) a fürtben telepített összetevőket. A következő példa egy fájlból egy Spark-fürt típusa által visszaadott adatokat.
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. A konfiguráció megváltozása összetevő beolvasása. Az alábbi példában cserélje le `INITIAL` az előző kérelem által visszaadott a címke értékű.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Ebben a példában a vonatkozó aktuális konfigurációját tartalmazó JSON-dokumentum adja vissza a `core-site` összetevő.

## <a name="example-update-configuration"></a>Példa: Konfigurációjának frissítése

1. Töltse le a jelenlegi konfigurációt, és a "szükségeskonfiguráció" Ambari tárolja:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Ebben a példában a jelenlegi konfiguráció tartalmazó JSON-dokumentum adja vissza (azonosíthatók a *címke* érték) a fürtben telepített összetevőket. A következő példa egy fájlból egy Spark-fürt típusa által visszaadott adatokat.
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    Ebből a listából kell másolnia az összetevő neve (például **spark\_thrift\_sparkconf** és a **címke** érték.

2. Az összetevő és a címke konfigurációjának beolvasása a következő parancsokkal:
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > Cserélje le **spark-thrift-sparkconf** és **kezdeti** az összetevő és a tag, szeretné beolvasni a konfigurációját.
   
    Kapcsolja be az új konfigurációs sablonba a HDInsight-ból beolvasott adat Jq szolgál. Pontosabban ezek a példák a következő műveleteket:
   
    * Létrehoz egy egyedi értéket a "version" karakterláncot és a dátum, ami a tartalmazó `newtag`.

    * Az új szükségeskonfiguráció a legfelső szintű dokumentum létrehozása.

    * Lekérdezi a tartalmát a `.items[]` tömb, és hozzáadja azt a a **desired_config** elemet.

    * Törli a `href`, `version`, és `Config` elemek, ezeket az elemeket, nem szükségesek a elküldeni a konfigurációt.

    * Hozzáad egy `tag` elem értéke az `version#################`. A numerikus része az aktuális dátumot alapul. Minden egyes-konfiguráció egyedi kódot kell rendelkeznie.
     
    Végül, az adatok mentése a `newconfig.json` dokumentum. A dokumentum struktúra az alábbi példához hasonlóan kell megjelennie:
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. Nyissa meg a `newconfig.json` dokumentum és a módosítása vagy hozzáadása az `properties` objektum. Az alábbi példa értékének megváltoztatása `"spark.yarn.am.memory"` a `"1g"` való `"3g"`. Bővíti ezenkívül `"spark.kryoserializer.buffer.max"` értékkel rendelkező `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Ha elkészült, hogy a módosításokat, mentse a fájlt.

4. A frissített konfiguráció Ambari küldhetnek a következő parancsok használatával.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    Ezek a parancsok, küldje el a tartalmát a **newconfig.json** a fürtön, amelyen az új szükségeskonfiguráció-fájlt. A kérelem egy JSON-dokumentum adja vissza. A **versionTag** elem ebben a dokumentumban meg kell felelnie a verzió benyújtott, és a **configs** objektum tartalmazza a kért konfigurációs módosításokat.

### <a name="example-restart-a-service-component"></a>Példa: Indítsa újra a szolgáltatás valamelyik összetevője

Ezen a ponton az Ambari webes felhasználói felület tekinti meg, ha a Spark szolgáltatást azt jelzi, hogy azt az új konfiguráció életbe léptetéséhez újra kell indítani. Az alábbi lépések segítségével indítsa újra a szolgáltatást.

1. A Spark szolgáltatás karbantartási mód engedélyezéséhez használja a következőket:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    Ezek a parancsok egy JSON-dokumentum küldeni a kiszolgáló, amely bekapcsolja a karbantartási módból. Ellenőrizheti, hogy a szolgáltatás jelenleg karbantartási módba a következő kérelmet:
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    A visszatérési érték `ON`.

2. Kapcsolja ki a szolgáltatás ezt követően használja a következő:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    A rendszer a választ az alábbi példához hasonló:
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > A `href` ezt az URI által visszaadott értéket használja a fürtcsomópont belső IP-címét. A használatához a fürtön kívül cserélje le a "10.0.0.18:8080" rész a fürt teljes Tartománynevét. 
    
    Az alábbi parancsokat a kérelem állapotának beolvasása:

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    A válasz `COMPLETED` azt jelzi, hogy a kérelem befejeződött.

3. Miután befejeződött az előző kérést, a szolgáltatás elindításához használja a következő.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    A szolgáltatás az új konfigurációt használ.

4. Végezetül a következő használatával kapcsolja ki a karbantartási módból.
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Következő lépések

A REST API-t a teljes referenciáért lásd: [Ambari API-referencia V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

