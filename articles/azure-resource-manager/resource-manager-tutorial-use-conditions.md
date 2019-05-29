---
title: Feltételek használata az Azure Resource Manager-sablonokban | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe Azure-erőforrásokat feltételek alapján.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0d27eef8ee2c70449bacaced0de89bdc5e6989ff
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357808"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Oktatóanyag: Feltétel használatához az Azure Resource Manager-sablonokban

Megtudhatja, hogyan helyezhet üzembe Azure-erőforrásokat feltételek alapján.

Az [erőforrások üzembehelyezési sorrendjének beállítását](./resource-manager-tutorial-create-templates-with-dependent-resources.md) ismertető oktatóanyagban egy virtuális gépet, egy virtuális hálózatot és egyéb függő erőforrásokat fog létrehozni, például egy tárfiókot. Ahelyett, hogy minden alkalommal egy új tárfiókot kellene létrehozni, megengedheti a felhasználóknak, hogy maguk döntsék el, új tárfiókot hoznak létre vagy egy meglévőt használnak. Ehhez egy további paramétert kell meghatároznia. Ha a paraméter értéke „új”, akkor a rendszer új tárfiókot hoz létre. Ellenkező esetben a megadott nevű meglévő storage-fiókot használja.

![Resource Manager sablon használata az állapot diagramja](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon módosítása
> * A sablon üzembe helyezése
> * Az erőforrások eltávolítása

Ebben az oktatóanyagban csak egy foglalkozik alapvető feltételek használatával. További információkért lásd:

* [Sablon fájlstruktúra: A feltétel](./resource-group-authoring-templates.md#condition).
* [Erőforrás feltételes üzembe helyezése az Azure Resource Manager-sablon](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Sablon függvény: Ha](./resource-group-template-functions-logical.md#if).
* [Összehasonlító függvények az Azure Resource Manager-sablonok](./resource-group-template-functions-comparison.md)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd: [oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
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
5. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.

## <a name="modify-the-template"></a>A sablon módosítása

A meglévő sablont két helyen kell módosítania:

* Adjon hozzá egy tárfióknév paramétert. A felhasználók megadhatnak egy új tárfióknevet vagy egy meglévő tárfiók nevét is.
* Adjon hozzá egy új, **newOrExisting** nevű paramétert. Az üzemelő példány e paraméter alapján határozza meg, hogy mikor hozzon létre új tárfiókot, és mikor használjon egy meglévőt.

A következő eljárással hajthatja végre a módosításokat:

1. Nyissa meg az **azuredeploy.json** fájlt a Visual Studio Code-ban.
2. Cserélje le a három **variables('storageAccountName')** a **parameters('storageAccountName')** a teljes sablonban.
3. Távolítsa el az alábbi változódefiníciót:

    ![Resource Manager sablon használata az állapot diagramja](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

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

    ![Feltétel használata a Resource Managerben](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Adja hozzá a következő kódsort a tárfiók-definíció elejéhez.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    A feltétel ellenőrzi a **newOrExisting** nevű paraméter értékét. Ha a paraméter értéke **new** (új), az üzemelő példány létrehozza a tárfiókot.

    A frissített tárfiók-definíció a következőképpen néz ki:

    ![Feltétel használata a Resource Managerben](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Frissítés a **storageUri** tulajdonság a virtuális gép erőforrás-definíció a következő értékkel:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Ez a módosítás szükséges, ha egy eltérő erőforráscsoport alá tartozó másik tárfiókot használ.

7. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse a [a sablon üzembe helyezéséhez](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) , a Cloud shellt, és töltse fel a módosított sablont, és futtassa a következő PowerShell-parancsfájl a sablon üzembe helyezéséhez.

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

Végezze el az üzembe helyezést úgy is, hogy a **newOrExisting** paramétert értékét „existing” (meglévő) értékre állítja, és megad egy létező tárfiókot. Ha a művelet előtt szeretne létrehozni egy tárfiókot, tekintse meg a [tárfiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető útmutatót.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Az erőforráscsoport törléséhez válassza ki **kipróbálás** megnyitása a Cloud shellben. Illessze be a PowerShell-parancsfájlt, kattintson a jobb gombbal a rendszerhéj ablaktáblán, és válassza **illessze be**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy olyan sablont állított össze, amellyel a felhasználók maguk dönthetik el, hogy új tárfiókot hoznak létre, vagy egy meglévőt használnak. Ha szeretné megtudni, hogyan kérheti le az Azure Key Vaultban tárolt titkos kódokat, és hogyan használhatja azokat jelszóként sablonok üzembe helyezésekor, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [A Key Vault integrálása a sablonok üzembe helyezésekor](./resource-manager-tutorial-use-key-vault.md)
