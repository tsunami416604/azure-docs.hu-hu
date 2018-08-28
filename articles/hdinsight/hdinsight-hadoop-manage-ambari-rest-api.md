---
title: Figyelése és kezelése az Ambari REST API – Azure HDInsight Hadoop
description: Ismerje meg, hogyan figyelheti és kezelheti az Azure HDInsight Hadoop-fürtök az Ambari használatával. Ebből a dokumentumból megtudhatja hogyan használhatja a mellékelt a HDInsight-fürtök az Ambari REST API.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: jasonh
ms.openlocfilehash: d6e2bdba7e3536404f087dc468a0895d0be0c2a0
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43106019"
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>HDInsight-fürtök kezelése az Ambari REST API használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Megtudhatja, hogyan felügyelheti és figyelheti az Azure HDInsight Hadoop-fürtök az Ambari REST API használatával.

Az Apache Ambari leegyszerűsíti a kezelése és figyelése a Hadoop-fürt azáltal, hogy egy könnyen használható webes felhasználói felületen és a REST API-t. A Linux operációs rendszert használó HDInsight-fürtök az Ambari tartalmazza. Az Ambari használatával figyelheti a fürt és a konfigurációs módosításokat.

## <a id="whatis"></a>Mi az Ambari

[Az Apache Ambari](http://ambari.apache.org) biztosít a webes felhasználói felületen, kezelni és megfigyelni a Hadoop-fürtök használható. A fejlesztők beépíthetik ezeket a képességeket alkalmazásaikban használatával a [az Ambari REST API-k](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Alapértelmezett Linux-alapú HDInsight-fürtök az Ambari biztosítják.

## <a name="how-to-use-the-ambari-rest-api"></a>Az Ambari REST API használata

> [!IMPORTANT]
> Az adatokat, és ebben a dokumentumban szereplő példák szükség egy HDInsight-fürtöt, amely Linux operációs rendszert használ. További információkért lásd: [HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).

Ebben a dokumentumban szereplő példák a Bourne rendszerhéj (bash) és a PowerShell-okat. A bash-példák tesztelt GNU 4.3.11 bash verzióval, de más Unix parancskörnyezet együttműködve. A PowerShell-példák PowerShell 5.0-s tesztelt, de használható a PowerShell 3.0-s vagy újabb verziója.

Ha használja a __Bourne rendszerhéj__ (Bash), rendelkeznie kell a következőkkel:

* [cURL](http://curl.haxx.se/): cURL egy segédprogram, amely segítségével a parancssorból a REST API-k használata. Ebben a dokumentumban használatos az Ambari REST API folytatott kommunikációhoz.

A Bash vagy a PowerShell használatával, hogy is rendelkeznie kell [jq](https://stedolan.github.io/jq/) telepítve. Jq, a segédprogram JSON-dokumentumokon végzett munkához. Szerepel, **összes** Bash példákkal és **egy** , a PowerShell-példák.

### <a name="base-uri-for-ambari-rest-api"></a>Alap URI-azonosítóját az Ambari Rest API

A HDInsight az Ambari REST API az alap URI https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, ahol **CLUSTERNAME** a fürt neve.

> [!IMPORTANT]
> Kis-és a fürt nevét az URI-t (CLUSTERNAME.azurehdinsight.net) teljesen minősített neve (FQDN) részében pedig más előfordulások URI-t a nagybetűk között. Például, ha a fürt neve `MyCluster`, érvényes URI-azonosítók a következők:
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> A következő URI-k egy hibaüzenetet ad vissza, mert a második előfordulása a név nem a megfelelő eset.
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>Hitelesítés

Csatlakozás a HDInsight az Ambari HTTPS van szüksége. A rendszergazdai fiók nevét használja (az alapértelmezett érték **rendszergazdai**) és a fürt létrehozásakor megadott jelszót.

## <a name="examples-authentication-and-parsing-json"></a>Példák: Hitelesítés és a JSON-elemzés

Az alábbi példák bemutatják, hogyan lehet, hogy az Alap az Ambari REST API egy GET kérelmet:

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> A Bash-példák ebben a dokumentumban hajtsa végre a következő előfeltételek:
>
> * A fürt bejelentkezési neve, a rendszer az alapértelmezett érték `admin`.
> * `$CLUSTERNAME` a fürt nevét tartalmazza. Ezt az értéket beállíthat használatával `set CLUSTERNAME='clustername'`
> * Amikor a rendszer kéri, adja meg a jelszót a fürt bejelentkezési (rendszergazdai).

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> A PowerShell-példák ebben a dokumentumban hajtsa végre a következő előfeltételek:
>
> * `$creds` van egy hitelesítő objektumot, amely tartalmazza a rendszergazdai bejelentkezési nevét és a fürthöz tartozó jelszót. Használja ezt az értéket is megadhat `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` és a hitelesítő adatokat, amikor a rendszer kéri.
> * `$clusterName` : a fürt nevét tartalmazó karakterlánc. Használja ezt az értéket is megadhat `$clusterName="clustername"`.

Mindkét példa adja vissza egy JSON-dokumentumot, az alábbi példához hasonló információkat kezdődik:

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

Az alábbi példában `jq` a JSON-válasz dokumentum elemzése és megjelenítése csak a `health_report` információkat az eredmények közül.

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

A PowerShell 3.0-s vagy újabb biztosít a `ConvertFrom-Json` parancsmagot, amely konvertálja a JSON-dokumentum olyan objektumot, amely egyszerűbbé teszik a Powershellből. Az alábbi példában `ConvertFrom-Json` megjelenítése csak a `health_report` információkat az eredmények közül.

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> Bár ez a dokumentum használatát a legtöbb példa `ConvertFrom-Json` a válasz dokumentumból elemek megjelenítése a [frissítés Ambari konfigurációs](#example-update-ambari-configuration) példában jq. Jq ebben a példában a JSON-válasz dokumentum az új sablon létrehozására használható.

A REST API teljes körű referenciáért lásd: [Ambari API-referencia V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>Példa: Fürtcsomópontok teljes Tartománynevének beolvasása

Az HDInsight használatakor szükség lehet tudni, hogy egy fürt csomópontja teljesen minősített tartománynevét (FQDN). Később könnyen hozzáférhet a teljes Tartománynevet a különböző csomópontokhoz a fürtben, a következő példák segítségével:

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

* **Fő csomópontok**

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

* **Munkavégző csomópontok**

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

* **Zookeeper-csomópontok**

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

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>Példa: A fürtcsomópontok belső IP-címének lekéréséhez

> [!IMPORTANT]
> Ebben a szakaszban szereplő példák által visszaadott IP-címek nem érhetők el közvetlenül az interneten keresztül. Csak azok az Azure virtuális hálózatban, amely tartalmazza a HDInsight-fürtön elérhető.
>
> A HDInsight és a virtuális hálózatok használatáról további információkért lásd: [egy egyéni Azure Virtual Network használatával a HDInsight kiterjesztése képességek](hdinsight-extend-hadoop-virtual-network.md).

Keresse meg az IP-cím, ismernie kell a fürt csomópontjai belső teljesen minősített tartománynevét (FQDN). Miután a teljes Tartománynevét, majd kérheti a gazdagép IP-címét. Az alábbi példák először Ambari lekérdezheti a gazdagép-csomópontok teljes Tartománynevét, majd Ambari lekérdezni a minden gazdagép IP-címét.

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> Előző példák kéri a jelszót. Ebben a példában futtatja a `curl` parancs többször, ezért a jelszó van megadva `$PASSWORD` több utasításokat elkerülése érdekében.

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

Egy HDInsight-fürt létrehozásakor meg kell használnia egy Azure Storage-fiók vagy a Data Lake Store az alapértelmezett tárolóként a fürt számára. Az Ambari segítségével ezen információk lekérése, a fürt létrehozása után. Ha például szeretné az adatok olvasására/írására HDInsight kívül a tárolóhoz.

Az alábbi példák az alapértelmezett tárolási konfiguráció lekérése a fürt:

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
> Ezekben a példákban a alkalmazni a kiszolgáló első konfiguráció adja vissza (`service_config_version=1`) amely ezeket az információkat tartalmazza. Ha egy érték, amely a fürt létrehozása után módosították lekéréséhez szükség lehet a konfiguráció listázása és a töltik le a legújabb.

A visszaadott érték hasonlít az alábbi példák egyikét:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Ez az érték azt jelzi, hogy a fürt egy Azure Storage-fiókot használja az alapértelmezett tároló. A `ACCOUNTNAME` a storage-fiók neve. A `CONTAINER` része annak a neve, a storage-fiókban lévő blobtárolóba. A tároló-e a fürt HDFS-kompatibilis tárolás gyökerében.

* `adl://home` – Ez az érték azt jelzi, hogy a fürt van egy Azure Data Lake Store használatával az alapértelmezett tároló.

    A Data Lake Store-fiók nevét, használja az alábbi példák:

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

    A visszaadott érték hasonlít a `ACCOUNTNAME.azuredatalakestore.net`, ahol `ACCOUNTNAME` a Data Lake Store-fiók neve.

    A könyvtár belül, amely tartalmazza a fürt tárolóhelyét Data Lake Store, használja az alábbi példák:

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

    A visszaadott érték hasonlít a `/clusters/CLUSTERNAME/`. Ez az érték út a Data Lake Store-fiókon belül. Ez az elérési út a HDFS-kompatibilis a fájlrendszer a fürt gyökerében. 

> [!NOTE]
> A `Get-AzureRmHDInsightCluster` parancsmag által biztosított [Azure PowerShell-lel](/powershell/azure/overview) is információval a tároló a fürt számára.


## <a name="example-get-configuration"></a>Példa: Get-konfiguráció

1. Beolvasni a konfigurációkat, a fürt számára elérhető.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    Ebben a példában az aktuális konfigurációt tartalmazó JSON-dokumentumok adja vissza (által azonosított a *címke* érték) az összetevők telepítve van a fürtön. Az alábbi példa egy olyan, az adatokból egy Spark-fürt típusa által visszaadott.
   
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

2. Az összetevő, amely az Önt érdeklő konfigurációjának beolvasása. A következő példában cserélje le a `INITIAL` az előző kérelem által visszaadott a címkeértékkel rendelkezik.

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    Ebben a példában a vonatkozó aktuális konfigurációját tartalmazó JSON-dokumentumok adja vissza a `core-site` összetevő.

## <a name="example-update-configuration"></a>Példa: Konfigurációjának frissítése

1. Kérje le a jelenlegi konfigurációt, és a "szükségeskonfiguráció" Ambari tárolja:

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    Ebben a példában az aktuális konfigurációt tartalmazó JSON-dokumentumok adja vissza (által azonosított a *címke* érték) az összetevők telepítve van a fürtön. Az alábbi példa egy olyan, az adatokból egy Spark-fürt típusa által visszaadott.
   
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
   
    Ebből a listából, át kell másolnia az összetevő neve (például **spark\_thrift\_sparkconf** és a **címke** értéket.

2. Az összetevő és a címke konfigurációjának beolvasása a következő parancsokat:
   
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
    > Cserélje le **spark-thrift-sparkconf** és **kezdeti** az összetevő és a címkék, melyek szeretné beolvasni a konfigurációját.
   
    Jq segítségével kapcsolja be az új konfigurációs sablont a HDInsight-ből lekért adatokat. Pontosabban ezek a példák hajtsa végre a következő műveleteket:
   
    * Létrehoz egy egyedi értéket a "verziójú" karakterlánc és a dátum, ami a tartalmazó `newtag`.

    * Az új kívánt konfiguráció egy legfelső szintű dokumentumot hoz létre.

    * Lekérdezi a tartalmát a `.items[]` tömböt, és hozzáadja azt a a **desired_config** elemet.

    * Törli a `href`, `version`, és `Config` elemek, ezek az elemek nem szükségesek a küldje el az új konfigurációt.

    * Hozzáad egy `tag` értékkel rendelkező elem `version#################`. A numerikus részét az aktuális dátum alapján történik. Az egyes konfigurációkhoz egy egyedi címkével kell rendelkeznie.
     
    Végül az adatok mentve a `newconfig.json` dokumentumot. A dokumentum szerkezete az alábbi példához hasonlóan kell megjelennie:
     
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

3. Nyissa meg a `newconfig.json` dokumentum és a módosítása vagy hozzáadása értékeit a `properties` objektum. Az alábbi példa módosítja az értékét `"spark.yarn.am.memory"` a `"1g"` való `"3g"`. Bővíti ezenkívül `"spark.kryoserializer.buffer.max"` értékkel `"256m"`.
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    Ha kötheti elkészült, mentse a fájlt.

4. A frissített konfigurációt az Ambari küldhetnek a következő parancsok használatával.
   
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
   
    Ezek a parancsok, küldje el a tartalmát a **newconfig.json** a fürt, az új szükségeskonfiguráció-fájlt. A kérelem egy JSON-dokumentumot ad vissza. A **versionTag** elem ebben a dokumentumban meg kell egyeznie a verzió küldte el, és a **configs** objektum tartalmazza a kért konfigurációs módosításokat.

### <a name="example-restart-a-service-component"></a>Példa: Indítsa újra a szolgáltatás valamelyik összetevőjéhez

Ezen a ponton az Ambari webes Felülettel tekinti meg, ha a Spark szolgáltatás azt jelzi, hogy kell az új konfiguráció életbe léptetéséhez újra kell indítani. A következő lépéseket követve indítsa újra a szolgáltatást.

1. A Spark szolgáltatáshoz a karbantartási mód engedélyezéséhez használja a következő:

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
   
    Ezeket a parancsokat a kiszolgálót, amely a karbantartási mód bekapcsolása egy JSON-dokumentum küldése. Ellenőrizheti, hogy a szolgáltatás most már karbantartási módban van a következő kérelmet használatával:
   
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
   
    A visszaadott érték `ON`.

2. Ezután használja a következő kapcsolja ki a szolgáltatást:

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
    
    A válasz a következő példához hasonló:
   
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
    > A `href` ezt az URI által visszaadott értéket a fürtcsomópont a belső IP-címet használ. A használatához a fürtön kívül cserélje le a "10.0.0.18:8080" rész a fürt teljes Tartománynevét. 
    
    Az alábbi parancsokat a kérelem állapotának lekéréséhez:

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

3. Az előző kérelem befejeződése után a következő használatával indítsa el a szolgáltatást.
   
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
    A szolgáltatás most már használja az új konfigurációt.

4. Végül használja a következő karbantartási mód kikapcsolása.
   
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

## <a name="next-steps"></a>További lépések

A REST API teljes körű referenciáért lásd: [Ambari API-referencia V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

