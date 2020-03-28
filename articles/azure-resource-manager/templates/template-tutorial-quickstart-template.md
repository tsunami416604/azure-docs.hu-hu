---
title: Oktatóanyag – Rövid útmutatósablonok használata
description: Ismerje meg, hogyan használhatja az Azure gyorsindítási sablonjait a sablonfejlesztés befejezéséhez.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8747a4614cb8106ce80c6caef0aae36111d2c384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369853"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Oktatóanyag: Azure-gyorsindítási sablonok használata

[Az Azure quickstart-sablonjai](https://azure.microsoft.com/resources/templates/) a közösség által készített sablonok tárháza. A mintasablonokat használhatja a sablonfejlesztésben. Ebben az oktatóanyagban megtalálja a webhely erőforrás-definícióját, és hozzáadhatja a saját sablonhoz. Körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [exportált sablonokról szóló oktatóanyagot,](template-tutorial-export-template.md)de ez nem kötelező.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

Végén az előző bemutató, a sablon volt a következő JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Ez a sablon a tárfiókok és az alkalmazásszolgáltatási csomagok üzembe helyezéséhez működik, de érdemes lehet hozzáadni egy webhelyet. Az előre elkészített sablonok segítségével gyorsan felderítheti az erőforrás üzembe helyezéséhez szükséges JSON-t.

## <a name="find-template"></a>Sablon keresése

1. [Az Azure gyorsindítási sablonjainak megnyitása](https://azure.microsoft.com/resources/templates/)
1. A **Keresés mezőbe**írja be a linux **webalkalmazás telepítését.**
1. Válassza ki azt a címet, amelynek címe **Egyszerű Linux webalkalmazás telepítése**. Ha nehezen találja meg, itt a [közvetlen kapcsolat](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Válassza **a Tallózás lehetőséget a GitHubon.**
1. Válassza **az azuredeploy.json lehetőséget.**
1. Tekintse át a sablont. Különösen keresse meg `Microsoft.Web/sites` az erőforrást.

    ![Az Erőforrás-kezelő sablon rövid útmutatówebhelye](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Meglévő sablon felülvizsgálata

A rövid útmutató sablon egyesítése a meglévő sablonnal:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

A webalkalmazás nevének egyedinek kell lennie az Azure-ban. Az ismétlődő nevek elkerülése érdekében a **webAppPortalName** változó **a "webAppPortalName" értékről frissült: "[concat(parameters('webAppName'), '-webApp)]"** és **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup(.id)]]"**.

Vessző hozzáadása a definíció végén `Microsoft.Web/serverfarms` az erőforrás-definíció és `Microsoft.Web/sites` a definíció elkülönítéséhez.

Van néhány fontos jellemzője, hogy vegye figyelembe az új erőforrás.

Észre fogod venni, hogy van egy elem neve **dependsOn,** amely be van állítva, hogy az app service-csomag. Erre a beállításra azért van szükség, mert az alkalmazásszolgáltatási csomagnak léteznie kell a webalkalmazás létrehozása előtt. A **dependsOn** elem megmondja az Erőforrás-kezelőnek, hogyan rendelje meg az erőforrásokat a központi telepítéshez.

A **serverFarmId** tulajdonság a [resourceId függvényt](template-functions-resource.md#resourceid) használja. Ez a függvény lekéri egy erőforrás egyedi azonosítóját. Ebben az esetben az alkalmazásszolgáltatási csomag egyedi azonosítóját kapja meg. A webalkalmazás egy adott alkalmazásszolgáltatási csomaghoz van társítva.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Használja az Azure CLI vagy az Azure PowerShell egy sablon üzembe helyezéséhez.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan használhat rövid útmutató sablont a sablon fejlesztéséhez. A következő oktatóanyagban címkéket adhat az erőforrásokhoz.

> [!div class="nextstepaction"]
> [Címkék hozzáadása](template-tutorial-add-tags.md)
