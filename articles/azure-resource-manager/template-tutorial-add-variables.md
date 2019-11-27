---
title: Oktatóanyag – változó hozzáadása a sablonhoz
description: Adja hozzá a változókat a Azure Resource Manager-sablonhoz a szintaxis egyszerűsítése érdekében.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0598da3c060b8a8055ffb045fe4aae60b3806060
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406054"
---
# <a name="tutorial-add-variables-to-your-resource-manager-template"></a>Oktatóanyag: változók hozzáadása a Resource Manager-sablonhoz

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá változót a sablonhoz. A változók leegyszerűsítik a sablonokat, mivel lehetővé teszi egy kifejezés egyszeri megírását, és az egész sablonban való felhasználását. Az oktatóanyag elvégzése **7 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [functions oktatóanyagot](template-tutorial-add-functions.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json)]

A Storage-fiók nevének paramétere nem használható, mert egyedi nevet kell megadni. Ha elvégezte a sorozat korábbi oktatóanyagait, akkor valószínűleg egy egyedi nevet is kitalál. Ezt a problémát úgy oldja meg, hogy hozzáad egy változót, amely egyedi nevet hoz létre a Storage-fiókhoz.

## <a name="use-variable"></a>Változó használata

A következő példa kiemeli azokat a módosításokat, amelyek segítségével egy változót adhat hozzá a sablonhoz, amely létrehoz egy egyedi Storage-fiók nevét. Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json?range=1-47&highlight=5-9,29-31,36)]

Figyelje meg, hogy tartalmaz egy **uniqueStorageName**nevű változót. Ez a változó négy függvényt használ egy karakterlánc-érték létrehozásához.

Már ismeri a [Parameters](resource-group-template-functions-deployment.md#parameters) függvényt, ezért nem vizsgáljuk meg.

A [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) függvényt is ismeri. Ebben az esetben az **ID** tulajdonságot a **Location** tulajdonság helyett az előző oktatóanyagban látható módon kapja meg. Az **ID** tulajdonság az erőforráscsoport teljes azonosítóját adja vissza, beleértve az előfizetés-azonosítót és az erőforráscsoport nevét.

A [uniqueString](resource-group-template-functions-string.md#uniquestring) függvény egy 13 karakterből álló kivonatoló értéket hoz létre. A visszaadott értéket a megadott paraméterek határozzák meg. Ebben az oktatóanyagban az erőforráscsoport AZONOSÍTÓját használja a kivonat értékének bemenetként. Ez azt jelenti, hogy ezt a sablont különböző erőforráscsoporthoz helyezheti üzembe, és egy másik egyedi karakterláncot is beszerezhet. Ugyanakkor ugyanezt az értéket kell megkapnia, ha ugyanarra az erőforrás-csoportra telepíti.

A [concat](resource-group-template-functions-string.md#concat) függvény értékeket vesz fel, és egyesíti azokat. Ehhez a változóhoz a karakterláncot a paraméterből és a karakterláncot a uniqueString függvényből veszi át, és egyetlen karakterlánccá egyesíti őket.

A **storagePrefix** paraméter lehetővé teszi, hogy olyan előtagot adjon át, amely segítséget nyújt a Storage-fiókok azonosításában. Létrehozhat egy saját elnevezési konvenciót, amely megkönnyíti a tárolási fiókok azonosítását az erőforrások hosszú listájáról való üzembe helyezés után.

Végezetül figyelje meg, hogy a tároló neve már egy paraméter helyett a változóra van beállítva.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Végezzük el a sablon üzembe helyezését. A sablon üzembe helyezése egyszerűbb, mint az előző sablonok, mert csak a tároló nevének előtagját adja meg.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Láthatja, hogy a Storage-fiók erőforrása telepítve van. A Storage-fiók **neve és egy** véletlenszerű karakterből álló karakterlánc.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy olyan változót adott hozzá, amely egyedi nevet hoz létre egy Storage-fiókhoz. A következő oktatóanyagban egy értéket ad vissza a központilag telepített Storage-fiókból.

> [!div class="nextstepaction"]
> [Kimenetek hozzáadása](template-tutorial-add-outputs.md)
