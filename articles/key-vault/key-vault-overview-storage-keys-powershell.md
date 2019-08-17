---
title: Azure Key Vault felügyelt Storage-fiók – PowerShell-verzió
description: A felügyelt tár fiók szolgáltatás seemless integrációt biztosít Azure Key Vault és egy Azure Storage-fiók között.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: df377b19d78a63b3cfc57347fff00345a9c63ead
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562531"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Felügyelt Storage-fiók Azure Key Vault – PowerShell

> [!NOTE]
> Az [Azure Storage és a Azure Active Directory (Azure ad) integrációja már előzetes](../storage/common/storage-auth-aad.md)verzióban érhető el. Javasoljuk, hogy használja az Azure AD-t a hitelesítéshez és az engedélyezéshez, amely OAuth2 jogkivonat-alapú hozzáférést biztosít az Azure Storage-hoz, akárcsak a Azure Key Vault. Ez a következőket teszi lehetővé:
> - Az ügyfélalkalmazás hitelesítése a Storage-fiók hitelesítő adatai helyett alkalmazás vagy felhasználói identitás használatával. 
> - Azure AD-beli [felügyelt identitás](/azure/active-directory/managed-identities-azure-resources/) használata az Azure-on való futtatáskor. A felügyelt identitások teljes egészében megszüntetik az ügyfél-hitelesítés szükségességét, és a hitelesítő adatokat a vagy az alkalmazásban tárolják.
> - Használja a szerepköralapú Access Control (RBAC) az engedélyezés felügyeletéhez, amelyet a Key Vault is támogat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az [Azure Storage-fiók](/azure/storage/storage-create-storage-account) olyan hitelesítő adatokat használ, amely egy fióknevet és egy kulcsot tartalmaz. A kulcs automatikusan létrejön, és a titkosítási kulcs helyett "jelszóként" szolgál. Key Vault kezelheti ezeket a Storage-fiók kulcsait, ha [Key Vault titokként](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)tárolja őket. 

## <a name="overview"></a>Áttekintés

A Key Vault felügyelt Storage-fiók szolgáltatás számos felügyeleti funkciót hajt végre az Ön nevében:

- Egy Azure Storage-fiókkal rendelkező (szinkronizált) kulcsok listája.
- A kulcsok rendszeres újragenerálása (elforgatása).
- A a Storage-fiókok és a klasszikus Storage-fiókok kulcsait kezeli.
- A rendszer soha nem adja vissza a kulcs értékeit a hívónak válaszul.

A felügyelt Storage-fiók kulcsa funkció használata esetén:

- **Csak Key Vault engedélyezése a Storage-fiók kulcsainak kezeléséhez.** Ne próbálja meg önállóan kezelni őket, mert az Key Vault folyamatait zavarja.
- **Ne engedélyezze, hogy a Storage-fiókok kulcsai egynél több Key Vault objektummal felügyelhetők legyenek**.
- **Ne végezze el manuálisan a Storage-fiók kulcsainak**újragenerálása. Javasoljuk, hogy Key Vault használatával újra létrehozza őket.

Az alábbi példa bemutatja, hogyan engedélyezheti Key Vault a Storage-fiók kulcsainak kezelését.

## <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse a PowerShell-munkamenetet a Connection [-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsmag használatával. 
```azurepowershell-interactive
Connect-AzAccount
```
Ha több Azure-előfizetéssel rendelkezik, a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) parancsmag használatával is listázhatja őket, és megadhatja a [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) parancsmaggal használni kívánt előfizetést. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Key Vault engedélyezése a Storage-fiókhoz való hozzáféréshez

