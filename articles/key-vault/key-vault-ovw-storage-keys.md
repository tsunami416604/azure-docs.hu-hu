---
ms.assetid: ''
title: Az Azure Key Vaultban felügyelt tárfiók – CLI
description: A tárfiókkulcsok egy Azure Key Vault és alapuló hozzáférés a kulcshoz seemless integrációjával nyújtson az Azure Storage-fiókot.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 3ee0d19c174490d558a8ff06d3f5e038ffff211f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064440"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Az Azure Key Vaultban felügyelt tárfiók – CLI

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
    - Kövesse a jelen [dokumentum](https://docs.microsoft.com/azure/storage/) a storage-fiók létrehozása  
    - **Elnevezési irányelvei:** A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Útmutató a Key Vault használata kezelheti a Tárfiók kulcsait. lépés
--------------------------------------------------------------------------------
Az az alábbi utasítások végrehajtásával, hogy társítja az Key Vault engedélyekkel kell rendelkeznie operátor a tárfiók szolgáltatásként

> [!NOTE]
> Kérjük vegye figyelembe, hogy miután beállította az Azure Key Vaultban felügyelt tárfiók a kulcsok akkor kell **nem** már módosítható, kivéve a Key Vault-n keresztül. A tároló kulcsait, az azt jelenti, hogy a Key Vault kezelne elforgatása a tárfiók kulcsát felügyelt

1. Miután létrehozott egy tárfiókot, a storage-fiók erőforrás-azonosító beolvasásához a következő parancsot, a felügyelni kívánt

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Alkalmazás azonosítója az Azure Key Vault szolgáltatás egyszerű beolvasása 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Tárolás kulcs operátori szerepkör hozzárendelése az Azure Key Vault Identity

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Key Vault létrehozása felügyelt Tárfiók.     <br /><br />
   Az alábbi azt állítja egy 90 napos regenerációs időszakot. 90 nap után a Key Vault "1. kulcs" újragenerálása és cseréje: key1"a"2. kulcs: az aktív kulcs.
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Abban az esetben a felhasználó nem hozott létre a tárfiókot, és nem rendelkezik engedélyekkel a storage-fiókba, az alábbi lépéseket, győződjön meg arról, hogy a Key Vault a tárolási engedélyek segítségével kezelheti a fiók engedélyeit, állítsa be.
 > [!NOTE] 
    Abban az esetben, hogy a felhasználó nem rendelkezik engedélyekkel a tárfiók először lekérjük a felhasználó objektumazonosítóját

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>Relavant Azure CLI-parancsmagok
- [A Storage-parancsmagok az Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

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
