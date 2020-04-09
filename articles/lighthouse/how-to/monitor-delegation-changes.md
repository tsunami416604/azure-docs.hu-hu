---
title: Delegálási változások figyelése a kezelő bérlőben
description: Ismerje meg, hogyan figyelheti a delegálási tevékenységet az ügyfél-bérlőktől a kezelő bérlőig.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 322580cc3d0246f7a34e28cdae94da57fda070b5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985150"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Delegálási változások figyelése a kezelő bérlőben

Szolgáltatóként érdemes lehet tudni, ha az ügyfél-előfizetések vagy erőforráscsoportok delegált a bérlő az [Azure delegált erőforrás-kezelés](../concepts/azure-delegated-resource-management.md), vagy ha a korábban delegált erőforrások eltávolítása.

A kezelő bérlőben az [Azure-tevékenységnapló](../../azure-monitor/platform/platform-logs-overview.md) a bérlői szinten követi nyomon a delegálási tevékenységet. Ez a naplózott tevékenység tartalmazza az összes ügyfél-bérlőhozzáadott vagy eltávolított delegálásait.

Ez a témakör ismerteti a bérlői (az összes ügyfél) delegálási tevékenység figyeléséhez szükséges engedélyeket, valamint az ehhez ajánlott eljárásokat. Egy mintaparancsfájlt is tartalmaz, amely az adatok lekérdezésének és jelentésének egyik módját jeleníti meg.

> [!IMPORTANT]
> Ezeket a lépéseket kell végrehajtani a kezelő bérlő, nem pedig bármely ügyfél-bérlők.

## <a name="enable-access-to-tenant-level-data"></a>Bérlőszintű adatokhoz való hozzáférés engedélyezése

A bérlői szintű tevékenységnapló adatainak eléréséhez egy fiókhoz hozzá kell rendelni a [Figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) beépített szerepkörét a gyökérhatókörhöz (/). Ezt a hozzárendelést olyan felhasználónak kell elvégeznie, aki további emelt szintű hozzáféréssel rendelkező globális rendszergazdai szerepkörrel rendelkezik.

### <a name="elevate-access-for-a-global-administrator-account"></a>Globális rendszergazdai fiók elérésének magasabb szintű ítése

Ha szerepkört szeretne hozzárendelni a gyökérhatókörhöz (/), emelt szintű hozzáféréssel kell rendelkeznie a globális rendszergazdai szerepkörrel. Ezt az emelt szintű hozzáférést csak akkor kell hozzáadni, ha el kell végeznie a szerepkör-hozzárendelést, majd el kell távolítania, amikor végzett.

A szintszintemelés hozzáadásáról és eltávolításáról az Access elemelése az [összes Azure-előfizetés és felügyeleti csoport kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md)című témakörben talál részletes útmutatást.

Miután magasabb szintre emeli a hozzáférést, a fiók a felhasználói hozzáférés-rendszergazdaszerepkört kapja az Azure-ban a gyökérhatókörben. Ez a szerepkör-hozzárendelés lehetővé teszi az összes erőforrás megtekintését és a hozzáférést a címtár bármely előfizetési vagy felügyeleti csoportjában, valamint szerepkör-hozzárendeléseket a gyökérhatókörben. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Új egyszerű szolgáltatásfiók létrehozása a bérlői szintű adatok eléréséhez

Miután emelt a hozzáférés, hozzárendelheti a megfelelő engedélyeket egy fiókhoz, hogy lekérdezheti a bérlői szintű tevékenységnapló adatait. Ennek a fióknak rendelkeznie kell a [figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) beépített szerepkörrel a kezelő bérlő gyökérhatóköréhez rendelve.

> [!IMPORTANT]
> Szerepkör-hozzárendelés megadása a legfelső hatókörben azt jelenti, hogy ugyanazok az engedélyek vonatkoznak a bérlő minden erőforrására.

Mivel ez a hozzáférés széles szintje, azt javasoljuk, hogy ezt a szerepkört egy egyszerű szolgáltatásfiókhoz rendelje hozzá, ne pedig egy adott felhasználóhoz vagy egy csoporthoz. Ezenkívül a következő ajánlott eljárásokat javasoljuk:

