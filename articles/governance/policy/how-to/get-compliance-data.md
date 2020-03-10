---
title: Szabályzatok megfelelőségi állapotának beolvasása
description: Az Azure házirend értékelések és hatások határozza meg a megfelelőség. Ismerje meg, hogyan kérheti le Azure-erőforrásai megfelelőségi adatait.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384427"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure-erőforrások megfelelőségi információk beolvasása

Azure Policy egyik legnagyobb előnye az, hogy az előfizetések és a vezérlőelemek az előfizetések vagy az előfizetések [felügyeleti csoportjai](../../management-groups/overview.md) számára biztosítanak forrásokat. Ez a vezérlő megakadályozza az erőforrások a megfelelő helyen létrehozott kényszerítése általános és következetes címke használatot, például számos különböző módon lehet érvényesíteni, vagy naplózási meglévő erőforrások, a szükséges konfigurációk és beállítások. A Azure Policy az összes esetben létrehozta az adatait, így megismerheti a környezet megfelelőségi állapotát.

Többféleképpen is lehet a megfelelőségi adatokat a szabályzat és a kezdeményezési hozzárendelések által generált eléréséhez:

- A [Azure Portal](#portal) használata
- [Parancssori](#command-line) parancsfájlok használatával

Mielőtt megnézzük a jelentés a megfelelőségi módszereket, tekintsük át megfelelőségi adatok frissítésekor és a gyakoriság és a egy kiértékelési ciklusa kiváltó események.

> [!WARNING]
> Ha a megfelelőségi állapotot **nem regisztráltként**jelenti, ellenőrizze, hogy a **Microsoft. PolicyInsights** erőforrás-szolgáltató regisztrálva van-e, és hogy a felhasználó rendelkezik-e a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel a következő cikkben leírtak szerint: [Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Értékelés eseményindítók

A befejezett értékelési ciklusok eredményei a `Microsoft.PolicyInsights` erőforrás-szolgáltatóban `PolicyStates` és `PolicyEvents` műveleteken keresztül érhetők el. Az Azure Policy-információk REST API működésével kapcsolatos további információkért tekintse meg az [Azure Policy](/rest/api/policy-insights/)elemzése című témakört.

Hozzárendelt házirendeket és kezdeményezések értékelések történt különféle események eredményeként fordulhat elő:

- Egy szabályzatot vagy kezdeményezést újonnan hozzárendel egy hatókörhöz. A hozzárendelés a alkalmazni lehessen a megadott hatókörhöz körülbelül 30 percet vesz igénybe. Az alkalmazása után az újonnan hozzárendelt szabályzatot vagy kezdeményezést vagy a szabályzat által használt hatása attól függően, hogy hatókörön belüli erőforrások megkezdi a kiértékelési ciklusa, vagy kezdeményezés, erőforrások vannak megjelölve megfelelő vagy nem megfelelő. Egy nagy szabályzatot vagy kezdeményezést erőforrások nagy hatókörének értékelni időt vehet igénybe. Mint ilyen nem érkezik nincs előre definiált elvárás, ha a kiértékelési ciklusa fog befejeződni. Ha kész, a portálon és az SDK-k frissített megfelelőségi eredmények érhetők el.

- Egy szabályzatot vagy kezdeményezést már hozzá van rendelve egy hatókör frissül. A kiértékelési ciklusa és az időzítési ebben a forgatókönyvben ugyanúgy történik, mint egy új hozzárendelést egy hatókörhöz.

- Egy erőforrás üzembe van helyezve, az erőforrás-kezelő, REST, Azure CLI-vel vagy az Azure PowerShell-hozzárendelés egy hatókörhöz. Ebben a forgatókönyvben a hatás esemény (hozzáfűzés, naplózása, megtagadása, üzembe helyezése) és a megfelelő állapotát az egyes erőforrások számára elérhetővé válik, a portálon és az SDK-k körülbelül 15 perc múlva. Ez az esemény nem okozhat más erőforrások értékelése.

- Standard szintű megfelelőségi kiértékelési ciklusa. Miután 24 óránként automatikusan újraértékeli a hozzárendeléseket. Egy nagy szabályzatot vagy kezdeményezést, számos erőforrás időt vehet igénybe, így nem előre definiált elvárás, ha az értékelési ciklusát fog befejeződni. Ha kész, a portálon és az SDK-k frissített megfelelőségi eredmények érhetők el.

- A [vendég-konfiguráció](../concepts/guest-configuration.md) erőforrás-szolgáltatóját egy felügyelt erőforrás megfelelőségi részleteivel frissíti.

- Igény szerinti vizsgálat

### <a name="on-demand-evaluation-scan"></a>Igény szerinti-értékelési vizsgálat

Egy értékelési vizsgálatának egy előfizetést vagy egy erőforráscsoport elindítható a REST API-hívással. Ez a vizsgálat a egy aszinkron folyamat. A vizsgálat elindítása a REST-végpont nem mint ilyen, várjon, amíg a vizsgálat teljes válaszolni. Ehelyett a kért kiértékelése állapotának lekérdezése egy URI-t biztosít.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourRG}` – cserélje le az erőforráscsoport nevére
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

A vizsgálat erőforrások értékelése támogatja az egy előfizetésben, vagy egy erőforráscsoportban. A következő URI-struktúrák használatával indítson el egy REST API **post** paranccsal vizsgálatot a hatókörben:

- Előfizetést

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Erőforráscsoport

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

A hívás **202 elfogadott** állapotot ad vissza. A válasz fejlécében szereplő **hely** tulajdonsága a következő formátumú:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` statikusan generálódik a kért hatókörhöz. A hatókör már fut egy igény szerinti vizsgálat, ha új vizsgálatot nincs elindítva. Ehelyett az új kérelem ugyanazt a `{ResourceContainerGUID}` **Location** URI-t kapja meg az állapothoz. Egy REST API **Get** parancs a **Location** URI-hoz egy **202** -as értéket ad vissza, miközben a kiértékelés folyamatban van. Az értékelési vizsgálat befejezését követően **200 OK** állapotot ad vissza. A szervezet egy befejezett ellenőrzési egy JSON-választ a állapota:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Megfelelőségi működése

Egy hozzárendelésben az erőforrás **nem megfelelő** , ha nem követi a házirend-vagy kezdeményezési szabályokat.
Az alábbi táblázat a különböző házirend hatások működnek a feltételek kiértékelésével a az eredményül kapott megfelelőségi állapot:

| Erőforrás állapota | Hatás | Szabályzat-kiértékelés | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False (Hamis) | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | False (Hamis) | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Vegyük például, hogy rendelkezik egy erőforráscsoport – ContsoRG, néhány azokat a tárfiókokat (vörös színnel) jelennek meg a nyilvános hálózatok.

![A nyilvános hálózatok vannak kitéve Storage-fiókok](../media/getting-compliance-data/resource-group01.png)

Ebben a példában kell legyen óvatos az olyan biztonsági kockázatokat. Most, hogy a létrehozott szabályzat-hozzárendelés, a rendszer kiértékeli az ContosoRG erőforráscsoportban lévő összes storage-fiók. A három nem megfelelő tárolási fiókot naplózza, így az állapotukat **nem megfelelőre** változtatja.

![Nem megfelelő tárfiókokat naplózza](../media/getting-compliance-data/resource-group03.png)

A **megfelelő** és **nem megfelelő**szabályzatok és erőforrások mellett három más állam is van:

- **Ütköző**: az ütköző szabályok két vagy több szabályzatot tartalmaznak. Ha például két szabályzat hozzáfűzése a különböző értékkel azonos címkével.
- **Nem indult el**: a kiértékelési ciklus nem indult el a házirendhez vagy az erőforráshoz.
- **Nincs regisztrálva**: a Azure Policy erőforrás-szolgáltató nincs regisztrálva, vagy a bejelentkezett fióknak nincs engedélye a megfelelőségi információk olvasásához.

Azure Policy a definíció **típus** és **név** mezőjét használja annak megállapítására, hogy az erőforrás egyezik-e. Ha az erőforrás megfelel, a rendszer megfelelőnek tekinti, és **megfelelő** vagy **nem megfelelő**állapotú. Ha bármelyik **típus** vagy **név** az egyetlen tulajdonság a definícióban, akkor az összes erőforrás alkalmazható, és ki van értékelve.

A megfelelőség százalékos arányát úgy határozzák meg, hogy az _összes erőforrás_alapján osztja el a **megfelelő** erőforrásokat.
Az _összes erőforrás_ a **megfelelő**, **nem megfelelő**és **ütköző** erőforrások összegeként van meghatározva. Az összesített megfelelőségi számok a különböző erőforrások összegével **megosztható** különálló erőforrások összessége. Az alábbi képen 20 különálló erőforrás áll rendelkezésre, és csak az egyik **nem megfelelő**. A teljes erőforrás-megfelelőség 95 %-os (19 20).

![Példa a megfelelőségi oldal szabályzatoknak való megfelelőségére](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

Az Azure Portalon bemutatja egy grafikus felületet jelenítenek meg, és a környezet megfelelőségi állapotát ismertetése. **A szabályzat lapon az** **áttekintő** lehetőség a házirendek és kezdeményezések megfelelőségére vonatkozó rendelkezésre álló hatókörök részletes adatait tartalmazza. A megfelelőségi állapotok és száma felhasználónként hozzárendelés együtt diagramját, az elmúlt hét napban megfelelőségi tartalmazza. A **megfelelőségi** oldal ezen információk nagy részét tartalmazza (kivéve a diagramot), de további szűrési és rendezési lehetőségeket is biztosít.

![Azure Policy megfelelőségi lap – példa](../media/getting-compliance-data/compliance-page.png)

Egy szabályzatot vagy kezdeményezést több hatókörhöz is rendelhető, mert a tábla minden hozzárendelés és-definíciót, amely hozzá volt rendelve típusát a hatókör magában foglalja. A nem megfelelő erőforrások és a nem megfelelő szabályzatok egyes hozzárendelések száma is biztosítja. Kattintson egy szabályzatot vagy kezdeményezést a tábla a biztosít, hogy adott hozzárendelés megfelelőségi alaposabban.

![Azure Policy megfelelőségi részletek oldalának példája](../media/getting-compliance-data/compliance-details.png)

Az **erőforrás-megfelelőség** lapon található erőforrások listája az aktuális hozzárendelés meglévő erőforrásainak kiértékelési állapotát jeleníti meg. A lap alapértelmezett értéke **nem megfelelő**, de szűrhető.
Az erőforrás-létrehozási kérelem által aktivált események (Hozzáfűzés, naplózás, megtagadás, üzembe helyezés) az **események** lapon jelennek meg.

> [!NOTE]
> Az AK-motor házirendjének esetében a megjelenő erőforrás az erőforráscsoport.

![Azure Policy megfelelőségi események – példa](../media/getting-compliance-data/compliance-events.png)

[Erőforrás-szolgáltatói mód](../concepts/definition-structure.md#resource-provider-modes) erőforrásai esetében az **erőforrás-megfelelőség** lapon válassza ki az erőforrást, vagy kattintson a jobb gombbal a sorra, és válassza a **megfelelőség megtekintése részletei** lehetőséget. Ekkor megnyílik az összetevő megfelelőségi adatai. Ez a lap lapokat is kínál az ehhez az erőforráshoz, eseményekhez, összetevő-eseményekhez és változási előzményekhez rendelt házirendek megtekintéséhez.

![Azure Policy összetevő megfelelőségi részletei – példa](../media/getting-compliance-data/compliance-components.png)

Az erőforrás-megfelelőség lapon kattintson a jobb gombbal az esemény azon sorára, amelyről további részleteket szeretne gyűjteni, majd válassza a **tevékenységi naplók megjelenítése**lehetőséget. A tevékenységnapló oldalon megnyílik, és a Keresés az események és az a hozzárendelés részleteit megjelenítő előre szűrt. A tevékenységnapló további kontextus és eseményekből információkat biztosít.

![Azure Policy megfelelőségi tevékenység naplójának példája](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>A nem megfelelőség megismerése

<a name="change-history-preview"></a>

Ha egy erőforrás úgy van meghatározva, hogy **nem megfelelőnek**minősül, számos lehetséges oka lehet. Annak megállapításához, hogy az erőforrás **nem megfelelő-** e, vagy hogy megkeresse a felelős változást, tekintse [meg a nem megfelelőség megállapítása](./determine-non-compliance.md)című témakört.

## <a name="command-line"></a>Parancssor

A portálon megjelenő információk a REST API (beleértve a [ARMClient](https://github.com/projectkudu/ARMClient)is), az Azure PowerShell és az Azure CLI (előzetes verzió) használatával kérhetők le.
A REST API részletes ismertetését lásd: [Azure Policy](/rest/api/policy-insights/) -információk áttekintése. A REST API-referencia lapok ", próbálja meg" egy zöld gombot rendelkezik minden művelet, amely lehetővé teszi, hogy próbálja ki a jobb oldalon, a böngészőben.

Használjon ARMClient vagy hasonló eszközt az Azure-ba való hitelesítés kezeléséhez az REST API példákkal.

### <a name="summarize-results"></a>Összegzés eredményei

A REST API-val összegzési tároló, a definition vagy a hozzárendelés alapján végezheti el. Íme egy példa az előfizetés szintjén az Azure Policy Insight összefoglalása az [előfizetéshez](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A kimenet az előfizetés foglalja össze. Az alábbi példában szereplő kimenetben az összegzett megfelelőség az **Value. Results. nonCompliantResources** és **Value. Results. nonCompliantPolicies**alatt található. A kérelem további adatait, például az egyes hozzárendelést a nem megfelelő számokat és a helydefiníció adatait minden hozzárendelés esetében végrehajtott biztosít. A hierarchiában lévő minden egyes házirend-objektum egy **queryResultsUri** tartalmaz, amely az adott szinten további részletek beszerzésére használható.

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

A fenti példában az **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** egy minta URI-t biztosít egy adott házirend-definícióhoz tartozó összes nem megfelelő erőforráshoz. Ha a **$Filter** értéket keresi, a IsCompliant értéke (EQ) hamis értékre van beállítva, PolicyAssignmentId van megadva a házirend-definícióhoz, majd maga a PolicyDefinitionId. Többek között a PolicyAssignmentId szűrő az az oka az oka, hogy a PolicyDefinitionId sikerült több szabályzat vagy más hatóköröknek a kezdeményezési hozzárendelések szerepel. A PolicyAssignmentId, mind a PolicyDefinitionId megadásával biztosak lehetünk abban a számíthatunk eredmények explicit. Korábban a **legújabb**PolicyStates használtuk, amely az elmúlt 24 órában automatikusan beállítja a **from** és **az** időpontot.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példaválasz kivonatosan egyetlen nem kompatibilis erőforrás van vágva. A válaszok részletes adatait az erőforrás, a szabályzatot vagy kezdeményezést és a hozzárendelés több adatra is rendelkezik. Figyelje meg, hogy is láthatja, milyen hozzárendelés paraméterek lettek átadva a szabályzat-definíció.

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

Amikor az erőforrás létrehozásakor vagy frissítésekor, egy házirend kiértékelésének eredménye jön létre. Az eredményeket _házirend-eseményeknek_nevezzük. Legutóbbi házirend-események az előfizetéshez tartozó megtekintéséhez használja a következő URI azonosítót.

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

A házirend-események lekérdezésével kapcsolatos további információkért tekintse meg a [Azure Policy Events](/rest/api/policy-insights/policyevents) Reference című cikket.

### <a name="azure-powershell"></a>Azure PowerShell

A Azure Policy Azure PowerShell modulja a PowerShell-galéria az az [. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)néven érhető el.
A PowerShellGet használatával `Install-Module -Name Az.PolicyInsights` használatával telepítheti a modult (ellenőrizze, hogy telepítve van-e a legújabb [Azure PowerShell](/powershell/azure/install-az-ps) ):

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

Példa: Állapotának beolvasása összegzése a legfelső hozzárendelt szabályzathoz a nem megfelelő erőforrások számát vesszük figyelembe.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: Az állapot rekord első, a legtöbb erőforrást kiértékelte a (alapértelmezés: csökkenő sorrendben-tárhelyek időbélyegző szerint).

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

Példa: Az összes nem megfelelő virtuális hálózati erőforrás adatainak beszerzése.

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

Példa: Nem kompatibilis virtuális hálózati erőforrások, amelyek adott dátum után történt kapcsolatos események beolvasása.

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

A **PrincipalOid** mező használatával egy adott felhasználó beolvasható a Azure PowerShell parancsmaggal `Get-AzADUser`. Cserélje le a **{principalOid}** helyére az előző példából kapott választ.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Ha [log Analytics munkaterülettel](../../../log-analytics/log-analytics-overview.md) rendelkezik, és az előfizetéséhez kötött [Activity Log Analytics megoldásból](../../../azure-monitor/platform/activity-log-collect.md) `AzureActivity`, akkor a kiértékelési ciklusban az egyszerű Kusto lekérdezések és a `AzureActivity` tábla használatával is megtekintheti a nem megfelelőségi eredményeket. Azure Monitor naplók részleteivel a riasztások úgy konfigurálhatók, hogy megfigyeljék a nem megfelelőséget.


![Megfelelőség Azure Policy Azure Monitor naplók használatával](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).