> [!IMPORTANT]
> Az Azure AD-bérlő minden regisztrált alkalmazást egy **[egyszerű szolgáltatással](/azure/active-directory/develop/developer-glossary#service-principal-object)** biztosít, amely az alkalmazás identitását szolgálja. Az egyszerű szolgáltatás alkalmazás-AZONOSÍTÓját akkor használja a rendszer, ha a szerepköralapú hozzáférés-vezérlésen (RBAC) keresztül engedélyezi az informatikai engedélyt más Azure-erőforrásokhoz való hozzáférésre. Mivel a Key Vault egy Microsoft-alkalmazás, az összes Azure AD-bérlőben előzetesen regisztrálva van minden Azure-felhőben:
> - Az Azure Government Cloud Azure AD-bérlői az alkalmazás `7e7c393b-45d0-48b1-a35e-2905ddf8183c`azonosítóját használják.
> - Azure AD-bérlők az Azure nyilvános felhőben, az összes többi `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`pedig az alkalmazás azonosítóját használja.

Mielőtt Key Vault a Storage-fiók kulcsainak elérését és kezelését, engedélyeznie kell a Storage-fiókjához való hozzáférését. A Key Vault alkalmazásnak engedélyekkel kell rendelkeznie a Storage-fiók kulcsainak listázásához és *újbóli létrehozásához* . Ezek az engedélyek engedélyezve vannak a beépített RBAC szerepkör- [kezelő szolgáltatás](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)szerepkörrel. 

Rendelje hozzá ezt a szerepkört az Key Vault egyszerű szolgáltatáshoz, és korlátozza a hatókört a Storage-fiókra az alábbi lépések segítségével. A (z), `$resourceGroupName`, `$storageAccountName`és `$storageAccountKey` `$keyVaultName` változókat a parancsfájl futtatása előtt frissítse:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Get your User Id for later commands
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Sikeres szerepkör-hozzárendelés esetén a következő példához hasonló kimenetnek kell megjelennie:

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

Ha Key Vault már hozzá lett adva a szerepkörhöz a Storage-fiókjában, akkor a *"szerepkör-hozzárendelés már létezik* " hibaüzenet jelenik meg. hiba. A szerepkör-hozzárendelést is ellenőrizheti, ha a Azure Portal a Storage-fiók "hozzáférés-vezérlés (IAM)" lapját használja.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Felhasználói fiók engedélyezése a felügyelt Storage-fiókok számára

>[!TIP] 
> Ugyanúgy, ahogy az Azure AD egy **egyszerű szolgáltatást** biztosít az alkalmazás identitásához, egy **felhasználói rendszerbiztonsági tag** van megadva a felhasználó identitásához. A felhasználói tag ezután engedélyt kaphat a Key Vault elérésére Key Vault hozzáférési házirend engedélyeivel.

Ugyanazzal a PowerShell-munkamenettel frissítse a felügyelt Storage-fiókok Key Vault hozzáférési házirendjét. Ez a lépés a Storage-fiók engedélyeit alkalmazza a felhasználói fiókjára, így biztosíthatja, hogy hozzáférhessen a felügyelt tár fiók szolgáltatásaihoz: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Vegye figyelembe, hogy a Storage-fiókokra vonatkozó engedélyek nem érhetők el a Azure Portal "hozzáférési szabályzatok" lapján.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Felügyelt Storage-fiók hozzáadása a Key Vault-példányhoz

Ugyanazzal a PowerShell-munkamenettel hozzon létre egy felügyelt Storage-fiókot a Key Vault-példányban. A `-DisableAutoRegenerateKey` kapcsoló azt adja meg, hogy ne generálja újra a Storage-fiók kulcsait.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Ha a Storage-fiókot a kulcs újragenerálása nélkül is sikeresen felhasználta, az alábbi példához hasonló kimenetnek kell megjelennie:

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

### <a name="enable-key-regeneration"></a>Kulcs újragenerálásának engedélyezése

Ha azt szeretné, hogy a Key Vault rendszeresen újragenerálja a Storage-fiók kulcsait, beállíthat egy újragenerálási időszakot. A következő példában egy három napos újragenerálási időszakot állítunk be. Három nap elteltével Key Vault újragenerálta a "key1", és az aktív kulcsot a "key2" értékről "key1"-re cseréli.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

A Storage-fiók a kulcs újragenerálásával való sikeres hozzáadását követően az alábbi példához hasonló kimenetnek kell megjelennie:

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

- [Felügyelt Storage-fiók kulcsainak mintái](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [PowerShell-útmutató Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
