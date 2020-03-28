---
title: Oktatóanyag – Helyi Azure Resource Manager-sablon telepítése
description: Az Azure Resource Manager-sablon helyi számítógépről történő üzembe helyezése
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c8e3eb62fa52caeaa63808b6b9ea199bdff5c4da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082257"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Oktatóanyag: Helyi Azure Resource Manager-sablon üzembe helyezése

Ismerje meg, hogyan telepíthet egy Azure Resource Manager-sablont a helyi számítógépről. Körülbelül **8 percet** vesz igénybe.

Ez az oktatóanyag az első a sorozatból. A sorozat előrehaladásával a sablont egy csatolt sablon létrehozásával, a csatolt sablon takarásban tárolhatja, és sas-jogkivonathasználatával biztonságossá teszi a csatolt sablont, és megtudhatja, hogyan hozhat létre DevOp-folyamatot a sablonok üzembe helyezéséhez. Ez a sorozat a sablon telepítésére összpontosít.  Ha meg szeretné tanulni sablon fejlesztés, lásd a [kezdő oktató .](./template-tutorial-create-first-template.md)

## <a name="get-tools"></a>Eszközök beszereznie

Kezdjük azzal, hogy meggyőződjön arról, hogy rendelkezik a sablonok üzembe helyezéséhez szükséges eszközökkel.

### <a name="command-line-deployment"></a>Parancssori telepítés

A sablon üzembe helyezéséhez az Azure PowerShellre vagy az Azure CLI-re van szükség. A telepítési útmutatót lásd:

- [Az Azure PowerShell telepítése](/powershell/azure/install-az-ps)
- [Az Azure CLI telepítése Windows rendszeren](/cli/azure/install-azure-cli-windows)
- [Az Azure CLI telepítése Linuxra](/cli/azure/install-azure-cli-linux)

Az Azure PowerShell vagy az Azure CLI telepítése után győződjön meg arról, hogy először jelentkezik be. További segítség: [Bejelentkezés – PowerShell](/powershell/azure/install-az-ps#sign-in) vagy [Bejelentkezés – Azure CLI.](/cli/azure/get-started-with-azure-cli#sign-in)

### <a name="editor-optional"></a>Szerkesztő (nem kötelező)

A sablonok JSON-fájlok. A sablonok áttekintéséhez/ szerkesztéséhez jó JSON-szerkesztőre van szükség. A Visual Studio-kódot az Erőforrás-kezelő eszközök bővítménylel ajánljuk. Ha telepítenie kell ezeket az eszközöket, olvassa el [a Visual Studio-kód használata Az Azure Resource Manager-sablonok létrehozásához című témakört.](use-vs-code-to-create-template.md)

## <a name="review-template"></a>Véleményezési sablon

Az oktatóanyagban használt sablon hasonló a [rövid útmutató sablonjában használt sablonhoz.](template-tutorial-quickstart-template.md) Ha érdekel a sablon létrehozása, akkor megy keresztül, hogy a bemutató. Ez azonban nem szükséges az oktatóanyag befejezéséhez.

A sablon üzembe helyez egy tárfiókot, az alkalmazásszolgáltatás-csomagot és a webalkalmazást.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> A tárfióknevek nek 3 és 24 karakter közötti hosszúságúnak kell lenniük, és csak számokat és kisbetűket használhatnak. A névnek egyedinek kell lennie. A sablonban a tárfiók neve a projekt neve az "store" csatolt, és a projekt neve 3 és 11 karakter között kell lennie. Így a projekt nevének meg kell felelnie a tárfiók nevére vonatkozó követelményeknek, és kevesebb, mint 11 karakterből áll.

Mentse a sablon egy példányát a helyi számítógépre a .json kiterjesztéssel, például az azuredeploy.json. Ezt a sablont az oktatóanyag későbbi részében telepíti.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha meg szeretne kezdeni dolgozni az Azure PowerShell/Azure CLI használatával egy sablon üzembe helyezéséhez, jelentkezzen be az Azure-hitelesítő adatokkal.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Ha több Azure-előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Sablon üzembe helyezésekor meg kell adnia egy erőforráscsoportot, amely tartalmazza az erőforrásokat. A központi telepítési parancs futtatása előtt hozza létre az erőforráscsoportot az Azure CLI vagy az Azure PowerShell használatával. Válassza ki a lapokat a következő kódszakaszban az Azure PowerShell és az Azure CLI közül választhat. A CLI példák ebben a cikkben vannak írva a Bash shell.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Sablon üzembe helyezése

A sablon központi telepítéséhez használja az egyik vagy mindkét telepítési lehetőséget.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Ha többet szeretne tudni a sablonok Azure PowerShell használatával történő üzembe helyezéséről, [olvassa el az Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure PowerShell használatával.](./deploy-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Ha többet szeretne tudni a sablonok Azure CLI használatával történő üzembe helyezéséről, [olvassa el az Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel.](./deploy-cli.md)

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport törlésével törölje a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan kell telepíteni egy helyi sablont. A következő oktatóanyagban a sablont egy fő sablonra és egy csatolt sablonra bonthatja, és megtudhatja, hogyan tárolhatja és biztonságossá teszi a csatolt sablont.

> [!div class="nextstepaction"]
> [Csatolt sablon telepítése](./deployment-tutorial-linked-template.md)
