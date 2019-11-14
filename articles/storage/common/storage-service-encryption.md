---
title: Azure Storage-titkosítás a REST-adatokhoz | Microsoft Docs
description: Az Azure Storage védi az adatait úgy, hogy automatikusan titkosítja azt, mielőtt megőrzi a felhőben. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.
services: storage
author: tamram
ms.service: storage
ms.date: 10/02/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: cfac7fdbbdbf06ae74385fbc33e61d11cb99ff87
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74066321"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-titkosítás a REST-adatokhoz

Az Azure Storage automatikusan titkosítja az adatait a felhőben való megőrzés során. A titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az Azure Storage-ban tárolt adatai a 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden új Storage-fiók esetében engedélyezve van, és nem tiltható le. Mivel az adatai alapértelmezés szerint biztonságosak, nem kell módosítania a kódot vagy az alkalmazásokat, hogy kihasználhassa az Azure Storage-titkosítás előnyeit.

A Storage-fiókok titkosítása a teljesítmény szintjétől (standard vagy prémium) vagy az üzembe helyezési modelltől (Azure Resource Manager vagy klasszikus) függetlenül történik. Az összes Azure Storage-redundancia-beállítás támogatja a titkosítást, és a Storage-fiók összes példánya titkosítva van. Minden Azure Storage-erőforrás titkosítva van, beleértve a blobokat, a lemezeket, a fájlokat, a várólistákat és a táblákat. Az összes objektum metaadatai is titkosítva vannak.

A titkosítás nem befolyásolja az Azure Storage teljesítményét. Az Azure Storage-titkosításhoz nem jár további díj.

