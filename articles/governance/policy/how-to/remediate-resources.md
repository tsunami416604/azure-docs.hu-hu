---
title: Nem megfelelő erőforrások szervizelése
description: Ez az útmutató végigvezeti a szervizelés, amely nem felel meg a házirendek az Azure Policy-erőforrások.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: fe06e7081e4e3691aeb054985f9f2f3f6dc7d19e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794988"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Az Azure Policy segítségével a nem megfelelő erőforrások szervizelése

Erőforrások, amelyek nem megfelelő, egy **deployIfNotExists** házirend elhelyezheti keresztül megfelelő állapotba **szervizelési**. Szervizelési végezhető el ezt a csoportházirend futtatása az **deployIfNotExists** a szabályzat hatása a meglévő erőforrások. Ez a cikk bemutatja a megértéséhez, valamint szervizelési szabályzattal elvégzéséhez szükséges lépéseket.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="how-remediation-security-works"></a>Szervizelési biztonsági működése

Házirend futtatásakor a sablon a **deployIfNotExists** szabályzat-definíció hajtja végre ezt a [identitás](../../../active-directory/managed-identities-azure-resources/overview.md).
Szabályzatot hoz létre egy felügyelt identitás minden hozzárendelés esetében, de rendelkeznie kell megadni a felügyelt identitás milyen szerepkörök részleteit. A felügyelt identitás szerepkörök hiányoznak, ha a házirend vagy-kezdeményezéshez a hozzárendelés során ez a hiba jelenik meg. A portál használata esetén a házirend automatikusan megkapják a felügyelt identitás a listán szereplő szerepkörök után hozzárendelés elindult.

