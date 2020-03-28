---
title: Oktatóanyag – paraméterfájl használata sablon telepítéséhez
description: Használjon paraméterfájlokat, amelyek tartalmazzák az Azure Resource Manager-sablon üzembe helyezéséhez használt értékeket.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0f7b966d4241716d71779e966de5d408711e4543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371781"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Oktatóanyag: Paraméterfájlok használata az ARM-sablon telepítéséhez

Ebben az oktatóanyagban megtudhatja, hogyan [használhatja a paraméterfájlokat](parameter-files.md) a központi telepítés során megadott értékek tárolására. Az előző oktatóanyagokban a központi telepítési parancsbeépített paramétereket használt. Ez a megközelítés az Azure Resource Manager (ARM) sablon teszteléséhez működött, de a központi telepítések automatizálása során egyszerűbb lehet értékeket átadni a környezetben. A paraméterfájlok megkönnyítik egy adott környezet paraméterértékeinek csomagolását. Ebben az oktatóanyagban paraméterfájlokat hozhat létre fejlesztési és éles környezetekben. Körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [oktatóanyagot a címkékről,](template-tutorial-add-tags.md)de ez nem szükséges.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

A sablon számos paramétert biztosít a telepítés során. Végén az előző bemutató, a sablon nézett ki:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Ez a sablon jól működik, de most egyszerűen szeretné kezelni a sablonhoz megadott paramétereket.

## <a name="add-parameter-files"></a>Paraméterfájlok hozzáadása

A paraméterfájlok Olyan JSON fájlok, amelyek szerkezete hasonló a sablonhoz. A fájlban adja meg az üzembe helyezés során átadni kívánt paraméterértékeket.

A VS-kód ban hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt az **azuredeploy.parameters.dev.json**névvel.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Ez a fájl a fejlesztői környezet paraméterfájlja. Figyelje meg, hogy Standard_LRS használ a tárfiókhoz, **megnevezi** az erőforrásokat egy fejlesztési előtaggal, és a **környezeti** címkét **Fejlesztői**értékre állítja.

Ismét hozzon létre egy új fájlt a következő tartalommal. Mentse a fájlt az **azuredeploy.parameters.prod.json**névvel.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Ez a fájl az éles környezet paraméterfájlja. Figyelje meg, hogy Standard_GRS használ a tárfiókhoz, megnevezi az erőforrásokat **egy contoso** előtaggal, és a **környezeti** címkét **élesre állítja.** Egy valós éles környezetben is szeretné használni egy alkalmazásszolgáltatás egy termékváltozat nem ingyenes, de továbbra is ezt a termékváltozatot ebben az oktatóanyagban továbbra is használni.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Használja az Azure CLI vagy az Azure PowerShell a sablon üzembe helyezéséhez.

A sablon utolsó tesztjeként hozzon létre két új erőforráscsoportot. Az egyik a fejlesztői környezet, a ztán a fejlesztői környezet.

Először is üzembe helyezünk a fejlesztői környezetben.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Most pedig az éles környezetbe telepítjük.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést az Azure Portalon található erőforráscsoportok feltárásával ellenőrizheti.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
1. Az oktatóanyagban üzembe helyezett két új erőforráscsoport látható.
1. Válassza ki bármelyik erőforráscsoportot, és tekintse meg az üzembe helyezett erőforrásokat. Figyelje meg, hogy megegyeznek az adott környezetben megadott értékekkel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét. Ha befejezte ezt a sorozatot, három erőforráscsoportot kell törölnie : myResourceGroup, myResourceGroupDev és myResourceGroupProd.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Gratulálunk, befejezte ezt a bevezetést a sablonok azure-ba való üzembe helyezéséhez. Tudassa velünk, ha bármilyen észrevétele és javaslata van a visszajelzés részben. Köszönjük!

A következő oktatóanyag-sorozat részletesebben ismerteti a sablonok üzembe helyezését.

> [!div class="nextstepaction"]
> [Helyi sablon telepítése](./deployment-tutorial-local-template.md)
