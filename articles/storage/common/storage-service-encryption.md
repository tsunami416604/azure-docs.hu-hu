---
title: Azure Storage-titkosítás a REST-adatokhoz
description: Az Azure Storage védi az adatait úgy, hogy automatikusan titkosítja azt, mielőtt megőrzi a felhőben. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.
services: storage
author: tamram
ms.service: storage
ms.date: 01/03/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 35a5bfd582c9717b062d42d86e7581029861fd0c
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665437"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-titkosítás a REST-adatokhoz

Az Azure Storage automatikusan titkosítja az adatait, amikor azt megőrzi a felhőben. Az Azure Storage-titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-azure-storage-encryption"></a>Tudnivalók az Azure Storage-titkosításról

Az Azure Storage-ban tárolt adatai a 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden új Storage-fiókhoz engedélyezve van, beleértve a Resource Manager és a klasszikus Storage-fiókokat is. Az Azure Storage-titkosítás nem tiltható le. Mivel az adatai alapértelmezés szerint biztonságosak, nem kell módosítania a kódot vagy az alkalmazásokat, hogy kihasználhassa az Azure Storage-titkosítás előnyeit.

A Storage-fiókok titkosítása a teljesítmény szintjétől (standard vagy prémium) vagy az üzembe helyezési modelltől (Azure Resource Manager vagy klasszikus) függetlenül történik. Az összes Azure Storage-redundancia-beállítás támogatja a titkosítást, és a Storage-fiók összes példánya titkosítva van. Minden Azure Storage-erőforrás titkosítva van, beleértve a blobokat, a lemezeket, a fájlokat, a várólistákat és a táblákat. Az összes objektum metaadatai is titkosítva vannak.

A titkosítás nem befolyásolja az Azure Storage teljesítményét. Az Azure Storage-titkosításhoz nem jár további díj.

Az Azure Storage-ba az 2017. október 20. után írt összes blokk blob, blob hozzáfűzése vagy Page blob titkosítva van. Az ezen dátum előtt létrehozott blobokat a háttérben futó folyamat továbbra is titkosítja. A 2017. október 20. előtt létrehozott blob titkosításának kényszerítéséhez újra lehet írni a blobot. A Blobok titkosítási állapotának vizsgálatával kapcsolatban lásd [a Blobok titkosítási állapotának ellenőrzését](../blobs/storage-blob-encryption-status.md)ismertető témakört.

