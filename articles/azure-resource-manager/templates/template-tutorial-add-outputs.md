---
title: Oktatóanyag - kimenetek hozzáadása a sablonhoz
description: A szintaxis egyszerűsítése érdekében adja hozzá a kimeneteket az Azure Resource Manager-sablonhoz.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 150801fe66da1ea8c6f10d437c6d7616a77b544e
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411008"
---
# <a name="tutorial-add-outputs-to-your-arm-template"></a>Oktatóanyag: Kimenetek hozzáadása az ARM-sablonhoz

Ebben az oktatóanyagban megtudhatja, hogyan ad vissza egy értéket az Azure Resource Manager (ARM) sablonból. Akkor használja a kimeneteket, ha egy üzembe helyezett erőforrásból származó értékre van szüksége. Ez az oktatóanyag **7 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [oktatóanyagot a változókról,](template-tutorial-add-variables.md)de ez nem szükséges.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

Végén az előző bemutató, a sablon volt a következő JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json":::

Üzembe helyez egy tárfiókot, de nem ad vissza semmilyen információt a tárfiókról. Előfordulhat, hogy egy új erőforrásból kell rögzítenie a tulajdonságokat, hogy később is elérhetők lesznek.

## <a name="add-outputs"></a>Kimenetek hozzáadása

A kimenetek segítségével értékeket adhat vissza a sablonból. Például hasznos lehet, ha lekéri az új tárfiók végpontjait.

A következő példa kiemeli a sablon módosítását egy kimeneti érték hozzáadásához. Másolja a teljes fájlt, és cserélje le a sablontartalmát.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json" range="1-53" highlight="47-52":::

Van néhány fontos elem, amelyet figyelembe kell venni a hozzáadott kimeneti értékkel kapcsolatban.

A visszaadott érték típusa **objektum,** ami azt jelenti, hogy egy JSON-objektumot ad vissza.

A [hivatkozási](template-functions-resource.md#reference) függvény t használja a tárfiók futásidejű állapotának leéséhez. Egy erőforrás futásidejű állapotának lekért, adja át egy erőforrás nevét vagy azonosítóját. Ebben az esetben ugyanazt a változót használja, amelyet a tárfiók nevének létrehozásához használt.

Végül az **elsődlegesvégpontok tulajdonságot** adja vissza a tárfiókból.

## <a name="deploy-template"></a>Sablon üzembe helyezése

Készen áll a sablon üzembe helyezésére, és tekintse meg a visszaadott értéket.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

A központi telepítési parancs kimenetében a következőhöz hasonló objektum jelenik meg:

```json
{
    "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
    "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
    "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
    "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
    "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
    "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Ha a telepítés nem sikerült, használja a **hibakeresési kapcsolót** a központi telepítési paranccsal a hibakeresési naplók megjelenítéséhez.  A **részletes** kapcsolóval is megjelenítheti a teljes hibakeresési naplókat.

## <a name="review-your-work"></a>A munka áttekintése

Sokat tettél az elmúlt hat oktató. Vessünk egy kis ideig, hogy vizsgálja felül, mit tett. Olyan sablont hozott létre, amelynek paraméterei könnyen biztosíthatóak. A sablon különböző környezetekben újrafelhasználható, mert lehetővé teszi a testreszabást, és dinamikusan létrehozza a szükséges értékeket. Azt is ad információt a tárfiók, amely a parancsfájlban használható.

Most nézzük meg az erőforráscsoport és a telepítési előzmények.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelybe telepítette.
1. Attól függően, hogy a lépéseket, akkor rendelkeznie kell legalább egy, és talán több tárfiókok az erőforráscsoportban.
1. Azt is meg kell, hogy több sikeres központi telepítések az előzményekben felsorolt. Jelölje ki a hivatkozást.

   ![Központi telepítések kiválasztása](./media/template-tutorial-add-outputs/select-deployments.png)

1. Az összes központi telepítést az előzményekben látja. Válassza ki az addoutputs nevű központi **telepítést.**

   ![Telepítési előzmények megjelenítése](./media/template-tutorial-add-outputs/show-history.png)

1. A bemeneteket áttekintheti.

   ![Bemenetek megjelenítése](./media/template-tutorial-add-outputs/show-inputs.png)

1. Megtekintheti a kimeneteket.

   ![Kimenetek megjelenítése](./media/template-tutorial-add-outputs/show-outputs.png)

1. Megtekintheti a sablont.

   ![Sablon megjelenítése](./media/template-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban hozzáadott egy visszatérési értéket a sablonhoz. A következő oktatóanyagból megtudhatja, hogyan exportálhat egy sablont, és hogyan használhatja az exportált sablon egyes részeit a sablonban.

> [!div class="nextstepaction"]
> [Exportált sablon használata](template-tutorial-export-template.md)
