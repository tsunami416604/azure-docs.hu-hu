---
title: Az Azure Key Vaultban felügyelt tárfiók – PowerShell-verzió
description: A felügyelt tárfiókok fiók szolgáltatása egy seemless integrációt, az Azure Key Vault és az Azure storage-fiók között.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 9b6089aa828b5667f100c1a8cbff3e69345e4512
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405096"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Az Azure Key Vaultban felügyelt tárfiók – PowerShell

> [!NOTE]
> [Az Azure storage-integráció az Azure Active Directory (Azure AD) már előzetes verzióban érhető el](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). A hitelesítéshez és engedélyezéshez, amely az Azure storage, csakúgy, mint az Azure Key Vault OAuth2 jogkivonat-alapú hozzáférést biztosít az Azure AD használatát javasoljuk. Ez lehetővé teszi, hogy:
> - Hitelesítse az ügyfélalkalmazást, egy alkalmazás vagy felhasználó identitását, helyett a tárfiók hitelesítő adatait. 
> - Használja az [Azure ad-ben felügyelt identitás](/azure/active-directory/managed-identities-azure-resources/) futtatásakor az Azure-ban. Felügyelt identitások távolítsa el az ügyfél-hitelesítéshez forrásokból együttesen kell és tárolását hitelesítő adatok a, vagy az alkalmazását.
> - Szerepkör alapú hozzáférés-vezérlés (RBAC) használata a kezeléséhez engedélyezésre, amelynek a Key Vault által is támogatott.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy [Azure storage-fiók](/azure/storage/storage-create-storage-account) hitelesítő adatot egy fióknevet és a egy kulcs használja. A kulcsot automatikusan létrehozott, és több mint egy "jelszó" helyett a titkosítási kulcs szolgál. A Key Vault kezelheti a tárfiók kulcsaihoz, tárolja őket, mint [Key Vault titkos kódok](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Áttekintés

A Key Vaultban felügyelt tárfiók funkció számos felügyeleti funkciója az Ön nevében hajt végre.:

- Az Azure storage-fiók kulcsok listákat (szinkronizálás).
- Újragenerálja (rotálja) a kulcsokat rendszeres időközönként.
- Storage-fiókok és a klasszikus tárfiókok kulcsainak kezeli.
- Kulcs értékeit a rendszer soha nem adja vissza a hívó adott válaszként.

A felügyelt tárfiókok fiók kulcsfontosságú funkció használatakor:

- **Kezelheti a tárfiók kulcsait a Key Vault engedélyezése csak.** Ne kísérelje meg saját maga is kezelheti őket, meg fogjuk zavarják a Key Vault folyamatokat.
- **Nem engedélyezi a tárfiók kulcsait a Key Vault egynél több objektum által felügyelendő**.
- **Manuálisan nem újragenerálni a tárfiókkulcsokat**. Azt javasoljuk, hogy a Key Vault-n keresztül újragenerálása.

Az alábbi példa bemutatja, hogyan kezelheti a tárfiók kulcsait a Key Vault teszi lehetővé.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>A tárfiók eléréséhez a Key Vault engedélyezése

> [!IMPORTANT]
> Az Azure AD-bérlő minden regisztrált alkalmazás biztosít egy  **[szolgáltatásnév](/azure/active-directory/develop/developer-glossary#service-principal-object)**, amely funkcionál az alkalmazás azonosítóját. Az egyszerű szolgáltatás Alkalmazásazonosítója használt adná más Azure-erőforrások hozzáférési szerepköralapú hozzáférés-vezérlés (RBAC) révén. Mivel a Key Vault egy Microsoft-alkalmazásba, előre regisztrált összes az Azure AD bérlő alatt ugyanazon Alkalmazásazonosítóval, minden egyes Azure-felhőben lévő:
> - Az Azure government felhőben az Azure AD-bérlőt használja Alkalmazásazonosító `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Az Azure nyilvános felhő, és minden más Azure AD-bérlőt használja Alkalmazásazonosító `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

A Key Vault eléréséhez és a kezelése a tárfiók kulcsait, mielőtt engedélyeznie kell a hozzáférést a tárfiókhoz. A Key Vault alkalmazás engedélyekre van szüksége *lista* és *újragenerálása* kulcsokat a tárfiók számára. Ezeket az engedélyeket – a beépített RBAC-szerepkör [tárolási fiók kulcs operátora – szolgáltatási szerepkör](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Ez a szerepkör hozzárendelése a Key Vault szolgáltatásnév, korlátozza az hatókörrel, hogy a tárfiókot, az alábbi lépéseket követve. Ne felejtse el frissíteni a `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey`, és `$keyVaultName` változók a parancsfájl futtatása előtt:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

A sikeres szerepkör-hozzárendelést követően az alábbi példához hasonló kimenetnek kell megjelennie:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Ha a Key Vault már hozzá lett adva a szerepkörhöz a storage-fiókjában, kapni fog egy *"szerepkör-hozzárendelés már létezik."* Hiba történt. Ellenőrizheti a szerepkör-hozzárendelés is, a tárolási fiók "Hozzáférés-vezérlés (IAM)" oldal használatával az Azure Portalon.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>A felhasználói fiók engedélyt a felügyelt tárfiókok

>[!TIP] 
> Ugyanúgy, mint az Azure AD biztosít egy **szolgáltatásnév** egy alkalmazás-identitás egy **egyszerű** lett megadva a felhasználó identitását. A felhasználó rendszerbiztonsági tag majd kaphatnak a Key Vault eléréséhez a Key Vault-hozzáférési házirend engedélyek engedélyezési.

Ugyanebben a PowerShell-munkamenetben használja, frissítse a Key Vault hozzáférési szabályzattal felügyelt tárfiókok esetében. Ebben a lépésben a felhasználói fiókjával, és annak biztosítása, hogy elérhető a felügyelt tárfiókok fiók funkciók storage-fiók engedélyeinek vonatkozik: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Vegye figyelembe, hogy a storage-fiókokra vonatkozó engedélyek nem érhetők el, a tárolási fiók "Hozzáférési házirendek" lapon az Azure Portalon.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>A Key Vault-példány hozzáadása egy felügyelt tárfiókot

Ugyanebben a PowerShell-munkamenetben használja, hozzon létre egy felügyelt tárfiókot a Key Vault-példány. A `-DisableAutoRegenerateKey` kapcsoló újragenerálni a tárfiókkulcsokat, nem adja meg.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Esetén a tárfiók nem kulcs újragenerálása sikeres hozzáadását az alábbi példához hasonló kimenetnek kell megjelennie:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Engedélyezze a kulcs újragenerálása

Ha azt szeretné, hogy rendszeresen generálja újra a tárfiók kulcsait a Key Vault, beállíthat egy regenerációs időszakot. A következő példában három napon belül újragenerálása állítjuk. Három nap után a Key Vault "1. kulcs" újragenerálása és cseréje: key1"a"2. kulcs: az aktív kulcs.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Esetén a tárfiók a kulcs újragenerálása sikeres emellett az alábbi példához hasonló kimenetnek kell megjelennie:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>További lépések

- [Storage-fiók kulcsok minták felügyelt](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell-referencia](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
