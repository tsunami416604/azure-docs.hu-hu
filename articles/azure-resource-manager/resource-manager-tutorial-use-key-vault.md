---
title: Azure Key Vault használata Resource Manager-sablonban
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
ms.openlocfilehash: 5dfc186e5d047de76e16ec145f5f0afe94b8d2f4
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533599"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-resource-manager-template-deployment"></a>Oktatóanyag: a Azure Key Vault integrálása a Resource Manager-sablonok üzembe helyezésével

Megtudhatja, hogyan kérhet le titkos kulcsokat egy Azure Key vaultból, és hogyan továbbíthatja a titkokat paraméterekként a Azure Resource Manager telepítésekor. A paraméter értéke soha nem érhető el, mert csak a Key Vault-AZONOSÍTÓra hivatkozik. További információ: [a Azure Key Vault használata a biztonságos paraméterek értékének](./resource-manager-keyvault-parameter.md)átadására az üzembe helyezés során.

Az [erőforrás-telepítési sorrend beállítása](./resource-manager-tutorial-create-templates-with-dependent-resources.md) oktatóanyagban hozzon létre egy virtuális GÉPET (VM). Meg kell adnia a virtuális gép rendszergazdájának felhasználónevét és jelszavát. A jelszó megadása helyett előre tárolhatja a jelszót egy Azure Key vaultban, majd testreszabhatja a sablont a Key vaultból a telepítés során való lekéréséhez.

