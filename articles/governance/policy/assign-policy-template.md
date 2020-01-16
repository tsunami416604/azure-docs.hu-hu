---
title: 'Rövid útmutató: új szabályzat-hozzárendelés sablonokkal'
description: Ebben a rövid útmutatóban egy Resource Manager-sablonnal hozhat létre szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 8b9b0024e5c15c78c6777b8657839791484d66b5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980507"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához Resource Manager-sablon használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és a _felügyelt lemezeket nem használó, naplózási virtuális gépek_nevű beépített szabályzat-definíciót rendel hozzá. Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](./samples/index.md).

A szabályzat-hozzárendelések létrehozásának számos módja van. Ebben a rövid útmutatóban egy rövid útmutató [sablont](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)használ.
A sablon egy másolata:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy szolgáltatás ingyenes. További információ: [Azure Policy áttekintése](./overview.md).

1. Válassza ki az alábbi rendszerképet, hogy bejelentkezzen a Azure Portalba, és nyissa meg a sablont:

   [![a házirend-sablon üzembe helyezése az Azure-ban](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket:

   | Név | Value (Díj) |
   |------|-------|
   | Előfizetés | Válassza ki az Azure-előfizetését. |
   | Erőforráscsoport | Válassza az **új létrehozása**lehetőséget, adjon meg egy nevet, majd kattintson **az OK gombra**. A képernyőképen az erőforráscsoport neve _mypolicyquickstart\<dátum a MMDD\>RG-ban_. |
   | Földrajzi egység | Válasszon régiót. Például az **USA középső**régiója. |
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

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

A lap bal oldalán kattintson a **megfelelőség** elemre. Ezután keresse meg **azokat a naplózási virtuális gépeket, amelyek nem használják a felügyelt lemezeken** létrehozott házirend-hozzárendelést.

![A szabályzatok megfelelőségének áttekintése oldal](./media/assign-policy-template/policy-compliance.png)

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, akkor a **nem megfelelő erőforrások**alatt jelennek meg.

További információt a [megfelelőség működéséről](./how-to/get-compliance-data.md#how-compliance-works)szóló témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott **Felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a felügyelt lemezek házirend-hozzárendelését **nem használó naplózási virtuális gépekre** , és válassza a **hozzárendelés törlése**lehetőséget.

   ![Hozzárendelés törlése a megfelelőségi áttekintés lapról](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy beépített szabályzat-definíciót rendelt hozzá egy hatókörhöz, és kiértékelte a megfelelőségi jelentést. A házirend-definíció ellenőrzi, hogy a hatókör összes erőforrása megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)