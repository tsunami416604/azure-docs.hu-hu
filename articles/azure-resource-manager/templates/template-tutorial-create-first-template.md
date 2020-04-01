---
title: Oktatóanyag – & üzembe helyezési sablon létrehozása
description: Hozza létre az első Azure Resource Manager-sablont. Az oktatóanyagban megismerheti a sablonfájl szintaxisát és a tárfiók üzembe helyezésének módját.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8b05bccf10ef5f273a74ca49e02162fd0408230f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411728"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Oktatóanyag: Az első ARM-sablon létrehozása és üzembe helyezése

Ez az oktatóanyag bemutatja az Azure Resource Manager (ARM) sablonokat. Bemutatja, hogyan hozhat létre egy kezdősablont, és hogyan helyezheti üzembe az Azure-ban. Megismerheti a sablon felépítését és a sablonok kezeléséhez szükséges eszközöket. Az oktatóanyag körülbelül **12 percet** vesz igénybe, de a tényleges idő attól függően változik, hogy hány eszközt kell telepítenie.

Ez az oktatóanyag az első a sorozatból. A sorozat előrehaladásával lépésről lépésre módosíthatja a kezdősablont, amíg fel nem deríti az ARM-sablon összes alapvető részét. Ezek az elemek sokkal összetettebb sablonok építőkövei. Reméljük, hogy a sorozat végére biztos lehet abban, hogy saját sablonokat hoz létre, és készen áll arra, hogy sablonoksegítségével automatizálja az üzemelő példányokat.

Ha meg szeretné tudni, hogy milyen előnyökkel jár a sablonok használata, és miért érdemes automatizálni a telepítést sablonokkal, olvassa el az [Azure Resource Manager-sablonok című témakört.](overview.md)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="get-tools"></a>Eszközök beszereznie

Kezdjük azzal, hogy meggyőződjön arról, hogy rendelkezik a sablonok létrehozásához és üzembe helyezéséhez szükséges eszközökkel.

### <a name="editor"></a>Szerkesztő

A sablonok JSON-fájlok. Sablonok létrehozásához szüksége van egy jó JSON szerkesztő. A Visual Studio-kódot az Erőforrás-kezelő eszközök bővítménylel ajánljuk. Ha telepítenie kell ezeket az eszközöket, olvassa el [a Visual Studio-kód használata ARM-sablonok létrehozásához című témakört.](use-vs-code-to-create-template.md)

### <a name="command-line-deployment"></a>Parancssori telepítés

A sablon üzembe helyezéséhez az Azure PowerShellre vagy az Azure CLI-re is szükség van. Ha az Azure CLI, rendelkeznie kell a legújabb verzióval. A telepítési útmutatót lásd:

