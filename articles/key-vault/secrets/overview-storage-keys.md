---
title: Tárfiók-kulcsok kezelése az Azure Key Vault és az Azure CLI segítségével
description: A tárfiók-kulcsok zökkenőmentes integrációt biztosítanak az Azure Key Vault és az Azure storage-fiókkulcs-alapú hozzáférés között.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 1125bafa43ce1752c58d1cce0bba66a6bbd32c32
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685422"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Tárfiók-kulcsok kezelése a Key Vault és az Azure CLI segítségével

Az Azure storage-fiók egy fióknévből és egy kulcsból álló hitelesítő adatokat használ. A kulcs automatikusan generálódik, és jelszóként szolgál, nem pedig kriptográfiai kulcsként. A Key Vault úgy kezeli a tárfiók-kulcsokat, hogy key vault-titokként tárolja őket. 

A Key Vault felügyelt tárfiók kulcsfunkció használatával felsorolhatja (szinkronizálhatja) a kulcsokat egy Azure storage-fiókkal, és rendszeresidőközönként újragenerálhatja (elforgathatja) a kulcsokat. A storage-fiókok és a klasszikus tárfiókok kulcsait is kezelheti.

A felügyelt tárfiók kulcsszolgáltatás használatakor vegye figyelembe a következő pontokat:

- A kulcsértékek et soha nem adja vissza a hívónak adott válaszként.
- Csak a Key Vault kezelheti a tárfiók kulcsait. Ne kezelje saját maga a kulcsokat, és ne zavarja meg a Key Vault-folyamatokat.
- Csak egy Key Vault-objektum nak kell kezelnie a tárfiók kulcsait. Ne engedélyezze a kulcskezelést több objektumból.
- Kérheti a Key Vault a tárfiók kezelése egy egyszerű felhasználó, de nem egy egyszerű szolgáltatás.
- A kulcsok újragenerálása csak a Key Vault használatával. Ne hozza létre manuálisan a tárfiók kulcsait.

Az Azure Active Directory (Azure AD), a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatásának használatát javasoljuk. Az Azure AD-integráció elérhető az [Azure blobok és várólisták,](../../storage/common/storage-auth-aad.md)és oAuth2 token alapú hozzáférést biztosít az Azure Storage (csakúgy, mint az Azure Key Vault).

Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését egy alkalmazás vagy felhasználói identitás használatával, a tárfiók hitelesítő adatai helyett. Az Azure [AD felügyelt identitást azure-beli](/azure/active-directory/managed-identities-azure-resources/) futtatáskor használhatja. A felügyelt identitások szükségtelent hoznak az ügyfélhitelesítésre és a hitelesítő adatok nak az alkalmazásban vagy az alkalmazással való tárolására.

Az Azure AD szerepköralapú hozzáférés-vezérlés (RBAC) használatával kezeli az engedélyezést, amelyet a Key Vault is támogat.

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

- [Telepítse az Azure CLI](/cli/azure/install-azure-cli).
- [Kulcstartó létrehozása](quick-create-cli.md)
- [Hozzon létre egy Azure-tárfiókot.](../../storage/common/storage-account-create.md?tabs=azure-cli) A tárfiók neve csak kisbetűket és számokat használhat. A név hosszának 3 és 24 karakter között kell lennie.
      
