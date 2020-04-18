---
title: Virtuálisgép-bővítmények telepítése sablonnal
description: Útmutató virtuális gépi bővítmények üzembe helyezéséhez Azure Resource Manager-sablonokkal.
author: mumian
ms.date: 04/16/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 280b4a9775346c719e82d1fef4162fa6ea666798
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616887"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Oktatóanyag: Virtuálisgép-bővítmények üzembe helyezése ARM-sablonokkal

Ismerje meg, hogyan használhatja az [Azure-beli virtuális gépi bővítményeket](../../virtual-machines/extensions/features-windows.md) Azure-beli virtuális gépeken az üzembe helyezés utáni konfigurációs és automatizálási feladatokhoz. Az Azure-beli virtuális gépekhez számos különböző virtuális gépi bővítmény érhető el. Ebben az oktatóanyagban egy Egyéni parancsfájl-bővítményt telepít egy Azure Resource Manager (ARM) sablonból egy PowerShell-parancsfájl futtatásához egy Windows virtuális rendszeren.  A szkript telepíti a webkiszolgálót a virtuális gépen.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * PowerShell-szkript előkészítése
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](use-vs-code-to-create-template.md)
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [Az Azure Key Vault integrálása az ARM-sablon üzembe helyezésében](./template-tutorial-use-key-vault.md)című témakörben olvashat. Azt is javasoljuk, hogy háromhavonta frissítse jelszavát.

## <a name="prepare-a-powershell-script"></a>PowerShell-szkript előkészítése

Használhat inline PowerShell-parancsfájlt vagy parancsfájlt.  Ez az oktatóanyag bemutatja, hogyan kell használni a parancsfájlokat. A Következő tartalommal rendelkező PowerShell-parancsfájlok meg vannak osztva a [GitHubról:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha úgy dönt, hogy közzéteszi a fájlt `fileUri` a saját helyére, frissítse az elemet a sablon később az oktatóanyagban.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure quickstart sablonok egy tárház ARM sablonok. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio-kódban válassza a **Fájlmegnyitása** > **fájl**lehetőséget.
1. A **Fájlnév** mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. A fájl megnyitásához válassza a **Megnyitás**gombot.
    A sablon öt erőforrást határoz meg:

   * [**Microsoft.Storage/storageAccounts .**](/azure/templates/Microsoft.Storage/storageAccounts)
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     A sablon testreszabása előtt hasznos lehet a sablon alapvető ismerete.

1. Mentse a fájl egy példányát a helyi számítógépre az *azuredeploy.json* névvel a **Fájl** > **mentés másként**lehetőség kiválasztásával.

## <a name="edit-the-template"></a>A sablon szerkesztése

Adjon hozzá a meglévő sablonhoz egy virtuális gépi bővítmény erőforrást a következő tartalommal:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
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

Az erőforrás-definícióról további információt a [bővítmény hivatkozási útmutatójában talál.](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions) A következők a fontosabb elemek:

* **name**: Mivel a bővítményerőforrás a virtuális gép objektum gyermekerőforrása, a nevének tartalmaznia kell a virtuális gép nevének előtagját. Lásd: [Név és típus beállítása a gyermekerőforrásokhoz](child-resource-name-type.md).
* **dependsOn**: A bővítmény erőforrás létrehozása a virtuális gép létrehozása után.
* **fileUris**: A helyek, ahol a parancsfájlok tárolódnak. Ha úgy dönt, hogy nem használja a megadott helyet, frissítenie kell az értékeket.
* **commandToExecute**: Ez a parancs meghívja a parancsfájlt.

A szövegközi parancsfájl használatához távolítsa el **a fileUris parancsot,** és frissítse a **CommandToExecute** parancsot a következőre:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Ez a szövegközi szkript is frissíti az iisstart.html tartalmat.

Meg kell nyitnia a HTTP-portot is, hogy hozzáférhessen a webkiszolgálóhoz.

1. Keresse meg a **securityRules** elemet a sablonban.
1. Adja hozzá a következő szabályt az **alapértelmezett-allow-3389**értékhez.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A telepítési eljárásról az Oktatóanyag "A sablon telepítése" című szakaszában [olvashat: ARM-sablonok létrehozása függő erőforrásokkal.](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) Azt javasoljuk, hogy a virtuális gép rendszergazdai fiókjához hozzon létre jelszót. Lásd a cikk [előfeltételei](#prerequisites) című szakaszát.

A Cloud Shell, futtassa a következő parancsot a virtuális gép nyilvános IP-címének lekéréséhez:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Illessze be az IP-címet egy webböngészőbe. Megnyílik az internetinformációs szolgáltatások (IIS) alapértelmezett üdvözlőlapja:

![Az Internet Information Services üdvözlőlapja](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az üzembe helyezett Azure-erőforrásokra, tisztítsa meg őket az erőforráscsoport törlésével.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Erőforráscsoport lehetőséget.**
2. A **Név szerint szűrés** mezőbe írja be az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
    Hat erőforrás jelenik meg az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy virtuális gépet és egy virtuális gépi bővítményt. A bővítmény telepítette az IIS-webkiszolgálót a virtuális gépen. Ha meg szeretné tudni, hogyan importálhat BACPAC-fájlt az Azure SQL Database-bővítmény használatával:

> [!div class="nextstepaction"]
> [SQL-bővítmények üzembe helyezése](./template-tutorial-deploy-sql-extensions-bacpac.md)
