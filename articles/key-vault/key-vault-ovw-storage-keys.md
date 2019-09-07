---
title: A Storage-fiók kulcsainak kezelése a Azure Key Vault és az Azure CLI használatával
description: A Storage-fiókok kulcsai zökkenőmentes integrációt biztosítanak a Azure Key Vault és a kulcs-alapú Azure Storage-fiókhoz való hozzáférés között.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 251a7c21b671052a23f6ee18cb4278737464b25c
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744874"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>A Storage-fiók kulcsainak kezelése a Azure Key Vault és az Azure CLI használatával 

A Azure Key Vault az Azure Storage-fiókok és a klasszikus Storage-fiókok kulcsait kezeli. A Key Vault felügyelt tár fiók funkció használatával több kulcskezelő funkciót is elvégezhet.

Az [Azure Storage-fiók](/azure/storage/storage-create-storage-account) olyan hitelesítő adatokat használ, amely egy fióknevet és egy kulcsot tartalmaz. A kulcs automatikusan létrejön, és jelszóként szolgál, nem pedig titkosítási kulcsként. A Key Vault a Storage-fiókok kulcsait úgy kezeli, hogy [Key Vault titokként](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)tárolja őket. A kulcsok egy Azure Storage-fiókkal vannak felsorolva (szinkronizálva), és rendszeresen újragenerálják vagy _elforgatják_őket. 

A felügyelt Storage-fiók kulcsa funkció használata esetén vegye figyelembe a következő szempontokat:

- A rendszer soha nem adja vissza a kulcs értékeit a hívónak adott válaszként.
- Csak Key Vault kell kezelnie a Storage-fiók kulcsait. Ne kezelje a kulcsokat, és ne zavarja a Key Vault folyamatokat.
- Csak egyetlen Key Vault objektumnak kell kezelnie a Storage-fiók kulcsait. Ne engedélyezze a kulcskezelő szolgáltatás több objektumból való felügyeletét.
- Key Vault kérheti, hogy kezelje a Storage-fiókját egy egyszerű felhasználóval, de nem egy egyszerű szolgáltatásnév használatával.
- Kulcsok újragenerálása csak Key Vault használatával. Ne végezze el manuálisan a Storage-fiók kulcsainak újragenerálása. 

> [!NOTE]
> Az Azure Storage és a Azure Active Directory (Azure AD) integrációja a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása.
> Az Azure AD [-integráció Azure-blobokhoz és-várólistákhoz](../storage/common/storage-auth-aad.md)érhető el.
> Használja az Azure AD-t a hitelesítéshez és az engedélyezéshez.
> Az Azure AD OAuth2 jogkivonat-alapú hozzáférést biztosít az Azure Storage-hoz, akárcsak a Azure Key Vault.
>
> Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését alkalmazás vagy felhasználói identitás használatával a Storage-fiók hitelesítő adatai helyett.
> Azure AD-beli [felügyelt identitást](/azure/active-directory/managed-identities-azure-resources/) használhat az Azure-ban való futtatáskor. A felügyelt identitások nem szükségesek az ügyfél-hitelesítéshez és a hitelesítő adatok tárolásához a vagy az alkalmazásban.
> Az Azure AD szerepköralapú hozzáférés-vezérlést (RBAC) használ az engedélyezés kezelésére, amelyet a Key Vault is támogat.

### <a name="service-principal-application-id"></a>Egyszerű szolgáltatásnév alkalmazásának azonosítója

