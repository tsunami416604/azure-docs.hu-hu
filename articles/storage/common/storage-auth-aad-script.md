---
title: Az Azure parancssori felület vagy PowerShell-parancsokat futtatni a blob- vagy várólista adatok eléréséhez az Azure AD-beli hitelesítő |} A Microsoft Docs
description: Az Azure CLI és PowerShell támogatja a parancsok futtatásához az Azure Storage-blobok és üzenetsorok adatait az Azure AD hitelesítő adatait bejelentkezni. Egy hozzáférési jogkivonatot a munkamenet számára megadott, és a hívó műveletek engedélyezése. Engedélyek az Azure AD rendszerbiztonsági tag rendelt RBAC szerepkör függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3fe142439dc80af660d286e5913fee13d4de8e86
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625656"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Az Azure parancssori felület vagy PowerShell-parancsokat futtatni a blob- vagy várólista adatok eléréséhez az Azure AD-beli hitelesítő

Az Azure Storage bővítményeket biztosít az Azure CLI és PowerShell, amelyek lehetővé teszik, hogy jelentkezzen be, és parancsfájl-kezelési parancsok futtatása az Azure Active Directory (Azure AD) hitelesítő adatokkal. Amikor bejelentkezik az Azure parancssori felület vagy PowerShell az Azure AD hitelesítő adatait, az OAuth 2.0 hozzáférési jogkivonatot ad vissza. Ezt a jogkivonatot a parancssori felület vagy PowerShell automatikusan használja a Blob vagy a Queue storage kapcsolatos művelet-végrehajtási adatok későbbi engedélyezéséhez. A támogatott műveletek már nincs szüksége egy kulccsal vagy SAS-jogkivonat a paranccsal át.

Blob és üzenetsor adatokat egy Azure AD rendszerbiztonsági tag szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket rendelhet hozzá. Az Azure Storage-ban RBAC-szerepkörök kapcsolatos további információkért lásd: [kezelés hozzáférési jogosultsága ahhoz, hogy az RBAC Azure Storage-adatokkal](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Támogatott műveletek

A bővítmények a tárolók és a várólisták műveletek támogatottak. Hívja előfordulhat, hogy mely műveletek az Azure AD biztonsági egyszerű, amellyel bejelentkezik az Azure parancssori felület vagy PowerShell jogosultságaitól függ. Az Azure Storage-tárolók, vagy várólisták engedélyeket szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. Például, ha hozzá van rendelve a **Blobadatok olvasója** szerepkörhöz, akkor futtathat parancsokat, amelyek egy tároló vagy egy üzenetsor adatokat olvasni. Ha hozzá van rendelve a **Blobadatok Közreműködője** szerepkörhöz, akkor futtathat parancsokat, amelyek olvasási, írási, vagy egy tároló vagy üzenetsor vagy a rajtuk tárolt adatok törlése. 

Minden Azure Storage-művelet egy tároló vagy a várólista szükséges jogosultságokat kapcsolatos részletekért lásd: [hívja a storage-műveletek elvégzése az OAuth-jogkivonatok](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

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
- Felügyelt identitások az Azure-erőforrások az Azure Storage használatával kapcsolatos további információkért lásd: [hitelesíti a blobok és üzenetsorok az Azure Active Directory és a felügyelt identitásokból hozzáférést az Azure-erőforrások](storage-auth-aad-msi.md).
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [storage alkalmazásait az Azure AD segítségével](storage-auth-aad-app.md).
