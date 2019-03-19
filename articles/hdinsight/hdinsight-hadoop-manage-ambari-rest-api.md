---
title: Figyelése és kezelése az Ambari REST API – Azure HDInsight Hadoop
description: Ismerje meg, hogyan figyelheti és kezelheti az Azure HDInsight Hadoop-fürtök az Ambari használatával. Ebből a dokumentumból megtudhatja hogyan használhatja a mellékelt a HDInsight-fürtök az Ambari REST API.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: 38d845e133b5ae1a091fd9fee698b2bf801135e1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880595"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>HDInsight-fürtök kezelése az Apache Ambari REST API használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Ismerje meg, hogyan kezelése és figyelése Apache Hadoop-fürtök Azure HDInsight az Apache Ambari REST API használatával.

## <a id="whatis"></a>Mi az Apache Ambari
[Az Apache Ambari](https://ambari.apache.org) leegyszerűsíti a felügyeleti és azáltal, hogy egy könnyen használható webes felhasználói felület által támogatott Hadoop-fürtök figyelése az [REST API-k](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Alapértelmezett Linux-alapú HDInsight-fürtök az Ambari biztosítják.

## <a name="prerequisites"></a>Előfeltételek
* **A HDInsight Hadoop-fürt**. Lásd: [HDInsight Linux első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Bash on Ubuntu on Windows 10-es**.  Ebben a cikkben szereplő példák a bash on Windows 10 használja. Lásd: [Linux telepítési útmutató a Windows 10-es Windows-alrendszer](https://docs.microsoft.com/windows/wsl/install-win10) a telepítési lépéseket.  Más [Unix parancskörnyezet](https://www.gnu.org/software/bash/) is működnek.  A példák, néhány kisebb módosításokkal, egy Windows parancssorban is működik.  Másik lehetőségként használhatja a Windows PowerShell.

* **jq**, egy parancssori JSON feldolgozó.  Lásd: [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).

* **Windows PowerShell**.  Másik lehetőségként használhatja [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uri-for-ambari-rest-api"></a>Alap URI-azonosítóját az Ambari Rest API

 A HDInsight az Ambari REST API az alap URI `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, ahol `CLUSTERNAME` a fürt neve.  Fürt neve, az URI-k **kis-és nagybetűket**.  Kis-és a fürt nevét az URI-t (CLUSTERNAME.azurehdinsight.net) teljesen minősített neve (FQDN) részében pedig más előfordulások URI-t a nagybetűk között.

## <a name="authentication"></a>Authentication

Csatlakozás a HDInsight az Ambari HTTPS van szüksége. A rendszergazdai fiók nevét használja (az alapértelmezett érték **rendszergazdai**) és a fürt létrehozásakor megadott jelszót.

## <a name="examples"></a>Példák

### <a name="setup-preserve-credentials"></a>A telepítő (Preserve hitelesítő adatok)
Őrzi meg a hitelesítő adatait, ismét be kell írni őket minden egyes például elkerülése érdekében.  A fürt nevét a rendszer egy külön lépésben megőrzi.

**EGY. Bash**  
Az alábbi parancsfájlt szerkesztése lecserélésével `PASSWORD` a tényleges jelszóval.  Ezután írja be a parancsot.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Azonosíthatja a fürt megfelelően kisbetűsek neve
A fürt nevét, a tényleges kis-és várt a fürt létrehozási módjától függően eltérő lehet.  A lépéseket a tényleges kis-és megjelenítése, és minden további példák egy változóban tárolja majd.

Cserélje le az alábbi parancsfájlok szerkesztése `CLUSTERNAME` a fürt nevére. Ezután írja be a parancsot. (A fürt teljes név nem kis-és nagybetűket.)

**EGY. Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLSUTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
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

### <a name="parsing-json-data"></a>JSON-adatok elemzése

Az alábbi példában [jq](https://stedolan.github.io/jq/) vagy [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) a JSON-válasz dokumentum elemzése és megjelenítése csak a `health_report` információkat az eredmények közül.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> Fürtcsomópontok teljes Tartománynevének beolvasása

Az HDInsight használatakor szükség lehet tudni, hogy egy fürt csomópontja teljesen minősített tartománynevét (FQDN). Később könnyen hozzáférhet a teljes Tartománynevet a különböző csomópontokhoz a fürtben, a következő példák segítségével:

**Minden csomópont**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Fő csomópontok**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Munkavégző csomópontok**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper-csomópontok**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> A fürtcsomópontok belső IP-címének lekéréséhez

Ebben a szakaszban szereplő példák által visszaadott IP-címek nem érhetők el közvetlenül az interneten keresztül. Csak azok az Azure virtuális hálózatban, amely tartalmazza a HDInsight-fürtön elérhető.

A HDInsight és a virtuális hálózatok használatáról további információkért lásd: [egy egyéni Azure Virtual Network használatával a HDInsight kiterjesztése képességek](hdinsight-extend-hadoop-virtual-network.md).

Keresse meg az IP-cím, ismernie kell a fürt csomópontjai belső teljesen minősített tartománynevét (FQDN). Miután a teljes Tartománynevét, majd kérheti a gazdagép IP-címét. Az alábbi példák először Ambari lekérdezheti a gazdagép-csomópontok teljes Tartománynevét, majd Ambari lekérdezni a minden gazdagép IP-címét.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Az alapértelmezett tároló beolvasása

Egy HDInsight-fürt létrehozásakor meg kell használnia egy Azure Storage-fiók vagy a Data Lake Storage az alapértelmezett tárolóként a fürt számára. Az Ambari segítségével ezen információk lekérése, a fürt létrehozása után. Ha például szeretné az adatok olvasására/írására HDInsight kívül a tárolóhoz.

Az alábbi példák az alapértelmezett tárolási konfiguráció lekérése a fürt:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Ezekben a példákban a alkalmazni a kiszolgáló első konfiguráció adja vissza (`service_config_version=1`) amely ezeket az információkat tartalmazza. Ha egy érték, amely a fürt létrehozása után módosították lekéréséhez szükség lehet a konfiguráció listázása és a töltik le a legújabb.

A visszaadott érték hasonlít az alábbi példák egyikét:

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` – Ez az érték azt jelzi, hogy a fürt egy Azure Storage-fiókot használja az alapértelmezett tároló. A `ACCOUNTNAME` a storage-fiók neve. A `CONTAINER` része annak a neve, a storage-fiókban lévő blobtárolóba. A tároló-e a fürt HDFS-kompatibilis tárolás gyökerében.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` – Ez az érték azt jelzi, hogy a fürt az Azure Data Lake Storage Gen2 használja alapértelmezett tárolóként. A `ACCOUNTNAME` és `CONTAINER` értékek jelentése mint azt korábban említettük, az Azure Storage.

* `adl://home` – Ez az érték azt jelzi, hogy a fürt az Azure Data Lake Storage Gen1 használja alapértelmezett tárolóként.

    A Data Lake Storage-fiók nevét, használja az alábbi példák:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    A visszaadott érték hasonlít a `ACCOUNTNAME.azuredatalakestore.net`, ahol `ACCOUNTNAME` a Data Lake Storage-fiók neve.

    Data Lake Storage, amely tartalmazza a fürt tárolóhelyét a címtárhoz, használja az alábbi példák:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    A visszaadott érték hasonlít a `/clusters/CLUSTERNAME/`. Ez az érték út a Data Lake Storage-fiókon belül. Ez az elérési út a HDFS-kompatibilis a fájlrendszer a fürt gyökerében.  

> [!NOTE]  
> A [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) parancsmag által biztosított [Azure PowerShell-lel](/powershell/azure/overview) is információval a tároló a fürt számára.


### <a name="get-all-configurations"></a> Az összes konfiguráció beolvasása

Beolvasni a konfigurációkat, a fürt számára elérhető.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Ebben a példában az aktuális konfigurációt tartalmazó JSON-dokumentumok adja vissza (által azonosított a *címke* érték) az összetevők telepítve van a fürtön. Az alábbi példa egy olyan, az adatokból egy Spark-fürt típusa által visszaadott.
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Az adott összetevő konfigurációjának lekérése

Az összetevő, amely az Önt érdeklő konfigurációjának beolvasása. A következő példában cserélje le a `INITIAL` az előző kérelem által visszaadott a címkeértékkel rendelkezik.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Ebben a példában a vonatkozó aktuális konfigurációját tartalmazó JSON-dokumentumok adja vissza a `livy2-conf` összetevő.

### <a name="update-configuration"></a>Konfiguráció frissítése

1. Hozzon létre `newconfig.json`.  
   Módosíthatja, és adja meg az alábbi parancsokat:

   * Cserélje le `livy2-conf` a kívánt összetevőt.
   * Cserélje le `INITIAL` beolvasni a tényleges értékkel `tag` a [az összes konfiguráció lekérése](#Get-all-configurations).

     **EGY. Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **B. powershell**  
     A PowerShell-szkript [jq](https://stedolan.github.io/jq/).  Szerkesztés `C:\HD\jq\jq-win64` tükrözik a tényleges elérési út és verziója az alábbi [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

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
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Szerkesztés `newconfig.json`.  
   Nyissa meg a `newconfig.json` dokumentum és a módosítása vagy hozzáadása értékeit a `properties` objektum. Az alábbi példa módosítja az értékét `"livy.server.csrf_protection.enabled"` a `"true"` való `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Ha kötheti elkészült, mentse a fájlt.

3. Küldje el `newconfig.json`.  
   A frissített konfigurációt az Ambari küldhetnek a következő parancsok használatával.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Ezek a parancsok, küldje el a tartalmát a **newconfig.json** a fürt, az új szükségeskonfiguráció-fájlt. A kérelem egy JSON-dokumentumot ad vissza. A **versionTag** elem ebben a dokumentumban meg kell egyeznie a verzió küldte el, és a **configs** objektum tartalmazza a kért konfigurációs módosításokat.

### <a name="restart-a-service-component"></a>Indítsa újra a szolgáltatás valamelyik összetevőjéhez

Ezen a ponton az Ambari webes Felülettel tekinti meg, ha a Spark szolgáltatás azt jelzi, hogy kell az új konfiguráció életbe léptetéséhez újra kell indítani. A következő lépéseket követve indítsa újra a szolgáltatást.

1. Karbantartási mód a Spark2 szolgáltatás engedélyezéséhez használja a következő:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. Karbantartási mód ellenőrzése  

    Ezeket a parancsokat a kiszolgálót, amely a karbantartási mód bekapcsolása egy JSON-dokumentum küldése. Ellenőrizheti, hogy a szolgáltatás most már karbantartási módban van a következő kérelmet használatával:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    A visszaadott érték `ON`.

3. Ezután használja a következő kapcsolja ki az Spark2 szolgáltatást:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
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

4. Ellenőrizze a kérelmet.  
    Az alábbi parancsot szerkesztése lecserélésével `29` a tényleges értékkel `id` adja vissza az előző lépésben.  Az alábbi parancsokat a kérelem állapotának lekéréséhez:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    A válasz `COMPLETED` azt jelzi, hogy a kérelem befejeződött.

5. Az előző kérelem befejeződése után a következő használatával indítsa el a Spark2 szolgáltatást.
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    A szolgáltatás most már használja az új konfigurációt.

6. Végül használja a következő karbantartási mód kikapcsolása.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>További lépések

A REST API teljes körű referenciáért lásd: [Apache Ambari API-referencia V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

