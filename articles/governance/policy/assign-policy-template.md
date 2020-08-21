---
title: 'Rövid útmutató: új szabályzat-hozzárendelés sablonokkal'
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hozhat létre szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 2f012f9cc8f986f8f887096098961e44c4b7dffb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689847"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához ARM-sablon használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti egy Azure Resource Manager-sablon (ARM-sablon) használatának folyamatán a szabályzat-hozzárendelés létrehozásához a felügyelt lemezeket nem használó virtuális gépek azonosításához. A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Az ARM-sablon üzembe helyezése Azure Policy az Azure-ban való hozzárendeléséhez" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és a _felügyelt lemezeket nem használó, naplózási virtuális gépek_nevű beépített szabályzat-definíciót rendel hozzá. Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](./samples/index.md).

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/) közül származik.

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json":::

A sablonban definiált erőforrás:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

> [!NOTE]
> Azure Policy szolgáltatás ingyenes. További információ: [Azure Policy áttekintése](./overview.md).

1. Az alábbi képre kattintva jelentkezzen be az Azure Portalra, és nyissa meg a sablont:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Az ARM-sablon üzembe helyezése Azure Policy az Azure-ban való hozzárendeléséhez" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. Válassza ki vagy adja meg a következő értékeket:

   | Név | Érték |
   |------|-------|
   | Előfizetés | Válassza ki az Azure-előfizetését. |
   | Erőforráscsoport | Válassza az **új létrehozása**lehetőséget, adjon meg egy nevet, majd kattintson **az OK gombra**. A képernyőképen az erőforráscsoport neve _mypolicyquickstart \<Date in MMDD\> RG_. |
   | Hely | Válasszon régiót. Például: **USA középső régiója**. |
   | Szabályzat-hozzárendelés neve | Adja meg a szabályzat-hozzárendelés nevét. Ha szeretné, használhatja a szabályzat-definíció megjelenítését. Például a _felügyelt lemezeket nem használó virtuális gépek naplózása_. |
   | RG neve | Adja meg azt az erőforráscsoport-nevet, amelyhez hozzá szeretné rendelni a szabályzatot. Ebben a rövid útmutatóban használja az alapértelmezett **[resourceGroup (). name]** értéket. a **[resourceGroup ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** egy olyan sablon-függvény, amely lekéri az erőforráscsoportot. |
   | Házirend-definíció azonosítója | **/Providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**megadásához. |
   | Elfogadom a fenti feltételeket és kikötéseket | Válassza |

1. Válassza a **Beszerzés** lehetőséget.

Néhány további erőforrás:

- További minták sablonjait az Azure rövid útmutató [sablonjában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)talál.
- A sablon hivatkozásának megtekintéséhez nyissa meg az [Azure-sablonok referenciáját](/azure/templates/microsoft.authorization/allversions).
- Az ARM-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](../../azure-resource-manager/management/overview.md)talál további információt.
- Az előfizetési szintű központi telepítés megismeréséhez tekintse meg [Az erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../../azure-resource-manager/templates/deploy-to-subscription.md)című témakört.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A lap bal oldalán kattintson a **megfelelőség** elemre. Ezután keresse meg _azokat a naplózási virtuális gépeket, amelyek nem használják a felügyelt lemezeken_ létrehozott házirend-hozzárendelést.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="A szabályzatok megfelelőségének áttekintése oldal" border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, akkor a **nem megfelelő erőforrások**alatt jelennek meg.

További információt a [megfelelőség működéséről](./how-to/get-compliance-data.md#how-compliance-works)szóló témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott _Felügyelt lemezeket nem használó virtuális gépek naplózása_ szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a felügyelt lemezek házirend-hozzárendelését _nem használó naplózási virtuális gépekre_ , és válassza a **hozzárendelés törlése**lehetőséget.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Hozzárendelés törlése a megfelelőségi áttekintés lapról" border="false":::

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy beépített szabályzat-definíciót rendelt hozzá egy hatókörhöz, és kiértékelte a megfelelőségi jelentést. A házirend-definíció ellenőrzi, hogy a hatókör összes erőforrása megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)