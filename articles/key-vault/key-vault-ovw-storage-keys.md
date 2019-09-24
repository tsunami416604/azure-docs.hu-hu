---
title: A Storage-fiók kulcsainak kezelése a Azure Key Vault és az Azure CLI használatával
description: A Storage-fiókok kulcsai zökkenőmentes integrációt biztosítanak a Azure Key Vault és a kulcs-alapú Azure Storage-fiókhoz való hozzáférés között.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 8b9478dda83b85e937faa8915fa5e9b77660f194
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203619"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>A Storage-fiók kulcsainak kezelése a Key Vault és az Azure CLI használatával

Az Azure Storage-fiók a fiók nevét és kulcsát tartalmazó hitelesítő adatokat használ. A kulcs automatikusan létrejön, és jelszóként szolgál, nem pedig titkosítási kulcsként. A Key Vault a Storage-fiókok kulcsait úgy kezeli, hogy [Key Vault titokként](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)tárolja őket. 

A Key Vault felügyelt Storage-fiók kulcsa funkció használatával listázhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeresen újragenerálhatja (elforgathatja) a kulcsokat. A kulcsokat a Storage-fiókok és a klasszikus Storage-fiókok esetében is kezelheti.

A felügyelt Storage-fiók kulcsa funkció használata esetén vegye figyelembe a következő szempontokat:

- A rendszer soha nem adja vissza a kulcs értékeit a hívónak adott válaszként.
- Csak Key Vault kell kezelnie a Storage-fiók kulcsait. Ne kezelje a kulcsokat, és ne zavarja a Key Vault folyamatokat.
- Csak egyetlen Key Vault objektumnak kell kezelnie a Storage-fiók kulcsait. Ne engedélyezze a kulcskezelő szolgáltatás több objektumból való felügyeletét.
- Key Vault kérheti, hogy kezelje a Storage-fiókját egy egyszerű felhasználóval, de nem egy egyszerű szolgáltatásnév használatával.
- Kulcsok újragenerálása csak Key Vault használatával. Ne végezze el manuálisan a Storage-fiók kulcsainak újragenerálása.

Javasoljuk, hogy az Azure Storage-integrációt Azure Active Directory (Azure AD), a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatásával használja. Az Azure AD-integráció az [Azure-blobok és-várólisták](../storage/common/storage-auth-aad.md)számára érhető el, és OAuth2 token-alapú hozzáférést biztosít az Azure Storage-hoz (akárcsak Azure Key Vault).

Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését alkalmazás vagy felhasználói identitás használatával a Storage-fiók hitelesítő adatai helyett. Azure AD-beli [felügyelt identitást](/azure/active-directory/managed-identities-azure-resources/) használhat az Azure-ban való futtatáskor. A felügyelt identitások nem szükségesek az ügyfél-hitelesítéshez és a hitelesítő adatok tárolásához a vagy az alkalmazásban.

Az Azure AD szerepköralapú hozzáférés-vezérlést (RBAC) használ az engedélyezés kezelésére, amelyet a Key Vault is támogat.

## <a name="service-principal-application-id"></a>Egyszerű szolgáltatásnév alkalmazásának azonosítója

