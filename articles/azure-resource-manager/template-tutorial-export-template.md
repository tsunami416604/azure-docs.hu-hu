---
title: Oktatóanyag – Azure Resource Manager sablon exportálása a Azure Portalból
description: Megtudhatja, hogyan használhatja az exportált sablont a sablon fejlesztésének befejezéséhez.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 53c93b4e0184468ddee75613b48b887c9d07f751
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71963792"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Oktatóanyag: Exportált sablon használata a Azure Portal

Ebben az oktatóanyag-sorozatban létrehozott egy sablont egy Azure Storage-fiók üzembe helyezéséhez. A következő két oktatóanyagban hozzá kell adnia egy *app Service csomagot* és egy *webhelyet*. A sablonok a semmiből való létrehozása helyett megtudhatja, hogyan exportálhat sablonokat a Azure Portalból, és hogyan használhatók a sablonok az Azure-beli [Gyorsindítás sablonokból](/resources/templates/). Ezeket a sablonokat saját használatra testreszabhatja. Ez az oktatóanyag a sablonok exportálására, valamint a sablon eredményének testreszabására koncentrál. A művelet elvégzése körülbelül **14 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be a [kimenetekkel kapcsolatos oktatóanyagot](template-tutorial-add-outputs.md), de ez nem kötelező.

A Visual Studio Code-nak rendelkeznie kell a Resource Manager-eszközök bővítménnyel, valamint Azure PowerShell vagy az Azure CLI-vel. További információ: [sablon eszközei](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>A sablon áttekintése

Az előző oktatóanyag végén a sablon a következő JSON-t használta:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Ez a sablon jól működik a Storage-fiókok telepítéséhez, de érdemes lehet további erőforrásokat hozzáadni hozzá. Egy meglévő erőforrásból is exportálhat sablonokat, így gyorsan lekérheti a JSON-t az adott erőforráshoz.

## <a name="create-app-service-plan"></a>App Service-csomag létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **erőforrás létrehozása**.
1. A **Keresés a piactéren**mezőbe írja be **app Service csomagot**, majd válassza a **app Service tervet**.  Ne válassza ki a **app Service csomagot (klasszikus)**
1. Kattintson a **Létrehozás** gombra.
1. Be

    - **Előfizetés**: válassza ki az Azure-előfizetést.
    - **Erőforráscsoport**: Válassza az **új létrehozása** lehetőséget, majd adjon meg egy nevet. Adjon meg egy másik erőforráscsoport-nevet, mint amelyet a jelen oktatóanyag-sorozatban használt.
    - **Név**: adja meg az App Service-csomag nevét.
    - **Operációs rendszer**: válassza a **Linux**lehetőséget.
    - **Régió**: válasszon ki egy Azure-helyet. Ha például **USA középső RÉGIÓJA**.
    - **Árképzési szint**: a költségek megtakarítása érdekében módosítsa az SKU-t az **alapszintű**értékre.

    ![Resource Manager-sablon exportálása sablon portál](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Válassza **a felülvizsgálat és létrehozás**lehetőséget.
1. Kattintson a **Létrehozás** gombra. Az erőforrás létrehozása néhány percet vesz igénybe.

## <a name="export-template"></a>Sablon exportálása

1. Válassza **az Ugrás erőforráshoz**lehetőséget.

    ![Erőforrás megnyitása](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Válassza a **sablon exportálása**lehetőséget.

    ![Resource Manager-sablon exportálása sablon](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   A sablon exportálása funkció egy erőforrás aktuális állapotát veszi alapul, és létrehoz egy sablont az üzembe helyezéséhez. A sablon exportálása hasznos lehet az erőforrások üzembe helyezéséhez szükséges JSON gyors beszerzéséhez.

1. Másolja a **Microsoft. Web/kiszolgálófarmok** definícióját és a paraméter definícióját a sablonba.

    ![Resource Manager-sablon exportálása sablon exportált sablon](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Az exportált sablon általában részletesebb, mint a sablon létrehozásakor. Az exportált sablon SKU objektuma például öt tulajdonsággal rendelkezik. Ez a sablon működik, de csak a **Name** tulajdonságot használhatja. Elindíthatja az exportált sablont, majd módosíthatja azt úgy, hogy illeszkedjen az igényeinek megfelelően.

## <a name="revise-existing-template"></a>Meglévő sablon módosítása

Az exportált sablon lehetővé teszi a legtöbb szükséges JSON-t, de testre kell szabnia azt a sablonhoz. Fordítson különös figyelmet a sablon és az exportált sablon közötti paraméterek és változók különbségére. Az exportálási folyamat természetesen nem ismeri a sablonban már definiált paramétereket és változókat.

Az alábbi példa kiemeli a sablon kiegészítéseit. Az exportált kódot és néhány módosítást is tartalmaz. Először is megváltoztatja a paraméter nevét, hogy az megfeleljen az elnevezési konvenciónak. Másodszor, a Location paramétert használja az App Service-csomag helyéhez. Harmadszor, eltávolítja a **nevet** a **Properties** objektumon belül, mert ez az érték redundáns a **Name** tulajdonsággal az erőforrás szintjén.

Másolja a teljes fájlt, és cserélje le a sablont a tartalmára.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-template"></a>Sablon üzembe helyezése

Sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell-t.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](template-tutorial-create-first-template.md#create-resource-group)című témakört. A példa feltételezi, hogy a **templateFile** változót a sablonfájl elérési útjára állította, ahogy az az [első oktatóanyagban](template-tutorial-create-first-template.md#deploy-template)is látható.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.
1. Válassza ki azt az erőforráscsoportot, amelyet központilag telepített.
1. Az erőforráscsoport egy Storage-fiókot és egy App Service tervet tartalmaz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes lehet törölni a telepített erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan exportálhat sablont a Azure Portalból, és hogyan használhatja az exportált sablont a sablon fejlesztéséhez. Az Azure rövid útmutató sablonjait is használhatja a sablonok fejlesztésének egyszerűbbé tételéhez.

> [!div class="nextstepaction"]
> [Az Azure Gyorsindítás sablonjainak használata](template-tutorial-quickstart-template.md)