- [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
- [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
- [Az Azure CLI telepítése Linuxra](/cli/azure/install-azure-cli-linux)

Az Azure PowerShell vagy az Azure CLI telepítése után győződjön meg arról, hogy először jelentkezik be. További segítség: [Bejelentkezés – PowerShell](/powershell/azure/install-az-ps#sign-in) vagy [Bejelentkezés – Azure CLI.](/cli/azure/get-started-with-azure-cli#sign-in)

Oké, készen állsz arra, hogy elkezdj tanulni a sablonokról.

## <a name="create-your-first-template"></a>Az első saját sablon létrehozása

1. Nyissa meg a Visual Studio-kódot úgy, hogy telepítve van az Erőforráskezelő eszközök bővítmény.
1. Új **fájl** létrehozásához válassza az Új fájl menü **Új fájl** parancsát.
1. A **Fájl** menüben válassza a **Mentés másként parancsot.**
1. Nevezze el a fájlt **azuredeploy** és válassza ki a **JSON-fájl** kiterjesztését. Az **azuredeploy.json**fájl teljes neve.
1. Mentse a fájlt a munkaállomásra. Válasszon egy könnyen megjegyezhető útvonalat, mert a sablon telepítésekor később megadja ezt az elérési utat.
1. Másolja a fájlt a következő JSON-fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Így néz ki a VS Code környezet:

    ![Erőforrás-kezelő sablon visual studio kód első sablon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Ez a sablon nem telepít erőforrásokat. Egy üres sablonnal kezdjük, így megismerheti a sablon üzembe helyezésének lépéseit, miközben minimalizálja annak esélyét, hogy valami rosszul sül el.

    A JSON-fájl a következő elemeket tartalmazza:

    - **$schema**: A JSON-sémafájl helyét adja meg. A sémafájl a sablonon belül elérhető tulajdonságokat írja le. A séma például az **erőforrásokat** egy sablon egyik érvényes tulajdonságaként határozza meg. Ne aggódjon, hogy a séma dátuma 2019-04-01. Ez a sémaverzió naprakész, és tartalmazza az összes legújabb funkciót. A séma dátuma nem változott, mert a bevezetése óta nem történt törési változás.
    - **contentVersion**: A sablon verzióját adja meg (például 1.0.0.0). Ehhez az elemhez bármilyen értéket megadhat. Ezzel az értékkel dokumentálhatja a sablon jelentős változásait. Erőforrások sablon használatával történő telepítésekor ez az érték biztosíthatja, hogy a megfelelő sablont használja.
    - **erőforrások**: Az üzembe helyezni vagy frissíteni kívánt erőforrásokat tartalmazza. Jelenleg üres, de később hozzá adja az erőforrásokat.

1. Mentse a fájlt.

Gratulálunk, az első sablont hozta létre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure PowerShell/Azure CLI használatával való munkához jelentkezzen be az Azure-hitelesítő adatokkal.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Sablon üzembe helyezésekor meg kell adnia egy erőforráscsoportot, amely tartalmazza az erőforrásokat. A központi telepítési parancs futtatása előtt hozza létre az erőforráscsoportot az Azure CLI vagy az Azure PowerShell használatával. Válassza ki a lapokat a következő kódszakaszban az Azure PowerShell és az Azure CLI közül választhat. A CLI példák ebben a cikkben vannak írva a Bash shell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon üzembe helyezéséhez használja az Azure CLI vagy az Azure PowerShell. Használja a létrehozott erőforráscsoportot. Adjon nevet a központi telepítésnek, hogy könnyen azonosíthassa a központi telepítési előzményekben. A kényelem érdekében hozzon létre egy változót is, amely tárolja a sablonfájl elérési útját. Ez a változó megkönnyíti a központi telepítési parancsok futtatását, mivel nem kell minden telepítéskor újrabeírnia az elérési utat.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A központi telepítési parancs futtatásához az Azure CLI [legújabb verziójával](/cli/azure/install-azure-cli) kell rendelkeznie.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

A központi telepítési parancs eredményeket ad vissza. Keresse `ProvisioningState` meg, hogy a központi telepítés sikerült-e.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell központi telepítési kiépítési állapota](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Az Azure CLI telepítési kiépítési állapota](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Ha a telepítés nem sikerült, használja a **hibakeresési kapcsolót** a központi telepítési paranccsal a hibakeresési naplók megjelenítéséhez.  A **részletes** kapcsolóval is megjelenítheti a teljes hibakeresési naplókat.

## <a name="verify-deployment"></a>Az üzembe helyezés ellenőrzése

Az üzembe helyezést az Azure Portalon található erőforráscsoport feltárásával ellenőrizheti.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget.

1. Válassza ki az erőforráscsoport üzembe helyezését az utolsó eljárásban. Az alapértelmezett név **a myResourceGroup**. Az erőforráscsoporton belül nem lesz telepítve erőforrás.

1. Értesítés az áttekintés jobb felső részén, a központi telepítés állapota jelenik meg. Válassza az **1 Sikeres lehetőséget.**

   ![A telepítés állapotának megtekintése](./media/template-tutorial-create-first-template/deployment-status.png)

1. Az erőforráscsoport központi telepítésének előzményeit láthatja. Válassza az **üres sablon lehetőséget**.

   ![Telepítés kiválasztása](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Megjelenik a központi telepítés összegzése. Ebben az esetben nincs sok látnivaló, mert nem telepítetterőforrások. A sorozat későbbi részében hasznos lehet az összegzés áttekintése a központi telepítés előzményeiben. Figyelje meg a bal oldalon megtekintheti a bemenetek, kimenetek és a sablon a központi telepítés során használt.

   ![A központi telepítés összegzésének megtekintése](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha továbblép a következő oktatóanyagra, nem kell törölnie az erőforráscsoportot.

Ha most leáll, érdemes törölnie az erőforráscsoportot.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Létrehozott egy egyszerű sablont az Azure-ba való üzembe helyezéshez. A következő oktatóanyagban hozzáad egy tárfiókot a sablonhoz, és üzembe helyezi az erőforráscsoportba.

> [!div class="nextstepaction"]
> [Erőforrás hozzáadása](template-tutorial-add-resource.md)
