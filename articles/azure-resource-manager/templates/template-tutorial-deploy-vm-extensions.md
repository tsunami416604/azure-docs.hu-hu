---
title: Virtuálisgép-bővítmények üzembe helyezése sablonnal
description: Útmutató virtuális gépi bővítmények üzembe helyezéséhez Azure Resource Manager-sablonokkal.
author: mumian
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 30b7aeaed0bfc2621cb2c71ab3f5e618771a1c26
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250090"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal

Ismerje meg, hogyan használhatja az [Azure-beli virtuális gépi bővítményeket](../../virtual-machines/extensions/features-windows.md) Azure-beli virtuális gépeken az üzembe helyezés utáni konfigurációs és automatizálási feladatokhoz. Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. Ebben az oktatóanyagban egyéni parancsfájl-bővítményt telepít egy Azure Resource Manager sablonból egy PowerShell-parancsfájl Windows rendszerű virtuális gépen való futtatásához.  A szkript telepíti a webkiszolgálót a virtuális gépen.

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

* Visual Studio Code a Resource Manager-eszközök bővítménnyel. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](use-vs-code-to-create-template.md).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./template-tutorial-use-key-vault.md). Azt is javasoljuk, hogy három havonta frissítse a jelszavát.

## <a name="prepare-a-powershell-script"></a>PowerShell-szkript előkészítése

A [githubon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)a következő tartalommal rendelkező PowerShell-szkriptek vannak megosztva:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha úgy dönt, hogy a fájlt a saját helyére teszi közzé, az oktatóanyag későbbi részében frissítenie kell a `fileUri` elemet a sablonban.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure Gyorsindítás sablonjai a Resource Manager-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **fájl** > fájl **megnyitása**lehetőséget.
1. Illessze be a következő URL-címet a **fájlnév** mezőbe: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. A fájl megnyitásához válassza a **Megnyitás**lehetőséget.
    A sablon öt erőforrást határoz meg:

   * **Microsoft. Storage/storageAccounts**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft. Network/nyilvános IP**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft. Network/virtualNetworks**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft. Network/networkInterfaces**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft. számítási/virtualMachines**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     A Testreszabás előtt hasznos lehet a sablon alapvető megismerése.

1. Mentse a fájl egy másolatát a helyi számítógépre a *azuredeploy. JSON* néven. Ehhez válassza a **fájl** > **Mentés másként**lehetőséget.

## <a name="edit-the-template"></a>A sablon szerkesztése

Adjon hozzá a meglévő sablonhoz egy virtuális gépi bővítmény erőforrást a következő tartalommal:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2018-06-01",
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
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Az erőforrás-definícióval kapcsolatos további információkért tekintse meg a [bővítmények referenciáját](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). A következők a fontosabb elemek:

* **name**: Mivel a bővítményerőforrás a virtuális gép objektum gyermekerőforrása, a nevének tartalmaznia kell a virtuális gép nevének előtagját. Lásd: [a gyermek erőforrások nevének és típusának beállítása](child-resource-name-type.md).
* **dependsOn**: hozza létre a bővítmény erőforrást a virtuális gép létrehozása után.
* **fileUris**: a parancsfájlokat tároló hely. Ha úgy dönt, hogy nem használja a megadott helyet, frissítenie kell az értékeket.
* **commandToExecute**: Ez a parancs meghívja a parancsfájlt.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az üzembe helyezési eljárással kapcsolatban tekintse meg a "sablon központi telepítése" című részt a következő [oktatóanyagban: Azure Resource Manager sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Javasoljuk, hogy a virtuális gép rendszergazdai fiókjához generált jelszót használjon. Tekintse meg ezt a cikk [előfeltételeit](#prerequisites) ismertető szakaszt.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. A Azure Portal válassza ki a virtuális gépet.
1. A virtuális gép áttekintése lapon másolja ki az IP-címet a **Másolás gombra kattintva**, majd illessze be egy böngészőbe. Megnyílik az alapértelmezett Internet Information Services (IIS) Kezdőlap:

![A Internet Information Services kezdőlapja](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az üzembe helyezett Azure-erőforrásokra, törölje azokat az erőforráscsoport törlésével.

1. A Azure Portal bal oldali ablaktábláján válassza az **erőforráscsoport**elemet.
2. A **szűrés név alapján** mezőbe írja be az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
    Az erőforráscsoporthoz hat erőforrás jelenik meg.
4. A felső menüben válassza az **erőforráscsoport törlése**elemet.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy virtuális gépet és egy virtuális gépi bővítményt. A bővítmény telepítette az IIS-webkiszolgálót a virtuális gépen. A következő témakörből megtudhatja, hogyan használhatja a Azure SQL Database bővítményt egy BACPAC-fájl importálásához:

> [!div class="nextstepaction"]
> [SQL-bővítmények üzembe helyezése](./template-tutorial-deploy-sql-extensions-bacpac.md)
