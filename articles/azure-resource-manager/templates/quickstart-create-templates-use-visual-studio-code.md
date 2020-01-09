---
title: Sablon létrehozása – Visual Studio Code
description: A Resource Manager-sablonokon a Visual Studio Code-dal és az Azure Resource Manager-eszközök bővítményeivel dolgozhat.
author: mumian
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 9a829b0c84c397f297539cdb04b3ad027a18c834
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473618"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Rövid útmutató: Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával

Útmutató Azure Resource Manager-sablonok létrehozásához és szerkesztéséhez a Visual Studio Code és az Azure Resource Manager Tools bővítmény használatával. Resource Manager-sablonokat a Visual Studio Code-ban a bővítmény nélkül is létrehozhat, a bővítmény azonban rendelkezik olyan automatikus kiegészítési szolgáltatásokkal, amelyek megkönnyítik a sablonok fejlesztését. Az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez tekintse meg a [sablonok üzembe helyezésének áttekintése](overview.md)című témakört.

Ebben a rövid útmutatóban egy Storage-fiókot telepít:

![Resource Manager-sablon – rövid útmutató Visual Studio Code diagram](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

- [Visual Studio Code](https://code.visualstudio.com/).
- A Resource Manager Tools bővítmény. A telepítéshez kövesse az alábbi lépéseket:

    1. Nyissa meg a Visual Studio Code-ot.
    2. A **CTRL+SHIFT+X** billentyűkombinációval nyissa meg a Bővítmények ablaktáblát
    3. Keresse meg az **Azure Resource Manager Tools** elemet, és válassza a **Telepítés** lehetőséget.
    4. A bővítmény telepítésének befejezéséhez válassza az **Újrabetöltés** lehetőséget.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure Gyorsindítás sablonjai a Resource Manager-sablonok tárháza.

Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) >**Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **Fájl**>**Mentés másként** elem kiválasztásával mentse a fájlt a helyi számítógépre **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

Ha szeretné megtapasztalni, hogyan szerkeszthet egy sablont a Visual Studio Code használatával, adjon hozzá még egy elemet a `outputs` szakaszhoz a tárolási URI megjelenítéséhez.

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

    Ha másolta és beillesztette a kódot a Visual Studio Code-ban, a **value** elem újbóli beírásával próbálhatja ki a Resource Manager Tools bővítmény IntelliSense képességét.

    ![IntelliSense a Visual Studio Code-ban egy Resource Manager-sablon használatakor](./media/quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. A fájl mentéséhez válassza a **Fájl**>**Mentés** lehetőséget.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere van. Ebben a rövid útmutatóban az Azure Cloud Shell használatos. A Cloud shell az Azure CLI-t és a Azure PowerShell is támogatja. A CLI és a PowerShell közötti választáshoz használja a TAB választót.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Jelentkezzen be az [Azure Cloud Shellbe](https://shell.azure.com).

2. Válassza ki az előnyben részesített környezetet a bal felső sarokban található **PowerShell** vagy **bash**(CLI) kiválasztásával.  A váltáskor a felületet újra kell indítani.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Az Azure Portal Cloud Shell CLI szolgáltatása](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![A Cloud Shell PowerShell Azure Portal](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Fájl feltöltése az Azure Portal Cloud Shell szolgáltatásával](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Fájl feltöltése az Azure Portal Cloud Shell szolgáltatásával](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)

    ---

    Válassza ki a korábbi szakaszban mentett fájlt. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**. A sablon fájljának elérhetőnek kell lennie a rendszerhéjból.

    Igény szerint az **ls** parancsot és a **Cat** parancsot is használhatja a fájl sikeres feltöltésének ellenőrzéséhez.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)

    ---
4. Futtassa az alábbi parancsokat a Cloud Shellben. Válassza ki a megfelelő lapot a PowerShell-kód vagy a parancssori felület kód megjelenítéséhez.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az group deployment create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

    Ha a fájlt nem **azuredeploy.json** néven menti el, frissítse a sablonban a fájlnevet.

    A következő képernyőkép egy felületi üzembe helyezés mintáját mutatja be:

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)

    ---

    A képernyőképen a tárfiók neve és a tár URL-címe ki van emelve a kimeneti szakaszban. A tárfiók nevére szükség lesz a következő lépésben.

5. Az alábbi parancssori felületi vagy PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott tárfiókot:

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```

    ---

Az Azure-tárfiókokkal kapcsolatos további információkért lásd: [Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A rövid útmutató elsősorban az Azure-gyorssablonok közül egy meglévő sablon a Visual Studio Code-dal történő szerkesztését mutatja be. Azt is megtanulta, hogyan helyezheti üzembe a sablont a parancssori felület vagy a PowerShell használatával az Azure Cloud shellben. Az Azure-gyorssablonok nem biztos, hogy minden tekintetben megfelelnek Önnek. A sablonok fejlesztésével kapcsolatos további tudnivalókért tekintse meg az új kezdő oktatóanyag-sorozatot:

> [!div class="nextstepaction"]
> [Kezdő oktatóanyagok](./template-tutorial-create-first-template.md)
