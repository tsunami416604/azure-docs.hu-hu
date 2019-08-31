---
title: Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI (előzetes verzió) használatával – Azure Storage
description: Megtudhatja, hogyan hozhat létre felhasználói delegálási SAS-t Azure Active Directory hitelesítő adatokkal az Azure Storage-ban az Azure CLI használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/29/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 6ea4dbf07c8ef99c43dbe7add1ae9270056f708c
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164327"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI-vel (előzetes verzió)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan használhatók a Azure Active Directory (Azure AD) hitelesítő adatai egy felhasználói delegálási SAS létrehozásához egy tárolóhoz vagy blobhoz az Azure CLI (előzetes verzió) használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Az Azure CLI legújabb verziójának telepítése

Ha az Azure CLI-t szeretné használni az SAS Azure AD-beli hitelesítő adatokkal való védelméhez, először győződjön meg arról, hogy telepítette az Azure CLI legújabb verzióját. Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sign-in-with-azure-ad-credentials"></a>Bejelentkezés Azure AD-beli hitelesítő adatokkal

Jelentkezzen be az Azure CLI-be az Azure AD-beli hitelesítő adataival. További információ: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Engedélyek kiosztása a RBAC

Ha Azure PowerShellból szeretne felhasználói delegálási SAS-t létrehozni, az Azure CLI-be való bejelentkezéshez használt Azure AD-fióknak olyan szerepkört kell hozzárendelni, amely tartalmazza a **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. Ez az engedély lehetővé teszi, hogy az Azure AD-fiók a *felhasználói delegálási kulcsot*kérje. A felhasználói delegálási kulcs a felhasználói delegálási SAS aláírására szolgál. A **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet biztosító szerepkört a Storage-fiók, az erőforráscsoport vagy az előfizetés szintjén kell hozzárendelni.

Ha nem rendelkezik megfelelő engedélyekkel ahhoz, hogy RBAC-szerepköröket rendeljen hozzá egy Azure AD-rendszerbiztonsági tag számára, előfordulhat, hogy a fiók tulajdonosának vagy a rendszergazdának kell megkérnie a szükséges engedélyek hozzárendelését.

A következő példa a **Storage blob** -adatközreműködői szerepkört rendeli hozzá, amely magában foglalja a **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A szerepkör hatóköre a Storage-fiók szintjén van.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

A **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet tartalmazó beépített szerepkörökkel kapcsolatos további információkért lásd: [beépített szerepkörök az Azure-erőforrásokhoz](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Az Azure AD hitelesítő adataival biztonságossá teheti az SAS-t

Amikor létrehoz egy felhasználói delegálási SAS-t az Azure CLI-vel, a rendszer implicit módon létrehozza az SAS aláírásához használt felhasználói delegálási kulcsot. A rendszer az SAS számára megadott kezdési időt és lejárati időt is használja a felhasználói delegálási kulcs kezdési és lejárati idejeként.

Mivel a felhasználói delegálási kulcs érvényességi időtartama a kezdő dátumtól számított 7 nap, a kezdési időponttól számított 7 napon belül meg kell adnia a lejárati időt. Az SAS érvénytelen a felhasználói delegálási kulcs lejárata után, így a 7 napnál hosszabb lejárati idővel rendelkező SAS-t a rendszer továbbra is csak 7 napig érvényes.

Felhasználói delegálási sas létrehozásakor a és `--auth-mode login` `--as-user parameters` a szükséges. Adja meg a `--auth-mode` paraméter *bejelentkezési adatait* , hogy az Azure Storage-ba irányuló kérések engedélyezve legyenek az Azure ad-beli hitelesítő adataival. Adja meg `--as-user` azt a paramétert, amely azt jelzi, hogy a visszaadott sas felhasználói delegálási sas-nek kell lennie.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Felhasználói delegálási SAS létrehozása tárolóhoz

Az Azure CLI-vel rendelkező tárolóhoz tartozó felhasználói delegálási SAS létrehozásához hívja meg az az [Storage Container Create-sas](/cli/azure/storage/container#az-storage-container-generate-sas) parancsot.

A tárolók felhasználói delegálási SAS-re vonatkozó támogatott engedélyei közé tartozik a Hozzáadás, a létrehozás, a törlés, a Listázás, az olvasás és az írás. Engedélyek adhatók meg egyszerre vagy kombinálva. További információ ezekről az engedélyekről: [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-user-delegation-sas).

A következő példa egy felhasználó delegálási SAS-jogkivonatát adja vissza egy tárolóhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

A felhasználói delegálási SAS-jogkivonat a következőhöz hasonló lesz:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Felhasználói delegálási SAS létrehozása blobhoz

Ha az Azure CLI-vel szeretne létrehozni egy felhasználói delegálási SAS-t egy blobhoz, hívja meg az az [Storage blob generált-sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) parancsot.

A felhasználók delegálására szolgáló SAS-re vonatkozó támogatott engedélyek a következők: Hozzáadás, létrehozás, törlés, olvasás és írás. Engedélyek adhatók meg egyszerre vagy kombinálva. További információ ezekről az engedélyekről: [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-user-delegation-sas).

A következő szintaxis a blobhoz tartozó felhasználói delegálási SAS-t adja vissza. A példa a `--full-uri` paramétert adja meg, amely visszaadja a blob URI azonosítót a hozzáfűzött sas-jogkivonattal. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

A felhasználói delegálás SAS URI-ja a következőhöz hasonló lesz:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> A felhasználói delegálási SAS nem támogatja az engedélyek megadását egy tárolt hozzáférési házirenddel.

## <a name="revoke-a-user-delegation-sas"></a>Felhasználói delegálási SAS visszavonása

Ha vissza szeretne vonni egy felhasználói delegálási SAS-t az Azure CLI-ből, hívja meg az az [Storage Account visszavonás-delegálás-Keys](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) parancsot. Ez a parancs visszavonja a megadott Storage-fiókhoz társított összes felhasználói delegálási kulcsot. A kulcsokhoz társított megosztott hozzáférési aláírások érvénytelenítve lettek.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> A felhasználói delegálási kulcs és a RBAC szerepkör-hozzárendeléseket az Azure Storage gyorsítótárazza, így a visszavonás folyamata és a meglévő felhasználói delegálási SAS érvénytelenné válik.

## <a name="next-steps"></a>További lépések

- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Felhasználói delegálási kulcs műveletének beolvasása](/rest/api/storageservices/get-user-delegation-key)
