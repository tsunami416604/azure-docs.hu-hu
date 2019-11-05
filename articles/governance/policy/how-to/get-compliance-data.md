---
title: Szabályzatok megfelelőségi állapotának beolvasása
description: Azure Policy értékelések és hatások határozzák meg a megfelelőséget. Ismerje meg, hogyan kérheti le a megfelelőségi adatokat.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: bd65fcf6ebff931fbb408ca8337a37d355221dfe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480236"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure-erőforrások megfelelőségi információk beolvasása

Azure Policy egyik legnagyobb előnye az, hogy az előfizetések és a vezérlőelemek az előfizetések vagy az előfizetések [felügyeleti csoportjai](../../management-groups/overview.md) számára biztosítanak forrásokat. Ez a vezérlő számos különböző módon gyakorolható, például megakadályozhatja, hogy az erőforrások nem a megfelelő helyen jöjjenek létre, melyek a közös és konzisztens címkék használatát kényszerítik, vagy a meglévő erőforrásokat naplózzák a megfelelő konfigurációkhoz és beállításokhoz. A Azure Policy az összes esetben létrehozta az adatait, így megismerheti a környezet megfelelőségi állapotát.

A szabályzat és a kezdeményezési hozzárendelések által létrehozott megfelelőségi adatokhoz többféleképpen férhet hozzá:

