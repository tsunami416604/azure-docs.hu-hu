---
title: Azure házirendben megfelelőségi adatainak beolvasása
description: Az Azure házirend értékelések és hatások határozza meg a megfelelőség. Útmutató a megfelelőségi részletek.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d36ecb18811901fb781e151c06badc0697c2d769
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655097"
---
# <a name="getting-compliance-data"></a>Megfelelőségi adatok

Az egyik legnagyobb előny Azure házirend a betekintést és erőforrások egy előfizetésben keresztül biztosít vezérlők vagy [felügyeleti csoport](../azure-resource-manager/management-groups-overview.md) előfizetések. A vezérlő például megakadályozza az általános és következetes kód használatának kényszerítése létrehozását a megfelelő helyre, az erőforrások számos különböző módon lehet érvényesíteni, vagy meglévő erőforrásokat naplózási szükséges konfigurációk és beállítások. Minden esetben megismerheti a megfelelőségi állapotát a környezetében engedélyezésére vonatkozó házirendet által létrehozott adatok.

Többféleképpen is lehet hozzáférni a megfelelőségi információkat a házirendet, és kezdeményezésére hozzárendelések állítja elő:

- Használja a [Azure-portálon](#portal)
- Keresztül [parancssori](#command_line) parancsfájlok

A jelentés a megfelelőségi módszereket megnézi, mielőtt vizsgáljuk meg a megfelelőségi adatok frissítésekor, és a gyakoriság és egy kiértékelési ciklusa kiváltó eseményeket.

## <a name="evaluation-triggers"></a>Kiértékelési eseményindítók

A befejezett kiértékelési ciklusa eredményei jelennek meg a `Microsoft.PolicyInsights` erőforrás-szolgáltató `PolicyStates` és `PolicyEvents` műveletek. További információ a beállításokról és képességekről az házirend Insights REST API: [házirend Insights](/rest/api/policy-insights/).

Hozzárendelt házirendek és kezdeményezések értékelések történt különféle események miatt fordulhat elő:

- Egy házirend vagy kezdeményezésére rendelve egy hatókört. Ha ez történik, a megadott hatókör alkalmazandó a hozzárendelés körülbelül 30 percet vesz igénybe. Vonatkozik, amennyiben a kiértékelési ciklusa újonnan hozzárendelt házirend vagy kezdeményezésére szemben, és attól függően, hogy a házirend által használt által okozott hatások hatókörön belüli erőforrások kezdődik, vagy kezdeményezésére erőforrások fel van tüntetve megfelelő vagy nem megfelelő. Egy nagy házirend vagy az erőforrások nagy hatókör értékelni kezdeményezésére időt vehet igénybe, így nincs előre definiált általános gyakorlat, ha a kiértékelési ciklusa fogja végrehajtani. Miután befejeződött, a portál és az SDK-k frissített megfelelőségi eredmények érhetők el.
- Egy házirend vagy a hatókör már hozzá van rendelve kezdeményezésére frissül. A kiértékelési ciklusa és ehhez a forgatókönyvhöz időzítési ugyanúgy történik, mint egy új, a hatókörhöz való hozzárendelést.
- Egy erőforrás hatóköre egy hozzárendelt erőforrás-kezelő, a többi, az Azure parancssori felület vagy a Azure PowerShell, a rendszer. Ebben a forgatókönyvben a hatás esemény (hozzáfűzés, naplózni, megtagadása, központi telepítése) és a megfelelőségi állapot elérhetővé válik, a portál és az SDK-k körülbelül 15 perccel.
- Standard megfelelőségi kiértékelési ciklusa. 24 óránként egyszer, hozzárendeléseket a rendszer automatikusan újraértékelése is megtörténik. Egy nagy házirend vagy az erőforrások nagy hatókör értékelni kezdeményezésére időt vehet igénybe, így nincs előre definiált általános gyakorlat, ha a kiértékelési ciklusa fogja végrehajtani. Miután befejeződött, a portál és az SDK-k frissített megfelelőségi eredmények érhetők el.

## <a name="how-compliance-works"></a>Megfelelőségi működése

A hozzárendelés erőforrás állapota nem megfelelő, ha azt nem követi a házirend vagy kezdeményezésére szabályok. Az alábbi táblázat a különböző házirend hatások dolgozni a feltétel kiértékelése az eredményül kapott megfelelőségi állapotát:

| Erőforrás állapota | Következmény | Házirend kiértékelése | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False (Hamis) | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | False (Hamis) | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Jobb megértése, hogyan vannak megjelölve az erőforrások nem megfelelő, most használja az előbb létrehozott házirend hozzárendelés példa.

Tegyük fel, hogy rendelkezik egy erőforráscsoport – ContsoRG, néhány tárfiókok (pirossal kiemelt), nyilvános hálózatokhoz elérhetővé tett tárolókra.

![Storage-fiókok számára nyilvános hálózatokon](media/policy-insights/resource-group01.png)

Ebben a példában kell lennie a biztonsági kockázatok óvatos. Most, hogy a házirend-hozzárendelés létrehozott, a rendszer kiértékeli az összes tárfiók ContosoRG erőforráscsoportban. Azt a három nem megfelelő tárfiókok esetén ebből következően módosítása az állapotot, és naplózás **nem megfelelő.**

![Nem megfelelő tárfiókok naplózása](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portál

Az Azure-portálon bővíthető egy grafikus élmény megjelenítése és a környezetben megfelelőségi állapotának ismertetése. Az a **házirend** lap, a **áttekintése** beállítás részletes adatokat biztosít a megfelelőségi házirendeket és az kezdeményezések elérhető hatóköreit. A megfelelőségi állapot és a hozzárendelés számának kívül tartalmaz egy táblázat megfelelőségi az elmúlt hét napban. A **megfelelőségi** lap tartalmazza-e ezt az információt (kivéve a diagram) jelentős részét, de további szűrési és rendezési lehetőségeket biztosít.

![Megfelelőségi házirendről](media/policy-compliance/compliance-page.png)

Egy házirend vagy kezdeményezésére számára különböző hatóköröket lehet hozzárendelni, vegye figyelembe, hogy a tábla minden egyes hozzárendelése és rendelt hatókörnek definition típusú hatóköre. A nem megfelelő házirendeket és az egyes hozzárendelések nem kompatibilis erőforrások számát is megadja. Egy házirend vagy a tábla kezdeményezésére kattintva biztosít egy mélyebb, hogy adott hozzárendelés megfelelőségi.

![Házirend-megfelelőségi részletei](media/policy-compliance/compliance-details.png)

Miközben az erőforrások listájához a a **nem megfelelő erőforrások** lapon a meglévő erőforrásokat az aktuális hozzárendelés események értékelési állapotát mutatja (hozzáfűzni, naplózási, megtagadása, üzembe helyezés) hozzon létre egy erőforrást a kérés vannak mezőben látható a **események** fülre.

![Házirend-megfelelőségi események](media/policy-compliance/compliance-events.png)

Kattintson a jobb gombbal az esemény szeretné gyűjt az további részleteket, és válassza ki a sorban **tevékenységi naplóit megjelenítése**. A tevékenységnapló oldalon megnyílik, és előre szűrt a Keresés a hozzárendelési és az események részleteinek megjelenítése. A műveletnapló további környezetet, és ezeket az eseményeket kapcsolatos információkat biztosít.

![Házirend-megfelelőségi műveletnapló](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Parancssor

Ugyanazokat az információkat a portálon elérhető lekérhető közvetlenül a REST API használatával (beleértve az [ARMClient](https://github.com/projectkudu/ARMClient)) vagy az Azure PowerShell REST API-val. A REST API-t a teljes részletekért lásd: a [házirend Insights](/rest/api/policy-insights/) hivatkozás. Minden művelet, amely lehetővé teszi, hogy próbálja ki a REST API-referencia lapok rendelkezik egy zöld "próbálja" gombra a böngésző jobb.

A következő példák az Azure PowerShell használatához szeretne létrehozni egy hitelesítési jogkivonatot a példa kóddal. Ezután cserélje le a $restUri egy JSON-objektum, amely majd értelmezni tudja lekérdezni a példákban a kívánt karakterlánc.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Eredmények összefoglalója

A REST API használatával összegzési végzi el felügyeleti csoporthoz, előfizetés, erőforráscsoport, erőforrás, kezdeményezés, házirend, előfizetés hozzárendelés vagy erőforrás csoport szolgáltatásiszint-hozzárendelés. Az előfizetés szintjén házirend Insight használatával az összegzés példa [összefoglalója az előfizetés](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A kimenet az előfizetés foglalja össze. Az alábbi példa kimenetben az összesített megfelelőségi alatt állnak **value.results.nonCompliantResources** és **value.results.nonCompliantPolicies**. A kérelem további adatait, többek között az egyes hozzárendelések, épülnek fel a nem megfelelő számok és az egyes hozzárendelések a helydefiníciós adatokat tartalmaz. A hierarchia minden egyes csoportházirend-objektum tartalmaz egy **queryResultsUri** , amely kiegészítő adatokat azon a szinten eléréséhez használható.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Erőforrások lekérdezése

A fenti példánál **value.policyAssignments.policyDefinitions.results.queryResultsUri** előírt velünk Uri mintával egy adott házirend-definíció nem kompatibilis erőforrások beolvasása. Megnézi a **$filter** IsCompliant érték egyenlő (eq) hamis, a házirend-definíció, és magát a PolicyDefinitionId PolicyAssignmentId van megadva.
Többek között a PolicyAssignmentId a szűrő az az oka az oka, hogy a PolicyDefinitionId sikerült több házirend vagy hatókörök számos kezdeményezésére hozzárendelések szerepel. Adja meg a PolicyAssignmentId, mind a PolicyDefinitionId, azt lehet explicit az eredmények azt keres. Korábban használtuk **legújabb** a PolicyStates a (az egyetlen megengedett érték **policyStatesSummaryResource** a összefoglalója az előfizetés operátor), amely automatikusan úgy állítja be a  **a** és **való** időkerete pedig az elmúlt 24 órában.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példa egy válasz csak megjelenítése kivonatosan mutatja egy nem kompatibilis erőforrás van vágva (vegye figyelembe, hogy @odata.count ténylegesen 15 és megegyezik-e a nem kompatibilis erőforrások a fenti példa száma). A részletes választ biztosít több adatra adatait az erőforrás, a házirend (vagy kezdeményezésére), és a hozzárendelés. Figyelje meg, hogy azt is láthatja, hogy milyen hozzárendelés paraméterek lettek átadva a házirend-definíció.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Események megtekintése

Amikor egy erőforrást jön létre vagy frissül, a házirend kiértékelésének eredménye jön létre. Eredmények nevezzük _házirendeseményeket_. Legutóbbi házirend-események az előfizetéshez tartozó megjelenítéséhez kövesse a következő URI azonosítót.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Az eredmények a következő példához hasonlók:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Házirend-események lekérdezésével kapcsolatban további információkért lásd: a [házirend-események](/rest/api/policy-insights/policyevents) áttekintésével foglalkozó cikkben.

### <a name="azure-powershell-preview"></a>Az Azure PowerShell (előzetes verzió)

Az Azure PowerShell modul a házirend még nem végleges, de nem érhető el a PowerShell-galériában, mint a egy [előzetes kiadás](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights).
Ha PowerShellGet legalább verzió 1.6.0 (előzetes verziójú elemek támogatásához szükséges), letöltheti az előzetes verzióját használja `Install-Module` (Győződjön meg arról, hogy a legújabb [Azure PowerShell](/powershell/azure/install-azurerm-ps) telepítve):

```powershell
# Download preview from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights -AllowPrerelease

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

A kép modulnak három parancsmag segítségével:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Példa: Lekérdezése nem kompatibilis erőforrások legmagasabb számú legfelső hozzárendelt házirend összefoglaló állapotát.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: Az állapot rekord első, az a legtöbb nemrég kiértékelése erőforrás (alapértelmezett érték szerint csökkenő sorrendben időbélyeg).

```powershell
PS > Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Példa: A adatainak lekérdezése összes nem megfelelő virtuális hálózati erőforrásokhoz.

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Példa: Első történt egy adott dátumot követően nem megfelelő virtuális hálózati erőforráshoz kapcsolódó eseményeket.

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

A **PrincipalOid** mező használható egy adott felhasználó az Azure PowerShell-parancsmag segítségével `Get-AzureRmADUser`. Cserélje le **{principalOid}** kap az előző példában a válasz.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Ha rendelkezik egy [Naplóelemzési](../log-analytics/log-analytics-overview.md) munkaterület a `AzureActivity` megoldás az előfizetéshez társítva, akkor is megtekintheti a kiértékelési ciklusa egyszerű Kusto lekérdezésekkel meg nem felelés eredményeinek és a `AzureActivity` tábla. A Naplóelemzési meg nem felelés adatokkal Ez azt is jelenti, hogy sikerült-e riasztások konfigurálni bemutató meg nem felelés egy adott erőforrás, erőforráscsoportban található vagy nem megfelelő elemek, például a több mint 10 az elmúlt 24 órában még akkor is a küszöbérték.

![Szabályzatoknak való megfelelés Log Analytics használatával](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>További lépések

- Tekintse át a [házirend-definíció szerkezetet](policy-definition.md).
- Felülvizsgálati [házirend hatások ismertetése](policy-effects.md).
- Tekintse át a felügyeleti csoport van [az Azure felügyeleti csoportok-erőforrások rendszerezése](../azure-resource-manager/management-groups-overview.md)