## <a name="manage-storage-account-keys"></a>Tárfiók-kulcsok kezelése

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse az Azure CLI-munkamenetet az [az bejelentkezési](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsokkal.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Hozzáférés a Key Vault-hoz a tárfiókhoz

Használja az Azure CLI [szerepkör-hozzárendelés create](/cli/azure/role/assignment?view=azure-cli-latest) parancsot, hogy a Key Vault hozzáférést a tárfiókhoz. Adja meg a parancsnak a következő paraméterértékeket:

- `--role`: Adja át a "Tárfiók kulcsoperátor-szolgáltatási szerepkör" RBAC szerepkör. Ez a szerepkör korlátozza a hozzáférési hatókört a tárfiókra. Klasszikus tárfiók esetén adja át a "Klasszikus tárfiók kulcsoperátori szolgáltatás szerepkör" helyett.
- `--assignee`: Adja áthttps://vault.azure.neta " " értéket, amely az Azure nyilvános felhőben a Key Vault URL-címe. (Az Azure Goverment felhőhasználata "--asingee-object-id" helyett, lásd: [Egyszerű szolgáltatás alkalmazásazonosítója.)](#service-principal-application-id)
- `--scope`: Adja át a tárfiók erőforrás-azonosítóját, amely a képernyőn található. `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` Az előfizetés-azonosító megkereséséhez használja az Azure CLI [az fióklista](/cli/azure/account?view=azure-cli-latest#az-account-list) parancsot; a tárfiók nevének és a tárfiók erőforráscsoportjának megkereséséhez használja az Azure CLI [az storage-fiók lista parancsát.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>A felhasználói fiók engedélyének engedélyezése a kezelt tárfiókokhoz

Az Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancsmaghasználatával frissítse a Key Vault hozzáférési szabályzatot, és adjon tárfiók engedélyeket a felhasználói fiókhoz.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Vegye figyelembe, hogy a tárfiókok engedélyei nem érhetők el az Azure Portal "Access szabályzatok" lapján.
### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault felügyelt tárfiók létrehozása

 Hozzon létre egy Key Vault által felügyelt tárfiókot az Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) paranccsal. Állítsa be a regenerációs időszak 90 nap. 90 nap elteltével a `key1` Key Vault újragenerálja `key2` `key1`és felcseréli az aktív kulcsot a-ból. `key1`ezután aktív kulcsként lesz megjelölve. Adja meg a parancsnak a következő paraméterértékeket:

- `--vault-name`: Adja meg a kulcstartó nevét. A kulcstartó nevének megkereséséhez használja az Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) parancsot.
- `-n`: Adja meg a tárfiók nevét. A tárfiók nevének megkereséséhez használja az Azure CLI [az storage-fiók lista parancs.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
- `--resource-id`: Adja át a tárfiók erőforrás-azonosítóját, amely a képernyőn található. `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` Az előfizetés-azonosító megkereséséhez használja az Azure CLI [az fióklista](/cli/azure/account?view=azure-cli-latest#az-account-list) parancsot; a tárfiók nevének és a tárfiók erőforráscsoportjának megkereséséhez használja az Azure CLI [az storage-fiók lista parancsát.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Megosztott hozzáférésű aláírás-jogkivonatok

Azt is kérheti a Key Vault közös hozzáférésű aláírás jogkivonatok létrehozásához. A megosztott hozzáférésű aláírás delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz. A fiókkulcsok megosztása nélkül is hozzáférést biztosíthat az ügyfeleknek a tárfiókban lévő erőforrásokhoz. A megosztott hozzáférésű aláírás biztonságos módot biztosít a tárolási erőforrások megosztására a fiókkulcsok veszélyeztetése nélkül.

Az ebben a szakaszban található parancsok a következő műveleteket hajtják végre:

- Fiók megosztott hozzáférés-aláírás-definíciójának `<YourSASDefinitionName>`beállítása. A definíció a key vaultban `<YourStorageAccountName>` `<YourKeyVaultName>`lévő Key Vault által kezelt tárfiókon van beállítva.
- Hozzon létre egy megosztott hozzáférésű aláírási jogkivonatot a Blob, a File, a Table és a Queue szolgáltatásokhoz. A jogkivonat szolgáltatás, tároló és objektum erőforrástípusokhoz jön létre. A jogkivonat jön létre az összes engedély, https-en keresztül, és a megadott kezdési és befejezési dátumokkal.
- Állítsa be a Key Vault felügyelt tároló megosztott hozzáférés-aláírás-definícióját a tárolóban. A definíció rendelkezik a létrehozott megosztott hozzáférés-aláírási jogkivonat sablon URI-jával. A definíció megosztott hozzáférésű `account` aláírástípussal rendelkezik, és N napokig érvényes.
- Ellenőrizze, hogy a megosztott hozzáférésű aláírás titkos kulcsként lett-e mentve a kulcstartóban.

### <a name="create-a-shared-access-signature-token"></a>Megosztott hozzáférésű aláírási jogkivonat létrehozása

Hozzon létre egy közös hozzáférésű aláírás-definíciót az Azure CLI [az storage-fiók generate-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) paranccsal. Ehhez a `storage` művelethez szükség van a és `setsas` engedélyeket.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
A művelet sikeres futtatása után másolja a kimenetet.

```console
"se=2020-01-01&sp=***"
```

Ez a kimenet lesz `--template-id` a paraméter a következő lépésben átadott.

### <a name="generate-a-shared-access-signature-definition"></a>Megosztott hozzáférés-aláírás-definíció létrehozása

Használja az Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) parancsot, `--template-id` átadva a kimenetet az előző lépésből a paraméter, hozzon létre egy közös hozzáférésű aláírás-definíció.  Megadhatja a választott nevet `-n` a paraméternek.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>A megosztott hozzáférés-aláírás-definíció ellenőrzése

Ellenőrizheti, hogy a megosztott hozzáférésű aláírás-definíció a key vault ban tárolt az Azure CLI [az keyvault titkos lista](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) és az [keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) parancsok használatával.

Először keresse meg a megosztott hozzáférésű aláírás definícióját a key vault az [keyvault titkoslista](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) paranccsal.

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

A SAS-definíciónak megfelelő titkos kód a következő tulajdonságokkal rendelkezik:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Most már használhatja az [az keyvault titkos show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) parancsot, és a `id` tulajdonság a titkos kulcs tartalmának megtekintéséhez.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

A parancs kimenete a SAS-definíciós karakterláncot`value`jeleníti meg .


## <a name="next-steps"></a>További lépések

- További információ a [kulcsokról, a titkos kulcsokról és a tanúsítványokról.](https://docs.microsoft.com/rest/api/keyvault/)
- Tekintse át az [Azure Key Vault csapatblogjában](https://blogs.technet.microsoft.com/kv/)szereplő cikkeket.
- Tekintse meg az [az keyvault tárolási](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) referencia dokumentációját.
