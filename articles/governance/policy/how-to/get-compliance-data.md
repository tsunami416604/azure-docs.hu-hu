---
title: Házirend-megfelelőségi adatok beszerezni
description: Az Azure Policy kiértékelések és hatások határozzák meg a megfelelőséget. Ismerje meg, hogyan szerezheti be az Azure-erőforrások megfelelőségi adatait.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280637"
---
# <a name="get-compliance-data-of-azure-resources"></a>Az Azure-erőforrások megfelelőségi adatainak beszereznie

Az Azure Policy egyik legnagyobb előnye az előfizetések vagy [előfizetések felügyeleti csoportjában](../../management-groups/overview.md) lévő erőforrásokhoz nyújtott elemzés és vezérlők. Ez a vezérlő számos különböző módon gyakorolható, például megakadályozhatja, hogy az erőforrások ne a megfelelő helyen jönnek létre, a közös és konzisztens címkehasználat kényszerítése, vagy a meglévő erőforrások naplózása a megfelelő konfigurációkhoz és beállításokhoz. Az adatokat minden esetben az Azure Policy hozza létre, hogy a környezet megfelelőségi állapotát is megismerhesse.

A házirend- és kezdeményezési hozzárendelések által létrehozott megfelelőségi információkhoz többféleképpen is hozzáférhet:

