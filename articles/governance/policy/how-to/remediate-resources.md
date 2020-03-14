---
title: Nem megfelelő erőforrások szervizelése
description: Ez az útmutató végigvezeti az olyan erőforrások szervizelésén, amelyek nem felelnek meg a Azure Policy szabályzatának.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 5cf26f5235fbc35cdc9bfc8527967c3cc5ca91b8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264530"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Az Azure Policy segítségével a nem megfelelő erőforrások szervizelése

Azok az erőforrások, amelyek nem felelnek meg a **deployIfNotExists** vagy a házirend- **módosítási** szabályzatoknak, **szervizeléssel**kompatibilis állapotba helyezhetők. A szervizelést arra utasítja Azure Policy, hogy futtassa a **deployIfNotExists** hatást vagy a hozzárendelt házirend címkézési **műveleteit** a meglévő erőforrásokon, függetlenül attól, hogy a hozzárendelés egy felügyeleti csoportba, egy előfizetésbe, egy erőforráscsoporthoz vagy egy adott erőforrásra vonatkozik-e. Ez a cikk azokat a lépéseket mutatja be, amelyekkel megismerheti és elvégezheti a szervizelést Azure Policy.

## <a name="how-remediation-security-works"></a>Szervizelési biztonsági működése

Ha Azure Policy futtatja a sablont a **deployIfNotExists** házirend-definíciójában, akkor azt [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md)használatával végzi el.
Azure Policy létrehoz egy felügyelt identitást az egyes hozzárendelésekhez, de a felügyelt identitást megadó szerepkörökkel kapcsolatos részletekkel kell rendelkeznie. A felügyelt identitás szerepkörök hiányoznak, ha a házirend vagy-kezdeményezéshez a hozzárendelés során ez a hiba jelenik meg. A portál használatakor Azure Policy automatikusan megadja a felügyelt identitást a felsorolt szerepköröknek a hozzárendelés elindítása után.

