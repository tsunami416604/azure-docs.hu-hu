---
title: Az Azure CLI használatával felhasználói delegálási SAS-t hozhat létre egy tárolóhoz vagy blobhoz
titleSuffix: Azure Storage
description: Ismerje meg, hogyan hozhat létre egy felhasználói delegáláss SAS-t az Azure Active Directory hitelesítő adataival az Azure CLI használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371989"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Felhasználói delegálássas SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI-vel

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan azure Active Directory (Azure AD) hitelesítő adatok segítségével hozzon létre egy felhasználói delegáláss SAS egy tároló vagy blob az Azure CLI használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Telepítse az Azure CLI legújabb verzióját

Az Azure CLI használatával az Azure AD-hitelesítő adatokkal rendelkező SAS-t szeretné biztosítani, először győződjön meg arról, hogy telepítette az Azure CLI legújabb verzióját. Az Azure CLI telepítéséről az [Azure CLI telepítése című](/cli/azure/install-azure-cli)témakörben talál további információt.

Ha az Azure CLI használatával felhasználói delegálási SAS-t szeretne létrehozni, győződjön meg arról, hogy telepítette a 2.0.78-as vagy újabb verzióját. A telepített verzió ellenőrzéséhez `az --version` használja a parancsot.

## <a name="sign-in-with-azure-ad-credentials"></a>Bejelentkezés Azure AD-hitelesítő adatokkal

Jelentkezzen be az Azure CLI-be az Azure AD-hitelesítő adataival. További információkért lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Engedélyek hozzárendelése Az RBAC-kal

Ha létre szeretne hozni egy felhasználói delegálássSAS-t az Azure PowerShellből, az Azure CLI-be való bejelentkezéshez használt Azure AD-fiókhoz hozzá kell rendelni egy szerepkört, amely tartalmazza a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. Ez az engedély lehetővé teszi, hogy az Azure AD-fiók kérje a *felhasználó delegálási kulcsát.* A felhasználói delegálási kulcs a felhasználói delegálássas sas aláírására szolgál. A **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet biztosító szerepkört a tárfiók, az erőforráscsoport vagy az előfizetés szintjén kell hozzárendelni.

Ha nem rendelkezik megfelelő engedélyekkel az RBAC-szerepkörök hozzárendeléséhez egy Azure AD rendszerbiztonsági taghoz, előfordulhat, hogy meg kell kérnie a fiók tulajdonosát vagy a rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket.

A következő példa a **Storage Blob Data Contributor** szerepkört rendeli hozzá, amely tartalmazza a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A szerepkör hatóköre a tárfiók szintjén.

Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

A **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet tartalmazó beépített szerepkörökről további információt az [Azure-erőforrások beépített szerepkörei](../../role-based-access-control/built-in-roles.md)című témakörben talál.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>A SAS biztonságossá tétele Azure AD-hitelesítő adatokkal

Amikor létrehoz egy felhasználói delegálássas SAS-t az Azure CLI-vel, implicit módon létrejön a SAS aláírásához használt felhasználói delegálási kulcs. A SAS-hez megadott kezdési és lejárati idő a felhasználó delegálási kulcsának kezdési és lejárati idejeként is használatos.

Mivel a felhasználói delegálási kulcs érvényességi ideje a kezdő dátumtól számított 7 nap, meg kell adnia a SAS lejárati idejét, amely a kezdési időponttól számított 7 napon belül van. A SAS érvénytelen a felhasználói delegálási kulcs lejárta után, így a 7 napnál hosszabb lejárati idővel rendelkező SAS továbbra is csak 7 napig lesz érvényes.

Felhasználói delegáláslétrehozásakor a `--auth-mode login` `--as-user parameters` és a és szükséges. Adja meg a `--auth-mode` *bejelentkezési* paramétert, hogy az Azure Storage-ba irányuló kérelmek az Azure AD hitelesítő adataival engedélyezettek. Adja `--as-user` meg azt a paramétert, amely azt jelzi, hogy a visszaadott SAS felhasználói delegálási SAS legyen.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Felhasználói delegálássas létrehozása tárolóhoz

Ha hozzon létre egy felhasználói delegáláss SAS egy tárolót az Azure CLI, hívja meg az [az storage container generate-sas](/cli/azure/storage/container#az-storage-container-generate-sas) parancsot.

A tárolón lévő felhasználói delegálási SAS támogatott engedélyei közé tartozik a Hozzáadás, létrehozás, törlés, lista, olvasás és írás. Az engedélyek külön-külön vagy kombinálva is megadhatók. Ezekről az engedélyekről további információt a [Felhasználói delegálási SAS létrehozása című](/rest/api/storageservices/create-user-delegation-sas)témakörben talál.

A következő példa egy felhasználói delegálási SAS-jogkivonatot ad vissza egy tárolóhoz. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

A visszaadott felhasználói delegálási SAS-token a következőhöz hasonló lesz:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Felhasználói delegálássAS létrehozása blobhoz

Ha hozzon létre egy felhasználói delegáláss SAS egy blob az Azure CLI, hívja meg az [az storage blob generate-sas](/cli/azure/storage/blob#az-storage-blob-generate-sas) parancsot.

A blobon lévő felhasználói delegálási SAS támogatott engedélyei közé tartozik a Hozzáadás, létrehozás, törlés, olvasás és írás. Az engedélyek külön-külön vagy kombinálva is megadhatók. Ezekről az engedélyekről további információt a [Felhasználói delegálási SAS létrehozása című](/rest/api/storageservices/create-user-delegation-sas)témakörben talál.

A következő szintaxis egy blob felhasználói delegálási SAS-t ad vissza. A példa adja `--full-uri` meg a paramétert, amely visszaadja a blob URI a SAS-jogkivonat hozzáfűzve. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni:

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

A visszaadott felhasználói delegálási SAS URI a következőhöz hasonló lesz:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> A felhasználói delegálási SAS nem támogatja az engedélyek definiálását tárolt hozzáférési házirenddel.

## <a name="revoke-a-user-delegation-sas"></a>Felhasználói delegálási SAS visszavonása

Ha vissza szeretne vonni egy felhasználói delegálási SAS-t az Azure CLI-ből, hívja meg az [az storage-fiók visszavonási-delegálási kulcsok](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) parancsát. Ez a parancs visszavonja a megadott tárfiókhoz társított összes felhasználói delegálási kulcsot. A kulcsokhoz társított megosztott hozzáférési aláírások érvénytelenné válnak.

Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Mind a felhasználói delegálási kulcs, mind az RBAC szerepkör-hozzárendelések gyorsítótárazása az Azure Storage gyorsítótárazza, így előfordulhat, hogy a visszavonási folyamat kezdeményezése és egy meglévő felhasználói delegálási SAS érvénytelenné válik között.

## <a name="next-steps"></a>További lépések

- [Felhasználói delegálássas (REST API) létrehozása](/rest/api/storageservices/create-user-delegation-sas)
- [Felhasználói delegálási kulcs művelet beszereznie](/rest/api/storageservices/get-user-delegation-key)
