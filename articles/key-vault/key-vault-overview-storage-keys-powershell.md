---
title: Azure Key Vault felügyelt tárfiók – PowerShell-verzió
description: A felügyelt tárfiók funkció zökkenőmentes integrációt biztosít az Azure Key Vault és egy Azure storage-fiók között.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 833f78d89a1a9033e62c10c3b16c5adfc65e1da4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195124"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Tárfiók-kulcsok kezelése a Key Vault és az Azure PowerShell segítségével

Az Azure storage-fiók egy fióknévből és egy kulcsból álló hitelesítő adatokat használ. A kulcs automatikusan generálódik, és jelszóként szolgál, nem pedig kriptográfiai kulcsként. A Key Vault úgy kezeli a tárfiók kulcsait, hogy [kulcstartó-titokként](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)tárolja őket. 

A Key Vault felügyelt tárfiók kulcsfunkció használatával felsorolhatja (szinkronizálhatja) a kulcsokat egy Azure storage-fiókkal, és rendszeresidőközönként újragenerálhatja (elforgathatja) a kulcsokat. A storage-fiókok és a klasszikus tárfiókok kulcsait is kezelheti.

A felügyelt tárfiók kulcsszolgáltatás használatakor vegye figyelembe a következő pontokat:

- A kulcsértékek et soha nem adja vissza a hívónak adott válaszként.
- Csak a Key Vault kezelheti a tárfiók kulcsait. Ne kezelje saját maga a kulcsokat, és ne zavarja meg a Key Vault-folyamatokat.
- Csak egy Key Vault-objektum nak kell kezelnie a tárfiók kulcsait. Ne engedélyezze a kulcskezelést több objektumból.
- Kérheti a Key Vault a tárfiók kezelése egy egyszerű felhasználó, de nem egy egyszerű szolgáltatás.
- A kulcsok újragenerálása csak a Key Vault használatával. Ne hozza létre manuálisan a tárfiók kulcsait.

Az Azure Active Directory (Azure AD), a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatásának használatát javasoljuk. Az Azure AD-integráció elérhető az [Azure blobok és várólisták,](../storage/common/storage-auth-aad.md)és oAuth2 token alapú hozzáférést biztosít az Azure Storage (csakúgy, mint az Azure Key Vault).

Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését egy alkalmazás vagy felhasználói identitás használatával, a tárfiók hitelesítő adatai helyett. Az Azure [AD felügyelt identitást azure-beli](/azure/active-directory/managed-identities-azure-resources/) futtatáskor használhatja. A felügyelt identitások szükségtelent hoznak az ügyfélhitelesítésre és a hitelesítő adatok nak az alkalmazásban vagy az alkalmazással való tárolására.

Az Azure AD szerepköralapú hozzáférés-vezérlés (RBAC) használatával kezeli az engedélyezést, amelyet a Key Vault is támogat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Egyszerű szolgáltatásalkalmazás-azonosító

Az Azure AD-bérlő minden regisztrált alkalmazás számára [egy egyszerű szolgáltatás.](/azure/active-directory/develop/developer-glossary#service-principal-object) Az egyszerű szolgáltatás az alkalmazásazonosítóként szolgál, amely az engedélyezési beállítások során más Azure-erőforrásokrBAC-on keresztüli elérésére szolgál.

A Key Vault egy Microsoft-alkalmazás, amely előre regisztrált az összes Azure AD-bérlőben. Key Vault regisztrálva van ugyanazon az alkalmazásazonosító minden Azure-felhőben.

| Bérlők | Felhő | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure – nyilvános | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Bármelyik | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez először a következőket kell tennie:

- [Telepítse az Azure PowerShell-modult.](/powershell/azure/install-az-ps?view=azps-2.6.0)
- [Kulcstartó létrehozása](quick-create-powershell.md)
- [Hozzon létre egy Azure-tárfiókot.](../storage/common/storage-account-create.md?tabs=azure-powershell) A tárfiók neve csak kisbetűket és számokat használhat. A név hosszának 3 és 24 karakter között kell lennie.
      

## <a name="manage-storage-account-keys"></a>Tárfiók-kulcsok kezelése

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse PowerShell-munkamenetét a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsmag használatával. 

```azurepowershell-interactive
Connect-AzAccount
```
Ha több Azure-előfizetéssel rendelkezik, a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) parancsmag használatával listázhatja őket, és megadhatja a [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) parancsmaggal használni kívánt előfizetést. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Változók beállítása

Először állítsa be a PowerShell-parancsmagok által használandó változókat a következő lépésekben. Mindenképpen frissítse <YourResourceGroupName>a <YourStorageAccountName>, <YourKeyVaultName> és helyőrzőket, `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` és állítsa $keyVaultSpAppId (az [egyszerű alkalmazásazonosítóban](#service-principal-application-id)megadottak szerint) a fenti értékre.

Az Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) és [a Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) parancsmagokkal is lefogjuk szerezni a felhasználói azonosítót és az Azure storage-fiók környezetét.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Hozzáférés a Key Vault-hoz a tárfiókhoz

Ahhoz, hogy a Key Vault elérhesse és kezelhesse a tárfiók kulcsait, engedélyeznie kell annak hozzáférését a tárfiókhoz. A Key Vault alkalmazás engedélyeket igényel a tárfiók kulcsainak *listázásához* és *újragenerálásához.* Ezek az engedélyek a beépített RBAC szerepkör [tárfiókkulcs-operátori szolgáltatásszerepkörén keresztül engedélyezettek.](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role) 

Rendelje hozzá ezt a szerepkört a Key Vault egyszerű szolgáltatás, a hatókör korlátozása a tárfiók, az Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) parancsmag használatával.

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Sikeres szerepkör-hozzárendelés után a következő példához hasonló kimenetnek kell lennie:

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

