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
ms.date: 09/07/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: a2b4c4824960c21011876a7c0adf029fc56d93d2
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419117"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Rövid útmutató: Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával

Útmutató Azure Resource Manager-sablonok létrehozásához a Visual Studio Code és az Azure Resource Manager Tools bővítmény használatával. Resource Manager-sablonokat a Visual Studio Code-ban a bővítmény nélkül is létrehozhat, a bővítmény azonban rendelkezik olyan automatikus kiegészítési szolgáltatásokkal, amelyek megkönnyítik a sablonok fejlesztését. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md).

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

A Visual Studio Code-dal történő sablonszerkesztés megismeréséhez egy további elemet kell felvenni a kimeneti szakaszba.

1. Egy további kimenet felvétele az exportált sablonba a Visual Studio Code használatával:

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

    Ha másolta és beillesztette a kódot a Visual Studio Code-ban, a **value** elem újbóli beírásával tapasztalhatja meg a Resource Manager Tools bővítmény IntelliSense képességét.

    ![IntelliSense a Visual Studio Code-ban egy Resource Manager-sablon használatakor](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. A fájl mentéséhez válassza a **Fájl**>**Mentés** lehetőséget.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere létezik.  Ebben a rövid útmutatóban az Azure Portal Azure Cloud Shell szolgáltatását használja. A Cloud Shell az Azure CLI és az Azure PowerShell használatát is támogatja. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
2. Válassza ki a jobb felső sarokban a **Cloud Shell** elemet az alábbi képen látható módon:

    ![Azure Portal – Cloud Shell](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell.png)

    A Cloud Shell az ablak alján van megnyitva.

3. A Cloud Shell bal felső sarkában vagy a **PowerShell**, vagy a **Bash** látható. A parancssori felület használatához Bash-munkamenetet kell megnyitnia. A PowerShell használatához PowerShell-munkamenetet kell megnyitnia. A Bash és a PowerShell közötti váltáshoz válassza a lefelé mutató nyilat. Az alábbi képen a PowerShellről Bashre váltás látható.

    ![Azure Portal – Cloud Shell – Parancssori felület](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)

    A váltáskor a felületet újra kell indítani.
4. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)
    
    ---

    Fel kell töltenie a sablonfájlt, mielőtt üzembe helyezhetné a felületről.
5. Válassza az e rövid útmutatóban korábban mentett fájlt. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**.
6. A fájl sikeres feltöltésének ellenőrzéséhez futtassa az **ls** parancsot a Cloud Shellben. A **cat** paranccsal a sablon tartalmát is ellenőrizheti. Az alábbi képen a parancs Bashben történő futtatása látható.  Ugyanezeket a parancsokat használja a PowerShell-munkamenetben is.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)
    
    ---
7. Futtassa az alábbi parancsokat a Cloud Shellben. Válassza ki a megfelelő lapot a PowerShell-kód vagy a parancssori felület kód megjelenítéséhez.

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)
    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```powershell
    New-AzureRmResourceGroup -Name <ResourceGroupName> -Location <AzureLocation>

    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroupName> -TemplateFile <TemplateFileName>
    ```
    
    ---

    A következő képernyőkép egy felületi üzembe helyezés mintáját mutatja be:

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)
    
    ---

    A képernyőképen az alábbi értékek láthatók:

    - **&lt;ResourceGroupName>**: myresourcegroup0709. A paraméter két helyen jelenik meg.  Győződjön meg arról, hogy ugyanazt az értéket használja mindkét helyen.
    - **&lt;AzureLocation>**: eastus2
    - **&lt;DeployName>**: mydeployment0709
    - **&lt;TemplateFile>**: azuredeploy.json

    A képernyőképen a tárfiók neve a következő: *3tqebj3slyfyestandardsa*. 

7. Az alábbi parancssori felületi vagy PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott tárfiókot:

    # <a name="clitabcli"></a>[Parancssori felület](#tab/CLI)
    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```powershell
    Get-AzureRmStorageAccount -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>
    ```
    
    ---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

A rövid útmutató elsősorban az Azure-gyorssablonok közül egy meglévő sablon a Visual Studio Code-dal történő szerkesztését mutatja be. Emellett azt is megismerte, hogyan helyezheti üzembe a sablont az Azure Cloud Shellben a parancssori felület vagy a PowerShell használatával. Az Azure-gyorssablonok nem biztos, hogy minden tekintetben megfelelnek Önnek. A következő oktatóanyag azt mutatja be, hogyan keresheti meg a megfelelő információkat a sablonreferenciában titkosított Azure Storage-fiók létrehozásához.

> [!div class="nextstepaction"]
> [Titkosított tárfiók létrehozása](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
