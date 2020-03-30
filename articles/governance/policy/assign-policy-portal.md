---
title: 'Rövid útmutató: Új házirend-hozzárendelés a portállal'
description: Ebben a rövid útmutatóban az Azure Portal segítségével hozzon létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 03/24/2020
ms.topic: quickstart
ms.openlocfilehash: 3a514478f3cefa49c37c3431a80b578dff480584
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240007"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Rövid útmutató: Házirend-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban létrehoz egy házirend-hozzárendelést, és hozzárendeli a felügyelt lemezek házirend-definícióját _nem használó naplózó virtuális gépeket._

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Házirend keresése minden szolgáltatásban" border="false":::

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Hozzárendelések kiválasztása a Házirend áttekintése lapon" border="false":::

1. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Szabályzat – Hozzárendelések** oldal tetején.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Házirend-definíció hozzárendelése a Hozzárendelések lapról" border="false":::

1. A **Szabályzat hozzárendelése** oldalon a **Hatókör** kiválasztásához kattintson a három pontra, majd válasszon felügyeleti csoportot vagy előfizetést. Ha szeretne, válasszon erőforráscsoportot. A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ezután kattintson a **Kiválasztás** gombra a **Hatókör** oldal alján.

   Ebben a példában a **Contoso** előfizetést használjuk. Saját előfizetése ettől eltérhet.

1. Az erőforrások kizárhatóak a **Hatókör** alapján. A **Kizárások** alacsonyabb szinten kezdődnek, mint a **Hatókör** szintje. A **Kizárások** megadása nem kötelező, ezért most hagyja üresen a mezőt.

1. Kattintson a **Szabályzatdefiníció** melletti három pontra az elérhető definíciók listájának megjelenítéséhez. Az Azure Policy beépített szabályzatdefiníciókat tartalmaz, amelyeket felhasználhat. Számos szabályzatdefiníció elérhető, többek között az alábbiak:

   - Címke és a hozzá tartozó érték kényszerítése
   - Címke és a hozzá tartozó érték alkalmazása
   - Címke öröklése az erőforráscsoporttól, ha hiányzik

   Az elérhető beépített szabályzatok részleges listáját az [Azure Policy-minták című témakörben található.](./samples/index.md)

1. Keresse meg a _Felügyelt lemezeket nem használó virtuális gépek naplózása_ definíciót a szabályzatdefiníciók listájában. Kattintson a szabályzatra, majd kattintson a **Kiválasztás** elemre.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="A megfelelő szabályzatdefiníció megtalálása" border="false":::

1. A **Hozzárendelés neve** mező automatikusan kitöltődik a kiválasztott szabályzat nevével, de megadhat más nevet is. A példánkban meghagyjuk a _Felügyelt lemezeket nem használó virtuális gépek naplózása_ értéket. Ha szeretné hozzáadhat egy **Leírást**. A leírás a szabályzat-hozzárendeléssel kapcsolatos információkat adja meg.
   A **Hozzárendelte** mező automatikusan ki lesz töltve az alapján, hogy ki van bejelentkezve. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. A **Felügyelt identitás létrehozása** jelölőnégyzetet hagyja üresen. Ezt a jelölőnégyzetet be _kell_ ellenőrizni, ha a házirend vagy a kezdeményezés [deployIfNotExists](./concepts/effects.md#deployifnotexists) hatással rendelkező házirendet tartalmaz. Mivel a rövid útmutatóhoz használt házirend nem, hagyja üresen. További információkért lásd a [felügyelt identitásokat](../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](./how-to/remediate-resources.md#how-remediation-security-works).

1. Kattintson a **Hozzárendelés** gombra.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megértéséhez.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

Válassza a **Megfelelőség** lehetőséget a lap bal oldalán. Ezután keresse meg a felügyelt lemezek házirend-hozzárendelését **nem használó virtuális gépek naplózása.**

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Megfelelőségi részletek a szabályzatmegfelelőségi lapon" border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, azok a **Nem megfelelő erőforrások**területen jelennek meg.

Ha a meglévő erőforrások kiértékelésekor egy feltétel igaznak bizonyul, ezek az erőforrások a szabályzatnak nem megfelelőként lesznek megjelölve. A következő táblázat azt mutatja be, hogyan működnek együtt a szabályzatok különböző hatásai a feltételek kiértékelésével a megfelelőségi állapot eléréséhez. Bár nem látja a kiértékelési logikát az Azure Portalon, a megfelelőségi állapot eredményei jelennek meg. A megfelelőségi állapotok eredménye lehet megfelelő vagy nem megfelelő.

| **Erőforrás-állapot** | **Hatás** | **Szabályzat-kiértékelés** | **Megfelelőségi állapot** |
| --- | --- | --- | --- |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Létezik | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False (Hamis) | Megfelelő |
| Új | Naplózás, AuditIfNotExist\* | True (Igaz) | Nem megfelelő |
| Új | Naplózás, AuditIfNotExist\* | False (Hamis) | Megfelelő |

\* Az Append, a DeployIfNotExist és az AuditIfNotExist hatás esetében az IF utasításnak TRUE értéket kell visszaadnia.
Emellett a létezési feltételnek FALSE értéket kell visszaadnia ahhoz, hogy a szabályzat nem megfelelőnek minősüljön. TRUE érték esetén az IF feltétel kiváltja a vonatkozó erőforrások létezési feltételének kiértékelését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához hajtsa végre az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott **Felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a **felügyelt lemezházirend-hozzárendelést nem használó virtuális gépek naplózására,** és válassza **a Hozzárendelés törlése parancsot.**

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Hozzárendelés törlése a Megfelelőség lapról" border="false":::

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy hatókörhöz rendelt hozzá egy szabályzatdefiníciót, és kiértékelte annak megfelelőségi jelentését.
A szabályzatdefiníció ellenőrzi, hogy a hatókörben lévő összes erőforrás megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni arról, hogy milyen házirendeket szeretne hozzárendelni az új erőforrások megfelelősének ellenőrzéséhez, folytassa az oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)