- Az [Azure Portal](#portal) használata
- [Parancssori](#command-line) parancsfájlok on keresztül

Mielőtt megvizsgálna a megfelelőségi jelentések jelentésének módszereit, nézzük meg, hogy mikor frissülnek a megfelelőségi információk, és milyen gyakorisággal és eseményekkel aktiválhatja a kiértékelési ciklust.

> [!WARNING]
> Ha a megfelelőségi állapot **nincs regisztrálva,** ellenőrizze, hogy a **Microsoft.PolicyInsights** erőforrás-szolgáltató regisztrálva van-e, és hogy a felhasználó rendelkezik-e a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel az [Azure-szabályzat RBAC-jában](../overview.md#rbac-permissions-in-azure-policy)leírtak szerint.

## <a name="evaluation-triggers"></a>Értékelési eseményindítók

A befejezett kiértékelési ciklus eredményei `Microsoft.PolicyInsights` az erőforrás-szolgáltatóban érhetők `PolicyStates` `PolicyEvents` el a műveleteken keresztül. Az Azure Policy Insights REST API műveleteiről az Azure Policy Insights insights című [témakörben](/rest/api/policy-insights/)talál további információt.

A kijelölt politikák és kezdeményezések értékelése különböző események eredményeként történik:

- Egy szabályzat vagy kezdeményezés újonnan van hozzárendelve egy hatókörhöz. Körülbelül 30 percet vesz igénybe, amíg a hozzárendelés a megadott hatókörre lesz alkalmazva. Alkalmazása után az értékelési ciklus az adott hatókörön belüli erőforrások esetében megkezdődik az újonnan hozzárendelt házirend vagy kezdeményezés ellenében, és a házirend vagy a kezdeményezés által használt hatásoktól függően az erőforrások megfelelőként vagy nem megfelelőként vannak megjelölve. Egy nagy szabályzat vagy kezdeményezés értékelése az erőforrások széles körére időbe telhet. Mint ilyen, nincs előre meghatározott elvárás, hogy mikor fejeződik be az értékelési ciklus. Miután befejeződött, a frissített megfelelőségi eredmények elérhetők a portálon és az SDK-kban.

- A hatókörhöz már hozzárendelt házirend vagy kezdeményezés frissül. A kiértékelési ciklus és az időzítés ebben a forgatókönyvben ugyanaz, mint egy új hozzárendelés egy hatókörhöz.

- Egy erőforrás üzembe helyezése egy hatókörbe egy hozzárendelés keresztül Resource Manager, REST, Azure CLI vagy az Azure PowerShell. Ebben a forgatókönyvben a hatás esemény (hozzáfűzés, naplózás, megtagadása, üzembe helyezés) és az egyes erőforrások megfelelő állapotinformációi körülbelül 15 perccel később válnak elérhetővé a portálon és az SDK-kban. Ez az esemény nem eredményez más erőforrások kiértékelését.

- Standard megfelelőségértékelési ciklus. A hozzárendelések 24 óránként egyszer automatikusan újraértékelésre kerülnek. Egy nagy szabályzat vagy kezdeményezés sok erőforrás időt vehet igénybe, így nincs előre meghatározott elvárás, hogy mikor fejeződik be az értékelési ciklus. Miután befejeződött, a frissített megfelelőségi eredmények elérhetők a portálon és az SDK-kban.

- A [vendég konfigurációs](../concepts/guest-configuration.md) erőforrás-szolgáltató frissül a megfelelőségi részleteket egy felügyelt erőforrás.

- Igény szerinti vizsgálat

### <a name="on-demand-evaluation-scan"></a>Igény szerinti értékelési vizsgálat

Egy kiértékelés egy előfizetés vagy egy erőforráscsoport indítható a REST API-t. Ez a vizsgálat egy aszinkron folyamat. Mint ilyen, a REST-végpont a vizsgálat elindításához nem várja meg, amíg a vizsgálat befejeződött, hogy válaszoljon. Ehelyett uri-t biztosít a kért kiértékelés állapotának lekérdezéséhez.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourRG}`- Cserélje le az erőforráscsoport nevét
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

A vizsgálat támogatja az erőforrások kiértékelését egy előfizetésben vagy egy erőforráscsoportban. A REST API **POST** paranccsal a következő URI-struktúrák használatával indítsa el a hatókör szerint történő vizsgálatot:

- Előfizetés

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Erőforráscsoport

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

A hívás **202 Elfogadott** állapotot ad vissza. A válaszfejlécben egy **Hely** tulajdonság található, amelynek formátuma a következő:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}`statikusan jön létre a kért hatókörhöz. Ha egy hatókör már futtat egy igény szerinti vizsgálatot, az új vizsgálat nem indul el. Ehelyett az új kérelem ugyanazt `{ResourceContainerGUID}` a **helyet** URI állapot. A REST API **GET** parancs a **hely** URI-hoz ad vissza egy **202 Elfogadott,** amíg a kiértékelés folyamatban van. Amikor az értékelési vizsgálat befejeződött, **200 OK** állapotot ad vissza. A befejezett vizsgálat törzse egy JSON-válasz, amelynek állapota:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>A megfelelőség működése

Egy hozzárendelésben egy erőforrás **nem megfelelő,** ha nem követi a szabályzatvagy a kezdeményezés szabályait.
Az alábbi táblázat bemutatja, hogy a különböző házirend-hatások hogyan működnek az eredményül kapott megfelelőségi állapot feltételkiértékelésével:

| Erőforrás állapota | Hatás | Szakpolitikai értékelés | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False (Hamis) | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | False (Hamis) | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Tegyük fel például, hogy rendelkezik egy erőforráscsoporttal – ContsoRG, és néhány (pirossal kiemelve kiemelt) tárfiókkal, amelyek nyilvános hálózatoknak vannak kitéve.

![Nyilvános hálózatoknak kitett tárfiókok](../media/getting-compliance-data/resource-group01.png)

Ebben a példában óvatosnak kell lennie a biztonsági kockázatokkal szemben. Most, hogy létrehozott egy házirend-hozzárendelést, kivan értékelve a ContosoRG erőforráscsoport összes tárfiókjára. A három nem megfelelő tárfiókot naplózza, ezért állapotukat **nem megfelelőre módosítja.**

![Auditált nem megfelelő tárfiókok](../media/getting-compliance-data/resource-group03.png)

A **megfelelő** és **a nem megfelelő**mellett a házirendek és erőforrások három másik állammal is rendelkeznek:

- **Ütköző**: Két vagy több házirend létezik az ütköző szabályokkal. Például két szabályzat, amely ugyanazt a címkét különböző értékekkel fűzi hozzá.
- **Nincs elindítva:** A kiértékelési ciklus még nem kezdődött el a szabályzat vagy erőforrás.
- **Nincs regisztrálva:** Az Azure Policy Resource Provider nincs regisztrálva, vagy a bejelentkezett fiók nem rendelkezik engedéllyel a megfelelőségi adatok olvasásához.

Az Azure Policy a definíció ban szereplő **típus-** és **névmezők** alapján határozza meg, hogy egy erőforrás egyezik-e. Amikor az erőforrás egyezik, akkor alkalmazhatónak minősül, és **a megfelelő** vagy a nem **megfelelő állapotú.** Ha a **definícióban** a típus vagy a **név** az egyetlen tulajdonság, akkor a rendszer minden erőforrást alkalmazhatónak és kiértékeltnek tekint.

A megfelelőségi százalékot úgy határozzák meg, hogy elosztjuk a **megfelelő** erőforrásokat az _összes erőforrással_.
_Az összes erőforrás_ a **megfelelő,** **nem megfelelő**és ütköző erőforrások összegeként van **definiálva.** Az általános megfelelőségi számok a **megfelelő** erőforrások és az összes különálló erőforrás összegének hányadosa. Az alábbi képen 20 különböző erőforrás alkalmazható, és csak egy **nem megfelelő.** Az erőforrások teljes megfelelősége 95% (20-ból 19).

![Példa a szabályzatok megfelelőségi lapjáról](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

Az Azure Portal a környezetmegfelelőség állapotának megjelenítését és megértését mutatja be. A **Szabályzat** lapon az **Áttekintés** beállítás részletesen ismerteti a szabályzatok és kezdeményezések megfelelőségének elérhető hatóköreit. A megfelelőségi állapot és a hozzárendelésenkénti szám mellett egy diagramot tartalmaz, amely az elmúlt hét nap megfelelőségét mutatja. A **Megfelelőség** ilap ugyanezen információk nagy részét tartalmazza (kivéve a diagramot), de további szűrési és rendezési lehetőségeket biztosít.

![Példa az Azure Policy Compliance lapra](../media/getting-compliance-data/compliance-page.png)

Mivel egy házirend vagy kezdeményezés különböző hatókörökhöz rendelhető, a tábla tartalmazza az egyes hozzárendelések hatókörét és a hozzárendelt definíció típusát. A nem megfelelő erőforrások és nem megfelelő házirendek száma az egyes hozzárendelésekhez szintén rendelkezésre áll. A táblázatban szereplő házirendre vagy kezdeményezésre kattintva mélyebben megvizsgálhatja az adott hozzárendelés megfelelőségét.

![Példa az Azure Policy Compliance Details (Azure Policy Compliance Details) lapra](../media/getting-compliance-data/compliance-details.png)

Az **Erőforrás-megfelelőség** lapon található erőforrások listája az aktuális hozzárendelés meglévő erőforrásainak kiértékelési állapotát jeleníti meg. A lap alapértelmezés szerint **nem megfelelő,** de szűrhető.
Az erőforrások létrehozására irányuló kérés által kiváltott események (hozzáfűzés, naplózás, megtagadás, üzembe helyezés) az **Események** lapon jelennek meg.

> [!NOTE]
> AKS Engine-házirend esetén a megjelenített erőforrás az erőforráscsoport.

![Példa az Azure Policy Compliance eseményeire](../media/getting-compliance-data/compliance-events.png)

[Erőforrás-szolgáltató módú](../concepts/definition-structure.md#resource-provider-modes) erőforrások esetén az **Erőforrás-megfelelőség** lapon jelölje ki az erőforrást, vagy kattintson a jobb gombbal a sorra, és válassza a **Megfelelőségi részletek megtekintése parancsot,** megnyitja az összetevő megfelelőségi részleteit. Ez a lap lapokat is kínál az erőforráshoz, eseményekhez, összetevőeseményekhez és módosítási előzményekhez rendelt házirendek megtekintéséhez.

![Példa az Azure Policy Component megfelelőségi részleteire](../media/getting-compliance-data/compliance-components.png)

Az erőforrás-megfelelőségi lapon kattintson a jobb gombbal annak az eseménynek a sorára, amelyről további részleteket szeretne gyűjteni, és válassza a **Tevékenységnaplók megjelenítése parancsot.** Megnyílik a tevékenységnapló lap, és a hozzárendelés és az események részleteit megjelenítő kereséshez előre szűrve lesz. A tevékenységnapló további környezetet és információkat biztosít az eseményekről.

![Példa az Azure-szabályzat megfelelőségi tevékenységnaplójára](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>A meg nem felelés megértése

<a name="change-history-preview"></a>

Ha egy erőforrás **nem megfelelőnek**minősül, annak számos oka lehet. Az erőforrás **nem megfelelő** forrásának megállapításához vagy a felelős módosítás megkereséséhez olvassa el a Meg [nem felelés megállapítása](./determine-non-compliance.md)című témakört.

## <a name="command-line"></a>Parancssor

A portálon elérhető adatok a REST API-val (beleértve az [ARMClient-](https://github.com/projectkudu/ARMClient)t is), az Azure PowerShell és az Azure CLI (előzetes verzió) segítségével is lehívhatók.
A REST API-val kapcsolatos részletekért tekintse meg az [Azure Policy Insights-referencia.](/rest/api/policy-insights/) A REST API referencialapjainak zöld "Try It" gombja van minden műveletnél, amely lehetővé teszi, hogy közvetlenül a böngészőben próbálja ki.

Használja armclient vagy hasonló eszköz az Azure-beli a REST API-példák kezelésére.

### <a name="summarize-results"></a>Eredmények összegzése

A REST API-val az összegzés tároló, definíció vagy hozzárendelés szerint hajtható végre. Íme egy példa az előfizetésszintjén történő összegzésre az Azure Policy Insight [Összegezése az előfizetéshez](/rest/api/policy-insights/policystates/summarizeforsubscription)használatával:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A kimenet összefoglalja az előfizetést. Az alábbi példakimenetben az összesített megfelelőség a **value.results.nonCompliantResources** és **value.results.nonCompliantPolicies érték**alatt található. Ez a kérés további részleteket tartalmaz, beleértve a nem megfelelő számokat és az egyes hozzárendelések definíciós adatait összeadó minden hozzárendelést. A hierarchia minden egyes házirend-objektuma egy **queryResultsUri-t** biztosít, amely további részletek benyomására használható ezen a szinten.

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

A fenti példában a **value.policyAssignments.policyDefinitions.results.queryResultsUri** egy adott házirend-definícióhoz tartozó összes nem megfelelő erőforráshoz biztosít egy uri-mintát. A **$filter** értéket tekintve az IsCompliant egyenlő (eq) és hamis, a PolicyAssignmentId a házirend-definícióhoz, majd maga a PolicyDefinitionId adható meg. A PolicyAssignmentId szűrőbe való belefoglalásának oka az, hogy a PolicyDefinitionId több házirend- vagy kezdeményezés-hozzárendelésben is létezhet különböző hatókörökkel. A PolicyAssignmentId és a PolicyDefinitionId megadásával explicit módon fogalmazhatjuk meg a keresett eredményeket. Korábban a PolicyStates használtuk **legújabb**, amely automatikusan beállítja a **be-és** **időablak** az elmúlt 24 órában.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példaválasz egyetlen nem megfelelő erőforrásra lett vágva a rövidség érdekében. A részletes válasz több adatot tartalmaz az erőforrásról, a házirendről vagy kezdeményezésről és a hozzárendelésről. Figyelje meg, hogy azt is megtekintheti, hogy milyen hozzárendelési paraméterek kerültek át a házirend-definícióhoz.

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

Erőforrás létrehozásakor vagy frissítésekor a házirend-kiértékelés eredménye jön létre. Az eredményeket _házirend-eseményeknek_nevezzük. Az alábbi Uri használatával megtekintheti az előfizetéshez kapcsolódó legutóbbi szabályzati eseményeket.

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

A házirend-események lekérdezéséről az [Azure Policy Events](/rest/api/policy-insights/policyevents) referenciacikkben olvashat bővebben.

### <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell-modul az Azure-szabályzathoz a PowerShell-galériában érhető el [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)néven.
A PowerShellGet használatával telepítheti `Install-Module -Name Az.PolicyInsights` a modult (győződjön meg arról, hogy a legújabb [Azure PowerShell](/powershell/azure/install-az-ps) telepítve van):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

A modul a következő parancsmagokkal rendelkezik:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Példa: A legfelső hozzárendelt házirend állapotösszegzésének beszerzése a legtöbb nem megfelelő erőforrással.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: A legutóbb kiértékelt erőforrás állapotrekordjának beszerzése (az alapértelmezett érték az időbélyeg csökkenő sorrendben).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

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

Példa: Az összes nem megfelelő virtuális hálózati erőforrás részleteinek beszerzése.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

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

Példa: Nem megfelelő virtuális hálózati erőforrásokkal kapcsolatos események beszerzése, amelyek egy adott dátum után történtek.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

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

A **PrincipalOid** mező segítségével egy adott felhasználó az Azure PowerShell-parancsmag. `Get-AzADUser` Cserélje le **a(z) {principalOid}** elemet az előző példából kapott válaszra.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Ha rendelkezik egy Log `AzureActivity` [Analytics-munkaterülettel](../../../log-analytics/log-analytics-overview.md) az előfizetéséhez kötött [Tevékenységnapló-elemzési megoldásból,](../../../azure-monitor/platform/activity-log-collect.md) egyszerű Kusto-lekérdezések `AzureActivity` és a tábla használatával megtekintheti a kiértékelési ciklus meg nem felelési eredményeit is. Az Azure Monitor naplóiban található részletekkel a riasztások beállíthatók a meg nem felelés figyelésére.


![Azure Policy Compliance az Azure Monitor-naplók használatával](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](programmatically-create.md)
- További információ a [nem megfelelő erőforrások kiújulásáról.](remediate-resources.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)