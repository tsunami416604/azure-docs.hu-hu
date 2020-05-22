---
title: 'Rövid útmutató: új szabályzat-hozzárendelés sablonokkal'
description: Ebben a rövid útmutatóban egy Resource Manager-sablonnal hozhat létre szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 05/21/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 7b9e372e8b86e1ac5b0a99d0aba77982896b4d11
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83757449"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához Resource Manager-sablon használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést. A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és a _felügyelt lemezeket nem használó, naplózási virtuális gépek_nevű beépített szabályzat-definíciót rendel hozzá. Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](./samples/index.md).

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)származik.

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-30" highlight="20-28":::

A sablonban definiált erőforrás:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

> [!NOTE]
> Azure Policy szolgáltatás ingyenes. További információ: [Azure Policy áttekintése](./overview.md).

1. Az alábbi képre kattintva jelentkezzen be az Azure Portalra, és nyissa meg a sablont:

   [![A házirend-sablon üzembe helyezése az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket:

   | Name | Érték |
   |------|-------|
   | Előfizetés | Válassza ki az Azure-előfizetését. |
   | Erőforráscsoport | Válassza az **új létrehozása**lehetőséget, adjon meg egy nevet, majd kattintson **az OK gombra**. A képernyőképen az erőforráscsoport neve _mypolicyquickstart \< dátum a MMDD \> RG-ban_. |
   | Hely | Válasszon régiót. Például az **USA középső**régiója. |
   | Szabályzat-hozzárendelés neve | Adja meg a szabályzat-hozzárendelés nevét. Ha szeretné, használhatja a szabályzat-definíció megjelenítését. Például a **felügyelt lemezeket nem használó virtuális gépek naplózása**. |
   | RG neve | Adja meg azt az erőforráscsoport-nevet, amelyhez hozzá szeretné rendelni a szabályzatot. Ebben a rövid útmutatóban használja az alapértelmezett **[resourceGroup (). name]** értéket. a **[resourceGroup ()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** egy olyan sablon-függvény, amely lekéri az erőforráscsoportot. |
   | Házirend-definíció azonosítója | **/Providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**megadásához. |
   | Elfogadom a fenti feltételeket és kikötéseket | Válassza |

1. Válassza a **Beszerzés** lehetőséget.

Néhány további erőforrás:

- További minták sablonjait az Azure rövid útmutató [sablonjában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)talál.
- A sablon hivatkozásának megtekintéséhez nyissa meg az [Azure-sablonok referenciáját](/azure/templates/microsoft.authorization/allversions).
- A Resource Manager-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](../../azure-resource-manager/management/overview.md)talál további információt.
- Az előfizetési szintű központi telepítés megismeréséhez tekintse meg [Az erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../../azure-resource-manager/templates/deploy-to-subscription.md)című témakört.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A lap bal oldalán kattintson a **megfelelőség** elemre. Ezután keresse meg **azokat a naplózási virtuális gépeket, amelyek nem használják a felügyelt lemezeken** létrehozott házirend-hozzárendelést.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="A szabályzatok megfelelőségének áttekintése oldal" border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, akkor a **nem megfelelő erőforrások**alatt jelennek meg.

További információt a [megfelelőség működéséről](./how-to/get-compliance-data.md#how-compliance-works)szóló témakörben talál.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

A létrehozott hozzárendelés eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott **Felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a felügyelt lemezek házirend-hozzárendelését **nem használó naplózási virtuális gépekre** , és válassza a **hozzárendelés törlése**lehetőséget.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Hozzárendelés törlése a megfelelőségi áttekintés lapról" border="false":::

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy beépített szabályzat-definíciót rendelt hozzá egy hatókörhöz, és kiértékelte a megfelelőségi jelentést. A házirend-definíció ellenőrzi, hogy a hatókör összes erőforrása megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)