Ha a Key Vault már hozzá lett adva a szerepkörhöz a tárfiókban, akkor *"A szerepkör-hozzárendelés már létezik" kap egy "A szerepkör-hozzárendelés már létezik."* hibát ad vissza. A szerepkör-hozzárendelést is ellenőrizheti az Azure Portal "Hozzáférés-vezérlési (IAM)" lapján.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>A felhasználói fiók engedélyének engedélyezése a kezelt tárfiókokhoz

Az Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) parancsmaggal frissítse a Key Vault hozzáférési szabályzatot, és adjon storage-fiók engedélyeket a felhasználói fióknak.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Vegye figyelembe, hogy a tárfiókok engedélyei nem érhetők el az Azure Portal "Access szabályzatok" lapján.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Felügyelt tárfiók hozzáadása a Key Vault-példányhoz

Az Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) parancsmag használatával hozzon létre egy felügyelt tárfiókot a Key Vault-példányban. A `-DisableAutoRegenerateKey` kapcsoló nem határozza meg a tárfiók kulcsainak újragenerálását.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

A tárfiók kulcsregenerálás nélküli sikeres hozzáadása után a következő példához hasonló kimenetnek kell lennie:

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

### <a name="enable-key-regeneration"></a>Kulcsregenerálás engedélyezése

Ha azt szeretné, hogy a Key Vault rendszeres időközönként újragenerálja a tárfiók-kulcsokat, az Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) parancsmag használatával regenerálási időszakot állíthat be. Ebben a példában három napos regenerációs időszakot állítunk be. Három nap elteltével a Key Vault újragenerálja a "key2" gombot, és az aktív kulcsot a "key2" -ről a "key1"-re cseréli.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

A tárfiók kulcs-regenerálással való sikeres hozzáadása után a következő példához hasonló kimenetnek kell látnia:

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

## <a name="shared-access-signature-tokens"></a>Megosztott hozzáférésű aláírás-jogkivonatok

Azt is kérheti a Key Vault közös hozzáférésű aláírás jogkivonatok létrehozásához. A megosztott hozzáférésű aláírás delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz. A fiókkulcsok megosztása nélkül is hozzáférést biztosíthat az ügyfeleknek a tárfiókban lévő erőforrásokhoz. A megosztott hozzáférésű aláírás biztonságos módot biztosít a tárolási erőforrások megosztására a fiókkulcsok veszélyeztetése nélkül.

Az ebben a szakaszban található parancsok a következő műveleteket hajtják végre:

- Fiók megosztott hozzáférés-aláírás-definíciójának beállítása. 
- Hozzon létre egy megosztott hozzáférésű aláírási jogkivonatot a Blob, a File, a Table és a Queue szolgáltatásokhoz. A jogkivonat szolgáltatás, tároló és objektum erőforrástípusokhoz jön létre. A jogkivonat jön létre az összes engedély, https-en keresztül, és a megadott kezdési és befejezési dátumokkal.
- Állítsa be a Key Vault felügyelt tároló megosztott hozzáférés-aláírás-definícióját a tárolóban. A definíció rendelkezik a létrehozott megosztott hozzáférés-aláírási jogkivonat sablon URI-jával. A definíció megosztott hozzáférésű `account` aláírástípussal rendelkezik, és N napokig érvényes.
- Ellenőrizze, hogy a megosztott hozzáférésű aláírás titkos kulcsként lett-e mentve a kulcstartóban.
- 
### <a name="set-variables"></a>Változók beállítása

Először állítsa be a PowerShell-parancsmagok által használandó változókat a következő lépésekben. Ügyeljen arra, <YourStorageAccountName> hogy <YourKeyVaultName> frissítse a és a helyőrzőket.

Az Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) parancsmagjait is használni fogjuk az Azure storage-fiók környezetének lefelvételéhez.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Megosztott hozzáférésű aláírási jogkivonat létrehozása

Hozzon létre egy közös hozzáférésű aláírás-definíciót az Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) parancsmagokkal.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
A $sasToken értéke ehhez hasonló lesz.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Megosztott hozzáférés-aláírás-definíció létrehozása

Használja az Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) parancsmag egy közös hozzáférésű aláírás-definíció.  Megadhatja a választott nevet `-Name` a paraméternek.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>A megosztott hozzáférés-aláírás-definíció ellenőrzése

Ellenőrizheti, hogy a megosztott hozzáférés-aláírás-definíció a key vaultban van-e tárolva az Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) parancsmag használatával.

Először keresse meg a megosztott hozzáférésű aláírás definícióját a kulcstartóban.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

A SAS-definíciónak megfelelő titkos kód a következő tulajdonságokkal rendelkezik:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Most már használhatja a [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) parancsmag és a titkos `Name` tulajdonság a titkos tartalom megtekintéséhez.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

A parancs kimenete a SAS-definíciós karakterláncot jeleníti meg.


## <a name="next-steps"></a>További lépések

- [Felügyelt tárfiókkulcs-minták](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [A kulcsok, titkos kódok és tanúsítványok ismertetése](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell-hivatkozás](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
