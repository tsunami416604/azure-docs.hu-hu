---
title: Az Azure Key Vault használata sablonokban
description: Megtudhatja, hogyan használható az Azure Key Vault biztonságos paraméterértékek megadására a Resource Manager-sablon üzembe helyezése során
author: mumian
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a305914c5c870543e16c515880955693c2634044
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239177"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Oktatóanyag: Integrálja az Azure Key Vaultot az ARM-sablon üzembe helyezésébe

Megtudhatja, hogyan kérhet le titkos kulcsokat egy Azure-kulcstárolóból, és adja át a titkos kulcsokat paraméterként egy Azure Resource Manager (ARM) sablon üzembe helyezésekor. A paraméter értéke soha nem érhető el, mert csak a key vault-azonosítóra hivatkozik. További információ: [Az Azure Key Vault használata a biztonságos paraméter értékének a telepítés során történő átadásához](./key-vault-parameter.md)című témakörben található.

Az [Erőforrás-telepítési sorrend beállítása](./template-tutorial-create-templates-with-dependent-resources.md) oktatóanyagban virtuális gépet (VM) hoz létre. Meg kell adnia a virtuális gép rendszergazdájának felhasználónevét és jelszavát. A jelszó megadása helyett előre tárolhatja a jelszót egy Azure-kulcstartóban, majd testreszabhatja a sablont a jelszó lekéréséhez a kulcstárolóból a központi telepítés során.

