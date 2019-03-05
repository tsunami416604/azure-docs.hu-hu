---
title: Azure Resource Manager-sablon létrehozása a Visual Studio Code használatával | Microsoft Docs
description: A Resource Manager-sablonokon a Visual Studio Code-dal és az Azure Resource Manager-eszközök bővítményeivel dolgozhat.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: c73084b03736a422e5e3b617ec058ade1de0fbb9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317118"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Gyors útmutató: Az Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával

Útmutató Azure Resource Manager-sablonok létrehozásához és szerkesztéséhez a Visual Studio Code és az Azure Resource Manager Tools bővítmény használatával. Resource Manager-sablonokat a Visual Studio Code-ban a bővítmény nélkül is létrehozhat, a bővítmény azonban rendelkezik olyan automatikus kiegészítési szolgáltatásokkal, amelyek megkönnyítik a sablonok fejlesztését. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md).

Ebben az oktatóanyagban üzembe helyezhet egy tárfiókot:

![Resource manager sablon a rövid útmutató a visual studio code diagram](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-template-quickstart-vscode-diagram.png)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

- [Visual Studio Code](https://code.visualstudio.com/).
- Resource Manager Tools bővítmény. A telepítéshez kövesse az alábbi lépéseket:

    1. Nyissa meg a Visual Studio Code-ot.
    2. A **CTRL+SHIFT+X** billentyűkombinációval nyissa meg a Bővítmények ablaktáblát
    3. Keresse meg az **Azure Resource Manager Tools** elemet, és válassza a **Telepítés** lehetőséget.
    4. A bővítmény telepítésének befejezéséhez válassza az **Újrabetöltés** lehetőséget.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure gyorsindítási sablonok a Resource Manager-sablonok tárházaként szolgálnak.

Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save as** (Mentés másként) lehetőség kiválasztásával mentheti a fájlt a helyi számítógépre, **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

Működik az szerkesztése a Visual Studio Code egy sablont, hozzá kell adnia egy további elem be a `outputs` szakasz megjelenítéséhez a tároló URI azonosítója.

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

    ![IntelliSense a Visual Studio Code-ban egy Resource Manager-sablon használatakor](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. A fájl mentéséhez válassza a **Fájl**>**Mentés** lehetőséget.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere létezik. Az Azure Cloud shell ebben a rövid útmutatóban szolgál. A cloud shell támogatja az Azure CLI-vel és az Azure PowerShell. Választhat a CLI és PowerShell lapon választómezőt használja.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Jelentkezzen be az [Azure Cloud Shellbe](https://shell.azure.com).

2. Válassza ki az Ön által preferált környezetben kiválasztásával vagy **PowerShell** vagy **Bash**(CLI) a bal felső sarokban.  A váltáskor a felületet újra kell indítani.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Parancssori felület](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

    ![Az Azure portal Cloud shell PowerShell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-powershell.png)

    ---

3. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)
    
    ---

    Válassza ki a korábbi szakaszban mentett fájlt. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**. A sablonfájl a rendszerhéj elérhetőknek kell lenniük.

    Lehetősége van a **ls** parancsot, és a **cat** paranccsal ellenőrizheti, hogy a fájl feltöltése sikeresen befejeződött. 

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)
    
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

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)
    
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

Az Azure storage-fiókok használatával kapcsolatos további tudnivalókért lásd: [a rövid útmutató: Blobok feltöltése, letöltése, és listát az Azure portal használatával](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

A rövid útmutató elsősorban az Azure-gyorssablonok közül egy meglévő sablon a Visual Studio Code-dal történő szerkesztését mutatja be. Azt is megtanulta, hogyan helyezheti üzembe a sablont az Azure Cloud shell a parancssori felület vagy a PowerShell használatával. Az Azure-gyorssablonok nem biztos, hogy minden tekintetben megfelelnek Önnek. A következő oktatóanyag azt mutatja be, hogyan keresheti meg a megfelelő információkat a sablonreferenciában titkosított Azure Storage-fiók létrehozásához.

> [!div class="nextstepaction"]
> [Titkosított tárfiók létrehozása](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