- A [Azure Portal](#portal) használata
- [Parancssori](#command-line) parancsfájlok használatával

Mielőtt megvizsgáljuk a megfelelőségi jelentés módszereit, nézzük meg, hogy mikor frissülnek a megfelelőségi információk, és milyen gyakorisággal és eseményekkel indítják el a kiértékelési ciklust.

> [!WARNING]
> Ha a megfelelőségi állapotot **nem regisztráltként**jelenti, ellenőrizze, hogy a **Microsoft. PolicyInsights** erőforrás-szolgáltató regisztrálva van-e, és hogy a felhasználó rendelkezik-e a megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) engedélyekkel a RBAC című témakörben leírtak szerint. [ Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Próbaverziós eseményindítók

A befejezett értékelési ciklusok eredményei a `Microsoft.PolicyInsights` erőforrás-szolgáltatóban `PolicyStates` és `PolicyEvents` műveleteken keresztül érhetők el. Az Azure Policy-információk REST API működésével kapcsolatos további információkért tekintse meg az [Azure Policy](/rest/api/policy-insights/)elemzése című témakört.

A hozzárendelt szabályzatok és kezdeményezések értékelése a különböző események eredményeképpen történik:

- Egy házirend vagy kezdeményezés újonnan van hozzárendelve egy hatókörhöz. A hozzárendelés a meghatározott hatókörre való alkalmazása körülbelül 30 percet vesz igénybe. Az alkalmazás alkalmazása után a kiértékelési ciklus az adott hatókörön belüli erőforrásokra kezdődik az újonnan hozzárendelt szabályzattal vagy kezdeményezéssel, valamint a házirend vagy kezdeményezés által használt hatástól függően az erőforrások megfelelőként vagy nem megfelelőként vannak megjelölve. Nagy mennyiségű erőforrásra kiértékelt nagyméretű házirend vagy kezdeményezés időt vehet igénybe. Ezért nincs előre definiált várakozás, ha a kiértékelési ciklus befejeződik. A befejezést követően a frissített megfelelőségi eredmények elérhetők a Portálon és az SDK-ban.

- Egy hatókörhöz már hozzárendelt házirend vagy kezdeményezés frissül. A kiértékelési ciklus és a forgatókönyv időzítése megegyezik egy hatókör új hozzárendelésével.

- Egy erőforrást egy, a Resource Managerrel, REST-vel, Azure CLI-vel vagy Azure PowerShell-vel való hozzárendeléssel rendelkező hatókörre telepítenek. Ebben az esetben az egyes erőforrásokra vonatkozó hatás esemény (Hozzáfűzés, naplózás, megtagadás, üzembe helyezés) és a megfelelő állapotadatok elérhetővé válnak a Portálon és az SDK-k körülbelül 15 perccel később. Ez az esemény nem okoz más erőforrások kiértékelését.

- Szabványos megfelelőség kiértékelési ciklusa. 24 óránként egyszer automatikusan újraértékeli a hozzárendeléseket. Számos erőforrás nagyméretű házirendje vagy kezdeményezése hosszabb időt vehet igénybe, így a kiértékelési ciklus befejezése után nincs előre definiált várakozás. A befejezést követően a frissített megfelelőségi eredmények elérhetők a Portálon és az SDK-ban.

- A [vendég-konfiguráció](../concepts/guest-configuration.md) erőforrás-szolgáltatóját egy felügyelt erőforrás megfelelőségi részleteivel frissíti.

- Igény szerinti vizsgálat

### <a name="on-demand-evaluation-scan"></a>Igény szerinti értékelés vizsgálata

Egy előfizetéshez vagy egy erőforráscsoporthoz tartozó értékelési vizsgálat elindítható a REST API hívásával. Ez a vizsgálat egy aszinkron folyamat. Így a vizsgálat elindításához szükséges REST-végpont nem várja meg, amíg a vizsgálat nem fejeződik be a válaszadáshoz. Ehelyett egy URI-t biztosít a kért értékelés állapotának lekérdezéséhez.

Minden REST API URI tartalmaz olyan változókat, amelyeket le kell cserélnie saját értékekre:

- `{YourRG}` – cserélje le az erőforráscsoport nevére
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

A vizsgálat támogatja az előfizetésben vagy egy erőforráscsoporthoz lévő erőforrások értékelését. A következő URI-struktúrák használatával indítson el egy REST API **post** paranccsal vizsgálatot a hatókörben:

- Előfizetés

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

`{ResourceContainerGUID}` statikusan generálódik a kért hatókörhöz. Ha egy hatókör már igény szerinti vizsgálatot futtat, az új vizsgálat nem indul el. Ehelyett az új kérelem ugyanazt a `{ResourceContainerGUID}` **Location** URI-t kapja meg az állapothoz. Egy REST API **Get** parancs a **Location** URI-hoz egy **202** -as értéket ad vissza, miközben a kiértékelés folyamatban van. Az értékelési vizsgálat befejezését követően **200 OK** állapotot ad vissza. A befejezett vizsgálat törzse egy JSON-válasz a (z) állapottal:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>A megfelelőség működése

Egy hozzárendelésben az erőforrás **nem megfelelő** , ha nem követi a házirend-vagy kezdeményezési szabályokat.
A következő táblázat bemutatja, hogyan működnek a különböző szabályzatok az eredményül kapott megfelelőségi állapotra vonatkozó feltételek kiértékelésével:

| Erőforrás állapota | Következmény | Szabályzat kiértékelése | Megfelelőségi állapot |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False (Hamis) | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | False (Hamis) | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

Tegyük fel például, hogy van egy erőforráscsoport – ContsoRG, és néhány Storage-fiók (piros színnel), amelyek nyilvános hálózatokon vannak kitéve.

![Nyilvános hálózatoknak kitett Storage-fiókok](../media/getting-compliance-data/resource-group01.png)

Ebben a példában óvatosnak kell lennie a biztonsági kockázatokkal szemben. Most, hogy létrehozott egy szabályzat-hozzárendelést, a rendszer kiértékeli a ContosoRG erőforráscsoport összes Storage-fiókját. A három nem megfelelő tárolási fiókot naplózza, így az állapotukat **nem megfelelőre** változtatja.

![Naplózott nem megfelelő tárolási fiókok](../media/getting-compliance-data/resource-group03.png)

A **megfelelő** és **nem megfelelő**szabályzatok és erőforrások mellett három más állam is van:

- **Ütköző**: az ütköző szabályok két vagy több szabályzatot tartalmaznak. Például két házirend fűzi hozzá ugyanazt a címkét eltérő értékekkel.
- **Nem indult el**: a kiértékelési ciklus nem indult el a házirendhez vagy az erőforráshoz.
- **Nincs regisztrálva**: a Azure Policy erőforrás-szolgáltató nincs regisztrálva, vagy a bejelentkezett fióknak nincs engedélye a megfelelőségi információk olvasásához.

Azure Policy a definíció **típus** és **név** mezőjét használja annak megállapítására, hogy az erőforrás egyezik-e. Ha az erőforrás megfelel, a rendszer megfelelőnek tekinti, és **megfelelő** vagy **nem megfelelő**állapotú. Ha bármelyik **típus** vagy **név** az egyetlen tulajdonság a definícióban, akkor az összes erőforrás alkalmazható, és ki van értékelve.

A megfelelőség százalékos arányát úgy határozzák meg, hogy az _összes erőforrás_alapján osztja el a **megfelelő** erőforrásokat.
Az _összes erőforrás_ a **megfelelő**, **nem megfelelő**és **ütköző** erőforrások összegeként van meghatározva. Az összesített megfelelőségi számok a különböző erőforrások összegével **megosztható** különálló erőforrások összessége. Az alábbi képen 20 különálló erőforrás áll rendelkezésre, és csak az egyik **nem megfelelő**. A teljes erőforrás-megfelelőség 95% (19 – 20).

![Példa a megfelelőségi oldal szabályzatoknak való megfelelőségére](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portál

A Azure Portal grafikus élményt nyújt a környezet megfelelőségi állapotának megjelenítéséhez és megismeréséhez. **A szabályzat lapon az** **áttekintő** lehetőség a házirendek és kezdeményezések megfelelőségére vonatkozó rendelkezésre álló hatókörök részletes adatait tartalmazza. A megfelelőségi állapottal és a hozzárendelések számával együtt az elmúlt hét nap megfelelőségét bemutató diagramot tartalmaz. A **megfelelőségi** oldal ezen információk nagy részét tartalmazza (kivéve a diagramot), de további szűrési és rendezési lehetőségeket is biztosít.

![Azure Policy megfelelőségi lap – példa](../media/getting-compliance-data/compliance-page.png)

Mivel egy házirend vagy kezdeményezés különböző hatókörökhöz rendelhető, a tábla tartalmazza az egyes hozzárendelések hatókörét és a hozzárendelt definíció típusát. A nem megfelelő erőforrások száma és az egyes hozzárendelésekhez nem megfelelő szabályzatok is rendelkezésre állnak. Ha egy házirendre vagy kezdeményezésre kattint a táblázatban, az adott hozzárendelés megfelelőségét részletesebben is megtekintheti.

![Azure Policy megfelelőségi részletek oldalának példája](../media/getting-compliance-data/compliance-details.png)

Az **erőforrás-megfelelőség** lapon található erőforrások listája az aktuális hozzárendelés meglévő erőforrásainak kiértékelési állapotát jeleníti meg. A lap alapértelmezett értéke **nem megfelelő**, de szűrhető.
Az erőforrás-létrehozási kérelem által aktivált események (Hozzáfűzés, naplózás, megtagadás, üzembe helyezés) az **események** lapon jelennek meg.

> [!NOTE]
> Az AK-motor házirendjének esetében a megjelenő erőforrás az erőforráscsoport.

![Azure Policy megfelelőségi események – példa](../media/getting-compliance-data/compliance-events.png)

[Erőforrás-szolgáltatói mód](../concepts/definition-structure.md#resource-provider-modes) erőforrásai esetében az **erőforrás-megfelelőség** lapon válassza ki az erőforrást, vagy kattintson a jobb gombbal a sorra, és válassza a **megfelelőség megtekintése részletei** lehetőséget. Ekkor megnyílik az összetevő megfelelőségi adatai. Ez a lap lapokat is kínál az ehhez az erőforráshoz, eseményekhez, összetevő-eseményekhez és változási előzményekhez rendelt házirendek megtekintéséhez.

![Azure Policy összetevő megfelelőségi részletei – példa](../media/getting-compliance-data/compliance-components.png)

Az erőforrás-megfelelőség lapon kattintson a jobb gombbal az esemény azon sorára, amelyről további részleteket szeretne gyűjteni, majd válassza a **tevékenységi naplók megjelenítése**lehetőséget. Megnyílik a műveletnapló lap, és a rendszer előre szűri a hozzárendelés részleteit és az eseményeket. A műveletnapló további kontextust és információkat nyújt ezekről az eseményekről.

![Azure Policy megfelelőségi tevékenység naplójának példája](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>A nem megfelelőség megismerése

<a name="change-history-preview"></a>

Ha egy erőforrás úgy van meghatározva, hogy **nem megfelelőnek**minősül, számos lehetséges oka lehet. Annak megállapításához, hogy az erőforrás **nem megfelelő-** e, vagy hogy megkeresse a felelős változást, tekintse [meg a nem megfelelőség megállapítása](./determine-non-compliance.md)című témakört.

## <a name="command-line"></a>Parancssor

A portálon megjelenő információk a REST API (beleértve a [ARMClient](https://github.com/projectkudu/ARMClient)is), az Azure PowerShell és az Azure CLI (előzetes verzió) használatával kérhetők le.
A REST API részletes ismertetését lásd: [Azure Policy](/rest/api/policy-insights/) -információk áttekintése. Az REST API-hivatkozási lapokon minden művelethez zöld "kipróbálás" gomb tartozik, amely lehetővé teszi, hogy közvetlenül a böngészőben próbálja ki.

Használjon ARMClient vagy hasonló eszközt az Azure-ba való hitelesítés kezeléséhez az REST API példákkal.

### <a name="summarize-results"></a>Eredmények összegzése

A REST API az összegzést tároló, definíció vagy hozzárendelés alapján végezheti el. Íme egy példa az előfizetés szintjén az Azure Policy Insight összefoglalása az [előfizetéshez](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A kimenet összefoglalja az előfizetést. Az alábbi példában szereplő kimenetben az összegzett megfelelőség az **Value. Results. nonCompliantResources** és **Value. Results. nonCompliantPolicies**alatt található. Ez a kérelem további részleteket tartalmaz, beleértve az egyes hozzárendeléseket, amelyek a nem megfelelő számokat és az egyes hozzárendelések definíciós információit tartalmazzák. A hierarchiában lévő minden egyes házirend-objektum egy **queryResultsUri** tartalmaz, amely az adott szinten további részletek beszerzésére használható.

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

A fenti példában az **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** egy minta URI-t biztosít egy adott házirend-definícióhoz tartozó összes nem megfelelő erőforráshoz. Ha a **$Filter** értéket keresi, a IsCompliant értéke (EQ) hamis értékre van beállítva, PolicyAssignmentId van megadva a házirend-definícióhoz, majd maga a PolicyDefinitionId. A szűrőben lévő PolicyAssignmentId belefoglalásának oka, hogy a PolicyDefinitionId különböző hatókörű házirend-vagy kezdeményezési hozzárendelésekben létezhetnek. A PolicyAssignmentId és a PolicyDefinitionId megadásával explicit módon megadhatjuk a keresett eredményeket. Korábban a **legújabb**PolicyStates használtuk, amely az elmúlt 24 órában automatikusan beállítja a **from** és **az** időpontot.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Az alábbi példában szereplő válasz egyetlen nem megfelelő erőforrásra van kimetszve a rövidség kedvéért. A részletes válasz több adatot tartalmaz az erőforrásról, a házirendről vagy a kezdeményezésről, valamint a hozzárendelésről. Figyelje meg, hogy azt is láthatja, hogy milyen hozzárendelési paramétereket adtak át a szabályzat-definíciónak.

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

Egy erőforrás létrehozásakor vagy frissítésekor a rendszer létrehoz egy szabályzat-kiértékelési eredményt. Az eredményeket _házirend-eseményeknek_nevezzük. A következő URI használatával megtekintheti az előfizetéshez társított legutóbbi házirend-eseményeket.

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

Példa: az állapot összegzésének beolvasása a legfelső szintű hozzárendelt szabályzathoz a legmagasabb számú nem megfelelő erőforrással.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Példa: az állapot rekordjának beolvasása a legutóbb kiértékelt erőforráshoz (alapértelmezés szerint az időbélyegző csökkenő sorrendben történik).

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

Példa: az összes nem megfelelő virtuális hálózati erőforrás adatainak beolvasása.

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

Példa: olyan nem megfelelő virtuális hálózati erőforrásokhoz kapcsolódó események beszerzése, amelyek egy adott dátum után történtek.

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

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).