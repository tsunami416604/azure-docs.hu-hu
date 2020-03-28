---
title: Oktatóanyag - címkék hozzáadása az erőforrásokhoz a sablonban
description: Címkék hozzáadása az Azure Resource Manager-sablonban üzembe helyezett erőforrásokhoz. A címkék segítségével logikusan rendszerezheti az erőforrásokat.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 748a32d0ea8bfb0f23a99ce99d0aaf051118bc19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369883"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Oktatóanyag: Címkék hozzáadása az ARM-sablonhoz

Ebben az oktatóanyagban megtudhatja, hogyan adhat hozzá címkéket az erőforrásokhoz az Azure Resource Manager (ARM) sablonban. [A címkék](../management/tag-resources.md) segítségével logikusan rendszerezheti az erőforrásokat. A címkeértékek megjelennek a költségjelentésekben. Ez az oktatóanyag **8 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [oktatóanyagot](template-tutorial-quickstart-template.md)a rövid útmutató sablonokról, de ez nem szükséges.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

Az előző sablon üzembe helyezett egy tárfiókot, az App Service-csomagot és a webalkalmazást.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Az erőforrások üzembe helyezése után előfordulhat, hogy nyomon kell követnie a költségeket, és meg kell találnia a kategóriába tartozó erőforrásokat. A problémák megoldásához címkéket adhat hozzá.

## <a name="add-tags"></a>Címkék hozzáadása

Az erőforrások címkézése olyan értékek hozzáadásához, amelyek segítenek a használatuk azonosításában. Hozzáadhat például olyan címkéket, amelyek a környezetet és a projektet sorolják fel. Hozzáadhat olyan címkéket, amelyek azonosítják a költségközpontot vagy az erőforrást birtokló csoportot. Adja meg a szervezet számára érthető értékeket.

A következő példa kiemeli a sablon módosításait. Másolja a teljes fájlt, és cserélje le a sablontartalmát.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

Itt az ideje, hogy telepítse a sablont, és nézd meg az eredményeket.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést az Azure Portalon található erőforráscsoport feltárásával ellenőrizheti.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelybe telepítette.
1. Válassza ki az egyik erőforrást, például a tárfiók erőforrás. Látod, hogy most már címkéket.

   ![Címkék megjelenítése](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban címkéket adott hozzá az erőforrásokhoz. A következő oktatóanyagból megtudhatja, hogyan használhatja a paraméterfájlokat az értékek sablonba való átadásának egyszerűsítésére.

> [!div class="nextstepaction"]
> [Paraméterfájl használata](template-tutorial-use-parameter-file.md)
