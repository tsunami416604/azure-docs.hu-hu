---
title: Oktatóanyag – Címkék hozzáadása a sablon erőforrásaihoz
description: Címkék hozzáadása a Azure Resource Manager-sablonban üzembe helyezett erőforrásokhoz. Címkék lehetővé teszik az erőforrások logikai rendszerezését.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3e0deb53e57cd29cbfce4c37f2d6c6729f15bebd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80411707"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Oktatóanyag: Címkék hozzáadása az ARM-sablonhoz

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá címkéket a Azure Resource Manager-(ARM-) sablon erőforrásaihoz. A [címkék](../management/tag-resources.md) segítségével logikailag rendszerezheti az erőforrásokat. A címke értékei a Cost-jelentésekben jelennek meg. Az oktatóanyag elvégzése **8 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a gyors üzembe helyezési [sablonokkal kapcsolatos oktatóanyagot](template-tutorial-quickstart-template.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző sablon üzembe helyezte a Storage-fiókot, a App Service-csomagot és a webalkalmazást.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Az erőforrások telepítése után lehetséges, hogy nyomon kell követnie a költségeket, és meg kell keresnie egy kategóriához tartozó erőforrásokat. A problémák megoldásához címkéket adhat hozzá.

## <a name="add-tags"></a>Címkék hozzáadása

Az erőforrások címkézésével olyan értékeket adhat hozzá, amelyek segítenek azonosítani a használatukat. Hozzáadhat például olyan címkéket, amelyek felsorolják a környezetet és a projektet. Hozzáadhat olyan címkéket, amelyek azonosítják a Cost centert vagy az erőforrást birtokló csapatot. Adjon hozzá olyan értékeket, amelyek ésszerűek a szervezet számára.

A következő példa kiemeli a sablon módosításait. Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

Itt az ideje, hogy üzembe helyezi a sablont, és tekintse meg az eredményeket.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

Az üzembe helyezési parancs futtatásához az Azure CLI [legújabb verziójára](/cli/azure/install-azure-cli) van szükség.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Ha az üzemelő példány nem sikerült, a hibakeresési naplók megjelenítéséhez használja a **Debug** kapcsolót a telepítési paranccsal.  A teljes hibakeresési naplók megjelenítéséhez használhatja a **részletes** kapcsolót is.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Válasszon ki egy erőforrást, például a Storage-fiók erőforrását. Láthatja, hogy most már rendelkezik címkékkel.

   ![Címkék megjelenítése](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban címkéket adott hozzá az erőforrásokhoz. A következő oktatóanyagban megtudhatja, hogyan használhatók a paraméterek a sablonban az értékek átadásának egyszerűbbé tételéhez.

> [!div class="nextstepaction"]
> [Paraméter fájljának használata](template-tutorial-use-parameter-file.md)