- [Hozzon létre egy új egyszerű szolgáltatásfiókot,](../../active-directory/develop/howto-create-service-principal-portal.md) amely csak ehhez a függvényhez használható, ahelyett, hogy ezt a szerepkört egy más automatizáláshoz használt meglévő egyszerű szolgáltatáshoz rendelné.
- Győződjön meg arról, hogy ez a szolgáltatásnév nem fér hozzá delegált ügyfél-erőforrásokhoz.
- [Tanúsítvány használatával hitelesítheti](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) és [biztonságosan tárolhatja az Azure Key Vaultban.](../../key-vault/key-vault-best-practices.md)
- Korlátozza azon felhasználókat, akik hozzáférhetnek a szolgáltatásnév nevében való cselekvéshez.

A gyökérhatókör-hozzárendelések az alábbi módszerek egyikével.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Emelt szintű hozzáférés eltávolítása a globális rendszergazdai fiókhoz

Miután létrehozta az egyszerű szolgáltatásfiókot, és hozzárendelte a figyelési olvasó szerepkört a gyökérhatókörhöz, győződjön meg arról, hogy távolítsa el a globális rendszergazdai fiók [emelt szintű hozzáférést,](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) mivel erre a hozzáférési szintre már nincs szükség.

## <a name="query-the-activity-log"></a>A tevékenységnapló lekérdezése

Miután létrehozott egy új egyszerű szolgáltatásfiókot a Monitoring Reader hozzáférés a kezelő bérlő gyökérhatóköréhez, használhatja a lekérdezésés és a jelentés a delegálási tevékenység a bérlőben. 

[Ez az Azure PowerShell-parancsfájl](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) segítségével lekérdezheti az elmúlt 1 nap tevékenység és jelentések bármely hozzáadott vagy eltávolított delegálások (vagy kísérletek, amelyek nem voltak sikeresek). Lekérdezi a [bérlői tevékenységnapló](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) adatait, majd a következő értékeket készíti a hozzáadott vagy eltávolított delegálások jelentéséhez:

- **DedededResourceId**: A delegált előfizetés vagy erőforráscsoport azonosítója
- **CustomerTenantId**: Az ügyfél bérlői azonosítója
- **CustomerSubscriptionId**: Dedededereált vagy delegált erőforráscsoportot tartalmazó előfizetés-azonosító
- **CustomerDelegationStatus**: A delegált erőforrás állapotváltozása (sikeres vagy sikertelen)
- **EventTimeStamp**: Az a dátum és időpont, amikor a delegálási módosítás naplózva lett

Az adatok lekérdezésekekénél tartsa szem előtt a következőket:

- Ha egyetlen központi telepítésben több erőforráscsoportot delegál, minden erőforráscsoporthoz külön bejegyzéseket ad vissza a rendszer.
- A korábbi delegáláson végrehajtott módosítások (például az engedélystruktúra frissítése) hozzáadott delegálásként kerülnek naplózásra.
- Mint fentebb már említettük, egy fióknak rendelkeznie kell a figyelési olvasó beépített szerepkörroot hatókör (/) a bérlői szintű adatok eléréséhez.
- Ezeket az adatokat saját munkafolyamataiban és jelentéseiben is használhatja. A [HTTP-adatgyűjtő API-t (nyilvános előzetes verzió)](../../azure-monitor/platform/data-collector-api.md) például használhatja az Azure Monitorba egy REST API-ügyfélről történő naplózáshoz, majd [műveletcsoportok](../../azure-monitor/platform/action-groups.md) használatával értesítéseket vagy riasztásokat hozhat létre.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan lehet az ügyfeleket az [Azure delegált erőforrás-kezelésére.](../concepts/azure-delegated-resource-management.md)
- További információ az [Azure Monitorról](../../azure-monitor/index.yml) és az [Azure-tevékenységnaplóról.](../../azure-monitor/platform/platform-logs-overview.md)
