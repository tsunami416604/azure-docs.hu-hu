---
title: Ügyfél által felügyelt kulcsok használata az Azure Key Vault segítségével a fióktitkosítás kezeléséhez
titleSuffix: Azure Storage
description: Használhatja a saját titkosítási kulcs a tárfiókban lévő adatok védelmére. Amikor megad egy ügyfél által felügyelt kulcsot, a rendszer azt a kulcsot használja az adatokat titkosító kulcshoz való hozzáférés védelmére és szabályozására. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlés kezeléséhez.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6b5712094b9821dfa041cd5ba8617e86f7231bde
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478019"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Az Azure Storage titkosításának kezeléséhez használja az ügyfelek által felügyelt kulcsokat az Azure Key Vault segítségével

Használhatja a saját titkosítási kulcs a tárfiókban lévő adatok védelmére. Amikor megad egy ügyfél által felügyelt kulcsot, a rendszer azt a kulcsot használja az adatokat titkosító kulcshoz való hozzáférés védelmére és szabályozására. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlés kezeléséhez.

Az ügyfél által felügyelt kulcsok tárolásához az Azure Key Vault használatával kell tárolnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához. A tárfiók nak és a kulcstárolónak ugyanabban a régióban és ugyanabban az Azure Active Directory (Azure AD) bérlőben kell lennie, de lehetnek különböző előfizetésekben. Az Azure Key Vaultról a [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)

## <a name="about-customer-managed-keys"></a>Ügyfél által kezelt kulcsok –

Az alábbi ábra bemutatja, hogy az Azure Storage hogyan használja az Azure Active Directoryt és az Azure Key Vaultot az ügyfél által felügyelt kulcs használatával történő kérésekhez:

