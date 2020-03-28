---
title: Oktatóanyag – Sablon exportálása az Azure Portalról
description: Ismerje meg, hogyan használhatja az exportált sablont a sablonfejlesztés befejezéséhez.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5a0d373fdf75f19c8fc1082593c15c14770f79c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369899"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Oktatóanyag: Exportált sablon használata az Azure Portalról

Ebben az oktatóanyag-sorozatban létrehozott egy sablont egy Azure storage-fiók üzembe helyezéséhez. A következő két oktatóanyagban hozzáad egy *App Service-csomagot* és egy *webhelyet.* Ahelyett, hogy teljesen új sablonokat hozna létre, megtudhatja, hogyan exportálhat sablonokat az Azure Portalról, és hogyan használhatja a mintasablonokat az [Azure gyorsútmutató sablonjaiból.](https://azure.microsoft.com/resources/templates/) Ezeket a sablonokat az Ön által használt testreszabásra szabhatja. Ez az oktatóanyag a sablonok exportálására és a sablon eredményének testreszabására összpontosít. Körülbelül **14 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az [oktatóanyagot a kimenetekről,](template-tutorial-add-outputs.md)de ez nem szükséges.

A Visual Studio-kódnak rendelkeznie kell a Resource Manager Tools bővítménylel, valamint az Azure PowerShell vagy az Azure CLI használatával. További információt a [sabloneszközök című témakörben talál.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Véleményezési sablon

Végén az előző bemutató, a sablon volt a következő JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Ez a sablon jól működik a tárfiókok üzembe helyezéséhez, de érdemes lehet további erőforrásokat hozzáadni hozzá. Egy sablont exportálhat egy meglévő erőforrásból, hogy gyorsan lekérhesse az adott erőforrás JSON-ját.

## <a name="create-app-service-plan"></a>App Service-csomag létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza az **Erőforrás létrehozása** lehetőséget.
1. A **Keresés a Piactéren**mezőbe írja be az **App Service-csomag**kifejezést, majd válassza **az App Service-csomag lehetőséget.**  Ne válassza az **App Service-csomag (klasszikus)**
1. Kattintson a **Létrehozás** gombra.
1. Adja meg a következőt:

    - **Előfizetés**: válassza ki az Azure-előfizetést.
    - **Erőforráscsoport**: Válassza **az Új létrehozása lehetőséget,** majd adjon meg egy nevet. Adjon meg egy másik erőforráscsoport-nevet, mint amelyet ebben az oktatóanyag-sorozatban használt.
    - **Név**: adja meg az App service csomag nevét.
    - **Operációs rendszer**: válassza a **Linux**lehetőséget.
    - **Régió**: válasszon egy Azure-helyet. Például **az USA középső régiója**.
    - **Tarifacsomag:** a költségek csökkentése érdekében módosítsa a termékváltozat **alapszintű B1** (a fejlesztési/tesztelési területen).

    ![Erőforrás-kezelő sablon exportálási sablonportálja](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Válassza **a Véleményezés és létrehozás lehetőséget.**
1. Kattintson a **Létrehozás** gombra. Az erőforrás létrehozása néhány percet vesz igénybe.

## <a name="export-template"></a>Sablon exportálása

1. Válassza **az Ugrás az erőforráshoz**lehetőséget.

    ![Erőforrás megnyitása](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Válassza **a Sablon exportálása lehetőséget.**

    ![Erőforrás-kezelő sablon exportálási sablonja](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   Az exportálási sablon szolgáltatás egy erőforrás aktuális állapotát veszi fel, és létrehoz egy sablont annak üzembe helyezéséhez. A sablon exportálása hasznos módja lehet az erőforrás üzembe helyezéséhez szükséges JSON gyors beszerzésének.

1. Másolja a sablonba a **Microsoft.Web/serverfarmdefiníciót** és a paraméterdefiníciót.

    ![Erőforrás-kezelő sablon exportált sablonja](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Az exportált sablon általában részletesebb, mint szeretné a sablon létrehozásakor. Az exportált sablon termékváltozat-objektuma például öt tulajdonsággal rendelkezik. Ez a sablon működik, de csak a **name** tulajdonságot használhatja. Kezdheti az exportált sablonnal, majd tetszés szerint módosíthatja azt, hogy megfeleljen az igényeinek.

## <a name="revise-existing-template"></a>Meglévő sablon felülvizsgálata

Az exportált sablon a szükséges JSON nagy részét biztosítja, de testre kell szabnia a sablonhoz. Fordítson különös figyelmet a sablon és az exportált sablon paraméterei és változói közötti különbségekre. Nyilvánvaló, hogy az exportálási folyamat nem ismeri a sablonban már definiált paramétereket és változókat.

A következő példa kiemeli a sablon kiegészítéseit. Ez tartalmazza az exportált kódot, valamint néhány változtatást. Először módosítja a paraméter nevét, hogy megfeleljen az elnevezési konvenciónak. Másodszor, a helyparamétert használja az alkalmazásszolgáltatási csomag helyéhez. Harmadszor eltávolítja a **nevet** a **tulajdonságobjektumon** belül, mert ez az érték redundáns az erőforrás szintjén lévő **névtulajdonsággal.**

Másolja a teljes fájlt, és cserélje le a sablontartalmát.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Sablon üzembe helyezése

Használja az Azure CLI vagy az Azure PowerShell egy sablon üzembe helyezéséhez.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](template-tutorial-create-first-template.md#create-resource-group) A példa feltételezi, hogy beállította a **templateFile** változót a sablonfájl elérési útvonalára, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)látható.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést az Azure Portalon található erőforráscsoport feltárásával ellenőrizheti.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelybe telepítette.
1. Az erőforráscsoport tartalmaz egy tárfiókot és egy App Service-csomag.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet az erőforráscsoport törlésével felvetheti a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan exportálhat egy sablont az Azure Portalról, és hogyan használhatja az exportált sablont a sablon fejlesztéséhez. Az Azure gyorsindítási sablonjai valegyszerűsíthetik a sablonfejlesztést.

> [!div class="nextstepaction"]
> [Azure gyorsindítási sablonjainak használata](template-tutorial-quickstart-template.md)
