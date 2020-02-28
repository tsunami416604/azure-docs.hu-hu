---
title: Azure rövid útmutató – Azure Key Vault és titkos kód létrehozása Azure Resource Manager sablon használatával | Microsoft Docs
description: Gyors útmutató, amely bemutatja, hogyan hozhatók létre Azure Key vaultok, és hogyan adhat hozzá titkokat a tárakhoz Azure Resource Manager sablon használatával.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/17/2019
ms.author: jgao
ms.openlocfilehash: b2561a4e065fc82cc08e8275965ab0b403fc66f0
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654562"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Rövid útmutató: titkos kód beállítása és lekérése Azure Key Vault Resource Manager-sablon használatával

A [Azure Key Vault](./key-vault-overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkok számára, például kulcsokat, jelszavakat, tanúsítványokat és egyéb titkokat. Ez a rövid útmutató egy Resource Manager-sablon üzembe helyezésének folyamatát, amely egy kulcstartó és egy titkos kulcs létrehozásával foglalkozik.

A [Resource Manager-sablon](../azure-resource-manager/templates/overview.md) egy JavaScript Object Notation (JSON) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon deklaratív szintaxist használ, amely lehetővé teszi, hogy a telepítéshez szükséges programozási parancsok sorrendjének megírása nélkül adja meg az üzembe helyezni kívánt adatokat. Ha többet szeretne megtudni a Resource Manager-sablonok fejlesztéséről, tekintse meg a [Resource Manager dokumentációját](/azure/azure-resource-manager/) és a [sablon referenciáját](/azure/templates/microsoft.keyvault/allversions).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárás az objektumazonosító (GUID) beolvasása.

    1. Futtassa a következő Azure PowerShell vagy Azure CLI-parancsot a **kipróbálás**lehetőség kiválasztásával, majd illessze be a szkriptet a rendszerhéj ablaktáblába. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés**lehetőséget.

        # <a name="cli"></a>[Parancssori felület](#tab/CLI)
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

    2. Jegyezze fel az objektum AZONOSÍTÓját. Ennek a rövid útmutatónak a következő szakaszában kell megadnia.

## <a name="create-a-vault-and-a-secret"></a>Tár és titkos kód létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-key-vault-create/)származik.

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json" range="1-150" highlight="107-148":::

Két Azure-erőforrás van definiálva a sablonban:

* [**Microsoft. kulcstartó/** ](/azure/templates/microsoft.keyvault/vaults)tárolók: hozzon létre egy Azure Key vaultot.
* [**Microsoft. kulcstartó/tárolók/titkok**](/azure/templates/microsoft.keyvault/vaults/secrets): Create a Key Vault Secret.

[Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)több Azure Key Vault sablon is található.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.

    ![Resource Manager-sablon, Key Vault integráció, portál üzembe helyezése](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Ha meg van adva, az alapértelmezett érték használatával hozza létre a kulcstartót és a titkos kulcsot.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása**lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK**gombra.
    * **Hely**: válasszon ki egy helyet.  Például az **USA középső**régiója.
    * **Key Vault neve**: adja meg a Key Vault nevét, amelynek globálisan egyedinek kell lennie a. Vault.Azure.net névtérben. A telepítés ellenőrzésekor a következő szakaszban kell megadnia a nevet.
    * **Bérlő azonosítója**: a sablon függvény automatikusan lekéri a bérlő azonosítóját.  Ne módosítsa az alapértelmezett értéket.
    * **Ad felhasználói azonosító**: adja meg az [előfeltételekből](#prerequisites)beolvasott Azure ad felhasználói objektum azonosítóját.
    * **Titkos kód neve**: adja meg a Key vaultban tárolt titok nevét.  Például: **AdminPassword**.
    * **Titkos érték**: adja meg a titkos értéket.  Ha jelszót tárol, azt javasoljuk, hogy használja az előfeltételekben létrehozott generált jelszót.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Válassza ki.
3. Válassza a **Beszerzés** lehetőséget. A Key Vault sikeres üzembe helyezését követően értesítést kap:

    ![Resource Manager-sablon, Key Vault integráció, portál-értesítés üzembe helyezése](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

A Azure Portal a sablon üzembe helyezéséhez használható. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal segítségével megtekintheti a kulcstartót és a titkos kulcsot, vagy a következő Azure CLI-vagy Azure PowerShell-szkripttel listázhatja a létrehozott titkos kulcsot.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

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

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

![Resource Manager-sablon, Key Vault integráció, portál-ellenőrzési kimenet üzembe helyezése](./media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![Resource Manager-sablon, Key Vault integráció, portál-ellenőrzési kimenet üzembe helyezése](./media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell használatával:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

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

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy kulcstartót és egy titkos kulcsot hozott létre egy Azure Resource Manager sablonnal, és ellenőrizte az üzemelő példányt. Ha többet szeretne megtudni a Key Vault és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](key-vault-overview.md)
- További információ a [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- További információ a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-and-certificates.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése
