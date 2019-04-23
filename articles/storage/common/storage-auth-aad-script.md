---
title: Egy Azure AD identitás alatt az Azure CLI vagy a PowerShell-parancsokat futtatni adatokhoz való hozzáférésének blob és üzenetsor |} A Microsoft Docs
description: Az Azure CLI és PowerShell támogatja adataikkal jelentkezhetnek be az Azure AD identitás-parancsok futtatásához az Azure Storage-blobok és üzenetsorok adatait. Egy hozzáférési jogkivonatot a munkamenet számára megadott, és a hívó műveletek engedélyezése. Engedélyek az Azure AD identity rendelt RBAC szerepkör függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: a0972beff48e07b6ce8afdcec10581300f59ed41
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786998"
---
# <a name="use-an-azure-ad-identity-to-access-blob-and-queue-data-with-cli-or-powershell"></a>Blob és üzenetsor adatok elérését az Azure AD identitás használata a parancssori felület vagy a PowerShell használatával

Az Azure Storage bővítményeket biztosít az Azure CLI és PowerShell, amelyek lehetővé teszik, hogy jelentkezzen be, és a egy Azure Active Directory (Azure AD) identitás alatt parancsfájl-kezelési parancsok futtatásához. Az Azure AD identity lehet egy felhasználó, csoport vagy alkalmazás egyszerű szolgáltatást, vagy lehet egy [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md). A szerepköralapú hozzáférés-vezérlés (RBAC) keresztül az Azure AD Identity blob és üzenetsor adatigénylésekre engedélyeket rendelhet. Az Azure Storage-ban RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC Azure Storage-adatokkal](storage-auth-aad-rbac.md).

Amikor bejelentkezik az Azure parancssori felület vagy PowerShell az Azure AD-identitásnak, egy hozzáférési jogkivonatot, hogy az identitás alatt az Azure Storage eléréséhez adja vissza. A jogkivonat ezután automatikusan használják parancssori felület vagy PowerShell történő hitelesítéséhez az Azure Storage kapcsolatos művelet-végrehajtási. A támogatott műveletek már nincs szüksége egy kulccsal vagy SAS-jogkivonat a paranccsal át.

## <a name="supported-operations"></a>Támogatott műveletek

A bővítmények a tárolók és a várólisták műveletek támogatottak. Hívja előfordulhat, hogy mely műveletek az Azure AD Identity, amellyel bejelentkezik az Azure parancssori felület vagy PowerShell jogosultságaitól függ. Az Azure Storage-tárolók, vagy várólisták engedélyeket szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. Például ha egy Adatolvasó szerepkör van rendelve az identitást, majd futtathatja parancsokat, amelyek az adatok olvasása a tárolóból vagy üzenetsor. Ha a Data-közreműködői szerepkör van rendelve az identitás, majd futtathatja parancsokat, amelyek olvasási, írási, vagy egy tároló vagy üzenetsor vagy a rajtuk tárolt adatok törlése. 

