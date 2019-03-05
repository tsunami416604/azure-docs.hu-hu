---
title: Azure rövid útmutató – egy Azure key vault és a titkos kulcs létrehozása Azure Resource Manager-sablon használatával |} A Microsoft Docs
description: Rövid útmutató, amely az Azure-kulcstartók létrehozása, és a titkos kulcsok hozzáadása a tárolók az Azure Resource Manager-sablon használatával.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2019
ms.author: jgao
ms.openlocfilehash: 2cb6e210aa0e232a4ebb60dc44777a55f2c4f7fc
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57318548"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Gyors útmutató: Beállítása és lekérése a titkos kulcs Azure Key vault Resource Manager-sablon használatával

[Az Azure Key Vault](./key-vault-overview.md) egy felhőalapú szolgáltatás, amely titkos adatait, például a kulcsok, a jelszavak, tanúsítványok, valamint egyéb titkok biztonságos tároló biztosít. Ebben a rövid útmutatóban egy kulcstartót és a titkos kulcs létrehozása Resource Manager-sablon üzembe helyezésének folyamata összpontosít. A Resource Manager-sablonok fejlesztéséhez további információkért lásd: [Resource Manager dokumentációja](/azure/azure-resource-manager/) és a [sablonreferenciája](/azure/templates/microsoft.keyvault/allversions).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárást az objektum Azonosítóját (GUID) beolvasása.

    1. Az alábbi parancsot az Azure PowerShell vagy az Azure CLI kiválasztásával **kipróbálás**, majd illessze be a parancsfájlt a rendszerhéj ablaktáblára. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéjat, és válassza **illessze be**. 

        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --upn-or-object-id $upn --query "objectId" 
        ```

        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        ```

    2. Jegyezze fel az objektumazonosítót. Ez a rövid útmutató következő szakaszában van szükség.

## <a name="create-a-vault-and-a-secret"></a>Hozzon létre egy tárolót, és a egy titkos kulcsot

A rendszer a rövid útmutatóban használt sablon [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-key-vault-create/). További Azure Key Vault-sablon példákat [Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és a egy titkos kulcsot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.  

    ![Resource Manager-sablon Key Vault-integrációjának üzembe helyezési portálja](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Kivéve, ha a célgyűjtemény meg van adva, használja az alapértelmezett értéket a key vault és a titkos kulcs létrehozása.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válasszon **új létrehozása**, adjon meg egy egyedi nevet az erőforráscsoport, és kattintson **OK**. 
    * **Hely**: válasszon ki egy helyet.  Ha például **USA középső RÉGIÓJA**.
    * **A kulcstartó nevét**: Adjon meg egy egyedi nevet a key vaultban.  
    * **Bérlőazonosító**: a sablonfüggvény automatikusan lekéri a bérlőazonosítót.  Ne módosítsa az alapértelmezett értéket.
    * **Ad-felhasználói azonosító**: Adja meg az Azure AD felhasználói objektum azonosítója, amely a lekért [Előfeltételek](#prerequisites).
    * **Název tajného kódu**: Adja meg a titkos kulcsot a kulcstartóban tárolt nevét.  Ha például **adminpassword**
    * **Titkos érték**: Adja meg a titkos érték.  Ha jelszó tárolja, javasoljuk, hogy használni létrehozott generált jelszót [előfeltétel](#prerequisite).
    * **Elfogadom a fenti feltételek és kikötések állapot**: Kiválasztás.
3. Válassza a **Beszerzés** lehetőséget.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Ellenőrizze a key vaultban, és a titkos kulcsot az Azure portal használatával, vagy használja a következő Azure CLI-vel vagy az Azure PowerShell-parancsfájlt a létrehozott titkos listázásához.

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
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése az Azure parancssori felület vagy az Azure Powershell használatával:

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

* [Az Azure Key Vault kezdőlapja](https://azure.microsoft.com/services/key-vault/)
* [Az Azure Key Vault dokumentációja](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/)