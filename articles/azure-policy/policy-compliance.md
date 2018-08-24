---
title: Az Azure Policy a megfelelőségi adatok beolvasása
description: Az Azure házirend értékelések és hatások határozza meg a megfelelőség. Ismerje meg, hogyan kérheti le a megfelelőségi adatait.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/29/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6b310daec67f41ba589ce279e4a2dad427adb734
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818892"
---
# <a name="getting-compliance-data"></a>Megfelelőségi adatok beolvasása

Egyik legnagyobb előnye az Azure Policy egy insight- és vezérlőket biztosít egy adott előfizetés erőforrások vagy [felügyeleti csoport](../azure-resource-manager/management-groups-overview.md) előfizetések. Ez a vezérlő megakadályozza az erőforrások a megfelelő helyen létrehozott kényszerítése általános és következetes címke használatot, például számos különböző módon lehet érvényesíteni, vagy naplózási meglévő erőforrások, a szükséges konfigurációk és beállítások. Minden esetben adatokat ahhoz, hogy a környezet megfelelési állapotának megjelenítése szabályzat jön létre.

Többféleképpen is lehet a megfelelőségi adatokat a szabályzat és a kezdeményezési hozzárendelések által generált eléréséhez:

- Használatával a [Azure Portalon](#portal)
- Keresztül [parancssori](#command-line) scripting

Mielőtt megnézzük a jelentés a megfelelőségi módszereket, tekintsük át megfelelőségi adatok frissítésekor és a gyakoriság és a egy kiértékelési ciklusa kiváltó események.

> [!WARNING]
> Ha a megfelelőségi állapotáról, mint **"N/A"**, ellenőrizze, hogy a **Microsoft.PolicyInsights** erőforrás-szolgáltató regisztrálva van, és hogy a felhasználó rendelkezik-e a megfelelő szerepköralapú hozzáférés-vezérlés (RBAC) engedélyek a leírtak szerint [Itt](azure-policy-introduction.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Értékelés eseményindítók

A befejezett kiértékelési ciklusa eredményei jelennek meg a `Microsoft.PolicyInsights` erőforrás-szolgáltató `PolicyStates` és `PolicyEvents` műveleteket. A beállítások és házirend Insights REST API-képességek kapcsolatos további információkért lásd: [Policy Insights](/rest/api/policy-insights/).

Hozzárendelt házirendeket és kezdeményezések értékelések történt különféle események eredményeként fordulhat elő:

- Egy szabályzatot vagy kezdeményezést újonnan hozzárendel egy hatókörhöz. Ha ez történik, a alkalmazni lehessen a hatókörbe való hozzárendelés körülbelül 30 percet vesz igénybe. Az alkalmazása után az újonnan hozzárendelt szabályzatot vagy kezdeményezést vagy a szabályzat által használt hatása attól függően, hogy hatókörön belüli erőforrások megkezdi a kiértékelési ciklusa, vagy kezdeményezés, erőforrások vannak megjelölve megfelelő vagy nem megfelelő. Egy nagy szabályzatot vagy kezdeményezést erőforrások nagy hatókörének értékelni időt vehet igénybe, így nem előre definiált elvárás, ha az értékelési ciklusát fog befejeződni. Ha kész, a portálon és az SDK-k frissített megfelelőségi eredmények érhetők el.
- Egy szabályzatot vagy kezdeményezést már hozzá van rendelve egy hatókör frissül. A kiértékelési ciklusa és az időzítési ebben a forgatókönyvben ugyanúgy történik, mint egy új hozzárendelést egy hatókörhöz.
- Egy erőforrás üzembe van helyezve, az erőforrás-kezelő, REST, Azure CLI-vel vagy az Azure PowerShell-hozzárendelés egy hatókörhöz. Ebben a forgatókönyvben a hatás esemény (hozzáfűzés, naplózása, megtagadása, üzembe helyezése) és a megfelelő állapotát az egyes erőforrások számára elérhetővé válik, a portálon és az SDK-k körülbelül 15 perc múlva. Ez az esemény nem okozhat más erőforrások értékelése.
- Standard szintű megfelelőségi kiértékelési ciklusa. 24 óránként, a hozzárendelések olyan automatikusan újraértékelése is megtörténik. Egy nagy szabályzatot vagy kezdeményezést erőforrások nagy hatókörének értékelni időt vehet igénybe, így nem előre definiált elvárás, ha az értékelési ciklusát fog befejeződni. Ha kész, a portálon és az SDK-k frissített megfelelőségi eredmények érhetők el.

## <a name="how-compliance-works"></a>Megfelelőségi működése

A hozzárendelés nem megfelelő, ha azt nem követi a csoportházirend vagy a kezdeményezési szabályok egy erőforrás. Az alábbi táblázat a különböző házirend hatások működnek a feltételek kiértékelésével a az eredményül kapott megfelelőségi állapot:

| Erőforrás állapota | Következmény | Szabályzat-kiértékelés | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False (Hamis) | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | False (Hamis) | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Vegyük például, hogy rendelkezik egy erőforráscsoport – ContsoRG, néhány azokat a tárfiókokat (vörös színnel) jelennek meg a nyilvános hálózatok.

![A nyilvános hálózatok vannak kitéve Storage-fiókok](media/policy-insights/resource-group01.png)

Ebben a példában kell legyen óvatos az olyan biztonsági kockázatokat. Most, hogy a létrehozott szabályzat-hozzárendelés, a rendszer kiértékeli az ContosoRG erőforráscsoportban lévő összes storage-fiók. Azt naplózza, hogy a három nem megfelelő tárfiókok, az állapotot, és ezért módosítása **nem megfelelő.**

![Nem megfelelő tárfiókokat naplózza](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portál

Az Azure Portalon bemutatja egy grafikus felületet jelenítenek meg, és a környezet megfelelőségi állapotát ismertetése. Az a **házirend** lapon a **áttekintése** beállítás részletes adatokat biztosít a megfelelőségi szabályzatok és a kezdeményezések elérhető hatóköreit. A megfelelőségi állapot-és száma felhasználónként hozzárendelés kívül diagramját, az elmúlt hét napban megfelelőségi tartalmazza. A **megfelelőségi** lap tartalmaz szinte ugyanezeket az adatokat (kivéve a diagramot), de további szűrési és rendezési lehetőségeket biztosít.

![Megfelelőségi szabályzatról](media/policy-compliance/compliance-page.png)

Mivel egy szabályzatot vagy kezdeményezést több hatókörhöz is rendelhető, vegye figyelembe a tábla hatóköre minden hozzárendelés, és hozzá lett rendelve a hatókör-definíció típusa. A nem megfelelő szabályzatok, és minden hozzárendelés esetében a nem megfelelő erőforrások számát is megadja. Kattintson egy szabályzatot vagy kezdeményezést a tábla a biztosít, hogy adott hozzárendelés megfelelőségi alaposabban.

![Szabályzat megfelelőségi részletei](media/policy-compliance/compliance-details.png)

Miközben az erőforrások listájához a a **nem megfelelő erőforrások** lapon tükrözi az aktuális hozzárendelés események meglévő erőforrások értékelés állapota (hozzáfűzés, naplózása, megtagadása, üzembe helyezése) váltott erőforrás létrehozására vonatkozó kérelem vannak alatt látható a **események** fülre.

![Megfelelőségi házirend-események](media/policy-compliance/compliance-events.png)

Kattintson a jobb gombbal a sor az esemény gyűjtse össze a további részleteket a, és válassza ki, szeretné **Tevékenységnaplók megjelenítése**. A tevékenységnapló oldalon megnyílik, és a Keresés az események és az a hozzárendelés részleteit megjelenítő előre szűrt. A tevékenységnapló további kontextus és eseményekből információkat biztosít.

![Szabályzat megfelelőségi tevékenységnapló](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Parancssor

A portálon elérhető adatokat közvetlenül a REST API használatával is lekérdezhetők (beleértve az [ARMClient](https://github.com/projectkudu/ARMClient)) vagy az Azure PowerShell, REST API-val. Részletes információ a REST API-t, lásd: a [Policy Insights](/rest/api/policy-insights/) hivatkozást. A REST API-referencia lapok ", próbálja meg" egy zöld gombot rendelkezik minden művelet, amely lehetővé teszi, hogy próbálja ki a jobb oldalon, a böngészőben.

Az alábbi példák az Azure PowerShell használatához hozhatnak létre egy hitelesítési tokent ebben a példában kóddal. Ezután cserélje le a $restUri a példákban egy JSON-objektumot, majd a program értelmezni tudja beolvasni a kívánt karakterláncot.

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

### <a name="summarize-results"></a>Összegzés eredményei

A REST API-val, összefoglaló végezhet el a felügyeleti csoport, előfizetés, erőforráscsoport, erőforrás, kezdeményezés, házirend, szint hozzárendelés előfizetést vagy erőforrás-csoport szolgáltatásiszint-hozzárendelés. Íme egy példa az előfizetés szintjén házirend Insight használatával összegzésének [összefoglalója az előfizetés](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A kimenet az előfizetés foglalja össze. Az alábbi példa kimenetében az összesített megfelelőségi vannak **value.results.nonCompliantResources** és **value.results.nonCompliantPolicies**. A kérelem további adatait, például az egyes hozzárendelést a nem megfelelő számokat és a helydefiníció adatait minden hozzárendelés esetében végrehajtott biztosít. A hierarchia minden egyes csoportházirend-objektum biztosít egy **queryResultsUri** , amely további részleteket lekérni azon a szinten használható.

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

A fenti példa használatával **value.policyAssignments.policyDefinitions.results.queryResultsUri** egy minta Uri segítségével velünk a kapcsolatot az összes nem megfelelő erőforrások beolvasása egy adott szabályzatdefiníciót megadva. Megnézzük a **$filter** , IsCompliant értéke (eq) FALSE, PolicyAssignmentId van megadva a szabályzat-definíció, majd a PolicyDefinitionId magát.
Többek között a PolicyAssignmentId szűrő az az oka az oka, hogy a PolicyDefinitionId sikerült több szabályzat vagy a különböző hatókörök kezdeményezési hozzárendelések szerepel. A PolicyAssignmentId, mind a PolicyDefinitionId megadásával az eredmények között, így explicit is lesznek. Korábban a használtuk **legújabb** a PolicyStates a (az egyetlen megengedett érték **policyStatesSummaryResource** meg az előfizetés összegzés operátor), amely automatikusan beállítja egy  **a** és **való** időkeretnél az elmúlt 24 órában.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példaválasz rendelkezik vágva csak megjelenítése egyetlen nem megfelelő erőforrás kivonatosan (vegye figyelembe, hogy @odata.count ténylegesen 15 és megegyezik-e a fenti példa a nem megfelelő erőforrások száma). A részletes választ biztosít több adatra is az erőforrást, a házirend (vagy kezdeményezés), adatait és a hozzárendelés. Figyelje meg, hogy is láthatja, milyen hozzárendelés paraméterek lettek átadva a szabályzat-definíció.

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

Amikor az erőforrás létrehozásakor vagy frissítésekor, egy házirend kiértékelésének eredménye jön létre. Eredmények az úgynevezett _házirend-események_. Legutóbbi házirend-események az előfizetéshez tartozó megtekintéséhez használja a következő URI azonosítót.

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

Házirend-események lekérdezésével kapcsolatos további információkért lásd: a [házirend-események](/rest/api/policy-insights/policyevents) áttekintésével foglalkozó cikkben.

### <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell-modul házirend érhető el, a PowerShell-galériából [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights). A PowerShellGet használata esetén telepítheti a modul használatával `Install-Module -Name AzureRM.PolicyInsights` (Ellenőrizze, hogy a legújabb [Azure PowerShell-lel](/powershell/azure/install-azurerm-ps) telepítve van):

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

A modul rendelkezik három parancsmag segítségével:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Példa: Állapotának beolvasása összegzése a legfelső hozzárendelt szabályzathoz a nem megfelelő erőforrások számát vesszük figyelembe.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: Az állapot rekord első, a legtöbb erőforrást kiértékelte a (alapértelmezés: csökkenő sorrendben-tárhelyek időbélyegző szerint).

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

Példa: Az összes nem megfelelő virtuális hálózati erőforrás adatainak beszerzése.

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

Példa: Nem kompatibilis virtuális hálózati erőforrások, amelyek adott dátum után történt kapcsolatos események beolvasása.

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

A **PrincipalOid** mező segítségével egy adott felhasználó lekérése az Azure PowerShell-parancsmaggal `Get-AzureRmADUser`. Cserélje le **{principalOid}** a válasz az előző példából kap.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Ha rendelkezik egy [Log Analytics](../log-analytics/log-analytics-overview.md) munkaterület a `AzureActivity` megoldás az előfizetéshez kötött, akkor is megtekintheti a kiértékelési ciklusa egyszerű Kusto-lekérdezésekkel meg nem felelés eredményeit és a `AzureActivity` tábla. A meg nem felelés részletei a Log Analyticsben Ez azt is jelenti, hogy sikerült-e riasztások konfigurálni és a egy adott erőforráshoz, erőforráscsoport vagy még a nem megfelelő elemek, például a több mint 10 az elmúlt 24 órában küszöbértéket meg nem felelés esetén tekintse meg.

![Szabályzatoknak való megfelelés, a Log Analytics használatával](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>További lépések

- A [Szabályzatdefiníciók struktúrájának](policy-definition.md) áttekintése.
- A [Szabályzatok hatásainak ismertetése](policy-effects.md).
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../azure-resource-manager/management-groups-overview.md) ismertető részt.
