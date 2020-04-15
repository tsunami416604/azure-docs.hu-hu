---
title: A Hadoop figyelése és kezelése az Ambari REST API-val – Azure HDInsight
description: Ismerje meg, hogyan figyelheti és kezelheti a Hadoop-fürtöket az Azure HDInsightban az Ambari használatával. Ebben a dokumentumban megtudhatja, hogyan használhatja a HDInsight-fürtökben található Ambari REST API-t.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381385"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>HDInsight-fürtök kezelése az Apache Ambari REST API használatával

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Ismerje meg, hogyan használhatja az Apache Ambari REST API-t az Apache Hadoop-fürtök kezelésére és figyelésére az Azure HDInsightban.

## <a name="what-is-apache-ambari"></a>Mi az Apache Ambari

[Az Apache Ambari](https://ambari.apache.org) leegyszerűsíti a Hadoop-fürtök felügyeletét és felügyeletét azáltal, hogy könnyen használható webes felhasználói felületet biztosít rest [API-k](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)segítségével.  Az Ambari alapértelmezés szerint Linux-alapú HDInsight-fürtökkel rendelkezik.

## <a name="prerequisites"></a>Előfeltételek

* Egy Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](hadoop/apache-hadoop-linux-tutorial-get-started.md)

* Bash az Ubuntu-n a Windows 10 rendszeren.  A példák ebben a cikkben használja a Bash shell windows 10.The examples in this article use the Bash shell on Windows 10. A windows [10-es Windows-alrendszer telepítési útmutatójában](https://docs.microsoft.com/windows/wsl/install-win10) a windows 10-es operációs rendszer telepítési útmutatójában talál.  Más [Unix héjak](https://www.gnu.org/software/bash/) is működni fog.  A példák, néhány kisebb módosítással, működhetnek a Windows parancssorban.  Vagy használhatja a Windows PowerShellt.

* jq, egy parancssori JSON processzor.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Windows PowerShell.  Vagy használhatja [Bash](https://www.gnu.org/software/bash/).

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Az Ambari rest API alapegységes erőforrás-azonosítója

 A HDInsight Ambari REST API-jának alap egységes `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`erőforrás-azonosítója (URI) a , hol `CLUSTERNAME` található a fürt neve.  Az URI-kban a fürtnevek **ben a kis- és nagybetűket nem lehet figyelembe.**  Míg a fürt neve a teljesen minősített tartománynév (FQDN) része az URI (`CLUSTERNAME.azurehdinsight.net`) a kis- és nagybetűk megkülönböztetése, más előfordulások az URI-ban a kis- és nagybetűk megkülönböztetése.

## <a name="authentication"></a>Hitelesítés

A HDInsight-on az Ambarihoz való csatlakozáshttps-t igényel. Használja a rendszergazdai fiók nevét (az alapértelmezett **rendszergazda)** és a fürt létrehozása során megadott jelszót.

Vállalati biztonsági csomag fürtök, `admin`ahelyett, hogy a , `username@domain.onmicrosoft.com`használja a teljesen minősített felhasználónevet, mint a .

## <a name="examples"></a>Példák

### <a name="setup-preserve-credentials"></a>Telepítés (hitelesítő adatok megőrzése)

Őrizze meg hitelesítő adatait, hogy ne adja meg újra őket az egyes példákban.  A fürt neve egy külön lépésben marad meg.

**A. Bash**  
Az alábbi parancsfájlt `PASSWORD` a tényleges jelszóval való helyettesítéssel szerkesztheti.  Ezután adja meg a parancsot.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Megfelelően kis- és nagybetűk alapján a fürt nevének azonosítása

A fürtnév tényleges burkolata eltérhet a várttól.  Az itt leírt lépések a tényleges burkolatot jelenítik meg, majd egy változóban tárolják az összes későbbi példához.

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

### <a name="parsing-json-data"></a>JSON-adatok elemzése

A következő példa [jq](https://stedolan.github.io/jq/) vagy [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) segítségével elemzi a JSON-válaszdokumentumot, és csak az `health_report` eredményekből származó adatokat jeleníti meg.

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>A fürtcsomópontok teljes tartománynevének beolvasása

Előfordulhat, hogy ismernie kell a fürtcsomópont teljesen minősített tartománynevét (FQDN). A fürt különböző csomópontjainak fqdn-jét egyszerűen bekeresheti a következő példák használatával:

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

**Dolgozó csomópontok**  

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

**Zookeeper csomópontok**

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

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>A fürtcsomópontok belső IP-címének beszereznie

Az ebben a szakaszban szereplő példák által visszaadott IP-címek nem érhetők el közvetlenül az interneten keresztül. Csak a HDInsight-fürtöt tartalmazó Azure virtuális hálózaton belül érhetők el.

A HDInsight és a virtuális hálózatok együttműködéséről a [HDInsight virtuális hálózatának megtervezése](hdinsight-plan-virtual-network-deployment.md)című témakörben talál további információt.

Az IP-cím megkereséséhez ismernie kell a fürtcsomópontok belső, teljesen minősített tartománynevét (FQDN). Miután rendelkezik a teljes tartománynnn, akkor majd kap az IP-címét a fogadó. A következő példák először az Ambari lekérdezése az összes állomáscsomópont teljes tartományn.The following examples first query Ambari for the FQDN of the all host nodes. Ezután lekérdezi az Ambari-t az egyes állomások IP-címéhez.

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

### <a name="get-the-default-storage"></a>Az alapértelmezett tárhely beszereznie

A HDInsight-fürtöknek azure storage-fiókot vagy Data Lake Storage-t kell alapértelmezett tárolóként használniuk. Az Ambari segítségével a fürt létrehozása után lekérheti ezeket az adatokat. Ha például adatokat szeretne olvasni/írni a HDInsighton kívüli tárolóba.

A következő példák az alapértelmezett tárolási konfiguráció lekérése a fürtből:

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
> Ezek a példák az adatokat`service_config_version=1`tartalmazó kiszolgálóra ( ) alkalmazott első konfigurációt adják vissza. Ha olyan értéket kér vissza, amelyet a fürt létrehozása után módosítottak, előfordulhat, hogy fel kell sorolnia a konfigurációs verziókat, és be kell olvasnia a legújabbat.

A visszatérési érték hasonló az alábbi példák egyikéhez:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`- Ez az érték azt jelzi, hogy a fürt egy Azure Storage-fiókot használ az alapértelmezett tároláshoz. Az `ACCOUNTNAME` érték a tárfiók neve. A `CONTAINER` rész a blob tároló neve a tárfiókban. A tároló a fürt HDFS-kompatibilis tárolójának gyökere.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net`- Ez az érték azt jelzi, hogy a fürt az Azure Data Lake Storage Gen2 alapértelmezett tárolást használ. A `ACCOUNTNAME` `CONTAINER` és az értékek jelentése megegyezik a korábban említett Azure Storage jelentéssel.

* `adl://home`- Ez az érték azt jelzi, hogy a fürt az Azure Data Lake Storage Gen1 alapértelmezett tárolást használja.

    A Data Lake Storage-fiók nevének megkereséséhez használja az alábbi példákat:

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

    A visszatérési érték `ACCOUNTNAME.azuredatalakestore.net`hasonló `ACCOUNTNAME` a , ahol a Data Lake Storage-fiók neve.

    A fürt tárolóját tartalmazó Data Lake Storage könyvtárának megkereséséhez használja az alábbi példákat:

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

    A visszatérési érték `/clusters/CLUSTERNAME/`hasonló a hoz. Ez az érték a Data Lake Storage-fiók egy elérési útja. Ez az elérési út a fürt HDFS-kompatibilis fájlrendszerének gyökere.  

> [!NOTE]  
> Az [Azure PowerShell](/powershell/azure/overview) által biztosított [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) parancsmag a fürt tárolási adatait is visszaadja.

### <a name="get-all-configurations"></a>Az összes konfiguráció beszereznie

A fürthöz rendelkezésre álló konfigurációk beszerzése.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Ez a példa egy JSON-dokumentumot ad vissza, amely a telepített összetevők aktuális konfigurációját tartalmazza. Tekintse meg a *címke* értékét. A következő példa egy részlet a Spark-fürttípusból visszaadott adatokból.

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

### <a name="get-configuration-for-specific-component"></a>Adott összetevő konfigurációjának beszereznie

Az önt érdeklő összetevő konfigurációjának beszerezése. A következő példában `INITIAL` cserélje le az előző kérelemből visszaadott címkeértékre.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Ez a példa egy JSON-dokumentumot `livy2-conf` ad vissza, amely az összetevő aktuális konfigurációját tartalmazza.

### <a name="update-configuration"></a>Konfiguráció frissítése

1. Létrehozás . `newconfig.json`  
   Módosítsa, majd írja be az alábbi parancsokat:

   * Cserélje `livy2-conf` ki az új alkatrészre.
   * Cserélje `INITIAL` le az összes `tag` [konfiguráció beolvasása](#get-all-configurations)ponthoz beolvasott tényleges értékre.

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     A PowerShell-parancsfájl [a jq-t](https://stedolan.github.io/jq/)használja.  Edit `C:\HD\jq\jq-win64` alatt, hogy tükrözze a tényleges elérési útját és verzióját [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     A Jq segítségével a HDInsightból beolvasott adatokat új konfigurációs sablonná alakíthatja. Ezek a példák konkrétan a következő műveleteket követik:

   * Egyedi értéket hoz létre, amely tartalmazza a karakterláncot , `newtag`a "verzió" karakterláncot és a dátumot, amely a alkalmazásban van tárolva.

   * Legfelső szintű dokumentumot hoz létre az új konfigurációhoz.

   * Leveszi a `.items[]` tömb tartalmát, és hozzáadja a **desired_config** elem alá.

   * Törli a `href` `version`, `Config` és az elemeket, mivel ezek az elemek nem szükségesek az új konfiguráció elküldéséhez.

   * Hozzáad `tag` egy elemet, `version#################`amelynek értéke . A numerikus rész az aktuális dátumon alapul. Minden konfigurációnak egyedi címkével kell rendelkeznie.

     Végül az adatok a `newconfig.json` dokumentumba kerülnek. A dokumentumszerkezetnek a következő példához hasonlóan kell megjelennie:

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

2. Szerkesztése `newconfig.json`.  
   Nyissa `newconfig.json` meg a dokumentumot, `properties` és módosítsa/adja hozzá az értékeket az objektumban. A következő példa a `"livy.server.csrf_protection.enabled"` `"true"` értékét a-ra `"false"`módosítja.

        "livy.server.csrf_protection.enabled": "false",

    Mentse a fájlt, miután végzett a módosításokkal.

3. Küldje `newconfig.json`el.  
   A következő parancsokkal küldje el a frissített konfigurációt az Ambari-nak.

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

    Ezek a parancsok az **újconfig.json** fájl tartalmát küldik el a fürtnek új konfigurációként. A kérelem egy JSON-dokumentumot ad vissza. A dokumentum **verziócímke** elemének meg kell egyeznie a beküldött verzióval, és a konfigurációs objektum tartalmazza a kért **konfigurációs** módosításokat.

### <a name="restart-a-service-component"></a>Szolgáltatás-összetevő újraindítása

Ezen a ponton az Ambari webes felhasználói felület azt jelzi, hogy a Spark-szolgáltatást újra kell indítani, mielőtt az új konfiguráció érvénybe léphetne. A szolgáltatás újraindításához kövesse az alábbi lépéseket.

1. A Spark2 szolgáltatás karbantartási módjának engedélyezéséhez az alábbiak használatával engedélyezheti a karbantartási módot:

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

    Ezek a parancsok JSON-dokumentumot küldenek a kiszolgálónak, amely bekapcsolja a karbantartási módot. A következő kéréssel ellenőrizheti, hogy a szolgáltatás karbantartási üzemmódban van-e:

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

    A visszatérési `ON`érték .

3. Ezután az alábbiak kal kapcsolja ki a Spark2 szolgáltatást:

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
    > Az `href` URI által visszaadott érték a fürtcsomópont belső IP-címét használja. Ha a fürtön kívülről `10.0.0.18:8080` szeretné használni, cserélje le a részt a fürt teljes tartományna tartománynevére.  

4. A kérelem ellenőrzése.  
    Az alábbi parancs szerkesztése az előző `id` lépésből visszaadott tényleges értékkel helyettesítve. `29`  A következő parancsok a kérelem állapotát kérik:

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

5. Az előző kérelem befejezése után a következő használatával indítsa el a Spark2 szolgáltatást.

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

    A szolgáltatás most az új konfigurációt használja.

6. Végül az alábbiakkal kapcsolja ki a karbantartási módot.

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

A REST API teljes körű hivatkozását az [Apache Ambari API Reference V1 című témakörben található.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)  Lásd még: [Felhasználók engedélyezése Apache Ambari nézetekhez](./hdinsight-authorize-users-to-ambari.md)
