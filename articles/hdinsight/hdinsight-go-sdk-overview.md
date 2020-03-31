---
title: Azure HDInsight SDK for Go
description: Referenciaanyag az Azure HDInsight SDK go- és Apache Hadoop-fürtök höz
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seodec18
ms.date: 01/03/2020
ms.openlocfilehash: 292496c4d458621213fe62105149ac845d78891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479586"
---
# <a name="hdinsight-sdk-for-go-preview"></a>HDInsight SDK go verzióhoz (előzetes verzió)

## <a name="overview"></a>Áttekintés
A HDInsight SDK for Go olyan osztályokat és függvényeket biztosít, amelyek lehetővé teszik a HDInsight-fürtök kezelését. Ez magában foglalja a hdinsight-fürtök létrehozására, törlésére, frissítésére, listára, átméretezésére, parancsfájl-műveletek végrehajtására, figyelésére, HDInsight-fürtök tulajdonságainak leésre és egyebek létrehozására irányuló műveleteket.

> [!NOTE]  
>GoDoc referenciaanyag az SDK is [elérhető itt](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Egy [ `go get` eszköz](https://github.com/golang/go/wiki/GoGetTools).
* [Menj, menj.](https://golang.org/dl/)

## <a name="sdk-installation"></a>SDK telepítése

A GOPATH helyéről futtassa a`go get github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight`

## <a name="authentication"></a>Hitelesítés

Az SDK-t először hitelesíteni kell az Azure-előfizetéssel.  Kövesse az alábbi példát egy egyszerű szolgáltatás létrehozásához és a hitelesítéshez. Miután ez megtörtént, egy példányát `ClustersClient`fogja használni, amely számos függvényt tartalmaz (az alábbi szakaszokban ismertetve), amelyek felügyeleti műveletek végrehajtására használhatók.

> [!NOTE]  
> Vannak más módon is hitelesíteni mellett az alábbi példában, hogy potenciálisan jobban megfelel az Ön igényeinek. Az összes függvény itt ismertetésre kerül: [Hitelesítési függvények az Azure SDK for Go-ban](https://docs.microsoft.com/azure/go/azure-sdk-go-authorization)

### <a name="authentication-example-using-a-service-principal"></a>Hitelesítési példa egyszerű szolgáltatás használatával

Először jelentkezzen be az [Azure Cloud Shellbe.](https://shell.azure.com/bash) Ellenőrizze, hogy jelenleg használja-e azt az előfizetést, amelyben az egyszerű szolgáltatás létrehozását szeretné használni.

```azurecli-interactive
az account show
```

Az előfizetési adatok JSON-ként jelennek meg.

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

Ha nem a megfelelő előfizetésbe jelentkezett be, válassza ki a megfelelőt a következő futtatásával: 
```azurecli-interactive
az account set -s <name or ID of subscription>
```

> [!IMPORTANT]  
> Ha egy másik függvény még nem regisztrálta a HDInsight-erőforrás-szolgáltatót (például egy HDInsight-fürt létrehozásához az Azure Portalon keresztül), ezt csak egyszer kell megtennie, mielőtt hitelesítene. Ez az Azure [Cloud Shell](https://shell.azure.com/bash) ből a következő parancs futtatásával végezhető el:
>```azurecli-interactive
>az provider register --namespace Microsoft.HDInsight
>```

Ezután válassza ki a szolgáltatásnév nevét, és hozza létre a következő paranccsal:

```azurecli-interactive
az ad sp create-for-rbac --name <Service Principal Name> --sdk-auth
```

Az egyszerű szolgáltatás információ jsonként jelenik meg.

```json
{
  "clientId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "clientSecret": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "subscriptionId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "tenantId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Másolja az `TENANT_ID`alábbi kódrészletet, `CLIENT_ID` `CLIENT_SECRET`és `SUBSCRIPTION_ID` töltse ki a , , és a JSON karakterláncait, amelyeket a parancs futtatása után adott vissza a szolgáltatásegyszerű létrehozásához.

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
> Ez a szakasz feltételezi, hogy már `ClusterClient` hitelesített és épített egy `client`példányt, és tárolja a nevű változóban. A hitelesítésre és beszerzésre `ClusterClient` vonatkozó utasítások a fenti Hitelesítés című részben találhatók.

### <a name="create-a-cluster"></a>Fürt létrehozása

Új fürt hozható létre `client.Create()`a hívással. 

#### <a name="example"></a>Példa

Ez a példa bemutatja, hogyan hozhat létre egy [Apache Spark-fürt](https://spark.apache.org/) két fő csomópontés egy feldolgozó csomópont.

> [!NOTE]  
> Először létre kell hoznia egy erőforráscsoportot és a tárfiókot, az alábbiak szerint. Ha már létrehozta ezeket, kihagyhatja ezeket a lépéseket.

##### <a name="creating-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure Cloud Shell](https://shell.azure.com/bash) használatával létrehozhat egy erőforráscsoportot a

```azurecli-interactive
az group create -l <Region Name (i.e. eastus)> --n <Resource Group Name>
```

##### <a name="creating-a-storage-account"></a>Tárfiók létrehozása

Az [Azure Cloud Shell](https://shell.azure.com/bash) használatával a következő futtatásával hozhat létre tárfiókot:

```azurecli-interactive
az storage account create -n <Storage Account Name> -g <Existing Resource Group Name> -l <Region Name (i.e. eastus)> --sku <SKU i.e. Standard_LRS>
```

Most futtassa a következő parancsot a tárfiók kulcsának lekérni (erre szüksége lesz egy fürt létrehozásához):

```azurecli-interactive
az storage account keys list -n <Storage Account Name>
```

---
Az alábbi Go kódrészlet létrehoz egy Spark-fürt két fő csomópont és egy munkavégző csomópont. Töltse ki az üres változókat a megjegyzésekben leírtak szerint, és nyugodtan változtasson más paramétereket, hogy megfeleljen az Ön egyedi igényeinek.

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

### <a name="get-cluster-details"></a>Fürt részleteinek beszereznie

Adott fürt tulajdonságainak beszerezni:

```golang
client.Get(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

A fürt `get` sikeres létrehozásának megerősítéséhez használható.

```golang
cluster, err := client.Get(context.Background(), resourceGroupName, clusterName)
if (err != nil) {
    fmt.Println("Error: ", err)
}
fmt.Println(*cluster.Name)
fmt.Println(*cluster.ID
```

A kimenetnek így kell kinéznie:

```
<Cluster Name>
/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<Resource Group Name>/providers/Microsoft.HDInsight/clusters/<Cluster Name>
```

### <a name="list-clusters"></a>Fürtök listázása

#### <a name="list-clusters-under-the-subscription"></a>Fürtök listázása az előfizetés alatt

```golang
client.List()
```

#### <a name="list-clusters-by-resource-group"></a>Fürtök listázása erőforráscsoport szerint

```golang
client.ListByResourceGroup("<Resource Group Name>")
```

> [!NOTE]  
> `List()` Mindkettő, `ListByResourceGroup()` és `ClusterListResultPage` vissza a struct. A következő oldal lekérni `Next()`kívánta a . Ez a `ClusterListResultPage.NotDone()` visszatérésig `false`megismételhető , amint az az alábbi példában látható.

#### <a name="example"></a>Példa

A következő példa az aktuális előfizetés összes fürtjének tulajdonságait nyomtatja ki:

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

Fürt törlése:

```golang
client.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

### <a name="update-cluster-tags"></a>Fürtcímkék frissítése

Egy adott fürt címkéit a következőkkel frissítheti:

```golang
client.Update(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterPatchParameters{<map[string]*string} of Tags>)
```

#### <a name="example"></a>Példa

```golang
client.Update(context.Background(), "SDKTestRG", "SDKTest", hdi.ClusterPatchParameters{map[string]*string{"tag1Name" : to.StringPtr("tag1Value"), "tag2Name" : to.StringPtr("tag2Value")}})
```

### <a name="resize-cluster"></a>Fürt átméretezése

Egy adott fürt munkavégző csomópontjainak számát átméretezheti egy új méret megadásával, így:

```golang
client.Resize(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ClusterResizeParameters{<Num of Worker Nodes (int)>})
```

## <a name="cluster-monitoring"></a>Fürtmonitorozás

A HDInsight Felügyeleti SDK az Operations Management Suite (OMS) segítségével is használható a fürtök figyelésének kezelésére.

Hasonlóan ahhoz, `ClusterClient` ahogyan a felügyeleti műveletekhez való `ExtensionClient` használatra létrehozott, létre kell hoznia egy figyelési műveletekhez használhatót. Miután elvégezte a fenti Hitelesítés szakaszt, létrehozhat egy `ExtensionClient` hasonlót:

```golang
extClient := hdi.NewExtensionsClient(SUBSCRIPTION_ID)
extClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Az alábbi figyelési példák `ExtensionClient` feltételezik, hogy már inicializált egy hívott, `extClient` és állítsa be a `Authorizer` fent látható módon.

### <a name="enable-oms-monitoring"></a>OMS-figyelés engedélyezése

> [!NOTE]  
> Az OMS-figyelés engedélyezéséhez rendelkeznie kell egy meglévő Log Analytics-munkaterülettel. Ha még nem hozott létre egyet, itt megtudhatja, hogyan teheti meg: [Log Analytics-munkaterület létrehozása az Azure Portalon.](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)

Az OMS-figyelés engedélyezése a fürtön:

```golang
extClient.EnableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name", hdi.ClusterMonitoringRequest {WorkspaceID: to.StringPtr("<Workspace Id>")})
```

### <a name="view-status-of-oms-monitoring"></a>Az OMS-figyelés állapotának megtekintése

Az OMS állapotának leminősítése a fürtön:

```golang
extClient.GetMonitoringStatus(context.Background(), "<Resource Group Name", "Cluster Name")
```

### <a name="disable-oms-monitoring"></a>OMS-figyelés letiltása

Az OMS letiltása a fürtön:

```golang
extClient.DisableMonitoring(context.Background(), "<Resource Group Name", "Cluster Name")
```

## <a name="script-actions"></a>Parancsfájl-műveletek

A HDInsight parancsfájlműveleteknek nevezett konfigurációs függvényt biztosít, amely egyéni parancsfájlokat hív meg a fürt testreszabásához.

> [!NOTE]  
> A parancsfájlműveletek használatáról további információ itt található: [Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)

### <a name="execute-script-actions"></a>Parancsfájl-műveletek végrehajtása

Parancsfájlműveleteket hajthat végre egy adott fürtön, így:

```golang
var scriptAction1 = hdi.RuntimeScriptAction{Name: to.StringPtr("<Script Name>"), URI: to.StringPtr("<URL To Script>"), Roles: <&[]string of roles>} //valid roles are "headnode", "workernode", "zookeepernode", and "edgenode"
client.ExecuteScriptActions(context.Background(), "<Resource Group Name>", "<Cluster Name>", hdi.ExecuteScriptActionParameters{PersistOnSuccess: to.BoolPtr(true), ScriptActions: &[]hdi.RuntimeScriptAction{scriptAction1}}) //add more RuntimeScriptActions to the list to execute multiple scripts
```

A "Parancsfájl-művelet törlése" és a "Persisted Script Actions listázása" műveletekhez létre kell hoznia egy `ScriptActionsClient`, hasonlóan ahhoz, ahogyan a felügyeleti műveletekhez való használatra létrehozott. `ClusterClient` Miután elvégezte a fenti Hitelesítés szakaszt, létrehozhat egy `ScriptActionsClient` hasonlót, így:

```golang
scriptActionsClient := hdi.NewScriptActionsClient(SUBSCRIPTION_ID)
scriptActionsClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Az alábbi parancsfájlműveletek példái feltételezik, `scriptActionsClient` hogy `Authorizer` már inicializált egy `ScriptActionsClient` hívott, és állítsa be a fent látható módon.

### <a name="delete-script-action"></a>Parancsfájl-művelet törlése

Adott, megőrzött parancsfájlművelet törlése egy adott fürtön:

```golang
scriptActionsClient.Delete(context.Background(), "<Resource Group Name>", "<Cluster Name>", "<Script Name>")
```

### <a name="list-persisted-script-actions"></a>A megőrzött parancsfájlműveletek listája

> [!NOTE]  
> Mindkettő `ListByCluster()` egy `ScriptActionsListPage` struct-ot ad vissza. A következő oldal lekérni `Next()`kívánta a . Ez a `ClusterListResultPage.NotDone()` visszatérésig `false`megismételhető , amint az az alábbi példában látható.

A megadott fürt összes megőrzött parancsfájlműveletének listázása:
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

### <a name="list-all-scripts-execution-history"></a>Az összes parancsfájl végrehajtási előzményének listázása

Ehhez a művelethez létre `ScriptExecutionHistoryClient`kell hoznia egy `ClusterClient` , hasonlóan ahhoz, ahogyan a felügyeleti műveletekhez való használatra létrehozott. Miután elvégezte a fenti Hitelesítés szakaszt, létrehozhat egy `ScriptActionsClient` hasonlót, így:

```golang
scriptExecutionHistoryClient := hdi.NewScriptExecutionHistoryClient(SUBSCRIPTION_ID)
scriptExecutionHistoryClient.Authorizer, _ = credentials.Authorizer()
```

> [!NOTE]  
> Az alábbiakban azt feltételezi, `ScriptExecutionHistoryClient` hogy `scriptExecutionHistoryClient` már `Authorizer` inicializáltegy hívott, és állítsa be a fent látható módon.

A megadott fürt összes parancsfájljának végrehajtási előzményeinek listázása:

```golang
scriptExecutionHistoryClient.ListByCluster(context.Background(), "<Resource Group Name>", "<Cluster Name>")
```

#### <a name="example"></a>Példa

Ez a példa az összes korábbi parancsfájl-végrehajtás összes részletét kinyomtatja.

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

Fedezze fel a [GoDoc referenciaanyagot.](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) A GoDocs referenciadokumentációt nyújt az SDK összes függvényének.