![Erőforrás-kezelő sablon és kulcstartó integrálását megjelenítő diagram](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Kulcstartó előkészítése
> * Gyorsindítási sablon megnyitása
> * A paraméterfájl szerkesztése
> * A sablon üzembe helyezése
> * Az üzembe helyezés ellenőrzése
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](use-vs-code-to-create-template.md)
* A biztonság növelése érdekében használjon létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa a jelszó generálásához:

    ```console
    openssl rand -base64 32
    ```
    Ellenőrizze, hogy a létrehozott jelszó megfelel-e a virtuális gép jelszókövetelményeinek. Az egyes Azure-szolgáltatások különböző jelszókövetelményeket szabnak meg. A virtuális gép jelszókövetelményeiről a [Mik a jelszókövetelmények a virtuális gép létrehozásakor.](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

## <a name="prepare-a-key-vault"></a>Kulcstartó előkészítése

Ebben a szakaszban hozzon létre egy key vault, és adjunk hozzá egy titkos kulcsot, így a sablon telepítésekor a titkos kulcsot. A kulcstartók létrehozásának számos módja van. Ebben az oktatóanyagban az Azure PowerShell használatával telepítegy [ARM-sablont.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json) Ez a sablon a következőket teszi:

* Létrehoz egy kulcstartót, amelyen engedélyezve van a `enabledForTemplateDeployment` tulajdonság. Ennek a tulajdonságnak *igaznak* kell lennie ahhoz, hogy a sablon központi telepítési folyamat a key vaultban definiált titkos kulcsokhoz férjen hozzá.
* Titkos kulcsot ad a kulcstartóhoz. A titkos tárolja a virtuális gép rendszergazdai jelszavát.

> [!NOTE]
> A felhasználó, aki telepíti a virtuális gép sablont, ha nem a tulajdonos vagy a közreműködő a key vault, a tulajdonos vagy a közreműködő hozzáférést kell biztosítania a *Microsoft.KeyVault/vaults/deploy/action* engedélyt a key vault. További információ: [Az Azure Key Vault használata a telepítés során biztonságos paraméterérték átadásához](./key-vault-parameter.md)című témakörben található.

A következő Azure PowerShell-parancsfájl futtatásához válassza **a Próbálja ki az** Azure Cloud Shell megnyitásához. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéj ablaktáblájára, majd válassza a **Beillesztés parancsot.**

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
> * Az erőforráscsoport neve a projekt neve, de **az rg** hozzá van fűzve hozzá. Az [oktatóanyagban létrehozott erőforrások egyszerűbb karbantartása érdekében](#clean-up-resources)használja ugyanazt a projektnevet és erőforráscsoport-nevet [a következő sablon telepítésekor.](#deploy-the-template)
> * A titkos adatik alapértelmezett neve **vmAdminPassword**. Ez kódolva van a sablonban.
> * Ahhoz, hogy a sablon lekérje a titkos kulcsot, engedélyeznie kell egy "Hozzáférés engedélyezése az Azure Resource Manager a sablon üzembe helyezéséhez" nevű hozzáférési szabályzatot a key vaultszámára. Ez a házirend engedélyezve van a sablonban. A hozzáférési szabályzattal kapcsolatos további információkért [lásd: Kulcstartók és titkos kulcsok telepítése.](./key-vault-parameter.md#deploy-key-vaults-and-secrets)

A sablon egy kimeneti értékkel rendelkezik, az úgynevezett *keyVaultId*. Írja le az azonosító értékét későbbi használatra, amikor telepíti a virtuális gépet. Az erőforrás-azonosító formátuma:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

Az azonosító másolásakor és beillesztésekénél előfordulhat, hogy több sorra bontva van. Egyesítse a vonalakat, és vágja le az extra szóközöket.

A központi telepítés érvényesítéséhez futtassa a következő PowerShell-parancsot ugyanabban a rendszerhéjablaktáblán a titkos kulcsot titkos ítésben, titkos ítésben. A parancs csak ugyanabban a rendszerhéj-munkamenetben működik, mert az előző PowerShell-parancsfájlban definiált *$keyVaultName*változót használja.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Most készítettél egy kulcstartót és egy titkot. A következő szakaszok bemutatják, hogyan szabhatja testre a meglévő sablont a titkos fájl lekéréséhez a központi telepítés során.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure quickstart sablonok egy tárház ARM sablonok. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az oktatóanyagban használt sablon neve [Egyszerű Windows virtuális gép telepítése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. A Visual Studio-kódban válassza a **Fájlmegnyitása** > **fájl**lehetőséget.

1. A **Fájlnév** mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt. A forgatókönyv megegyezik az oktatóanyagban használt: [Arm-sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md).
   A sablon öt erőforrást határoz meg:

   * `Microsoft.Storage/storageAccounts`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

   A sablon testreszabása előtt hasznos lehet a sablon néhány alapvető ismerete.

1. Válassza **a Fájlmentés** > **másként**lehetőséget, majd mentse a fájl egy példányát a helyi számítógépre az *azuredeploy.json*névvel.

1. Ismételje meg az 1-3. *azuredeploy.parameters.json*

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>A paraméterfájl szerkesztése

A sablonfájlt nem kell módosítania.

1. A Visual Studio-kód, nyissa *azuredeploy.parameters.json,* ha még nincs megnyitva.
1. A `adminPassword` paraméter frissítése a következőre:

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
    > Cserélje le az **id** értékét az előző eljárásban létrehozott key vault erőforrás-azonosítójára.

    ![A kulcstároló és az Erőforrás-kezelő sablon virtuálisgép-telepítési paraméterfájljának integrálása](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Frissítse a következő értékeket:

    * **adminUsername**: A virtuális gép rendszergazdai fiókjának neve.
    * **dnsLabelPrefix**: A dnsLabelPrefix érték neve.

    Nevekre vonatkozó példákat az előző képen talál.

1. Mentse a módosításokat.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Kövesse a [sablon telepítése](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)című részben található utasításokat. Töltse fel az *azuredeploy.json* és *az azuredeploy.parameters.json fájlt* a Cloud Shellbe, majd használja a következő PowerShell-parancsfájlt a sablon üzembe helyezéséhez:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$HOME/azuredeploy.json" `
    -TemplateParameterFile "$HOME/azuredeploy.parameters.json"
```

A sablon telepítésekor használja ugyanazt az erőforráscsoportot, amelyet a key vaultban használt. Ez a megközelítés megkönnyíti az erőforrások karbantartását, mert kettő helyett csak egy erőforráscsoportot kell törölnie.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Miután sikeresen telepítette a virtuális gépet, tesztelje a bejelentkezési hitelesítő adatokat a key vaultban tárolt jelszó használatával.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. Válassza az **Erőforráscsoportok** > **\<*YourResourceGroupName*>** > **simpleWinVM elemét.**
1. Válassza a **csatlakozás** gombot felül.
1. Válassza **az RDP-fájl letöltése**lehetőséget, majd kövesse az utasításokat a virtuális gépre való bejelentkezéshez a key vaultban tárolt jelszó használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Azure-erőforrások, törölje az erőforrásokat, hogy az erőforráscsoport törlésével üzembe helyezett.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban lekért egy titkot az Azure-key vaultból. Ezután a titkos kulcsot használta a sablon központi telepítésében. Ha meg szeretné tudni, hogyan hozhat létre csatolt sablonokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Hivatkozott sablonok létrehozása](./template-tutorial-create-linked-templates.md)
