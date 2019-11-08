---
title: Adatbázisok csatolása az Azure-ban a követő adatbázis funkciójának használatával Adatkezelő
description: Ismerje meg, hogyan csatolhat adatbázisokat az Azure Adatkezelő a követő adatbázis funkció használatával.
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: a46cf78d902ec8391d7dc3667a6d66daa78927ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828564"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>Adatbázisok csatolása az Azure-ban a követő adatbázis használatával Adatkezelő

A **követő adatbázis** funkció lehetővé teszi, hogy egy másik fürtben található adatbázist csatoljon az Azure adatkezelő-fürthöz. A **követő adatbázis** *írásvédett* módban van csatolva, így megtekintheti az adatot, és lekérdezéseket futtathat a **Leader-adatbázisba**betöltött adatmennyiségen. A követő adatbázis szinkronizálja a változásokat a Leader-adatbázisokban. A szinkronizálás miatt néhány másodpercen belül néhány percen belül megtörténik az adatelérés. Az időeltolódás hossza a vezető adatbázis metaadatainak teljes méretétől függ. A vezető és követő adatbázisok ugyanazt a Storage-fiókot használják az adatlekérdezéshez. A tároló tulajdonosa a Leader-adatbázis. A követő adatbázis megtekinti az adatot anélkül, hogy be kellene tartania azt. Mivel a csatolt adatbázis írásvédett adatbázis, az adatbázisban található, az adatbázishoz tartozó, a táblák és a házirendek nem módosíthatók, kivéve a [gyorsítótárazási házirendet](#configure-caching-policy), a [rendszerbiztonsági tag](#manage-principals)és az [engedélyeket](#manage-permissions). A csatolt adatbázisok nem törölhetők. Ezeket le kell választani a Leader vagy a követő számára, és csak akkor törölhetik őket. 

Ha az adatbázist egy másik fürthöz csatolja, a követő képesség használatával a szervezetek és a csapatok közötti adatmegosztási infrastruktúra szolgál. Ez a funkció hasznos lehet a számítási erőforrások elkülönítéséhez, hogy az éles környezetet a nem éles környezetből származó használati esetekből védjék. A követő az Azure Adatkezelő-fürt költségeit is hozzárendelheti az adatlekérdezéseket futtató fél számára.

## <a name="which-databases-are-followed"></a>Mely adatbázisok vannak követve?

* A fürtök egyetlen adatbázist, több adatbázist vagy egy vezető fürt összes adatbázisát követhetik nyomon. 
* Egyetlen fürt több Leader-fürt adatbázisait is követheti. 
* A fürtök A követő adatbázisokat és a Leader-adatbázisokat is tartalmazhatják

## <a name="prerequisites"></a>Előfeltételek

1. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.
1. [Hozzon létre egy fürtöt és](/azure/data-explorer/create-cluster-database-portal) egy adatbázist a Leader és a követő számára.
1. A betöltési [áttekintésben](/azure/data-explorer/ingest-data-overview)tárgyalt különböző módszerek egyikével betöltheti az [információkat](/azure/data-explorer/ingest-sample-data) a Leader-adatbázisba.

## <a name="attach-a-database"></a>Adatbázis csatolása

Több módszer is használható az adatbázisok csatolására. Ebből a cikkből megbeszéljük, hogyan csatolhat egy C# adatbázist a vagy egy Azure Resource Manager sablon használatával. Adatbázis csatolásához a Leader-fürtre és a követő fürtre vonatkozó engedélyekkel kell rendelkeznie. Az engedélyekkel kapcsolatos további információkért lásd: [engedélyek kezelése](#manage-permissions).

### <a name="attach-a-database-using-c"></a>Adatbázis csatolása a használatávalC#

**Szükséges Nuget**

* Telepítse a [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Telepítse [a Microsoft. Rest. ClientRuntime. Azure. Authentication hitelesítést a hitelesítéshez](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication).


```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "adc";
var databaseName = "db" // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>Adatbázis csatolása Azure Resource Manager sablon használatával

Ebből a szakaszból megtudhatja, hogyan csatolhat egy adatbázist egy [Azure Resource Manager sablon](../azure-resource-manager/resource-group-overview.md)használatával. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the follower cluster."
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
                "description": "Name of the leader cluster to create."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "",
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
            "name": "[parameters('followerClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]"
        },
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Kusto/clusters', parameters('followerClusterName'))]"
            ],
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

A Azure Resource Manager sablont [a Azure Portal vagy a](https://portal.azure.com) PowerShell használatával is telepítheti.

   ![sablon központi telepítése](media/follower/template-deployment.png)


|**Beállítás**  |**Leírás**  |
|---------|---------|
|Követő fürt neve     |  A követő fürt neve       |
|Csatolt adatbázis-konfigurációk neve    |    A csatolt adatbázis-konfigurációk objektum neve. A névnek egyedinek kell lennie a fürt szintjén.     |
|Adatbázis neve     |      A követendő adatbázis neve. Ha követni szeretné az összes vezető adatbázisát, használja a "*" lehetőséget.   |
|Leader-fürterőforrás azonosítója    |   A Leader-fürt erőforrás-azonosítója.      |
|A rendszerbiztonsági tag alapértelmezett módosításának típusa    |   Az alapértelmezett egyszerű módosítási típus. Lehet `Union`, `Replace` vagy `None`. Az alapértelmezett egyszerű módosítási típussal kapcsolatos további információkért lásd: az [egyszerű módosítás típusa vezérlő parancs](/azure/kusto/management/cluster-follower?branch=master#alter-follower-database-principals-modification-kind).      |
|Hely   |   Az összes erőforrás helye. A vezetőnek és a követőnek ugyanazon a helyen kell lennie.       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>Az adatbázis sikeres csatolásának ellenőrzése

Annak ellenőrzéséhez, hogy az adatbázis sikeresen csatolva lett-e, keresse meg a csatolt adatbázisokat a [Azure Portal](https://portal.azure.com). 

1. Navigáljon a követő fürthöz, és válassza az **adatbázisok** lehetőséget.
1. Keresse meg az új írásvédett adatbázisokat az adatbázisok listájában.

    ![Csak olvasási jogosultsággal rendelkező követő adatbázis](media/follower/read-only-follower-database.png)

Vagylagosan

1. Navigáljon a Leader-fürthöz, és válassza az **adatbázisok** lehetőséget.
2. Győződjön meg arról, hogy a megfelelő adatbázisok meg vannak **OSZTVA másokkal** > **Igen**

    ![Csatolt adatbázisok olvasása és írása](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>A követő adatbázis leválasztása a következő használatávalC# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>A csatolt követő adatbázis leválasztása a követő fürtből

A követő fürtök a következő módon választhatják le bármelyik csatolt adatbázist:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "adc";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>A csatolt követő adatbázis leválasztása a Leader-fürtből

A Leader-fürt a következő módon tudja leválasztani bármelyik csatolt adatbázist:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "adc",
        ClusterResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="manage-principals-permissions-and-caching-policy"></a>Rendszerbiztonsági tag, engedélyek és gyorsítótárazási szabályzatok kezelése

### <a name="manage-principals"></a>Rendszerbiztonsági tag kezelése

Adatbázis csatolásakor az **alapértelmezett rendszerbiztonsági tag módosítási típust**kell megadnia. " Az alapértelmezett érték a vezető adatbázis-gyűjtemény, amely a [felhatalmazott rendszerbiztonsági tag](/azure/kusto/management/access-control/index.md#authorization)

|**Típusú** |**Leírás**  |
|---------|---------|
|**Union**     |   A csatolt adatbázis-rendszerbiztonsági tag mindig tartalmazza az eredeti adatbázis-rendszerbiztonsági tag, valamint a követő adatbázishoz hozzáadott további új rendszerbiztonsági tagokat is.      |
|**Csere**   |    Nem található a rendszerbiztonsági tag öröklése az eredeti adatbázisból. Új rendszerbiztonsági tag létrehozása szükséges a csatolt adatbázishoz. Legalább egy rendszerbiztonsági tag hozzáadása szükséges a rendszerbiztonsági tag öröklődésének blokkolásához.     |
|**NEz egy**   |   A csatolt adatbázis-rendszerbiztonsági tag csak az eredeti adatbázis rendszerbiztonsági tagjait tartalmazza, további rendszerbiztonsági tag nélkül.      |

További információ a jogosultságokkal rendelkező rendszerbiztonsági tag konfigurálásához használható vezérlési parancsokról: a [követő parancsok kezelése a követő fürtök](/azure/kusto/management/cluster-follower.md)felügyeletéhez.

### <a name="manage-permissions"></a>Engedélyek kezelése

A csak olvasási jogosultsággal rendelkező adatbázis-engedélyek kezelése ugyanúgy történik, mint minden adatbázis esetében. Lásd: [az engedélyek kezelése a Azure Portalban](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal).

### <a name="configure-caching-policy"></a>Gyorsítótárazási házirend konfigurálása

A követő adatbázis rendszergazdája módosíthatja a csatolt adatbázis vagy a hozzá tartozó táblák [gyorsítótárazási házirendjét](/azure/kusto/management/cache-policy) az üzemeltetési fürtön. Az alapértelmezett érték az adatbázis és a tábla szintű gyorsítótárazási házirendek vezető adatbázis-gyűjteményének megőrzése. Például egy 30 napos gyorsítótárazási házirendet használhat a Leader-adatbázisban a havi jelentéskészítés futtatásához és egy három napos gyorsítótárazási házirendet a követő adatbázison, hogy csak a legutóbbi adatokat kérdezze le a hibaelhárításhoz. A következő témakörben talál további információt arról, hogyan használhatók a vezérlési parancsok a követő adatbázison vagy táblázatban a gyorsítótárazási házirend konfigurálásához: [vezérlési parancsok egy követő fürt kezeléséhez](/azure/kusto/management/cluster-follower.md).

## <a name="limitations"></a>Korlátozások

* A követő és a Leader-fürtöknek ugyanabban a régióban kell lenniük.
* Az [adatfolyam](/azure/data-explorer/ingest-data-streaming) -betöltés nem használható olyan adatbázison, amelyet követnek.
* Nem törölhet olyan adatbázist, amely egy másik fürthöz van csatolva a leválasztása előtt.
* Nem törölhet olyan fürtöt, amely egy másik fürthöz csatolt adatbázissal rendelkezik a leválasztása előtt.
* Nem állíthat le olyan fürtöt, amely csatolt követő vagy vezető adatbázis (oka) t tartalmaz. 

## <a name="next-steps"></a>További lépések

* További információ a követő fürt konfigurációjával kapcsolatban: [vezérlési parancsok a követő fürtök kezeléséhez](/azure/kusto/management/cluster-follower.md).