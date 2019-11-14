---
title: Oktatóanyag – & üzembe helyezése Azure Resource Manager-sablon létrehozása
description: Hozza létre az első Azure Resource Manager-sablonját. Az oktatóanyagban megismerheti a sablonfájl szintaxisát és a Storage-fiók központi telepítését.
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 463f9a54648a9e6950f839bd856ccf42bc66dca0
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074375"
---
# <a name="tutorial-create-and-deploy-your-first-azure-resource-manager-template"></a>Oktatóanyag: az első Azure Resource Manager-sablon létrehozása és üzembe helyezése

Ez az oktatóanyag bemutatja, hogyan Azure Resource Manager sablonokat. Bemutatja, hogyan hozhat létre kezdő sablont, és hogyan helyezheti üzembe az Azure-ban. Megismerheti a sablon felépítését és a sablonokkal való munkához szükséges eszközöket. Az oktatóanyag elvégzése körülbelül **12 percet** vesz igénybe, de a tényleges idő attól függően változik, hogy hány eszközt kell telepítenie.

Ez az oktatóanyag egy sorozat első része. Az adatsorozaton keresztüli előrehaladást követően módosítsa a sablon indítása lépésről lépésre, amíg meg nem vizsgálja a Resource Manager-sablonok összes alapvető részét. Ezek az elemek a sokkal összetettebb sablonok építőelemei. Reméljük, hogy a sorozat végén biztos lehet abban, hogy saját sablonokat hoz létre, és készen áll a sablonokkal való üzembe helyezések automatizálására.

