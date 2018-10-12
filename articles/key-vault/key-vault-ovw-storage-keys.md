---
ms.assetid: ''
title: Az Azure Key Vault-Tárfiókkulcsok
description: A tárfiókkulcsok egy Azure Key Vault és alapuló hozzáférés a kulcshoz seemless integrációjával nyújtson az Azure Storage-fiókot.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: adc8b84f0f22e85de88c4bd80c10a2a35d7b490a
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114600"
---
# <a name="azure-key-vault-storage-account-keys"></a>Az Azure Key Vault-Tárfiókkulcsok

> [!NOTE]
> [Az Azure storage mostantól támogatja az aad-ben engedélyezési](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Azt javasoljuk, hogy az Azure Active Directory hitelesítés és engedélyezés a Storage, a felhasználóknak nem kell aggódnia a Storage-fiók kulcsok.

- Az Azure Key Vault kezeli a kulcsok az Azure Storage fiók (ASA).
    - Belsőleg az Azure Key Vault listázhatja az Azure Storage-fiók kulcsok (sync).    
    - Az Azure Key Vault újragenerálása (rotálja) a kulcsokat rendszeres időközönként.
    - Kulcs értékeit a rendszer soha nem adja vissza a hívó adott válaszként.
    - Az Azure Key Vault kezeli a Storage-fiókok és a klasszikus Tárfiókok kulcsait.

<a name="prerequisites"></a>Előfeltételek
--------------
1. [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) az Azure CLI telepítése   
2. [Storage-fiók létrehozása](https://azure.microsoft.com/services/storage/)
    - Ezen lépések végrehajtásával [dokumentum](https://docs.microsoft.com/azure/storage/) a storage-fiók létrehozása  
    - **Elnevezési irányelvei:** Tárfiókok nevének 3 – 24 karakter hosszúságúnak kell lennie, és számokat, és csak kisbetűket tartalmazhat.        
      
<a name="step-by-step-instructions"></a>Útmutató lépés
-------------------------

1. Az erőforrás-Azonosítóját az Azure Storage-fiókot, amelyet kezelni szeretne kaphat.
    a. Storage-fiók létrehozása után futtassa a következő parancsot a kezelni kívánt beolvasni a storage-fiók erőforrás-azonosító
    ```
    az storage account show -n storageaccountname (Copy ID out of the result of this command)
    ```
2. Alkalmazás azonosítója az Azure Key Vault szolgáltatás egyszerű beolvasása 
    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
3. Tárolás kulcs operátori szerepkör hozzárendelése az Azure Key Vault Identity
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id hhjkh --scope idofthestorageaccount
    ```
4. Key Vault létrehozása felügyelt Tárfiók.     <br /><br />
   Alábbi parancsot a tárfiókkulcsok rendszeresen újragenerálja a regenerációs időszakot a Key Vault kéri. Az alábbi azt állítja egy 90 napos regenerációs időszakot. 90 nap után a Key Vault "1. kulcs" újragenerálása és cseréje: key1"a"2. kulcs: az aktív kulcs.
   ### <a name="key-regeneration"></a>Kulcs újragenerálása
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-generate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Abban az esetben a felhasználó nem hozott létre a tárfiókot, és nem rendelkezik engedélyekkel a storage-fiókba, az alábbi lépéseket, győződjön meg arról, hogy a Key Vault a tárolási engedélyek segítségével kezelheti a fiók engedélyeit, állítsa be.
    [!NOTE] Abban az esetben, hogy a felhasználó végrehajtja-e a tárfiókhoz, először lekérjük a felhasználói objektum azonosítója nem engedélyeket

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover purge restore set setsas update
    ```

### <a name="relevant-powershell-cmdlets"></a>Kapcsolódó Powershell-parancsmagok

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>Lásd még

- [A kulcsok, titkos kódok és tanúsítványok ismertetése](https://docs.microsoft.com/rest/api/keyvault/)
- [A Key Vault-csapat blogja](https://blogs.technet.microsoft.com/kv/)