Az Azure AD-bérlő minden regisztrált alkalmazást biztosít egy [egyszerű szolgáltatással](/azure/active-directory/develop/developer-glossary#service-principal-object). Az egyszerű szolgáltatásnév a RBAC-on keresztül más Azure-erőforrásokhoz való hozzáférés engedélyezési beállítása során használt alkalmazás-AZONOSÍTÓként szolgál.

A Key Vault egy olyan Microsoft-alkalmazás, amely az összes Azure AD-bérlőben előre regisztrálva van. A Key Vault minden Azure-felhőben ugyanazzal az alkalmazás-AZONOSÍTÓval van regisztrálva.

| bérlők | Felhő | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure – nyilvános | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Előfeltételek

Az útmutató elvégzéséhez először a következőket kell tennie:

- [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.
- [Kulcstartó létrehozása](quick-create-cli.md)
- [Hozzon létre egy Azure-tárfiókot](../storage/common/storage-quickstart-create-account.md?tabs=azure-cli). A Storage-fiók nevének csak kisbetűket és számokat kell használnia. A név hosszának 3 és 24 karakter közöttinek kell lennie.
      
## <a name="manage-storage-account-keys"></a>A Storage-fiók kulcsainak kezelése

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse Azure CLI-munkamenetét az az [login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsok használatával.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault hozzáférés biztosítása a Storage-fiókhoz

A Storage-fiók eléréséhez használja az Azure CLI az [role hozzárendelés Create](/cli/azure/role/assignment?view=azure-cli-latest) paranccsal Key Vault. Adja meg a parancsot a következő paraméter-értékekkel:

- `--role`: Adja át a "Storage-fiók kulcsát kezelő szolgáltatás szerepkör" RBAC szerepkört. Ez a szerepkör korlátozza a hozzáférési hatókört a Storage-fiókra. Klasszikus Storage-fiók esetén a "klasszikus Storage-fiók kulcs-kezelője" szerepkört adja át helyette.
- `--assignee-object-id`: Adja át a "93c27d83-f79b-4cb2-8dd4-4aa716542e74" értéket, amely az Azure-beli nyilvános felhőben Key Vault objektum azonosítója. (A Azure Government-felhőben Key Vaulthoz tartozó objektumazonosító beszerzéséhez tekintse meg a [szolgáltatásnév alkalmazás-azonosítóját](#service-principal-application-id).)
- `--scope`: Adja át a Storage-fiók erőforrás-AZONOSÍTÓját, amely `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`az űrlapon található. Az előfizetés AZONOSÍTÓjának megkereséséhez használja az Azure CLI az [Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) parancsot; a Storage-fiók neve és a Storage-fiók erőforráscsoport megkereséséhez használja az Azure CLI az [Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) parancsot.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault felügyelt Storage-fiók létrehozása

 Hozzon létre egy Key Vault felügyelt Storage-fiókot az Azure CLI az kulcstartó [Storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) paranccsal. Állítsa be a 90 napos újragenerálási időszakot. 90 nap után Key Vault újragenerálta `key1` és felcseréli az aktív `key2` kulcsot `key1`a verzióról a verzióra. `key1`Ekkor az aktív kulcsként van megjelölve. Adja meg a parancsot a következő paraméter-értékekkel:

- `--vault-name`: Adja át a kulcstartó nevét. A Key Vault nevének megkereséséhez használja az Azure CLI az kulcstartó [List](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) parancsot.
- `-n`: Adja meg a Storage-fiók nevét. A Storage-fiók nevének megkereséséhez használja az Azure CLI az [Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) parancsot.
- `--resource-id`: Adja át a Storage-fiók erőforrás-AZONOSÍTÓját, amely `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`az űrlapon található. Az előfizetés AZONOSÍTÓjának megkereséséhez használja az Azure CLI az [Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) parancs; a Storage-fiók neve és a Storage-fiók erőforráscsoport megkereséséhez használja az Azure CLI az [Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) parancsot.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Közös hozzáférésű aláírási jogkivonatok

Azt is megteheti, Key Vault hogy közös hozzáférésű aláírási jogkivonatokat állítson elő. Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít. A fiók kulcsainak megosztása nélkül megadhatja az ügyfeleknek a Storage-fiók erőforrásaihoz való hozzáférést. A közös hozzáférésű aláírás biztonságos módot biztosít a tárolási erőforrások megosztására a fiók kulcsainak veszélyeztetése nélkül.

Az ebben a szakaszban szereplő parancsok a következő műveleteket hajtják végre:

- Fiók közös hozzáférésű aláírás-definíciójának `<YourSASDefinitionName>`beállítása. A definíció egy Key Vault felügyelt Storage-fiókban `<YourStorageAccountName>` van beállítva a `<YourKeyVaultName>`kulcstartóban.
- Hozzon létre egy fiók közös hozzáférési aláírási tokent a blob-, fájl-, tábla-és üzenetsor-szolgáltatásokhoz. A jogkivonat az erőforrástípusok szolgáltatás, a tároló és az objektum számára lett létrehozva. A jogkivonat minden engedélyekkel, HTTPS-kapcsolattal és a megadott kezdési és befejezési dátumokkal jön létre.
- Key Vault felügyelt tároló közös hozzáférésű aláírás-definíciójának beállítása a tárban. A definíció a megosztott hozzáférés-aláírási jogkivonat sablonjának URI-JÁT hozza létre. A definíció a közös hozzáférési aláírás típusát adja `account` meg, és N napig érvényes.
- Ellenőrizze, hogy a közös hozzáférésű aláírás mentve lett-e a Key vaultban titkos kulcsként.

### <a name="create-a-shared-access-signature-token"></a>Közös hozzáférésű aláírási jogkivonat létrehozása

Hozzon létre egy közös hozzáférésű aláírás-definíciót az Azure CLI az [Storage Account regenerált-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) parancs használatával. Ehhez a művelethez `storage` a `setsas` és az engedélyek szükségesek.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
A művelet sikeres futtatása után másolja ki a kimenetet.

```console
"se=2020-01-01&sp=***"
```

Ez a kimenet a következő lépésben a `--template-id` paraméternek lesz átadva.

### <a name="generate-a-shared-access-signature-definition"></a>Közös hozzáférésű aláírás definíciójának létrehozása

A közös hozzáférésű aláírás definíciójának létrehozásához használja az Azure CLI az Key [Vault Storage sas-definition Create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) parancsot, `--template-id` és adja át az előző lépés eredményét a paraméternek.  Megadhatja az Ön által választott nevet a `-n` paraméternek.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>A közös hozzáférésű aláírás definíciójának ellenőrzése

Ellenőrizheti, hogy a közös hozzáférésű aláírás definícióját a kulcstartó tárolja-e a Key vaultban az Azure CLI az kulcstartó [Secret List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) és [az az kulcstartó Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal.

Először keresse meg a közös hozzáférésű aláírás definícióját a kulcstartóban az az Key [Vault Secret List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) paranccsal.

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Az SAS-definíciónak megfelelő titok a következő tulajdonságokkal rendelkezik:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Mostantól az az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) paranccsal és a `id` tulajdonsággal is megtekintheti a titkos tartalmat.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

A parancs kimenete az SAS-definíciós karakterláncot`value`fogja megjeleníteni.


## <a name="next-steps"></a>További lépések

- További információ a [kulcsokról, a titkokról és a tanúsítványokról](https://docs.microsoft.com/rest/api/keyvault/).
- Tekintse át a [Azure Key Vault csapat blogján](https://blogs.technet.microsoft.com/kv/)található cikkeket.
- Lásd az az kulcstartó [Storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) Reference dokumentációját.
