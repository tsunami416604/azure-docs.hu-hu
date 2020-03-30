---
title: Feltétel használata sablonokban
description: Megtudhatja, hogyan helyezhet üzembe Azure-erőforrásokat feltételek alapján. Bemutatja, hogyan telepíthet egy új erőforrást, vagy hogyan használhat egy meglévő erőforrást.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f88f141257e8e614f62c7441c313002b5735116d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239194"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Oktatóanyag: Feltétel használata ARM-sablonokban

Megtudhatja, hogyan helyezheti üzembe az Azure-erőforrásokat feltételek alapján egy Azure Resource Manager (ARM) sablonban.

Az [erőforrások üzembehelyezési sorrendjének beállítását](./template-tutorial-create-templates-with-dependent-resources.md) ismertető oktatóanyagban egy virtuális gépet, egy virtuális hálózatot és egyéb függő erőforrásokat fog létrehozni, például egy tárfiókot. Ahelyett, hogy minden alkalommal egy új tárfiókot kellene létrehozni, megengedheti a felhasználóknak, hogy maguk döntsék el, új tárfiókot hoznak létre vagy egy meglévőt használnak. Ehhez egy további paramétert kell meghatároznia. Ha a paraméter értéke „új”, akkor a rendszer új tárfiókot hoz létre. Ellenkező esetben egy meglévő tárfiók a megadott nevet használja.

![Az Erőforrás-kezelő sablon feltételdiagramja](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon módosítása
> * A sablon üzembe helyezése
> * Az erőforrások eltávolítása

Ez az oktatóanyag csak a feltételek használatának alapvető forgatókönyvét tartalmazza. További információkért lásd:

* [Sablonfájl szerkezete: Feltétel](conditional-resource-deployment.md).
* [Erőforrás feltételes üzembe helyezése ARM-sablonban.](/azure/architecture/building-blocks/extending-templates/conditional-deploy)
* [Sablon függvény: Ha](./template-functions-logical.md#if).
* [Arm sablonok összehasonlító funkciói](./template-functions-comparison.md)

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
4. A sablon öt erőforrást határoz meg:

   * `Microsoft.Storage/storageAccounts`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
5. Válassza a **Fájlmentés**>**másként** lehetőséget, ha a fájl egy példányát az **azuredeploy.json**nevű számítógépre szeretné menteni.

## <a name="modify-the-template"></a>A sablon módosítása

A meglévő sablont két helyen kell módosítania:

* Adjon hozzá egy tárfióknév paramétert. A felhasználók megadhatnak egy új tárfióknevet vagy egy meglévő tárfiók nevét is.
* Adjon hozzá egy új, **newOrExisting** nevű paramétert. A központi telepítés ezt a paramétert használja annak meghatározásához, hogy új tárfiókot vagy meglévő tárfiókot hozzon létre.

A következő eljárással hajthatja végre a módosításokat:

1. Nyissa meg az **azuredeploy.json** fájlt a Visual Studio Code-ban.
2. Cserélje le a három **variable('storageAccountName')** **paraméterre('storageAccountName')** a teljes sablonban.
3. Távolítsa el az alábbi változódefiníciót:

    ![Az Erőforrás-kezelő sablon feltételdiagramja](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Adja hozzá a sablonhoz az alábbi két paramétert:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    A frissített paraméterdefiníció a következőképpen néz ki:

    ![Feltétel használata a Resource Managerben](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Adja hozzá a következő kódsort a tárfiók-definíció elejéhez.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A feltétel ellenőrzi a **newOrExisting** nevű paraméter értékét. Ha a paraméter értéke **new** (új), az üzemelő példány létrehozza a tárfiókot.

    A frissített tárfiók-definíció a következőképpen néz ki:

    ![Feltétel használata a Resource Managerben](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Frissítse a virtuálisgép-erőforrás-definíció **storageUri** tulajdonságát a következő értékkel:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Ez a módosítás szükséges, ha egy eltérő erőforráscsoport alá tartozó másik tárfiókot használ.

7. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse a [sablon telepítése a sablon](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) megnyitásához a felhőshell, és töltse fel a módosított sablont, majd futtassa a következő PowerShell-parancsfájlt a sablon üzembe helyezéséhez.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> Az üzembe helyezés meghiúsul, ha a **newOrExisting** paraméter értéke **new**, de a megadott névvel rendelkező tárfiók már létezik.

Próbáljon meg egy másik üzembe helyezést a **newOrExisting** "meglévő" beállítással, és adjon meg egy meglévő tárfiókot. Ha a művelet előtt szeretne létrehozni egy tárfiókot, tekintse meg a [tárfiók létrehozását](../../storage/common/storage-account-create.md) ismertető útmutatót.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Az erőforráscsoport törléséhez válassza a **Próbálja ki** a felhőrendszerhéj megnyitásához lehetőséget. A PowerShell-parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablaktáblájára, és válassza a **Beillesztés parancsot.**

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy olyan sablont állított össze, amellyel a felhasználók maguk dönthetik el, hogy új tárfiókot hoznak létre, vagy egy meglévőt használnak. Ha szeretné megtudni, hogyan kérheti le az Azure Key Vaultban tárolt titkos kódokat, és hogyan használhatja azokat jelszóként sablonok üzembe helyezésekor, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A Key Vault integrálása a sablonok üzembe helyezésekor](./template-tutorial-use-key-vault.md)
