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
ms.openlocfilehash: 9bff93fbec73eb73dca01660d46e35e194edb626
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963488"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Az Azure Key Vaultban felügyelt tárfiók – CLI

> [!NOTE]
> [Az Azure storage-integráció az Azure Active Directory (Azure AD) már előzetes verzióban érhető el](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). A hitelesítéshez és engedélyezéshez, amely az Azure storage, csakúgy, mint az Azure Key Vault OAuth2 jogkivonat-alapú hozzáférést biztosít az Azure AD használatát javasoljuk. Ez lehetővé teszi, hogy:
> - Hitelesítse az ügyfélalkalmazást, egy alkalmazás vagy felhasználó identitását, helyett a tárfiók hitelesítő adatait. 
> - Használja az [Azure ad-ben felügyelt identitás](/azure/active-directory/managed-identities-azure-resources/) futtatásakor az Azure-ban. Felügyelt identitások távolítsa el az ügyfél-hitelesítéshez forrásokból együttesen kell és tárolását hitelesítő adatok a, vagy az alkalmazását.
> - Szerepkör alapú hozzáférés-vezérlés (RBAC) használata a kezeléséhez engedélyezésre, amelynek a Key Vault által is támogatott.

- Az Azure Key Vault kezeli a kulcsok az Azure Storage fiók (ASA).
    - Belsőleg az Azure Key Vault listázhatja az Azure Storage-fiók kulcsok (sync).    
    - Az Azure Key Vault újragenerálása (rotálja) a kulcsokat rendszeres időközönként.
    - Kulcs értékeit a rendszer soha nem adja vissza a hívó adott válaszként.
    - Az Azure Key Vault kezeli a Storage-fiókok és a klasszikus Tárfiókok kulcsait.
    
