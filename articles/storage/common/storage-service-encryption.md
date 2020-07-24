---
title: Inaktív adatok Azure Storage-titkosítása
description: Az Azure Storage védi az adatait úgy, hogy automatikusan titkosítja azt, mielőtt megőrzi a felhőben. A Microsoft által felügyelt kulcsokat használhatja a Storage-fiókban lévő adatok titkosításához, vagy a titkosítást a saját kulcsaival is kezelheti.
services: storage
author: tamram
ms.service: storage
ms.date: 07/16/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 002eeaedf4ae479408cd1ba0c7a373d8a2661cdc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089396"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Inaktív adatok Azure Storage-titkosítása

Az Azure Storage automatikusan titkosítja az adatait, amikor azt megőrzi a felhőben. Az Azure Storage-titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-azure-storage-encryption"></a>Tudnivalók az Azure Storage-titkosításról

Az Azure Storage-ban tárolt adatai a 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden Storage-fiók esetében engedélyezve van, beleértve a Resource Manager és a klasszikus Storage-fiókokat is. Az Azure Storage-titkosítás nem tiltható le. Mivel az adatai alapértelmezés szerint biztonságosak, nem kell módosítania a kódot vagy az alkalmazásokat, hogy kihasználhassa az Azure Storage-titkosítás előnyeit.

A Storage-fiókban lévő adatok titkosítása a teljesítmény szintjétől (standard vagy prémium), a hozzáférési szinttől (gyakori vagy ritka elérésű) vagy a telepítési modelltől (Azure Resource Manager vagy klasszikus) függetlenül történik. Az archív szint összes blobja is titkosítva van. Az összes Azure Storage redundancia-beállítás támogatja a titkosítást, és az elsődleges és a másodlagos régióban lévő összes érték titkosítva van, ha a Geo-replikáció engedélyezve van. Minden Azure Storage-erőforrás titkosítva van, beleértve a blobokat, a lemezeket, a fájlokat, a várólistákat és a táblákat. Az összes objektum metaadatai is titkosítva vannak. Az Azure Storage-titkosításhoz nem jár további díj.

Az Azure Storage-ba az 2017. október 20. után írt összes blokk blob, blob hozzáfűzése vagy Page blob titkosítva van. Az ezen dátum előtt létrehozott blobokat a háttérben futó folyamat továbbra is titkosítja. A 2017. október 20. előtt létrehozott blob titkosításának kényszerítéséhez újra lehet írni a blobot. A Blobok titkosítási állapotának vizsgálatával kapcsolatban lásd [a Blobok titkosítási állapotának ellenőrzését](../blobs/storage-blob-encryption-status.md)ismertető témakört.