További információ az Azure Storage-titkosítás alapjául szolgáló kriptográfiai modulokról [: a kriptográfiai API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A Microsoft által felügyelt kulcsokat használhatja a Storage-fiók titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, két lehetőség közül választhat:

- Megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a Storage-fiókban lévő összes érték titkosításához és visszafejtéséhez használhat. Az ügyfél által felügyelt kulcs a Storage-fiók összes szolgáltatásában lévő összes érték titkosítására szolgál.
- Megadhat egy *ügyfél által megadott kulcsot* a blob Storage-műveletekhez. A blob Storage-hoz tartozó olvasási vagy írási kérelmet készítő ügyfél tartalmazhat egy titkosítási kulcsot a kérelemben, amely részletesen szabályozza a Blobok titkosításának és visszafejtésének módját.

Az alábbi táblázat összehasonlítja az Azure Storage-titkosítás legfontosabb felügyeleti lehetőségeit.

|                                        |    Microsoft által felügyelt kulcsok                             |    Ügyfél által felügyelt kulcsok                                                                                                                        |    Ügyfél által biztosított kulcsok                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Titkosítási/visszafejtési műveletek    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Az Azure Storage szolgáltatásai támogatottak    |    Összes                                                |    BLOB Storage, Azure Files                                                                                                               |    Blob Storage                                                                  |
|    Kulcstároló                         |    Microsoft Key Store    |    Azure Key Vault                                                                                                                              |    Azure Key Vault vagy bármely más kulcstároló                                                                 |
|    Kulcs rotációs felelőssége         |    Microsoft                                          |    Ügyfél                                                                                                                                     |    Ügyfél                                                                      |
|    Kulcshasználat                           |    Microsoft                                          |    Azure Portal, Storage erőforrás-szolgáltató REST API, Azure Storage felügyeleti kódtárak, PowerShell, parancssori felület        |    Azure Storage-REST API (blob Storage), Azure Storage ügyféloldali kódtárak    |
|    Kulcs elérése                          |    Csak Microsoft                                     |    Microsoft, ügyfél                                                                                                                    |    Csak ügyfél                                                                 |

A következő szakaszok részletesebben ismertetik a kulcskezelő lehetőségeit.

## <a name="microsoft-managed-keys"></a>Microsoft által felügyelt kulcsok

Alapértelmezés szerint a Storage-fiók a Microsoft által felügyelt titkosítási kulcsokat használja. A Storage-fiók titkosítási beállításait a [Azure Portal](https://portal.azure.com) **titkosítási** szakaszában tekintheti meg, ahogy az az alábbi képen is látható.

![Microsoft által felügyelt kulcsokkal titkosított fiók megtekintése](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys"></a>Ügyfél által felügyelt kulcsok

Megadhatja, hogy az Azure Storage-titkosítást a Storage-fiók szintjén a saját kulcsaival kezelje. Ha ügyfél által felügyelt kulcsot ad meg a Storage-fiók szintjén, akkor a rendszer a Storage-fiókban lévő összes adattal titkosítja és visszafejti a kulcsot, beleértve a blob, a üzenetsor, a fájl és a tábla adataival.  Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.

Az ügyfél által felügyelt kulcsok tárolásához Azure Key Vaultt kell használnia. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)

Ez az ábra azt mutatja, hogy az Azure Storage hogyan használja az Azure Active Directory és Azure Key Vault a kérelmeket az ügyfél által felügyelt kulcs használatával:

![Ábra, amely bemutatja, hogyan működnek az ügyfél által felügyelt kulcsok az Azure Storage-ban](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

Az alábbi lista a diagram számozott lépéseit ismerteti:

1. Egy Azure Key Vault rendszergazda engedélyeket biztosít a titkosítási kulcsokhoz a Storage-fiókhoz társított felügyelt identitás számára.
2. Egy Azure Storage-rendszergazda a Storage-fiókhoz tartozó ügyfél által felügyelt kulccsal konfigurálja a titkosítást.
3. Az Azure Storage a Storage-fiókhoz társított felügyelt identitást használja a Azure Key Vaulthoz való hozzáférés hitelesítéséhez Azure Active Directory használatával.
4. Az Azure Storage becsomagolja a fiók titkosítási kulcsát a Azure Key Vault ügyfél kulcsával.
5. Az olvasási/írási műveletek esetében az Azure Storage kéréseket küld Azure Key Vault a titkosítási és visszafejtési műveletek elvégzéséhez a fiók titkosítási kulcsának becsomagolásához és kicsomagolásához.

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához a Storage-fiókban tekintse meg a [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) és [Azure Key Vault parancssori](https://docs.microsoft.com/cli/azure/keyvault)felület című témakört. A hozzáférés visszavonása hatékonyan blokkolja a Storage-fiókban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs nem érhető el az Azure Storage-ban.

Az ügyfél által felügyelt kulcsok nyilvános előzetesként is elérhetők az Azure Managed Disks szolgáltatásban, az ügyfél által felügyelt kulcsok egy kicsit másképp működnek a felügyelt lemezeken, mint a többi tárterület. Részletekért tekintse [meg a témával kapcsolatos cikket](../../virtual-machines/linux/disk-encryption.md#customer-managed-keys-public-preview).

Az ügyfél által felügyelt kulcsok Azure Storage-ban való használatáról a következő cikkekből tájékozódhat:

- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon](storage-encryption-keys-portal.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához a PowerShellből](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok használata Azure Storage-titkosítással az Azure CLI-vel](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Ha az ügyfél által felügyelt kulcsokat a Azure Portal konfigurálja, a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alatt lévő Storage-fiókhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a Storage-fiókot egyik Azure AD-címtárból a másikba, a Storage-fiókhoz társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok többé nem fognak működni. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

## <a name="customer-provided-keys-preview"></a>Ügyfél által biztosított kulcsok (előzetes verzió)

Az Azure Blob Storage-ra irányuló kérelmeket használó ügyfelek számára lehetősége van egy titkosítási kulcs megadására egy egyedi kérelemben. A kérelemben szereplő titkosítási kulcs részletesen szabályozható a blob Storage-műveletek titkosítási beállításai között. A felhasználó által megadott kulcsok (előzetes verzió) Azure Key Vault vagy egy másik kulcstárolóban is tárolhatók.

### <a name="encrypting-read-and-write-operations"></a>Olvasási és írási műveletek titkosítása

Ha egy ügyfélalkalmazás titkosítási kulcsot biztosít a kérelemhez, az Azure Storage a blob-adatok olvasása és írása során transzparens módon végzi a titkosítást és a visszafejtést. A titkosítási kulcs SHA-256 kivonata a blob tartalmával együtt íródik, és annak ellenőrzésére szolgál, hogy a blobon végzett összes további művelet ugyanazt a titkosítási kulcsot használja-e. Az Azure Storage nem tárolja és nem kezeli azt a titkosítási kulcsot, amelyet az ügyfél a kérelemmel küld. A kulcs biztonságos eldobása, amint a titkosítási vagy a visszafejtési folyamat befejeződött.

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
- [BLOB tulajdonságainak beolvasása](/rest/api/storageservices/get-blob-properties)
- [BLOB metaadatainak beolvasása](/rest/api/storageservices/get-blob-metadata)
- [Pillanatkép-blob](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Ügyfél által biztosított kulcsok elforgatása

A kérelemben átadott titkosítási kulcs elforgatásához töltse le a blobot, és töltse fel újra az új titkosítási kulccsal.

> [!IMPORTANT]
> A Azure Portal nem használható olyan tárolóba vagy blobba való olvasásra vagy írásra, amely a kérelemben megadott kulccsal van titkosítva.
>
> Ügyeljen arra, hogy a blob Storage-ba irányuló kérelemben megadott titkosítási kulcsot a Azure Key Vault. Ha a titkosítási kulcs nélküli tárolón vagy blobon próbálkozik írási művelettel, a művelet sikertelen lesz, és nem fog tudni hozzáférni az objektumhoz.

### <a name="example-use-a-customer-provided-key-to-upload-a-blob-in-net"></a>Példa: felhasználó által megadott kulcs használata blob feltöltéséhez a .NET-ben

Az alábbi példa egy ügyfél által megadott kulcsot hoz létre, és ezt a kulcsot használja a Blobok feltöltéséhez. A kód feltölt egy blokkot, majd véglegesíti a blokkok listáját, hogy a blobot az Azure Storage-ba írja. A kulcs a [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) objektumban van megadva a [CustomerProvidedKey](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.customerprovidedkey) tulajdonság beállításával.

A kulcs a [AesCryptoServiceProvider](/dotnet/api/system.security.cryptography.aescryptoserviceprovider) osztálysal jön létre. Ha az osztály egy példányát szeretné létrehozni a kódban, adjon hozzá egy `using` utasítást, amely a `System.Security.Cryptography` névtérre hivatkozik:

```csharp
public static void UploadBlobWithClientKey(CloudBlobContainer container)
{
    // Create a new key using the Advanced Encryption Standard (AES) algorithm.
    AesCryptoServiceProvider keyAes = new AesCryptoServiceProvider();

    // Specify the key as an option on the request.
    BlobCustomerProvidedKey customerProvidedKey = new BlobCustomerProvidedKey(keyAes.Key);
    var options = new BlobRequestOptions
    {
        CustomerProvidedKey = customerProvidedKey
    };

    string blobName = "sample-blob-" + Guid.NewGuid();
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);

    try
    {
        // Create an array of random bytes.
        byte[] buffer = new byte[1024];
        Random rnd = new Random();
        rnd.NextBytes(buffer);

        using (MemoryStream sourceStream = new MemoryStream(buffer))
        {
            // Write the array of random bytes to a block.
            int blockNumber = 1;
            string blockId = Convert.ToBase64String(Encoding.ASCII.GetBytes(string.Format("BlockId{0}",
                blockNumber.ToString("0000000"))));

            // Write the block to Azure Storage.
            blockBlob.PutBlock(blockId, sourceStream, null, null, options, null);

            // Commit the block list to write the blob.
            blockBlob.PutBlockList(new List<string>() { blockId }, null, options, null);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage-titkosítás és lemez titkosítása

Az Azure Storage encryption titkosítja az Azure-beli virtuális gépek lemezeit tároló blobokat. Emellett az összes Azure-beli virtuálisgép-lemez, beleértve a helyi Temp lemezeket is, [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md)is titkosítható. Azure Disk Encryption az iparági szabványnak megfelelő [BitLockert](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) használ a Windowson és a [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt) on Linuxon, hogy az operációs rendszer-alapú titkosítási megoldásokat biztosítson a Azure Key Vaulthoz.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon](storage-encryption-keys-portal.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához a PowerShellből](storage-encryption-keys-powershell.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure CLI-ből](storage-encryption-keys-cli.md)