> [!IMPORTANT]
> Az Azure AD-bérlő minden regisztrált alkalmazás biztosít egy  **[szolgáltatásnév](/azure/active-directory/develop/developer-glossary#service-principal-object)**, amely funkcionál az alkalmazás azonosítóját. Az egyszerű szolgáltatás Alkalmazásazonosítója használt adná más Azure-erőforrások hozzáférési szerepköralapú hozzáférés-vezérlés (RBAC) révén. Mivel a Key Vault egy Microsoft-alkalmazásba, előre regisztrált összes az Azure AD bérlő alatt ugyanazon Alkalmazásazonosítóval, minden egyes Azure-felhőben lévő:
> - Az Azure government felhőben az Azure AD-bérlőt használja Alkalmazásazonosító `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Az Azure nyilvános felhő, és minden más Azure AD-bérlőt használja Alkalmazásazonosító `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Előfeltételek
--------------
1. [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) az Azure CLI telepítése   
2. [Storage-fiók létrehozása](https://azure.microsoft.com/services/storage/)
    - Kövesse a jelen [dokumentum](https://docs.microsoft.com/azure/storage/) a storage-fiók létrehozása  
    - **Elnevezési irányelvei:** A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Útmutató a Key Vault használata kezelheti a Tárfiók kulcsait. lépés
--------------------------------------------------------------------------------
A koncepciót tekintve, hogy teljesülnek a lépések listájában vannak
- Először lekérjük a (meglévő) storage-fiók
- Azt, majd beolvassa (meglévő) key vault
- Hogy majd KeyVault által felügyelt tárfiók hozzáadása a tárolóhoz, 1. kulcs beállítása az aktív kulcshoz és egy regenerációs időszakot 180 napig
- Végül beállított egy storage-környezetet, a megadott tárfiók, az 1. kulcs

Az az alábbi utasítások végrehajtásával, hogy társítja az Key Vault engedélyekkel kell rendelkeznie operátor a tárfiók szolgáltatásként

> [!NOTE]
> Kérjük vegye figyelembe, hogy miután beállította az Azure Key Vaultban felügyelt tárfiók a kulcsok akkor kell **nem** már módosítható, kivéve a Key Vault-n keresztül. A tároló kulcsait, az azt jelenti, hogy a Key Vault kezelne elforgatása a tárfiók kulcsát felügyelt

1. Miután létrehozott egy tárfiókot, a storage-fiók erőforrás-azonosító beolvasásához a következő parancsot, a felügyelni kívánt

    ```
    az storage account show -n storageaccountname 
    ```
    Másolja ki a fenti parancs eredménye azonosító mező
    
2. Lekéréséhez objektum azonosítója az Azure Key Vault szolgáltatás egyszerű futtassa az alábbi parancs

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    Ez a parancs sikeres befejezését követően keresse meg az eredményben Objektumazonosítóját:
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. A Storage-kulcs operátori szerepkör hozzárendelése az Azure Key Vault identitást.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Key Vault létrehozása felügyelt Tárfiók.     <br /><br />
   Az alábbi azt állítja egy 90 napos regenerációs időszakot. 90 nap után a Key Vault "1. kulcs" újragenerálása és cseréje: key1"a"2. kulcs: az aktív kulcs. Ez lezárásával befejezettként jelöli meg Key1 aktív kulcsként most. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    Abban az esetben a felhasználó nem hozott létre a tárfiókot, és nem rendelkezik engedélyekkel a storage-fiókba, az alábbi lépéseket, győződjön meg arról, hogy a Key Vault a tárolási engedélyek segítségével kezelheti a fiók engedélyeit, állítsa be.
    

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Útmutató a Key Vault használatát létrehozása és SAS-jogkivonatokat hoz létre. lépés
--------------------------------------------------------------------------------
Is megkérheti a Key Vault SAS (közös hozzáférésű Jogosultságkód) jogkivonatokat hoz létre. Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. A SAS használatával biztosíthat az ügyfelek erőforrásokhoz való hozzáférés a tárfiókban lévő a fiókkulcsok megosztása nélkül. Ez a lényege a közös hozzáférésű jogosultságkód alkalmazásokban való használatának – az SAS a fiókkulcsok veszélyeztetése nélkül teszi lehetővé a tárolási erőforrások megosztását.

Miután végzett a fent felsorolt lépéseket futtathatja kérje meg a Key Vault-, SAS-jogkivonatokat hoz létre a következő parancsokat. 

A lista azokról a dolgokról, amelyek a azok megvalósítására az alábbi lépéseket is
- Beállítja egy SAS-definíciója nevű fiók "<YourSASDefinitionName>"a "KeyVault által felügyelt storage-fiók<YourStorageAccountName>"a a tár'<VaultName>". 
- Létrehoz egy SAS-jogkivonata szolgáltatások Blob, fájl, tábla és üzenetsor, erőforrástípusok Service, a tároló és az objektum, minden engedélyt https-kapcsolaton keresztül és a megadott kezdő és záró dátuma
- A KeyVault által felügyelt tárolási SAS-definíciója beállítja a tárolóban, a sablon URI-N napig fent, SAS típusa "account", és érvényes létrehozott SAS-token
- A tényleges hozzáférési jogkivonat lekéri a KeyVault titkos kulcsnak megfelelő az SAS-definíciója

1. Ebben a lépésben hozunk létre egy SAS-definíciója. Miután létrejött az SAS-definíciója, megkérheti a Key Vault további SAS-jogkivonatok létrehozásához meg. Ez a művelet a tárolás/setsas engedélyre van szüksége.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
Láthatja, hogy a fenti művelettel kapcsolatos további segítségért [Itt](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)

Ha ez a művelet sikeresen lefutott, lent látható módon a hasonló kimenetnek kell megjelennie. Másolja ki, amely

```console
   "se=2020-01-01&sp=***"
```

2. Ebben a lépésben használjuk az előállított kimeneti adatokat ($sasToken) feletti hozzon létre egy SAS-definíciót. További dokumentáció olvasási [Itt](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > Abban az esetben, hogy a felhasználó nem rendelkezik engedélyekkel a tárfiók először lekérjük a felhasználó objektumazonosítóját

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="fetch-sas-tokens-in-code"></a>SAS-tokeneket kód beolvasása

Ebben a szakaszban ismertetjük, hogyan elvégezhető műveletek a tárfiók a beolvasása [SAS-tokeneket](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) a Key Vaultból

Az az alábbi szakaszban bemutatjuk, hogyan SAS-tokeneket beolvasni egy SAS-definíciója, ahogyan fentebb létrehozása után.

> [!NOTE] 
  Hitelesítés a Key Vaultba, mert olvashat 3 módon a [alapvető fogalmai](key-vault-whatis.md#basic-concepts)
> - Felügyeltszolgáltatás-identitás (ajánlott) használatával
> - Egyszerű szolgáltatás és a tanúsítvány használatával 
> - Egyszerű szolgáltatás és a jelszóval (nem ajánlott)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Ha lejár az SAS-jogkivonatot, majd kívánja a SAS-jogkivonat ismét beolvassa a Key Vaultból, majd a kód frissítése

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


### <a name="relavant-azure-cli-cmdlets"></a>Relavant Azure CLI-parancsmagok
[A Storage-parancsmagok az Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

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
