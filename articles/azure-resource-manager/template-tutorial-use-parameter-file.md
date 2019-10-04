---
title: Oktatóanyag – Azure Resource Manager sablon üzembe helyezéséhez használható paraméter-fájl használata
description: Használjon olyan paramétereket, amelyek tartalmazzák a Azure Resource Manager-sablon üzembe helyezéséhez használandó értékeket.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0d06006e72405a53361d0551cf773488ec809762
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963820"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Oktatóanyag: Paraméterek használata a Resource Manager-sablon üzembe helyezéséhez

Ebből az oktatóanyagból megtudhatja, hogyan használhatók a [paraméter-fájlok](resource-manager-parameter-files.md) az üzembe helyezés során beadott értékek tárolására. Az előző oktatóanyagokban beágyazott paramétereket használt a telepítési paranccsal. Ez a megközelítés a sablon tesztelésére szolgál, de a központi telepítések automatizálásakor könnyebb lehet átadni a környezete értékeit. A paraméter-fájlok megkönnyítik egy adott környezet paramétereinek értékének becsomagolását. Ebben az oktatóanyagban paramétereket hozhat létre fejlesztési és éles környezetekhez. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [címkékre vonatkozó oktatóanyagot](template-tutorial-add-tags.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>A sablon áttekintése

A sablon számos, az üzembe helyezés során megadható paramétert tartalmaz. Az előző oktatóanyag végén a sablon a következőképpen néz ki:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Ez a sablon jól működik, de most egyszerűen kezelheti a sablonhoz megadott paramétereket.

## <a name="add-parameter-files"></a>Paraméter-fájlok hozzáadása

A paraméter fájljai a sablonhoz hasonló struktúrával rendelkező JSON-fájlok. A fájlban adja meg az üzembe helyezés során átadni kívánt paramétereket.

A VS Code-ban hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt a **azuredeploy. Parameters. dev. JSON**néven.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Ez a fájl a fejlesztési környezethez tartozó paraméter-fájl. Figyelje meg, hogy a Standard_LRS használja a Storage-fiókhoz, az erőforrásokat pedig **fejlesztői** előtaggal látja el, és beállítja a **környezeti** címkét a **dev**értékre.

Ismét hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt a **azuredeploy. Parameters. prod. JSON**néven.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Ez a fájl az éles környezethez tartozó paraméter-fájl. Figyelje meg, hogy az Standard_GRS-t használ a Storage-fiókhoz, megnevezi az erőforrásokat egy **contoso** -előtaggal, és beállítja a környezeti címkét az **éles** **környezetben** . Valós éles környezetben érdemes lehet olyan app Service-t használni, amely nem ingyenes, de továbbra is ezt az SKU-t fogjuk használni ehhez az oktatóanyaghoz.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell-t.

A sablon utolsó tesztje hozzon létre két új erőforráscsoportot. Az egyik a fejlesztői környezethez és az éles környezetben.

Először üzembe kell helyezni a fejlesztői környezetet.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters @azuredeploy.parameters.dev.json
```

---

Most üzembe helyezzük az éles környezetben.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters @azuredeploy.parameters.prod.json
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.
1. Ekkor megjelenik az oktatóanyagban üzembe helyezett két új erőforráscsoport.
1. Válassza ki az erőforráscsoportot, és tekintse meg a telepített erőforrásokat. Figyelje meg, hogy az adott környezethez tartozó paraméterben megadott értékeknek felelnek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét. Ha elvégezte ezt a sorozatot, három erőforráscsoport törölhető – myResourceGroup, myResourceGroupDev és myResourceGroupProd.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Gratulálunk, készen áll a sablonok Azure-ba történő üzembe helyezésének bevezetésére. Tudassa velünk, ha megjegyzésekkel és javaslatokkal rendelkezik a visszajelzések szakaszban. Köszönjük!

Készen áll a sablonokkal kapcsolatos speciális fogalmak beugrására. A következő oktatóanyag részletesen ismerteti a sablon-referenciák dokumentációjának használatát, amely segítséget nyújt a telepítendő erőforrások definiálásához.

> [!div class="nextstepaction"]
> [A sablonreferencia felhasználása](resource-manager-tutorial-create-encrypted-storage-accounts.md)