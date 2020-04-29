---
title: Oktatóanyag – gyorsindítási sablonok használata
description: Ismerje meg, hogyan végezheti el a sablonok fejlesztését az Azure Gyorsindítás sablonjaival.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4b82e02ecc009e587b89d1fd151fd13f75a4bcf8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80408510"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Oktatóanyag: az Azure Gyorsindítás sablonjainak használata

Az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) a Közösség által biztosított sablonok tárháza. A sablonok fejlesztésében a sablonokat is használhatja. Ebben az oktatóanyagban megtalálja a webhely erőforrás-definícióját, és hozzáadja a saját sablonhoz. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy végezze el az [exportált sablonokkal kapcsolatos oktatóanyagot](template-tutorial-export-template.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json":::

Ez a sablon a Storage-fiókok és az App Service-csomagok üzembe helyezésére használható, de előfordulhat, hogy hozzá szeretne adni egy webhelyet. Az előre elkészített sablonok segítségével gyorsan felderítheti az erőforrások üzembe helyezéséhez szükséges JSON-t.

## <a name="find-template"></a>Sablon keresése

1. [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/) megnyitása
1. A **Search (keresés**) mezőbe írja be a **linuxos webalkalmazás üzembe helyezése**kifejezést.
1. Válassza ki a címet egy **alapszintű linuxos webalkalmazás üzembe helyezésével**. Ha nem találja meg a problémát, a [közvetlen hivatkozás](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)látható.
1. Válassza **a Tallózás lehetőséget a githubon**.
1. Válassza ki a **azuredeploy. JSON**fájlt.
1. Tekintse át a sablont. Különösen keresse meg az `Microsoft.Web/sites` erőforrást.

    ![Resource Manager-sablon – gyors üzembe helyezési webhely](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Meglévő sablon módosítása

A gyors üzembe helyezési sablon egyesítése a meglévő sablonnal:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json" range="1-108" highlight="32-45,49,85-100":::

A webalkalmazás nevének egyedinek kell lennie az Azure-ban. Ha meg szeretné akadályozni, hogy a rendszer duplikálja a neveket, a **webAppPortalName** változót a " **webAppPortalName": "[concat (parameters (" webAppName "),"-WebApp "]]** és **" webAppPortalName ":" [concat (parameters (' webAppName '), uniqueString (resourceGroup (). id)] "** értékre frissíti.

Adjon hozzá egy vesszőt a `Microsoft.Web/serverfarms` definíció végén, hogy elkülönítse az erőforrás- `Microsoft.Web/sites` definíciót a definícióból.

Ebben az új erőforrásban néhány fontos szempontot figyelembe kell venni.

Megfigyelheti, hogy egy **dependsOn** nevű elemet tartalmaz, amely az App Service-csomagra van beállítva. Erre a beállításra azért van szükség, mert az App Service-csomagnak léteznie kell a webalkalmazás létrehozása előtt. A **dependsOn** elem azt mutatja be, hogy a Resource Manager hogyan rendeli hozzá az erőforrásokat az üzembe helyezéshez.

A **serverFarmId** tulajdonság a [resourceId](template-functions-resource.md#resourceid) függvényt használja. Ez a függvény egy erőforrás egyedi azonosítóját kapja meg. Ebben az esetben az App Service-csomag egyedi azonosítóját kapja meg. A webalkalmazás egy adott app Service-csomaghoz van társítva.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell-t.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

Az üzembe helyezési parancs futtatásához az Azure CLI [legújabb verziójára](/cli/azure/install-azure-cli) van szükség.

```azurecli
az deployment group create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Ha az üzemelő példány nem sikerült, a hibakeresési naplók megjelenítéséhez használja a **Debug** kapcsolót a telepítési paranccsal.  A teljes hibakeresési naplók megjelenítéséhez használhatja a **részletes** kapcsolót is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan használhat egy rövid útmutató sablont a sablonok fejlesztéséhez. A következő oktatóanyagban címkéket adhat hozzá az erőforrásokhoz.

> [!div class="nextstepaction"]
> [Címkék hozzáadása](template-tutorial-add-tags.md)
