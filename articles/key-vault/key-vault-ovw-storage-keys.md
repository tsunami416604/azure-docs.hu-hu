---
ms.assetid: ''
title: Az Azure Key Vault Tárfiókkulcsok
description: Tárfiókkulcsok az Azure Storage-fiókot adjon meg egy Azure Key Vault és a kulcs alapú hozzáférés seemless integrációjával.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179588"
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
1. Tárolja a kapcsolati karakterlánc vagy SAS-jogkivonatot az Azure App Service alkalmazás beállításait, vagy egy másik tárolási.
1. Alkalmazás indításkor hívjon le a kapcsolati karakterlánc- vagy SAS-jogkivonat.
1. Hozzon létre [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) tárolási kommunikál.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Az Azure Key Vault tárolási kulcsok után

A fejlesztők hozzon létre egy [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) és a SAS-jogkivonat lekérése a tároló, amely kihasználja. Ezt követően, hogy létre [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) adott tokenhez.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Fejlesztői útmutató

- Engedélyezése csak a Key Vault a ASA kulcsok kezeléséhez. Ne kísérelje meg magának kezelhetők, a Key Vault folyamatok zavarja meg.
- Több Key Vault objektum által kezelt ASA kulcsok nem engedélyezett.
- Manuálisan újragenerálja a ASA kulcsok van szüksége, azt javasoljuk keresztül Key Vault generálni.

## <a name="getting-started"></a>Első lépések

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>A szerepköralapú hozzáférés-vezérlést (RBAC) engedélyek beállítása

Az Azure Key Vault identitása engedélyeket kell *lista* és *újragenerálja* tárfiókok esetén a kulcsok. Állítsa be ezeket az engedélyeket az alábbi lépéseket követve:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Munka – példa

A következő példa bemutatja a kulcstároló létrehozása kezelt Azure Storage-fiókot és a társított közös hozzáférésű Jogosultságkód (SAS)-definíciót.

### <a name="prerequisite"></a>Előfeltétel

Győződjön meg arról, hogy végrehajtotta [szerepköralapú hozzáférés-vezérlést (RBAC) engedélyek beállítása](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Beállítás

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Ezután állítsa be a **fiókja** annak érdekében, hogy a Key Vault a tárolási engedélyekkel kezelheti. Az alábbi példában az Azure-fiókra van _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Hozzon létre egy kulcstartót Tárfiók felügyelete

Most felügyelt Storage-fiók létrehozása az Azure Key Vault, és a SAS-jogkivonat készítése esetleges a storage-fiókhoz tartozó hozzáférési kulcsot használ.
- `-ActiveKeyName` a SAS-jogkivonatok létrehozásához használja a "key2".
- `-AccountName` a felügyelt storage-fiók azonosítására szolgál. Az alábbi egyszerű biztosítható, hogy a tárfiók neve használunk, de ez bármilyen választott név lehet.
- `-DisableAutoRegenerateKey` Itt adhatja meg újragenerálni a tárfiókkulcsokat.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Kulcs újragenerálása

Ha azt szeretné, hogy a Key Vault rendszeresen újragenerálja a tárelérési kulcsot, beállíthat egy újragenerálása időszak. Az alábbi 3 nap újragenerálása időtartamra állítja azt. 3 nap után a Key Vault "key1" generálni, és az aktív-kulcsot "key2" a "key1" felcserélése.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>SAS-definíciók beállítása

A fiók SAS eltérő engedélyekkel a blob szolgáltatás hozzáférést biztosít.
Állítsa be a SAS-definíciókat a Key Vault a felügyelt tárfiók.
- `-AccountName` a kezelt tárfiókot a kulcstároló neve van.
- `-Name` az azonosítóját, a SAS-jogkivonat a tárolóban van.
- `-ValidityPeriod` Beállítja a generált SAS-jogkivonat lejárati dátuma.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>A SAS-jogkivonatok lekérésére

A megfelelő SAS-jogkivonatok lekérésére, és a Storage-hívások. `-SecretName` a bemeneti segítségével jön létre a `AccountName` és `Name` hajtja végre paramétereket [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Tároló létrehozása

Figyelje meg, hogy az elérni próbált *$readSasToken* nem sikerül, de ez lehet hozzáférni a *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Biztosan férnek hozzá a tárolási blob tartalom legyen írási hozzáférése a SAS-jogkivonatot.

### <a name="relevant-powershell-cmdlets"></a>Megfelelő Powershell-parancsmagok

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

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

## <a name="see-also"></a>Lásd még

- [A kulcsok, titkos kódok és tanúsítványok ismertetése](https://docs.microsoft.com/rest/api/keyvault/)
- [Kulcstároló csapat blogja](https://blogs.technet.microsoft.com/kv/)
