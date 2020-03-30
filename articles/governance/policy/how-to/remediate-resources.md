---
title: Nem megfelelő erőforrások szervizelése
description: Ez az útmutató végigvezeti az Azure-szabályzatszabályzatainak nem megfelelő erőforrások szervizelésén.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471387"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Nem megfelelő erőforrások kiújítása az Azure-szabályzattal

Azok az erőforrások, amelyek nem megfelelőek egy **deployIfNotExists** vagy **módosító** házirendnek, **a szervizelés**révén megfelelő állapotba helyezhetők. A szervizelés úgy történik, hogy utasítja az Azure Policy-t a **deployIfNotExists** hatás vagy a hozzárendelt szabályzat **címkeműveleteinek** futtatására a meglévő erőforrásokon, függetlenül attól, hogy a hozzárendelés felügyeleti csoporthoz, előfizetéshez, erőforráscsoporthoz vagy egyéni erőforráshoz tartozik. Ez a cikk az Azure Policy használatával történő szervizelés megértéséhez és végrehajtásához szükséges lépéseket mutatja be.

## <a name="how-remediation-security-works"></a>A szervizelési biztonság működése

Amikor az Azure Policy futtatja a sablont a **deployIfNotExists** házirend-definícióban, ezt [egy felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md)használatával teszi.
Az Azure Policy minden hozzárendeléshez létrehoz egy felügyelt identitást, de részletesen meg kell adnia a felügyelt identitásnak a szerepköröket. Ha a felügyelt identitásból hiányoznak a szerepkörök, ez a hiba a házirend vagy egy kezdeményezés hozzárendelése során jelenik meg. A portál használatakor az Azure Policy automatikusan megadja a felügyelt identitása a felsorolt szerepkörök hozzárendelés indításakor. A felügyelt identitás _helye_ nincs hatással az Azure Policy használatával való működésre.

