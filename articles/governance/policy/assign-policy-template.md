---
title: 'Rövid útmutató: új szabályzat-hozzárendelés sablonokkal'
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablon (ARM-sablon) használatával hozhat létre szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 05/21/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: f4cb4cb1fc56d06ab1e061b2d0e9a031e0e511dc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242049"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához ARM-sablon használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti egy Azure Resource Manager-sablon (ARM-sablon) használatának folyamatán a szabályzat-hozzárendelés létrehozásához a felügyelt lemezeket nem használó virtuális gépek azonosításához. A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonok használatát, válassza az **üzembe helyezés az Azure** -ban gombot. A sablon megnyílik a Azure Portalban.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Az ARM-sablon üzembe helyezése Azure Policy az Azure-ban való hozzárendeléséhez" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és a _felügyelt lemezeket nem használó, naplózási virtuális gépek_nevű beépített szabályzat-definíciót rendel hozzá. Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](./samples/index.md).

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)származik.

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-30" highlight="20-28":::

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
   | Szabályzat-hozzárendelés neve | Adja meg a szabályzat-hozzárendelés nevét. Ha szeretné, használhatja a szabályzat-definíció megjelenítését. Például a **felügyelt lemezeket nem használó virtuális gépek naplózása**. |
   | RG neve | Adja meg azt az erőforráscsoport-nevet, amelyhez hozzá szeretné rendelni a szabályzatot. Ebben a rövid útmutatóban használja az alapértelmezett **[resourceGroup (). name]** értéket. a **[resourceGroup ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** egy olyan sablon-függvény, amely lekéri az erőforráscsoportot. |
   | Házirend-definíció azonosítója | **/Providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**megadásához. |
   | Elfogadom a fenti feltételeket és kikötéseket | Válassza |

1. Válassza a **Vásárlás** lehetőséget.

Néhány további erőforrás:

- További minták sablonjait az Azure rövid útmutató [sablonjában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)talál.
- A sablon hivatkozásának megtekintéséhez nyissa meg az [Azure-sablonok referenciáját](/azure/templates/microsoft.authorization/allversions).
- Az ARM-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](../../azure-resource-manager/management/overview.md)talál további információt.
- Az előfizetési szintű központi telepítés megismeréséhez tekintse meg [Az erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../../azure-resource-manager/templates/deploy-to-subscription.md)című témakört.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A lap bal oldalán kattintson a **megfelelőség** elemre. Ezután keresse meg **azokat a naplózási virtuális gépeket, amelyek nem használják a felügyelt lemezeken** létrehozott házirend-hozzárendelést.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="A szabályzatok megfelelőségének áttekintése oldal" border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, akkor a **nem megfelelő erőforrások**alatt jelennek meg.

További információt a [megfelelőség működéséről](./how-to/get-compliance-data.md#how-compliance-works)szóló témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott **Felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a felügyelt lemezek házirend-hozzárendelését **nem használó naplózási virtuális gépekre** , és válassza a **hozzárendelés törlése**lehetőséget.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Hozzárendelés törlése a megfelelőségi áttekintés lapról" border="false":::

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy beépített szabályzat-definíciót rendelt hozzá egy hatókörhöz, és kiértékelte a megfelelőségi jelentést. A házirend-definíció ellenőrzi, hogy a hatókör összes erőforrása megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)