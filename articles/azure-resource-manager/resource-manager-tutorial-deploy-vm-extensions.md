---
title: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal | Microsoft Docs
description: Útmutató virtuális gépi bővítmények üzembe helyezéséhez Azure Resource Manager-sablonokkal.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a1df94c7157823a4d7dcdcf652b1c7c926f4dbba
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406507"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Oktatóanyag: Az Azure Resource Manager-sablonok virtuálisgép-bővítmények telepítése

Ismerje meg, hogyan használhatja az [Azure-beli virtuális gépi bővítményeket](../virtual-machines/extensions/features-windows.md) Azure-beli virtuális gépeken az üzembe helyezés utáni konfigurációs és automatizálási feladatokhoz. Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. Ebben az oktatóanyagban egy egyéni szkriptek bővítménye az Azure Resource Manager-sablon, egy PowerShell-parancsprogram futtatásához a Windows virtuális gép üzembe helyezése.  A szkript telepíti a webkiszolgálót a virtuális gépen.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * PowerShell-szkript előkészítése
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * Az üzemelő példány ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) a Resource Manager Tools bővítménnyel. Lásd: [a bővítmény telepítése](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd: [oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md). Azt javasoljuk, hogy frissíti a jelszó háromhavonta.

## <a name="prepare-a-powershell-script"></a>PowerShell-szkript előkészítése

Egy PowerShell-parancsfájlt az alábbi tartalommal megosztott egy [nyilvános hozzáférés az Azure storage-fiók](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha a fájl közzététele a saját helyét, frissítenie kell a `fileUri` elem az oktatóanyag későbbi részében a sablonban.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure gyorsindítási sablonok tárháza a Resource Manager-sablonok. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. Válassza ki a Visual Studio Code- **fájl** > **fájl megnyitása**.
1. Az a **Fájlnév** mezőbe illessze be a következő URL-címe: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. A fájl megnyitásához válassza **nyissa meg a**.  
    A sablon határozza meg, hogy öt erőforrások:

    * **Microsoft.Storage/storageAccounts**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * **Microsoft.Network/publicIPAddresses**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * **Microsoft.Network/virtualNetworks**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * **Microsoft.Network/networkInterfaces**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * **Microsoft.Compute/virtualMachines**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Hasznos lehet néhány alapvető ismeretekkel a sablon lekérni, mielőtt testre szabni.

1. Mentse a fájl másolatát nevű a helyi számítógépen *azuredeploy.json* kiválasztásával **fájl** > **Mentés másként**.

## <a name="edit-the-template"></a>A sablon szerkesztése

Adjon hozzá a meglévő sablonhoz egy virtuális gépi bővítmény erőforrást a következő tartalommal:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Az erőforrás-definíció kapcsolatos további információkért lásd: a [kiterjesztéshivatkozásának](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). A következők a fontosabb elemek:

* **Név**: A bővítmény erőforrás egy gyermek-erőforrás a virtuális gép objektum, mert a név rendelkeznie kell a virtuális gépnév előtagja. Tekintse meg a [gyermekerőforrásokkal](./resource-group-authoring-templates.md#child-resources) kapcsolatos részt.
* **dependsOn**: A virtuális gép létrehozása után a bővítmény erőforrás létrehozása
* **fileUris**: A helyek, a parancsfájl-fájlok tárolására. Ha nem kíván használni a megadott hely, az értékek frissíteni szeretné.
* **commandToExecute**: Ez a parancs a parancsfájl meghívja.  

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A telepítési eljárás "A sablon üzembe helyezése" című szakaszában talál [oktatóanyag: Az Azure Resource Manager-sablonok létrehozása a tőle függő erőforrások](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Azt javasoljuk, hogy a virtuális gép rendszergazdai fiók használni létrehozott jelszót. Ebben a cikkben [Előfeltételek](#prerequisites) szakaszban.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. Az Azure Portalon válassza ki a virtuális Gépet.
1. A virtuális gép áttekintésében másolja ki az IP-cím kiválasztásával **kattintson a másoláshoz**, majd illessze be a böngészőlapot.  
   Az Internet Information Services (IIS) alapértelmezett üdvözlő oldal megnyitása:

![Az Internet Information Services kezdőlapjának](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure-erőforrások üzembe helyezett, megtisztítsa azokat az erőforráscsoport törlésével.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **erőforráscsoport**.
2. Az a **Szűrés név alapján** mezőbe írja be az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  
    Hat erőforrások az erőforráscsoportban lévő jelennek meg.
4. A felső menüben válassza ki a **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy virtuális gépet és egy virtuális gépi bővítményt. A bővítmény telepítette az IIS-webkiszolgálót a virtuális gépen. BACPAC-fájl importálása az Azure SQL Database-bővítmény használatával kapcsolatban lásd:

> [!div class="nextstepaction"]
> [SQL-bővítmények telepítése](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
