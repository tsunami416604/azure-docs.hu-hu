---
title: 'Rövid útmutató: Új házirend-hozzárendelés sablonokkal'
description: Ebben a rövid útmutatóban egy Erőforrás-kezelő sablon használatával hozzon létre egy házirend-hozzárendelést a nem megfelelő erőforrások azonosítására.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 9f9998c407c39d11615a5997549a363a276b9e10
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471404"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Rövid útmutató: Házirend-hozzárendelés létrehozása a nem megfelelő erőforrások azonosítására erőforrás-kezelő sablon használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést. A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban létrehoz egy házirend-hozzárendelést, és hozzárendel egy beépített házirend-definíciót, _amelyet felügyelt lemezeket nem használó naplózó virtuális gépeknek neveznek._ Az elérhető beépített szabályzatok részleges listáját az [Azure Policy-minták című témakörben található.](./samples/index.md)

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

A sablonban definiált erőforrás a következő:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

> [!NOTE]
> Az Azure Policy szolgáltatás ingyenes. További információt [az Azure-szabályzat áttekintése című témakörben talál.](./overview.md)

1. Válassza ki az alábbi képet az Azure Portalra való bejelentkezéshez, és nyissa meg a sablont:

   [![A házirendsablon üzembe helyezése az Azure-ban](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Jelölje be vagy írja be a következő értékeket:

   | Név | Érték |
   |------|-------|
   | Előfizetés | Válassza ki az Azure-előfizetését. |
   | Erőforráscsoport | Válassza **az Új létrehozása**lehetőséget, adjon meg egy nevet, majd kattintson az OK **gombra.** A képernyőképen az erőforráscsoport neve _mypolicyquickstart\<dátum\>mmDD rg_. |
   | Hely | Válasszon régiót. Például **az USA középső régiója**. |
   | Házirend-hozzárendelés neve | Adja meg a házirend-hozzárendelés nevét. A házirend-definíció megjelenítését tetszés szerint használhatja. Például **a felügyelt lemezeket nem használó virtuális gépek naplózása.** |
   | Rg név | Adja meg azt az erőforráscsoport nevet, amelyhez a házirendet hozzá kívánja rendelni. Ebben a rövid útmutatóban használja az alapértelmezett értéket **[resourceGroup(.name]**. **[ResourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** egy sablon függvény, amely beolvassa az erőforráscsoportot. |
   | Házirend-definíció azonosítója | Adja meg **a /providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a kapcsolót.** |
   | Elfogadom a fent említett feltételeket | (Kijelölés) |

1. Válassza a **Beszerzés** lehetőséget.

Néhány további forrás:

- További mintasablonokat az [Azure gyorsindítási sablonjában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)talál.
- A sablonhivatkozás megtekintéséhez nyissa meg az [Azure-sablon hivatkozását.](/azure/templates/microsoft.authorization/allversions)
- Az Erőforrás-kezelő-sablonok fejlesztéséről az [Azure Resource Manager dokumentációjában](../../azure-resource-manager/management/overview.md)olvashat.
- Az előfizetésszintű telepítésről az [Erőforráscsoportok és -erőforrások létrehozása az előfizetés szintjén című témakörben](../../azure-resource-manager/templates/deploy-to-subscription.md)olvashat.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Válassza a **Megfelelőség** lehetőséget a lap bal oldalán. Ezután keresse meg a felügyelt lemezek házirend-hozzárendelését **nem használó virtuális gépek naplózása.**

![Házirend-megfelelőség – áttekintés lap](./media/assign-policy-template/policy-compliance.png)

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, azok a **Nem megfelelő erőforrások**területen jelennek meg.

További információ: [Hogyan működik a megfelelőség.](./how-to/get-compliance-data.md#how-compliance-works)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához hajtsa végre az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott **Felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a **felügyelt lemezházirend-hozzárendelést nem használó virtuális gépek naplózására,** és válassza **a Hozzárendelés törlése parancsot.**

   ![Hozzárendelés törlése a megfelelőségi áttekintés lapról](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy beépített szabályzatdefiníciót rendelt egy hatókörhöz, és értékelte a megfelelőségi jelentést. A szabályzatdefiníció ellenőrzi, hogy a hatókörben lévő összes erőforrás megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni arról, hogy milyen házirendeket szeretne hozzárendelni az új erőforrások megfelelősének ellenőrzéséhez, folytassa az oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)