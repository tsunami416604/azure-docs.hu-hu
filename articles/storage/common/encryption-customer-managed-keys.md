---
title: Ügyfél által felügyelt kulcsok használata a Azure Key Vault a fiókok titkosításának kezeléséhez
titleSuffix: Azure Storage
description: A Storage-fiókban tárolt adatvédelmet saját titkosítási kulccsal is elvégezheti. Ha ügyfél által felügyelt kulcsot ad meg, akkor a kulcs az adatait titkosító kulcshoz való hozzáférés védelme és vezérlése céljából használatos. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések kezeléséhez.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81456821"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Ügyfél által felügyelt kulcsok használata Azure Key Vault az Azure Storage-titkosítás kezeléséhez

A Storage-fiókban tárolt adatvédelmet saját titkosítási kulccsal is elvégezheti. Ha ügyfél által felügyelt kulcsot ad meg, akkor a kulcs az adatait titkosító kulcshoz való hozzáférés védelme és vezérlése céljából használatos. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések kezeléséhez.

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Storage-fióknak és a kulcstartónak ugyanabban a régióban és ugyanabban a Azure Active Directory (Azure AD) bérlőben kell lennie, de különböző előfizetésekben lehet. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Az ügyfél által felügyelt kulcsok

Az alábbi ábra azt mutatja be, hogyan használja az Azure Storage Azure Active Directory és Azure Key Vault a kérelmeket az ügyfél által felügyelt kulcs használatával:

