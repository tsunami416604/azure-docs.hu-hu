---
title: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába | Microsoft Docs
description: Megtudhatja, hogyan használható az Azure Key Vault biztonságos paraméterértékek megadására a Resource Manager-sablon üzembe helyezése során
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 0d78e6eaca708073c3a216507b320fe8783a25b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239243"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>Oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon telepítése

Ismerje meg, hogyan lehet beolvasni a titkos kulcsok Azure Key vault és a titkos kulcsok paraméterként átadni a Resource Manager üzembe helyezése során. Az érték sosem hagyja el, mert csak hivatkozhat a key vault azonosítója. További információért lásd azt a cikket, amely azzal foglalkozik, hogyan lehet [használni az Azure Key Vaultot biztonságos paraméterértékek megadásához az üzembe helyezés során](./resource-manager-keyvault-parameter.md).

Az a [erőforrás telepítési sorrendet,](./resource-manager-tutorial-create-templates-with-dependent-resources.md) az oktatóanyagban egy virtuális gépet hoz létre. Meg kell adnia a virtuális gép rendszergazdai felhasználónevét és jelszavát. A jelszó megadása helyett a jelszót az Azure Key Vault előre tárolhatja, és ezután testre szabhatja a sablont a jelszó lekérése a kulcstartóból a telepítés során.