További információ az Azure Storage-titkosítás alapjául szolgáló kriptográfiai modulokról [: a kriptográfiai API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, két lehetőség közül választhat:

- Megadhat egy, a blob Storage-ban és a Azure Filesban található adattitkosításhoz és visszafejtéshez Azure Key Vault használó *ügyfél által felügyelt kulcsot* .
- Megadhat egy *ügyfél által megadott kulcsot* a blob Storage-műveletekhez. A blob Storage-hoz tartozó olvasási vagy írási kérelmet készítő ügyfél tartalmazhat egy titkosítási kulcsot a kérelemben, amely részletesen szabályozza a Blobok titkosításának és visszafejtésének módját.

Az alábbi táblázat összehasonlítja az Azure Storage-titkosítás legfontosabb felügyeleti lehetőségeit.

|                                        |    Microsoft által felügyelt kulcsok                             |    Ügyfél által felügyelt kulcsok                                                                                                                        |    Ügyfél által biztosított kulcsok                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Titkosítási/visszafejtési műveletek    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Az Azure Storage szolgáltatásai támogatottak    |    Mind                                                |    BLOB Storage, Azure Files                                                                                                               |    Blobtároló                                                                  |
|    Kulcstároló                         |    Microsoft Key Store    |    Azure Key Vault                                                                                                                              |    Azure Key Vault vagy bármely más kulcstároló                                                                 |
|    Kulcs rotációs felelőssége         |    Microsoft                                          |    Ügyfél                                                                                                                                     |    Ügyfél                                                                      |
|    Kulcshasználat                           |    Microsoft                                          |    Azure Portal, Storage erőforrás-szolgáltató REST API, Azure Storage felügyeleti kódtárak, PowerShell, parancssori felület        |    Azure Storage-REST API (blob Storage), Azure Storage ügyféloldali kódtárak    |
|    Kulcs elérése                          |    Csak Microsoft                                     |    Microsoft, ügyfél                                                                                                                    |    Csak ügyfél                                                                 |

A következő szakaszok részletesebben ismertetik a kulcskezelő lehetőségeit.

## <a name="microsoft-managed-keys"></a>Microsoft által felügyelt kulcsok

Alapértelmezés szerint a Storage-fiók a Microsoft által felügyelt titkosítási kulcsokat használja. A Storage-fiók titkosítási beállításait a [Azure Portal](https://portal.azure.com) **titkosítási** szakaszában tekintheti meg, ahogy az az alábbi képen is látható.

![Microsoft által felügyelt kulcsokkal titkosított fiók megtekintése](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok Azure Key Vault

A Storage-fiók szintjén a saját kulcsaival kezelheti az Azure Storage-titkosítást. Ha ügyfél által felügyelt kulcsot ad meg a Storage-fiók szintjén, a rendszer a kulcs használatával védi és szabályozza a hozzáférést a Storage-fiókhoz tartozó gyökérszintű titkosítási kulcshoz, amely viszont az összes blob és fájl adatainak titkosítására és visszafejtésére szolgál. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)

Ez az ábra azt mutatja, hogy az Azure Storage hogyan használja az Azure Active Directory és Azure Key Vault a kérelmeket az ügyfél által felügyelt kulcs használatával:

![Ábra, amely bemutatja, hogyan működnek az ügyfél által felügyelt kulcsok az Azure Storage-ban](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

Az alábbi lista a diagram számozott lépéseit ismerteti:

1. Egy Azure Key Vault rendszergazda engedélyeket biztosít a titkosítási kulcsokhoz a Storage-fiókhoz társított felügyelt identitás számára.
2. Egy Azure Storage-rendszergazda a Storage-fiókhoz tartozó ügyfél által felügyelt kulccsal konfigurálja a titkosítást.
3. Az Azure Storage a Storage-fiókhoz társított felügyelt identitást használja a Azure Key Vaulthoz való hozzáférés hitelesítéséhez Azure Active Directory használatával.
4. Az Azure Storage becsomagolja a fiók titkosítási kulcsát a Azure Key Vault ügyfél kulcsával.
5. Az olvasási/írási műveletek esetében az Azure Storage kéréseket küld Azure Key Vault a titkosítási és visszafejtési műveletek elvégzéséhez a fiók titkosítási kulcsának becsomagolásához és kicsomagolásához.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Ügyfél által felügyelt kulcsok engedélyezése egy Storage-fiókhoz

Ha az ügyfél által felügyelt kulcsokkal engedélyezi a titkosítást egy Storage-fiókhoz, az Azure Storage a társított kulcstartóban becsomagolja a fiók titkosítási kulcsát a felhasználó által felügyelt kulccsal. Az ügyfél által felügyelt kulcsok engedélyezése nem befolyásolja a teljesítményt, és a fiók azonnal, késedelem nélkül titkosítva lesz az új kulccsal.

Az új Storage-fiók mindig a Microsoft által felügyelt kulcsokkal van titkosítva. A fiók létrehozásakor nem lehet engedélyezni az ügyfél által felügyelt kulcsokat. Az ügyfél által felügyelt kulcsok Azure Key Vault tárolódnak, és a kulcstárolót olyan hozzáférési házirendekkel kell kiépíteni, amelyek kulcsfontosságú engedélyeket biztosítanak a Storage-fiókhoz társított felügyelt identitásnak. A felügyelt identitás csak a Storage-fiók létrehozása után érhető el.

Ha módosítja az Azure Storage-titkosításhoz használt kulcsot az ügyfél által felügyelt kulcsok engedélyezésével vagy letiltásával, a kulcs verziójának frissítésével, illetve egy másik kulcs megadásával, akkor a legfelső szintű kulcs megváltozásakor, de az Azure Storage-fiókban lévő adatai nem újra kell titkosítani.

Az alábbi cikkekből megtudhatja, hogyan használhatók az ügyfelek által felügyelt kulcsok Azure Key Vault Azure Storage-titkosításhoz:

- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a Azure Portal](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz a PowerShell-lel](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok konfigurálása Key Vault Azure Storage-titkosításhoz az Azure CLI-vel](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt lévő Storage-fiókhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a Storage-fiókot egyik Azure AD-címtárból a másikba, a Storage-fiókhoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok többé nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ügyfél által felügyelt kulcsok tárolása Azure Key Vault

Az ügyfél által felügyelt kulcsok Storage-fiókban való engedélyezéséhez Azure Key Vault kell használnia a kulcsok tárolásához. Engedélyeznie kell a **Soft delete** és a No **Purge** tulajdonságot a Key vaulton.

Az Azure Storage-titkosítás csak a 2048 méretű RSA-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

A Key vaultnak ugyanabban az előfizetésben kell lennie, mint a Storage-fióknak. Az Azure Storage felügyelt identitásokat használ az Azure-erőforrások számára a titkosítási és visszafejtési műveletek Key vaultba való hitelesítéséhez. A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket.

### <a name="rotate-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok elforgatása

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. A kulcs elforgatásakor frissítenie kell a Storage-fiókot az új kulcs URI-azonosítójának használatához. Ha szeretné megtudni, hogyan frissítheti a Storage-fiókot úgy, hogy a kulcs új verzióját használja a Azure Portalban, tekintse meg a következő témakört: **a kulcs verziójának frissítése** az [Azure Storage-hoz az ügyfél által felügyelt kulcsok konfigurálása](storage-encryption-keys-portal.md)című rész a Azure Portal használatával.

A kulcs elforgatása nem indítja el újra a Storage-fiókban tárolt adattitkosítást. Nincs szükség további műveletre a felhasználótól.

### <a name="revoke-access-to-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok hozzáférésének visszavonása

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához használja a PowerShellt vagy az Azure CLI-t. További információ: [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) vagy [Azure Key Vault parancssori](/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Storage-fiókban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs nem érhető el az Azure Storage-ban.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Ügyfél által felügyelt kulcsok az Azure Managed Disks szolgáltatáshoz (előzetes verzió)

Az ügyfél által felügyelt kulcsok az Azure Managed Disks (előzetes verzió) titkosításának kezelésére is használhatók. Az ügyfél által felügyelt kulcsok eltérően működnek a felügyelt lemezeken, mint az Azure Storage-erőforrások esetében. További információ: az Azure [Managed Disks kiszolgálóoldali titkosítása](../../virtual-machines/windows/disk-encryption.md) a Windowshoz vagy az [Azure Managed Disks](../../virtual-machines/linux/disk-encryption.md) for Linux szolgáltatás kiszolgálóoldali titkosítása.

## <a name="customer-provided-keys-preview"></a>Ügyfél által biztosított kulcsok (előzetes verzió)

Az Azure Blob Storage-ra irányuló kérelmeket használó ügyfelek számára lehetősége van egy titkosítási kulcs megadására egy egyedi kérelemben. A kérelemben szereplő titkosítási kulcs részletesen szabályozható a blob Storage-műveletek titkosítási beállításai között. A felhasználó által megadott kulcsok (előzetes verzió) Azure Key Vault vagy egy másik kulcstárolóban is tárolhatók.

Egy példa, amely bemutatja, hogyan lehet megadnia egy ügyfél által megadott kulcsot a blob Storage-kérelemre vonatkozóan, a [blob Storage-hoz a .net-mel való kérelemben a felhasználó által megadott kulcs megadása című cikk](../blobs/storage-blob-customer-provided-key.md)nyújt tájékoztatást. 

### <a name="encrypting-read-and-write-operations"></a>Olvasási és írási műveletek titkosítása

Ha egy ügyfélalkalmazás titkosítási kulcsot biztosít a kérelemhez, az Azure Storage a blob-adatok olvasása és írása során transzparens módon végzi a titkosítást és a visszafejtést. Az Azure Storage a blob tartalma mellett a titkosítási kulcs SHA-256 kivonatát írja. A kivonattal ellenőrizheti, hogy a blobon végzett összes további művelet ugyanazt a titkosítási kulcsot használja-e. 

Az Azure Storage nem tárolja és nem kezeli azt a titkosítási kulcsot, amelyet az ügyfél a kérelemmel küld. A kulcs biztonságos eldobása, amint a titkosítási vagy a visszafejtési folyamat befejeződött.

Amikor egy ügyfél egy ügyfél által megadott kulccsal hoz létre vagy frissít egy blobot, az adott blobra vonatkozó későbbi olvasási és írási kérelmek is meg kell adniuk a kulcsot. Ha a kulcs nincs megadva egy olyan blobra vonatkozó kérelemben, amely már egy ügyfél által megadott kulccsal lett titkosítva, akkor a kérelem meghiúsul a 409-as hibakódtal (ütközés).

Ha az ügyfélalkalmazás egy titkosítási kulcsot küld a kérelemre, és a Storage-fiók egy Microsoft által felügyelt kulccsal vagy egy ügyfél által felügyelt kulccsal is titkosítva van, akkor az Azure Storage a titkosítás és a visszafejtés iránti kérelemben megadott kulcsot használja.

A titkosítási kulcsnak a kérés részeként történő elküldéséhez az ügyfélnek biztonságos kapcsolatot kell létesítenie az Azure Storage szolgáltatással a HTTPS használatával.

Minden blob-pillanatképhez saját titkosítási kulcs tartozhat.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Ügyfél által biztosított kulcsok megadására vonatkozó kérések fejlécei

REST-hívások esetén az ügyfelek a következő fejlécek segítségével biztonságosan továbbítják a titkosítási kulcs információit a blob Storage-kérelemre:

|Kérelem fejléce | Leírás |
|---------------|-------------|
|`x-ms-encryption-key` |Írási és olvasási kérelmek esetén egyaránt szükséges. Base64 kódolású AES-256 titkosítási kulcs értéke. |
|`x-ms-encryption-key-sha256`| Írási és olvasási kérelmek esetén egyaránt szükséges. A titkosítási kulcs Base64 kódolású SHA256. |
|`x-ms-encryption-algorithm` | Írási kérésekhez szükséges, az olvasási kérelmek esetében nem kötelező. Meghatározza az adattitkosításhoz használt algoritmust a megadott kulccsal. AES256 kell lennie. |

A kérelemben szereplő titkosítási kulcsok megadása nem kötelező. Ha azonban egy írási művelethez a fent felsorolt fejlécek egyikét adja meg, akkor mindegyiket meg kell adnia.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Az ügyfél által biztosított kulcsokat támogató blob Storage-műveletek

A blob Storage következő műveletei támogatják az ügyfél által biztosított titkosítási kulcsok küldését egy kérelemre:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Tiltási lista](/rest/api/storageservices/put-block-list)
- [Put blokk](/rest/api/storageservices/put-block)
- [Blokk elhelyezése URL-címről](/rest/api/storageservices/put-block-from-url)
- [Oldal elhelyezése](/rest/api/storageservices/put-page)
- [Oldal elhelyezése az URL-címről](/rest/api/storageservices/put-page-from-url)
- [Blokk hozzáfűzése](/rest/api/storageservices/append-block)
- [BLOB tulajdonságainak beállítása](/rest/api/storageservices/set-blob-properties)
- [BLOB metaadatainak beállítása](/rest/api/storageservices/set-blob-metadata)
- [BLOB beolvasása](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadatainak beolvasása](/rest/api/storageservices/get-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Ügyfél által biztosított kulcsok elforgatása

A kérelemben átadott titkosítási kulcs elforgatásához töltse le a blobot, és töltse fel újra az új titkosítási kulccsal.

> [!IMPORTANT]
> A Azure Portal nem használható olyan tárolóba vagy blobba való olvasásra vagy írásra, amely a kérelemben megadott kulccsal van titkosítva.
>
> Ügyeljen arra, hogy a blob Storage-ba irányuló kérelemben megadott titkosítási kulcsot a Azure Key Vault. Ha a titkosítási kulcs nélküli tárolón vagy blobon próbálkozik írási művelettel, a művelet sikertelen lesz, és nem fog tudni hozzáférni az objektumhoz.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage-titkosítás és lemez titkosítása

Az Azure Storage encryption titkosítja az Azure-beli virtuális gépek lemezeit tároló blobokat. Emellett az összes Azure-beli virtuálisgép-lemez, beleértve a helyi Temp lemezeket is, [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md)is titkosítható. Azure Disk Encryption az iparági szabványnak megfelelő [BitLockert](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) használ a Windowson és a [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt) on Linuxon, hogy az operációs rendszer-alapú titkosítási megoldásokat biztosítson a Azure Key Vaulthoz.

## <a name="next-steps"></a>Következő lépések

- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon](storage-encryption-keys-portal.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához a PowerShellből](storage-encryption-keys-powershell.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure CLI-ből](storage-encryption-keys-cli.md)