![Ábra, amely bemutatja, hogyan működnek az ügyfél által felügyelt kulcsok az Azure Storage-ban](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

Az alábbi lista a diagram számozott lépéseit ismerteti:

1. Egy Azure Key Vault rendszergazda engedélyeket biztosít a titkosítási kulcsokhoz a Storage-fiókhoz társított felügyelt identitás számára.
2. Egy Azure Storage-rendszergazda a Storage-fiókhoz tartozó ügyfél által felügyelt kulccsal konfigurálja a titkosítást.
3. Az Azure Storage a Storage-fiókhoz társított felügyelt identitást használja a Azure Key Vaulthoz való hozzáférés hitelesítéséhez Azure Active Directory használatával.
4. Az Azure Storage becsomagolja a fiók titkosítási kulcsát a Azure Key Vault ügyfél kulcsával.
5. Az olvasási/írási műveletek esetében az Azure Storage kéréseket küld Azure Key Vaultnak a titkosítási és visszafejtési műveletek elvégzéséhez.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Ügyfél által felügyelt kulcsokat támogató fiók létrehozása a várólistákhoz és a táblákhoz

A várólista és a Table Services szolgáltatásban tárolt adatkészleteket nem védi automatikusan az ügyfél által felügyelt kulcs, ha az ügyfél által felügyelt kulcsok engedélyezve vannak a Storage-fiókhoz. Igény szerint konfigurálhatja ezeket a szolgáltatásokat abban az esetben, amikor létrehozza a védelembe bevonni kívánt Storage-fiókot.

További információ arról, hogyan hozható létre olyan Storage-fiók, amely támogatja az ügyfél által kezelt kulcsokat a várólistákhoz és a táblákhoz: [hozzon létre egy fiókot, amely támogatja a táblák és a várólisták ügyfél által felügyelt kulcsait](account-encryption-key-create.md).

A blob-és Fájlszolgáltatások-beli adatvédelmet mindig az ügyfél által felügyelt kulcsok védik, ha az ügyfél által felügyelt kulcsok konfigurálva vannak a Storage-fiókhoz.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Ügyfél által felügyelt kulcsok engedélyezése egy Storage-fiókhoz

Az ügyfél által felügyelt kulcsok csak a meglévő Storage-fiókokon engedélyezhetők. A Key vaultot olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak a Storage-fiókhoz társított felügyelt identitásnak. A felügyelt identitás csak a Storage-fiók létrehozása után érhető el.

Az ügyfél által felügyelt kulcs konfigurálásakor az Azure Storage a társított kulcstartóban lévő ügyfél által felügyelt kulccsal becsomagolja a fiók gyökérszintű adattitkosítási kulcsát. Az ügyfél által felügyelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és azonnal érvénybe lép.

Ha módosítja az Azure Storage-titkosításhoz használt kulcsot az ügyfél által felügyelt kulcsok engedélyezésével vagy letiltásával, a kulcs verziójának frissítésével vagy egy másik kulcs megadásával, akkor a legfelső szintű kulcs megváltozását, de az Azure Storage-fiókban tárolt adatait nem szükséges újra titkosítani.

Ha engedélyezi vagy letiltja az ügyfelek által felügyelt kulcsokat, vagy ha módosítja a kulcsot vagy a kulcs verzióját, a rendszer megváltoztatja a gyökérszintű titkosítási kulcs védelmét, de az Azure Storage-fiókban lévő adatait nem szükséges újra titkosítani.

Az alábbi cikkekből megtudhatja, hogyan használhatók az ügyfelek által felügyelt kulcsok Azure Key Vault Azure Storage-titkosításhoz:

- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a Azure Portal](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a PowerShell-lel](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz az Azure CLI-vel](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure AD szolgáltatásban az Azure-erőforrások felügyelt identitásait használják. A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt lévő Storage-fiókhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a Storage-fiókot egyik Azure AD-címtárból a másikba, a Storage-fiókhoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok többé nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása Azure Key Vault

Az ügyfél által felügyelt kulcsok Storage-fiókban való engedélyezéséhez Azure Key Vault kell használnia a kulcsok tárolásához. Engedélyeznie kell a **Soft delete** és a No **Purge** tulajdonságot a Key vaulton.

Az Azure Storage-titkosítás csak 2048 bites RSA-és RSA-HSM-kulcsokat támogat. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

## <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor frissítenie kell a Storage-fiókot az új kulcs-verzió URI-azonosító használatára. Ha szeretné megtudni, hogyan frissítheti a Storage-fiókot úgy, hogy a kulcs új verzióját használja a Azure Portalban, tekintse meg a következő témakört: **a kulcs verziójának frissítése** az [Azure Storage-hoz az ügyfél által felügyelt kulcsok konfigurálása](storage-encryption-keys-portal.md)című rész a Azure Portal használatával.

A kulcs elforgatása nem indítja el újra a Storage-fiókban tárolt adattitkosítást. Nincs szükség további műveletre a felhasználótól.

## <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Bármikor visszavonhatja a Storage-fiók hozzáférését az ügyfél által felügyelt kulcshoz. Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása után vagy a kulcs letiltását vagy törlését követően az ügyfelek nem hívhatnak meg egy blobba vagy annak metaadataiba beolvasott vagy írt műveleteket. A következő műveletek bármelyikének meghívására tett kísérletek sikertelenek lesznek: 403 (tiltott) hibakód minden felhasználó esetében:

- [Blobok listázása](/rest/api/storageservices/list-blobs)a kérelem URI `include=metadata` -ja paraméterének meghívásakor
- [BLOB beolvasása](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadatainak beolvasása](/rest/api/storageservices/get-blob-metadata)
- [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [Pillanatkép-blob](/rest/api/storageservices/snapshot-blob), a `x-ms-meta-name` kérelem fejlécének hívásakor
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [BLOB másolása URL-címről](/rest/api/storageservices/copy-blob-from-url)
- [Blobszint beállítása](/rest/api/storageservices/set-blob-tier)
- [Put blokk](/rest/api/storageservices/put-block)
- [Blokk elhelyezése URL-címről](/rest/api/storageservices/put-block-from-url)
- [Blokk hozzáfűzése](/rest/api/storageservices/append-block)
- [Blokk hozzáfűzése URL-címről](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Oldal elhelyezése](/rest/api/storageservices/put-page)
- [Oldal elhelyezése az URL-címről](/rest/api/storageservices/put-page-from-url)
- [Növekményes másolási blob](/rest/api/storageservices/incremental-copy-blob)

A műveletek ismételt meghívásához állítsa vissza a hozzáférést az ügyfél által felügyelt kulcshoz.

Az ebben a szakaszban nem szereplő adatműveletek az ügyfél által felügyelt kulcsok visszavonása, illetve a kulcs letiltását vagy törlését követően is folytatódnak.

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a [PowerShellt](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) vagy az [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys)-t.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Ügyfél által felügyelt kulcsok az Azure Managed Disks szolgáltatáshoz

Az ügyfél által felügyelt kulcsok az Azure Managed Disks titkosításának kezelésére is használhatók. Az ügyfél által felügyelt kulcsok eltérően működnek a felügyelt lemezeken, mint az Azure Storage-erőforrások esetében. További információkért lásd: az Azure Managed [Disks szolgáltatás kiszolgálóoldali titkosítása](../../virtual-machines/windows/disk-encryption.md) a Windows rendszerhez vagy az [Azure Managed Disks](../../virtual-machines/linux/disk-encryption.md) for Linux szolgáltatás kiszolgálóoldali titkosítása.

## <a name="next-steps"></a>További lépések

- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a Azure Portal](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a PowerShell-lel](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz az Azure CLI-vel](storage-encryption-keys-cli.md)
- [Azure Storage-titkosítás a REST-adatokhoz](storage-service-encryption.md)