Minden Azure Storage-művelet egy tároló vagy a várólista szükséges jogosultságokat kapcsolatos részletekért lásd: [REST-műveleteinek meghívására szolgáló engedélyek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Hívás CLI-parancsok az Azure AD hitelesítő adatok használatával

Az Azure parancssori felület támogatja a `--auth-mode` paraméter blob és üzenetsor műveletekhez:

- Állítsa be a `--auth-mode` paramétert `login` jelentkezzen be az Azure AD rendszerbiztonsági tagot.
- Állítsa be a `--auth-mode` paramétert az örökölt `key` értéket, hogy megpróbálja lekérdezni egy fiók fő Ha nincs a fiókhoz tartozó hitelesítési paraméterek vannak megadva. 

Az alábbi példa bemutatja, hogyan, létrehozhat egy tárolót az új storage-fiókot az Azure CLI használatával az Azure AD hitelesítő adatait. Ne felejtse el a csúcsos zárójeleket helyőrzőértékeket cserélje le a saját értékeit: 

1. Győződjön meg arról, hogy telepítette-e az Azure CLI-verzió 2.0.46 vagy újabb verziója. Futtatás `az --version` a telepített verzió ellenőrzéséhez.

1. Futtatás `az login` és hitelesítse a böngésző ablakában: 

    ```azurecli
    az login
    ```
    
1. Adja meg a kívánt előfizetés azonosítóértékét. Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. Hozzon létre egy tárfiókot, hogy az erőforrás csoport belül [az tárfiók létrehozása](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```
    
1. A tároló létrehozásához, hozzárendeléséhez a [Storage-Blobadatok Közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkör saját magának. Annak ellenére, hogy Ön a fiók tulajdonosa, akkor hajtsa végre a tárfiókon futtatható műveletekhez explicit engedélyre van szükségük. További információ az RBAC-szerepkörök hozzárendelése: [hozzáférést biztosít az Azure blob és üzenetsor az adatokat az RBAC az Azure Portalon](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC szerepkör-hozzárendelések propagálása néhány percig is eltarthat.
    
1. Hívja a [az a tároló létrehozása](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) parancsot a `--auth-mode` paraméter beállítása `login` létrehozni a tárolót az Azure ad-ben használt hitelesítő adataival:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

A társított környezeti változót a `--auth-mode` paraméter `AZURE_STORAGE_AUTH_MODE`. A környezeti változóban, például egy Azure Storage-adatok művelet minden meghívásához elkerülése érdekében a megfelelő értéket is megadhat.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Hívás PowerShell-parancsok az Azure AD hitelesítő adatok használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell használatával jelentkezzen be, és a későbbi műveletek futtatásához az Azure Storage-ban az Azure AD hitelesítő adatait, hozzon létre egy storage-környezetet való hivatkozáshoz a tárfiók, és többek között a `-UseConnectedAccount` paraméter.

Az alábbi példa bemutatja, hogyan, létrehozhat egy tárolót az új storage-fiókot az Azure PowerShell használatával az Azure AD hitelesítő adatait. Ne felejtse el a csúcsos zárójeleket helyőrzőértékeket cserélje le a saját értékeit:

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő utasításokat az Azure AD hitelesítő adatait: 

    ```powershell
    Connect-AzAccount
    ```
    
1. Hozzon létre egy Azure-erőforráscsoport meghívásával [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Hozzon létre egy tárfiókot meghívásával [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. A tárfiók környezetét, amely meghatározza az új tárfiókot meghívásával első [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). A storage-fiók eljárva hivatkozhat a környezetre ismételten átadja a hitelesítő adatait. Tartalmazza a `-UseConnectedAccount` paramétert az Azure ad-ben használt hitelesítő adataival az adatok későbbi követően:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. A tároló létrehozásához, hozzárendeléséhez a [Storage-Blobadatok Közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkör saját magának. Annak ellenére, hogy Ön a fiók tulajdonosa, akkor hajtsa végre a tárfiókon futtatható műveletekhez explicit engedélyre van szükségük. További információ az RBAC-szerepkörök hozzárendelése: [hozzáférést biztosít az Azure blob és üzenetsor az adatokat az RBAC az Azure Portalon](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC szerepkör-hozzárendelések propagálása néhány percig is eltarthat.

1. Hozzon létre egy tárolót meghívásával [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Mivel a hívás az előző lépésekben létrehozott összefüggésben használja, a tároló jön létre az Azure ad-ben használt hitelesítő adataival. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>További lépések

- Az Azure storage szolgáltatáshoz az RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC tárolási adatok](storage-auth-aad-rbac.md).
- Felügyelt identitások az Azure-erőforrások az Azure Storage használatával kapcsolatos további információkért lásd: [blobok és üzenetsorok az Azure-ral való hitelesítés hozzáférési felügyelt identitások az Azure-erőforrások](storage-auth-aad-msi.md).
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [storage alkalmazásait az Azure AD segítségével](storage-auth-aad-app.md).
