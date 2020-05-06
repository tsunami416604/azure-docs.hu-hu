---
title: Hadoop figyelése és kezelése a Ambari REST API – Azure HDInsight
description: Ismerje meg, hogyan használhatja a Ambari az Hadoop-fürtök figyelésére és kezelésére az Azure HDInsight. Ebből a dokumentumból megtudhatja, hogyan használható a HDInsight-fürtökhöz tartozó Ambari-REST API.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 48602cb65430bcf6720b4d6f4ba05c771a7bd55b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559961"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>HDInsight-fürtök kezelése az Apache Ambari REST API használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Ismerje meg, hogyan kezelheti és figyelheti Apache Hadoop-fürtöket az Azure HDInsight az Apache Ambari REST API használatával.

## <a name="what-is-apache-ambari"></a>Mi az Apache Ambari?

Az Apache Ambari megkönnyíti a Hadoop-fürtök felügyeletét és figyelését azáltal, hogy könnyen használható webes felhasználói felületet biztosít a [REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)-kkal.  A Ambari alapértelmezés szerint a Linux-alapú HDInsight-fürtökhöz van megadva.

## <a name="prerequisites"></a>Előfeltételek

* Hadoop-fürt a HDInsight-on. Lásd: Ismerkedés [a HDInsight Linux rendszeren](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash on Ubuntu on Windows 10.  A cikkben szereplő példák a bash rendszerhéjt használják a Windows 10 rendszeren. A telepítési lépésekért lásd: [Windows-alrendszer Linux-telepítési útmutató Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) rendszerhez.  Más [UNIX-rendszerhéj](https://www.gnu.org/software/bash/) is működik.  Néhány kis módosítással a Windows parancssorban is működhet.  Vagy használhatja a Windows PowerShellt is.

* jQ, parancssori JSON-processzor.  Lásd [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/):.

* Windows PowerShell.  Vagy használhatja a bash-t is.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Alapszintű Uniform Resource Identifier a Ambari REST API-hoz

 A HDInsight Ambari REST API alapszintű Uniform Resource Identifier (URI) `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, ahol `CLUSTERNAME` a a fürt neve.  Az URI-k fürtjének nevei **megkülönböztetik a kis-és nagybetűket**.  Míg a fürt neve az URI (`CLUSTERNAME.azurehdinsight.net`) teljes TARTOMÁNYNEVE (FQDN) része, a kis-és nagybetűk megkülönböztetése, az URI-n belüli más előfordulások megkülönböztetik a kis-és nagybetűket.

## <a name="authentication"></a>Hitelesítés

A HDInsight Ambari-hez való csatlakozáshoz HTTPS szükséges. Használja a rendszergazdai fiók nevét (az alapértelmezett a **rendszergazda**) és a jelszót, amelyet a fürt létrehozásakor adott meg.

Enterprise Security Package-fürtök esetében a helyett `admin`használjon egy teljesen minősített felhasználónevet, `username@domain.onmicrosoft.com`például:.

## <a name="examples"></a>Példák

### <a name="setup-preserve-credentials"></a>Beállítás (hitelesítő adatok megőrzése)

Őrizze meg a hitelesítő adatait, hogy elkerülje az egyes példák újbóli beírását.  A fürt nevét külön lépésben megőrzi a rendszer.

**A. bash**  
Szerkessze az alábbi szkriptet `PASSWORD` úgy, hogy lecseréli a tényleges jelszavát.  Ezután adja meg a parancsot.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>A megfelelő tokozású fürt nevének azonosítása

A fürt nevének tényleges burkolata eltérő lehet a vártnál.  Az itt látható lépések megjelenítik a tényleges burkolatot, majd egy változóban tárolják az összes későbbi példát.

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

### <a name="parsing-json-data"></a>JSON-adatbázis elemzése

A következő példa a [jQ](https://stedolan.github.io/jq/) vagy a [ConvertFrom-JSON](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) használatával értelmezi a JSON-válasz dokumentumát `health_report` , és csak az eredményekből jeleníti meg az adatokat.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>Fürtcsomópontok teljes tartománynevének beolvasása

Előfordulhat, hogy ismernie kell egy fürtcsomópont teljes tartománynevét (FQDN). A következő példákkal egyszerűen lekérheti a fürt különböző csomópontjainak teljes tartománynevét:

**Minden csomópont**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>A fürtcsomópontok belső IP-címének beolvasása

Az ebben a szakaszban szereplő példák által visszaadott IP-címek nem érhetők el közvetlenül az interneten keresztül. Ezek csak a HDInsight-fürtöt tartalmazó Azure Virtual Networkon érhetők el.

A HDInsight és a virtuális hálózatok használatáról további információt a [virtuális hálózat megtervezése a HDInsight számára](hdinsight-plan-virtual-network-deployment.md)című témakörben talál.

Az IP-cím megkereséséhez ismernie kell a fürtcsomópontok belső teljes tartománynevét (FQDN). Ha a teljes tartománynevet elvégezte, lekérheti a gazdagép IP-címét. Az alábbi példák az összes Ambari teljes tartománynevére vonatkozó első lekérdezési műveletet mutatják be. Ezután lekérdezi az egyes gazdagépek IP-címének Ambari.

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

A HDInsight-fürtöknek egy Azure Storage-fiókot vagy Data Lake Storage kell használniuk alapértelmezett tárolóként. Ezt az információt a Ambari használatával kérheti le a fürt létrehozása után. Ha például az HDInsight-on kívüli tárolóba szeretne olvasni/írni az adattárolást.

Az alábbi példák a fürt alapértelmezett tárolási konfigurációját kérik le:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Ezek a példák a kiszolgálóra (`service_config_version=1`) vonatkozó első konfigurációt adják vissza, amely tartalmazza ezt az információt. Ha olyan értéket kér le, amely a fürt létrehozása után módosult, előfordulhat, hogy fel kell sorolnia a konfigurációs verzióit, és le kell kérnie a legújabbat.

A visszatérési érték az alábbi példák egyikéhez hasonló:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`– Ez az érték azt jelzi, hogy a fürt egy Azure Storage-fiókot használ az alapértelmezett tárolóhoz. Az `ACCOUNTNAME` érték a Storage-fiók neve. A `CONTAINER` rész a blob tároló neve a Storage-fiókban. A tároló a fürt HDFS-kompatibilis tárterületének gyökere.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`– Ez az érték azt jelzi, hogy a fürt Azure Data Lake Storage Gen2 használ az alapértelmezett tárolóhoz. A `ACCOUNTNAME` és `CONTAINER` az értékek azonos jelentéssel rendelkeznek, mint a korábban említett Azure Storage-hoz.

* `adl://home`– Ez az érték azt jelzi, hogy a fürt Azure Data Lake Storage Gen1 használ az alapértelmezett tárolóhoz.

    A Data Lake Storage fiók nevének megkereséséhez használja az alábbi példákat:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    A visszatérési érték a következőhöz `ACCOUNTNAME.azuredatalakestore.net`hasonló `ACCOUNTNAME` :, ahol a a Data Lake Storage fiók neve.

    A fürt tárolóját tartalmazó Data Lake Storageon belüli könyvtár megkereséséhez használja az alábbi példákat:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    A visszatérési érték hasonló a `/clusters/CLUSTERNAME/`következőhöz:. Ez az érték a Data Lake Storage fiókon belüli elérési út. Ez az elérési út a fürt HDFS-kompatibilis fájlrendszerének gyökere.  

> [!NOTE]  
> A [Azure PowerShell](/powershell/azure/overview) által megadott [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) parancsmag a fürt tárolási adatait is visszaadja.

### <a name="get-all-configurations"></a>Összes konfiguráció beolvasása

A fürthöz elérhető konfigurációk beolvasása.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Ez a példa egy JSON-dokumentumot ad vissza, amely a telepített összetevők aktuális konfigurációját tartalmazza. Tekintse meg a *címke* értékét. A következő példa egy Spark-fürt típusból visszaadott adatok kivonata.

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

### <a name="get-configuration-for-specific-component"></a>Adott összetevő konfigurációjának beolvasása

Szerezze be annak az összetevőnek a konfigurációját, amelyre kíváncsi. Az alábbi példában cserélje le `INITIAL` az elemet az előző kérelemből visszaadott címke értékre.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Ez a példa egy JSON-dokumentumot ad vissza, amely az `livy2-conf` összetevő aktuális konfigurációját tartalmazza.

### <a name="update-configuration"></a>Konfiguráció frissítése

1. Létrehozás `newconfig.json`.  
   Módosítsa, majd írja be az alábbi parancsokat:

   * Cserélje `livy2-conf` le az elemet az új összetevőre.
   * Cserélje `INITIAL` le a értéket a beolvasás tényleges értékére `tag` az [összes konfigurációból](#get-all-configurations).

     **A. bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     A PowerShell-szkript [jQ](https://stedolan.github.io/jq/)használ.  Az `C:\HD\jq\jq-win64` alábbi szerkesztéssel jelenítheti meg a [jQ](https://stedolan.github.io/jq/)aktuális elérési útját és verzióját.

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     A jQ a HDInsight-ből beolvasott adatok új konfigurációs sablonba való bekapcsolására szolgálnak. Ezek a példák a következő műveleteket végzik el:

   * Létrehoz egy egyedi értéket, amely tartalmazza a "version" karakterláncot és a dátumot, amely `newtag`a ben tárolódik.

   * Létrehoz egy legfelső szintű dokumentumot az új konfigurációhoz.

   * Lekéri a `.items[]` tömb tartalmát, és hozzáadja azt a **desired_config** elemhez.

   * Törli a `href`, `version`a és `Config` az elemeket, mivel ezek az elemek nem szükségesek új konfiguráció elküldéséhez.

   * Hozzáadott egy `tag` elemet a értékkel `version#################`. A numerikus rész az aktuális dátumon alapul. Minden konfigurációnak egyedi címkével kell rendelkeznie.

     Végül a rendszer menti az adatfájlokat a `newconfig.json` dokumentumba. A dokumentum struktúrájának az alábbi példához hasonlóan kell megjelennie:

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
   Nyissa `newconfig.json` meg a dokumentumot, és módosítsa/adja `properties` hozzá a kívánt értékeket az objektumhoz. A következő példa a értékét a `"livy.server.csrf_protection.enabled"` értékre `"true"` módosítja `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    A módosítások végrehajtása után mentse a fájlt.

3. Küldés `newconfig.json`.  
   A frissített konfiguráció Ambari való elküldéséhez használja a következő parancsokat.

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

    Ezek a parancsok a **newconfig. JSON** fájl tartalmát az új konfigurációként küldi el a fürtön. A kérelem egy JSON-dokumentumot ad vissza. A dokumentum **versionTag** elemének meg kell egyeznie az elküldött verzióval, és a **konfigurációk** objektum tartalmazza a kért konfigurációs módosításokat.

### <a name="restart-a-service-component"></a>Szolgáltatás-összetevő újraindítása

Ezen a ponton a Ambari webes felület azt jelzi, hogy a Spark szolgáltatást újra kell indítani az új konfiguráció érvénybe léptetéséhez. A szolgáltatás újraindításához kövesse az alábbi lépéseket.

1. A következő paranccsal engedélyezheti a karbantartási módot a Spark2 szolgáltatás számára:

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
    ```

2. Karbantartási mód ellenőrzése  

    Ezek a parancsok olyan JSON-dokumentumot küldenek a kiszolgálónak, amely bekapcsolja a karbantartási módot. A következő kéréssel ellenőrizheti, hogy a szolgáltatás már karbantartási módban van-e:

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

    A visszatérési érték `ON`:.

3. Ezután a Spark2 szolgáltatás kikapcsolásához használja a következőt:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > Az `href` URI által visszaadott érték a fürtcsomópont belső IP-címét használja. Ha a fürtön kívülről szeretné használni, cserélje `10.0.0.18:8080` le a részét a fürt teljes tartománynevére.  

4. Ellenőrizze a kérelmet.  
    Szerkessze az alábbi parancsot úgy `29` , hogy az előző lépésben `id` visszaadott tényleges értéket cseréli le.  A kérelem állapota a következő parancsokkal kérdezhető le:

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

    A válasz `COMPLETED` azt jelzi, hogy a kérés befejeződött.

5. Az előző kérelem befejeződése után a következő paranccsal indíthatja el a Spark2 szolgáltatást.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    A szolgáltatás mostantól az új konfigurációt használja.

6. Végül használja a következőt a karbantartási mód kikapcsolásához.

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

A REST API teljes körű ismertetését lásd: [Apache AMBARI API Reference v1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Lásd még: [felhasználók engedélyezése Apache Ambari-nézetekhez](./hdinsight-authorize-users-to-ambari.md)
