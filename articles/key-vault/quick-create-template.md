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
ms.date: 09/03/2019
ms.author: jgao
ms.openlocfilehash: 13ac8b9f6d82cc8b0060f87237ef3140cb245379
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259205"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Gyors útmutató: Azure Key Vault titkos kód beállítása és beolvasása Resource Manager-sablon használatával

A [Azure Key Vault](./key-vault-overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkok számára, például kulcsokat, jelszavakat, tanúsítványokat és egyéb titkokat. Ez a rövid útmutató egy Resource Manager-sablon üzembe helyezésének folyamatát, amely egy kulcstartó és egy titkos kulcs létrehozásával foglalkozik. A Resource Manager-sablonok fejlesztésével kapcsolatos további információkért tekintse meg a [Resource Manager dokumentációját](/azure/azure-resource-manager/) és a [sablonra vonatkozó referenciát](/azure/templates/microsoft.keyvault/allversions).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárás az objektumazonosító (GUID) beolvasása.

    1. Futtassa a következő Azure PowerShell vagy Azure CLI-parancsot a **kipróbálás**lehetőség kiválasztásával, majd illessze be a szkriptet a rendszerhéj ablaktáblába. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a **Beillesztés**lehetőséget. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Jegyezze fel az objektum AZONOSÍTÓját. Ennek a rövid útmutatónak a következő szakaszában kell megadnia.

## <a name="create-a-vault-and-a-secret"></a>Tár és titkos kód létrehozása

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-key-vault-create/)származik. [Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)több Azure Key Vault sablon is található.

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.  

    ![Resource Manager-sablon Key Vault-integrációjának üzembe helyezési portálja](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Ha meg van adva, az alapértelmezett érték használatával hozza létre a kulcstartót és a titkos kulcsot.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása**lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK**gombra. 
    * **Hely**: válasszon ki egy helyet.  Ha például **USA középső RÉGIÓJA**.
    * **Key Vault neve**: adja meg a Key Vault nevét, amelynek globálisan egyedinek kell lennie a. Vault.Azure.net névtérben.  
    * **Bérlőazonosító**: a sablonfüggvény automatikusan lekéri a bérlőazonosítót.  Ne módosítsa az alapértelmezett értéket.
    * **Ad felhasználói azonosító**: adja meg az [előfeltételekből](#prerequisites)beolvasott Azure ad felhasználói objektum azonosítóját.
    * **Titkos kód neve**: adja meg a Key vaultban tárolt titok nevét.  Például: **AdminPassword**.
    * **Titkos érték**: adja meg a titkos értéket.  Ha jelszót tárol, azt javasoljuk, hogy használja az előfeltételekben létrehozott generált jelszót.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Válassza.
3. Válassza a **Beszerzés** lehetőséget.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A Azure Portal segítségével megtekintheti a kulcstartót és a titkos kulcsot, vagy a következő Azure CLI-vagy Azure PowerShell-szkripttel listázhatja a létrehozott titkos kulcsot.

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName
```

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése az Azure CLI vagy az Azure PowerShell használatával:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```
```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta a kulcstartót és egy titkos kulcsot egy Azure Resource Manager-sablon használatával, és ellenőrizte a központi telepítést. Ha többet szeretne megtudni a Key Vault és a Azure Resource Managerról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](key-vault-overview.md)
- További információ a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
- További információ a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-and-certificates.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése