---
title: Az Azure Key Vault és az Azure CLI tárfiókkulcsok kezelése
description: A tárfiókkulcsok zökkenőmentes integráció az Azure Key Vault- és hozzáférés-alapú Azure storage-fiókba között adja meg.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476153"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Az Azure Key Vault és az Azure CLI tárfiókkulcsok kezelése 

Az Azure Key Vault kezeli az Azure storage-fiókok és a klasszikus tárfiókok kulcsait. A Key Vaultban felügyelt tárolási fiók funkció használatával végezze el a számos kulcskezelési funkciót az Ön számára.

Egy [Azure storage-fiók](/azure/storage/storage-create-storage-account) hitelesítő adatot egy fióknevet és a egy kulcs használja. A kulcs automatikusan létrehozott és szolgálja ki a jelszó, hanem egy, a titkosítási kulcs. A Key Vault kezeli a tárfiókkulcsok tárolja őket, mint [Key Vault titkos kódok](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Kulcsok szerepel a listában (szinkronizált) egy Azure storage-fiókot, és rendszeres időközönként újbóli vagy _elforgatott_. 

A felügyelt tárfiókok fiók kulcsfontosságú funkció használatakor vegye figyelembe a következőket:

- Kulcs értékeit a rendszer soha nem adja vissza a hívó adott válaszként.
- Csak a Key Vault kezelje a tárfiók kulcsait. Nem kezelhetik a kulcsokat, és elkerülheti a Key Vault folyamatok zavarása.
- Csak egyetlen Key Vault objektum felügyelje a tárfiók kulcsait. Több objektumot a kulcskezelés tiltása.
- A Key Vault kezelése a tárfiók, egy felhasználó rendszerbiztonsági tag, de nem egy egyszerű szolgáltatás kérhetnek.
- Csak a Key Vault kulcsainak újragenerálása. Manuálisan nem a tárfiókkulcsok újragenerálása. 

> [!NOTE]
> Az Azure Storage-integrációja az Azure Active Directory (Azure AD) a Microsoft felhőalapú identitás- és hozzáférés felügyeleti szolgáltatása.
> Az Azure AD-integráció érhető el a [Azure-blobok és üzenetsorok](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Azure AD használata a hitelesítéshez és engedélyezéshez.
> Az Azure AD hasonlóan az Azure Key Vault az Azure Storage OAuth2 jogkivonat-alapú hozzáférést biztosít.
>
> Az Azure AD lehetővé teszi, hogy hitelesítse az ügyfélalkalmazást egy alkalmazás vagy felhasználó identitását a tárfiók hitelesítő adatai helyett.
> Használhat egy [Azure ad-ben felügyelt identitás](/azure/active-directory/managed-identities-azure-resources/) futtatásakor az Azure-ban. Felügyelt identitások távolítsa el az ügyfél-hitelesítés és a hitelesítő adatok tárolása a, vagy az alkalmazással van szükség.
> Azure AD elküldi a szerepköralapú hozzáférés-vezérlés (RBAC) a hitelesítés, amelyet a Key Vault is támogat.

### <a name="service-principal-application-id"></a>Egyszerű szolgáltatás azonosítója

Az Azure AD-bérlő minden regisztrált alkalmazás biztosít egy [szolgáltatásnév](/azure/active-directory/develop/developer-glossary#service-principal-object). Az egyszerű szolgáltatás funkcionál az alkalmazás azonosítója (ID). Az Alkalmazásazonosítót engedélyezési telepítéskor szolgál az RBAC-n keresztül más Azure-erőforrásokhoz való hozzáférés.

A Key Vault egy Microsoft-alkalmazásba, amely előzetesen regisztrálva van az összes Azure AD-bérlőt. A Key Vault regisztrálva van, ugyanazon Alkalmazásazonosítóval, és minden egyes Azure-felhőben.

| Bérlők | Felhő | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Bármely | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Előfeltételek

A Key Vault használata kezelheti a tárfiók kulcsára, előtt tekintse át az előfeltételeket:

- Telepítse az [Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Hozzon létre egy [Azure storage-fiók](https://azure.microsoft.com/services/storage/). Hajtsa végre a [ezeket a lépéseket](https://docs.microsoft.com/azure/storage/).
- A tárfiók neve csak kisbetűket és számokat kell használnia. A név hossza 3 – 24 karakter hosszúnak kell lennie.        
      
## <a name="manage-storage-account-keys"></a>Storage-fiók kulcsainak kezelése

Storage-fiók kulcsainak kezelése a Key Vault használatával négy egyszerű lépésben történik:

1. Egy meglévő tárfiókot beolvasása.
1. Beolvassa egy meglévő kulcstartón.
1. A Key Vault felügyelt tárfiók hozzáadása a tárolóhoz. Állítsa be `key1` és a egy regenerációs időszakot 180 napig aktív kulcsként.
1. Használat `key1` a megadott tárfiók tárolási környezet beállításához.

> [!NOTE]
> A Key Vault szolgáltatás operátori a storage-fiók van hozzárendelve.
> 
> Miután beállította az Azure Key Vaultban felügyelt tárfiókkulcsok, csak módosítása a kulcsok a Key Vault használatával.
> A felügyelt tárfiókok hozzáférési kulcsait a Key Vault kezeli elforgatási szögét a tárfiók kulcsát.

1. Miután létrehozott egy tárfiókot, a következő parancsot kezelése a storage-fiók erőforrás Azonosítójának lekéréséhez:
    ```
    az storage account show -n storageaccountname
    ```

    Az erőforrás-Azonosítójának értéke átmásolja a parancs kimenete:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Példa a kimenetre:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. A "Storage fiók kulcs operátora – szolgáltatási szerepkör" RBAC szerepkör hozzárendelése a Key Vault. Ez a szerepkör korlátozza a hozzáférési hatókör a tárfiókhoz. Egy klasszikus tárfiók esetén használja a "Klasszikus tárolási fiók kulcs operátora – szolgáltatási szerepkör" szerepkört.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` az Objektumazonosító a Key vault az Azure nyilvános felhő. Objektum lekérése a Key vault az Azure Government cloud, lásd: [szolgáltatás egyszerű alkalmazás azonosítója](#service-principal-application-id).
    
1. Key Vault felügyelt tárfiók létrehozása:

    Beállítani a regenerációs időszakot 90 nap. 90 nap után a Key Vault újragenerálása `key1` és az aktív kulcs felcserélése `key2` való `key1`. `key1` Ezután van megjelölve az aktív kulcs. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Hozzon létre és jogkivonatokat hoz létre

Megkérheti a Key Vault létrehozása a közös hozzáférésű jogosultságkódok jogkivonataival is. Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. Biztosíthat az ügyfelek erőforrások elérését a tárfiókban lévő a fiókkulcsok megosztása nélkül. Közös hozzáférésű jogosultságkód biztosít egy biztonságos módon a tárolási erőforrások megosztását a fiókkulcsok veszélyeztetése nélkül.

Az ebben a szakaszban szereplő parancsokkal végezze el a következő műveleteket:

- Állítsa be egy megosztott hozzáférési aláírást definíció `<YourSASDefinitionName>`. A definíciója van megadva, a storage-fiók felügyelt Key Vault `<YourStorageAccountName>` tárol a kulcstárolóban `<VaultName>`.
- Hozzon létre egy fiók közös hozzáférésű jogosultságkód Blob, fájl, tábla és üzenetsor-szolgáltatásokhoz. A token Service, Container és objektum erőforrástípusok jön létre. A jogkivonat jön létre minden engedélyt, https protokollal, és a megadott kezdő és záró dátuma.
- Állítsa be a Key Vaultban felügyelt megosztott tároló hozzáférési aláírás definícióját a tárolóban. A definíció a sablon URI-ját a közös hozzáférésű jogosultságkód létrehozásának rendelkezik. A definíció van a közös hozzáférésű jogosultságkód típus `account` és érvényes N napig.
- A tényleges hozzáférési jogkivonat lekérése a Key Vault titkos kulcsából, amely megfelel a közös hozzáférésű jogosultságkód-definíció.

Miután elvégezte az előző szakasz lépéseit, a következő parancsokat kérje meg a Key Vault létrehozása a közös hozzáférésű jogosultságkód jogkivonata. 

1. Hozzon létre egy közös hozzáférésű jogosultságkód-definíció. A közös hozzáférésű jogosultságkód-definíció létrehozása után kérje meg a Key Vault létrehozása több közös hozzáférésű jogosultságkód jogkivonata. Ehhez a művelethez szükséges a `storage` és `setsas` engedélyeket.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    A művelettel kapcsolatos segítségért olvassa el a [az tárolási fiók sas generálása](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) referenciák dokumentációiba.

    A művelet sikeres futtatása után másolja ki a kimenetet.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Használja a `$sasToken` az előző parancs által létrehozott és a egy közös hozzáférésű jogosultságkód-definíció létrehozása. A parancssori paraméterekkel kapcsolatos további információkért tekintse meg a [az keyvault-tároló sas-definíció létrehozására](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) referenciák dokumentációiba.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Amikor a felhasználó nem rendelkezik jogosultsággal a tárfiókba, először kérje le a felhasználói objektum azonosítója:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Beolvassa a kódban a tokenek

Hajtsa végre a műveleteket a tárfiók által beolvasása [közös hozzáférésű jogosultságkódok jogkivonataival](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) a Key Vaultból.

A Key Vault hitelesítésre három módja van:

- Felügyeltszolgáltatás-identitás használata. Ez a megközelítés erősen ajánlott.
- Egy egyszerű szolgáltatás és a tanúsítványt használja. 
- Egy egyszerű szolgáltatás és a jelszót használja. Ez a módszer nem ajánlott.

További információkért lásd: [Azure Key Vault: Alapvető fogalmait](key-vault-whatis.md#basic-concepts).

A következő példa bemutatja, hogyan lehet beolvasni a közös hozzáférésű jogosultságkód jogkivonata. A tokenek egy közös hozzáférésű jogosultságkód-definíció létrehozása után olvassa be. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Ha a közös hozzáférésű jogosultságkód hamarosan lejár, a közös hozzáférésű jogosultságkód ismét beolvassa a Key Vaultból, és frissítse a kódot.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Az Azure parancssori felület parancsai

Az Azure CLI-parancsok, amely a felügyelt tárfiókok kapcsolatos információkért tekintse meg a [az keyvault tárolási](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) referenciadokumentációt.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [kulcsok, titkos kódok és tanúsítványok](https://docs.microsoft.com/rest/api/keyvault/).
- Tekintse át a cikkeket az a [Azure Key Vaultért felelős csapat blogjára](https://blogs.technet.microsoft.com/kv/).
