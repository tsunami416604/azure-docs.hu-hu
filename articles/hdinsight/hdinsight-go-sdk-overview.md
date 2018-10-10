---
title: Az Azure HDInsight Go SDK
description: Az Azure HDInsight Go SDK-referencia
services: hdinsight
author: tylerfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 9/21/2018
ms.author: tyfox
ms.openlocfilehash: 8beb75748c2e9fe3f71ad321c4cd523e344fb90c
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901906"
---
# <a name="hdinsight-go-management-sdk-preview"></a>HDInsight Go SDK előzetes verziójának

## <a name="overview"></a>Áttekintés
A HDInsight Go SDK nyújt az osztályok, amelyeket a HDInsight-fürtök kezelését teszi lehetővé. Ez magában foglalja a műveletek létrehozása, törlése, frissítése, listázása, átméretezése, parancsfájl műveletek végrehajtása, figyelése, HDInsight-fürtök és egyéb tulajdonságainak beolvasása.

> [!NOTE]
>GoDoc referenciaanyag az olyan Ez az SDK egyben [elérhető itt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-fiók. Ha nem rendelkezik ilyennel, [ingyenes próbaverzió beszerzése](https://azure.microsoft.com/free/).
* [Go](https://golang.org/dl/)

## <a name="sdk-installation"></a>Az SDK telepítése

Futtassa a GOPATH helyről `go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Hitelesítés

Az SDK-t először van szükség az Azure-előfizetésében.  Hajtsa végre az egyszerű szolgáltatás létrehozása és hitelesítését az alábbi példában. Miután ez megtörtént, hogy egy példányát egy `ClustersClient`, tartalmazó számos függvénye nem (ismertetett alábbi szakaszokat) használható kezelési műveletek végrehajtásához.

> [!NOTE]
> Egyéb módon hitelesítéséhez mellett az alábbi példában, amely sikerült potenciálisan lehet jobban megfelel az igényeinek. A függvények leírása itt található: [hitelesítési funkció a Góhoz készült Azure SDK-ban](https://docs.microsoft.com/go/azure/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Hitelesítés a példában egy egyszerű szolgáltatás használatával

Először jelentkezzen be [Azure Cloud Shell](https://shell.azure.com/bash). Ellenőrizze, hogy jelenleg használja a létrehozott egyszerű szolgáltatás használni kívánt előfizetést. 

```azurecli-interactive
az account show
```

Az előfizetési adatok JSON-ként jelenik meg.

```json
{
  "environmentName": "AzureCloud",
  "id": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "isDefault": true,
  "name": "XXXXXXX",
  "state": "Enabled",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "user": {
    "cloudShellID": true,
    "name": "XXX@XXX.XXX",
    "type": "user"
  }
}
```

Ha még nem jelentkezett be a megfelelő előfizetés, válassza ki a megfelelő futtatásával: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]
> Ha Ön még nem regisztrálta a HDInsight erőforrás-szolgáltató egy másik függvény által (például hozzon létre egy HDInsight-fürtöt az Azure Portalon keresztül), Ön előtt is hitelesítése után erre van szükség. Az ehhez a [Azure Cloud Shell](https://shell.azure.com/bash) a következő parancs futtatásával:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Ezután válasszon a szolgáltatásnévhez tartozó nevet, és hozza létre a következő paranccsal:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

A szolgáltatásnév adatait JSON-ként jelenik meg.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Másolja az alábbi kódrészletet, és írja be a `TENANT_ID`, `CLIENT_ID`, `CLIENT_SECRET`, és `SUBSCRIPTION_ID` a karakterláncok által eredményül adott az egyszerű szolgáltatás létrehozása a parancs futtatása után a JSON-ból.

```golang
package main

import (
    "context"
    "github.com/Azure/go-autorest/autorest/azure/auth"
    hdi "github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight"
    "github.com/Azure/go-autorest/autorest/to"    
)

func main() {
    var err error

    // Tenant ID for your Azure Subscription
    var TENANT_ID = ""
    // Your Service Principal App Client ID
    var CLIENT_ID = ""
    // Your Service Principal Client Secret
    var CLIENT_SECRET = ""
    // Azure Subscription ID
    var SUBSCRIPTION_ID = ""

    var credentials = auth.NewClientCredentialsConfig(CLIENT_ID, CLIENT_SECRET, TENANT_ID)
    var client = hdi.NewClustersClient(SUBSCRIPTION_ID)

    client.Authorizer, err = credentials.Authorizer()
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
```

## <a name="cluster-management"></a>Fürtkezelés

> [!NOTE]
> Ez a szakasz feltételezi, hogy már hitelesített és létrehozni egy `ClusterClient` példány és a egy nevű változóban tárolja el `client`. Utasítások a hitelesítés és beszerzése egy `ClusterClient` hitelesítési című fenti szakaszban található.

### <a name="create-a-cluster"></a>Fürt létrehozása

Új fürt is létrehozható meghívásával `client.Create()`. 

#### <a name="example"></a>Példa

Ez a példa bemutatja, hogyan hozhat létre egy Spark-fürt 2 átjárócsomópontokhoz és az 1 munkavégző csomópont.

> [!NOTE]
> Először hozzon létre egy erőforráscsoportot és Storage-fiókot, kövesse az alábbi utasításokat. Ha már létrehozott ezeket, kihagyhatja ezeket a lépéseket.

##### <a name="creating-a-resource-group"></a>Egy erőforráscsoport létrehozása

Egy erőforrás csoport használatával is létrehozhat a [Azure Cloud Shell](https://shell.azure.com/bash) futtatásával
```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```
##### <a name="creating-a-storage-account"></a>Tárfiók létrehozása

A tárolási fiók használatával is létrehozhat a [Azure Cloud Shell](https://shell.azure.com/bash) futtatásával:
```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```
Most futtassa az alábbi parancsot a kulcs a tárfiók (szüksége lesz egy fürt létrehozása) lekérése:
```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```
---
A Go alábbi kódrészlet egy Spark-fürtöt hoz létre 2 fő csomópont és 1 munkavégző csomópont. A megjegyzések leírtak szerint adja meg az üres változók, és nyugodtan más paramétereket saját igényeinek megfelelően módosítsa.

```golang
// The name for the cluster you are creating
var clusterName = "";
// The name of your existing Resource Group
var resourceGroupName = "";
// Choose a username
var username = "";
// Choose a password
var password = "";
// Replace <> with the name of your storage account
var storageAccount = "<>.blob.core.windows.net";
// Storage account key you obtained above
var storageAccountKey = "";
// Choose a region
var location = "";
var container = "default";

var parameters = hdi.ClusterCreateParametersExtended {
    Location: to.StringPtr(location),
    Tags: make(map[string]*string),
    Properties: &hdi.ClusterCreateProperties {
        ClusterVersion: to.StringPtr("3.6"),
        OsType: hdi.Linux,
        ClusterDefinition: &hdi.ClusterDefinition {
            Kind: to.StringPtr("spark"),
            Configurations: map[string]map[string]interface{}{
                "gateway": {
                    "restAuthCredential.isEnabled": "True",
                    "restAuthCredential.username":  username,
                    "restAuthCredential.password":  password,
                },
            },
        },
        Tier: hdi.Standard,
        ComputeProfile: &hdi.ComputeProfile {
            Roles: &[]hdi.Role {
                hdi.Role {
                    Name: to.StringPtr("headnode"),
                    TargetInstanceCount: to.Int32Ptr(2),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
                hdi.Role {
                    Name: to.StringPtr("workernode"),
                    TargetInstanceCount: to.Int32Ptr(1),
                    HardwareProfile: &hdi.HardwareProfile {
                        VMSize: to.StringPtr("Large"),
                    },
                    OsProfile: &hdi.OsProfile {
                        LinuxOperatingSystemProfile: &hdi.LinuxOperatingSystemProfile {
                            Username: to.StringPtr(username),
                            Password: to.StringPtr(password),
                        },
                    },
                },
            },
        },
        StorageProfile: &hdi.StorageProfile {
            Storageaccounts: &[]hdi.StorageAccount {
                hdi.StorageAccount {
                    Name: to.StringPtr(storageAccount),
                    Key: to.StringPtr(storageAccountKey),
                    Container: to.StringPtr(container),
                    IsDefault: to.BoolPtr(true),
                },
            },
        },
    },
}
client.Create(context.Background(), resourceGroupName, clusterName, parameters)
```

### <a name="get-cluster-details"></a>Fürt részleteinek beolvasása

Egy adott fürt tulajdonságainak lekéréséhez:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

Használhat `get` annak ellenőrzéséhez, hogy sikeresen létrehozta a fürtöt.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

A kimenet hasonlóan kell kinéznie:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Fürtök listázása

#### <a name="list-clusters-under-the-subscription"></a>Az előfizetés alá tartozó fürtök listázása
```golang
client.List()
```
#### <a name="list-clusters-by-resource-group"></a>Által erőforráscsoportba tartozó fürtök listázása
```golang
client.ListByResourceGroup("<Resource Group Name>")
```
> [!NOTE]
> Mindkét `List()` és `ListByResourceGroup()` adja vissza egy `ClusterListResultPage` struct. A következő lap beolvasásához hívása `Next()`. Ez is megismételhető, amíg `ClusterListResultPage.NotDone()` adja vissza `false`, az alábbi példában látható módon.

#### <a name="example"></a>Példa
Az alábbi példa megjeleníti az aktuális előfizetéshez tartozó összes fürt tulajdonságait:

```golang
page, err := client.List(context.Background())
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, cluster := range page.Values() {
        fmt.Println(*cluster.Name)
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }
}
```

### <a name="delete-a-cluster"></a>Fürt törlése

A fürt törléséhez:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Frissítse a fürt címkék

Frissítheti a címkéket egy adott fürt, például így:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```
#### <a name="example"></a>Példa

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Fürt átméretezése

Adjon meg egy új méret átméretezheti a munkavégző csomópontok számát egy adott fürtben, például így:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Fürtmonitorozás

A HDInsight Management SDK-val is kezelheti a figyelés az Operations Management Suite (OMS) keresztül a fürtökön használható.

Hasonló módon, hogy hogyan létrehozott `ClusterClient` felügyeleti műveletekhez használandó szeretne létrehozni egy `ExtensionClient` figyelési műveletekhez használandó. Miután végzett a fenti hitelesítés szakaszban, létrehozhat egy `ExtensionClient` lépések szerint:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Az alábbi figyelési példák azt feltételezik, már van inicializálva egy `ExtensionClient` nevű `extClient` , és állítsa annak `Authorizer` jelennek meg.

### <a name="enable-oms-monitoring"></a>Engedélyezze az OMS-monitorozással

> [!NOTE]
> Engedélyezi az OMS-Monitorozással, rendelkeznie kell egy meglévő Log Analytics-munkaterületet. Ha Ön még nem hozott létre egyet, hogyan valósítható meg Itt tudhat: [Log Analytics-munkaterület létrehozása az Azure Portalon](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

OMS-Monitorozással engedélyezése a fürtön:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>OMS-monitorozással állapotának megtekintése

Az OMS a fürt állapotának lekéréséhez:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>Tiltsa le az OMS-monitorozással

OMS letiltása a fürtön:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Szkriptműveletek

HDInsight biztosít egy szkriptműveletek nevű konfigurációs függvény, amely meghívja az egyéni szkriptek a fürt testreszabása.
> [!NOTE]
> Szkriptműveletek használatáról további információt itt találhat: [testreszabása Linux-alapú HDInsight-fürtök parancsfájlműveletekkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Parancsfájl-műveletek végrehajtása

Egy adott fürtön parancsfájl műveleteket hajthat végre, például így:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

A "Törlése Script Action" és "Lista megőrzött Parancsfájlműveletek" műveletek szeretne létrehozni egy `ScriptActionsClient`, hasonló módon, hogy hogyan létrehozott `ClusterClient` felügyeleti műveletekhez használandó. Miután végzett a fenti hitelesítés szakaszban, létrehozhat egy `ScriptActionsClient` lépések szerint:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> A szkriptműveletek alábbi példák feltételezik, már inicializálva van egy `ScriptActionsClient` nevű `scriptActionsClient` , és állítsa annak `Authorizer` jelennek meg.

### <a name="delete-script-action"></a>Parancsprogram-művelet törlése

Egy adott fürtön megadott megőrzött parancsfájlművelet törlése:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>Megőrzött Parancsfájlműveletek listázása

> [!NOTE]
> Mindkét `ListByCluster()` adja vissza egy `ScriptActionsListPage` struct. A következő lap beolvasásához hívása `Next()`. Ez is megismételhető, amíg `ClusterListResultPage.NotDone()` adja vissza `false`, az alábbi példában látható módon.

A megadott fürtben az összes megőrzött Parancsfájlműveletek megjelenítése:
```golang
scriptActionsClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

```golang
page, err := scriptActionsClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }    
}
```

### <a name="list-all-scripts-execution-history"></a>Az összes parancsfájl végrehajtási előzmények listázása

Ehhez a művelethez szeretne létrehozni egy `ScriptExecutionHistoryClient`, hasonló módon, hogy hogyan létrehozott `ClusterClient` felügyeleti műveletekhez használandó. Miután végzett a fenti hitelesítés szakaszban, létrehozhat egy `ScriptActionsClient` lépések szerint:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]
> Az alábbi feltételezi, hogy már van inicializálva egy `ScriptExecutionHistoryClient` nevű `scriptExecutionHistoryClient` , és állítsa annak `Authorizer` jelennek meg.

A megadott fürtben az összes parancsfájl végrehajtási előzményeinek megjelenítése:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

Ebben a példában a részletekről jelenít meg az összes korábbi parancsprogram-végrehajtások.

```golang
page, err := scriptExecutionHistoryClient.ListByCluster(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
for (page.NotDone()) {
    for _, script := range page.Values() {          
        fmt.Println(*script.Name) //There are functions to get other properties of RuntimeScriptActionDetail besides Name, such as Status, Operation, StartTime, EndTime, etc. See reference documentation.
    }
    err = page.Next();
    if (err != nil) {
        fmt.Println("Error: ", err)
    }       
}
```

## <a name="next-steps"></a>További lépések

* Fedezze fel a [GoDoc – referenciaanyag](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight). A GoDocs adja meg az SDK-ban a függvények dokumentációjában.