További információ az Azure Storage-titkosítás alapjául szolgáló kriptográfiai modulokról [: a kriptográfiai API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

Az Azure Managed Disks titkosításával és kulcskezelő szolgáltatásával kapcsolatos információkért lásd: az [Azure Managed Disks szolgáltatás kiszolgálóoldali titkosítása](../../virtual-machines/windows/disk-encryption.md) Windows rendszerű virtuális gépekhez, illetve az [Azure Managed Disks szolgáltatás kiszolgálóoldali titkosítása](../../virtual-machines/linux/disk-encryption.md) Linux rendszerű virtuális gépekhez.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Az új Storage-fiókokban lévő adatforgalom a Microsoft által felügyelt kulcsokkal van titkosítva. Hivatkozhat a Microsoft által felügyelt kulcsokra az adatok titkosításához, vagy kezelheti a titkosítást a saját kulcsaival. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, két lehetőség közül választhat:

- Megadhat egy, a blob Storage-ban és a Azure Filesban található adattitkosításhoz és visszafejtéshez Azure Key Vault használó *ügyfél által felügyelt kulcsot* . <sup>az ügyfél</sup> által felügyelt kulcsokkal kapcsolatos további információkért lásd: [ügyfél által felügyelt kulcsok használata Azure Key Vault az Azure Storage-titkosítás kezeléséhez](encryption-customer-managed-keys.md).
- Megadhat egy *ügyfél által megadott kulcsot* a blob Storage-műveletekhez. A blob Storage-hoz tartozó olvasási vagy írási kérelmet készítő ügyfél tartalmazhat egy titkosítási kulcsot a kérelemben, amely részletesen szabályozza a Blobok titkosításának és visszafejtésének módját. Az ügyfél által biztosított kulcsokkal kapcsolatos további információkért lásd: [titkosítási kulcs megadása a blob Storage-hoz való kérelemben](encryption-customer-provided-keys.md).

Az alábbi táblázat összehasonlítja az Azure Storage-titkosítás legfontosabb felügyeleti lehetőségeit.

| Kulcskezelő paraméter | Microsoft által felügyelt kulcsok | Felhasználó által kezelt kulcsok | Ügyfél által biztosított kulcsok |
|--|--|--|--|
| Titkosítási/visszafejtési műveletek | Azure | Azure | Azure |
| Az Azure Storage szolgáltatásai támogatottak | Mind | BLOB Storage, Azure Files<sup>1, 2</sup> | Blob Storage |
| Kulcstároló | Microsoft Key Store | Azure Key Vault | Ügyfél saját kulcstárolója |
| Kulcs rotációs felelőssége | Microsoft | Ügyfél | Ügyfél |
| Kulcs vezérlő | Microsoft | Ügyfél | Ügyfél |

<sup>1</sup> . az ügyfél által felügyelt kulcsok üzenetsor-tárolással történő létrehozását támogató fiók létrehozásával kapcsolatos információkért lásd: [hozzon létre egy fiókot, amely támogatja az ügyfél által felügyelt kulcsokat a várólistákhoz](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> . az ügyfél által felügyelt kulcsokat tartalmazó fiók létrehozásával kapcsolatos információkért lásd: [hozzon létre egy fiókot, amely támogatja az ügyfél által felügyelt kulcsokat a táblázatokhoz](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="encryption-scopes-for-blob-storage-preview"></a>BLOB Storage titkosítási hatókörök (előzetes verzió)

Alapértelmezés szerint a Storage-fiók egy olyan kulccsal van titkosítva, amely a Storage-fiókra terjed ki. Dönthet úgy, hogy a Microsoft által felügyelt kulcsokat vagy az ügyfél által felügyelt kulcsokat használja Azure Key Vaultban, hogy megvédje és vezérelje az adatait titkosító kulcshoz való hozzáférést.

A titkosítási hatókörök lehetővé teszik a titkosítás beállítását a tároló szintjén vagy egy egyedi blobon. A titkosítási hatókörök használatával biztonságos határokat hozhat létre az azonos Storage-fiókban található, de különböző ügyfelekhez tartozó adategységek között.

Az Azure Storage erőforrás-szolgáltató használatával létrehozhat egy vagy több titkosítási hatókört egy Storage-fiókhoz. Titkosítási hatókör létrehozásakor meg kell adnia, hogy a hatókör védett-e egy Microsoft által felügyelt kulccsal, vagy egy ügyfél által felügyelt kulccsal, amelyet a Azure Key Vault tárol. Ugyanazon a Storage-fiókon belül a különböző titkosítási hatókörök a Microsoft által felügyelt vagy az ügyfél által felügyelt kulcsokat egyaránt használhatják.

Miután létrehozott egy titkosítási hatókört, megadhatja a titkosítási hatókört egy tároló vagy blob létrehozásához szükséges kérelemben. A titkosítási hatókör létrehozásával kapcsolatos további információkért lásd: [titkosítási hatókörök létrehozása és kezelése (előzetes verzió)](../blobs/encryption-scope-manage.md).

> [!NOTE]
> A titkosítási hatókörök nem támogatottak az előzetes verzióban elérhető, olvasási hozzáférésű geo-redundáns tárolási (RA-GRS) fiókokkal.

> [!IMPORTANT]
> A titkosítási hatókörök előzetes verziója csak nem éles használatra készült. Az üzemi szolgáltatási szintű szerződések (SLA-kat) jelenleg nem érhetők el.
>
> A váratlan költségek elkerülése érdekében ügyeljen arra, hogy letiltsa a jelenleg nem szükséges titkosítási hatóköröket.

### <a name="create-a-container-or-blob-with-an-encryption-scope"></a>Tároló vagy blob létrehozása titkosítási hatókörrel

A titkosítási hatókörben létrehozott Blobok titkosítva vannak az adott hatókörhöz megadott kulccsal. A blob létrehozásakor megadhat egy egyéni blob titkosítási hatókörét, vagy megadhat egy alapértelmezett titkosítási hatókört is, ha tárolót hoz létre. Ha egy tároló szintjén egy alapértelmezett titkosítási hatókör van megadva, a tárolóban lévő összes blob titkosítva lesz az alapértelmezett hatókörhöz társított kulccsal.

Ha olyan tárolóban hoz létre blobot, amely rendelkezik alapértelmezett titkosítási hatókörrel, megadhat egy titkosítási hatókört, amely felülbírálja az alapértelmezett titkosítási hatókört, ha a tároló úgy van konfigurálva, hogy engedélyezze az alapértelmezett titkosítási hatókör felülbírálását. Az alapértelmezett titkosítási hatókör felülbírálásának megakadályozásához konfigurálja úgy a tárolót, hogy megtagadja az egyes Blobok felülbírálásait.

A titkosítási hatókörhöz tartozó Blobok olvasási műveletei transzparens módon történnek, feltéve, hogy a titkosítási hatókör nincs letiltva.

### <a name="disable-an-encryption-scope"></a>Titkosítási hatókör letiltása

Ha letilt egy titkosítási hatókört, a titkosítási hatókörön végrehajtott összes további olvasási vagy írási művelet sikertelen lesz a 403-as HTTP-hibakódnál (tiltott). Ha újra engedélyezi a titkosítási hatókört, az olvasási és írási műveletek általában újra bekerülnek.

Ha a titkosítási hatókör le van tiltva, már nem számítunk fel díjat. Tiltsa le azokat a titkosítási hatóköröket, amelyek nem szükségesek a szükségtelen költségek elkerülése érdekében.

Ha a titkosítási hatókör védett a Azure Key Vault ügyfél által felügyelt kulcsaival, akkor a titkosítási hatókör letiltásához a kulcstartóban is törölheti a társított kulcsot. Ne feledje, hogy a Azure Key Vault ügyfél által felügyelt kulcsait a rendszer a Soft delete és a Purge Protection védi, és a törölt kulcsra a tulajdonságok által meghatározott viselkedés vonatkozik. További információkért tekintse meg a következő témakörök egyikét az Azure Key Vault dokumentációjában:

- [A Soft delete használata a PowerShell-lel](../../key-vault/general/soft-delete-powershell.md)
- [A Soft delete használata a parancssori felülettel](../../key-vault/general/soft-delete-cli.md)

> [!NOTE]
> Titkosítási hatókört nem lehet törölni.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon](storage-encryption-keys-portal.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához a PowerShellből](storage-encryption-keys-powershell.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure CLI-ből](storage-encryption-keys-cli.md)
