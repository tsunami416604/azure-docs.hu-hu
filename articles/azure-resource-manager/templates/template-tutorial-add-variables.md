---
title: Oktatóanyag - változó hozzáadása a sablonhoz
description: A szintaxis egyszerűsítése érdekében adja hozzá a változókat az Azure Resource Manager-sablonhoz.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b1df86e5b593edec784de21e21a4399274d820bb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411683"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Oktatóanyag: Változók hozzáadása az ARM-sablonhoz

Ebben az oktatóanyagban megtudhatja, hogyan adhat hozzá egy változót az Azure Resource Manager (ARM) sablonhoz. A változók leegyszerűsítik a sablonokat, mivel lehetővé teszik egy kifejezés egyszeri írását és újrafelhasználását a sablonban. Ez az oktatóanyag **7 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [oktatóanyagot](template-tutorial-add-functions.md)a függvényekről , de ez nem szükséges.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

Végén az előző bemutató, a sablon volt a következő JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

A tárfiók nevének paramétere nehezen használható, mert egyedi nevet kell megadnia. Ha befejezte a sorozat korábbi oktatóanyagait, akkor valószínűleg belefáradt az egyedi név kitalálásába. A problémát úgy oldhatja meg, hogy hozzáad egy változót, amely egyedi nevet hoz létre a tárfiókhoz.

## <a name="use-variable"></a>Változó használata

A következő példa kiemeli a módosításokat, hogy egy változót adjon a sablonhoz, amely egyedi tárfiók nevet hoz létre. Másolja a teljes fájlt, és cserélje le a sablontartalmát.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Figyelje meg, hogy tartalmaz egy **uniqueStorageName nevű változót.** Ez a változó négy függvényt használ egy karakterláncérték létrehozásához.

Már ismeri a [paraméterek](template-functions-deployment.md#parameters) funkciót, ezért nem vizsgáljuk meg.

Ismeri az [erőforráscsoport](template-functions-resource.md#resourcegroup) funkciót is. Ebben az esetben a **helytulajdonság** helyett az **id** tulajdonságot kapja, ahogy az az előző oktatóanyagban is látható. Az **id** tulajdonság az erőforráscsoport teljes azonosítóját adja vissza, beleértve az előfizetés-azonosítót és az erőforráscsoport nevét.

Az [uniqueString](template-functions-string.md#uniquestring) függvény 13 karakteres kivonatértéket hoz létre. A visszaadott értéket a megadott paraméterek határozzák meg. Ebben az oktatóanyagban az erőforráscsoport-azonosítót használja a kivonatérték bemeneteként. Ez azt jelenti, hogy ezt a sablont különböző erőforráscsoportokba helyezheti üzembe, és egy másik egyedi karakterlánc-értéket kaphat. Azonban ugyanazt az értéket kapja, ha ugyanabba az erőforráscsoportba telepíti.

A [concat](template-functions-string.md#concat) függvény értékeket vesz fel, és egyesíti azokat. Ehhez a változóhoz a paraméterből és a uniqueString függvényből származó karakterláncból veszi a karakterláncot, és egyetlen karakterláncba egyesíti őket.

A **storagePrefix** paraméter lehetővé teszi, hogy adja át egy előtag, amely segít azonosítani a tárfiókok. Létrehozhat saját elnevezési konvenciót, amely megkönnyíti a tárfiókok azonosítását az erőforrások hosszú listájából történő üzembe helyezés után.

Végül figyelje meg, hogy a tároló neve most paraméter helyett a változóra van állítva.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Telepítsük a sablont. A sablon telepítése egyszerűbb, mint az előző sablonok, mert csak a tároló név előtagja.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, használja a **hibakeresési kapcsolót** a központi telepítési paranccsal a hibakeresési naplók megjelenítéséhez.  A **részletes** kapcsolóval is megjelenítheti a teljes hibakeresési naplókat.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést az Azure Portalon található erőforráscsoport feltárásával ellenőrizheti.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelybe telepítette.
1. Láthatja, hogy egy tárfiók-erőforrás telepítve van. A tárfiók neve **tároló,** valamint egy sor véletlenszerű karakter.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban hozzáadott egy változót, amely létrehoz egy egyedi nevet egy tárfiókhoz. A következő oktatóanyagban egy értéket ad vissza az üzembe helyezett tárfiókból.

> [!div class="nextstepaction"]
> [Kimenetek hozzáadása](template-tutorial-add-outputs.md)