Ha szeretné megismerni a sablonok használatának előnyeit, és azt, hogy miért érdemes a sablonokkal automatizálni az üzembe helyezést, olvassa el a [Azure Resource Manager-sablonok](template-deployment-overview.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="get-tools"></a>Eszközök beolvasása

Először is győződjön meg arról, hogy rendelkezik a sablonok létrehozásához és üzembe helyezéséhez szükséges eszközökkel.

### <a name="editor"></a>Szerkesztő

A sablonok JSON-fájlok. Sablonok létrehozásához jó JSON-szerkesztőre van szükség. A Visual Studio Code-ot a Resource Manager-eszközök bővítménnyel ajánljuk. Ha telepítenie kell ezeket az eszközöket, tekintse meg [Azure Resource Manager sablonok létrehozásához használja a Visual Studio Code-](./resource-manager-tools-vs-code.md)ot.

### <a name="command-line-deployment"></a>Parancssori telepítés

A sablon telepítéséhez Azure PowerShell vagy Azure CLI is szükséges. A telepítési utasításokért lásd:

- [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
- [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
- [Az Azure CLI telepítése Linux rendszeren](/cli/azure/install-azure-cli-linux)

A Azure PowerShell vagy az Azure CLI telepítését követően ellenőrizze, hogy az első alkalommal jelentkezik-e be. Segítségért lásd: [Bejelentkezés – PowerShell](/powershell/azure/install-az-ps#sign-in) vagy [Bejelentkezés – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

Most már készen áll a sablonok megismerésének megkezdésére.

## <a name="create-your-first-template"></a>Az első sablon létrehozása

1. Nyissa meg a Visual Studio Code-ot, és telepítse a Resource Manager Tools bővítményt.
1. Új fájl létrehozásához a **fájl** menüben válassza az **új fájl** elemet.
1. A **fájl** menüben válassza a **Mentés másként**lehetőséget.
1. Nevezze el a fájlt **azuredeploy** , és válassza ki a **JSON** -fájl kiterjesztését. A **azuredeploy. JSON**fájl teljes neve.
1. Mentse a fájlt a munkaállomásra. Olyan elérési utat adjon meg, amely könnyen megjegyezhető, mert a sablon telepítésekor később is megadhatja ezt az útvonalat.
1. Másolja és illessze be a következő JSON-fájlt a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    A VS Code-környezet a következőképpen néz ki:

    ![Resource Manager-sablon Visual Studio Code – első sablon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Ez a sablon nem telepít semmilyen erőforrást. A rendszer egy üres sablont használ, hogy megismerje a sablon üzembe helyezésének lépéseit, miközben minimalizálja a hiba valószínűségét.

    A JSON-fájl a következő elemeket tartalmazza:

    - **$Schema**: a JSON-séma fájljának helyét adja meg. A sémafájl a sablonon belül elérhető tulajdonságokat írja le. A séma például az **erőforrásokat** egy sablon érvényes tulajdonságainak egyike alapján határozza meg. Ne aggódjon, hogy a séma dátuma 2015-01-01. Ez a séma-verzió naprakész, és tartalmazza a legújabb funkciókat. A séma dátumának módosítása nem történt meg, mert a bevezetése óta nem történt változás.
    - **contentVersion**: a sablon verzióját adja meg (például 1.0.0.0). Ehhez az elemhez bármilyen értéket megadhat. Ezzel az értékkel dokumentálhatja a sablon jelentős változásait. Ha a sablonnal telepít erőforrásokat, ezzel az értékkel meggyőződhet arról, hogy a megfelelő sablon használatban van-e.
    - **erőforrások**: tartalmazza a telepíteni vagy frissíteni kívánt erőforrásokat. Jelenleg üres, de később további erőforrásokat fog hozzáadni.

1. Mentse a fájlt.

Gratulálunk, létrehozta az első sablont.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A Azure PowerShell/Azure CLI használatának megkezdéséhez jelentkezzen be az Azure-beli hitelesítő adataival.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Sablon központi telepítésekor megadhat egy erőforráscsoportot, amely az erőforrásokat fogja tartalmazni. Az üzembe helyezési parancs futtatása előtt hozza létre az erőforráscsoportot az Azure CLI vagy a Azure PowerShell használatával. Válassza ki az alábbi kódrészletet a Azure PowerShell és az Azure CLI közötti választáshoz.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez használja az Azure CLI-t vagy a Azure PowerShell. Használja az Ön által létrehozott erőforráscsoportot. Adjon nevet a központi telepítésnek, hogy könnyen azonosítható legyen az üzembe helyezési előzményekben. A kényelem érdekében hozzon létre egy olyan változót is, amely a sablonfájl elérési útját tárolja. Ez a változó megkönnyíti az üzembe helyezési parancsok futtatását, mert minden egyes telepítésekor nem kell újraírnia az elérési utat.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$templateFile="{provide-the-path-to-the-template-file}"
az group deployment create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Az üzembehelyezési parancs az eredményeket adja vissza. Keresse meg a `ProvisioningState`, és ellenőrizze, hogy sikeres volt-e az üzembe helyezés.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell üzembe helyezési állapota](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLI üzembe helyezési állapot](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

A központi telepítés ellenőrzéséhez tekintse meg az erőforráscsoportot a Azure Portalból.

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza az **erőforráscsoportok**lehetőséget.

1. Válassza ki az erőforráscsoport központi telepítését a legutóbbi eljárásban. Az alapértelmezett név a **myResourceGroup**. Az erőforráscsoporthoz tartozó erőforrás nem jelenik meg.

1. Az Áttekintés jobb felső sarkában megjelenik az üzemelő példány állapota. Válassza az **1 sikeres**lehetőséget.

   ![Központi telepítés állapotának megtekintése](./media/template-tutorial-create-first-template/deployment-status.png)

1. Az erőforráscsoport központi telepítésének előzményei láthatók. Válassza a **blanktemplate**lehetőséget.

   ![Központi telepítés kiválasztása](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. A központi telepítés összegzése látható. Ebben az esetben nem sokat kell látnia, mert nincs üzembe helyezett erőforrás. A sorozat későbbi részében hasznos lehet megtekinteni az összefoglalás áttekintését az üzembe helyezési előzményekben. A bal oldalon megtekintheti a bemeneteket, a kimeneteket és az üzembe helyezés során használt sablont.

   ![Központi telepítés összegzésének megtekintése](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes törölni az erőforráscsoportot.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Létrehozott egy egyszerű sablont az Azure-ba való üzembe helyezéshez. A következő oktatóanyagban hozzá kell adnia egy Storage-fiókot a sablonhoz, és telepítenie kell azt az erőforráscsoporthoz.

> [!div class="nextstepaction"]
> [Erőforrás hozzáadása](template-tutorial-add-resource.md)
