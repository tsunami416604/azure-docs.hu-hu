---
ms.assetid: 
title: "Az Azure Key Vault Tárfiókkulcsok"
description: "Tárfiókkulcsok az Azure Storage-fiókot adjon meg egy Azure Key Vault és a kulcs alapú hozzáférés seemless integrációjával."
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: a87877f4b213365442400d113a67964ef942341f
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="azure-key-vault-storage-account-keys"></a>Az Azure Key Vault Tárfiókkulcsok

Azure Key Vault Tárfiókok kulcsait, mielőtt a fejlesztők kellett a saját Azure Storage-fiók (ASA) kulcsok kezelése és elforgatása őket manuálisan vagy egy külső automation. Most, a Key Vault Tárfiókok kulcsait, megvalósítva [Key Vault titkok](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) hitelesítéséhez az Azure Storage-fiók. 

Az Azure Storage-fiók (ASA) alapfunkciója kezeli az Ön titkos elforgatás. Is eltávolítja a közvetlen kapcsolattól szükség ASA kulccsal rendelkező megosztott hozzáférési aláírásokkal (SAS) módszerként felajánlásával. 

Az Azure Storage-fiókokról további általános információkért lásd: [tudnivalók az Azure storage-fiókok](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Kapcsolatok támogatása

A teljes listát és a programozási és parancsfájl-kezelési felület mutató hivatkozásokat talál a [Key Vault fejlesztői útmutatója](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Key Vault kezeli

Key Vault több belső felügyeleti funkciókat az Ön nevében hajtja végre a Tárfiókkulcsok felügyelt használatakor.

- Az Azure Key Vault kezeli a kulcsokat egy Azure Storage figyelembe (ASA).
    - Belsőleg az Azure Key Vault készíthetünk egy Azure Storage-fiók (sync) kulcsokat.  
    - Az Azure Key Vault újragenerálja (forog) a kulcsok rendszeres időközönként. 
    - Értékek soha nem visszakerülnek a hívó adott válaszként. 
    - Az Azure Key Vault kezeli a Storage-fiókok és a klasszikus Tárfiókokat kulcsokat. 
- Az Azure Key Vault lehetővé teszi, a tároló/objektum tulajdonosa, SAS (fiók vagy szolgáltatás SAS)-meghatározások létrehozása.
    - A SAS értéket, SAS-definícióban használatával létrehozott egy titkos kulcsként adja vissza a többi URI elérési útján. További információkért lásd: [Azure Key Vault tárolási fiók műveletek](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Elnevezési irányelvei

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.  
- A SAS-definíció nevét 1-102 karakter hosszúságú-csak 0-9, a – z, A-Z kell lennie.

## <a name="developer-experience"></a>A fejlesztői változat

### <a name="before-azure-key-vault-storage-keys"></a>Az Azure Key Vault tárolási kulcsok előtt 

A fejlesztők kell tennie az alábbi eljárásokkal a tárfiók kulcsa a használatával is elérheti az Azure storage. 
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Az Azure Key Vault tárolási kulcsok után 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Fejlesztői útmutató

- Engedélyezése csak a Key Vault a ASA kulcsok kezeléséhez. Ne kísérelje meg magának kezelhetők, a Key Vault folyamatok zavarja meg. 
- Több Key Vault objektum által kezelt ASA kulcsok nem engedélyezett. 
- Manuálisan újragenerálja a ASA kulcsok van szüksége, azt javasoljuk keresztül Key Vault generálni. 

## <a name="getting-started"></a>Bevezetés

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>A szerepköralapú hozzáférés-vezérlést (RBAC) engedélyek beállítása

Az Azure Key Vault identitása engedélyeket kell *lista* és *újragenerálja* tárfiókok esetén a kulcsok. Állítsa be ezeket az engedélyeket az alábbi lépéseket követve:

- Töltse le az Azure Key Vault identitás ObjectId: 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- Tárolási kulcs kezelői szerepkör hozzárendelése az Azure Key Vault Identity: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > A klasszikus fióktípus be a szerepkör-paraméter *"Klasszikus tárolási fiók kulcs operátori szerepkör-szolgáltatás."*

## <a name="working-example"></a>Munka – példa

A következő példa bemutatja a kulcstároló létrehozása kezelt Azure Storage-fiókot és a társított közös hozzáférésű Jogosultságkód (SAS)-definíciót.

### <a name="assumptions"></a>Előfeltételek

Az alábbi utasításokat a működő például givens.

- A tároló-erőforrás a következő helyen található: */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- A kulcstároló neve: *yourtest1*

### <a name="get-a-service-principal"></a>A szolgáltatás egyszerű beolvasása

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Az előző parancs megjeleníti a szolgáltatásnév, amely Felhívjuk *yourKeyVaultServicePrincipalId*. 

### <a name="set-permissions"></a>Engedélyek beállítása

Győződjön meg arról, hogy a tároló engedély *összes*. YouruserPrincipalId lekérni, és engedélyeket a tárolóban, az alábbi parancsok használatával.

```powershell
$youruserPrincipalId = (Get-AzureRmADUser -SearchString "your user principal name").Id
```
Most keresse meg a nevét, és a kapcsolódó ObjectId, mert ezzel fogja a tároló engedélyeinek beállítása az beszerzése.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $youruserPrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Hozzáférés engedélyezése

Hozzá kell rendelnie a Key Vault szolgáltatás hozzáférést a storage-fiókokra, a felügyelt storage-fiókok és a SAS-definíciók létrehozása előtt.

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Storage-fiók létrehozása

Most hozzon létre egy felügyelt Tárfiókot és két SAS-definíciók. A fiók SAS eltérő engedélyekkel a blob szolgáltatás hozzáférést biztosít.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Újragenerálása

Állítsa be az újbóli időszakot az alábbi parancsok használatával.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>SAS-definíciók beállítása

Állítsa be a SAS-definíciókat a Key Vault a felügyelt tárfiók.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Szerezze be a tokent

A megfelelő SAS-jogkivonatok lekérésére, és a Storage-hívások.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Tároló létrehozása

Figyelje meg, hogy az elérni próbált *$sastoken1* nem sikerül, de ez lehet hozzáférni a *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Összegző – példa

Biztosan férnek hozzá a tárolási blob tartalom legyen írási hozzáférése a SAS-jogkivonatot.

### <a name="relevant-powershell-cmdlets"></a>Megfelelő Powershell-parancsmagok

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Adja hozzá AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Frissítés-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

## <a name="storage-account-onboarding"></a>Tárolási fiók bevezetése 

Példa: tulajdonosaként Key Vault objektumot ad hozzá egy tárolóobjektumot fiók az Azure Key Vault szolgáltatás bevezetésében egy tárfiókot.

Bevezetési, során Key Vault kell győződjön meg arról, hogy a bevezetési fiók identitását jogosult legyen *lista* , és *újragenerálja* tárolási kulcsok. Ahhoz, hogy ezek az engedélyek ellenőrzéséhez a Key Vault lekérése egy OBO (a nevében a) token a hitelesítési szolgáltatás, állítsa be az Azure Resource Manager célközönség, és lehetővé teszi egy *lista* kulcsát az Azure Storage szolgáltatás hívása. Ha a *lista* hívás sikertelen lesz, a Key Vault objektum-létrehozás sikertelen, és a HTTP-állapotkódot *tiltott*. Ilyen módon szereplő kulcsok gyorsítótárba kerüljenek-e a kulcstartót entitás tárolóval. 

Key Vault ellenőriznie kell, hogy rendelkezik-e az identity *újragenerálja* engedélyek előtt tulajdonjogát, a kulcsok újragenerálása is igénybe vehet. Győződjön meg arról, hogy rendelkezik-e ezeket az engedélyeket az identitást, keresztül OBO jogkivonatot, valamint a Key Vault első fél identitás: a

- Key Vault a tárolási fiók erőforrás RBAC engedélyeinek sorolja fel.
- Key Vault érvényesíti a válaszból reguláris kifejezések egyeztetésének műveletek és a nem műveletek keresztül. 

A támogató példákat található [Key Vault - kezelt tárolási fiók kulcsok minták](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Ha az identitás nem rendelkezik *újragenerálja* engedélyeket, vagy ha a Key Vault első fél identitása nem rendelkezik *lista* vagy *újragenerálja* engedélyt, majd a Bevezetés kérelem sikertelen lesz, egy megfelelő hibakód és az üzenet vissza. 

A OBO jogkivonat-k, a PowerShell vagy a CLI natív ügyfélalkalmazások használatakor csak működik.

## <a name="other-applications"></a>Más alkalmazások

- SAS-tokenje használatával a Key Vault tárfiókok kulcsait, az Azure-tárfiók még több ellenőrzött hozzáférést biztosítanak. További információkért lásd: [használata közös hozzáférésű jogosultságkód](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Lásd még:

- [A kulcsok, titkos kódok és tanúsítványok ismertetése](https://docs.microsoft.com/rest/api/keyvault/)
- [Kulcstároló csapat blogja](https://blogs.technet.microsoft.com/kv/)