![Felügyelt identitás – hiányzó szerepkör](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Ha egy erőforrás módosította **deployIfNotExists** terjed ki a szabályzat-hozzárendelést vagy a sablon tulajdonságainak hozzáfér a szabályzat-hozzárendelés hatókörén kívüli erőforrások, a hozzárendelés-felügyelt identitásnak kell lennie [manuálisan hozzáférést](#manually-configure-the-managed-identity) vagy a szervizelési központi telepítés sikertelen lesz.

## <a name="configure-policy-definition"></a>Szabályzat-definíció konfigurálása

Az első lépés az, hogy a Szerepkörök definiálása, amely **deployIfNotExists** szabályzatdefinícióban sikeres üzembe helyezéséhez a tartalom a csomagban foglalt sablon van szüksége. Alatt a **részletek** tulajdonság, adjon hozzá egy **roleDefinitionIds** tulajdonság. Ez a tulajdonság karakterláncok, amelyek megfelelnek a szerepkört a környezetben. Egy teljes példa: a [deployIfNotExists példa](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** a teljes erőforrás-azonosítóját használja, és nem használ a rövid **roleName** szerepkör. A "Közreműködő" szerepkört a környezetben az azonosító lekéréséhez használja a következő kódot:

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Manuálisan konfigurálnia a felügyelt identitás

A portál használatával hozzárendelés létrehozásakor házirend állít elő, a felügyelt identitás és is biztosít, a definiált szerepkörök **roleDefinitionIds**. A következő feltételek esetében alkalmazhatja a felügyelt identitás létrehozása és az engedélyek hozzárendelése manuálisan kell elvégezni:

- Miközben az SDK-t (például az Azure PowerShell)
- A sablon által a hozzárendelési hatókör kívül erőforrás módosításának
- Ha a sablon által kívül a hozzárendelési hatókör erőforrás olvasható

> [!NOTE]
> Az Azure PowerShell és a .NET az egyetlen SDK-k, amelyek jelenleg támogatják ezt a funkciót.

### <a name="create-managed-identity-with-powershell"></a>Felügyelt identitás létrehozása a PowerShell használatával

Hozzon létre egy felügyelt identitás során a a szabályzat-hozzárendelés **hely** meg kell határozni és **AssignIdentity** használt. Az alábbi példa lekéri a beépített szabályzat definíciója **üzembe helyezése az SQL-Adatbázisok transzparens adattitkosításának**, és beállítja a célként megadott erőforráscsoportja, majd létrehozza a hozzárendelést.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A `$assignment` változó már tartalmazza a felügyelt identitás és a standard szintű értékeket adja vissza, ha egy szabályzat-hozzárendelés létrehozása a résztvevő-azonosító. Keresztül elérhető `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Engedélyezés definiált szerepkörök a PowerShell-lel

Az új felügyelt identitás kell végeznie az Azure Active Directory replikációs, mielőtt azt is biztosítani a szükséges szerepkörök. Replikáció befejeződése után az alábbi példa ismétlődik-e a szabályzat-definíció `$policyDef` számára a **roleDefinitionIds** , és használja [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) adni az új felügyelt identitás a szerepkörök.

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

A definiált szerepkörök használatával a portál használatával adja meg a hozzárendelés felügyelt identitás kétféleképpen **hozzáférés-vezérlés (IAM)** vagy a szabályzatot vagy kezdeményezést hozzárendelés szerkesztése, majd **mentése**.

A hozzárendelés felügyelt identitás ad hozzá egy szerepkörhöz, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán.

1. Keresse meg a hozzárendelés, amely rendelkezik egy felügyelt identitás, és kattintson a nevére.

1. Keresse meg a **hozzárendelés azonosítója** tulajdonságot az edit oldalon. A hozzárendelés azonosítója lesz hasonló:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   A felügyelt identitás neve nem az utolsó része a hozzárendelés erőforrás azonosítója, amely `2802056bfc094dfb95d4d7a5` ebben a példában. Másolja, ez a része a hozzárendelés erőforrás-azonosítója.

1. Keresse meg az erőforrás vagy az erőforrások szülőtároló (erőforráscsoport, előfizetés, a felügyeleti csoport), manuálisan kell hozzáadni a szerepkör-definíció igénylő.

1. Kattintson a **hozzáférés-vezérlés (IAM)** az erőforrás lapon hivatkozásra, és kattintson a **+ szerepkör-hozzárendelés hozzáadása** felső részén a hozzáférés-vezérlő lapját.

1. Válassza ki a megfelelő szerepkör, amely megfelel egy **roleDefinitionIds** a szabályzat-definíció. Hagyja **rendelhet hozzáféréseket** "Azure AD felhasználói, csoport vagy alkalmazás" az alapértelmezett értékre. Az a **kiválasztása** mezőbe illessze be, vagy írja be a korábban található hozzárendelés erőforrás-azonosító részét. Ha a keresés befejeződött, kattintson az objektumra, ugyanazzal a névvel, válassza ki az azonosítója, és kattintson a **mentése**.

## <a name="create-a-remediation-task"></a>A javítási feladat létrehozása

### <a name="create-a-remediation-task-through-portal"></a>Hozzon létre egy szervizelési feladatot a portálon keresztül

Kiértékelés, a szabályzat-hozzárendelés során **deployIfNotExists** érvénybe határozza meg, hogy vannak-e a nem megfelelő erőforrások. Ha a nem megfelelő erőforrások találhatók, a részletek a biztosított a **szervizelési** lapot. A nem megfelelő tároló erőforrásait tartalmazó szabályzatok listáján, valamint a aktiválásához lehetőség egy **javítási feladat**. Ez a beállítás akkor mit hoz létre a központi telepítés a **deployIfNotExists** sablont.

Hozhat létre egy **javítási feladat**, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

   ![Az összes szolgáltatás szabályzat keresése](../media/remediate-resources/search-policy.png)

1. Válassza ki **szervizelési** az Azure Policy oldal bal oldalán.

   ![A szabályzat oldalon válassza ki a szervizelés](../media/remediate-resources/select-remediation.png)

1. Az összes **deployIfNotExists** a szabályzat-hozzárendelést a nem megfelelő erőforrások tartalmazzák a **javítása házirendek** lapra, és az adatok table. Kattintson az erőforrások, amelyek nem megfelelő házirendet. A **új javítási feladat** lap megnyitásakor.

   > [!NOTE]
   > Nyisson meg egy másik módszere a **javítási feladat** lapot, hogy keresse meg és kattintson a szabályzatra, a **megfelelőségi** lapon, majd kattintson a **javítási feladat létrehozása** gombra.

1. A a **új javítási feladat** lapon, szűrheti az erőforrásokat a használatával javíthatja a **hatókör** választja ki a gyermekszintű erőforrása, ahol a szabályzat hozzá van rendelve a három pontra (beleértve az egyes erőforrások lefelé objektumok). Ezenkívül használhatja a **helyek** legördülő tovább szűkítheti az erőforrásokat. Csak a táblázatban szereplő erőforrások szervizelni fogja.

   ![Szervizelése – adja meg, mely erőforrásokat javítása](../media/remediate-resources/select-resources.png)

1. A javítási feladat megkezdése után az erőforrások vannak szűrve kattintva **szervizelése**. A szabályzat megfelelőségi lap nyílik meg a **javítási feladatok** fülre, és a feladatok előrehaladásának megjelenítése.

   ![Szervizelje - javítási tevékenységek előrehaladásának](../media/remediate-resources/task-progress.png)

1. Kattintson a **javítási feladat** a szabályzat megfelelőségi lapon részletes információkat a folyamat állapotát. A szűrés a feladat használható együtt a szervizelt alatt álló erőforrások listája látható.

1. Az a **javítási feladat** lapon kattintson a jobb gombbal egy erőforrás vagy a javítási feladat üzembe helyezési megtekintéséhez vagy az erőforrás. A sor végén kattintson a **kapcsolatos eseményeket** például egy hibaüzenet részleteinek megtekintéséhez.

   ![Szervizelje - erőforrás a feladat helyi menü](../media/remediate-resources/resource-task-context-menu.png)

Üzembe helyezett erőforrások keresztül egy **javítási feladat** kerülnek a **üzembe helyezett erőforrások** fülre a házirend-megfelelőségi oldalon.

### <a name="create-a-remediation-task-through-azure-cli"></a>Azure CLI-n keresztül javítási feladat létrehozása

Hozhat létre egy **javítási feladat** Azure CLI-vel használni a `az policy remediation` parancsokat. Cserélje le `{subscriptionId}` meg előfizetési azonosítóját és `{myAssignmentId}` együtt a **deployIfNotExists** házirend hozzárendelési azonosítója.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Más szervizelési parancsokat és példákért tekintse meg a [az házirend szervizelési](/cli/azure/policy/remediation) parancsokat.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Azure PowerShell-lel javítási feladat létrehozása

Hozhat létre egy **javítási feladat** az Azure PowerShell-lel, használja a `Start-AzPolicyRemediation` parancsokat. Cserélje le `{subscriptionId}` meg előfizetési azonosítóját és `{myAssignmentId}` együtt a **deployIfNotExists** házirend hozzárendelési azonosítója.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Más szervizelési parancsmagok és példákért tekintse meg a [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) modul.

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Tekintse át a [szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)
- Felülvizsgálat [házirend hatások ismertetése](../concepts/effects.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](programmatically-create.md)
- Ismerje meg, hogyan [megfelelőségi adatok lekérése](getting-compliance-data.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/overview.md) ismertető részt.