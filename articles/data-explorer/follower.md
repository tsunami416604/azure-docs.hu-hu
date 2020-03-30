---
title: Adatbázisok csatolása az Azure Data Explorerben a követőadatbázis-szolgáltatással
description: Megtudhatja, hogyan csatolhat adatbázisokat az Azure Data Explorerben a follower adatbázis-funkció használatával.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: f6dbdb54c1c5a5d477c3ccb988963758faab83b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140014"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Adatbázisok csatolása a követői adatbázissal az Azure Data Explorerben

A **követő adatbázis-szolgáltatás** lehetővé teszi, hogy egy másik fürtben található adatbázist csatoljon az Azure Data Explorer-fürthöz. A **követő adatbázis** *írásvédett* módban van csatolva, lehetővé téve az adatok megtekintését és lekérdezések futtatását a **vezető adatbázisba**bevitt adatokon. A követő adatbázis szinkronizálja a vezető adatbázisok változásait. A szinkronizálás miatt az adatok rendelkezésre állása néhány másodperctől néhány percig tart. Az időeltolódás hossza a vezető adatbázis metaadatainak teljes méretétől függ. A vezető és a követő adatbázisok ugyanazt a tárfiókot használják az adatok lekéréséhez. A tárház a vezető adatbázis tulajdonában van. A követő adatbázis a betöltés nélkül tekinti meg az adatokat. Mivel a csatolt adatbázis írásvédett adatbázis, az adatbázis ban lévő adatok, táblák és házirendek nem módosíthatók, kivéve a [gyorsítótárazási házirendeket](#configure-caching-policy), [a rendszerbiztonsági tagokat](#manage-principals)és [az engedélyeket.](#manage-permissions) A csatolt adatbázisok nem törölhetők. Ezeket a vezetőnek vagy követőnek kell leválasztania, és csak ezután törölhetők. 

Az adatbázis csatolása egy másik fürthöz a follower képesség használatával infrastruktúraként szolgál az adatok szervezetek és csoportok közötti megosztásához. A szolgáltatás hasznos a számítási erőforrások elkülönítéséhez az éles környezet nem éles használati esetektől való védelme érdekében. A Follower az Azure Data Explorer-fürt költségének társítására is használható ahhoz a félhez, amely lekérdezéseket futtat az adatokon.

## <a name="which-databases-are-followed"></a>Mely adatbázisokat követi a(z) ?

* A fürt egy adatbázist, több adatbázist vagy egy vezércsoport összes adatbázisát követheti. 
* Egyetlen fürt több vezércsoport-adatbázist is követhet. 
* A fürt követő és vezető adatbázist is tartalmazhat

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
1. [Hozzon létre fürtöt és adatbázist](/azure/data-explorer/create-cluster-database-portal) a vezető és a követő számára.
1. [Adatok betöltése](/azure/data-explorer/ingest-sample-data) a vezető adatbázisba a [betöltési áttekintésben](/azure/data-explorer/ingest-data-overview)tárgyalt különböző módszerek egyikével.

## <a name="attach-a-database"></a>Adatbázis csatolása

Az adatbázis csatolásához különböző módszerek et használhat. Ebben a cikkben azt tárgyalja, hogy egy adatbázis c# vagy egy Azure Resource Manager sablon használatával csatolása. Adatbázis csatolásához engedéllyel kell rendelkeznie a vezető és a követő fürthöz. Az engedélyekről az [Engedélyek kezelése](#manage-permissions)című témakörben talál további információt.

### <a name="attach-a-database-using-c"></a>Adatbázis csatolása C használatával #

#### <a name="needed-nugets"></a>Szükséges NuGets

* Telepítse a [Microsoft.Azure.Management.kusto alkalmazást.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Telepítse a [Microsoft.Rest.ClientRuntime.Azure.Authentication programot a hitelesítéshez.](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)

#### <a name="code-example"></a>Példa kódra

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>Adatbázis csatolása python használatával

#### <a name="needed-modules"></a>Szükséges modulok

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>Példa kódra

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Adatbázis csatolása Azure Resource Manager-sablon használatával

Ebben a szakaszban megtudhatja, hogyan csatolhat egy adatbázist egy meglévő beiktatással egy [Azure Resource Manager-sablon](../azure-resource-manager/management/overview.md)használatával. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>A sablon üzembe helyezése 

Az Azure Resource Manager-sablon t az [Azure Portalon](https://portal.azure.com) vagy a PowerShell használatával telepítheti.

   ![sablon telepítése](media/follower/template-deployment.png)


|**Beállítás**  |**Leírás**  |
|---------|---------|
|Követő fürt neve     |  A követő fürt neve; ahol a sablon ttelepíteni fogja.  |
|Csatolt adatbázis-konfigurációk neve    |    A csatolt adatbázis-konfigurációk objektum neve. A név bármilyen karakterlánc lehet, amely a fürt szintjén egyedi.     |
|Adatbázis neve     |      A követendő adatbázis neve. Ha követni szeretné a vezető összes adatbázisát, használja a '*' (*) című fájlt.   |
|Leader-fürt erőforrásazonosítója    |   A vezető fürt erőforrásazonosítója.      |
|Alapértelmezett rendszerint módosítási típus    |   Az alapértelmezett fő módosítási típus. Lehet `Union`, `Replace` `None`vagy . Az alapértelmezett fő módosítási típusról további információt a [főmódosítási típusú vezérlőparancs ban talál.](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind)      |
|Hely   |   Az összes erőforrás helye. A vezetőnek és a követőnek ugyanazon a helyen kell lennie.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Az adatbázis sikeres csatlakoztatásának ellenőrzése

Az adatbázis sikeres csatlakoztatásának ellenőrzéséhez keresse meg a csatolt adatbázisokat az [Azure Portalon.](https://portal.azure.com) 

1. Keresse meg a követőfürtöt, és válassza **az Adatbázisok** lehetőséget
1. Új írásvédett adatbázisok keresése az adatbázislistában.

    ![Írásvédett követő adatbázis](media/follower/read-only-follower-database.png)

Alternatív megoldás:

1. Keresse meg a vezetőfürtöt, és válassza **az Adatbázisok** lehetőséget
2. Annak ellenőrzése, hogy a megfelelő adatbázisok **másokkal megosztva vannak-e** > **Megosztva Igen**

    ![Csatolt adatbázisok olvasása és írása](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>A követőadatbázis leválasztása C használatával # 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>A csatolt követőadatbázis leválasztása a követőfürtről

A követő fürt az alábbiak szerint képes leválasztani bármely csatolt adatbázist:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>A csatolt követőadatbázis leválasztása a vezetőfürtről

A vezércsoport az alábbiak szerint választhat le bármely csatolt adatbázist:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>A követő adatbázis leválasztása python használatával

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>A csatolt követőadatbázis leválasztása a követőfürtről

A követő fürt az alábbiak szerint képes leválasztani bármely csatolt adatbázist:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>A csatolt követőadatbázis leválasztása a vezetőfürtről

A vezércsoport az alábbiak szerint választhat le bármely csatolt adatbázist:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Egyszerű biztonsági tagok, engedélyek és gyorsítótárazási házirend kezelése

### <a name="manage-principals"></a>Egyszerű biztonsági tagok kezelése

Adatbázis csatolásakor adja meg az **"alapértelmezett rendszertagok módosítási fajtáját"**. Az alapértelmezett érték az [engedélyezett rendszertagok](/azure/kusto/management/access-control/index#authorization) vezető adatbázis-gyűjteményének megtartása

|**Fajta** |**Leírás**  |
|---------|---------|
|**Unió**     |   A csatolt adatbázis-biztonsági tagok mindig tartalmazzák az eredeti adatbázis-biztonsági tagokat, valamint a követő adatbázishoz hozzáadott további új biztonsági tagokat.      |
|**Helyettesít**   |    Az eredeti adatbázisból nincs enek elem. A csatolt adatbázishoz új rendszertagokat kell létrehozni.     |
|**Nincs**   |   A csatolt adatbázis-biztonsági tagok csak az eredeti adatbázis rendszerbiztonsági tagokat tartalmazzák további rendszerbiztonsági tagok nélkül.      |

A vezérlőparancsok használatáról az engedélyezett egyszerű st- és szolgáltatás konfigurálásáról a [Vezérlőpultok fürtjének kezeléséhez](/azure/kusto/management/cluster-follower)szükséges parancsok című témakörben talál további információt.

### <a name="manage-permissions"></a>Engedélyek kezelése

Az írásvédett adatbázis-engedélyek kezelése megegyezik az összes adatbázistípussal. Lásd: [Engedélyek kezelése az Azure Portalon.](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)

### <a name="configure-caching-policy"></a>Gyorsítótárazási házirend konfigurálása

A követő adatbázis-rendszergazda módosíthatja a csatolt adatbázis vagy annak bármely táblájának [gyorsítótárazási házirendjét](/azure/kusto/management/cache-policy) a gazdafürtön. Az alapértelmezett érték az adatbázis- és táblaszintű gyorsítótárazási házirendek vezető adatbázis-gyűjteményének megtartása. Például rendelkezhet egy 30 napos gyorsítótárazási házirenddel a vezető adatbázisban a havi jelentések futtatásához, és egy háromnapos gyorsítótárazási házirendet a követő adatbázisban, hogy csak a legutóbbi adatokat kérdezhesse le hibaelhárításhoz. A vezérlőparancsok használatával a követő adatbázis vagy tábla gyorsítótárazási házirendjének konfigurálásáról a [Következő fürt kezeléséhez szükséges parancsok vezérlése](/azure/kusto/management/cluster-follower)című témakörben talál további információt.

## <a name="limitations"></a>Korlátozások

* A követőnek és a vezető csoportoknak ugyanabban a régióban kell lenniük.
* [A streamelés](/azure/data-explorer/ingest-data-streaming) nem használható olyan adatbázisban, amelyet követ.
* Az [ügyfél által kezelt kulcsokat](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault) használó adattitkosítás nem támogatott mind a vezető, mind a követő fürtökön. 
* Nem törölheti azokat az adatbázisokat, amelyek egy másik fürthöz vannak csatolva a leválasztás előtt.
* Nem törölhet olyan fürtöt, amelynek adatbázisa egy másik fürthöz van csatolva a leválasztás előtt.
* Nem állíthatja le a figyelő vagy vezető adatbázis(oka)t csatolt fürtöt. 

## <a name="next-steps"></a>További lépések

* A követő fürtkonfigurációról a [Figyelőfürt kezeléséhez szükséges parancsok vezérlése](/azure/kusto/management/cluster-follower)című témakörben talál további információt.
