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
ms.openlocfilehash: b91a3488750795e8ee9df6602bb2b3df8a9b08ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67436575"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Oktatóanyag: Integrálhatja az Azure Key Vault a Resource Manager-sablon üzembe helyezése

Útmutató egy Azure key vault titkos kódok lekérése, és adja át a titkos kulcsok meg paraméterként, az Azure Resource Manager telepítésekor. A paraméter értéke sosem hagyja el, mert hivatkozik csak annak a key vault azonosítója. További információkért lásd: [használata Azure Key Vaultban történő biztonságos paraméter értéke továbbítása üzembe helyezés során](./resource-manager-keyvault-parameter.md).

Az a [erőforrás telepítési sorrendet,](./resource-manager-tutorial-create-templates-with-dependent-resources.md) az oktatóanyagban létrehozott egy virtuális gépet (VM). Meg kell adnia a virtuális gép rendszergazdai felhasználónevét és jelszavát. A jelszó megadása helyett a jelszót a egy Azure key vault előre tárolhatja, és ezután testre szabhatja a sablont a jelszó lekérése a kulcstartóból a telepítés során.

![Diagram integrációja a key vault Resource Manager-sablon megjelenítése](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

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

* [A Visual Studio Code](https://code.visualstudio.com/) együtt a [Resource Manager Tools bővítmény](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* A biztonság növelése érdekében használja a virtuális gép rendszergazdai fiók egy létrehozott jelszót. Íme egy példa egy jelszó létrehozásához:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Győződjön meg arról, hogy a létrehozott jelszó megfelel-e a virtuális gép jelszava követelményeknek. Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. A virtuális gép jelszava követelmények, lásd: [milyen jelszó követelmények vonatkoznak, ha létrehoz egy virtuális Gépet?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Key vault előkészítése

Ebben a szakaszban hozzon létre egy kulcstartót, és adja hozzá a titkos kulcs, hogy a sablon telepítésekor lehet lekérdezni a titkos kulcsot. Számos módon hozzon létre egy kulcstartót. Ebben az oktatóanyagban az Azure PowerShell használatával üzembe helyezése egy [Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Ez a sablon a következőket teszi:

* A key vault hoz létre a `enabledForTemplateDeployment` tulajdonság engedélyezve van. Ez a tulajdonság kell *igaz* a sablon üzembe helyezés előtt folyamat férhet hozzá a titkos kulcsok a key vaultban definiált.
* Titkos kód hozzáadása a key vaultban. A titkos kulcsot tárol a virtuális gép rendszergazdai jelszót.

> [!NOTE]
> A felhasználó üzembe helyezése virtuálisgép-sablont, ha Ön nem tulajdonosa vagy Közreműködője a key vaulthoz, mint a tulajdonosi vagy közreműködői hozzáférést kell biztosítania, a *Microsoft.KeyVault/vaults/deploy/action* oszlopengedélye a key vaultban. További információkért lásd: [használata Azure Key Vaultban történő biztonságos paraméterérték továbbítása üzembe helyezés során](./resource-manager-keyvault-parameter.md).

A következő Azure PowerShell-szkript futtatásához válassza **kipróbálás** Azure Cloud Shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéj ablaktáblán, és válassza **illessze be**.

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

> [!IMPORTANT]
> * Az erőforráscsoport neve a projekt nevére, de **rg** rá hozzáfűzve. A könnyebb [távolítsa el az erőforrásokat, ebben az oktatóanyagban létrehozott](#clean-up-resources), projekt nevét és az erőforrás-csoport neve, mikor használja, [a következő sablon üzembe helyezéséhez](#deploy-the-template).
> * Az alapértelmezett neve a titkos kulcs **vmAdminPassword**. Ez azt szoftveresen kötött a sablonban.
> * Ahhoz, hogy a sablon beolvasni a titkos kulcsot, engedélyeznie kell egy úgynevezett "Engedélyezze a hozzáférést az Azure Resource Manager sablon telepítése" a kulcstartó hozzáférési szabályzatot. Ez a szabályzat engedélyezve van a sablonban. A hozzáférési házirenddel kapcsolatos további információkért lásd: [kulcstartók és titkos kódok üzembe helyezése](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

A sablon tartalmaz egy kimeneti értéket, nevű *keyVaultId*. Jegyezze fel az azonosító értéket későbbi használatra, a virtuális gép telepítésekor. Az erőforrás-azonosító formátuma a következő:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Másolja és illessze be az Azonosítót, akkor előfordulhat, hogy tördelhető több sort. Egyesítse a sorokat, és a felesleges szóközöket.

Az üzembe helyezés ellenőrzéséhez futtassa a következő PowerShell-parancsot a azonos rendszerhéj panelen beolvasni a titkos kulcsot szövegként. A parancs csak a azonos rendszerhéj-munkamenetben működik, mert használ, a változó *$keyVaultName*, amely az előző PowerShell-parancsprogram van definiálva.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Most, hogy előkészítette a key vaulttal és a egy titkos kulcsot. A következő szakaszok bemutatják, hogyan szabhatja testre a meglévő sablont, és a titkos kód beolvasása az üzembe helyezés során.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure gyorsindítási sablonok tárháza a Resource Manager-sablonok. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Ebben az oktatóanyagban használt sablon neve [egyszerű Windows virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. Válassza ki a Visual Studio Code- **fájl** > **fájl megnyitása**.

1. Az a **Fájlnév** mezőbe illessze be a következő URL-címe:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt. A forgatókönyv akkor ugyanaz, mint a használt [oktatóanyag: Az Azure Resource Manager-sablonok létrehozása a tőle függő erőforrások](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   A sablon határozza meg, hogy öt erőforrások:

   * `Microsoft.Storage/storageAccounts`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   Hasznos rendelkezik néhány alapvető ismeretekkel a sablont, mielőtt testre szabni.

1. Válassza ki **fájl** > **Mentés másként**, majd mentse a fájl másolatát nevű a helyi számítógépen *azuredeploy.json*.

1. Ismételje meg az 1-3, nyissa meg a következő URL-címet, és mentse a fájlt az *azuredeploy.parameters.json*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>A paraméterfájl szerkesztése

A sablonfájlt nem kell módosítania.

1. Nyissa meg a Visual Studio Code- *azuredeploy.parameters.json* Ha ezt még nem nyílt meg.
1. Frissítés a `adminPassword` paramétert:

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
    > Cserélje le az értéket a **azonosító** az erőforrás-azonosító, a kulcstároló, amelyet az előző eljárás során hozott létre.

    ![a key vault és a Resource Manager sablon virtuális gép üzembehelyezési paraméterfájl integrálása](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Frissítse a következő értékeket:

    * **AdminUsername**: A virtuális gép rendszergazdai fiókjának nevét.
    * **dnsLabelPrefix**: Írja be a dnsLabelPrefix értéket.

    Nevek példákért tekintse meg az előző képen.

1. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse a [helyezheti üzembe a sablont](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Töltse fel is *azuredeploy.json* és *azuredeploy.parameters.json* és a Cloud Shell majd használja a következő PowerShell-parancsfájlt a sablon üzembe helyezéséhez:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

A sablon üzembe helyezéséhez, használja ugyanazt az erőforráscsoportot, amely a key vaultban használt. Ez a megközelítés megkönnyíti, hogy erőforrásokat, mert csak egy erőforráscsoportot, két helyett törölni kell.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Miután sikeresen telepítette a virtuális gép, a bejelentkezési hitelesítő adatok tesztelése a jelszót, amelyet a key vaultban.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. Válassza ki **erőforráscsoportok** >  **\<*YourResourceGroupName*>**  > **simpleWinVM** .
1. Válassza ki **csatlakozás** tetején.
1. Válassza ki **RDP-fájl letöltése**, majd kövesse az utasításokat követve jelentkezzen be a virtuális géphez a key vaultban tárolt jelszó segítségével.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure-erőforrások, tisztítsa meg az erőforráscsoport törlésével helyezett erőforrásokat.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban lekért egy titkos kulcsot az Azure key vaultban. Ezután használja a titkos kulcsot a sablon központi telepítéséhez. Ha meg szeretné tudni, hogyan hozhat létre csatolt sablonokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Csatolt sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md)