![Egy Resource Manager-sablon és egy kulcstartó integrálását megjelenítő diagram](./media/resource-manager-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Kulcstartó előkészítése
> * Gyorsindítási sablon megnyitása
> * A paraméterfájl szerkesztése
> * A sablon üzembe helyezése
> * Az üzembe helyezés ellenőrzése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) a [Resource Manager-eszközök bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* A biztonság növeléséhez használjon generált jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa a jelszó létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Ellenőrizze, hogy a generált jelszó megfelel-e a virtuális gép jelszavával kapcsolatos követelményeknek. Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. A virtuális gép jelszavával kapcsolatos követelményekért lásd: [Mik a jelszóra vonatkozó követelmények a virtuális gépek létrehozásakor?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Kulcstartó előkészítése

Ebben a szakaszban létrehoz egy kulcstartót, és hozzáad egy titkos kulcsot a sablon üzembe helyezése során a titkos kulcs lekéréséhez. A Key Vault számos módon hozható létre. Ebben az oktatóanyagban egy [Resource Manager-sablon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json)üzembe helyezéséhez Azure PowerShell használ. A sablon a következő műveleteket végzi el:

* Létrehoz egy Key vaultot, amelyen engedélyezve van a `enabledForTemplateDeployment` tulajdonság. Ennek a tulajdonságnak *igaznak* kell lennie ahhoz, hogy a sablon központi telepítési folyamata hozzáférhessen a kulcstartóban definiált titkos kulcsokhoz.
* Titkos kulcsot helyez el a kulcstartóhoz. A titkos kulcs a virtuális gép rendszergazdai jelszavát tárolja.

> [!NOTE]
> Ha a virtuálisgép-sablont telepítő felhasználó nem a kulcstartó tulajdonosa vagy közreműködője, akkor a tulajdonosnak vagy a közreműködőnek hozzáférést kell adnia a Microsoft. kulcstartó/tárolók/ *üzembe helyezés/művelet* engedélyhez a kulcstartóhoz. További információ: a [Azure Key Vault használata biztonságos paraméterek értékének](./resource-manager-keyvault-parameter.md)átadására az üzembe helyezés során.

A következő Azure PowerShell parancsfájl futtatásához válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablaktáblára, majd válassza a **Beillesztés**lehetőséget.

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
> * Az erőforráscsoport neve a projekt neve, de **RG** hozzáfűzéssel. Az [oktatóanyagban létrehozott erőforrások](#clean-up-resources)törlésének egyszerűbbé tételéhez használja a projekt nevét és az erőforráscsoport nevét a [következő sablon telepítésekor](#deploy-the-template).
> * A titok alapértelmezett neve **vmAdminPassword**. A sablon hardcoded.
> * Ha engedélyezni szeretné a sablon számára a titkos kulcs lekérését, engedélyeznie kell egy "hozzáférés engedélyezése Azure Resource Manager a sablon központi telepítéséhez" nevű hozzáférési szabályzatot a kulcstartóhoz. Ez a szabályzat engedélyezve van a sablonban. A hozzáférési szabályzattal kapcsolatos további információkért lásd: [kulcstartók és titkos kulcsok üzembe helyezése](./resource-manager-keyvault-parameter.md#deploy-key-vaults-and-secrets).

A sablon egy *keyVaultId*nevű kimeneti értékkel rendelkezik. A virtuális gép telepítésekor írja le az azonosító értékét későbbi használatra. Az erőforrás-azonosító formátuma:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Az azonosító másolásakor és beillesztésekor előfordulhat, hogy több sorba van bontva. Egyesítse a sorokat, és vágja fel a felesleges szóközöket.

A központi telepítés ellenőrzéséhez futtassa a következő PowerShell-parancsot ugyanazon a rendszerhéj-ablaktáblán a titkos kód egyszerű szövegként való lekéréséhez. A parancs csak ugyanabban a rendszerhéj-munkamenetben működik, mert a *$keyVaultName*változót használja, amely az előző PowerShell-parancsfájlban van meghatározva.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Most előkészített egy kulcstartót és egy titkos kulcsot. A következő részben bemutatjuk, hogyan szabhatja testre a meglévő sablonokat a titkos kód beolvasására a telepítés során.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure Gyorsindítás sablonjai a Resource Manager-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az oktatóanyagban használt sablon [egy egyszerű Windows-alapú virtuális gép üzembe helyezését](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)hívja meg.

1. A Visual Studio Code-ban válassza a **fájl**  >  fájl**megnyitása**lehetőséget.

1. Illessze be a következő URL-címet a **fájlnév** mezőbe:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt. A forgatókönyv ugyanaz, mint az [oktatóanyagban használt: Azure Resource Manager sablonok létrehozása függő erőforrásokkal](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
   A sablon öt erőforrást határoz meg:

   * `Microsoft.Storage/storageAccounts` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   A Testreszabás előtt hasznos lehet a sablon alapvető ismerete.

1. Válassza a **fájl**  > **Mentés másként**lehetőséget, majd mentse a fájl egy másolatát a helyi számítógépre a *azuredeploy. JSON*néven.

1. Ismételje meg a 1-3 lépést a következő URL-cím megnyitásához, majd mentse a fájlt *azuredeploy. Parameters. JSON*néven.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>A paraméterfájl szerkesztése

A sablonfájlt nem kell módosítania.

1. A Visual Studio Code-ban nyissa meg a *azuredeploy. Parameters. JSON* fájlt, ha még nincs megnyitva.
1. Frissítse a `adminPassword` paramétert a következőre:

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
    > Cserélje le az **azonosító** értékét az előző eljárás során létrehozott kulcstartó erőforrás-azonosítójával.

    ![A Key Vault és a Resource Manager-sablon virtuális gép telepítési paramétereinek fájljának integrálása](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Frissítse a következő értékeket:

    * **adminUsername**: a virtuális gép rendszergazdai fiókjának neve.
    * **dnsLabelPrefix**: nevezze el a dnsLabelPrefix értéket.

    Példák a nevekre: az előző képen látható.

1. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse a [sablon üzembe helyezése](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)című témakör utasításait. Töltse fel a *azuredeploy. JSON* és a *azuredeploy. Parameters. json* fájlt a Cloud Shellba, majd a következő PowerShell-parancsfájl használatával telepítse a sablont:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

A sablon központi telepítésekor használja ugyanazt az erőforráscsoportot, amelyet a Key vaultban használt. Ezzel a módszerrel könnyebben törölheti az erőforrásokat, mivel kettő helyett csak egy erőforráscsoportot kell törölnie.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A virtuális gép sikeres üzembe helyezését követően tesztelje a bejelentkezési hitelesítő adatokat a Key vaultban tárolt jelszó használatával.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Válassza ki az **erőforráscsoportok**  >  **\<*YourResourceGroupName* >**  > **simpleWinVM**.
1. Kattintson a fent található **kapcsolat** lehetőségre.
1. Válassza az **RDP-fájl letöltése**lehetőséget, majd kövesse az utasításokat a virtuális gépre való bejelentkezéshez a Key vaultban tárolt jelszó használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure-erőforrásokra, az erőforráscsoport törlésével törölje az üzembe helyezett erőforrásokat.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy titkos kulcsot adott vissza az Azure Key vaultból. Ezután használta a titkot a sablon üzembe helyezésében. Ha meg szeretné tudni, hogyan hozhat létre csatolt sablonokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Csatolt sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md)
