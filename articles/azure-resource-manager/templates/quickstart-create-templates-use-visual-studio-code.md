---
title: Sablon létrehozása – Visual Studio-kód
description: A Resource Manager-sablonokon a Visual Studio Code-dal és az Azure Resource Manager-eszközök bővítményeivel dolgozhat.
author: mumian
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: a0c80f18e9cd09b765804aaddbd178b4b3e32a9d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984452"
---
# <a name="quickstart-create-arm-templates-by-using-visual-studio-code"></a>Rövid útmutató: ARM-sablonok létrehozása a Visual Studio-kód használatával

Ismerje meg, hogyan hozhat létre és szerkeszthette az Azure Resource Manager (ARM) sablonokat a Visual Studio-kód és az Azure Resource Manager Tools bővítmény használatával. Arm-sablonokat a Visual Studio Code programban is létrehozhat a bővítmény nélkül, de a bővítmény automatikus kiegészítési beállításokat biztosít, amelyek leegyszerűsítik a sablonfejlesztést. Az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez olvassa el a [sablonüzembe helyezés áttekintése című témakört.](overview.md)

Ebben a rövid útmutatóban egy tárfiókot telepít:

![erőforrás-kezelő sablon rövid útmutató a visual studio kóddiagramjában](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

- [Visual Studio kód](https://code.visualstudio.com/).
- A Resource Manager Tools bővítmény. A telepítéshez kövesse az alábbi lépéseket:

    1. Nyissa meg a Visual Studio Code-ot.
    2. A **CTRL+SHIFT+X** billentyűkombinációval nyissa meg a Bővítmények ablaktáblát
    3. Keresse meg az **Azure Resource Manager Tools** elemet, és válassza a **Telepítés** lehetőséget.
    4. A bővítmény telepítésének befejezéséhez válassza az **Újrabetöltés** lehetőséget.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure quickstart sablonok egy tárház ARM sablonok.

Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása**>**fájl**lehetőséget.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Válassza a **Fájlmentés**>**másként** lehetőséget a fájl **azuredeploy.json néven** a helyi számítógépre történő mentéséhez.

## <a name="edit-the-template"></a>A sablon szerkesztése

Ha meg szeretné tapasztalni, hogyan szerkesztheti a sablont a `outputs` Visual Studio-kód használatával, adjon hozzá még egy elemet a szakaszhoz a tárolási URI megjelenítéséhez.

1. Egy további kimenet felvétele az exportált sablonba:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Ha elkészült, a kimeneti szakasz az alábbihoz hasonlóan néz ki:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Ha a kódot a Visual Studio-kódba másolta és illesztette be, próbálja meg újrabeírni az **értékelemet,** hogy megtapasztalhassa az Erőforrás-kezelő eszközök bővítmény IntelliSense-képességét.

    ![IntelliSense a Visual Studio Code-ban egy Resource Manager-sablon használatakor](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. A fájl mentéséhez válassza a>**Fájlmentés** lehetőséget. **File**

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere van. Az Azure Cloud rendszerhéj ebben a rövid útmutatóban használatos. A felhőbeli rendszerhéj támogatja az Azure CLI-t és az Azure PowerShellt is. A tabulátor segítségével választhat a CLI és a PowerShell között.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Jelentkezzen be az [Azure Cloud Shellbe](https://shell.azure.com).

2. Válassza ki a kívánt környezetet a **PowerShell** vagy a **Bash**(CLI) kiválasztásával a bal felső sarokban.  A váltáskor a felületet újra kell indítani.

    # <a name="cli"></a>[parancssori felület](#tab/CLI)

    ![Az Azure Portal Cloud Shell CLI szolgáltatása](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure Portal Felhőbeli rendszerhéj PowerShell](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre.

    # <a name="cli"></a>[parancssori felület](#tab/CLI)

    ![Fájl feltöltése az Azure Portal Cloud Shell szolgáltatásával](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Fájl feltöltése az Azure Portal Cloud Shell szolgáltatásával](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Válassza ki a korábbi szakaszban mentett fájlt. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**. A sablonfájlnak elérhetőnek kell lennie a rendszerhéjból.

    Az **ls** és a **cat** paranccsal tetszés szerint ellenőrizheti, hogy a fájl feltöltése sikeres-e.

    # <a name="cli"></a>[parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Futtassa az alábbi parancsokat a Cloud Shellben. Válassza ki a megfelelő lapot a PowerShell-kód vagy a parancssori felület kód megjelenítéséhez.

    # <a name="cli"></a>[parancssori felület](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

    Ha a fájlt nem **azuredeploy.json** néven menti el, frissítse a sablonban a fájlnevet.

    A következő képernyőkép egy felületi üzembe helyezés mintáját mutatja be:

    # <a name="cli"></a>[parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    A képernyőképen a tárfiók neve és a tár URL-címe ki van emelve a kimeneti szakaszban. A tárfiók nevére szükség lesz a következő lépésben.

5. Az alábbi parancssori felületi vagy PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott tárfiókot:

    # <a name="cli"></a>[parancssori felület](#tab/CLI)

    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```

    ---

Az Azure-tárfiókokkal kapcsolatos további információkért lásd: [Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

A rövid útmutató elsősorban az Azure-gyorssablonok közül egy meglévő sablon a Visual Studio Code-dal történő szerkesztését mutatja be. Azt is megtanulta, hogyan telepítheti a sablont a CLI vagy a PowerShell használatával az Azure Cloud rendszerhéjból. Az Azure-gyorssablonok nem biztos, hogy minden tekintetben megfelelnek Önnek. Ha többet szeretne megtudni a sablonfejlesztésről, tekintse meg az új kezdő oktatósorozatunkat:

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)