Az Azure AD-bérlő minden regisztrált alkalmazást biztosít egy [egyszerű szolgáltatással](/azure/active-directory/develop/developer-glossary#service-principal-object). Az egyszerű szolgáltatásnév az alkalmazás identitása (azonosító). Az alkalmazás AZONOSÍTÓját a rendszer az RBAC-on keresztül más Azure-erőforrásokhoz való hozzáférés engedélyezési beállítása során használja.

A Key Vault egy olyan Microsoft-alkalmazás, amely az összes Azure AD-bérlőben előre regisztrálva van. A Key Vault ugyanabban az alkalmazás-AZONOSÍTÓban és minden egyes Azure-felhőben regisztrálva van.

| Bérlők | Felhő | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Nyilvános Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Előfeltételek

A Storage-fiók kulcsainak kezeléséhez Key Vault használata előtt tekintse át az előfeltételeket:

- Telepítse az [Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Hozzon létre egy [Azure Storage-fiókot](https://azure.microsoft.com/services/storage/). Kövesse [az alábbi lépéseket](../storage/index.yml).
- A Storage-fiók nevének csak kisbetűket és számokat kell használnia. A név hosszának 3 és 24 karakter közöttinek kell lennie.        
      
## <a name="manage-storage-account-keys"></a>A Storage-fiók kulcsainak kezelése

A Storage-fiók kulcsainak kezeléséhez négy alapvető lépést kell megtenni a Key Vault használatával:

1. Meglévő Storage-fiók beszerzése.
1. Meglévő kulcstartó beolvasása.
1. Vegyen fel egy Key Vault felügyelt Storage-fiókot a tárolóba. Állítsa `key1` aktív kulcsként egy 90 napos újragenerálási időszakra.
1. Ezzel `key1` a beállítással állíthatja be a megadott Storage-fiók tárolási környezetét.

> [!NOTE]
> Key Vault a szolgáltatáshoz hozzárendelt operátori engedélyek vannak társítva a Storage-fiókhoz.
> 
> Miután beállította Azure Key Vault felügyelt Storage-fiók kulcsait, csak Key Vault használatával módosítsa a kulcsokat.
> A felügyelt Storage-fiókok kulcsainál Key Vault kezeli a Storage-fiók kulcsainak forgását.

1. Miután létrehozta a Storage-fiókot, futtassa a következő parancsot a kezelendő Storage-fiók erőforrás-AZONOSÍTÓjának lekéréséhez:
    ```
    az storage account show -n storageaccountname
    ```

    Másolja ki az erőforrás-azonosító értékét a parancs kimenetből:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Példa a kimenetre:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Rendelje hozzá a "Storage-fiók kulcsának kezelője szolgáltatási szerepkört" RBAC szerepkört a Key Vaulthoz. Ez a szerepkör korlátozza a hozzáférési hatókört a Storage-fiókra. Klasszikus Storage-fiók esetén használja a "klasszikus Storage-fiók kulcsát kezelő szolgáltatás szerepkör" szerepkört.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`Az Azure-beli nyilvános felhőben Key Vault objektum azonosítója. A Azure Government felhőben lévő Key Vault objektumazonosító beszerzéséhez tekintse meg a [szolgáltatásnév alkalmazás-azonosítóját](#service-principal-application-id).
    
1. Key Vault felügyelt Storage-fiók létrehozása:

    Állítsa be a 90 napos újragenerálási időszakot. 90 nap után Key Vault újragenerálta `key1` és felcseréli az aktív `key2` kulcsot `key1`a verzióról a verzióra. `key1`Ekkor az aktív kulcsként van megjelölve. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Jogkivonatok létrehozása és létrehozása

Azt is megteheti, Key Vault hogy közös hozzáférésű aláírási jogkivonatokat állítson elő. Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. A fiók kulcsainak megosztása nélkül megadhatja az ügyfeleknek a Storage-fiók erőforrásaihoz való hozzáférést. A közös hozzáférésű aláírás biztonságos módot biztosít a tárolási erőforrások megosztására a fiók kulcsainak veszélyeztetése nélkül.

Az ebben a szakaszban szereplő parancsok a következő műveleteket hajtják végre:

- Fiók közös hozzáférésű aláírás-definíciójának `<YourSASDefinitionName>`beállítása. A definíció egy Key Vault felügyelt Storage-fiókban `<YourStorageAccountName>` van beállítva a `<VaultName>`kulcstartóban.
- Hozzon létre egy fiók közös hozzáférési aláírási tokent a blob-, fájl-, tábla-és üzenetsor-szolgáltatásokhoz. A jogkivonat az erőforrástípusok szolgáltatás, a tároló és az objektum számára lett létrehozva. A jogkivonat minden engedélyekkel, HTTPS-kapcsolattal és a megadott kezdési és befejezési dátumokkal jön létre.
- Key Vault felügyelt tároló közös hozzáférésű aláírás-definíciójának beállítása a tárban. A definíció a megosztott hozzáférés-aláírási jogkivonat sablonjának URI-JÁT hozza létre. A definíció a közös hozzáférési aláírás típusát adja `account` meg, és N napig érvényes.
- Kérje le a tényleges hozzáférési tokent a közös hozzáférési aláírás definíciójának megfelelő Key Vault titkos kulcsból.

Az előző szakaszban ismertetett lépések elvégzése után futtassa a következő parancsokat, hogy megkérdezze Key Vaultt a közös hozzáférésű aláírási tokenek létrehozásához. 

1. Hozzon létre egy közös hozzáférési aláírás definícióját. A közös hozzáférési aláírás definíciójának létrehozása után kérje meg Key Vault, hogy hozzon létre több közös hozzáférésű aláírási jogkivonatot. Ehhez a művelethez `storage` a `setsas` és az engedélyek szükségesek.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    A művelettel kapcsolatos segítségért tekintse meg az az [Storage Account regenerált-sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) dokumentációját.

    A művelet sikeres futtatása után másolja ki a kimenetet.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Használja az `$sasToken` előző parancs által generált parancsot, és hozzon létre egy közös hozzáférési aláírás definícióját. További információ a parancs paramétereivel kapcsolatban: az az kulcstartó [Storage sas-definition Create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) Reference dokumentáció.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Ha a felhasználó nem rendelkezik engedéllyel a Storage-fiókhoz, először kérje le a felhasználó objektum-AZONOSÍTÓját:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Jogkivonatok beolvasása a kódban

Hajtson végre műveleteket a Storage-fiókban a [közös hozzáférésű aláírási jogkivonatok](../storage/common/storage-dotnet-shared-access-signature-part-1.md) beolvasásával Key Vaultból.

A Key Vault háromféleképpen lehet hitelesíteni:

- Felügyelt szolgáltatás identitásának használata. Ez a megközelítés kifejezetten ajánlott.
- Egyszerű szolgáltatásnév és tanúsítvány használata. 
- Egyszerű szolgáltatásnév és jelszó használata. Ez a megközelítés nem ajánlott.

További információkért lásd [: Azure Key Vault: Alapvető fogalmak](key-vault-whatis.md#basic-concepts).

Az alábbi példa bemutatja, hogyan lehet beolvasni a közös hozzáférésű aláírási jogkivonatokat. A tokeneket a közös hozzáférésű aláírás definíciójának létrehozása után kell beolvasni. 

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

Ha a megosztott hozzáférési aláírási token hamarosan lejár, a Key Vault és a kód frissítésével olvassa be újra a közös hozzáférés-aláírási tokent.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Az Azure parancssori felület parancsai

A felügyelt tárolási fiókokhoz kapcsolódó Azure CLI-parancsokkal kapcsolatos további információkért tekintse meg az az kulcstartó [Storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) -dokumentációját.

## <a name="next-steps"></a>További lépések

- További információ a [kulcsokról, a titkokról és a tanúsítványokról](https://docs.microsoft.com/rest/api/keyvault/).
- Tekintse át a [Azure Key Vault csapat blogján](https://blogs.technet.microsoft.com/kv/)található cikkeket.
