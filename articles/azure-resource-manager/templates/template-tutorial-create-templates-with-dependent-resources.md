---
title: Sablon függő erőforrásokkal
description: Ismerje meg, hogyan hozhat létre több erőforrással ellátott Azure Resource Manager-sablont, valamint hogyan helyezheti üzembe azt az Azure Portal használatával.
author: mumian
ms.date: 04/10/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e589fa8ae5627746ec1f04e2098a7b592f00dc24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684937"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Oktatóanyag: Arm-sablonok létrehozása függő erőforrásokkal

Ismerje meg, hogyan hozhat létre egy Azure Resource Manager (ARM) sablont több erőforrás üzembe helyezéséhez és a telepítési sorrend konfigurálásához. A sablon létrehozása után üzembe helyezi a sablont az Azure Portalon a Cloud Shell használatával.

Az oktatóanyag során egy tárfiókot, egy virtuális gépet, egy virtuális hálózatot és néhány egyéb függő erőforrást fog létrehozni. Bizonyos erőforrások nem helyezhetők üzembe addig, amíg egy másik erőforrás létre nem lett hozva. Nem hozhat létre például virtuális gépet addig, amíg annak tárfiókja és hálózati adaptere létre nem lett hozva. Ezt a kapcsolatot úgy definiáljuk, hogy egy adott erőforrás más erőforrásokkal áll függőségi viszonyban. A Resource Manager kiértékeli az erőforrások közötti függőségeket, majd azokat függőségi sorrendben üzembe helyezi. Ha az erőforrások között nincs függőségi viszony, akkor a Resource Manager párhuzamosan helyezi üzembe azokat. További [információ: Az erőforrások ARM-sablonokban történő üzembe helyezésének sorrendjének meghatározása.](./define-resource-dependency.md)

![Erőforrás-kezelő sablonfüggő erőforrások telepítési sorrendjének diagramja](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon vizsgálata
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](use-vs-code-to-create-template.md)
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [Az Azure Key Vault integrálása az ARM-sablon üzembe helyezésében](./template-tutorial-use-key-vault.md)című témakörben olvashat. Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure QuickStart Templates az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása**>**fájl**lehetőséget.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Válassza a **Fájlmentés**>**másként** lehetőséget, ha a fájl egy példányát az **azuredeploy.json**nevű számítógépre szeretné menteni.

## <a name="explore-the-template"></a>A sablon vizsgálata

Amikor ebben a szakaszban a sablont vizsgálja, próbálja megválaszolni a következő kérdéseket:

* Hány Azure-erőforrás van meghatározva ebben a sablonban?
* Az egyik erőforrás egy Azure-tárfiók.  A definíció hasonlít a legutóbbi oktatóanyagban használtra?
* Hogyan tudja megkeresni a sablonban meghatározott erőforrásokhoz a sablonreferenciákat?
* Hogyan tudja megkeresni az erőforrások függőségeit?

1. A Visual Studio Code-ban csukja össze az elemeket mindaddig, amíg csak az első szintű és a **resources** (erőforrások) alatt lévő második szintű elemek lesznek láthatók:

    ![Visual Studio Code – Azure Resource Manager-sablonok](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    A sablon hat erőforrást határoz meg:

   * [**Microsoft.Storage/storageAccounts .**](/azure/templates/Microsoft.Storage/storageAccounts)
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     A sablon testreszabása előtt érdemes áttekinteni a sablonhivatkozást.

1. Bontsa ki az első erőforrást. Ez egy tárfiók. Hasonlítsa össze az erőforrás-definíciót a [sablonhivatkozással.](/azure/templates/Microsoft.Storage/storageAccounts)

    ![Visual Studio Code – Azure Resource Manager-sablonok, tárfiók-definíciók](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Bontsa ki a második erőforrást. Az erőforrástípus `Microsoft.Network/publicIPAddresses`. Hasonlítsa össze az erőforrás-definíciót a [sablonhivatkozással.](/azure/templates/microsoft.network/publicipaddresses)

    ![Visual Studio Code – Azure Resource Manager-sablonok, nyilvános IP-cím definíciója](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Bontsa ki a harmadik erőforrást. Az erőforrástípus `Microsoft.Network/networkSecurityGroups`. Hasonlítsa össze az erőforrás-definíciót a [sablonhivatkozással.](/azure/templates/microsoft.network/networksecuritygroups)

    ![Visual Studio Code Azure Resource Manager-sablonok hálózati biztonsági csoportdefiníciója](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Bontsa ki a negyedik erőforrást. Az erőforrástípus `Microsoft.Network/virtualNetworks`:

    ![Visual Studio Code Azure Resource Manager sablonok virtuális hálózat dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    A dependsOn elem lehetővé teszi, hogy egy adott erőforrást egy vagy több erőforrástól függőként lehessen meghatározni. Ez az erőforrás egy másik erőforrástól függ:

    * `Microsoft.Network/networkSecurityGroups`

1. Bontsa ki az ötödik erőforrást. Az erőforrástípus `Microsoft.Network/networkInterfaces`. Az erőforrás két másik erőforrástól függ:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Bontsa ki a hatodik erőforrást. Ez az erőforrás egy virtuális gép. Ez az erőforrás a következő két másik erőforrástól függ:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

A következő ábrán az erőforrások és az ehhez a sablonhoz tartozó függőségi adatok láthatók:

![Visual Studio Code – Azure Resource Manager-sablonok – függőségi ábra](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

A függőségek megadásával a Resource Manager már hatékonyan képes üzembe helyezni a megoldást. Mivel nincs közöttük függőség, a tárfiók, a nyilvános IP-cím és a virtuális hálózat párhuzamosan lesz létrehozva. A nyilvános IP-cím és a virtuális hálózat után a hálózati adapter üzembe helyezése következik. A Resource Manager azt követően helyezi üzembe helyezve, hogy az összes többi erőforrás üzembe helyezése megtörtént.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kövesse a [sablon üzembe helyezése](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) a cloud shell megnyitásához és töltse fel a módosított sablont.

1. Futtassa a következő PowerShell-parancsfájlt a sablon központi telepítéséhez.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Az alábbi PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott virtuális gépet:

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $resourceGroupName = "${projectName}rg"
    $vmName = "SimpleWinVM"

    Get-AzVM -Name $vmName -ResourceGroupName $resourceGroupName
    
    Write-Host "Press [ENTER] to continue ..."
    ```

    A sablonban szereplő virtuálisgépnév (**SimpleWinVM**) nem módosítható.

1. RDP-kapcsolattal csatlakozzon a virtuális géphez, hogy ellenőrizze, sikeresen létrejött-e a virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét. Az erőforráscsoportban összesen hat erőforrás jelenik meg.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott és üzembe helyezett egy virtuális gép, egy virtuális hálózat és a függő erőforrások létrehozására szolgáló sablont. A központi telepítési parancsfájlok üzembe helyezés előtti/utáni műveletek végrehajtásáról a következő témakörökből megtudhatja:

> [!div class="nextstepaction"]
> [Üzembehelyezési szkript használata](./template-tutorial-deployment-script.md)
