---
title: Szabályzat-hozzárendelés létrehozása Resource Manager-sablonnal
description: Ez a cikk végigvezeti egy Resource Manager-sablon használatának lépésein a szabályzat-hozzárendelés létrehozásához a nem megfelelő erőforrások azonosításához.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 72d8fab39e8dd11cf46eb2977a9b9fe288ca2de1
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980806"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Gyors útmutató: Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához Resource Manager-sablon használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából *nem megfelelőnek* minősülnek.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és a *felügyelt lemezeket nem használó, naplózási virtuális gépek*nevű beépített szabályzat-definíciót rendel hozzá. Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](./samples/index.md).

A szabályzat-hozzárendelések létrehozásának számos módja van. Ebben a rövid útmutatóban egy rövid útmutató [sablont](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)használ.
A sablon egy másolata:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy szolgáltatás ingyenes.  További információ: [Azure Policy áttekintése](./overview.md).

1. Válassza ki az alábbi rendszerképet, hogy bejelentkezzen a Azure Portalba, és nyissa meg a sablont:

   [@no__t – 1Deploy az Azure-ba](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket:

   | Name (Név) | Value |
   |------|-------|
   | Subscription | Válassza ki az Azure-előfizetését. |
   | Resource group | Válassza az **új létrehozása**lehetőséget, adjon meg egy nevet, majd kattintson **az OK gombra**. A képernyőképen az erőforráscsoport neve *mypolicyquickstart @ no__t-1Date a MMDD > RG-ban*. |
   | Location | Válasszon régiót. Ha például **USA középső RÉGIÓJA**. |
   | Szabályzat-hozzárendelés neve | Adja meg a szabályzat-hozzárendelés nevét. Ha szeretné, használhatja a szabályzat-definíció megjelenítését. Például a **felügyelt lemezeket nem használó virtuális gépek naplózása**. |
   | RG neve | Adja meg azt az erőforráscsoport-nevet, amelyhez hozzá szeretné rendelni a szabályzatot. Ebben a rövid útmutatóban használja az alapértelmezett **[resourceGroup (). name]** értéket. a **[resourceGroup ()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** egy olyan sablon-függvény, amely lekéri az erőforráscsoportot. |
   | Házirend-definíció azonosítója | **/Providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**megadásához. |
   | Elfogadom a fenti feltételeket és kikötéseket | Válassza |

1. Válassza a **Beszerzés** lehetőséget.

Néhány további erőforrás:

- További minták sablonjait az Azure rövid útmutató [sablonjában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)talál.
- A sablon hivatkozásának megtekintéséhez nyissa meg az [Azure-sablonok referenciáját](/azure/templates/microsoft.authorization/allversions).
- A Resource Manager-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](/azure/azure-resource-manager/)talál további információt.
- Az előfizetési szintű központi telepítés megismeréséhez tekintse meg [Az erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../../azure-resource-manager/deploy-to-subscription.md)című témakört.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

Válassza ki **megfelelőségi** az oldal bal oldalán található. Keresse meg a **felügyelt lemezeket nem használó virtuális gépek naplózása** létrehozott szabályzat-hozzárendelést.

![A szabályzatok megfelelőségének áttekintése oldal](./media/assign-policy-template/policy-compliance.png)

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, azok jelennek meg **nem megfelelő erőforrások**.

További információt a [megfelelőség működéséről](./how-to/get-compliance-data.md#how-compliance-works)szóló témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelést eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott **Felügyelt lemezeket nem használó virtuális gépek naplózása** szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a felügyelt lemezek házirend-hozzárendelését **nem használó naplózási virtuális gépekre** , és válassza a **hozzárendelés törlése**lehetőséget.

   ![Hozzárendelés törlése a megfelelőségi áttekintés lapról](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy beépített szabályzat-definíciót rendelt hozzá egy hatókörhöz, és kiértékelte a megfelelőségi jelentést. A szabályzatdefiníció ellenőrzi, hogy megfelelő a hatókörben lévő összes erőforrást, és azonosítja a nem.

További információ a szabályzatok ellenőrzése, hogy az új erőforrások megfelelnek hozzárendeléséről, folytassa a következő oktatóanyagban:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)