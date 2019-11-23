---
title: Deploy VM extensions with template
description: Útmutató virtuális gépi bővítmények üzembe helyezéséhez Azure Resource Manager-sablonokkal.
author: mumian
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c18e5959a1ec52d9f10f27f456a466669e7b99c9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325376"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal

Ismerje meg, hogyan használhatja az [Azure-beli virtuális gépi bővítményeket](../virtual-machines/extensions/features-windows.md) Azure-beli virtuális gépeken az üzembe helyezés utáni konfigurációs és automatizálási feladatokhoz. Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. In this tutorial, you deploy a Custom Script extension from an Azure Resource Manager template to run a PowerShell script on a Windows VM.  A szkript telepíti a webkiszolgálót a virtuális gépen.

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

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./resource-manager-tutorial-use-key-vault.md). We also recommend that you update your password every three months.

## <a name="prepare-a-powershell-script"></a>PowerShell-szkript előkészítése

A PowerShell script with the following content is shared from [Github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

If you choose to publish the file to your own location, you must update the `fileUri` element in the template later in the tutorial.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Azure Quickstart Templates is a repository for Resource Manager templates. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. In Visual Studio Code, select **File** > **Open File**.
1. In the **File name** box, paste the following URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. To open the file, select **Open**.
    The template defines five resources:

   * **Microsoft.Storage/storageAccounts**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresses**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     It's helpful to get some basic understanding of the template before you customize it.

1. Save a copy of the file to your local computer with the name *azuredeploy.json* by selecting **File** > **Save As**.

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
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

For more information about this resource definition, see the [extension reference](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). A következők a fontosabb elemek:

* **name**: Mivel a bővítményerőforrás a virtuális gép objektum gyermekerőforrása, a nevének tartalmaznia kell a virtuális gép nevének előtagját. See [Set name and type for child resources](child-resource-name-type.md).
* **dependsOn**: Create the extension resource after you've created the virtual machine.
* **fileUris**: The locations where the script files are stored. If you choose not to use the provided location, you need to update the values.
* **commandToExecute**: This command invokes the script.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

For the deployment procedure, see the "Deploy the template" section of [Tutorial: Create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). We recommended that you use a generated password for the virtual machine administrator account. See this article's [Prerequisites](#prerequisites) section.

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

1. In the Azure portal, select the VM.
1. In the VM overview, copy the IP address by selecting **Click to copy**, and then paste it in a browser tab. The default Internet Information Services (IIS) welcome page opens:

![The Internet Information Services welcome page](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

When you no longer need the Azure resources you deployed, clean them up by deleting the resource group.

1. In the Azure portal, in the left pane, select **Resource group**.
2. In the **Filter by name** box, enter the resource group name.
3. Válassza ki az erőforráscsoport nevét.
    Six resources are displayed in the resource group.
4. In the top menu, select **Delete resource group**.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy virtuális gépet és egy virtuális gépi bővítményt. A bővítmény telepítette az IIS-webkiszolgálót a virtuális gépen. To learn how to use the Azure SQL Database extension to import a BACPAC file, see:

> [!div class="nextstepaction"]
> [Deploy SQL extensions](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
