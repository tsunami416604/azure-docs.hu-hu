---
title: Az Azure Policy segítségével a nem megfelelő erőforrások szervizelése
description: Ez az útmutató végigvezeti a szervizelés, amely nem felel meg a házirendek az Azure Policy-erőforrások.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 747650bc47644cdca07f705f42d063c995ebe9bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980253"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Az Azure Policy segítségével a nem megfelelő erőforrások szervizelése

Erőforrások, amelyek nem megfelelő, egy **deployIfNotExists** házirend elhelyezheti keresztül megfelelő állapotba **szervizelési**. Szervizelési végezhető el ezt a csoportházirend futtatása az **deployIfNotExists** a szabályzat hatása a meglévő erőforrások. Ez az útmutató végigvezeti a lépéseken, valósítható meg ez szükséges.

## <a name="how-remediation-security-works"></a>Szervizelési biztonsági működése

Házirend futtatásakor a sablon a **deployIfNotExists** szabályzat-definíció hajtja végre ezt a [identitás](../../../active-directory/managed-identities-azure-resources/overview.md).
Szabályzat létrehoz egy felügyelt identitás számára, hogy minden hozzárendelés esetében, de meg kell adni a felügyelt identitás megadását milyen szerepkörök részleteit. Ha a felügyelt identitás hiányzik a szerepkörök, ez jelenik meg a szabályzat vagy a szabályzatot tartalmazó kezdeményezések a hozzárendelés során. A portál használata esetén a házirend automatikusan megkapják a felügyelt identitás a listán szereplő szerepkörök követően hozzárendelés van.

![Felügyelt identitás – hiányzó szerepkör](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Ha egy erőforrás módosította **deployIfNotExists** hozzáférést kap a szabályzat-hozzárendelés, felügyelt identitás a hozzárendelés hatókörét programozott módon kell lennie azon kívül, vagy a szervizelési központi telepítés sikertelen lesz.

## <a name="configure-policy-definition"></a>Szabályzat-definíció konfigurálása

Az első lépés az, hogy a Szerepkörök definiálása, amely **deployIfNotExists** szabályzatdefinícióban sikeres üzembe helyezéséhez a tartalom a csomagban foglalt sablon van szüksége. Alatt a **részletek** tulajdonság, adjon hozzá egy **roleDefinitionIds** tulajdonság. Ez a karakterláncok, amelyek megfelelnek a szerepkört a környezetben.
Egy teljes példa: a [deployIfNotExists példa](../concepts/effects.md#deployifnotexists-example).

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
Get-AzureRmRoleDefinition -Name 'Contributor'
```

## <a name="create-a-remediation-task"></a>A javítási feladat létrehozása

Kiértékelés, a szabályzat-hozzárendelés során **deployIfNotExists** érvénybe határozza meg, hogy vannak-e a nem megfelelő erőforrások. Ha a nem megfelelő erőforrások találhatók, a részletek a biztosított a **szervizelési** lapot. A nem megfelelő tároló erőforrásait tartalmazó szabályzatok listáján, valamint a aktiválásához lehetőség egy **javítási feladat**. Ez az mit hoz létre a központi telepítés a **deployIfNotExists** sablont.

Hozhat létre egy **javítási feladat**, kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg és válassza ki a **Szabályzat** elemet.

   ![Szabályzat keresése](../media/remediate-resources/search-policy.png)

1. Válassza ki **szervizelési** az Azure Policy oldal bal oldalán.

   ![Válassza ki a szervizelés](../media/remediate-resources/select-remediation.png)

1. Az összes **deployIfNotExists** a szabályzat-hozzárendelést a nem megfelelő erőforrások tartalmazzák a **javítása házirendek** lapra, és az adatok table. Kattintson az erőforrások, amelyek nem megfelelő házirendet. A **új javítási feladat** lap megnyitásakor.

   > [!NOTE]
   > Nyisson meg egy másik módszere a **javítási feladat** lapot, hogy keresse meg és kattintson a szabályzatra, a **megfelelőségi** lapon, majd kattintson a **javítási feladat létrehozása** gombra.

1. A a **új javítási feladat** lapon, szűrheti az erőforrásokat a használatával javíthatja a **hatókör** választja ki a gyermekszintű erőforrása, amelyekhez a szabályzat hozzá volt rendelve a három pontra (beleértve az egyes erőforrások lefelé objektumok). Ezenkívül használhatja a **helyek** legördülő tovább szűkítheti az erőforrásokat. Csak a táblázatban szereplő erőforrások szervizelni fogja.

   ![Szervizelje - erőforrások kiválasztása](../media/remediate-resources/select-resources.png)

1. A javítási feladat kezdeményezni, ha az erőforrások vannak szűrve kattintva **szervizelése**. A szabályzat megfelelőségi lap nyílik meg a **javítási feladatok** fülre, és a feladatok előrehaladásának megjelenítése.

   ![Szervizelje - feladat a folyamatban](../media/remediate-resources/task-progress.png)

1. Kattintson a **javítási feladat** a szabályzat megfelelőségi lapon részletes információkat a folyamat állapotát. A szűrés a feladat használható együtt a szervizelt alatt álló erőforrások listája látható.

1. Az a **remedation feladat** lapon kattintson a jobb gombbal egy erőforrás vagy a javítási feladat üzembe helyezési megtekintéséhez vagy az erőforrás. A sor végén kattintson a **kapcsolatos eseményeket** például egy hibaüzenet részleteinek megtekintéséhez.

   ![Szervizelje - erőforrás a feladat helyi menü](../media/remediate-resources/resource-task-context-menu.png)

Üzembe helyezett erőforrások keresztül egy **javítási feladat** hozzáadódik a **üzembe helyezett erőforrások** fülre a házirend megfelelőségi oldalon rövid késleltetés után.

## <a name="next-steps"></a>További lépések

- Tekintse át a következő példák [Azure Policy-minták](../samples/index.md)
- Tekintse át a [szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)
- Felülvizsgálat [házirend hatások ismertetése](../concepts/effects.md)
- Megismerheti, hogyan [szabályzatok létrehozása programozott módon](programmatically-create.md)
- Ismerje meg, hogyan [megfelelőségi adatok lekérése](getting-compliance-data.md)
- A felügyeleti csoportok áttekintéséért lásd [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../../management-groups/overview.md) ismertető részt.