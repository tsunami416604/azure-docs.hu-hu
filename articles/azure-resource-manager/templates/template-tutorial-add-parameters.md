---
title: Oktatóanyag - paraméterek hozzáadása a sablonhoz
description: Paraméterek hozzáadása az Azure Resource Manager-sablonhoz, hogy újrafelhasználható legyen.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: de7ec961672db2f3120e00f1a42b33f71e7ab092
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437834"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Oktatóanyag: Paraméterek hozzáadása az ARM-sablonhoz

Az [előző oktatóanyagból](template-tutorial-add-resource.md)megtanulta, hogyan adhat hozzá tárfiókot a sablonhoz, és hogyan telepítheti azt. Ebben az oktatóanyagban megtudhatja, hogyan fejlesztheti az Azure Resource Manager (ARM) sablont paraméterek hozzáadásával. Ez az oktatóanyag körülbelül **14 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [oktatóanyagot az erőforrásokról,](template-tutorial-add-resource.md)de ez nem szükséges.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

Végén az előző bemutató, a sablon volt a következő JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Lehet, hogy észrevette, hogy van egy probléma ezzel a sablonnal. A tárfiók neve kódolva van. Ezt a sablont csak akkor használhatja, ha mindig ugyanazt a tárfiókot telepíti. Egy más nevű tárfiókot üzembe helyezéséhez létre kell hoznia egy új sablont, amely nyilvánvalóan nem praktikus módja az üzemelő példányok automatizálásának.

## <a name="make-template-reusable"></a>Sablon újrafelhasználhatóvá tegyük

A sablon újrafelhasználhatóvá tenni, adjunk hozzá egy paramétert, amely segítségével adja át a tárfiók nevét. A következő példában kiemelt JSON bemutatja, hogy mi változott a sablonban. A **storageName** paraméter karakterláncként van azonosítva. A maximális hossz 24 karakterre van állítva, hogy megakadályozza a túl hosszú neveket.

Másolja a teljes fájlt, és cserélje le a sablontartalmát.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

Telepítsük a sablont. A következő példa telepíti a sablont az Azure CLI vagy a PowerShell használatával. Figyelje meg, hogy a tárfiók nevét adja meg a központi telepítési parancs egyik értékeként. A tárfiók nevéhez adja meg az előző oktatóanyagban használt nevet.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Az erőforrás-frissítések ismertetése

Az előző szakaszban üzembe helyezett egy tárfiókot a korábban létrehozott nevű tárfiókot. Lehet, hogy vajon hogyan befolyásolja az erőforrás az átcsoportosítás.

Ha az erőforrás már létezik, és a tulajdonságokban nem észlelváltozás, a rendszer nem tesz műveletet. Ha az erőforrás már létezik, és egy tulajdonság megváltozott, az erőforrás frissül. Ha az erőforrás nem létezik, létrejön.

A frissítések kezelésének ezen módja azt jelenti, hogy a sablon az Azure-megoldáshoz szükséges összes erőforrást tartalmazhatja. Biztonságosan újratelepítheti a sablont, és tudja, hogy az erőforrások csak szükség esetén módosulnak vagy jönnek létre. Ha például fájlokat adott hozzá a tárfiókhoz, újratelepítheti a tárfiókot anélkül, hogy elveszítené ezeket a fájlokat.

## <a name="customize-by-environment"></a>Testreszabás környezet szerint

Paraméterek megadásával testreszabhatja az üzemelő példányt úgy, hogy az adott környezetnek megfelelő értékeket ad meg. Például különböző értékeket adhat át attól függően, hogy fejlesztési, tesztelési és éles környezetbe telepít-e.

Az előző sablon mindig Standard_LRS tárfiókot telepített. Előfordulhat, hogy a környezettől függően rugalmasan szeretné üzembe helyezni a különböző sus-okat. A következő példa a termékváltozat paraméterének hozzáadásához végrehajtott módosításokat mutatja be. Másolja a teljes fájlt, és illessze be a sablont.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

A **storageSKU** paraméter alapértelmezett értékkel rendelkezik. Ez az érték akkor használatos, ha a központi telepítés során nincs megadva érték. Az engedélyezett értékek listája is szerepel benne. Ezek az értékek megfelelnek a tárfiók létrehozásához szükséges értékeknek. Nem szeretné, hogy a sablon felhasználói átmenjenek a nem működik, nem használható sk-ekben.

## <a name="redeploy-template"></a>Sablon ismételt üzembe helyezése

Készen áll a bevetésre. Mivel az alapértelmezett termékváltozat **értéke Standard_LRS,** nem kell értéket megadnia az adott paraméterhez.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Ha a telepítés nem sikerült, használja a **hibakeresési kapcsolót** a központi telepítési paranccsal a hibakeresési naplók megjelenítéséhez.  A **részletes** kapcsolóval is megjelenítheti a teljes hibakeresési naplókat.

A sablon rugalmasságának megtekintéséhez telepítse újra a webhelyet. Ezúttal állítsa a termékváltozat paramétert **Standard_GRS**. Átadhat egy új nevet egy másik tárfiók létrehozásához, vagy ugyanazt a nevet használhatja a meglévő tárfiók frissítéséhez. Mindkét lehetőség működik.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Végül futtasson még egy tesztet, és nézze meg, mi történik, ha egy termékváltozatban, amely nem az engedélyezett értékek egyike. Ebben az esetben teszteljük azt a forgatókönyvet, amelyben a sablon felhasználója úgy gondolja, hogy az **alapvető** a termék- és termékkészlet egyike.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

A parancs azonnal sikertelen lesz egy hibaüzenettel, amely kimondja, hogy mely értékek engedélyezettek. Az Erőforrás-kezelő a telepítés megkezdése előtt azonosítja a hibát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Paraméterek hozzáadásával javította az [első oktatóanyagban](template-tutorial-create-first-template.md) létrehozott sablont. A következő oktatóanyagban megismerheti a sablonfüggvényeket.

> [!div class="nextstepaction"]
> [Sablonfüggvények hozzáadása](template-tutorial-add-functions.md)