![Felügyelt identitás – hiányzó szerepkör](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Ha egy **deployIfNotExists** vagy **módosítás** által módosított erőforrás kívül esik a házirend-hozzárendelés hatókörén, vagy a sablon a házirend-hozzárendelés hatókörén kívül eső erőforrásokon fér hozzá, a hozzárendelés felügyelt identitását manuálisan kell [biztosítani,](#manually-configure-the-managed-identity) vagy a szervizelés telepítése sikertelen lesz.

## <a name="configure-policy-definition"></a>Házirend-definíció konfigurálása

Az első lépés az, hogy meghatározza a szerepköröket, amelyek **deployIfNotExists** és **módosítsa** a szükséges igényeket a házirend-definícióban, hogy sikeresen telepítse a tartalmát a mellékelt sablon. A **details** tulajdonság alatt adjon hozzá egy **roleDefinitionIds tulajdonságot.** Ez a tulajdonság olyan karakterláncok tömbje, amelyek megfelelnek a környezetben lévő szerepköröknek. Egy teljes példa, lásd a [deployIfNotExists például](../concepts/effects.md#deployifnotexists-example) vagy a [módosítási példákat.](../concepts/effects.md#modify-examples)

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

A **roleDefinitionIds** tulajdonság a teljes erőforrás-azonosítót használja, és nem veszi át a szerepkör rövid **szerepkörnevét.** A környezetben a "Közreműködő" szerepkör azonosítójának lekérni, használja a következő kódot:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>A felügyelt identitás manuális konfigurálása

Amikor egy hozzárendelést hoz létre a portálhasználatával, az Azure Policy egyaránt létrehozza a felügyelt identitást, és megadja neki a **roleDefinitionIds definiált szerepköröket.** A következő feltételek mellett a felügyelt identitás létrehozásának és az engedélyek hozzárendelésének lépéseit manuálisan kell elvégezni:

- Az SDK használata közben (például az Azure PowerShell)
- Ha a sablon a hozzárendeléshatókörön kívüli erőforrást módosít
- Ha a sablon a hozzárendeléshatókörön kívüli erőforrást olvas be

> [!NOTE]
> Az Azure PowerShell és a .NET az egyetlen SDK-k, amelyek jelenleg támogatják ezt a funkciót.

### <a name="create-managed-identity-with-powershell"></a>Felügyelt identitás létrehozása a PowerShell használatával

Felügyelt identitás létrehozásához a házirend hozzárendelése során, **hely** definiálni kell, és **AssignIdentity** használni. A következő példa beszerzi a beépített házirend definícióját **Az SQL DB átlátható adattitkosítás telepítése**, beállítja a célerőforrás-csoportot, majd létrehozza a hozzárendelést.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A `$assignment` változó most már tartalmazza a felügyelt identitás elsődleges azonosítóját, valamint a házirend-hozzárendelés létrehozásakor visszaadott szabványos értékeket. Ez elérhető a `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Meghatározott szerepkörök megadása a PowerShellhasználatával

Az új felügyelt identitásnak az Azure Active Directoryn keresztül i. replikációt kell végeznie ahhoz, hogy megkaphassa a szükséges szerepköröket. A replikáció befejezése után a következő példa a `$policyDef` **szerepkördefinitionítési azonosítók** házirend-definícióját művelete után a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) segítségével adja meg az új felügyelt identitásnak a szerepköröknek.

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

### <a name="grant-defined-roles-through-portal"></a>Meghatározott szerepkörök megadása a portálon keresztül

Kétféleképpen adhat meg egy hozzárendelés felügyelt identitásának a megadott szerepköröket a portálhasználatával a **hozzáférés-vezérlés (IAM)** használatával, vagy a házirend- vagy kezdeményezéshozzárendelés szerkesztésével, majd a **Mentés**gombra kattintva.

Ha szerepkört szeretne hozzáadni a hozzárendelés felügyelt identitásához, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán.

1. Keresse meg a felügyelt identitással rendelkező hozzárendelést, és kattintson a névre.

1. Keresse meg a **Hozzárendelés-azonosító** tulajdonságot a szerkesztés lapon. A hozzárendelés-azonosító a következőhez hasonló lesz:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   A felügyelt identitás neve a hozzárendelés-erőforrásazonosító utolsó része, `2802056bfc094dfb95d4d7a5` amely ebben a példában található. Másolja a hozzárendelés-erőforrás azonosítója ezen részébe.

1. Keresse meg az erőforrást vagy az erőforrás-szülő tárolót (erőforráscsoport, előfizetés, felügyeleti csoport), amely manuálisan hozzá adja a szerepkör-definíciót.

1. Kattintson a **hozzáférés-vezérlés (IAM)** hivatkozásra az erőforrások lapon, majd kattintson a **+ Szerepkör-hozzárendelés hozzáadása** elemre a hozzáférés-vezérlési lap tetején.

1. Válassza ki a **házirend-definícióból egy roleDefinitionId-nek** megfelelő szerepkört.
   Hagyja **a hozzáférés hozzárendelése** az "Azure AD-felhasználó, -csoport vagy -alkalmazás" alapértelmezett beállításához. A **Kijelölés** mezőbe illessze be vagy írja be a hozzárendelési erőforrás-azonosító korábbi részét. A keresés befejezése után kattintson az azonos nevű objektumra az Azonosító kiválasztásához, majd kattintson a **Mentés gombra.**

## <a name="create-a-remediation-task"></a>Szervizelési feladat létrehozása

### <a name="create-a-remediation-task-through-portal"></a>Szervizelési feladat létrehozása a portálon keresztül

A kiértékelés során a **házirend-hozzárendelés deployIfNotExists** vagy **módosítási** hatások határozza meg, ha nem megfelelő erőforrások. Ha nem megfelelő erőforrásokat talál, a részletek a **Szervizelés** oldalon találhatók. A nem megfelelő erőforrásokkal rendelkező házirendek listájával együtt lehetőség van a **szervizelési feladat**indítására. Ez a beállítás hozza létre a **deployIfNotExists** sablonból vagy a **módosítási** műveletekből a központi telepítést.

**Szervizelési feladat**létrehozásához hajtsa végre az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

   ![Házirend keresése minden szolgáltatásban](../media/remediate-resources/search-policy.png)

1. Válassza a Szervizelés az Azure Policy lap bal oldalán válassza a **szervizelést.**

   ![Válassza a Szervizelés lehetőséget a Házirend lapon](../media/remediate-resources/select-remediation.png)

1. Minden **deployIfNotExists** és **a** nem megfelelő erőforrásokkal rendelkező házirend-hozzárendelések szerepelnek a **Házirendek a javításra** lap és az adattábla között. Kattintson egy szabályzatra, amely nem megfelelő erőforrásokat tartalmazó. Megnyílik **az Új szervizelési feladat** lap.

   > [!NOTE]
   > A **szervizelési feladatlap** megnyitásának másik módja, ha megkeressük és rákattintanak a szabályzatra a **Megfelelőség lapon,** majd a **Javítási feladat létrehozása** gombra kattintanak.

1. Az **Új szervizelési feladat** lapon szűrje a szervizelésre használt erőforrásokat a **Hatókör** három pontot használva válassza ki a gyermekerőforrásokat aházirend hozzárendeléséhez (beleértve az egyes erőforrásobjektumokra is képest). Ezenkívül a **Helyek** legördülő menüsegítségével tovább szűrheti az erőforrásokat. Csak a táblázatban felsorolt erőforrások lesznek kiállítva.

   ![Javítás – válassza ki, hogy mely erőforrásokat kell kiújítani](../media/remediate-resources/select-resources.png)

1. Az erőforrások szűrése után kezdje el a szervizelési feladatot a **Javítás**gombra kattintva. A házirend-megfelelőségi lap megnyílik a **Szervizelési feladatok** lapon, amely a tevékenységek előrehaladásának állapotát mutatja. A szervizelési feladat által létrehozott központi telepítések azonnal megkezdődnek.

   ![Javítás – a javítási feladatok előrehaladása](../media/remediate-resources/task-progress.png)

1. Kattintson a **szervizelési feladatra** a szabályzat megfelelőségi lapján a folyamat részleteinek megismeréséhez. A tevékenységhez használt szűrés a kiigazított erőforrások listájával együtt jelenik meg.

1. A **szervizelési feladat** lapon kattintson a jobb gombbal egy erőforrásra a szervizelési feladat központi telepítésének vagy az erőforrásnak a megtekintéséhez. A sor végén kattintson a **Kapcsolódó események** elemre a részletek, például egy hibaüzenet megtekintéséhez.

   ![Javítás - erőforrás-tevékenység helyi menüje](../media/remediate-resources/resource-task-context-menu.png)

A **szervizelési feladaton** keresztül üzembe helyezett erőforrások hozzáadódnak a házirend-megfelelőségi lap **Telepített erőforrások** lapjához.

### <a name="create-a-remediation-task-through-azure-cli"></a>Szervizelési feladat létrehozása az Azure CLI-n keresztül

Ha az Azure CLI-vel **szervizelési feladatot** szeretne létrehozni, használja a `az policy remediation` parancsokat. Cserélje `{subscriptionId}` le az előfizetés-azonosítóját és `{myAssignmentId}` a **deployIfNotExists-et,** vagy módosítsa **a** házirend-hozzárendelés-azonosítót.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

További javítási parancsokat és példákat az [az házirend-javítási](/cli/azure/policy/remediation) parancsokban talál.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Szervizelési feladat létrehozása az Azure PowerShell használatával

Ha **szervizelési feladatot** szeretne létrehozni `Start-AzPolicyRemediation` az Azure PowerShell használatával, használja a parancsokat. Cserélje `{subscriptionId}` le az előfizetés-azonosítóját és `{myAssignmentId}` a **deployIfNotExists-et,** vagy módosítsa **a** házirend-hozzárendelés-azonosítót.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

További szervizelési parancsmagok és példák: [az Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) modul.

## <a name="next-steps"></a>További lépések

- Tekintse át a példákat az [Azure Policy-mintákban.](../samples/index.md)
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](get-compliance-data.md)
- Tekintse át, hogy mi a felügyeleti csoport az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal.](../../management-groups/overview.md)
