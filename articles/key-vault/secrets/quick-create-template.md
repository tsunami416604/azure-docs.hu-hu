---
title: Azure-gyorsútmutató – Hozzon létre egy Azure-kulcstárolót és egy titkot az Azure Resource Manager sablon használatával | Microsoft dokumentumok
description: Rövid útmutató, amely bemutatja, hogyan hozhat létre Azure-kulcstartókat, és titkokat adhat hozzá a tárolókhoz az Azure Resource Manager-sablon használatával.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 273a467f5db2201015352aaf4a232f5a42e29673
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618083"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Rövid útmutató: Titkos kulcs beállítása és beolvasása az Azure Key Vaultból az Erőforrás-kezelő sablon használatával

[Az Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkos kulcsok, például kulcsok, jelszavak, tanúsítványok és egyéb titkos kulcsok számára. Ez a rövid útmutató az Erőforrás-kezelő sablon központi telepítésének folyamatára összpontosít egy kulcstartó és egy titkos kulcs létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. A következő eljárás leteszi az objektum azonosítóját (GUID).

    1. Futtassa a következő Azure PowerShell vagy Azure CLI parancsot válassza **a Próbálja ki**, majd illessze be a parancsfájlt a rendszerhéj ablaktáblába. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, és válassza a **Beillesztés parancsot.**

        # <a name="cli"></a>[parancssori felület](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Írja le az objektumazonosítót. Szüksége van rá a rövid útmutató következő részében.

## <a name="create-a-vault-and-a-secret"></a>Hozzon létre egy trezort és egy titkos

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-key-vault-create/)

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

A sablonban két Azure-erőforrás van definiálva:

* [**Microsoft.KeyVault/vaults:**](/azure/templates/microsoft.keyvault/vaults)hozzon létre egy Azure-kulcstartót.
* [**Microsoft.KeyVault/vaults/secrets**](/azure/templates/microsoft.keyvault/vaults/secrets): hozzon létre egy key vault titkos.

További Azure Key Vault-sablonminták [itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)találhatók.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy key vault és egy titkos kulcsot.

    [![Üzembe helyezés az Azure-ban](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    ![Resource Manager-sablon, Key Vault-integráció, portál üzembe helyezése](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Ha nincs megadva, használja az alapértelmezett értéket a key vault és egy titkos kulcs létrehozásához.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: Válassza **az Új létrehozása**lehetőséget, adja meg az erőforráscsoport egyedi nevét, majd kattintson az **OK**gombra.
    * **Hely**: válasszon ki egy helyet.  Például **az USA középső régiója**.
    * **Key Vault name:** adja meg a kulcstartó nevét, amelynek globálisan egyedinek kell lennie az .vault.azure.net névtéren belül. A központi telepítés ellenőrzésekor a következő szakaszban szüksége van a névre.
    * **Bérlői azonosító:** a sablonfüggvény automatikusan lekéri a bérlői azonosítót.  Ne módosítsa az alapértelmezett értéket.
    * **Ad user id**: adja meg az Azure AD felhasználói objektum azonosítóját, amelyet [az Előfeltételek](#prerequisites)ből kért be.
    * **Titkos név:** adja meg a kulcstartóban tárolt titkos kulcs nevét.  Például **adminpassword**.
    * **Titkos érték**: adja meg a titkos értéket.  Ha egy jelszót tárol, ajánlott az Előfeltételek ben létrehozott jelszót használni.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Válassza ki.
3. Válassza a **Beszerzés** lehetőséget. A kulcstartó sikeres üzembe helyezése után értesítést kap:

    ![Erőforrás-kezelő sablon, Key Vault-integráció, portálértesítés üzembe helyezése](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Az Azure Portal a sablon üzembe helyezéséhez használható. Az Azure Portalon kívül használhatja az Azure PowerShellt, az Azure CLI-t és a REST API-t is. További telepítési módszerekről a [Sablonok telepítése ..](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az Azure Portalon ellenőrizheti a kulcstárolót és a titkos kulcsot, vagy használhatja a következő Azure CLI- vagy Azure PowerShell-parancsfájlt a létrehozott titkos kulcs listázásához.

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

A kimenet a következőhöz hasonlóan néz ki:

# <a name="cli"></a>[parancssori felület](#tab/CLI)

![Resource Manager-sablon, Key Vault-integráció, portálérvényesítési kimenet telepítése](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Resource Manager-sablon, Key Vault-integráció, portálérvényesítési kimenet telepítése](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése az Azure CLI vagy az Azure PowerShell használatával:

# <a name="cli"></a>[parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy key vault ot és egy titkos kulcsot egy Azure Resource Manager-sablon használatával, és érvényesítette a központi telepítést. Ha többet szeretne megtudni a Key Vaultról és az Azure Resource Managerről, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)