![Felügyelt identitás – hiányzó szerepkör](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Ha egy **deployIfNotExists** vagy **módosítás** által módosított erőforrás kívül esik a házirend-hozzárendelés hatókörén, vagy a sablon a szabályzat-hozzárendelés hatókörén kívüli erőforrásokhoz fér hozzá, akkor a hozzárendelés felügyelt identitásának [manuálisan kell hozzáférést adni](#manually-configure-the-managed-identity) , vagy a Szervizelési telepítés sikertelen lesz.

## <a name="configure-policy-definition"></a>Szabályzat-definíció konfigurálása

Első lépésként meg kell határozni azokat a szerepköröket, amelyek **deployIfNotExists** és **módosítása** szükséges a házirend-definícióban a tartalmazott sablon tartalmának sikeres telepítéséhez. A **részletek** tulajdonságban adjon hozzá egy **roleDefinitionIds** tulajdonságot. Ez a tulajdonság karakterláncok, amelyek megfelelnek a szerepkört a környezetben. Teljes példaként tekintse meg a [deployIfNotExists példát](../concepts/effects.md#deployifnotexists-example) vagy a [példák módosításait](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A **roleDefinitionIds** tulajdonság a teljes erőforrás-azonosítót használja, és nem veszi igénybe a szerepkör rövid **roleName** . A "Közreműködő" szerepkört a környezetben az azonosító lekéréséhez használja a következő kódot:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Manuálisan konfigurálnia a felügyelt identitás

Ha a portál használatával hoz létre hozzárendelést, Azure Policy mindkét esetben létrehoz egy felügyelt identitást, és megadja a **roleDefinitionIds**-ben definiált szerepköröket. A következő feltételek esetében alkalmazhatja a felügyelt identitás létrehozása és az engedélyek hozzárendelése manuálisan kell elvégezni:

- Miközben az SDK-t (például az Azure PowerShell)
- A sablon által a hozzárendelési hatókör kívül erőforrás módosításának
- Ha a sablon által kívül a hozzárendelési hatókör erőforrás olvasható

> [!NOTE]
> Az Azure PowerShell és a .NET az egyetlen SDK-k, amelyek jelenleg támogatják ezt a funkciót.

### <a name="create-managed-identity-with-powershell"></a>Felügyelt identitás létrehozása a PowerShell használatával

Ha felügyelt identitást szeretne létrehozni a szabályzat hozzárendelése során, a **helyet** definiálni és **AssignIdentity** kell használni. A következő példa beolvassa a beépített házirend definícióját, amely az **SQL db transzparens adattitkosítását telepíti**, beállítja a célként megadott erőforráscsoportot, majd létrehozza a hozzárendelést.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A `$assignment` változó most már tartalmazza a felügyelt identitás elsődleges AZONOSÍTÓját, valamint a szabályzat-hozzárendelés létrehozásakor visszaadott szabványos értékeket. `$assignment.Identity.PrincipalId`használatával érhető el.

### <a name="grant-defined-roles-with-powershell"></a>Engedélyezés definiált szerepkörök a PowerShell-lel

Az új felügyelt identitás kell végeznie az Azure Active Directory replikációs, mielőtt azt is biztosítani a szükséges szerepkörök. A replikáció befejezését követően a következő példa megismétli a **roleDefinitionIds** `$policyDef` a házirend-definícióját, és a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) használatával adja meg a szerepkörök új felügyelt identitását.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Engedélyezés definiált szerepkörök portálon keresztül

A hozzárendelések felügyelt identitását kétféleképpen lehet megadnia a portál használatával, a **hozzáférés-vezérlés (iam)** használatával, vagy a házirend vagy a kezdeményezés hozzárendelésének szerkesztésével, és a **Mentés**gombra kattintva.

A hozzárendelés felügyelt identitás ad hozzá egy szerepkörhöz, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán.

1. Keresse meg a hozzárendelés, amely rendelkezik egy felügyelt identitás, és kattintson a nevére.

1. Keresse meg a **hozzárendelés-azonosító** tulajdonságot a szerkesztés oldalon. A hozzárendelés azonosítója lesz hasonló:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   A felügyelt identitás neve a hozzárendelési erőforrás AZONOSÍTÓjának utolsó része, amely `2802056bfc094dfb95d4d7a5` ebben a példában. Másolja, ez a része a hozzárendelés erőforrás-azonosítója.

1. Keresse meg az erőforrás vagy az erőforrások szülőtároló (erőforráscsoport, előfizetés, a felügyeleti csoport), manuálisan kell hozzáadni a szerepkör-definíció igénylő.

1. Kattintson a **hozzáférés-vezérlés (iam)** hivatkozásra az erőforrások lapon, majd a hozzáférés-vezérlés lap tetején kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre.

1. Válassza ki a megfelelő szerepkört, amely megfelel egy **roleDefinitionIds** a házirend-definícióban.
   Az "Azure AD-felhasználó, csoport vagy alkalmazás" alapértelmezett értékének beállításához hagyja a **hozzáférés-hozzárendelést** . A **kiválasztás** mezőben illessze be vagy írja be a korábban megjelenő hozzárendelési erőforrás-azonosító részét. A keresés befejeződése után kattintson az azonos nevű objektumra az azonosító kiválasztásához, majd kattintson a **Mentés**gombra.

## <a name="create-a-remediation-task"></a>A javítási feladat létrehozása

### <a name="create-a-remediation-task-through-portal"></a>Szervizelési feladat létrehozása a portálon keresztül

Az értékelés során a szabályzat-hozzárendelés **deployIfNotExists** vagy a hatások **módosítása** meghatározza, hogy vannak-e nem megfelelő erőforrások. Ha nem megfelelő erőforrásokat talál, a részletek a **szervizelés** oldalon találhatók. A nem megfelelő erőforrásokkal rendelkező szabályzatok listája mellett lehetőség van egy **szervizelési feladat**elindítására. Ez a beállítás azt eredményezi, hogy a központi telepítést a **deployIfNotExists** sablonból vagy a **módosítási** műveletekkel hozza létre.

**Szervizelési feladat**létrehozásához kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

   ![Szabályzat keresése az összes szolgáltatásban](../media/remediate-resources/search-policy.png)

1. Válassza a **szervizelés** elemet a Azure Policy lap bal oldalán.

   ![Szervizelés kiválasztása a szabályzat lapon](../media/remediate-resources/select-remediation.png)

1. A nem megfelelő erőforrásokkal rendelkező szabályzat-hozzárendelések minden **deployIfNotExists** és **módosítása** a lap és az adattábla **szervizeléséhez** szükséges. Kattintson az erőforrások, amelyek nem megfelelő házirendet. Megnyílik az **új szervizelési feladat** lap.

   > [!NOTE]
   > A **szervizelési feladat** lap megnyitásának másik módja, ha megkeresi és rákattint a szabályzatra a **megfelelőség** lapon, majd kattintson a **szervizelési feladat létrehozása** gombra.

1. Az **új szervizelési feladat** lapon a **hatóköri** ellipszisek használatával szűrheti a kijavítani kívánt erőforrásokat, hogy kiválassza a gyermek erőforrásokat, amelyekről a szabályzat hozzá van rendelve (beleértve az egyes erőforrás-objektumokat is). Emellett az erőforrások további szűréséhez használja a **helyszínek** legördülő listát. Csak a táblázatban szereplő erőforrások szervizelni fogja.

   ![Szervizelés – kiválaszthatja, hogy mely erőforrások legyenek szervizelve](../media/remediate-resources/select-resources.png)

1. Indítsa el a Szervizelési feladatot az erőforrások szűrése után, a **szervizelés**gombra kattintva. A szabályzat megfelelősége lap megnyílik a **szervizelési feladatok** lapra, hogy megjelenjen a feladatok állapota. A Szervizelési feladat által létrehozott központi telepítések azonnal megkezdődik.

   ![Szervizelés – szervizelési feladatok állapota](../media/remediate-resources/task-progress.png)

1. Kattintson a **szervizelési feladatra** a szabályzat megfelelősége lapon a folyamat részleteinek megismeréséhez. A szűrés a feladat használható együtt a szervizelt alatt álló erőforrások listája látható.

1. A **szervizelési feladat** lapon kattintson a jobb gombbal egy erőforrásra a Szervizelési feladat központi telepítésének vagy erőforrásának megtekintéséhez. A sor végén kattintson a **kapcsolódó események** elemre, hogy megtekintse a részleteket, például a hibaüzenetet.

   ![Szervizelje - erőforrás a feladat helyi menü](../media/remediate-resources/resource-task-context-menu.png)

A **szervizelési feladattal** üzembe helyezett erőforrások hozzáadódnak a szabályzat megfelelősége lap **telepített erőforrások** lapjához.

### <a name="create-a-remediation-task-through-azure-cli"></a>Szervizelési feladat létrehozása az Azure CLI-n keresztül

Ha az Azure CLI-vel szeretne **szervizelési feladatot** létrehozni, használja a `az policy remediation` parancsokat. Cserélje le a `{subscriptionId}`t az előfizetési AZONOSÍTÓra, és `{myAssignmentId}` a **deployIfNotExists** , vagy **módosítsa** a szabályzat-hozzárendelés azonosítóját.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

További szervizelési parancsokért és példákért tekintse meg az az [Policy szervizelési](/cli/azure/policy/remediation) parancsokat.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Szervizelési feladat létrehozása Azure PowerShell használatával

Ha Azure PowerShell használatával szeretne **szervizelési feladatot** létrehozni, használja a `Start-AzPolicyRemediation` parancsokat. Cserélje le a `{subscriptionId}`t az előfizetési AZONOSÍTÓra, és `{myAssignmentId}` a **deployIfNotExists** , vagy **módosítsa** a szabályzat-hozzárendelés azonosítóját.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Más szervizelési parancsmagok és példák esetében tekintse meg az az [. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) modult.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](get-compliance-data.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
