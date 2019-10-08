---
title: Oktatóanyag – paraméterek hozzáadása Azure Resource Manager sablonhoz
description: Adja hozzá a paramétereket a Azure Resource Manager-sablonhoz, hogy az újrafelhasználható legyen.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f5e631994223d6362512ed0ddc89d1d3c884fbd4
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001498"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>Oktatóanyag: Paraméterek hozzáadása a Resource Manager-sablonhoz

Az [előző oktatóanyagban](template-tutorial-add-resource.md)megtanulta, hogyan adhat hozzá Storage-fiókot a sablonhoz, és hogyan telepítheti azt. Ebből az oktatóanyagból megtudhatja, hogyan javíthatja a sablont paraméterek hozzáadásával. Ez az oktatóanyag körülbelül **14 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be az [erőforrásokkal kapcsolatos oktatóanyagot](template-tutorial-add-resource.md), de nem szükséges.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>A sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

Előfordulhat, hogy észrevette, hogy probléma merült fel ezzel a sablonnal. A Storage-fiók neve nem rögzített. Ezt a sablont csak akkor használhatja, ha minden alkalommal ugyanazt a Storage-fiókot telepíti. Egy másik névvel rendelkező Storage-fiók üzembe helyezéséhez létre kell hoznia egy új sablont, ami nyilvánvalóan nem praktikus módszer az üzemelő példányok automatizálására.

## <a name="make-your-template-reusable"></a>A sablon újrafelhasználhatóvé tétele

A sablon újrafelhasználhatóvé tételéhez vegyünk fel egy paramétert, amely segítségével átadható a Storage-fiók neve. A következő példában szereplő JSON-fájl azt mutatja, hogy mi változott a sablonban. A **storageName** paraméter karakterláncként van azonosítva. A maximális hossz 24 karakter, ami megakadályozza a túl hosszú nevek megadását.

Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Végezzük el a sablon üzembe helyezését. Az alábbi példa telepíti a sablont az Azure CLI vagy a PowerShell használatával. Figyelje meg, hogy a Storage-fiók nevét a telepítési parancs egyik értékeként adja meg. A Storage-fiók neve mezőben adja meg az előző oktatóanyagban használt nevet.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Az erőforrás-frissítések ismertetése

Az előző szakaszban a korábban létrehozott nevű Storage-fiókot telepítette. Előfordulhat, hogy kíváncsi, hogy az erőforrás milyen hatással van az újratelepítésre.

Ha az erőforrás már létezik, és a tulajdonságok között nem észlelhető változás, a rendszer nem végez műveletet. Ha az erőforrás már létezik, és a tulajdonság módosult, az erőforrás frissül. Ha az erőforrás nem létezik, akkor létrejön.

A frissítések kezelési módja azt jelenti, hogy a sablon tartalmazhatja az Azure-megoldáshoz szükséges összes erőforrást. Biztonságosan újra üzembe helyezheti a sablont, és biztos lehet benne, hogy az erőforrások módosulnak, vagy csak szükség esetén jönnek létre. Ha például hozzáadta a fájlokat a Storage-fiókjához, akkor a fájlok elvesztése nélkül újra üzembe helyezheti a Storage-fiókot.

## <a name="customize-by-environment"></a>Testreszabás környezet szerint

Paraméterek megadásával testreszabhatja az üzemelő példányt úgy, hogy az adott környezetnek megfelelő értékeket ad meg. Például különböző értékeket adhat át, attól függően, hogy fejlesztési, tesztelési és éles környezetben végzi-e a telepítést.

Az előző sablon mindig üzembe helyezett egy Standard_LRS-Storage-fiókot. A környezettől függően előfordulhat, hogy a rugalmasságot a különböző SKU-ket érdemes telepíteni. Az alábbi példa bemutatja, hogyan adhat hozzá egy paramétert az SKU-hoz. Másolja a teljes fájlt, és illessze be a sablont.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

A **storageSKU** paraméter alapértelmezett értékkel rendelkezik. Ezt az értéket akkor kell használni, ha nincs megadva érték az üzemelő példányban. Emellett az engedélyezett értékek listája is szerepel. Ezek az értékek megegyeznek a Storage-fiók létrehozásához szükséges értékekkel. Nem szeretné, hogy a sablon felhasználói ne adjanak át olyan SKU-ket, amelyek nem működnek.

## <a name="redeploy-the-template"></a>A sablon újbóli üzembe helyezése

Most már készen áll a telepítésre. Mivel az alapértelmezett SKU a **Standard_LRS**értékre van állítva, nem kell megadnia a paraméter értékét.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

A sablon rugalmasságának megtekintéséhez próbálkozzon újra a telepítéssel. Ezúttal állítsa az SKU paramétert **Standard_GRS**értékre. Átadhat egy új nevet egy másik Storage-fiók létrehozásához, vagy használhatja ugyanazt a nevet a meglévő Storage-fiók frissítéséhez. Mindkét beállítás működik.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Végül futtasson még egy tesztet, és nézze meg, mi történik, ha olyan SKU-t továbbít, amely nem a megengedett értékek egyike. Ebben az esetben teszteljük azt a forgatókönyvet, amelyben a sablon felhasználója azt gondolja, hogy az **alapszintű** az egyik SKU.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

A parancs azonnali hibát jelez, amely jelzi, hogy mely értékek engedélyezettek. A Resource Manager a telepítés megkezdése előtt azonosítja a hibát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Javította az [első oktatóanyagban](template-tutorial-create-first-template.md) létrehozott sablont paraméterek hozzáadásával. A következő oktatóanyagban megismerheti a sablon funkcióit.

> [!div class="nextstepaction"]
> [Sablon funkcióinak hozzáadása](template-tutorial-add-functions.md)
