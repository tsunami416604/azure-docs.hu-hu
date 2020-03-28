---
title: Oktatóanyag - sablonfüggvények hozzáadása
description: Sablonfüggvények hozzáadása az Azure Resource Manager-sablonhoz értékek létrehozásához.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6bb95d5a8022c6ac94dd8b7c777c65ff5e20617a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369916"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Oktatóanyag: Sablonfüggvények hozzáadása az ARM-sablonhoz

Ebben az oktatóanyagban megtudhatja, hogyan adhat [hozzá sablonfüggvényeket](template-functions.md) az Azure Resource Manager (ARM) sablonhoz. A függvények segítségével dinamikusan hozhat létre értékeket. A rendszer által biztosított sablonfüggvények mellett [felhasználó által definiált függvényeket](./template-user-defined-functions.md)is létrehozhat. Ez az oktatóanyag **7 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [oktatóanyagot a paraméterekről,](template-tutorial-add-parameters.md)de ez nem szükséges.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

Végén az előző bemutató, a sablon volt a következő JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

A tárolófiók helye az **USA keleti része.** Előfordulhat azonban, hogy a tárfiókot más régiókba kell telepítenie. Ismét a sablon rugalmasságának hiányával kell szembenéznie. Hozzáadhat egy paramétert a helyhez, de nagyszerű lenne, ha az alapértelmezett érték több értelme lenne, mint egy kódolt érték.

## <a name="use-function"></a>Függvény használata

Ha befejezte az előző oktatóanyag ebben a sorozatban, már használt egy függvényt. A **"[parameters('storageName')"** hozzáadásakor a [paraméterek függvényt](template-functions-deployment.md#parameters) használta. A szögletes zárójelek azt jelzik, hogy a szögletes zárójeleken belüli szintaxis egy [sablonkifejezés.](template-expressions.md) Az Erőforrás-kezelő a szintaxist nem literális értékként oldja meg.

A függvények rugalmasságot biztosítanak a sablonhoz azáltal, hogy dinamikusan kapják meg az értékeket az üzembe helyezés során. Ebben az oktatóanyagban egy függvény t, hogy lekéri a helyét az erőforráscsoport központi telepítéshez használt.

A következő példa kiemeli a **hely**nevű paraméter hozzáadásának módosításait.  A paraméter alapértelmezett értéke meghívja a [resourceGroup](template-functions-resource.md#resourcegroup) függvényt. Ez a függvény egy olyan objektumot ad vissza, amely a központi telepítéshez használt erőforráscsoportadatait tartalmazza. Az objektum egyik tulajdonsága egy helytulajdonság. Az alapértelmezett érték használatakor a tárfiók helye megegyezik az erőforráscsoporttal. Az erőforráscsoporton belüli erőforrásoknak nem kell ugyanazon a helyen vannak megosztva. Szükség esetén más helyet is megadhat.

Másolja a teljes fájlt, és cserélje le a sablontartalmát.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

Az előző oktatóanyagokban létrehozott egy tárfiókot az USA keleti részén, de az erőforráscsoport az USA középső részén jött létre. Ebben az oktatóanyagban a tárfiók ugyanabban a régióban jön létre, mint az erőforráscsoport. Használja az alapértelmezett értéket a helyhez, így nem kell megadnia a paraméter értékét. Meg kell adnia egy új nevet a tárfióknak, mert egy másik helyen hoz létre tárfiókot. Például a **store2-t** használja előtagként a **store1**helyett.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést az Azure Portalon található erőforráscsoport feltárásával ellenőrizheti.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelybe telepítette.
1. Láthatja, hogy egy tárfiók-erőforrás telepítve van, és ugyanaz a hely, mint az erőforráscsoport.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy függvényt használt egy paraméter alapértelmezett értékének meghatározásakor. Ebben az oktatóanyag-sorozatban továbbra is használja a függvényeket. A sorozat végére függvényeket adhat hozzá a sablon minden szakaszához.

> [!div class="nextstepaction"]
> [Változók hozzáadása](template-tutorial-add-variables.md)