![Resource Manager sablon Key Vault integration diagramja](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Key vault előkészítése
> * Gyorsindítási sablon megnyitása
> * A paraméterfájl szerkesztése
> * A sablon üzembe helyezése
> * Az üzembe helyezés ellenőrzése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Ellenőrizze, hogy a létrejött jelszó megfelel-e a virtuális gép jelszókövetelményeinek. Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. A virtuális gépek jelszókövetelményeiért tekintse át [a virtuális gépek létrehozásakor érvényesülő jelszókövetelményeket](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) ismertető szakaszt.

## <a name="prepare-a-key-vault"></a>Key vault előkészítése

Ebben a szakaszban hozzon létre egy kulcstartót és a titkos kód hozzáadása a kulcstartóhoz, hogy a sablon telepítésekor lehet lekérdezni a titkos kulcsot. Számos módon hozzon létre egy kulcstartót. Ebben az oktatóanyagban az Azure PowerShell használatával üzembe helyezése egy [Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ez a sablon:

* A key vault létrehozása a `enabledForTemplateDeployment` tulajdonság lehetővé teszi. Ez a tulajdonság igaznak kell lennie, a sablon üzembe helyezési folyamat férjenek hozzá a meghatározott a kulcstartó titkos kulcsait.
* Titkos kód hozzáadása a kulcstartóhoz.  Ez a titkos kód tárolja a virtuális gép rendszergazdájának jelszavát.

> [!NOTE]
> Ha (mint a felhasználót, hogy a virtuálisgép-sablon üzembe helyezése) nem a tulajdonos vagy a közreműködői a key vault, a tulajdonos vagy közreműködő a key vault hozzáférést kell biztosítania, az a Microsoft.KeyVault/vaults/deploy/action engedéllyel a kulcstartó. További információért lásd azt a cikket, amely azzal foglalkozik, hogyan lehet [használni az Azure Key Vaultot biztonságos paraméterértékek megadásához az üzembe helyezés során](./resource-manager-keyvault-parameter.md).

A következő PowerShell-szkript futtatásához válassza **kipróbálás** megnyitása a Cloud shellben. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéj ablaktáblán, és válassza **illessze be**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue
```

Kódrészletek néhány fontos információkat:

* Az erőforráscsoport neve a projekt nevére a **rg** hozzáfűzve. A könnyebb [távolítsa el az ebben az oktatóanyagban létrehozott erőforrásokat](#clean-up-resources), projekt nevét és az erőforrás-csoport neve, mikor használja, [a következő sablon üzembe helyezéséhez](#deploy-the-template).
* Az alapértelmezett név a titkos kód neve **vmAdminPassword**. Szoftveresen kötött a sablonban.
* Ahhoz, a sablon beolvasni a titkos kulcsot, engedélyeznie kell a hozzáférési házirend nevű **engedélyezze a hozzáférést az Azure Resource Manager-sablon telepítése** a kulcstartó. Ez a szabályzat engedélyezve van a sablonban. A hozzáférési házirenddel kapcsolatos további információkért lásd: [kulcstartók és titkos kódok üzembe helyezése](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

A sablon tartalmaz egy kimeneti értéket nevű **keyVaultId**. Jegyezze fel az értéket. Erre az azonosítóra szüksége lesz a virtuális gép üzembe helyezésekor. Az erőforrás azonosítójának formátuma:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Másolja és illessze be az Azonosítót, amikor azonosítója előfordulhat, hogy több sorba tördelhető. A sorok egyesíteni kell, és a felesleges szóközöket.

Az üzembe helyezés ellenőrzéséhez futtassa a következő PowerShell-parancsot a azonos rendszerhéj panelen beolvasni a titkos kulcsot szövegként. A parancs csak akkor működik ugyanabban a rendszerhéj-munkamenetben, mert egy változót az előző PowerShell-parancsprogram meghatározott $keyVaultName használ.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Most már előkészítette a key vault és a egy titkos kulcsot, a következő szakaszok bemutatják, hogyan szabhatja testre a meglévő sablont, és a titkos kód beolvasása az üzembe helyezés során.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt. Ez ugyanaz a forgatókönyv, amelyet az [Oktatóanyag: Függő erőforrásokkal ellátott Azure Resource Manager-sablonok létrehozása](./resource-manager-tutorial-create-templates-with-dependent-resources.md) részben használt.
4. A sablon öt erőforrást határoz meg:

   * `Microsoft.Storage/storageAccounts`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
5. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
6. Ismételje meg az 1–4. lépést a következő URL-cím megnyitásához, majd mentse a fájlt **azuredeploy.parameters.json** néven.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>A paraméterfájl szerkesztése

A sablonfájlt nem kell módosítania.

1. Ha még nincs megnyitva, nyissa meg az **azuredeploy.parameters.json** fájlt a Visual Studio Code-ban.
2. Frissítse az **adminPassword** paramétert a következőre:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Cserélje le a értékét **azonosító** az utolsó eljárás során létrehozott a key vault erőforrás-azonosító.

    ![A Key Vault és a Resource Manager-sablon integrációja, a virtuális gép üzembehelyezési paraméterfájljai](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. Adja meg a következők értékét:

    * **adminUsername**: a virtuális gép rendszergazdai fiókjának neve.
    * **dnsLabelPrefix**: a dnsLabelPrefix neve.

    Látható egy példa az előző képernyőképen látható.

4. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse [a sablon üzembe helyezését](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) ismertető témakörben található utasításokat a sablon üzembe helyezéséhez. Fel kell tölteni a mindkét **azuredeploy.json** és **azuredeploy.parameters.json** a Cloud shellt, és majd használja a következő PowerShell-parancsfájlt a sablon üzembe helyezéséhez:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

A sablon központi telepítése esetén használja a key vault ugyanabban az erőforráscsoportban. Ez egyszerűbbé teszi az erőforrások törlését. Kettő helyett csak egy erőforráscsoportot kell majd törölnie.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Miután sikeresen telepítette a virtuális gép, tesztelje a bejelentkezést, a key vaultban tárolt jelszó segítségével.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza az **Erőforráscsoportok**/**SajátErőforráscsoportNeve>** /**simpleWinVM** lehetőséget.
3. Válassza a **csatlakozás** lehetőséget a lap tetején.
4. Válassza ki **RDP-fájl letöltése** és kövesse az utasításokat követve jelentkezzen be a virtuális géppel a key vaultban tárolt jelszó segítségével.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban lekért egy titkos kódot az Azure Key Vaultból, amelyet felhasznált a sablon üzembe helyezése során.  Ha meg szeretné tudni, hogyan hozhat létre csatolt sablonokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Csatolt sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md)