![Az ügyfél által kezelt kulcsok működését bemutató diagram az Azure Storage-ban](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

Az alábbi lista az ábrán szereplő számozott lépéseket ismerteti:

1. Az Azure Key Vault-rendszergazda engedélyeket ad a titkosítási kulcsok a tárfiókhoz társított felügyelt identitáshoz.
2. Az Azure Storage-rendszergazda konfigurálja a titkosítást egy ügyfél által felügyelt kulccsal a tárfiókhoz.
3. Az Azure Storage a tárfiókhoz társított felügyelt identitást használja az Azure Key Vaulthoz való hozzáférés hitelesítéséhez az Azure Active Directoryn keresztül.
4. Az Azure Storage becsomagolja a fiók titkosítási kulcsát az Ügyfélkulcssal az Azure Key Vaultban.
5. Az olvasási/írási műveletekhez az Azure Storage kéréseket küld az Azure Key Vaultnak a fiók titkosítási kulcsának kicsomagolásához a titkosítási és visszafejtési műveletek végrehajtásához.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Olyan fiók létrehozása, amely támogatja a várólisták és táblák ügyféláltal kezelt kulcsait

A várólista- és táblaszolgáltatásokban tárolt adatokat nem védi automatikusan az ügyfél által felügyelt kulcs, ha a tárfiókhoz engedélyezve vannak az ügyfél által kezelt kulcsok. Ezeket a szolgáltatásokat beállíthatja a tárfiók létrehozásának időpontjában, hogy szerepeljen ebben a védelemben.

Ha többet szeretne tudni arról, hogy miként hozhat létre olyan tárfiókot, amely támogatja az ügyfél által felügyelt kulcsokat a várólistákhoz és táblákhoz, olvassa el a [Táblák és várólisták ügyféláltal kezelt kulcsait támogató fiók létrehozása című témakört.](account-encryption-key-create.md)

A Blob és a File szolgáltatásokban lévő adatokat mindig az ügyfél által felügyelt kulcsok védik, ha az ügyfél által felügyelt kulcsok a tárfiókhoz vannak konfigurálva.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Tárfiók ügyfél által kezelt kulcsainak engedélyezése

Az ügyfél által kezelt kulcsok csak meglévő tárfiókokon engedélyezhetők. A key vault kell kiépíteni a hozzáférési szabályzatok, amelyek kulcsengedélyeket a storage-fiókhoz társított felügyelt identitás. A felügyelt identitás csak a tárfiók létrehozása után érhető el.

Az ügyfél által felügyelt kulcs konfigurálásakor az Azure Storage becsomagolja a fiók gyökéradat-titkosítási kulcsát a társított kulcstartóban lévő ügyfél által felügyelt kulccsal. Az ügyfél által felügyelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és azonnal érvénybe lép.

Ha módosítja az Azure Storage-titkosításhoz használt kulcsot az ügyfél által felügyelt kulcsok engedélyezésével vagy letiltásával, a kulcsverzió frissítésével vagy egy másik kulcs megadásával, akkor a gyökérkulcs titkosítása megváltozik, de az Azure Storage-fiókban lévő adatokat nem kell újra titkosítani.

Ha engedélyezi vagy letiltja az ügyfél által kezelt kulcsokat, vagy ha módosítja a kulcsot vagy a kulcsverziót, a gyökérszintű titkosítási kulcs védelme megváltozik, de az Azure Storage-fiókban lévő adatokat nem kell újra titkosítani.

Az ügyfél által felügyelt kulcsok azure Key Vault for Azure Storage-titkosítással való használatáról az alábbi cikkekben olvashat:

- [Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage-titkosításkey Vault szolgáltatásával az Azure Portalról](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok konfigurálása a Key Vault for Azure Storage titkosítással a PowerShellből](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok konfigurálása az Azure STORAGE-titkosításkey Vault for Azure Storage titkosításával az Azure CLI-ből](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásaira támaszkodnak, amely az Azure AD egyik szolgáltatása. A felügyelt identitások jelenleg nem támogatják a címtárközi forgatókönyveket. Amikor az Azure Portalon ügyfél által felügyelt kulcsokat konfigurál, a rendszer automatikusan hozzárendeli a felügyelt identitást a tárfiókhoz a borítók alatt. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a tárfiókot az egyik Azure AD-címtárból egy másikba, a tárfiókhoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok már nem működnek. További információ: **Előfizetés átvitele az Azure AD-könyvtárak között** [gyakori kérdésekben és az Azure-erőforrások felügyelt identitásaival kapcsolatos ismert problémák.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása az Azure Key Vaultban

Ahhoz, hogy az ügyfél által felügyelt kulcsok at egy tárfiókban, a kulcsok tárolásához egy Azure Key Vault használatával kell használnia. Engedélyeznie kell mind a **soft delete,** mind a **Ne ürítse** ki a kulcstartót.

Csak 2048 bites RSA és RSA-HSM kulcsok at az Azure Storage titkosítása támogatja. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Kulcstároló-kulcsok** című témakörben talál.

## <a name="rotate-customer-managed-keys"></a>Ügyfél által kezelt kulcsok elforgatása

Az azure-key vaultban az ügyfél által felügyelt kulcsot a megfelelőségi szabályzatok szerint forgathatja. A kulcs elforgatása után frissítenie kell a tárfiókot az új kulcsverzió URI használatához. Ha meg szeretné tudni, hogyan frissítheti a tárfiókot a kulcs új verziójának használatára az Azure Portalon, tekintse meg **a Kulcsverzió frissítése** című szakaszt az [Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage-hoz az Azure Portalhasználatával című szakaszban.](storage-encryption-keys-portal.md)

A kulcs elforgatása nem indítja el az adatok újratitkosítását a tárfiókban. A felhasználótól nincs szükség további műveletre.

## <a name="revoke-access-to-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása

A tárfiók bármikor visszavonhatja az ügyfél által felügyelt kulcshoz való hozzáférését. Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása, illetve a kulcs letiltása vagy törlése után az ügyfelek nem hívhatják meg a blobból vagy annak metaadataiból beolvasott vagy azt író műveleteket. Az alábbi műveletek bármelyikének hívására tett kísérletek a 403-as (Tiltott) hibakóddal minden felhasználó esetében sikertelenek lesznek:

- [Blobok listája](/rest/api/storageservices/list-blobs), amikor `include=metadata` a kérelem URI-jának paraméterével hívják meg
- [Blob beszerezése](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Blob metaadatok bekésezése](/rest/api/storageservices/get-blob-metadata)
- [Blob metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [Pillanatkép blob](/rest/api/storageservices/snapshot-blob), `x-ms-meta-name` amikor a kérelem fejlécével hívták
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Blob másolása URL-címből](/rest/api/storageservices/copy-blob-from-url)
- [Blobszint beállítása](/rest/api/storageservices/set-blob-tier)
- [Blokk berakása](/rest/api/storageservices/put-block)
- [Blokk felkerülése URL-címből](/rest/api/storageservices/put-block-from-url)
- [Hozzáfűző blokk](/rest/api/storageservices/append-block)
- [Blokk hozzáfűzése URL-címből](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Oldal berakása](/rest/api/storageservices/put-page)
- [Oldal felkerülése URL-címből](/rest/api/storageservices/put-page-from-url)
- [Növekményes másolási blob](/rest/api/storageservices/incremental-copy-blob)

Ha újra meg szeretné hívni ezeket a műveleteket, állítsa vissza a hozzáférést az ügyfél által felügyelt kulcshoz.

Az ebben a szakaszban nem szereplő összes adatművelet az ügyfél által kezelt kulcsok visszavonása, illetve a kulcs letiltása vagy törlése után folytatódhat.

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) t vagy [az Azure CLI-t.](storage-encryption-keys-cli.md#revoke-customer-managed-keys)

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Ügyfél által kezelt kulcsok az Azure által kezelt lemezekhez

Az ügyfelek által felügyelt kulcsok az Azure által felügyelt lemezek titkosításának kezeléséhez is rendelkezésre állnak. Az ügyfél által kezelt kulcsok eltérően viselkednek a felügyelt lemezeken, mint az Azure Storage-erőforrások. További információ: [Az Azure felügyelt lemezeinek kiszolgálóoldali titkosítása](../../virtual-machines/windows/disk-encryption.md) Windows vagy Az Azure felügyelt lemezek Linuxhoz [kiszolgálóoldali titkosítása.](../../virtual-machines/linux/disk-encryption.md)

## <a name="next-steps"></a>További lépések

- [Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage-titkosításkey Vault szolgáltatásával az Azure Portalról](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok konfigurálása a Key Vault for Azure Storage titkosítással a PowerShellből](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok konfigurálása az Azure STORAGE-titkosításkey Vault for Azure Storage titkosításával az Azure CLI-ből](storage-encryption-keys-cli.md)
- [Az Azure Storage titkosítása az inaktív adatokhoz](storage-service-encryption.md)
