---
title: Oktatóanyag – sablon-függvények hozzáadása
description: Adja hozzá a Template functions-t a Azure Resource Manager-sablonhoz az értékek létrehozásához.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 03a38178ec66c1c1a10934975d20778369d80dbe
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405960"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Oktatóanyag: template functions hozzáadása a Resource Manager-sablonhoz

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá [sablon-függvényeket](resource-group-template-functions.md) a sablonhoz. A függvények használatával dinamikusan hozhat létre értékeket. A rendszer által biztosított sablon-függvények mellett létrehozhat [felhasználó által definiált függvényeket](./template-user-defined-functions.md)is. Az oktatóanyag elvégzése **7 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [paraméterekkel kapcsolatos oktatóanyagot](template-tutorial-add-parameters.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

A Storage-fiók helye az **USA keleti**régiójában rögzített. Előfordulhat azonban, hogy más régiókban is telepítenie kell a Storage-fiókot. A sablon problémája még nincs rugalmassága. Hozzáadhat egy paramétert a helyhez, de nagyszerű lenne, ha az alapértelmezett értéke több értelme, mint a nehezen kódolt érték.

## <a name="use-function"></a>Függvény használata

Ha befejezte az előző oktatóanyagot ebben a sorozatban, már használt egy függvényt. Ha hozzáadta a **"[parameters (' storageName ')]"** paramétert, a [Paraméterek](resource-group-template-functions-deployment.md#parameters) függvényt használta. A zárójelek azt jelzik, hogy a zárójelben lévő szintaxis a [sablon kifejezése](template-expressions.md). A Resource Manager úgy oldja fel a szintaxist, hogy literál értékként kezelje.

A függvények rugalmasságot biztosítanak a sablonhoz az értékek dinamikus beolvasásával az üzembe helyezés során. Ebben az oktatóanyagban egy függvény használatával beolvassa az üzembe helyezéshez használt erőforráscsoport helyét.

A következő példa kiemeli a **Location**nevű paraméter hozzáadásának módosításait.  A paraméter alapértelmezett értéke meghívja a [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) függvényt. Ez a függvény egy objektumot ad vissza, amely az üzembe helyezéshez használt erőforráscsoport adatait ismerteti. Az objektum egyik tulajdonsága a Location tulajdonság. Ha az alapértelmezett értéket használja, a Storage-fiók helye megegyezik az erőforráscsoport helyével. Az erőforráscsoporthoz tartozó erőforrásoknak nem kell ugyanazt a helyet megosztaniuk. Szükség esetén másik helyet is megadhat.

Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Sablon üzembe helyezése

Az előző oktatóanyagokban létrehozott egy Storage-fiókot az USA keleti régiójában, de az erőforráscsoport az USA középső régiójában lett létrehozva. Ebben az oktatóanyagban a Storage-fiók ugyanabban a régióban jön létre, mint az erőforráscsoport. Használja az alapértelmezett értéket a helyhez, így nem kell megadnia a paraméter értékét. Meg kell adnia egy új nevet a Storage-fiókhoz, mert egy másik helyen hoz létre egy Storage-fiókot. Használja például a **store2** előtagot a **store1**helyett.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Láthatja, hogy a Storage-fiók erőforrása telepítve van, és ugyanazzal a hellyel rendelkezik, mint az erőforráscsoport.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy függvényt használt egy paraméter alapértelmezett értékének definiálásához. Ebben az oktatóanyag-sorozatban továbbra is használhatja a functions funkciót. Az adatsorozat végére a sablon minden szakaszához hozzá kell adnia a függvényeket.

> [!div class="nextstepaction"]
> [Változók hozzáadása](template-tutorial-add-variables.md)
