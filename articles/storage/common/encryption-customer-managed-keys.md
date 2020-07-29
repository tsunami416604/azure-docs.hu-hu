---
title: Ügyfél által felügyelt kulcsok használata a Azure Key Vault a fiókok titkosításának kezeléséhez
titleSuffix: Azure Storage
description: A Storage-fiókban tárolt adatvédelmet saját titkosítási kulccsal is elvégezheti. Ha ügyfél által felügyelt kulcsot ad meg, akkor a kulcs az adatait titkosító kulcshoz való hozzáférés védelme és vezérlése céljából használatos. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések kezeléséhez.
services: storage
author: tamram
ms.service: storage
ms.date: 07/20/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: af70b1746b2ac847d964975aaf1b2186aa89be01
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292742"
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

Az ügyfél által felügyelt kulcs konfigurálásakor az Azure Storage a társított kulcstartóban lévő ügyfél által felügyelt kulccsal becsomagolja a fiók gyökérszintű adattitkosítási kulcsát. Az ügyfél által felügyelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és azonnal érvénybe lép.

Ha engedélyezi vagy letiltja az ügyfelek által felügyelt kulcsokat, vagy ha módosítja a kulcsot vagy a kulcs verzióját, a rendszer megváltoztatja a gyökérszintű titkosítási kulcs védelmét, de az Azure Storage-fiókban lévő adatait nem szükséges újra titkosítani.

Az ügyfél által felügyelt kulcsok csak a meglévő Storage-fiókokon engedélyezhetők. A Key vaultot olyan hozzáférési házirendekkel kell konfigurálni, amelyek engedélyeket biztosítanak a Storage-fiókhoz társított felügyelt identitásnak. A felügyelt identitás csak a Storage-fiók létrehozása után érhető el.

Bármikor válthat az ügyfél által felügyelt kulcsok és a Microsoft által felügyelt kulcsok között. A Microsoft által kezelt kulcsokkal kapcsolatos további információkért lásd: [a titkosítási kulcsok kezelésének ismertetése](storage-service-encryption.md#about-encryption-key-management).

Az alábbi cikkekből megtudhatja, hogyan használhatók az ügyfelek által felügyelt kulcsok Azure Key Vault Azure Storage-titkosításhoz:

- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a Azure Portal](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a PowerShell-lel](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz az Azure CLI-vel](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure AD szolgáltatásban az Azure-erőforrások felügyelt identitásait használják. A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt lévő Storage-fiókhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a Storage-fiókot egyik Azure AD-címtárból a másikba, a Storage-fiókhoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok többé nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása Azure Key Vault

Az ügyfél által felügyelt kulcsok Storage-fiókban való engedélyezéséhez Azure Key vaultot kell használnia a kulcsok tárolásához. Engedélyeznie kell a **Soft delete** és a No **Purge** tulajdonságot a Key vaulton.

Az Azure Storage encryption a 2048, 3072 és 4096 méretű RSA-és RSA-HSM-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

A Azure Key Vault használata társított költségekkel rendelkezik. További információ: [Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. Az ügyfél által felügyelt kulcs elforgatására két lehetőség áll rendelkezésre:

- **Automatikus elforgatás:** Az ügyfél által felügyelt kulcsok automatikus elforgatásának konfigurálásához hagyja ki a kulcs verzióját, ha engedélyezi a titkosítást az ügyfél által felügyelt kulcsokkal a Storage-fiókhoz. Ha a kulcs verziója nincs megadva, az Azure Storage naponta ellenőrzi az Azure Key Vaultban, hogy nincs-e új verziója a felhasználó által kezelt kulcsnak. Ha elérhető új kulcsverzió, az Azure Storage automatikusan a kulcs legújabb verzióját használja.
- **Manuális elforgatás:** Ha az Azure Storage-titkosításhoz egy adott verziót szeretne használni, akkor a kulcs verziószámát a Storage-fiókhoz tartozó ügyfél által felügyelt kulcsokkal engedélyezheti. Ha megadja a kulcs verzióját, az Azure Storage ezt a verziót használja a titkosításhoz, amíg manuálisan nem frissíti a kulcs verzióját.

    A kulcs manuális elforgatásakor frissítenie kell a Storage-fiókot az új kulcs-verzió URI-azonosító használatára. A következő témakörből megtudhatja, hogyan frissítheti a Storage-fiókot úgy, hogy a kulcs új verzióját használja a Azure Portalban: a [kulcs verziójának manuális frissítése](storage-encryption-keys-portal.md#manually-update-the-key-version).

Az ügyfél által felügyelt kulcs elforgatása nem indítja el újra a Storage-fiókban tárolt adattitkosítást. Nincs szükség további műveletre a felhasználótól.

## <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Bármikor visszavonhatja a Storage-fiók hozzáférését az ügyfél által felügyelt kulcshoz. Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonása után vagy a kulcs letiltását vagy törlését követően az ügyfelek nem hívhatnak meg egy blobba vagy annak metaadataiba beolvasott vagy írt műveleteket. A következő műveletek bármelyikének meghívására tett kísérletek sikertelenek lesznek: 403 (tiltott) hibakód minden felhasználó esetében:

- [Blobok listázása](/rest/api/storageservices/list-blobs)a `include=metadata` kérelem URI-ja paraméterének meghívásakor
- [BLOB beolvasása](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadatainak beolvasása](/rest/api/storageservices/get-blob-metadata)
- [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [Pillanatkép-blob](/rest/api/storageservices/snapshot-blob), a `x-ms-meta-name` kérelem fejlécének hívásakor
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [BLOB másolása URL-címről](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
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
- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
